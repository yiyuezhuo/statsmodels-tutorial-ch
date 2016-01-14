Getting started
===============

This very simple case-study is designed to get you up-and-running quickly with
``statsmodels``. Starting from raw data, we will show the steps needed to
estimate a statistical model and to draw a diagnostic plot. We will only use
functions provided by ``statsmodels`` or its ``pandas`` and ``patsy``
dependencies.

开始
===============

这个非常简单的案例研究是设计让你可以在实践中快速上手 ``statsmodels``。 从原始数据开始
我们将战史估计一个统计模型与画诊断图所需要的步骤。我们将仅适用 ``statsmodels`` 提供的
函数或者它依赖的 ``pandas`` 与 ``patsy`` 项。



Loading modules and functions
-----------------------------

After `installing statsmodels and its dependencies <install.html>`_, we load a
few modules and functions:

.. ipython:: python

    import statsmodels.api as sm
    import pandas
    from patsy import dmatrices
    
载入模块与函数
-----------------------------

在 `安装statsmodels与它的依赖项 <install.html>`_  后, 我们载入一些其中的模块与函数:

.. ipython:: python

    import statsmodels.api as sm
    import pandas
    from patsy import dmatrices


`pandas <http://pandas.pydata.org/>`_ builds on ``numpy`` arrays to provide
rich data structures and data analysis tools. The ``pandas.DataFrame`` function
provides labelled arrays of (potentially heterogenous) data, similar to the
``R`` "data.frame". The ``pandas.read_csv`` function can be used to convert a
comma-separated values file to a ``DataFrame`` object.

`patsy <https://github.com/pydata/patsy>`_ is a Python library for describing
statistical models and building `Design Matrices
<http://en.wikipedia.org/wiki/Design_matrix>`_ using ``R``-like formulas.

`pandas <http://pandas.pydata.org/>`_ 是建立在 ``numpy`` 数组之上的， 提供富数据结构
与数据处理工具. The ``pandas.DataFrame`` 函数提供数据的标记数组（可能是异质heterogenous的）
类似 ``R`` "data.frame"。 ``pandas.read_csv`` 函数可以被用来转换一个逗号分隔值文件
到 ``DataFrame`` 对象。

`patsy <https://github.com/pydata/patsy>`_ is a Python library for describing
statistical models and building `Design Matrices
<http://en.wikipedia.org/wiki/Design_matrix>`_ using ``R``-like formulas.

`patsy <https://github.com/pydata/patsy>`_ 是一个Python库，其以类似R公式的语法描述
统计模型并且以此构造`设计矩阵 <http://en.wikipedia.org/wiki/Design_matrix>`_ .



Data
----

We download the `Guerry dataset
<http://vincentarelbundock.github.com/Rdatasets/doc/HistData/Guerry.html>`_, a
collection of historical data used in support of Andre-Michel Guerry's 1833
*Essay on the Moral Statistics of France*. The data set is hosted online in
comma-separated values format (CSV) by the `Rdatasets
<http://vincentarelbundock.github.com/Rdatasets/>`_ repository.
We could download the file locally and then load it using ``read_csv``, but
``pandas`` takes care of all of this automatically for us:

数据
----

我们下载 `Guerry dataset <http://vincentarelbundock.github.com/Rdatasets/doc/HistData/Guerry.html>`_, 一个
历史数据集，是Andre-Michel Guerry 1833年的 *Essay on the Moral Statistics of France*。
数据集以逗号分隔值格式（CSV）存在 `Rdatasets <http://vincentarelbundock.github.com/Rdatasets/>`_ 仓库.
我们可以下载这个文件到本地并且加载它使用 ``read_csv``，而 ``pandas`` 将一切都自动帮我们做好了


.. ipython:: python

    df = sm.datasets.get_rdataset("Guerry", "HistData").data

The `Input/Output doc page <iolib.html>`_ shows how to import from various
other formats.

We select the variables of interest and look at the bottom 5 rows:

