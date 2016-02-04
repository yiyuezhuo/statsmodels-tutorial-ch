.. _formula_examples:


使用R风格模式拟合模型
=====================================

在0.5.0版本之后, ``statsmodels`` 让用户可以使用R风格的公式拟合模型。在内部，
``statsmodels`` 使用 `patsy <http://patsy.readthedocs.org/>`_ 包来转换公式
与数据为模型拟合所用的矩阵。这个公式框架是非常有力的，这个教程只介绍最简单
的部分。一个对公式语言的全面介绍可以参见 ``patsy`` 的文档：

-  `Patsy formula language description <http://patsy.readthedocs.org/>`_


模块与函数的导入
-----------------------------

.. code:: python

    import statsmodels.formula.api as smf
    import numpy as np
    import pandas


注意我们导入的是 ``statsmodels.formula.api`` 而不是通常的 ``statsmodels.api``
``formula.api`` 放了很多类似 ``api`` 里的函数（如OLS,GLM），但是它还放了对等的其小写
版本。通常来说，小写的模型接收 ``formula`` 与 ``df`` 参数，而大写版本则接收``endog``
与``exog`` 设计矩阵。 ``formula`` 接收一个字符串以 ``patsy`` 公式项描述的模型。
``df`` 则持一个 `pandas <http://pandas.pydata.org/>`_ DataFrame 对象。`

``dir(smf)`` 会打印出一个可用模型的列表。

适用公式的模型有以下通用的参数调用格式:

``(formula, data, subset=None, *args, **kwargs)``


使用公式进行OLS回归
-----------------------------

为了入门，我们拟合一个在 `Getting Started <gettingstarted.html>`_ 页面
描述过的线性模型。下载数据，选择列并且智能删除带缺失的观测值：

.. code:: python

    df = sm.datasets.get_rdataset("Guerry", "HistData").data
    df = df[['Lottery', 'Literacy', 'Wealth', 'Region']].dropna()
    df.head()

.. parsed-literal::

       Lottery  Literacy  Wealth Region
    0       41        37      73      E
    1       38        51      22      N
    2       66        13      61      C
    3       80        46      76      E
    4       79        69      83      E


拟合模型：

.. code:: python

    mod = smf.ols(formula='Lottery ~ Literacy + Wealth + Region', data=df)
    res = mod.fit()
    print res.summary()

.. parsed-literal::

                                OLS Regression Results                            
    ==============================================================================
    Dep. Variable:                Lottery   R-squared:                       0.338
    Model:                            OLS   Adj. R-squared:                  0.287
    Method:                 Least Squares   F-statistic:                     6.636
    Date:                Sun, 13 Jan 2013   Prob (F-statistic):           1.07e-05
    Time:                        10:38:36   Log-Likelihood:                -375.30
    No. Observations:                  85   AIC:                             764.6
    Df Residuals:                      78   BIC:                             781.7
    Df Model:                           6                                         
    ===============================================================================
                      coef    std err          t      P>|t|      [95.0% Conf. Int.]
    -------------------------------------------------------------------------------
    Intercept      38.6517      9.456      4.087      0.000        19.826    57.478
    Region[T.E]   -15.4278      9.727     -1.586      0.117       -34.793     3.938
    Region[T.N]   -10.0170      9.260     -1.082      0.283       -28.453     8.419
    Region[T.S]    -4.5483      7.279     -0.625      0.534       -19.039     9.943
    Region[T.W]   -10.0913      7.196     -1.402      0.165       -24.418     4.235
    Literacy       -0.1858      0.210     -0.886      0.378        -0.603     0.232
    Wealth          0.4515      0.103      4.390      0.000         0.247     0.656
    ==============================================================================
    Omnibus:                        3.049   Durbin-Watson:                   1.785
    Prob(Omnibus):                  0.218   Jarque-Bera (JB):                2.694
    Skew:                          -0.340   Prob(JB):                        0.260
    Kurtosis:                       2.454   Cond. No.                         371.
    ==============================================================================



分类变量
---------------------

观察上面的打印出来的汇总结果，注意到 ``patsy`` 发现了 *Region* 元素是文本字符串，
所以它将 *Region* 当成了分类变量。因为 ``patsy`` 默认设定是包含了截距的，所以我们
自动抛弃了 *Region* 分类变量中的一个值。

如果 *Region* 已经是一个整数变量了而我们想让其被当成是一个分类变量，我们可以做到这一点
通过使用 ``C()`` 操作符：

.. code:: python

    res = smf.ols(formula='Lottery ~ Literacy + Wealth + C(Region)', data=df).fit()
    print res.params

.. parsed-literal::

    Intercept         38.651655
    C(Region)[T.E]   -15.427785
    C(Region)[T.N]   -10.016961
    C(Region)[T.S]    -4.548257
    C(Region)[T.W]   -10.091276
    Literacy          -0.185819
    Wealth             0.451475



``patsy`` 的分类变量的更高级的使用例子可以在这里找到 `Patsy: Contrast Coding Systems for
categorical variables <contrasts.html>`_


操作符
---------

我们已经看到 "~" 分割了模型的左边与右边，以及 "+" 增加了新的列到设计矩阵中。


移除变量
~~~~~~~~~~~~~~~~~~

"-" 符号可以用于移除列/变量。作为例子，我们可以从模型中移除截距，通过：

.. code:: python

    res = smf.ols(formula='Lottery ~ Literacy + Wealth + C(Region) -1 ', data=df).fit()
    print res.params

.. parsed-literal::

    C(Region)[C]    38.651655
    C(Region)[E]    23.223870
    C(Region)[N]    28.634694
    C(Region)[S]    34.103399
    C(Region)[W]    28.560379
    Literacy        -0.185819
    Wealth           0.451475



乘法交互项
~~~~~~~~~~~~~~~~~~~~~~~~~~~



":" 操作符可以增加一个列到设计矩阵中，该列有另两个列的乘积组成。"\*" 操
作符则会包含三个列，分别由两边的单独列与乘积列组成。

.. code:: python

    res1 = smf.ols(formula='Lottery ~ Literacy : Wealth - 1', data=df).fit()
    res2 = smf.ols(formula='Lottery ~ Literacy * Wealth - 1', data=df).fit()
    print res1.params, '\n'
    print res2.params

.. parsed-literal::

    Literacy:Wealth    0.018176 
    
    Literacy           0.427386
    Wealth             1.080987
    Literacy:Wealth   -0.013609



关于操作符更得内容请参考 `patsy docs <https://patsy.readthedocs.org/en/latest/formulas.html>`_ 。


