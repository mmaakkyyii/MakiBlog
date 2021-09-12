---
title: "STM32マイコンとPC間でUDP通信をする"
date: 2021-09-13T1:45:00+09:00
draft: false
categories: ["組み込み"]
tags: ["STM32","Ethernet"]
---

# はじめに
マイコンとPCとの通信でEthernet経由で通信がしたい  
今回の最終目標はマイコンからのデータをEthernet経由でPC側の自作プログラムから受信すること。


# マイコン側のハードウェア
今回利用するのは[STM32F769I-DISCO](https://www.st.com/en/evaluation-tools/32f769idiscovery.html)という開発ボード。  
Ethernet用のペリフェラルを持っているSTM32F769マイコンとEthernetトランシーバー[LAN8742A-CZ-TR](https://www.microchip.com/en-us/product/LAN8742A)とLANコネクタが乗っている。
![](../F7disco.jpg)

# マイコン側の設定
CubeIDEのバージョンは1.7.0を利用した。  
CubeMXではマイコンを選択するとき公式のマイコンボードを選択すると初めから備え付けの回路用のペリフェラルを設定してくれるが、今回は余計な設定を勝手にして欲しくないのでマイコン単体のSTM32F769NIを選択した。

## CubeMXの設定
必要な設定項目は以下に示す。またCubeMXのレポートは
<a href="../ethernet_test2.pdf">これ(PDFが開きます)</a>

### SYS
Mode
* Debug : Serial Wire  
デバッグ用
* Timebase Source : TIM6  
RTOSのクロック用。デフォルトのままだとなんかタイマー指定しろって言われるはずTIM6にしているけどどれでも問題ないので機能が少ないタイマーを利用した
### ETH
Mode
* Mode : RMII  
7線のインターフェース(Reducded Media-Independent Interface)
今回はハードウェア的にすでにこの接続だったのでこれを利用する

Configuration
* General Settings  
Ethernet Configuration : PHY Address 0  

今回はPHYチップを利用しているので、Advanced Paramaters -> External PHY ConfigurationのPHYにLAN8742A_PHY_ADDRESSとしている(デフォルトがこの値になってるので他のレシーバICを使うときは変える必要がありそう)  
そしてこのLAN8742Aのデータシート曰く
![](../LAN8742A_datasheet_3_7_1.JPG)
のようでPHY Addressはデフォルトで0になっているので、マイコン側も基本的にこれに合わせて0にする必要がある。

### LWPI
Mode
* [✔] Enable

Configuration
* General Settings  
IPv4 -DHCP Option : Disable  
DHCPを無効化してIPアドレスを直接指定する  
IP Address Settings  
    IP_ADDRESS(IP Address) 169.254.137.114  
    NETMASK_ADDRESS 225.225.225.000  
    GATEWAY_ADDRESS 169.254.137.001  
### FREERTOS
Mode
* interface : CMSIS_V1

Configuration  
* Advanced setting  
Newlib setting  
    USE_NEWLIB_REENTRANT : Enable  
* Task and Queues :stack size 526  

### Clock Configuration
HCLM : 216 MHz

## マイコンのソースコード
基本的にはRTOSのタスク内に必要なコードを書いておく。  
こちらのブログを参考に https://blog.spiralray.net/archives/798  

||||
|----|----|----|
|マイコン側IPアドレス |169.254.137.114|(CubeMXで指定)|
|マイコン側ポート |54321||
|PC側IPアドレス| 169.254.137.86|(ipconfigなどで確認)|
|PC側ポート |50000||


```C
/* USER CODE BEGIN Header_StartDefaultTask */
/**
  * @brief  Function implementing the defaultTask thread.
  * @param  argument: Not used
  * @retval None
  */
/* USER CODE END Header_StartDefaultTask */
void StartDefaultTask(void const * argument)
{
  /* init code for LWIP */
  MX_LWIP_Init();
  /* USER CODE BEGIN 5 */
  struct udp_pcb *pcb;
  struct pbuf *p;
  err_t err;
  ip4_addr_t dst_addr;
  const unsigned short src_port = 54321;
  const unsigned short dst_port = 50000;

  IP4_ADDR(&dst_addr,169,254,137,86);

  pcb = udp_new();
  err = udp_bind(pcb, IP_ADDR_ANY, src_port);

  uint32_t cnt = 0;
  const int data_len=8;
  uint8_t send_data[data_len];
  for(int i=0;i<data_len;i++)send_data[i]=0;

  /* Infinite loop */
  for(;;)
  {
    HAL_GPIO_TogglePin(LED1_GPIO_Port, LED1_Pin);
    
    p = pbuf_alloc(PBUF_TRANSPORT, sizeof(send_data), PBUF_RAM);//ここでデータサイズ指定
    p->payload = (uint8_t *)send_data; //データのポインタを渡す
    err = udp_sendto(pcb, p, &dst_addr, dst_port);
    pbuf_free(p);

    for(int i=0;i<data_len;i++)send_data[i]=cnt++;

    osDelay(500);
  }
  /* USER CODE END 5 */
}
```

# PC側のソフト
とりあえずマイコンからデータが送られているか確認するために[Wireshark](https://forest.watch.impress.co.jp/library/software/wireshark/)というソフトで確認  
![](../Wireshark.JPG)  
データの部分で長さ8バイトでプログラムで送っている通りのデータを確認できる。

## Pythonでデータの受信を行う

```Python
import socket

my_address = '169.254.137.86'
stm_address ='169.254.137.114'
my_port     = 50000
stm_port    = 54321

with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as s: #ソケット作成 IPv4(AF_INET) UDP(SOCK_DGRAM)
    s.bind((my_address,my_port))
    s.connect((stm_address,stm_port))
    s.send(b"") #ここ入れないとなぜかデータ受信ができない
    while True:
        data, addr = s.recvfrom(1024)
        for i in range(8): 
            print(int(data[i]),end=",")

        print("")

```
原因が良く分かっていないが、一度データを送らないと受信が開始されない。また送る先も正しくマイコンに送らないと受信が始まらない。一度データを送れるようになるとマイコンをリセットしてもLABケーブルの抜き差しをしても、受信はできるため送信は常にできているように思われる(Wiresharkでも常にデータは送られている)ため、やはり受信側に問題がありそう。

### 実行結果
![](../python_socket_test.JPG)
マイコンからのデータの送信とPC側でのデータの受信を確認できた