---
title: "STM32CubeIDEでSWO/SWV (Serial Wire Output/Viewer)を使う"
date: 2022-04-01T22:20:00+09:00
draft: false
categories: ["組み込み"]
tags: ["STM32","SWD"]
---
# STM32の
# SWDについて
ここの説明より、  
http://www.tokudenkairo.co.jp/jtag/adv2018/08.php  
SWDはARM Cortex用のデバッグ用の独自規格だそうです。

Nucleoだと[UM1724](https://www.st.com/resource/en/user_manual/um1724-stm32-nucleo64-boards-mb1136-stmicroelectronics.pdf)より
![](../img/nucleo_stlink.JPG)  
CN4にデバッグ用のピンが生えています。
プログラムの書き込みデバッグだけであればSWCLK、SWDIO、NRST、GNDだけターゲットのマイコンに接続で来ていれば問題ないですが、さらにSWOを用いることによって、一ピンで内部の変数をPCに出力したりすることができます。

# SWVについて
ユーザーマニュアル[UM2609](https://www.st.com/resource/en/user_manual/dm00629856-description-of-the-integrated-development-environment-for-stm32-products-stmicroelectronics.pdf)の 4.Debug with Serial Wire Viewer tracing (SWV)にていろいろ説明されています。  
SWDはSWOピンとSWDポートを利用することでリアルタイムにマイコン内のデータ解析が可能になるものです。


# 使い方
今回はSTM32CubeIDE1.7.0とNucleo-F446REを使用
## SWO有効化設定(CubeMX)
* SYS-> Mode -> DebugをTrace Asynchronous SWにするとSWOピンが設定される。  
これをST-LinkであればSWOにあたるピンに接続されていればOK。

![](../img/SWV_cubemx_setting.JPG)

## SWV設定(STM32CubeIDE)
* Debug ConfigurationsでSWDインターフェースにチェックを入れて、SWVを有効にチェックを入れる。  
* Core Clockにはデバイスのクロックスピードを同じ値にする。この実際の値はDebugモードでSystemCoreClock
* SWVのポートはGDB接続のポートと同じものに設定する。
![](../img/SWV_debug_config.JPG)

## データの時系列データ
SWVデータトレースは4つまでの変数を表示できる。アドレスか、グローバル変数名でトレースする変数を指定できます。
* デバッグを開始し、Window->Show View->SWV->SWV Data Trace Timeline Graphを選択し、表示させる。
* トレースの設定(ツールバーボタン)より↓のメニューを開く。
![](../img/SWV_config.JPG)
* Data Traceを許可する(Enable)にチェックをいれ、変数名/アドレスを入れる。
* Debug開始してプログラムをスタートさせる前にStart Trace(赤い丸)ボタンを押してトレースを開始しておく。
![](../img/SWV_data_trace.png)
またWindow->Show View->SWV->SWV Data Traceでも同様にデータの数値をみることができる。

## ITMの設定
ITMは任意のデータをSWOピンに書き込むための機能を持っています。特にprintfなどの出力をSWVコンソールに表示させることなどができます。 
* ```syscalls.c```の```_write()```関数の中身を適当な場所で変更する。   
* デフォルトだとデータを送る部分が```__io_putchar(*ptr++)```となっているところを```ITM_SendChar(*ptr++);```で置き換える。  
```syscalls.c```の```_write()```関数はweak属性がついているのでmain.cなどで次のように置き換えた関数を宣言しておく。
``` C
int _write(int file, char *ptr, int len)
{
 int DataIdx;
 for (DataIdx = 0; DataIdx < len; DataIdx++)
 {
 ITM_SendChar(*ptr++);
 }
 return len;
}

```


これでprintfで出力したい文字列をITMポートに送ることができます。

## TIMコンソールにデータ表示
* SWVと同様に、デバッグを開始し、Window->Show View->SWV->SWV ITM Data Consoleを選択し、表示させる。
* トレースの設定よりメニューを開きITM Simulusポートの有効なポートの0番にチェックを入れる。
* Debug開始後にStart Trace(赤い丸)ボタンを押してトレースを開始しておき、プログラムをスタートさせるとprintfによるプリントデバッグができる。

ITM Simulusポートはデフォルトが0番で、ITM_SendCharの中身を見る限り↓、ポート番号を直書きしている気がする。(複数のコアがあるマイコンとかだったら複数ポート使ったりするのかな？)
``` C
/**
  \brief   ITM Send Character
  \details Transmits a character via the ITM channel 0, and
           \li Just returns when no debugger is connected that has booked the output.
           \li Is blocking when a debugger is connected, but the previous character sent has not been transmitted.
  \param [in]     ch  Character to transmit.
  \returns            Character to transmit.
 */
__STATIC_INLINE uint32_t ITM_SendChar (uint32_t ch)
{
  if (((ITM->TCR & ITM_TCR_ITMENA_Msk) != 0UL) &&      /* ITM enabled */
      ((ITM->TER & 1UL               ) != 0UL)   )     /* ITM Port #0 enabled */
  {
    while (ITM->PORT[0U].u32 == 0UL)
    {
      __NOP();
    }
    ITM->PORT[0U].u8 = (uint8_t)ch;
  }
  return (ch);
}
```


SWVを使うと変数をリアルタイムにトレース出来てCubeIDE上で時系列プロットが出来たりするので嬉しい。  
printfとかも簡単に使えて嬉しい。