函数
---------

你可以作用向量化函数到你的模型中

.. code:: python

    res = smf.ols(formula='Lottery ~ np.log(Literacy)', data=df).fit()
    print res.params

.. parsed-literal::

    Intercept           115.609119
    np.log(Literacy)    -20.393959



这样定义一个新的函数:

.. code:: python

    def log_plus_1(x):
        return np.log(x) + 1.
    res = smf.ols(formula='Lottery ~ log_plus_1(Literacy)', data=df).fit()
    print res.params

.. parsed-literal::

    Intercept               136.003079
    log_plus_1(Literacy)    -20.393959


.. _patsy-namespaces:


命令空间
----------

注意上面所有例子使用的都是调用者命令控件来寻找所要调用的函数。命名空间可以使用关键字 ``eval_env`` 空间。
作为例子，你可能会想要一个自定义的命名空间通过使用类 :class:`patsy:patsy.EvalEnvironment` 或者你想要
使用"清洁"的命令控件，我们可以通过传入 ``eval_func=-1`` 来做到这一点。默认情况下则会使用调用者命名空间。
而这可能造成一些意料之外的结果，比如，如果有人有一个叫 ``C`` 的变量在用户命名空间中，或者在传入 ``patsy``
的数据结构中包含它，则 ``C`` 会被在公式里被当成一个分类变量。见 `Patsy 
API Reference <http://patsy.readthedocs.org/en/latest/API-reference.html>`_ 了解更多



在未支持的模型中使用公式
---------------------------------------------------------

甚至一个``statsmodels``函数不支持公式时，你也可以使用 ``patsy`` 公式语言
形成设计矩阵。这些矩阵之后可以分成 ``endog`` , ``exog`` 参数传到拟合函数中去。

.. code:: python

    import patsy
    f = 'Lottery ~ Literacy * Wealth'
    y,X = patsy.dmatrices(f, df, return_type='dataframe')
    print y[:5]
    print X[:5]

.. parsed-literal::

       Lottery
    0       41
    1       38
    2       66
    3       80
    4       79
       Intercept  Literacy  Wealth  Literacy:Wealth
    0          1        37      73             2701
    1          1        51      22             1122
    2          1        13      61              793
    3          1        46      76             3496
    4          1        69      83             5727



若要获取pandas DataFrame:

.. code:: python

    f = 'Lottery ~ Literacy * Wealth'
    y,X = patsy.dmatrices(f, df, return_type='dataframe')
    print y[:5]
    print X[:5]

.. parsed-literal::

       Lottery
    0       41
    1       38
    2       66
    3       80
    4       79
       Intercept  Literacy  Wealth  Literacy:Wealth
    0          1        37      73             2701
    1          1        51      22             1122
    2          1        13      61              793
    3          1        46      76             3496
    4          1        69      83             5727


.. code:: python

    print smf.OLS(y, X).fit().summary()

.. parsed-literal::

                                OLS Regression Results                            
    ==============================================================================
    Dep. Variable:                Lottery   R-squared:                       0.309
    Model:                            OLS   Adj. R-squared:                  0.283
    Method:                 Least Squares   F-statistic:                     12.06
    Date:                Sun, 13 Jan 2013   Prob (F-statistic):           1.32e-06
    Time:                        10:38:36   Log-Likelihood:                -377.13
    No. Observations:                  85   AIC:                             762.3
    Df Residuals:                      81   BIC:                             772.0
    Df Model:                           3                                         
    ===================================================================================
                          coef    std err          t      P>|t|      [95.0% Conf. Int.]
    -----------------------------------------------------------------------------------
    Intercept          38.6348     15.825      2.441      0.017         7.149    70.121
    Literacy           -0.3522      0.334     -1.056      0.294        -1.016     0.312
    Wealth              0.4364      0.283      1.544      0.126        -0.126     0.999
    Literacy:Wealth    -0.0005      0.006     -0.085      0.933        -0.013     0.012
    ==============================================================================
    Omnibus:                        4.447   Durbin-Watson:                   1.953
    Prob(Omnibus):                  0.108   Jarque-Bera (JB):                3.228
    Skew:                          -0.332   Prob(JB):                        0.199
    Kurtosis:                       2.314   Cond. No.                     1.40e+04
    ==============================================================================
    
    The condition number is large, 1.4e+04. This might indicate that there are
    strong multicollinearity or other numerical problems.

