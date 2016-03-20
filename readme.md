# python statsmodels中文翻译。

## 进度

### 已完成

* ``index.rst`` 首页
* ``gettingstarted.rst`` 最小例子
* ``regression.rst`` 回归模型对象
* ``example_formulas.rst`` 使用R风格公式表示模型
* ``endog_exog.rst`` endog,exog(因变量，自变量)来源
* ``about.rst`` statsmodels模块历史

#statsmodels 注释

## summary()

``summary()`` 返回的是对象，不过一般利用 ``__str__`` 直接print。但也有其他输出格式。

summary报表中的``Omnibus`` 项是 Omnibus正态性检验（检验残差正态性）的统计量值。

``Prob(Omnibus)`` 是符合正态性假设的话出现更极端值的概率（显著性）。当然这个P值越小越偏离正态性，模型越糟糕。

``Skew``（偏度），``Kurtosis``（峰度）是形成Omnibus统计量的参数。当然它们自己也反应了正态性质。因为如果
正态性假设成立它们应该是0,3(也可能变换成0好对比)。而作为估计量因为受误差项随机因素影响本来也可能偏离这两个假设值，
但如果这次抽样偏的太大就有点疑问了。

``Durbin-Watson`` DW统计量是衡量误差项序列相关的，取值为0-4，不相关的理论值是2，完全负相关与正相关是0,4.

``Jarque-Bera (JB)`` 与 ``Prob(JB)`` 分别是JB正态性检验的统计量值与P值。与 ``Omnibus`` 与 ``Prob(Omnibus)`` 类似

``Cond. No.`` 是多重共线性的一种“检验”（Condition number test）。一般大于30就代表可能有很强的多重共线性问题。
这并不会带来理论上的问题（它会增大估计量标准误，但这并不是一个“错误”）。但它会带来数值上的困难。这个数如果
真的太大了下面的报告会写出来。

条件数的另一个意义是方程本身发生很小的变化使结果(解)产生很大的变化，这对于参数估计当然会产生对其可靠性的怀疑。
