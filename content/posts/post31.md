---
title: "ブログのマークダウンで数式を使えるようにする"
date: 2022-05-08T02:50:54+09:00
draft: false
categories: ["blog"]
tags: ["blog","markdown"]
---

# やりたいこと
タイトルの通りこのブログ内で数式を書きたいのでマークダウンで数式をかけるようにする。
普通だとjavascriptで以下のようなコードを追加すればよい。
```
<script type="text/javascript" async src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
<script type="text/x-mathjax-config">
 MathJax.Hub.Config({
 tex2jax: {
 inlineMath: [['$', '$'] ],
 displayMath: [ ['$$','$$'], ["\\[","\\]"] ]
 }
 });
</script>
```
参考：[https://qiita.com/tomtsutom0122/items/e0ab6b6ccbd369db1aa2](https://qiita.com/tomtsutom0122/items/e0ab6b6ccbd369db1aa2)

これをmdファイル内に追加すればいいが、毎回追加するのは流石にやりたくないので、ヘッダーとかで必ず読み込むようにしておく。
今回はlayout\partials\site-navigation.htmlの最終行に上に示したコードをそのまま貼り付けておく。これで毎回MathJaxを読み込んでくれるので

$$
数式_{が}=\frac{入力}{できる}
$$

$$
F(s)=\int_0^\infty f(t)e^{-st}t
$$

とりあえず数式は使えるけどフォントをいい感じにしたいな…

