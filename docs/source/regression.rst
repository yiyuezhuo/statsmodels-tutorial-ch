.. currentmodule:: statsmodels.regression.linear_model


.. _regression:

线性回归
=================


线性模型建模独立同分布的误差分布，也可以剞劂异方差或者自相关的情况。这个模块可以
使用最小二乘法（OLS），加权最小二乘（WLS），广义最小二乘（GLS）进行估计，并且
可用光以最小二乘处理自相关AR(p)误差。

例子
--------

::

    # 载入模块和数据
    import numpy as np
    import statsmodels.api as sm
    spector_data = sm.datasets.spector.load()
    spector_data.exog = sm.add_constant(spector_data.exog, prepend=False)

    # 汇报拟合OLS模型结果
    mod = sm.OLS(spector_data.endog, spector_data.exog)
    res = mod.fit()
    print res.summary()


更细致的例子像这样：

.. toctree::
   :maxdepth: 1

   examples/notebooks/generated/ols
   examples/notebooks/generated/wls
   examples/notebooks/generated/gls

技术文档
-----------------------

统计模型被假设为

:math:`Y = X\beta + \mu`, 其中 :math:`\mu\sim N\left(0,\Sigma\right).`

决定于 :math:`\Sigma` 的设定, 我们现在有四个可用类

* GLS : 广义最小二乘，对于任意协方差结构 :math:`\Sigma`
* OLS : 最小二乘法对于i.i.d残差 :math:`\Sigma=\textvf{I}`
* WLS : 加权最小二乘对于异方差误差结构 :math:`\text{diag}\left (\Sigma\right)`
* GLSAR : 可用广义最小二乘对于自回归AR(p)误差 :math:`\Sigma=\Sigma\left(\rho\right)`


所有回归模型定义了类似的方法并且遵循类似的结构，以类似的方式使用。它们中的一些
具有一些模型所特有的方法和属性。

GLS 是其他回归类的超类。

.. Class hierachy: TODO

.. yule_walker is not a full model class, but a function that estimate the
.. parameters of a univariate autoregressive process, AR(p). It is used in GLSAR,
.. but it can also be used independently of any models. yule_walker only
.. calculates the estimates and the standard deviation of the lag parameters but
.. not the additional regression statistics. We hope to include yule-walker in
.. future in a separate univariate time series class. A similar result can be
.. obtained with GLSAR if only the constant is included as regressors. In this
.. case the parameter estimates of the lag estimates are not reported, however
.. additional statistics, for example aic, become available.



参考文献
^^^^^^^^^^

关于回归模型的提要:

* D.C. Montgomery and E.A. Peck. "Introduction to Linear Regression Analysis." 2nd. Ed., Wiley, 1992.

回归模型在计量经济学上的应用的提要:

* R.Davidson and J.G. MacKinnon. "Econometric Theory and Methods," Oxford, 2004.
* W.Green.  "Econometric Analysis," 5th ed., Pearson, 2003.

.. toctree::
..   :maxdepth: 1
..
..   regression_techn1

属性
^^^^^^^^^^


下面是对模型类的通用属性进行的详细描述

pinv_wexog : array
    `p` x `n` 白化设计矩阵的Moore-Penrose 伪逆.
    它近似等于
    :math:`\left(X^{T}\Sigma^{-1}X\right)^{-1}X^{T}\Psi`, 其中
    :math:`\Psi` 被定义为 :math:`\Psi\Psi^{T}=\Sigma^{-1}`.
cholsimgainv : array
    `n` x `n` 上三角矩阵 :math:`\Psi^{T}` 其满足
    :math:`\Psi\Psi^{T}=\Sigma^{-1}`.
df_model : float
    模型自由度，它等于 `p` -1,其中 `p` 是回归子数量。注意这是因为截距不算进去导致的-1.
df_resid : float
    剩余自由度。这等于 `n - p` ，其中 `n` 是观测数而 `p` 是参数个数。注意截距
    在这里是被算进减去的自由度里的。
llf : float
    拟合模型的似然函数值
nobs : float
    观测值 `n` 的数值。
normalized_cov_params : array
    一个 `p` x `p` 数组，其等于 :math:`(X^{T}\Sigma^{-1}X)^{-1}`.
sigma : array
    误差项的协方差矩阵:
    :math:`\mu\sim N\left(0,\Sigma\right)`.
wexog : array
    白化设计矩阵 :math:`\Psi^{T}X`.
wendog : array
    白化响应变量 :math:`\Psi^{T}Y`.

模块引用
----------------

模型类
^^^^^^^^^^^^^

.. autosummary::
   :toctree: generated/

   OLS
   GLS
   WLS
   GLSAR
   yule_walker

.. currentmodule:: statsmodels.regression.quantile_regression

.. autosummary::
   :toctree: generated/

   QuantReg

结果类
^^^^^^^^^^^^^^^

拟合一个线性回归模型会返回一个结果类。OLS有一个特别的结果类，其包含一些
额外的方法来比较其他模型类的拟合结果。

.. currentmodule:: statsmodels.regression.linear_model

.. autosummary::
   :toctree: generated/

   RegressionResults
   OLSResults

.. currentmodule:: statsmodels.regression.quantile_regression

.. autosummary::
   :toctree: generated/

   QuantRegResults
