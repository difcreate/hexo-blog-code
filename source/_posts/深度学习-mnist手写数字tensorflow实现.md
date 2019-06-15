---
title: 深度学习-mnist手写数字tensorflow实现
date: 2018-11-14 11:01:58
tags: [深度学习,Tensorflow,Python]
categories: [深度学习,Tensorflow,Python]
---

# 深度学习实战（一）：手写数字识别

---

# 1. 前言
为了更好的理解Neural Network，本文使用Tensorflow实现一个最简单的神经网络，然后使用MNIST数据集进行测试。同时使用Tensorboard对训练过程进行可视化。

---

# 2. MNIST数据集简介
当我们学习新的编程语言时，通常第一个程序就是打印输出著名的“Hello World!”。在深度学习中，MNIST数据集就相当于Hello World。

**数据集：**
train-images-idx3-ubyte 训练数据图像 (60,000)
train-labels-idx1-ubyte 训练数据label
t10k-images-idx3-ubyte 测试数据图像 (10,000)
t10k-labels-idx1-ubyte 测试数据label

>我们的任务是使用上面数据训练一个可以准确识别手写数字的神经网络模型，并使用Tensorflow对训练过程各个参数的变化进行可视化。

---

# 3. Tensorboard简介
本文要使用到Tensorboard，先让我们看看它究竟是用来干什么的。

当使用Tensorflow训练大量深层的神经网络时，我们希望去跟踪神经网络的整个训练过程中的信息，比如迭代的过程中每一层参数是如何变化与分布的，比如每次循环参数更新后模型在测试集与训练集上的准确率是如何的，比如损失值的变化情况，等等。如果能在训练的过程中将一些信息加以记录并可视化得表现出来，是不是对我们探索模型有更深的帮助与理解呢？

Tensorflow官方推出了可视化工具Tensorboard，可以帮助我们实现以上功能，它可以将模型训练过程中的各种数据汇总起来存在自定义的路径与日志文件中，然后在指定的web端可视化地展现这些信息。

## 3.1 Tensorboard的数据形式：
Tensorboard可以记录与展示以下数据形式：

（1）标量Scalars

（2）图片Images

（3）音频Audio

（4）计算图Graph

（5）数据分布Distribution

（6）直方图Histograms

（7）嵌入向量Embeddings

## 3.2 Tensorboard的可视化过程：
（1）首先肯定是先建立一个graph,你想从这个graph中获取某些数据的信息

（2）确定要在graph中的哪些节点放置summary operations以记录信息

使用tf.summary.scalar记录标量

使用tf.summary.histogram记录数据的直方图

使用tf.summary.distribution记录数据的分布图

使用tf.summary.image记录图像数据

.....等等

（3）operations并不会去真的执行计算，除非你告诉他们需要去run,或者它被其他的需要run的operation所依赖。而我们上一步创建的这些summary operations其实并不被其他节点依赖，因此，我们需要特地去运行所有的summary节点。但是呢，一份程序下来可能有超多这样的summary 节点，要手动一个一个去启动自然是极其繁琐的，因此我们可以使用tf.summary.merge_all去将所有summary节点合并成一个节点，只要运行这个节点，就能产生所有我们之前设置的summary data。

（4）使用tf.summary.FileWriter将运行后输出的数据都保存到本地磁盘中

（5）运行整个程序，并在命令行输入运行tensorboard的指令，之后打开web端可查看可视化的结果

考虑多类情况。非onehot，标签是类似0 1 2 3...n这样。而onehot标签则是顾名思义，一个长度为n的数组，只有一个元素是1.0，其他元素是0.0。例如在n为4的情况下，标签2对应的onehot标签就是 0.0 0.0 1.0 0.0使用onehot的直接原因是现在多分类cnn网络的输出通常是softmax层，而它的输出是一个概率分布，从而要求输入的标签也以概率分布的形式出现，进而算交叉熵之类。

---

# 4. 手写数字识别
现在，我们使用最基础的手写数字识别。

## 4.1 准备数据集、定义超参数等准备工作
1）首先是导入需要使用的包：