.. ipython:: python

    vars = ['Department', 'Lottery', 'Literacy', 'Wealth', 'Region']
    df = df[vars]
    df[-5:]

Notice that there is one missing observation in the *Region* column. We
eliminate it using a ``DataFrame`` method provided by ``pandas``:

.. ipython:: python

    df = df.dropna()
    df[-5:]

Substantive motivation and model
--------------------------------

We want to know whether literacy rates in the 86 French departments are
associated with per capita wagers on the Royal Lottery in the 1820s. We need to
control for the level of wealth in each department, and we also want to include
a series of dummy variables on the right-hand side of our regression equation to
control for unobserved heterogeneity due to regional effects. The model is
estimated using ordinary least squares regression (OLS).


Design matrices (*endog* & *exog*)
----------------------------------

To fit most of the models covered by ``statsmodels``, you will need to create
two design matrices. The first is a matrix of endogenous variable(s) (i.e.
dependent, response, regressand, etc.). The second is a matrix of exogenous
variable(s) (i.e. independent, predictor, regressor, etc.). The OLS coefficient
estimates are calculated as usual:

.. math::

    \hat{\beta} = (X'X)^{-1} X'y

where :math:`y` is an :math:`N \times 1` column of data on lottery wagers per
capita (*Lottery*). :math:`X` is :math:`N \times 7` with an intercept, the
*Literacy* and *Wealth* variables, and 4 region binary variables.

The ``patsy`` module provides a convenient function to prepare design matrices
using ``R``-like formulas. You can find more information here:
http://patsy.readthedocs.org

We use ``patsy``'s ``dmatrices`` function to create design matrices:

.. ipython:: python

    y, X = dmatrices('Lottery ~ Literacy + Wealth + Region', data=df, return_type='dataframe')

The resulting matrices/data frames look like this:

.. ipython:: python

    y[:3]
    X[:3]

Notice that ``dmatrices`` has

* split the categorical *Region* variable into a set of indicator variables.
* added a constant to the exogenous regressors matrix.
* returned ``pandas`` DataFrames instead of simple numpy arrays. This is useful because DataFrames allow ``statsmodels`` to carry-over meta-data (e.g. variable names) when reporting results.

The above behavior can of course be altered. See the `patsy doc pages
<http://patsy.readthedocs.org/>`_.

Model fit and summary
---------------------

Fitting a model in ``statsmodels`` typically involves 3 easy steps:

1. Use the model class to describe the model
2. Fit the model using a class method
3. Inspect the results using a summary method

For OLS, this is achieved by:

.. ipython:: python

    mod = sm.OLS(y, X)    # Describe model
    res = mod.fit()       # Fit model
    print res.summary()   # Summarize model


The ``res`` object has many useful attributes. For example, we can extract
parameter estimates and r-squared by typing:


.. ipython:: python

    res.params
    res.rsquared

Type ``dir(res)`` for a full list of attributes.

For more information and examples, see the `Regression doc page <regression.html>`_

Diagnostics and specification tests
-----------------------------------

``statsmodels`` allows you to conduct a range of useful `regression diagnostics
and specification tests
<stats.html#residual-diagnostics-and-specification-tests>`_.  For instance,
apply the Rainbow test for linearity (the null hypothesis is that the
relationship is properly modelled as linear):

.. ipython:: python

    sm.stats.linear_rainbow(res)

Admittedly, the output produced above is not very verbose, but we know from
reading the `docstring <generated/statsmodels.stats.diagnostic.linear_rainbow.html>`_
(also, ``print sm.stats.linear_rainbow.__doc__``) that the
first number is an F-statistic and that the second is the p-value.

``statsmodels`` also provides graphics functions. For example, we can draw a
plot of partial regression for a set of regressors by:

.. ipython:: python

    @savefig gettingstarted_0.png
    sm.graphics.plot_partregress('Lottery', 'Wealth', ['Region', 'Literacy'],
                                 data=df, obs_labels=False)

More
----

Congratulations! You're ready to move on to other topics in the
`Table of Contents <index.html#table-of-contents>`_
