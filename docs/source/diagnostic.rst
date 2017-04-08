:orphan:

.. _diagnostics:


回归诊断与设定检验
==============================================



介绍
------------


在很多类型的统计分析中，我们不确定是否我们的统计模型被正确设定了。如当我们使用
ols时，我们假设了线性与同方差的成立，有时一些检验还进一步假设了分布是正态分布的，
或者样本是大样本。由于我们的分析结果依赖于这些统计假设，所以只有这些假设成立
（或者至少近似成立）的情况下我们的结果才是成立。


如果并不确信设定的正确性，一个解决方法是使用稳健方法，比如稳健回归或稳健协方差
（sandwich）估计量。第二种方法是检验是否我们的样本与这些假设相一致。


下面的内容简略的概括了线性回归的设定与诊断检验。


异方差检验
------------------------


它们是一些原假设是所有观测具有相同的误差方差（或者说误差是同方差的）的检验。
这些检验区别在哪种异方差模式被放在备择假设上。它们在不同的异方差模式上的检验
功效也是有区别的。

	
:py:func:`het_breushpagan <statsmodels.stats.diagnostic.het_breushpagan>`
    Breush-Pagan的拉格朗日乘子异方差检验

:py:func:`het_white <statsmodels.stats.diagnostic.het_white>`
	White的拉格朗日乘子异方差检验

:py:func:`het_goldfeldquandt <statsmodels.stats.diagnostic.het_goldfeldquandt>`
	检验是否方差在两个子样本中是相同的




自相关检验
---------------------


这些检验用于检验是否回归残差有自相关性。
它们假设观测值是按时间排序的。

  
:py:func:`durbin_watson <statsmodels.stats.diagnostic.durbin_watson>`
  - Durbin-Watson 检验，检验残差是否自相关
  - 该结果会被 summary() 显示出来

:py:func:`acorr_ljungbox <statsmodels.stats.diagnostic.acorr_ljungbox>`
  - Ljung-Box 检验，检验残差是否自相关
  - 同时返回 Box-Pierce 统计量

:py:func:`acorr_breush_godfrey <statsmodels.stats.diagnostic.acorr_breush_godfrey>`
  - Breush-Pagan 检验，检验残差是否自相关





非线性检验
-------------------

	
:py:func:`linear_harvey_collier <statsmodels.stats.diagnostic.linear_harvey_collier>`
  - 乘子检验，其原假设为线性设定是正确的。

:py:func:`acorr_linear_rainbow <statsmodels.stats.diagnostic.acorr_linear_rainbow>`
  - 乘子检验，其原假设为线性设定是正确的。

:py:func:`acorr_linear_lm <statsmodels.stats.diagnostic.acorr_linear_lm>`
  - 拉格朗日乘子检验，其原假设是线性设定是正确的。这个检验的备择假设的函数形式需要指定。




检验结构性变化，参数稳定性
------------------------------------------------


检验回归系数在整个样本序列一直是不变的。

	
已知系数可能变化位置情况
^^^^^^^^^^^^^^^^^^

OneWayLS :
  - 用来简化检验分组回归的回归系数是否相等的检验的ols简单封装。

缺失
  - predictive test: Greene, 分组中的观测数少于解释变量。



  
不知道系数可能变化位置情况
^^^^^^^^^^^^^^^^^^^^

:py:func:`breaks_cusumolsresid <statsmodels.stats.diagnostic.breaks_cusumolsresid>`
  - cusum 检验，基于ols残差的来判定参数稳定性。（在突变的那一刻残差累积值会突然变大）

:py:func:`breaks_hansen <statsmodels.stats.diagnostic.breaks_hansen>`
  - ols模型稳定性检验, 基于参数化的分段, Hansen 1992 
  

:py:func:`recursive_olsresiduals <statsmodels.stats.diagnostic.recursive_olsresiduals>`
  计算递归的ols(每次进入前几个数据)的残差和累积值统计量。这是一个服务于基于递归
  残差的检验的辅助函数。由于它使用递归更新规则计算每步的值，所以它比反复重新估计
  模型的方法效率高。