```python
# 导入使用的包
import tensorflow as tf
from tensorflow.examples.tutorials.mnist import input_data
import os
```

2）定义超参数

如果你问，这个超参数为啥要这样设定，如何选择最优的超参数？这个问题此处先不讨论，超参数的选择在机器学习建模中最常用的方法就是“交叉验证法”。而现在假设我们已经获得了最优的超参数，设置学利率为0.001，dropout的保留节点比例为0.9，最大循环次数为1000。

另外，还要设置两个路径，第一个是数据下载下来存放的地方，一个是summary输出保存的地方。

```python
# 定义超参数
max_steps = 1000  # 最大迭代数
learning_rate = 0.001  # 学习率
dropout = 0.9  # dropout时随机保留神经元的比例
data_dir = './MNIST_DATA'  # 样本数据存储的路径
log_dir = './MNIST_LOG'    # 输出日志保存的路径
```

3）GPU设置

这里使用GPU进行训练，如果使用cpu，可以略过此步。如果使用GPU建议进行设置。

```python
# GPU设置
os.environ["CUDA_VISIBLE_DEVICES"] = "0"
config = tf.ConfigProto(allow_soft_placement=True)
gpu_options = tf.GPUOptions(per_process_gpu_memory_fraction=0.99)
config.gpu_options.allow_growth = True
```

>*Tips：*
>上述代码的意思是使用GPU设备0，最多给GPU分配总共内存的百分之33，并且允许GPU按需申请内存。也就是说，假设一个程序使用一块GPU内存百分之10就够了，如果我们没有指定allow_growth=True，那么程序会直接占用GPU内存的百分之33，因为这个是我们给它分配的。如果我们连0.33，也就是GPU内存的百分之33都没有指定，那么程序会直接占用整个GPU设备0。虽然占用这么多没有用，但是我就占着，属于“占着茅坑不拉屎”。所以，为了充分利用资源，特别是一帮人使用一个服务器的时候，指定下这些参数就很有必要了。

4）下载数据下载数据是直接调用了tensorflow提供的函数**read_data_sets**，输入两个参数，第一个是下载到数据存储的路径，第二个one_hot表示是否要将类别标签进行独热编码。它首先回去找制定目录下有没有这个数据文件，没有的话才去下载，有的话就直接读取。所以第一次执行这个命令，速度会比较慢，因为没有数据集，需要进行下载。

```python
# 获取数据集，并采用one_hot独热编码
mnist = input_data.read_data_sets(data_dir, one_hot=True)
```

## 4.2 数据处理
1）创建tensorflow默认会话：
```python
# 创建tensorflow默认会话
sess = tf.InteractiveSession(config=config)
```

>*Tips:*
>为了使设置的GPU参数生效，我们需要在创建会话的时候传入这个config参数。

2）创建输入数据的占位符，分别创建特征数据x，标签数据y_

```python
# 创建输入数据的占位符，分别创建特征数据x，标签数据y_
with tf.name_scope('input'):
    x = tf.placeholder(tf.float32, [None, 784], name='x-input')
    y_ = tf.placeholder(tf.float32, [None, 10], name='y-input')
```
>*Tips：*
>在**tf.placeholder()**函数中传入了3个参数，第一个是定义数据类型为float32；第二个是数据的大小，特征数据是大小784的向量，标签数据是大小为10的向量，None表示不定死大小，到时候可以传入任何数量的样本；第3个参数是这个占位符的名称
>**mnist下载好的数据集就是很多个1*784的向量，就是已经对28*28的图片进行了向量化处理。**

3）使用tf.summary.image保存图像信息

前面也说了，特征数据其实就是图像的像素数据拉升成一个1*784的向量，现在如果想在tensorboard上还原出输入的特征数据对应的图片，就需要将拉升的向量转变成28 * 28 * 1的原始像素了，于是可以用**tf.reshape()**直接重新调整特征数据的维度：

将输入的数据转换成[28 * 28 * 1]的shape，存储成另一个tensor，命名为image_shaped_input。
为了能使图片在tensorbord上展示出来，使用**tf.summary.image**将图片数据汇总给tensorbord。
**tf.summary.image（）**中传入的第一个参数是命名，第二个是图片数据，第三个是最多展示的张数，此处为10张。

