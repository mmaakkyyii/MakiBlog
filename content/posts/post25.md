---
title: "Raspberry Pi Picoの環境構築 on WSL1"
date: 2022-04-04T00:26:07+09:00
draft: false
categories: ["組み込み"]
tags: ["RP2040"]
---

# はじめに
最近はマイコンがなさ過ぎるので新しいマイコンたちにも手を出していきたい。  
家で眠っているRaspberry Pi PicoをWin10のWSL1の上でビルド環境を作る。

# Raspberry Pi Pico
[Raspberry Pi Pico](https://www.raspberrypi.com/products/raspberry-pi-pico/)は[RP2040](https://www.raspberrypi.com/products/rp2040/)というRaspberry Piが作ったチップが乗ったマイコンボードです。

最近の半導体不足で製造されているものとは違う半導体プロセスで構築されていてチップ不足の影響が少ないらしい。
https://japan.zdnet.com/article/35182607/

特徴としては、[https://www.switch-science.com/catalog/7173/](https://www.switch-science.com/catalog/7173/) より  
* デュアルコア Arm Cortex-M0+ @ 133 MHz  
* 264 KB オンチップRAM  
* 最大16 MBのQSPIバスを介した外付けフラッシュメモリをサポート  
* DMAコントローラ  
* インターポレータおよび整数除算ペリフェラル  
* 30 GPIOピン（4ピンはアナログ入力として利用可能）  
* 2 × UART、2 × SPIコントローラ、2 × I2Cコントローラ  
* 16 × PWMチャンネル  
* 1 × USB 1.1コントローラ/PHY（ホスト/デバイスサポート）  
* 8 × Raspberry Pi Programmable I/O（PIO）ステートマシン  
* ドラッグアンドドロッププログラミングのためのUSBマスストレージブートモード（UF2サポート）  

適当なモータドライバ用のマイコンとしても使えそうかも？

# 環境
* Windows 10 
* WSL1

# 環境構築
基本的には [https://github.com/raspberrypi/pico-sdk](https://github.com/raspberrypi/pico-sdk)に従う

## CMake GCCコンパイラのインストール
```
sudo apt install cmake gcc-arm-none-eabi libnewlib-arm-none-eabi libstdc++-arm-none-eabi-newlib
```

## Raspberry Pi Pico SDKのインストール
方式はいくつかあるが、今回は```raspi_pico```というフォルダにSDKをgit cloneしてくる
```
mkdir raspi_pico
cd raspi_pico
git clone https://github.com/raspberrypi/pico-sdk.git
```

## プロジェクトの作成・ビルド
ここでは```pico_test```というプロジェクトを作成する
```
cd raspi_pico
mkdir pico_test
```
pico_sdk_import.cmakeファイルをプロジェクトのフォルダにコピーする。
```
cp ../pico-sdk/external/pico_sdk_import.cmake pico_sdk_import.cmake
```
pico_test内に次のCMakeLists.txtを作成
```
cmake_minimum_required(VERSION 3.13)

# initialize the SDK based on PICO_SDK_PATH
# note: this must happen before project()
include(pico_sdk_import.cmake)

project(pico_test)

# initialize the Raspberry Pi Pico SDK
pico_sdk_init()

# rest of your project
add_executable(hello_world
    hello_world.c
)
# Add pico_stdlib library which aggregates commonly used features
target_link_libraries(hello_world pico_stdlib)

# create map/bin/hex/uf2 file in addition to ELF.
pico_add_extra_outputs(hello_world)
```
またpico_test内に次のhello_world.cを作成
``` C
#include <stdio.h>
#include "pico/stdlib.h"

int main() {
    setup_default_uart();
    const uint LED_PIN1 = PICO_DEFAULT_LED_PIN;
    const uint LED_PIN2 = 3;
    gpio_init(LED_PIN);
    gpio_set_dir(LED_PIN, GPIO_OUT);
    gpio_init(LED_PIN2);
    gpio_set_dir(LED_PIN2, GPIO_OUT);

    while (true) {
        gpio_put(LED_PIN1,1);
        gpio_put(LED_PIN2,0);
        sleep_ms(250);
        gpio_put(LED_PIN1,0);
        gpio_put(LED_PIN2,1);
        sleep_ms(250);
        printf("Hello, world!\n");
    }
    return 0;
}
```
そして次のコマンドでビルドする
```
mkdir build
cd build
cmake .. -DPICO_SDK_PATH=/mnt/d/raspi_pico/pico-sdk
make hello_world
```
すると/build/hello_world.uf2が生成される。

# 書きこみ
Raspberry Pi Picoのリセットボタンを押した状態で、USBケーブルでPCと接続すると、ストレージとして認識される。このストレージに先ほど作成したhello_world.uf2をコピーすれば書き込み完了。
[データシート](https://datasheets.raspberrypi.com/pico/pico-datasheet.pdf)より、
![](../img/pico_pin_assign.JPG)
PICO_DEFAULT_LED_PINはGP25で、デフォルトのUARTは1,2番ピン、ボーレートは115200が利用されているので適当なUSBシリアル変換器とTera Termなどで接続すればprintfの出力も見れる。