**3、利用符号执行检测Excel恶意宏** 

![img](https://ask.qcloudimg.com/http-save/yehe-1878705/137cf4fcf640cc90cbb071b51158ae33.png?imageView2/2/w/1620)

议题名：Symbexcel: Bringing the　Power of Symbolic Execution　to the Fight Against Malicious Excel 4 Macros

现在搞恶意软件的不加点混淆都显得不专业，但这对于反病毒人士分析恶意软件时就有点费劲了，而且混淆技术层出不穷，变个花样，你就得分析半天，是个体力活。为了减少分析成本，更加自动化地提取IOCs检测特征，作者应用了符号执行技术，再结合宏语法缩小求解范围，提高求解速度。 

里面的符号执行示例，很好地解释了符号执行的基本原理和应用，对这块感兴趣的可以看下。

![img](https://ask.qcloudimg.com/http-save/yehe-1878705/91abac1b125d4609a65534162e8f4261.png?imageView2/2/w/1620)

看他们最后解混淆出来的样本分析效果对比看，还不错的样子，很多样本在VirusTotal上的检测率比较低。

![img](https://ask.qcloudimg.com/http-save/yehe-1878705/c757ea5c18019f842abe9cf6ba9f5463.png?imageView2/2/w/1620)