```python
# 使用tf.summary.image保存图像信息
with tf.name_scope('input_reshape'):
    image_shaped_input = tf.reshape(x, [-1, 28, 28, 1])
    tf.summary.image('input', image_shaped_input, 10)
```

## 4.3 初始化参数并保存参数信息到summary
1）初始化参数w和b

在构建神经网络模型中，每一层中都需要去初始化参数w,b,为了使代码简介美观，最好将初始化参数的过程封装成方法function。 创建初始化权重w的方法，生成大小等于传入的shape参数，标准差为0.1，遵循正态分布的随机数，并且将它转换成tensorflow中的variable返回。

```python
# 初始化权重参数
def weight_variable(shape):
    initial = tf.truncated_normal(shape, stddev=0.1)
    return tf.Variable(initial)
```

创建初始换偏执项b的方法，生成大小为传入参数shape的常数0.1，并将其转换成tensorflow的variable并返回。

```python
# 初始化偏执参数
def bias_variable(shape):
    initial = tf.constant(0.1, shape=shape)
    return tf.Variable(initial)
```

2）记录训练过程参数变化

我们知道，在训练的过程在参数是不断地在改变和优化的，我们往往想知道每次迭代后参数都做了哪些变化，可以将参数的信息展现在tenorbord上，因此我们专门写一个方法来收录每次的参数信息。

```python
# 记录训练过程参数变化
def variable_summaries(var):
    with tf.name_scope('summaries'):
        # 计算参数的均值，并使用tf.summary.scalar记录
        mean = tf.reduce_mean(var)
        tf.summary.scalar('mean', mean)

        # 计算参数的标准差
        with tf.name_scope('stddev'):
            stddev = tf.sqrt(tf.reduce_mean(var - mean))

        # 使用tf.summat.scalar记录标准差，最大值，最小值
        tf.summary.scalar('stddev', stddev)
        tf.summary.scalar('max', tf.reduce_max(var))
        tf.summary.scalar('min', tf.reduce_min(var))

        # 用直方图记录参数的分布
        tf.summary.histogram('histogram', var)
```

## 4.4 构建神经网络层
1）创建第一层隐藏层

创建一个构建隐藏层的方法,输入的参数有：

>input_tensor：特征数据
>input_dim：输入数据的维度大小
>output_dim：输出数据的维度大小(=隐层神经元个数）
>layer_name：命名空间
>act=tf.nn.relu：激活函数（默认是relu)

```python
# 构建神经网络
def nn_layer(input_tensor, input_dim, out_dim, layer_name, act=tf.nn.relu):
    # 设置命名空间
    with tf.name_scope(layer_name):
        # 调用之前的方法初始化权重w，并且调用参数信息的记录方法，记录w的信息
        with tf.name_scope('weights'):
            weights = weight_variable([input_dim, out_dim])
            variable_summaries(weights)
        # 调用之前的方法初始化权重b，并且调用参数信息的记录方法，记录b的信息
        with tf.name_scope('biases'):
            biases = bias_variable([out_dim])
            variable_summaries(biases)
        # 执行wx+b的线性计算，并且用直方图记录下来
        with tf.name_scope('linear_compute'):
            preactivate = tf.matmul(input_tensor, weights) + biases
            tf.summary.histogram('linear', preactivate)
        # 将线性输出经过激励函数，并将输出也用直方图记录下来
        activations = act(preactivate, name='activations')
        tf.summary.histogram('activations', activations)
    # 返回激励层的最终输出
    return activations
```

调用隐层创建函数创建一个隐藏层：输入的维度是特征的维度784，隐藏层的神经元个数是500，也就是输出的维度。

```python
# 创建一个隐藏层
hidden1 = nn_layer(x, 784, 500, 'layer1')
```

2）创建一个dropout层

随机关闭掉hidden1的一些神经元，并记录keep_prob，减少保存参数，防止过拟合。

```python
# 创建一个dropout层
with tf.name_scope('dropout'):
    keep_prob = tf.placeholder(tf.float32)
    tf.summary.scalar('dropout_keep_probability', keep_prob)
    dropped = tf.nn.dropout(hidden1, keep_prob)
```

