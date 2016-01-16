
入门
===============

这个非常简单的案例研究是设计让你可以在实践中快速上手 ``statsmodels``。 从原始数据开始
我们将战史估计一个统计模型与画诊断图所需要的步骤。我们将仅适用 ``statsmodels`` 提供的
函数或者它依赖的 ``pandas`` 与 ``patsy`` 项。


    
载入模块与函数
-----------------------------

在 `安装statsmodels与它的依赖项 <install.html>`_  后, 我们载入一些其中的模块与函数:

.. ipython:: python

    import statsmodels.api as sm
    import pandas
    from patsy import dmatrices


`pandas <http://pandas.pydata.org/>`_ 是建立在 ``numpy`` 数组之上的， 提供富数据结构
与数据处理工具. The ``pandas.DataFrame`` 函数提供数据的标记数组（可能是异质heterogenous的）
类似 ``R`` "data.frame"。 ``pandas.read_csv`` 函数可以被用来转换一个逗号分隔值文件
到 ``DataFrame`` 对象。

`patsy <https://github.com/pydata/patsy>`_ 是一个Python库，其以类似R公式的语法描述
统计模型并且以此构造 `设计矩阵 <http://en.wikipedia.org/wiki/Design_matrix>`_ .


数据
----

我们下载 `Guerry dataset <http://vincentarelbundock.github.com/Rdatasets/doc/HistData/Guerry.html>`_, 一个
历史数据集，是Andre-Michel Guerry 1833年的 *Essay on the Moral Statistics of France*。
数据集以逗号分隔值格式（CSV）存在 `Rdatasets <http://vincentarelbundock.github.com/Rdatasets/>`_ 仓库.
我们可以下载这个文件到本地并且加载它使用 ``read_csv``，而 ``pandas`` 将一切都自动帮我们做好了


.. ipython:: python

    df = sm.datasets.get_rdataset("Guerry", "HistData").data

`导入/导出doc page <iolib.html>`_ 显示了如何载入其他数据格式。

我们选择感兴趣的变量并且选择底下5行：


.. ipython:: python

    vars = ['Department', 'Lottery', 'Literacy', 'Wealth', 'Region']
    df = df[vars]
    df[-5:]

注意这里有一个缺失值在 *Region* (区域)列。我们删除它通过 ``pandas`` 的
``DataFrame`` 方法。 


.. ipython:: python

    df = df.dropna()
    df[-5:]


建模目标
--------------------------------

我们想要知道1820年时，法国86个部门中的识字率是否与其在皇家彩票（Royal Lottery）上的人均
赌注（per capita wagers）量相关。我们需要控制每个部门的财富水平，我们也想要包含一系列
dummy变量于回归方程的右边以控制没观测到的区域影响异质性。模型以最小二乘（OLS）进行估计。


设计矩阵 (*endog* & *exog*)
----------------------------------

为了拟合 ``statsmodels`` 中大多数模型，你需要创建两个设计矩阵。第一个是一个内生变量矩阵
（例如，因变量dependent，响应response，从属变量regressand等）。第二个是外生变量。（例
如，因变量independent，预测子predictor，回归子regressor等）。OLS系数像通常那样进行计算。


.. math::

    \hat{\beta} = (X'X)^{-1} X'y

:math:`y` 是 :math:`N \times 1` 数据中的人均赌注(*Lottery*)变量。 :math:`X` 是 :math:`N \time7`
矩阵，其中7由 *Literacy* , *Wealth* 四个地域二分变量与截距构成。


``patsy`` 模块提供了方便的函数来生成设计矩阵通过使用 ``R`` 风格的公式。
你可以在这里找到更多的信息： http://patsy.readthedocs.org

我们使用 ``patsy`` 的 ``dmatrices`` 函数创建设计矩阵。


.. ipython:: python

    y, X = dmatrices('Lottery ~ Literacy + Wealth + Region', data=df, return_type='dataframe')

矩阵，数据frame看上去像这样：

.. ipython:: python

    y[:3]
    X[:3]


注意 ``dmatrices`` 会

* 分解分类变量 *Region* 为一组指示变量。
* 为外生变量矩阵增加了一个常数列
* 不是直接返回一个numpy数组而是反悔了 ``pandas`` DataFrames 数据结构。这是有用的因为DataFrames 使得 ``statsmodels`` 可以携带元数据（比如变量名称），当报道结果时。

上面的行为当然可以调整. 参见 `patsy doc pages
<http://patsy.readthedocs.org/>`_.


模型拟合与汇报
---------------------

在 ``statsmodels`` 中拟合模型通常由3个简单步骤组成：

1. 使用模型来描述模型
2. 拟合模型通过使用模型方法
3. 检视结果通过使用summary方法

.. ipython:: python

    mod = sm.OLS(y, X)    # Describe model
    res = mod.fit()       # Fit model
    print res.summary()   # Summarize model


``res`` 对象有很多有用的属性。作为例子，我们可以提取参数估计值以及r方通过输入：


.. ipython:: python

    res.params
    res.rsquared


输入 ``dir(res)`` 来获得属性的完整列表。

为了更多信息与例子，参见 ``回归的文档页面 <regression.html>``_


诊断与规范检验
-----------------------------------

``statsmodels`` 允许你执行一系列有用的 `回归诊断与规范检验 <stats.html#residual-diagnostics-and-specification-tests>`_.
作为例子，应用Rainbow检验进行线性检验（空假设为变量关系的确是线性的）:

.. ipython:: python

    sm.stats.linear_rainbow(res)


的确，输出结果的所指不是非常明显，但是我们可以从 `文档字符串 <generated/statsmodels.stats.diagnostic.linear_rainbow.html>`_
中得知第一个数是F-统计量，第二个是p值。（也可以使用 ``print sm.stats.linear_rainbow.__doc__``）

``statsmodels`` 也提供图形函数，作为例子，我们可以画一个偏回归的图形为一组回归子通过：

.. ipython:: python

    @savefig gettingstarted_0.png
    sm.graphics.plot_partregress('Lottery', 'Wealth', ['Region', 'Literacy'],
                                 data=df, obs_labels=False)

进一步
----


恭喜，你已经可以前往更高级的主题，它们在 `Table of Contents <index.html#table-of-contents>`_