多重共线性检验
--------------------------------

  
条件数 (statsmodels.stattools)
  - -- 需要与 Stata 结果比较 --
  - cf Grene (3rd ed.) pp 57-8
  
numpy.linalg.cond
  - 用于计算更一般的条件数,

方差膨胀因子
  This is currently together with influence and outlier measures
  (with some links to other tests here: http://www.stata.com/help.cgi?vif)
  现在它被和离群点测度与影响放在一起 (一些相关检验可参考：http://www.stata.com/help.cgi?vif )





正态性检验与分布拟合检验
--------------------------------

  
:py:func:`jarque_bera <statsmodels.stats.tools.jarque_bera>`
  - 会在summary()中被汇报
  - 检验残差是否符合正态分布

在scipy.stats中的正态性检验
  需要重新编写

:py:func:`omni_normtest <statsmodels.stats.tools.omni_normtest>`
  - 检验残差是否符合正态分布
  - 会在summary()中被汇报。
  
:py:func:`normal_ad <statsmodels.stats.diagnostic.normal_ad>`
  - Anderson Darling正态性检验，使用估计而非给定的均值和方差。

:py:func:`kstest_normal <statsmodels.stats.diagnostic.kstest_normal>` :py:func:`lillifors <statsmodels.stats.diagnostic.lillifors>`
  Lillifors 正态性检验，这是一种使用 Kolmogorov-Smirnov统计量并使用估计的均值与方差进行的检验。
  lillifors是kstest_normal的别名。

qqplot, scipy.stats.probplot

scipy.stats中的其他的分布拟合优度检验与强化版
  - kolmogorov-smirnov
  - anderson : Anderson-Darling
  - likelihood-ratio, ...
  - chisquare tests, powerdiscrepancy : 需要分组的封装




离群点与影响诊断测度
-----------------------------------------
这些测度方式试图识别出那些是离群点的观测，它们有着大残差或对回归估计产生着
巨大的影响。稳健回归，RLM，既可以用于进行削弱离群点影响的稳健的回归，也可以
用来识别离群点。RLM的优势在于它的估计结果在有很多离群点时也不受很大影响，
与之对比，虽然有很多其他方法在识别个别离群点上更为有效，
但它们却不能识别成组的离群点。

:py:class:`RLM <statsmodels.robust.robust_linear_model.RLM>`
    example_rlm.py 中的例子 ::

        import statsmodels.api as sm

		### 使用 Huber's T norm 与默认的绝对偏差中位数度量的例子

        data = sm.datasets.stackloss.load()
        data.exog = sm.add_constant(data.exog)
        huber_t = sm.RLM(data.endog, data.exog, M=sm.robust.norms.HuberT())
        hub_results = huber_t.fit()
        print hub_results.weights

	这个权重一个关于特定的观测是如何被所要求的尺度所加权的的度量。

   
:py:class:`Influence <statsmodels.stats.outliers_influence.OLSInfluence>`
   stats.outliers_influence中的类，当接收一个拟合出的OLS模型时给出对于离群点
   的标准测度与影响度量。它主要是面向OLS编写的，其中有些而不是全部也可以用于
   其他模型。
   这些统计量中的一部分可以从OLS结果对象中被计算，有些需要原始数据进行辅助回归。

   - resid_press
   - resid_studentized_external
   - resid_studentized_internal
   - ess_press
   - hat_matrix_diag
   - cooks_distance - Cook's Distance `Wikipedia <http://en.wikipedia.org/wiki/Cook%27s_distance>`_ (with some other links)
   - cov_ratio
   - dfbetas
   - dffits
   - dffits_internal
   - det_cov_params_not_obsi
   - params_not_obsi
   - sigma2_not_obsi




单位根检验
---------------

:py:func:`unitroot_adf <statsmodels.stats.diagnostic.unitroot_adf>`
  - 与adfuller等价，只是另一个名字而已。


