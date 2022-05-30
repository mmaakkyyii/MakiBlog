---
title: "STM32CubeIDEとHALでTIMに合わせてADCをする"
date: 2022-05-07T18:30:47+09:00
draft: false
categories: ["組み込み"]
tags: ["STM32"]
---

# はじめに
モータドライバでローサイドのシャント抵抗に流れる電流測定は図のようにローサイドのFETがONになっているタイミングでシャント抵抗の電圧を計測する必要があります。
![](../img/current_measurement.png)
STM32では上下のFETのために2つの相補PWM(TIMx,TIMxN)が出力できるため上下のFETにこれを使い、タイマーのいいタイミングでトリガーを出してADCのタイミングを決めます。(上の図でいうとタイマーのカウンターが0になったタイミングは必ずFETがONになっていてそれ以外のタイミングだと上側のFETがONになって電流が流れないタイミングになることがある。)

# STM32 TIM PWM設定
PWMで必要な設定は次の通り  
図はリファレンスマニュアル[RM0440](https://www.st.com/resource/en/reference_manual/rm0440-stm32g4-series-advanced-armbased-32bit-mcus-stmicroelectronics.pdf)
より、

### タイマー周期
Countor Setting>Prescaler  
Countor Setting>Countor Period(auto-reload value)  
より、PWM周波数は

$$f_{PWM}=\frac{f_{timerclock}}{(prescaler+1)(countor+1)}$$

となる。Countor PeriodはPWN入力の分解能になるのでいい感じの整数になるように適宜設定する。
今回はタイマー周波数170 MHzで、プリスケーラは**17-1**, カウンタは **1000-1** とした。
ただし今回はカウンタをup/downとするので周期は半分になるため周期は5 kHzとなる。

### Counter Mode
* up:カウンターが1つづあがってauto-reload-1までカウントアップすると0に戻る  
* down:auto-reload-1から0までカウントダウンしてauto-reload-1に戻る  

Centor Aligned:0からauto-reloadまでカウントアップして0までカウントダウンする。出力比較割り込みのフラグのタイミングがmode1,2,3によって異なる
* Centor Aligned mode1:カウントダウン時  
* Centor Aligned mode2:カウントアップ時  
* Centor Aligned mode3:カウントアップ&カウントダウン時  

今回はカウンタが0になったタイミングで必ずLOWになるようにしたいので**Centor Aligned mode1**にする。

Centor Aligned modeではUpdate Event(UEV)はカウンタのオーバーフローとアンダーフローの際に発生します。

### Repetition Countor
UEVはCentor Aligned modeのときにはカウントダウン時とカウントアップ時に発生するがRepetition Countor(RCR)を1以上に設定するとUEVを間引いてトリガーを発生させることができる。  
データシートより
![](../img/stm32g4_tim_RCR.JPG)
Repetition Countor=**1**とすることで今回の目的であるカウンタが0のタイミングでトリガーを出すことができる。
ADC完了割り込みのタイミングでGPIOをトグルしたときの波形。  
黄色がPWM_N出力で青がGPIO  
#### RCR=0  
![](../img/RCR_0.bmp)  
#### RCR=1  
![](../img/RCR_1.bmp)  
RCR=1のときは

### Trigger Output(TRGO)
トリガーの設定、タイマーのコンペアマッチでトリガーをかけたりもできるけど今回はUpdate Eventを使う
* Trigger Event Selection TRGO:**Update Event**

### PWM mode
* PWM mode 1    
* PWM mode 2  

mode1はTIMx_CCRx(比較レジスタ)< TIMx_CNT(カウンタレジスタ)のときHIGHを出力  
mode2はその逆でTIMx_CCRx > TIMx_CNTのときHIGHを出力  
今回は**PWM mode 1**を用いる  

設定はこんな感じ
![](../img/PWM_TIM_setting.JPG)
## ADCの設定
今回はいま作っている基板でADC1とADC2を電流測定用ポートに接続しているので、**Dual injected simultaneous mode only**を使う

### ADC Injected ConversionMode

* External Trigger Source:**Timer 1 Trigger Out event**  
* External Trigger Conversion Edge:**Trigger deteection on the rising edge**
* Number Of Conversions: 

またNVIC setting よりADC1 and ADC2 global interruptにチェックを入れておく。

設定はこんな感じ
![](../img/ADC1_setting.JPG)
![](../img/ADC2_setting.JPG)



# 動作確認

```C
uint16_t adc_current[3];
uint16_t adc_bat;
uint16_t adc_ntc;

void HAL_ADCEx_InjectedConvCpltCallback(ADC_HandleTypeDef *hadc){
      HAL_GPIO_TogglePin(LED_RED_GPIO_Port,LED_RED_Pin);
      adc_current[0]=ADC1->JDR1;
      adc_current[1]=ADC1->JDR2;
      adc_current[2]=ADC2->JDR1;
      adc_tmp=ADC1->JDR3;
      adc_ntc=ADC2->JDR2;
      adc_bat=ADC2->JDR3;
}

void Init(){

//中略//

    HAL_ADCEx_Calibration_Start(&hadc1, ADC_SINGLE_ENDED);
    HAL_ADCEx_Calibration_Start(&hadc2, ADC_SINGLE_ENDED);

    HAL_ADC_Start(&hadc2);
    HAL_ADC_Start(&hadc1);

    HAL_ADCEx_InjectedStart_IT(&hadc1);

}

```

黄色のプロットがTIMxNで水色のプロットがGPIO
![](../img/ADC_timing.bmp)