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
问题\ \ 设\ x_n=\int_{0}^\frac{\pi}{2}(\sin^{2n}\theta+\cos^{2n}\theta)\mathrm{d}{\theta}\ (n=1,2,\cdots),\ 求\ \lim_{n\rightarrow\infin}\frac{x_n}{x_{n-1}}. \hspace{20cm} \\\\
\textcolor{blue}{解答1：}\ x_n=\int_{0}^\frac{\pi}{2}(\sin^{2n}\theta+\cos^{2n}\theta)\mathrm{d}{\theta}=\int_{0}^\frac{\pi}{2}\sin^{2n}\theta\mathrm{d}{\theta}+\int_{0}^\frac{\pi}{2}\cos^{2n}\theta\mathrm{d}{\theta}, \hspace{20cm} \\\\
\begin{aligned}
设\ a_n&=\int_{0}^\frac{\pi}{2}\sin^{2n}\theta\mathrm{d}{\theta},\ b_n=\int_{0}^\frac{\pi}{2}\cos^{2n}\theta\mathrm{d}{\theta},\ 则有\ x_n=a_n+b_n. \\\\
a_n&=\int_{0}^\frac{\pi}{2}\sin^{2n}\theta\mathrm{d}{\theta} & b_n&=\int_{0}^\frac{\pi}{2}\cos^{2n}\theta\mathrm{d}{\theta} \\\\
&=\int_{0}^\frac{\pi}{2}\sin^{2n-1}\theta{\sin\theta}\mathrm{d}{\theta} & &=\int_{0}^\frac{\pi}{2}\cos^{2n-1}\theta{\cos\theta}\mathrm{d}{\theta} \\\\
&=\int_{0}^\frac{\pi}{2}\sin^{2n-1}{\theta}\mathrm{d}(-\cos\theta) & &=\int_{0}^\frac{\pi}{2}\cos^{2n-1}{\theta}\mathrm{d}(\sin\theta) \hspace{0.5cm} (分部积分公式\downarrow) \\\\
&=\lbrack \sin^{2n-1}{\theta}(-\cos\theta)\rbrack_{0}^{\frac{\pi}{2}} - \int_{0}^{\frac{\pi}{2}} (-\cos{\theta})\mathrm{d}(\sin^{2n-1}{\theta}) & &=\lbrack \cos^{2n-1}{\theta}\sin{\theta}\rbrack_{0}^{\frac{\pi}{2}} - \int_{0}^{\frac{\pi}{2}}\sin{\theta}\mathrm{d}(\cos^{2n-1}{\theta}) \\\\
&=0+\int_{0}^{\frac{\pi}{2}} \cos{\theta}\cdot(2n-1)\sin^{2n-2}{\theta}\cos{\theta}\mathrm{d}{\theta} & &=0-\int_{0}^\frac{\pi}{2}\sin{\theta}\cdot(2n-1)\cos^{2n-2}{\theta}(-\sin\theta)\mathrm{d}{\theta} \\\\
&=(2n-1)\int_{0}^{\frac{\pi}{2}} \sin^{2n-2}{\theta} \cos^2{\theta}\mathrm{d}{\theta} & &=(2n-1)\int_{0}^{\frac{\pi}{2}} \cos^{2n-2}{\theta} \sin^2{\theta}\mathrm{d}{\theta} \\\\
&=(2n-1)\int_{0}^{\frac{\pi}{2}} \sin^{2n-2}{\theta} (1-\sin^2{\theta})\mathrm{d}{\theta} & &=(2n-1)\int_{0}^{\frac{\pi}{2}} \cos^{2n-2}{\theta} (1-\cos^2{\theta})\mathrm{d}{\theta} \\\\
&=(2n-1)\int_{0}^{\frac{\pi}{2}} (\sin^{2n-2}{\theta} - \sin^{2n}{\theta})\mathrm{d}{\theta} & &=(2n-1)\int_{0}^{\frac{\pi}{2}} (\cos^{2n-2}{\theta} - \cos^{2n}{\theta})\mathrm{d}{\theta} \\\\
&=(2n-1)(\int_{0}^{\frac{\pi}{2}} \sin^{2n-2}{\theta}\mathrm{d}{\theta} - \int_{0}^{\frac{\pi}{2}} \sin^{2n}{\theta}\mathrm{d}{\theta}) & &=(2n-1)(\int_{0}^{\frac{\pi}{2}} \cos^{2n-2}{\theta}\mathrm{d}{\theta} - \int_{0}^{\frac{\pi}{2}} \cos^{2n}{\theta}\mathrm{d}{\theta}) \\\\
&=(2n-1)(a_{n-1}-a_{n}) & &=(2n-1)(b_{n-1}-b_{n}) \\\\
&=(2n-1)a_{n-1} - (2n-1)a_{n} & &=(2n-1)b_{n-1} - (2n-1)b_{n} \\\\
\end{aligned} \\\\[0.5cm]
因此\ \begin{aligned}
(2n-1)(a_{n-1}-a_{n})=a_{n}+(2n-1)a_{n}=2na_{n} \\\\
(2n-1)(b_{n-1}-b_{n})=b_{n}+(2n-1)b_{n}=2nb_{n} \\\\
\end{aligned} \implies \begin{aligned}
a_{n}=\dfrac{2n-1}{2n}a_{n-1} \\\\
b_{n}=\dfrac{2n-1}{2n}b_{n-1}
\end{aligned}\ . \\\\
所以\ x_{n}=a_{n}+b_{n}=\dfrac{2n-1}{2n}a_{n-1}+\dfrac{2n-1}{2n}b_{n-1}=\dfrac{2n-1}{2n}(a_{n-1}+b_{n-1})=\dfrac{2n-1}{2n}x_{n-1}\implies\dfrac{x_n}{x_{n-1}}=\dfrac{2n-1}{2n} \\\\
\left(其中\ x_1=\int_0^{\frac{\pi}{2}}(\sin^2\theta+\cos^2{\theta})\mathrm{d}{\theta}=\int_0^{\frac{\pi}{2}}1\cdot\mathrm{d}{\theta}=\cfrac{\pi}{2}\right) \hspace{20cm} \\\\
所以\ \lim_{n\rightarrow\infin}\frac{x_n}{x_{n-1}}=\lim_{n\rightarrow\infin}\frac{2n-1}{2n}=1. \hspace{20cm} \\\\[1cm]
\textcolor{blue}{解答2：}\ 用点火公式，即华理士(Wallis)公式： \hspace{20cm} \\\\
\int_{0}^\frac{\pi}{2}\sin^{n}x\mathrm{d}x=\int_{0}^\frac{\pi}{2}\cos^{n}x\mathrm{d}x= \begin{cases}
\cfrac{n-1}{n}\cdot\cfrac{n-3}{n-2}\cdots \cfrac{2}{3}\cdot 1 &\text{if } n是奇数. \\\\
\cfrac{n-1}{n}\cdot\cfrac{n-3}{n-2}\cdots \cfrac{1}{2}\cdot \cfrac{\pi}{2} &\text{if } n是偶数. \\\\
\end{cases} \hspace{10cm} \\\\[0.5cm]
2n恒为偶数，故\ \begin{aligned} x_{n}&=2(\frac{2n-1}{2n-2} \cdot \frac{2n-3}{2n-4} \cdot \cdots \cdot \frac{1}{2} \cdot \frac{\pi}{2}) \\\\ 
x_{n-1}&=2(\frac{2(n+1)-1}{2(n-1)-2} \cdot \frac{2(n-1)-3}{2(n-1)-4} \cdot \cdots \cdot \frac{1}{2} \cdot \frac{\pi}{2})
\end{aligned} \hspace{10cm} \\\\[0.5cm]
\frac{x_{n}}{x_{n-1}} 约分后=\frac{2n-1}{2n-2} \ ,\ 
所以\ \lim_{n\rightarrow\infin}\frac{x_n}{x_{n-1}}=\lim_{n\rightarrow\infin}\frac{2n-1}{2n-2}=1. \hspace{20cm} 
$$