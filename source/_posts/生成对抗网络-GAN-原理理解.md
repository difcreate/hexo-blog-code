---
title: 生成对抗网络(GAN)原理理解
date: 2018-11-06 21:06:16
tags: [深度学习,GAN]
categories: [深度学习,GAN]
---

**Generative Adversarial Network**，就是大家耳熟能详的 **GAN**，由 Ian Goodfellow 首先提出，在这两年更是深度学习中最热门的东西，仿佛什么东西都能由 GAN 做出来。我最近刚入门 **GAN**，主要是在哔哩哔哩网站上学习李宏毅教授的**GAN**课程。现在总结一下思路，做一篇笔记。
<br>

---

# 1. GAN基础概念

**GAN**全称是Generative Adversarial Nets，中文叫做“**生成对抗网络**”。
在GAN中有2个网络，一个网络用于生成数据，叫做**generator**也就是“**生成器**”。另一个网络用于判别生成数据是否接近于真实，叫做**discriminator**也就是“**判别器**”。<br>
流程图如下所示：<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/%E6%B5%81%E7%A8%8B%E5%9B%BE.jpg?q-sign-algorithm=sha1&q-ak=AKIDWhVyPj0ivBduQzAthEtlQYRuV7ZESOvw&q-sign-time=1541591468;1541593268&q-key-time=1541591468;1541593268&q-header-list=&q-url-param-list=&q-signature=cc2de2fba4d67289dfe7ca7b5780334db75a6583&x-cos-security-token=ddfc8a545dc91147c517c5234380dbd571f78fd510001&response-content-disposition=attachment)</div>
<br>
首先，有一个一代的 generator，它能生成一些很差的图片，然后有一个一代的discriminator，它能准确的把生成的图片，和真实的图片分类，简而言之，这个 discriminator 就是一个二分类器，对生成的图片输出 0，对真实的图片输出 1。

接着，开始训练出二代的 generator，它能生成稍好一点的图片，能够让一代的 discriminator 认为这些生成的图片是真实的图片。然后会训练出一个二代的 discriminator，它能准确的识别出真实的图片，和二代 generator 生成的图片。以此类推，会有三代，四代。。。n 代的 generator 和 discriminator，最后 discriminator 无法分辨生成的图片和真实图片，这个网络就拟合了。

这就是 GAN，运行过程就是这么的简单。这就结束了嘛？显然没有，下面还要介绍一下 GAN 的原理。

---

# 2. GAN原理

首先我们知道真实图片集的分布 Pdata(x)，x 是一个真实图片，可以想象成一个向量，这个向量集合的分布就是 Pdata。我们需要生成一些也在这个分布内的图片，如果直接就是这个分布的话，怕是做不到的。

我们现在有的 generator 生成的分布可以假设为 PG(x;θ)，这是一个由 θ 控制的分布，θ 是这个分布的参数（如果是高斯混合模型，那么 θ 就是每个高斯分布的平均值和方差）

假设我们在真实分布中取出一些数据，{x1, x2, ... , xm}，我们想要计算一个似然 PG(xi; θ)。

对于这些数据，在生成模型中的似然就是<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/1.jpg?q-sign-algorithm=sha1&q-ak=AKIDNi0Bsg5QydkxlMUNY29ahl9kMFD4Hvw4&q-sign-time=1541591395;1541593195&q-key-time=1541591395;1541593195&q-header-list=&q-url-param-list=&q-signature=7c3f7d9afe8199d9f3ea49b73ee66a39efec7502&x-cos-security-token=81c78b985feb56ee872a2c00727e8db44e50b5b310001&response-content-disposition=attachment)</div>
我们想要最大化这个似然，等价于让 generator 生成那些真实图片的概率最大。这就变成了一个**最大似然估计的问题**了，我们需要找到一个 θ* 来最大化这个似然。<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/2.jpg?q-sign-algorithm=sha1&q-ak=AKIDWeZstr7lvkbmecS2GkW4CnttRZNNYCR8&q-sign-time=1541591495;1541593295&q-key-time=1541591495;1541593295&q-header-list=&q-url-param-list=&q-signature=d328b8438250f4d310b24d0b18fa662260bcb59a&x-cos-security-token=3a7970557101263e1089a6d69f0b90b2b342f4bb10001&response-content-disposition=attachment)</div>

