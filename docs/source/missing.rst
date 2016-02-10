:orphan:

.. _missing_data:

Missing Data
------------
All of the models can handle missing data. For performance reasons, the default is not to do any checking for missing data. If, however, you would like for missing data to be handled internally, you can do so by using the missing keyword argument. The default is to do nothing

缺失数据
------------
所有模型都可以处理缺失数据。为了性能原因，默认设定是不出来任何缺失数据。如果，你如果想内部处理缺失数据。你可以通过关键字参数
做到这一点。默认情况下并不做任何事。

.. code-block:: python

   >>> import statsmodels.api as sm
   >>> data = sm.datasets.longley.load()
   >>> data.exog = sm.add_constant(data.exog)
   >>> # 加入一些缺失数据
   >>> missing_idx = np.array([False] * len(data.endog))
   >>> missing_idx[[4, 10, 15]] = True
   >>> data.endog[missing_idx] = np.nan
   >>> ols_model = sm.OLS(data.endog, data.exog)
   >>> ols_fit = ols_model.fit()
   >>> print ols_fit.params
   [ nan  nan  nan  nan  nan  nan  nan]

This silently fails and all of the model parameters are NaN, which is probably not what you expected. If you are not sure whether or not you have missing data you can use `missing = 'raise'`. This will raise a `MissingDataError` during model instantiation if missing data is present so that you know something was wrong in your input data.

发生了一些隐秘的事情使得所有模型参数被估计为了NaN，这可能并不是你期望的。如果你不确定是否出现了缺失数据问题，
你可以使用 `missing= 'raise'`. 如果的确出现了缺失数据问题那么在对模型进行实例化时将会发生 `MissingDataError` 
错误使得你可以知道你的数据出现了某些问题。

.. code-block:: python

   >>> ols_model = sm.OLS(data.endog, data.exog, missing='raise')

If you want statsmodels to handle the missing data by dropping the observations, use `missing = 'drop'`.

如果你想要 statsmodels 处理缺失数据，以丢掉这些观测的方式，可以使用 `missing = 'drop'`

.. code-block:: python

   >>> ols_model = sm.OLS(data.endog, data.exog, missing='drop')

We are considering adding a configuration framework so that you can set the option with a global setting.

我们正在考虑加入一个配置框架，让你可以为全局设定选项。


Multiple Imputation with Chained Equations
------------------------------------------

The MICE module allows most Statsmodels models to be fit to a dataset
with missing values on the independent and/or dependent variables, and
provides rigorous standard errors for the fitted parameters.  The
basic idea is to treat each variable with missing values as the
dependent variable in a regression, with some or all of the remaining
variables as its predictors.  The MICE procedure cycles through these
models, fitting each in turn, then uses a procedure called "predictive
mean matching" (PMM) to generate random draws from the predictive
distributions determined by the fitted models.  These random draws
become the imputed values for one imputed data set.

链式方程的多重填充
------------------------------------------

MICE模块


By default, each variable with missing variables is modeled using a
linear regression with main effects for all other variables in the
data set.  Note that even when the imputation model is linear, the PMM
procedure preserves the domain of each variable.  Thus, for example,
if all observed values for a given variable are positive, all imputed
values for the variable will always be positive.  The user also has
the option to specify which model is used to produce imputed values
for each variable.

.. code



Implementation Details
----------------------

Internally, this function uses `pandas.isnull <pandas:http://pandas.pydata.org/pandas-docs/stable/missing_data.html#working-with-missing-data>`_. Anything that returns True from this function will be treated as missing data.
