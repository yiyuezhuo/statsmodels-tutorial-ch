.. :tocdepth: 2


欢迎来到Statsmodels的文档
======================================

:mod:`statsmodels` 是一个Python模块，其为估计大量统计模型提供估计用的类和函数，同时还能进行统计检验
统计数据探索。每个估计量都有一系列额外的统计结果可用。
这些结果被与其他统计包所给出的结果进行了比较以保证其给出的结果正确。此包以BSD许可证发布。
网上文档可以在这里托管  `sourceforge <http://statsmodels.sourceforge.net/>`__.



最简单的例子
----------------

在 ``statsmodels`` 的 ``0.5.0`` 版之后，你可以使用R语言式的公式协同 ``pandas``
data frames 来拟合你的模型。这里有一个使用最小二乘的简单例子： 

.. code-block:: python

    import numpy as np
    import statsmodels.api as sm
    import statsmodels.formula.api as smf

    # 载入数据
    dat = sm.datasets.get_rdataset("Guerry", "HistData").data

    # 拟合回归模型（一个回归子（regressors）进行了自然对数变换）
    results = smf.ols('Lottery ~ Literacy + np.log(Pop1831)', data=dat).fit()

    # 检视结果
    print results.summary()



你也可以使用 ``numpy`` 数组取代公式:


.. code-block:: python

    import numpy as np
    import statsmodels.api as sm

    # 生成人工数据
    nobs = 100
    X = np.random.random((nobs, 2))
    X = sm.add_constant(X)
    beta = [1, .1, .5]
    e = np.random.random(nobs)
    y = np.dot(X, beta) + e

    # 拟合回归模型
    results = sm.OLS(y, X).fit()

    # 检视结果
    print results.summary()


使用 `dir(results)` 查看有关返回值。这些属性在 `results.__doc__` 里进行了描述，而
相关方法也有它们自己的docstrings.


引用
--------

当使用statsmodels在科学出版物中，请考虑引用下面的文献：

 Seabold, Skipper, and Josef Perktold. "`Statsmodels: Econometric and statistical modeling with python. <http://conference.scipy.org/proceedings/scipy2010/pdfs/seabold.pdf>`_" *Proceedings of the 9th Python in Science Conference.* 2010.

Bibtex entry::

  @inproceedings{seabold2010statsmodels,
    title={Statsmodels: Econometric and statistical modeling with python},
    author={Seabold, Skipper and Perktold, Josef},
    booktitle={9th Python in Science Conference},
    year={2010},
  }


简单文档
-------------------

.. toctree::
    :maxdepth: 3

    release/index
    gettingstarted
    example_formulas
    install
    about

关于statsmodels开发的信息:

.. toctree::
   :maxdepth: 1

   endog_exog
   importpaths
   pitfalls
   dev/index
   dev/internal

内容表
-----------------

.. toctree::
   :maxdepth: 3

   regression
   glm
   gee
   rlm
   mixed_linear
   discretemod
   anova
   tsa
   statespace
   duration
   stats
   nonparametric
   gmm
   contingency_tables
   emplike
   miscmodels
   distributions
   graphics
   iolib
   tools
   datasets/index
   sandbox


索引和表
------------------

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