寻找一个 θ* 来最大化这个似然，等价于最大化 log 似然。因为此时这 m 个数据，是从真实分布中取的，所以也就约等于，真实分布中的所有 x 在 PG 分布中的 log 似然的期望。

真实分布中的所有 x 的期望，等价于求概率积分，所以可以转化成积分运算，因为减号后面的项和 θ 无关，所以添上之后还是等价的。然后提出共有的项，括号内的反转，max 变 min，就可以转化为 **KL divergence** 的形式了，**KL divergence** 描述的是两个概率分布之间的差异。

所以最大化似然，让 generator 最大概率的生成真实图片，也就是要找一个 θ 让 PG 更接近于 Pdata。

那如何来找这个最合理的 θ 呢？我们可以假设 PG(x; θ) 是一个神经网络。

首先随机一个向量 z，通过 G(z)=x 这个网络，生成图片 x，那么我们如何比较两个分布是否相似呢？只要我们取一组 sample z，这组 z 符合一个分布，那么通过网络就可以生成另一个分布 PG，然后来比较与真实分布 Pdata。

大家都知道，神经网络只要有非线性激活函数，就可以去拟合任意的函数，那么分布也是一样，所以可以用一直正态分布，或者高斯分布，取样去训练一个神经网络，学习到一个很复杂的分布。<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/3.jpg?q-sign-algorithm=sha1&q-ak=AKID8APajjKNuWJS9aCQkHmcFWEorQ8jD7D9&q-sign-time=1541591517;1541593317&q-key-time=1541591517;1541593317&q-header-list=&q-url-param-list=&q-signature=3ec4d14a65862cb65d2163e16460aacd0960a97f&x-cos-security-token=20aa5a08ea0f50eeadae91edc671c12679d7e15510001&response-content-disposition=attachment)</div>

如何来找到更接近的分布，这就是 GAN 的贡献了。先给出 GAN 的公式：<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/4.jpg?q-sign-algorithm=sha1&q-ak=AKIDoWe3VUDyqycm7RyqrjwZCnGSzHELr0vj&q-sign-time=1541591545;1541593345&q-key-time=1541591545;1541593345&q-header-list=&q-url-param-list=&q-signature=8113178a4281f8a55730f210a51291f721d73046&x-cos-security-token=b40666353c57b5adc316fa0fc9a9f9e919ed848710001&response-content-disposition=attachment)</div>

这个式子的好处在于，固定 G，max  V(G,D) 就表示 PG 和 Pdata 之间的差异，然后要找一个最好的 G，让这个最大值最小，也就是两个分布之间的差异最小。<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/5.jpg?q-sign-algorithm=sha1&q-ak=AKIDYUaGmc2uEkkzGUzxE063Yoat7IH9JZX9&q-sign-time=1541591570;1541593370&q-key-time=1541591570;1541593370&q-header-list=&q-url-param-list=&q-signature=127c16952bf66ab22a96622195bdc89003fa130f&x-cos-security-token=5f25638471592a9a75beec512e21faf7269a708d10001&response-content-disposition=attachment)</div>

表面上看这个的意思是，D 要让这个式子尽可能的大，也就是对于 x 是真实分布中，D(x) 要接近与 1，对于 x 来自于生成的分布，D(x) 要接近于 0，然后 G 要让式子尽可能的小，让来自于生成分布中的 x，D(x) 尽可能的接近 1。

现在我们先固定 G，来求解最优的 D：<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/6.jpg?q-sign-algorithm=sha1&q-ak=AKIDWLzH7cLFlZ8Kyzjns5jZxbyJ1DMv4kId&q-sign-time=1541591600;1541593400&q-key-time=1541591600;1541593400&q-header-list=&q-url-param-list=&q-signature=919a1842c9519e4e9ec662362053bd4cde761aea&x-cos-security-token=728962d323caf679a36e836883a2163938de362a10001&response-content-disposition=attachment)</div>

