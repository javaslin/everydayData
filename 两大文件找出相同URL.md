## 每天一道数据开发面试题

### 两大文件找出相同URL

#### 题目描述

**给定a、b两个文件，各存放50亿个url，每个url各占64字节，内存限制是4G，让你找出a、b文件共同的url？**

 可以估计每个文件安的大小为5G×64=320G，远远大于内存限制的4G。所以不可能将其完全加载到内存中处理。考虑采取分而治之的方法。

1. 分而治之/hash映射：遍历文件a，对每个url求取![img](https://user-gold-cdn.xitu.io/2017/12/6/1602b7316f6d9786?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)，然后根据所取得的值将url分别存储到1000个小文件（记为![img](https://user-gold-cdn.xitu.io/2017/12/6/1602b7316eca1653?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)， 这里漏写个了a1）中。这样每个小文件的大约为300M。遍历文件b，采取和a相同的方式将url分别存储到1000小文件中（记为![img](https://user-gold-cdn.xitu.io/2017/12/6/1602b7316c34b938?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)）。这样处理后，所有可能相同的url都在对应的小文件（![img](https://user-gold-cdn.xitu.io/2017/12/6/1602b731e807f559?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)）中，不对应的小文件不可能有相同的url。然后我们只要求出1000对小文件中相同的url即可。
2. hash_set统计：求每对小文件中相同的url时，可以把其中一个小文件的url存储到hash_set中。然后遍历另一个小文件的每个url，看其是否在刚才构建的hash_set中，如果是，那么就是共同的url，存到文件里面就可以了。