---
title: "Raspberry Pi PicoでGPIOを使う"
date: 2022-06-04T16:43:52+09:00
draft: false
categories: ["組み込み"]
tags: ["RP2040"]
---

# GPIO概要
[Raspberry Pi Pico SDKのドキュメント](https://raspberrypi.github.io/pico-sdk-doxygen/group__hardware__gpio.html)と実際に手元で動かしたコードについて説明していく
Raspberry Pi Picoに搭載されているRP2040のGPIOは36個のGPIOピンがあるが、その中でおQSPIピンは外付けフラッシュとの通信用に使われる。そのため実際にはGPIO0からGPIO29が自由に利用できるGPIOピンになっている。またその中でもGPIO26-29はADCとして利用できるピンになっている。  
GPIOピンにはSPI、UARTといった機能も利用することができるようになっていて実際にどのピンで何ができるかはデータシートを参照して確認する必要がある。以下はその抜粋  
|GPIO|F1|F2|F3|F4|F5|F6|F7|F8|F9|
|---|---|---|---|---|---|---|---|---|---|
0|SPI0 RX|UART0 TX|I2C0 SDA|PWM0 A|SIO|PIO0|PIO1||USB OVCUR DET|
1|SPI0 CSn|UART0 RX|I2C0 SCL|PWM0 B|SIO|PIO0|PIO1||USB VBUS DET|
2|SPI0 SCK|UART0 CTS|I2C1 SDA|PWM1 A|SIO|PIO0|PIO1||USB VBUS EN|
3|SPI0 TX|UART0 RTS|I2C1 SCL|PWM1 B|SIO|PIO0|PIO1||USB OVCUR DET|
4|SPI0 RX|UART1 TX|I2C0 SDA|PWM2 A|SIO|PIO0|PIO1||USB VBUS DET|
5|SPI0 CSn|UART1 RX|I2C0 SCL|PWM2 B|SIO|PIO0|PIO1||USB VBUS EN|
6|SPI0 SCK|UART1 CTS|I2C1 SDA|PWM3 A|SIO|PIO0|PIO1||USB OVCUR DET|
7|SPI0 TX|UART1 RTS|I2C1 SCL|PWM3 B|SIO|PIO0|PIO1||USB VBUS DET|
8|SPI1 RX|UART1 TX|I2C0 SDA|PWM4 A|SIO|PIO0|PIO1||USB VBUS EN|
9|SPI1 CSn|UART1 RX|I2C0 SCL|PWM4 B|SIO|PIO0|PIO1||USB OVCUR DET|
10|SPI1 SCK|UART1 CTS|I2C1 SDA|PWM5 A|SIO|PIO0|PIO1||USB VBUS DET|
11|SPI1 TX|UART1 RTS|I2C1 SCL|PWM5 B|SIO|PIO0|PIO1||USB VBUS EN|
12|SPI1 RX|UART0 TX|I2C0 SDA|PWM6 A|SIO|PIO0|PIO1||USB OVCUR DET|
13|SPI1 CSn|UART0 RX|I2C0 SCL|PWM6 B|SIO|PIO0|PIO1||USB VBUS DET|
14|SPI1 SCK|UART0 CTS|I2C1 SDA|PWM7 A|SIO|PIO0|PIO1||USB VBUS EN|
15|SPI1 TX|UART0 RTS|I2C1 SCL|PWM7 B|SIO|PIO0|PIO1||USB OVCUR DET|
16|SPI0 RX|UART0 TX|I2C0 SDA|PWM0 A|SIO|PIO0|PIO1||USB VBUS DET|
17|SPI0 CSn|UART0 RX|I2C0 SCL|PWM0 B|SIO|PIO0|PIO1||USB VBUS EN|
18|SPI0 SCK|UART0 CTS|I2C1 SDA|PWM1 A|SIO|PIO0|PIO1||USB OVCUR DET|
19|SPI0 TX|UART0 RTS|I2C1 SCL|PWM1 B|SIO|PIO0|PIO1||USB VBUS DET|
20|SPI0 RX|UART1 TX|I2C0 SDA|PWM2 A|SIO|PIO0|PIO1|CLOCK GPIN0|USB VBUS EN|
21|SPI0 CSn|UART1 RX|I2C0 SCL|PWM2 B|SIO|PIO0|PIO1|CLOCK GPOUT0|USB OVCUR DET|
22|SPI0 SCK|UART1 CTS|I2C1 SDA|PWM3 A|SIO|PIO0|PIO1|CLOCK GPIN1|USB VBUS DET|
23|SPI0 TX|UART1 RTS|I2C1 SCL|PWM3 B|SIO|PIO0|PIO1|CLOCK GPOUT1|USB VBUS EN|
24|SPI1 RX|UART1 TX|I2C0 SDA|PWM4 A|SIO|PIO0|PIO1|CLOCK GPOUT2|USB OVCUR DET|
25|SPI1 CSn|UART1 RX|I2C0 SCL|PWM4 B|SIO|PIO0|PIO1|CLOCK GPOUT3|USB VBUS DET|
26|SPI1 SCK|UART1 CTS|I2C1 SDA|PWM5 A|SIO|PIO0|PIO1||USB VBUS EN|
27|SPI1 TX|UART1 RTS|I2C1 SCL|PWM5 B|SIO|PIO0|PIO1||USB OVCUR DET|
28|SPI1 RX|UART0 TX|I2C0 SDA|PWM6 A|SIO|PIO0|PIO1||USB VBUS DET|
29|SPI1 CSn|UART0 RX|I2C0 SCL|PWM6 B|SIO|PIO0|PIO1||USB VBUS EN|

# GPIOピンの内部回路
![](../img/RP2040_pad.JPG)

出力の設定項目としては、
* 出力Enable
* 駆動できる電流の設定(Drive Strength)
* スルーレート(Slew Rate)がそれぞれ

入力の設定には
* 入力Enable
* シュミットトリガー
* プルアップ/プルダウン

# 初期化(入出力モード)
以降の設定にはGPIOの番号を指定するものとマスクを使って一度に指定する関数のそれぞれが用意されている。
```C
gpio_init(uint gpio);
```
引数に利用するGPIOの番号を入れて入出力モード(GPIO_FUNC_SIO)にセットする。  
または
```C
gpio_init_mask(uint gpio_mask);
```
で1になっているビットのGPIOポートを入出力モードにする。また出力Enableを1にしてデフォルトのハイインピーダンスから出力可能にします。

入出力の設定
```
gpio_set_dir(uint gpio, bool out)
```
out==trueで出力  
out==falseで入力

# 出力ポートの設定
## ドライブ電流
```
gpio_set_drive_strength(uint gpio, enum gpio_drive_strength drive)
enum  gpio_drive_strength { GPIO_DRIVE_STRENGTH_2MA = 0, GPIO_DRIVE_STRENGTH_4MA = 1, GPIO_DRIVE_STRENGTH_8MA = 2, GPIO_DRIVE_STRENGTH_12MA = 3 }
```
gpio_drive_strengthはデフォルトは1(GPIO_DRIVE_STRENGTH_4MA)で4 mAになっています。(データシートTable 351より)  
また設定できる値は2,4,8,12 mAです。この電流値は当然負荷によって変わりますが設定によって出力電圧が変化するので大きめの電流を流すときには注意したほうがよさそうです。(インジケーター用のLEDと出力先のロジック回路などに接続する時とか？) 
特性は以下の通りになっています。  またRP2040がトータルで出力できる電流値は50 mAなのでSTM32などと比べるとすこし小さめかもしれないです。

![](../img/RP2040_drive_strength.JPG)  
(データシートFigure169より)

## スルーレート
速いモードと遅いモードが選べる。デフォルトは遅いモード
```
gpio_set_slew_rate(uint gpio, enum gpio_slew_rate slew)
gpio_slew_rate { GPIO_SLEW_RATE_SLOW = 0, GPIO_SLEW_RATE_FAST = 1 }
```
これもドライブ電流の設定によって特性が変わりそうなので何とも言えないですが、速さを設定できるようになっています。

# 入力ポートの設定
## プルアッププルアップ
RP2040は内部プルアップおよびプルダウンが可能です。設定方法は3種類あって
```
gpio_set_pulls(uint gpio, bool up, bool down)
gpio_pull_up (uint gpio)
gpio_pull_down (uint gpio)
```
またそれらを解除するには
```
gpio_disable_pulls (uint gpio)
```
という関数があります。

## ヒステリシス設定
```
gpio_set_input_hysteresis_enabled (uint gpio, bool enabled)
```
シュミットトリガーを利用できるようにします。

# 設定の確認
Pico SDKにはgpio_is_hogehogeという名前の関数が用意されているので上で設定した項目を確認することもできます。

# 入力割り込み
入力割り込みには
```
gpio_set_irq_enabled_with_callback(uint gpio, uint32_t events, bool enabled, gpio_irq_callback_t callback) {
```
を使います。
eventは
```
gpio_irq_level { GPIO_IRQ_LEVEL_LOW = 0x1u, GPIO_IRQ_LEVEL_HIGH = 0x2u, GPIO_IRQ_EDGE_FALL = 0x4u, GPIO_IRQ_EDGE_RISE = 0x8u }
```
でGPIOのどのタイミングで割り込みさせるか指定できます。各ビットが1になればよいので```(GPIO_IRQ_EDGE_FALL|GPIO_IRQ_EDGE_RISE)```で立ち上がり立ち下がりで割込みさせることも可能です。
割り込み関数は一コアにつき一つしか指定できないため、複数のGPIOによる割込みをするためには、次のようにコールバック関数でGPIOによる条件分けが必要になります。
```C
#include <stdio.h>
#include "pico/stdlib.h"

int counter0=0;
int counter1=0;
uint sw_pin0=15;
uint sw_pin1=16;

void gpio_callback0(uint gpio, uint32_t event){
    if(gpio==sw_pin0){
        gpio_set_irq_enabled(sw_pin0, 4, false);

        counter0++;
        gpio_set_irq_enabled(sw_pin0, 4, true);
    }
    if(gpio==sw_pin1){
        gpio_set_irq_enabled(sw_pin1, 8, false);

        counter1++;
        gpio_set_irq_enabled(sw_pin1, 8, true);
    }
}

int main(){
    setup_default_uart();

    gpio_init(PICO_DEFAULT_LED_PIN);
    gpio_set_dir(PICO_DEFAULT_LED_PIN, GPIO_OUT);

    gpio_init(sw_pin0);
    gpio_set_dir(sw_pin0, GPIO_IN);
    gpio_set_input_hysteresis_enabled(sw_pin0,1);
    gpio_init(sw_pin1);
    gpio_set_dir(sw_pin1, GPIO_IN);
    gpio_set_input_hysteresis_enabled(sw_pin1,1);

    gpio_set_irq_enabled_with_callback(sw_pin1, GPIO_IRQ_EDGE_FALL|GPIO_IRQ_EDGE_RISE, 1 ,gpio_callback0 );

    gpio_set_irq_enabled_with_callback(sw_pin0, GPIO_IRQ_EDGE_FALL, 1 ,gpio_callback0 );
    
    uint8_t i=0;
    int led=0;
    while(1){
        gpio_put(PICO_DEFAULT_LED_PIN,led);
        led=1-led;
        printf("%4d,%d,%d\n",i++,counter0,counter1);
        sleep_ms(200);
    }
}
```