对于一个给定的 x，得到最优的 D 如上图，范围在 (0,1) 内，把最优的 D 带入<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/7.jpg?q-sign-algorithm=sha1&q-ak=AKIDkJLBnezxSbp65d2BSn3JZmN9MIZoJJQI&q-sign-time=1541591621;1541593421&q-key-time=1541591621;1541593421&q-header-list=&q-url-param-list=&q-signature=f8cff0fa270195317e8c186fea0157efa03b1dd3&x-cos-security-token=75acab77f5437891fb7ecb5cf6cc921da463b1ae10001&response-content-disposition=attachment)</div>

可以得到：<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/8.jpg?q-sign-algorithm=sha1&q-ak=AKIDE80S6rrnbdP60A9i6p9jj3ONIC5YyeZu&q-sign-time=1541591651;1541593451&q-key-time=1541591651;1541593451&q-header-list=&q-url-param-list=&q-signature=41439a45d80e2b6e9dfb1bf598e32c9104393495&x-cos-security-token=4ee1800772b8da834ddcaad119a016603563d41110001&response-content-disposition=attachment)</div><div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/8-1.jpg?q-sign-algorithm=sha1&q-ak=AKIDbEdE7Zh7Esxyy1b8csaA55VfFZmd4gPR&q-sign-time=1541592169;1541593969&q-key-time=1541592169;1541593969&q-header-list=&q-url-param-list=&q-signature=2a50d3908cf3eb79f525f0f081223ca16cc3e7eb&x-cos-security-token=874c459cbef33b6610b15a96ae927ffae886c8d910001&response-content-disposition=attachment)</div>

JS divergence 是 KL divergence 的对称平滑版本，表示了两个分布之间的差异，这个推导就表明了上面所说的，固定 G。<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/7.jpg?q-sign-algorithm=sha1&q-ak=AKIDkJLBnezxSbp65d2BSn3JZmN9MIZoJJQI&q-sign-time=1541591621;1541593421&q-key-time=1541591621;1541593421&q-header-list=&q-url-param-list=&q-signature=f8cff0fa270195317e8c186fea0157efa03b1dd3&x-cos-security-token=75acab77f5437891fb7ecb5cf6cc921da463b1ae10001&response-content-disposition=attachment)</div>

表示两个分布之间的差异，最小值是 -2log2，最大值为 0。

现在我们需要找个 G，来最小化<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/7.jpg?q-sign-algorithm=sha1&q-ak=AKIDkJLBnezxSbp65d2BSn3JZmN9MIZoJJQI&q-sign-time=1541591621;1541593421&q-key-time=1541591621;1541593421&q-header-list=&q-url-param-list=&q-signature=f8cff0fa270195317e8c186fea0157efa03b1dd3&x-cos-security-token=75acab77f5437891fb7ecb5cf6cc921da463b1ae10001&response-content-disposition=attachment)</div>

观察上式，当 PG(x)=Pdata(x) 时，G 是最优的。

---

# 3. 训练

有了上面推导的基础之后，我们就可以开始训练 GAN 了。结合我们开头说的，两个网络交替训练，我们可以在起初有一个 G0 和 D0，先训练 D0 找到 ：<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/9.jpg?q-sign-algorithm=sha1&q-ak=AKIDGIqYYaFOSTaxLBoT0wVPSFZEbK6rrZJs&q-sign-time=1541591672;1541593472&q-key-time=1541591672;1541593472&q-header-list=&q-url-param-list=&q-signature=7cf05362dfd80d5fd19e6b991a8462f141ebd7db&x-cos-security-token=13a5612f500464783827e7d395ddde686fea6fb910001&response-content-disposition=attachment)</div>

然后固定 D0 开始训练 G0， 训练的过程都可以使用 **gradient descent**，以此类推，训练 D1,G1,D2,G2,...

但是这里有个问题就是，你可能在 D0* 的位置取到了<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/10.jpg?q-sign-algorithm=sha1&q-ak=AKIDJCVLVCPzwlaPGM8StdZ8nadi3yz3sy4x&q-sign-time=1541591692;1541593492&q-key-time=1541591692;1541593492&q-header-list=&q-url-param-list=&q-signature=a9225d7bfd09820c49e2f8c858a50cfe41bfda6f&x-cos-security-token=b97dd0bfe8cfe72b509e534b773eb5ab1957e5f310001&response-content-disposition=attachment)</div>

