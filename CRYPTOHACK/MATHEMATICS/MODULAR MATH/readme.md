# MATHEMATICS

这一章节讲述密码学中一些数学原理。在学习分组密码和 RSA 章节之前，可以先不学本节。

本节第一部分会讲述模数学相关知识，随后会讲一些格的知识。

## 一、模数学

### 1.1 二次剩余

我们已见识过了模算数中的乘法和除法，现在来学习一下如何求一个整数的模平方根。

对于接下来的讲解，定义模数 $p=29$ ，整数 $a=11$，计算 a 的平方在 模 29 下的值：

 $a^2=5\,mod\,29$

因为 $a=11,a^2=5$ ，所以我们说 5 的平方根是 11（在模 29 下）。

现在让我们考虑一下 18 的平方根。从以上讨论我们知道现在我们要求一个数 a 使得：

$a^2=18\,mod\,29$

一个很自然的想法是从 a=1 开始循环到 a = p-1。此处 p = 29，并不是很大，所以我们可以很快找到正确的 a。

写一个 python 脚本感受一下：

```python
p = 29
tar = 18
for a in range(1,p):
    if ((a**2) % p == tar):
        print(a)
```

运行完毕发现并没有找到这样的 a。也就是说在 $a\in\,F_p^*$ 找不到一个合适的 a 使得：

$a^2=18$

从以上结果我们可以看到，$F_p^*$ 中的每个元素并非都会有一个模平方根，实际上在 $F_p^*$ 中有一半的元素是没有模平方根的。

> 我们说整数 x 是一个二次剩余，如果存在整数 a 使得 $a^2=x\,mod\,p$，也就是 x 有模平方根。
>
> 如果没有，我们称 x 是一个二次非剩余。



**下表中有三个数，其中有两个二次非剩余，一个二次剩余，请你找出这个二次剩余并求出其模平方根，并提交较小的那个根作为 flag。**

> 如果 $a^2=x$，那么 $(-a)^2=x$，所以如果 x 是某个有限域中的二次剩余，那么其肯定有两个模平方根。

```python
p = 29
ints = [14, 6, 11]
```

答案：

```python
p = 29
ints = [14, 6, 11]

qr = [a for a in range(p) if pow(a,2,p) in ints]
print(f"flag {min(qr)}")
```

### 1.2 勒让德符号

之前我们通过枚举来判断一个整数是否是二次剩余，因为我们当时所用的模数比较小，所以这个方法也是很快的。但是随着模数 p 的增加，该方法显然变得不切实际。

幸运地是，勒让德发明了一个公式/符号，能够快速地判断一个整数是否是二次剩余。在说明这个公式前，我们先阐述一下二次剩余的相关性质：

1. 二次剩余乘以二次剩余等于二次剩余
2. 二次剩余乘以二次非剩余等于二次非剩余
3. 二次非剩余乘以二次非剩余等于二次剩余

> 总结一下可以得出：同类相乘就可以得到二次剩余
>
> 快捷的记法是把二次剩余记作 +1，二次非剩余记作 -1

现在让我们正式开始介绍勒让德符号，这个符号能够帮我们高效地确定一个整数是否是模一个奇素数 p 的二次剩余。

勒让德符号：$(a/p)\equiv\,a^{({p-1})/2}\,mod\,p$，该符号遵循以下性质：

1. 若 a 是一个二次剩余，并且 p 不能整除 a，那么 (a/p)=1
2. 若 a 是一个模 p 的二次非剩余，那么 (a/p)=-1
3. 若 p 整除 a，那么 (a/p)=0

以上式子告诉我们，如果想确定 a 是否是模 p 的二次剩余，那么只需要计算 $a^{(p-1)/2}\,mod\,p$ 即可，如果结果是 1，那么 a 是二次剩余。

#### 实操环节

给定一个 1024 比特的素数和 10 个整数，请你找到二次剩余并计算它们的平方根（提交大的那个作为 flag）