3）创建一个输出层

输入的维度是上一层的输出:500,输出的维度是分类的类别种类：10，激活函数设置为全等映射identity。（暂且先别使用softmax，会放在之后的损失函数中一起计算）

```python
# 创建一个输出层
y = nn_layer(dropped, 500, 10, 'layer2', act=tf.identity)
```

## 4.5 创造损失函数
使用tf.nn.softmax_cross_entropy_with_logits来计算softmax并计算交叉熵损失,并且求均值作为最终的损失值。

```python
# 创建损失函数
with tf.name_scope('loss'):
    # 计算交叉熵损失（每个样本都会有一个损失）
    diff = tf.nn.softmax_cross_entropy_with_logits(labels=y_, logits=y)
    with tf.name_scope('total'):
        # 计算所有样本交叉熵损失的均值
        cross_entropy = tf.reduce_mean(diff)
    tf.summary.scalar('loss', cross_entropy)
```

## 4.6 训练
首先，使用AdamOptimizer优化器训练模型，最小化交叉熵损失。

```python
# 使用AdamOptimizer优化器训练模型，最小化交叉熵损失
with tf.name_scope('train'):
    train_step = tf.train.AdamOptimizer(learning_rate).minimize(cross_entropy)
```

然后，计算准确率,并用tf.summary.scalar记录准确率。

```python
# 计算准确率
with tf.name_scope('accuracy'):
    with tf.name_scope('correct_prediction'):
        # 分别将预测和真实的标签中取出最大值的索引，若相同则返回1(true),不同则返回0(false)
        correct_prediction = tf.equal(tf.argmax(y, 1), tf.argmax(y_, 1))
        with tf.name_scope('accuracy'):
            # 求均值即为准确率
            accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))

tf.summary.scalar('accuracy', accuracy)
```

## 4.7 所有变量初始化
将所有的summaries合并，并且将它们写到之前定义的log_dir路径。

```python
# summaries合并
merged = tf.summary.merge_all()

# 写到指定的磁盘路径中
train_writer = tf.summary.FileWriter(log_dir + '/train', sess.graph)
test_writer = tf.summary.FileWriter(log_dir + '/test')

# 运行初始化所有变量
tf.global_variables_initializer().run()
```

## 4.8 送入数据集
feed_dict用于获取数据，如果是**train == true**，也就是进行训练的时候，就从mnist.train中获取一个batch大小为100样本，并且设置dropout值为0.9。如果是**train == false**,则获取minist.test的测试数据，并且设置dropout为1，即保留所有神经元开启。

同时，每隔10步，进行一次测试，并打印一次测试数据集的准确率，然后将测试数据集的各种summary信息写进日志中。 其余的时候，都是在进行训练，将训练集的summary信息并写到日志中。

```python
def feed_dict(train):
    if train:
        xs, ys = mnist.train.next_batch(100)
        k = dropout
    else:
        xs, ys = mnist.test.images, mnist.test.labels
        k = 1.0
    return {x: xs, y_: ys, keep_prob: k}


for i in range(max_steps):
    if i % 10 == 0:  # 记录测试集的summary与accuracy
        summary, acc = sess.run([merged, accuracy], feed_dict=feed_dict(False))
        test_writer.add_summary(summary, i)
        print('Accuracy at step %s: %s' % (i, acc))
    else:  # 记录训练集的summary
        summary, _ = sess.run([merged, train_step], feed_dict=feed_dict(True))
        train_writer.add_summary(summary, i)

train_writer.close()
test_writer.close()
```

## 4.9 运行程序
运行整个程序，在程序中定义的summary node就会将要记录的信息全部保存在指定的logdir路径中了，训练的记录会存一份文件，测试的记录会存一份文件。

运行程序，如果使用GPU进行训练，等待几分钟应该就OK了

## 4.10 利用Tensorboard查看训练状态

与此同时，在运行的时候，我们就可以打开Tensorboard查看训练状态。使用如下指令：

```python
tensorboard --logdir=./MNIST_LOG --port=8008
```