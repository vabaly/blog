# 使用 Node.js 在深度学习中做图片预处理

## 背景

最近在做一个和对象识别相关的项目，由于团队内技术栈偏向 `JavaScript`，在已经用 `Python` 和 `Tensorflow` 搭建好了对象识别服务器后，为了不再增加团队成员维护成本，所以尽可能将训练和识别之外的任务交给 `Node.js` 来做，今天要讲到的图片预处理就是其中之一。

> 这里对还不了解深度学习的人就几个概念做个简单的解释。
>
> 1. 对象识别：对象识别可理解为计算机在一张图片中发现某个或某些指定的物体，比如找到里面所有的狗。
> 2. 训练：计算机学会对象识别这个本领就像人类学会说话一样，需要不断地练习，深度学习中管这个过程叫做 “训练”。
> 3. 训练集：人类学会说话需要看别人怎么说，听别人的声音等等，这些能够让自己学会说话的信息在深度学习中称为训练集，只不过对象识别中需要的训练集只有图片。

做图片预处理的目的是为了解决对象识别中训练集不足的问题。当对象识别应用于某个专用领域的时候，就会遇到这个问题。如果你是识别一只狗，这样的图片一大把，而且有人已经训练好了，并且可以提供服务给大家使用了。如果你是识别团队内的文化衫，这样的图片就太少了，费了老半天劲拍 100 张，这样的数据量依然少得可怜。要知道网上那些成熟的 AI 服务，训练集随随便便就成千上万，甚至以亿为单位。当然，专用领域一般需求也比较简单，需要识别出来的东西种类不多，特征也比较明显，但是仍然会希望训练集越大越好，这时候就可以对所拥有的图片做一些处理，来生成新的图片，从而扩充当前的训练集，这个过程就叫图片预处理了。

常见的图片预处理方式有以下几种：

1. **旋转**。由于旋转的角度可以是任意值，所以需要随机生成一些角度来旋转，这又称为随机旋转。
1. **翻转**。相当于在图片旁边放面镜子，新图片就是镜子内的图片，一般有水平翻转和竖直翻转两种。
1. **调节亮度**。调节过手机的亮度就能体会这个意思。
1. **调节饱和度**。调节过传统电视就能体会到这个意思，饱和度越高，色彩显示越鲜艳，反之给人一种冷色的感觉。
1. **调节色相**。这个相当于给整个图片变颜色一样，想象一下以前调出来的绿色电视。
1. **调节对比度**。这个会让图片亮的地方更亮，暗的地方更暗。也可以想象一下电视上的对比度调节，不得不说电视机启蒙了这些专业名词。

上述每项操作都需要视场景而选择，目前适用于我们团队的处理方式主要也就是上面这些。还有一些白化、Gamma 处理等操作，由于不是那么直观，有兴趣的人可以自己去了解。

## 安装 `gm`