> 你可能会疑惑，勒让德符号仅仅告诉我们 a 是否是二次剩余，但是并没有告诉我们如何求出二次剩余的平方根，所以我们怎么办呢？
>
> 本题的素数 p 模 4 的余数是3，这有什么用吗？
>
> 我们要知道的是，对于具有 p=3 mod 4 这样性质的素数 p 来说，我们可以很容易地计算在模 p 下的二次剩余的平方根。答案在[网上](https://math.stackexchange.com/questions/1816258/quadratic-residue-p-equiv-3-pmod4)都有，但是你也可以利用费马小定理自己推导出来！

```python
#!/usr/bin/python3
p = ?
ints = [?]
quadr_res = [n for n in ints if pow(n, (p-1)//2, p) == 1][0] 
# with the legendre simbol, i'm looking for a number that satisfied the equation (a / p) == 1 with (a / p) the legendre simbol

# after i've found this i calculate it
# i known that (a / p) == 1, then
# (a / p) * a == a that is
# a **((p - 1) // 2) * a == a that is
# a ** ((p + 1)// 2) == a

# i'm looking for the square root of a mod p, that is the same as
# the square root of a ** ((p + 1)// 2) mod p.
# it is likely that that square root is a ** ((p + 1) // 4) then (just by dividing the denominator by 2)
# which is a good choice because p = 3 mod 4 meaning that there exists
# k such that 4k + 3 = p.
# substituting we bet that 
# a ** ((p + 1) // 4) = a ** ((4k + 3 + 1) // 4) = a ** ((4k + 4) // 4) = a ** (k + 1) which is a valid number
# as it is an integer

print(pow(quadr_res, (p + 1) // 4, p))
```

### 1.3 模平方根

上一节介绍的求平方根的算法是针对具有特定性质的素数 p 的，那么是否存在一个高效的算法能求解对于所有素数 p 的平方根呢？

答案是有的，算法的名称叫做 `Tonelli-Shanks`。

首先我们需要知道的是，除了 2 以外，所有的素数都具有以下两种性质之一：

1. p = 3 mod 4
2. p = 1 mod 4

对于具有第一种性质的素数 p，如何快速求平方根已在上节探讨过。

现在还剩下的事情是对于第二种性质的素数 p 如何求平方根，这时候就要用到我们的 Tonelli-Shanks 算法。对于

$r^2\equiv\,a\,\,mod\,p$ 这样的同余式，该算法能够求解 r。

> 注意，该算法对于模数不是素数的同余式无效！找到模合数的平方根等价于素因子分解，这是一个困难问题。

Tonelli-Shanks 主要用于寻找椭圆曲线的横坐标，算法的操作稍微有些复杂，所以我们不会具体地讨论它。

好在算法的实现十分容易找到，并且 sage 内置了该算法！

#### 实操环节

给定一个 2048 比特的素数 p，找到模 p 下的 a 的模平方根并提交小的那个作为 flag。

```python
from sage.rings.finite_rings.integer_mod import square_root_mod_prime

a = ?
p = ?

print(square_root_mod_prime(Mod(a, p), p)) # 找到在模p下a的平方根
```

### 1.4 中国剩余定理

- 如果一个线性同余方程组各模数之间是互素的，那么中国剩余定理可以给出该同余方程组的唯一解。

这意味着对于一组任意整数集 $a_i$ 、两两互素的整数集 $n_i$，以下式子成立：

```
x ≡ a1 mod n1
x ≡ a2 mod n2
...
x ≡ an mod nn
```

> 两两互素是说从 $n_i$ 中随便挑出两个数来，这两个数的最大公因子为 1

并且存在一个唯一解 `x ≡ a mod N`，其中`N = n1 * n2 * ... * nn`.

在密码学中，我们通常使用中国剩余定理来帮助我们将一个非常大的整数问题转化为一些较小的、更容易的整数问题。

#### 实操环节

给定以下线性同余方程组：

```
x ≡ 2 mod 5
x ≡ 3 mod 11
x ≡ 5 mod 17
```

找到一个整数 a，使得下式成立：

```
x ≡ a mod 935
```

> 不要忘记，如果 $x\equiv\,a\,\mod\,p$ ，那么可以将 x 写成 $x=a+k*p$ 的形式，其中k是任意整数。