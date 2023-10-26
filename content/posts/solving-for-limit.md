+++
title = "一道求解极限题"
date = 2023-10-26T10:50:00+08:00
draft = false
authors = ["Simon Tatham"]

[taxonomies]
categories = ["学而时习之"]
tags = ["数学"]

[extra]
lang = "zh_CN"
toc = false
copy = true
math = true
mermaid = false
outdate_alert = false
outdate_alert_days = 120
display_tags = true
truncate_summary = false
+++

<!--more-->
$$
\begin{aligned}
问题\ &\ 设\ x_n=\int_{0}^\frac{\pi}{2}(\sin^{2n}\theta+\cos^{2n}\theta)\mathrm{d}{\theta}\ (n=1,2,\cdots),\ 求\ \lim_{n\rightarrow\infin}\frac{x_n}{x_{n-1}}. \\\\
解答\ &\ x_n=\int_{0}^\frac{\pi}{2}(\sin^{2n}\theta+\cos^{2n}\theta)\mathrm{d}{\theta}=\int_{0}^\frac{\pi}{2}\sin^{2n}\theta\mathrm{d}{\theta}+\int_{0}^\frac{\pi}{2}\cos^{2n}\theta\mathrm{d}{\theta}, \\\\
设\ a_n&=\int_{0}^\frac{\pi}{2}\sin^{2n}\theta\mathrm{d}{\theta},\ b_n=\int_{0}^\frac{\pi}{2}\cos^{2n}\theta\mathrm{d}{\theta},\ 则有\ x_n=a_n+b_n.& \\\\
a_n&=\int_{0}^\frac{\pi}{2}\sin^{2n}\theta\mathrm{d}{\theta} & b_n&=\int_{0}^\frac{\pi}{2}\cos^{2n}\theta\mathrm{d}{\theta} \\\\
&=\int_{0}^\frac{\pi}{2}\sin^{2n-1}\theta{\sin\theta}\mathrm{d}{\theta} & &=\int_{0}^\frac{\pi}{2}\cos^{2n-1}\theta{\cos\theta}\mathrm{d}{\theta} \\\\
&=\int_{0}^\frac{\pi}{2}\sin^{2n-1}{\theta}\mathrm{d}(-\cos\theta) & &=\int_{0}^\frac{\pi}{2}\cos^{2n-1}{\theta}\mathrm{d}(\sin\theta) \ \ \ \ (分部积分公式\downarrow) \\\\
&=\lbrack \sin^{2n-1}{\theta}(-\cos\theta)\rbrack_{0}^{\frac{\pi}{2}} - \int_{0}^{\frac{\pi}{2}} (-\cos{\theta})\mathrm{d}(\sin^{2n-1}{\theta}) & &=\lbrack \cos^{2n-1}{\theta}\sin{\theta}\rbrack_{0}^{\frac{\pi}{2}} - \int_{0}^{\frac{\pi}{2}}\sin{\theta}\mathrm{d}(\cos^{2n-1}{\theta}) \\\\
&=0+\int_{0}^{\frac{\pi}{2}} \cos{\theta}\cdot(2n-1)\sin^{2n-2}{\theta}\cos{\theta}\mathrm{d}{\theta} & &=0-\int_{0}^\frac{\pi}{2}\sin{\theta}\cdot(2n-1)\cos^{2n-2}{\theta}(-\sin\theta)\mathrm{d}{\theta} \\\\
&=(2n-1)\int_{0}^{\frac{\pi}{2}} \sin^{2n-2}{\theta} \cos^2{\theta}\mathrm{d}{\theta} & &=(2n-1)\int_{0}^{\frac{\pi}{2}} \cos^{2n-2}{\theta} \sin^2{\theta}\mathrm{d}{\theta} \\\\
&=(2n-1)\int_{0}^{\frac{\pi}{2}} \sin^{2n-2}{\theta} (1-\sin^2{\theta})\mathrm{d}{\theta} & &=(2n-1)\int_{0}^{\frac{\pi}{2}} \cos^{2n-2}{\theta} (1-\cos^2{\theta})\mathrm{d}{\theta} \\\\
&=(2n-1)\int_{0}^{\frac{\pi}{2}} (\sin^{2n-2}{\theta} - \sin^{2n}{\theta})\mathrm{d}{\theta} & &=(2n-1)\int_{0}^{\frac{\pi}{2}} (\cos^{2n-2}{\theta} - \cos^{2n}{\theta})\mathrm{d}{\theta} \\\\
&=(2n-1)(\int_{0}^{\frac{\pi}{2}} \sin^{2n-2}{\theta}\mathrm{d}{\theta} - \int_{0}^{\frac{\pi}{2}} \sin^{2n}{\theta}\mathrm{d}{\theta}) & &=(2n-1)(\int_{0}^{\frac{\pi}{2}} \cos^{2n-2}{\theta}\mathrm{d}{\theta} - \int_{0}^{\frac{\pi}{2}} \cos^{2n}{\theta}\mathrm{d}{\theta}) \\\\
&=(2n-1)(a_{n-1}-a_{n}) & &=(2n-1)(b_{n-1}-b_{n}) \\\\
&=(2n-1)a_{n-1} - (2n-1)a_{n} & &=(2n-1)b_{n-1} - (2n-1)b_{n} \\\\
\end{aligned} \\\\
因此\ \begin{aligned}
(2n-1)(a_{n-1}-a_{n})=a_{n}+(2n-1)a_{n}=2na_{n} \\\\
(2n-1)(b_{n-1}-b_{n})=b_{n}+(2n-1)b_{n}=2nb_{n} \\\\
\end{aligned} \implies \begin{aligned}
a_{n}=\dfrac{2n-1}{2n}a_{n-1} \\\\
b_{n}=\dfrac{2n-1}{2n}b_{n-1}
\end{aligned}\ . \\\\
所以\ x_{n}=a_{n}+b_{n}=\dfrac{2n-1}{2n}a_{n-1}+\dfrac{2n-1}{2n}b_{n-1}=\dfrac{2n-1}{2n}(a_{n-1}+b_{n-1})=\dfrac{2n-1}{2n}x_{n-1}\implies\dfrac{x_n}{x_{n-1}}=\dfrac{2n-1}{2n} \\\\
\left(其中\ x_1=\int_0^{\frac{\pi}{2}}(\sin^2\theta+\cos^2{\theta})\mathrm{d}{\theta}=\int_0^{\frac{\pi}{2}}1\cdot\mathrm{d}{\theta}=\cfrac{\pi}{2}\right) \\\\
所以\ \lim_{n\rightarrow\infin}\frac{x_n}{x_{n-1}}=\lim_{n\rightarrow\infin}\frac{2n-1}{2n}=1.
$$