[ gm ](https://github.com/aheckmann/gm) 是一个图片处理的 `npm` 库，性能在 `Node.js` 库中应该算佼佼者了，它底层默认使用的是 `GraphicsMagick`，所以你需要先安装 `GraphicsMagick`，在 Mac 系统中直接用 `Homebrew` 安装：

```sh
brew install graphicsmagick
```

其他系统的安装方式可以直接[前往官网](http://www.graphicsmagick.org/)查看。

> 如果你需要在图片上添加文字，还需要安装 `ghostscript`，在 Mac 上可以用 `brew install ghostscript` 安装。由于本文没涉及到这一个功能，所以可以不用安装。

同时，需要将 `gm` 安装在你的项目下：

```sh
npm i gm -S
```

## 预处理

为了直观，我选了一张图片作为预处理对象：

![示例图片](https://raw.githubusercontent.com/vabaly/picture/master/demo-public.png)

另外，在本文的示例代码中，每种预处理方法的函数名都是参照 `Tensorflow` 中 `Image` 模块的同名方法而定，更多处理图片的方法可以[前往 Tensorflow 文档官网](https://www.tensorflow.org/api_docs/python/tf/image)自行查看，同时去[ gm 官方文档 ](http://aheckmann.github.io/gm/docs.html) 中寻找相同作用的方法。

### 翻转

沿 Y 轴翻转用到了 `gm` 的 `.flip` 方法：

```js
import gm from 'gm';

/**
 * 沿 Y 轴翻转，即上下颠倒
 * @param inputPath 输入的图像文件路径
 * @param outputPath 输出的图像文件路径
 * @param callback 处理后的回调函数
 */
function flip(inputPath, outputPath, callback) {
    gm(inputPath)
        .flip()
        .write(outputPath, callback);
}
```

翻转后的效果如下图所示：

![Flip](https://raw.githubusercontent.com/vabaly/picture/master/demo-public-flip.png)

沿 X 轴翻转用到了 `gm` 的 `.flop` 方法：

```js
import gm from 'gm';

/**
 * 沿 X 轴翻转，即上下颠倒
 * @param inputPath 输入的图像文件路径
 * @param outputPath 输出的图像文件路径
 * @param callback 处理后的回调函数
 */
function flop(inputPath, outputPath, callback) {
    gm(inputPath)
        .flop()
        .write(outputPath, callback);
}
```

翻转后的效果如下图所示：

![Flip](https://raw.githubusercontent.com/vabaly/picture/master/demo-public-flop.png)

你还可以把 `.flip` 和 `.flop` 组合起来使用，形成对角线翻转的效果：

![Flip and flop](https://raw.githubusercontent.com/vabaly/picture/master/demo-public-flip-flop.png)

**如果把原图看成一个前端组件，即一个购物按钮组，里面每个按钮的背景可以自定义，按钮里面由文字、分隔线、文字三种元素组成，那么上面翻转后的图片是可以看成同一个组件的，即可以拿来作为训练集。**

有时候，翻转带来的效果并不是自己想要的，可能翻转后，和原来的图片就不应该视作同一个东西了，这时候这种方法就有局限性了。

### 调整亮度

相比之后，调整亮度就显得更加普适了，无论是什么图片，调整亮度后，里面的东西依然还是原来的那个东西。

调整亮度用到了 `gm` 的 `.modulate` 方法：

```js
/**
 * 调整亮度
 * @param inputPath 输入的图像文件路径
 * @param outputPath 输出的图像文件路径
 * @param brightness 图像亮度的值，基准值是 100，比 100 高则是增加亮度，比 100 低则是减少亮度
 * @param callback 处理后的回调函数
 */
function adjustBrightness(inputPath, outputPath, brightness, callback) {
    gm(inputPath)
        .modulate(brightness, 100, 100)
        .write(outputPath, callback);
}
```

`.modulate` 方法是一个多功能的方法，可以同时调整图片的亮度、饱和度和色相三种特性，这三种特性分别对应着该方法的三个参数，这里只调整亮度，所以只改变第一个参数（比 100 高则是增加亮度，比 100 低则是减少亮度），其他保持 100 基准值不变。

我把亮度从 0 - 200 的图片都生成了出来，并进行了对比，选出了一个亮度处理较为合适的区间。可以看看 0 - 200 之间相邻亮度相差为 10 的图片之间的差别（*提示：每张图片的左上角标识出了该图片的亮度*）：

![亮度](https://raw.githubusercontent.com/vabaly/picture/master/imagesbrightness.png)

可以看到亮度为 60 以下的图片，都太暗了，细节不够明显，亮度为 150 以上的图片，都太亮了，也是细节不够明显。而经过多张图片综合对比之后，我认为 [60, 140] 这个区间的图片质量比较好，与原图相比不会丢失太多细节。

再来看看亮度为 50 和 60 的两张图片，其实看起来像是一张图片一样，不符合训练集多样性的原则，更何况是相邻亮度相差为 1 的两张图片。所以最终决定作为训练集的相邻两张图片亮度差为 20，这样差异就比较明显，比如亮度为 80 和亮度为 100 的两张图片。

最终，调节亮度产生的新图片将会是 4 张。**从亮度为 60 的图片开始，每增加 20 亮度就选出来加入训练集，直到亮度为 140 的图片，其中亮度为 100 的图片不算。**

### 调节饱和度

调节饱和度也是用 `.modulate` 方法，只不过是调节第二个参数：

```js
/**
 * 调整饱和度
 * @param inputPath 输入的图像文件路径
 * @param outputPath 输出的图像文件路径
 * @param saturation 图像饱和度的值，基准值是 100，比 100 高则是增加饱和度，比 100 低则是减少饱和度
 * @param callback 处理后的回调函数
 */
function adjustSaturation(inputPath, outputPath, saturation, callback) {
    gm(inputPath)
        .modulate(100, saturation, 100)
        .write(outputPath, callback);
}
```

同样按调节亮度的方法来确定饱和度的范围以及训练集中相邻两张图片的饱和度相差多少。可以看看相邻饱和度相差为 10 的图片之间的差别（*提示：每张图片的左上角标识出了该图片的饱和度*）：

![饱和度](https://raw.githubusercontent.com/vabaly/picture/master/imagessaturation.png)

调节饱和度的产生的图片细节没有丢，大多都能够用作训练集中的图片，与亮度一样，饱和度相差 20 的两张图片差异性明显。另外，饱和度大于 140 的时候，图片改变就不明显了。**所以调节饱和度产生的新图片将会是 6 张。从饱和度为 0 的图片开始，每增加 20 饱和度就选出来加入训练集，直到饱和度为 140 的图片，其中饱和度为 100 的图片不算。**

### 调节色相

调节色相的方法在此场景下是最有用的方法，产生的训练集最多，率先来看下色相相邻为 10 的图片之间的差距吧（*提示：每张图片的左上角标识出了该图片的色相*）：

![色相](https://raw.githubusercontent.com/vabaly/picture/master/hue.png)

几乎每个图片都能作为新的训练集，由于色相调节范围只能在 0 - 200 之间，**所以从色相为 0 的图片开始，每增加 10 色相就选出来加入训练集，直到色相为 190 的图片，其中色相为 100 的图片不算。** 这样就能够产生 20 张图片作为训练集。

至于调节色相的代码则和亮度、饱和度一样，只是改变了第三个参数：

```js
/**
 * 调整色相
 * @param inputPath 输入的图像文件路径
 * @param outputPath 输出的图像文件路径
 * @param hue 图像色相的值，基准值是 100，比 100 高则是增加色相，比 100 低则是减少色相
 * @param callback 处理后的回调函数
 */
function adjustHue(inputPath, outputPath, hue, callback) {
    gm(inputPath)
        .modulate(100, 100, hue)
        .write(outputPath, callback);
}
```

调节色相并不是万能的，只是适用于这个场景，当然，我们团队的需求都是类似这个场景的。但是，如果你要训练识别梨的人工智能，告诉它有个蓝色的梨显然是不合适的。

### 调节对比度

调整对比度用到了 `gm` 的 `.contrast` 方法：

```js
/**
 * 调整对比度
 * @param inputPath 输入的图像文件路径
 * @param outputPath 输出的图像文件路径
 * @param multiplier 调节对比度的因子，默认是 0，可以为负值，n 表示增加 n 次对比度，-n 表示降低 n 次对比度
 * @param callback 处理后的回调函数
 */
function adjustContrast(inputPath, outputPath, multiplier, callback) {
    gm(inputPath)
        .contrast(multiplier)
        .write(outputPath, callback);
}
```

下面是对比度因子从 -10 到 10 之间的图像，可以看到图片质量较好的区间是 [-5, 2]，其他都会丢失一些细节。另外相邻对比度因子的图片之间的差异也比较明显，所以每张图片都可作为训练集，这样又多出 7 张图片。

![对比度](https://raw.githubusercontent.com/vabaly/picture/master/contrast.png)

## 总结

通过上述 5 种方法，可以在一张图片的基础上额外获得 40 张图片，即训练集是原来的 40 倍。这还是在没有多种方法混合使用的情况下，如果混合使用，恐怕几百倍都不止。

`gm` 还支持对图片进行其他处理方式，你可以自己去发掘，每种方式在特定场景下都有自己的局限性，需要你去甄选。希望大家都有一个自己满意的训练集。

## 宣传

欢迎大家 [Star 笔者的 Github](https://github.com/vabaly/blog)，另外，也欢迎大家关注笔者的公众号，获取最新文章的推送：

![微信公众号二维码](https://raw.githubusercontent.com/vabaly/picture/master/imageswechat-qrcode.jpg)
