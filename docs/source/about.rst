.. currentmodule:: statsmodels

*****************
关于 Statsmodels
*****************

背景
----------


``scipy.stats`` 的 ``models`` 模块是Jonathan Taylor写的。曾经它是scipy的一部分，后
来它被移除了。在Google Summer of Code 2009后， ``statsmodels`` 被修正，测试，补完并
以一个新的包的形式被发布。在这之后，statsmodels开发团队继续增加新的模型，绘图函数以及统计方法。

测试
-------

多数结果都使用了其他统计包进行了验证，它们包括R, Stata 或 SAS。最初的重写与之后
的开发的指导原则就是所有内容都应该被验证。一些统计方法使用Monte Carlo方法进行了
检验。尽管我们力求遵循这个方式，但是并不能保证代码没有bug。一些辅助函数依然没有
进行充分的测试，一些边界情况依然没有被考虑到，以及很多统计模型仍有残留的数值问题
我们特别欢迎任何帮助以及对这些错误的报告，这使得我们可以改进我们已经存在的模型。

代码稳定性
~~~~~~~~~~~~~~

既已存在的模型一般会固定它们的用户接口，我们并不期望在此发生很大的改变。对于
既已存在的带啊，尽管不能保证API的稳定性，我们应当让代码一直可用，除了极个别
的情况。对于新的模型我们会修正用户接口反映我们获得更多经验后获得的教训。
这些变化将在我们的release的文档中被标注出来。

财务支持
-----------------

We are grateful for the financial support that we obtained for the
development of statsmodels:

我们感激资助我们进行statsmodels开发的财务支持:

 Google `www.google.com <https://www.google.com/>`_ : Google Summer of Code
 (GSOC) 2009-2013.

 AQR `www.aqr.com <http://www.aqr.com/>`_ :  为Wes McKinney 的向量自回归模型(VAR)财务赞助者


我们也感谢托管方 `github <https://github.com/>`_ 提供公共代码仓库。
`sourceforge <http://sourceforge.net/>`_ 托管文档以及 `python.org <https://python.org>`_ 
使得我们的模块可以从PyPi上被下载。