然后更新 G0 为 G1，可能<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/11.jpg?q-sign-algorithm=sha1&q-ak=AKIDfEdAa5EucPp2kamKc6Ys2FNQIHeC9fRm&q-sign-time=1541591709;1541593509&q-key-time=1541591709;1541593509&q-header-list=&q-url-param-list=&q-signature=55309c3e8da3bf8a4499a5c829f00a81472a7f1c&x-cos-security-token=cb7d9b3d3dcc64fa9c2babd91aa7345edeb4309910001&response-content-disposition=attachment)</div>

但是并不保证会出现一个新的点 D1* 使得<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/12.jpg?q-sign-algorithm=sha1&q-ak=AKID3okL8rmuLowWC8QS7VIsqx14RiSsVAs5&q-sign-time=1541591727;1541593527&q-key-time=1541591727;1541593527&q-header-list=&q-url-param-list=&q-signature=29134c48a4a4c35a454097e82f0fe52957d4b161&x-cos-security-token=d3ad284e07a94dc51b257952efbd7de77d238b0210001&response-content-disposition=attachment)</div>

这样更新 G 就没达到它原来应该要的效果，如下图所示：<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/13.jpg?q-sign-algorithm=sha1&q-ak=AKIDDy2YAW1SccPKc8wPFbpp7ZA5cSxqV1cp&q-sign-time=1541591742;1541593542&q-key-time=1541591742;1541593542&q-header-list=&q-url-param-list=&q-signature=481aa6cfa37224d266eaab80bdca51fe262f4502&x-cos-security-token=54acc507a82604a6816d554bf06561536965dda210001&response-content-disposition=attachment)</div>

避免上述情况的方法就是更新 G 的时候，不要更新 G 太多。

知道了网络的训练顺序，我们还需要设定两个 loss function，一个是 D 的 loss，一个是 G 的 loss。下面是整个 GAN 的训练具体步骤：<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/14.jpg?q-sign-algorithm=sha1&q-ak=AKIDEqlpcYjXTj8izB3OsFg4TgAs6la3Lcr7&q-sign-time=1541591759;1541593559&q-key-time=1541591759;1541593559&q-header-list=&q-url-param-list=&q-signature=4618819c9895b5d38859183ba9d14472b4250944&x-cos-security-token=bbf74c76ad5119e1c93762bb11e5595c22f0e43c10001&response-content-disposition=attachment)</div>

上述步骤在机器学习和深度学习中也是非常常见，易于理解。

---

# 4. 存在的问题

但是上面 G 的 **loss function** 还是有一点小问题，下图是两个函数的图像：<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/15.jpg?q-sign-algorithm=sha1&q-ak=AKIDml8g67H6N2vV8WVtK9NgYcUpQh1YiFxW&q-sign-time=1541591940;1541593740&q-key-time=1541591940;1541593740&q-header-list=&q-url-param-list=&q-signature=7abf97829c9ec11526dd4fb4c2e34b5cac355a9a&x-cos-security-token=51e13b9e423947fe7ea45e313979f9c49369673e10001&response-content-disposition=attachment)</div>

log(1-D(x)) 是我们计算时 G 的 loss function，但是我们发现，在 D(x) 接近于 0 的时候，这个函数十分平滑，梯度非常的小。这就会导致，在训练的初期，G 想要骗过 D，变化十分的缓慢，而上面的函数，趋势和下面的是一样的，都是递减的。但是它的优势是在 D(x) 接近 0 的时候，梯度很大，有利于训练，在 D(x) 越来越大之后，梯度减小，这也很符合实际，在初期应该训练速度更快，到后期速度减慢。

所以我们把 G 的 loss function 修改为<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/16.jpg?q-sign-algorithm=sha1&q-ak=AKIDL1vRXMGF214YXXnWgbGCxwtATePB1OGs&q-sign-time=1541591918;1541593718&q-key-time=1541591918;1541593718&q-header-list=&q-url-param-list=&q-signature=767c97e63d0a6a72ccb0fcdea432fd60d47d3db6&x-cos-security-token=dcc01eb725a87df948d2cade4658bf92d80f473e10001&response-content-disposition=attachment)</div>

