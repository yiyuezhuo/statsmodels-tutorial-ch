.. _endog_exog:


``endog``, ``exog``, 是什么?
=================================
statsmodels使用 ``endog`` 与 ``exog`` （内生变量，外生变量，下面这对词一律不翻译
）作为观测的变量在估计中的名字。其他的统计包或书籍可能使用其他的名称，如:

===================== ======================
endog                 exog
===================== ======================
y                     x
y variable            x variable
left hand side (LHS)  right hand side (RHS)
dependent variable    independent variable
regressand            regressors
outcome               design
response variable     explanatory variable
===================== ======================


不同的领域的模型往往使用不同的名称，无论如何，我们基本上只使用 `endog` 与 `exog` 。
一个区分它们两个的记忆技巧是exogenous中含有一个x，其处于x变量的地位，正如其名。

`x` 与 `y` 是子目名称，它们有时被用于临时变量而且也不能提示使用它们的相关信息。
为了避免字母名称，我们决定使用描述性名称并且指定为 ``endog`` 与 ``exog`` 。
这一点虽然被有所质疑，但是也许未来会改观。

背景知识
----------


它们的非正式定义可以这样表述:

`endogenous`: （内生变量）系统内部决定的因素

`exogenous`: （外生变量）系统外决定的因素

*Endogenous 变量意味着（designates）变量是被经济学/计量经济学模型所解释，预测的*
http://stats.oecd.org/glossary/detail.asp?ID=794

*Exigenous 变量意味着变量在经济学/计量经济学模型中出现，但是没有被这个模型解释(它们被模型给定)*
http://stats.oecd.org/glossary/detail.asp?ID=890


在计量经济学和统计学中，术语的定义更正式，不同的exogeneity(weak,strong,strict)定义
以模型如何使用所决定。而statsmodels的变量用法通常不能总是以形式的意义得到解释，
但是它总是试图符合这些原理。

在最简单的形式中，模型将被观测的变量y与另一个被观测变量集合x以线性或非线性形式连接在一起

   y = f(x, beta) + noise
   y = x * beta + noise


无论如何，为了建立一个统计模型，我们需要关于解释变量x以及噪声的额外假设。一个
标准假设对于这些简单的模型是x与噪声不想管，再更广义的定义中，x作为exogenous意味着
我们没有考虑x这些解释变量是如何生成的，是实验设计出的还是从背后总体随机抽取出的，
当我们想要顾及x对y的效应时，或者检验这个效应的假设时。

换句话说,y是 *endogenous* 对于我们的模型，x是 *exogenous* 对于我们所估计的模型。


作为例子，假设你run一个实验，第二期一些被试没有被继续追朔。那么这个缺失是否影响你实验？

这将有助益，如果用户了解（或者从其能从教科书上参考的）统计模型背后所具有的假设。作为一个例子，
``exog`` 在 ``OLS`` 中可以有滞后因变量，如果误差项与噪声项在时间上(over time)分布独立（不相关）。无论如何，
如果误差项是自相关的，则OLS将失去其良好的统计属性（变得不一致）而应当选择的模型是ARMAX。
``statsmodels`` 提供了用于回归诊断的函数来检验是否一些假设是正确的。
结论？换句话说，我们能对待中途退出选择作为一个我们问题中的exogenous。



