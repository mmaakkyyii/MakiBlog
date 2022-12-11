---
title: "STM32のスタートアップルーチンを理解する1"
date: 2022-05-29T12:06:14+09:00
draft: true
categories: ["組み込み"]
tags: ["STM32","ARM","アセンブラ"]
---

アセンブラの各命令については[こちら](https://sourceware.org/binutils/docs-2.24/as/index.html#Top)を参考にしていく

startup_stm32g431vbtx.sを上から読んでいく

"."から始まる命令はAssembler Directives(アセンブラ指示命令)といって

```
  .syntax unified
	.cpu cortex-m4
	.fpu softvfp
	.thumb

.global	g_pfnVectors
.global	Default_Handler

```
## .global
```.global symbol```で```symbol```をリンカスクリプトなど外部からアクセスできるようになります。

https://sourceware.org/binutils/docs-2.24/as/Global.html#Global

## .word
```.word expressions```

https://sourceware.org/binutils/docs-2.24/as/Word.html#Word