这样可以提高训练的速度。

还有一个问题，在其他 paper 中提出，就是经过实验发现，经过许多次训练，loss 一直都是平的，也就是<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/17.jpg?q-sign-algorithm=sha1&q-ak=AKIDLkOy5N24dxJQZPCY5FVRXCIStdOachPP&q-sign-time=1541591968;1541593768&q-key-time=1541591968;1541593768&q-header-list=&q-url-param-list=&q-signature=5df5e128bf9953d96c105e03f559a8745a709220&x-cos-security-token=191089cbdcd62a0d62a5f1ef92a03a50e40d4a1710001&response-content-disposition=attachment)</div>

JS divergence 一直都是 log2，PG 和 Pdata 完全没有交集，但是实际上两个分布是有交集的，造成这个的原因是因为，我们无法真正计算期望和积分，只能使用 sample 的方法，如果训练的过拟合了，D 还是能够完全把两部分的点分开，如下图：<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/18.jpg?q-sign-algorithm=sha1&q-ak=AKIDhHDqGGeTbNLhM4qq3t2Ipn4KKVVwmXLU&q-sign-time=1541591993;1541593793&q-key-time=1541591993;1541593793&q-header-list=&q-url-param-list=&q-signature=cf022121831240dddf3385e533f6640c77805618&x-cos-security-token=7ae0a2c729b0eb71f2c9a8e7015e58170aadd2a210001&response-content-disposition=attachment)</div>

对于这个问题，我们是否应该让 D 变得弱一点，减弱它的分类能力，但是从理论上讲，为了让它能够有效的区分真假图片，我们又希望它能够 powerful，所以这里就产生了矛盾。

还有可能的原因是，虽然两个分布都是高维的，但是两个分布都十分的窄，可能交集相当小，这样也会导致 **JS divergence** 算出来 =log2，约等于没有交集。

解决的一些方法，有添加噪声，让两个分布变得更宽，可能可以增大它们的交集，这样 JS divergence 就可以计算，但是随着时间变化，噪声需要逐渐变小。

还有一个问题叫 **Mode Collapse**，如下图：<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/19.jpg?q-sign-algorithm=sha1&q-ak=AKIDGwuroT10H75imvVIq9VmZ2c1yVinWQy3&q-sign-time=1541592013;1541593813&q-key-time=1541592013;1541593813&q-header-list=&q-url-param-list=&q-signature=80221ba3513178daf7fec817a0f292f0e3b4125f&x-cos-security-token=5e03cae7040c2a8bb24fede9f72ed0d9c08bc0d710001&response-content-disposition=attachment)</div>

这个图的意思是，data 的分布是一个双峰的，但是学习到的生成分布却只有单峰，我们可以看到模型学到的数据，但是却不知道它没有学到的分布。

造成这个情况的原因是，KL divergence 里的两个分布写反了<div align=center>![](https://xhjiang-1256231208.cos.ap-chengdu.myqcloud.com/hexo-GAN/image/20.jpg?q-sign-algorithm=sha1&q-ak=AKIDznzX4HpBlsNpwXDzZt088AiUaagX7MwT&q-sign-time=1541592029;1541593829&q-key-time=1541592029;1541593829&q-header-list=&q-url-param-list=&q-signature=2a8d20d8af2fc6153d329a541ea90c49791a387b&x-cos-security-token=692829b819dba91d595a5da3a88ab271030b35d910001&response-content-disposition=attachment)</div>

这个图很清楚的显示了，如果是第一个 KL divergence 的写法，为了防止出现无穷大，所以有 Pdata 出现的地方都必须要有 PG 覆盖，就不会出现 Mode Collapse。

---

# 5. 参考

这是对 GAN 入门学习做的一些笔记和理解，后来太懒了，不想打公式了，主要是参考了李宏毅老师的视频：https://www.bilibili.com/video/av24011528/?p=1