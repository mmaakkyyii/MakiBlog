---
title: "STM32CubeIDEにCMSISを導入する"
date: 2022-05-04T23:27:26+09:00
draft: false
categories: ["組み込み"]
tags: ["STM32","STM32CubeIDE","CMSIS"]
---

# はじめに
いろいろな計算をさせる時にCMSISが使いたくなったので導入方法のメモをしておく。  
環境はWin10 STM32CubeIDE1.7.0 マイコンはSTM32G431VBTx(STSPIN32G4)を使います。

# CMSIS-DSPの導入
## 1.実行ファイルの追加
STM32CubeIDEの[リポジトリフォルダ](/posts/post20/)の中の
\Repository\STM32Cube_FW_G4_V1.4.0\Drivers\CMSIS\DSP\Lib\ARM\arm_cortexM4lf_math.lib  
と  
\Repository\STM32Cube_FW_G4_V1.4.0\Drivers\CMSIS\DSP\Lib\GCC\libarm_cortexM4lf_math.a  
をそれぞれプロジェクト内の適当な場所にコピーしてきます。(今回はプロジェクトのディレクトリ直下に置きました)

![](../img/CMSIS_files.JPG)
## 2.インクルードファイルの追加
また、\Repository\STM32Cube_FW_G4_V1.4.0\Drivers\CMSIS\DSP\Include 内の  
arm_common_tables.h  
arm_const_structs.h  
arm_math.h  
をそれぞれパスが通っている場所にコピーします。(今回はCore/Inc内にコピーしました)  

もしかしたら最初からパス通ってるっぽいのでこれはやる必要ないかも？(要検証)
![](../img/CMSIS_inc_memo.JPG)

## 3.インクルードパスの設定等
プロジェクトのPropertiesより、C/C++ Buuild > Setting > Tool Settings > MCU G++ Linker > Librariesより、Librarise (-l) add...で```arm_cortexM4lf_math```を追加する。
![](../img/CMSIS_setting.JPG)
このようになっていればおｋ

## 4.ARM_MATH_CM4の定義
プロジェクトのPropertiesより、C/C++ Buuild > Setting > Tool Settings > MCU GCC Compiler > Preprocessorより```ARM_MATH_CM4```を追加 ここは使うマイコンによって変わってくるものだと思いますが今回はこれで。
コード内でも定義できますが、どこからでも定義している状態にしたほうがいい気がするのでこちらで定義しておきます。
![](../img/CMSIS_define_setting.JPG)
# 動作させる
今回はちょうど動かしているブラシレスモータドライバ用のコードの一部で計算速度の比較をしてみます。
```C
#include "arm_math.h"

//~~中略~~//

      HAL_GPIO_WritePin(LED_RED_GPIO_Port, LED_RED_Pin,GPIO_PIN_SET);
//    int Vu=(int)( V*sin(omega*t)+V );
//    int Vv=(int)( V*sin(omega*t - dir* 2*pi/3.0)+V );
//    int Vw=(int)( V*sin(omega*t + dir* 2*pi/3.0)+V );
      int Vu=(int)( V*arm_sin_f32(omega*t)+V );
      int Vv=(int)( V*arm_sin_f32(omega*t - dir* 2*pi/3.0)+V );
      int Vw=(int)( V*arm_sin_f32(omega*t + dir* 2*pi/3.0)+V );
      HAL_GPIO_WritePin(LED_RED_GPIO_Port, LED_RED_Pin,GPIO_PIN_RESET);

```
こちらをsin(math.h)とarm_sin_f32(CMSIS)をそれぞれ実行します。  
またこのとき計算中にGPIOポートをHIGHにしているのでオシロスコープで時間を計測します。

math.hのsinでは約49.6 us,CMSIS-DSPでは13.4 usとなりおそよ1/4程度計算時間を短縮することができました。  
![math.h](../img/sin_math.bmp)
![CMSIS-DSP](../img/sin_CMSIS-DSP.bmp)


やはりCMSISライブラリを積極的に使った方がよいかと思いました。三角関数以外にもクラーク変換、パーク変換といったベクトル制御などで使えそうな関数やデジタルフィルタなどもあるのでいろいろ試していきたいです。

### 参考
[STM32 CubeIDE環境で、CMSIS-DSPを使う方法](https://ioloa.com/?p=81)
[](https://community.st.com/s/article/configuring-dsp-libraries-on-stm32cubeide)