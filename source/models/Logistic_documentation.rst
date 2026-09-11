*****************
Logistic()
*****************

Description
===========
Conducts logistic regression for a binary outcome using Maximum Likelihood
Estimation (MLE). By default, results are reported as odds ratios.

The class is fit via the ``binomial`` family with a ``logit`` link and supports
odds-ratio or raw log-odds (coefficient) reporting, confidence intervals, model
fit statistics (LR :math:`\chi^2`, log-likelihood, McFadden's pseudo :math:`R^2`),
and a post-estimation classification table.



Parameters
==========

Input
-----
**Logistic(formula, data = None, conf_level = 0.95, report_betas_as = "or", solver_options = None, table_decimals = None, initial_betas = None, initial_betas_method = "zeros", fit = True, display_summary = True)**

  * **formula** : A valid formula which will parse the data into a design matrix.
  * **data** : The dataframe which contains the data to be analyzed.
  * **conf_level** : The confidence level desired for the confidence intervals. The default is 0.95.
  * **report_betas_as** : How the estimated coefficients should be reported. ``"or"`` (default) reports odds ratios (:math:`e^{\beta}`); ``"coef"`` reports the raw log-odds coefficients.
  * **solver_options** : A ``SolverOptions`` dataclass instance or a dictionary of solver overrides. Supported keys include ``algorithm`` (default ``"IRLS"``), ``tol`` / ``tolerance`` (default ``1e-6``), ``max_iter`` (default ``300``), ``display`` (default ``True``), ``regularization`` (``None``, ``"l1"``, or ``"l2"``), and ``alpha`` (default ``0.0``).
  * **table_decimals** : A dictionary specifying the number of decimal places to use for different statistics. If ``None``, sensible defaults are used.
  * **initial_betas** : An optional array of starting coefficient values for the optimizer.
  * **initial_betas_method** : The method used to initialize the coefficients when ``initial_betas`` is not provided. The default is ``"zeros"``.
  * **fit** : If ``True`` (default), the model is fit when it is instantiated.
  * **display_summary** : If ``True`` (default), a formatted summary of the model is printed when the model is fit.

The ``LogisticRegression`` class is also available under the alias ``Logistic``.


Returns
-------
Returns an object with class "LogisticRegression"; this object has accessible
methods which are described below.

logistic methods
^^^^^^^^^^^^^^^^^

  * **results(report_betas_as = "or", return_type = "Dataframe", pretty_format = True, table_decimals = None)**

      * **report_betas_as** : ``"or"`` (default) reports odds ratios; ``"coef"`` reports the raw log-odds coefficients. When reporting odds ratios the coefficient column is labeled ``"Odds Ratio"``; otherwise it is labeled ``"Coef."``.
      * **return_type** : The type of data structure the results should be returned as. Supported options are 'Dataframe' which will return a Pandas DataFrame or 'Dictionary' which will return a dictionary.
      * **pretty_format** : If pretty formatting should be applied. This adds extra empty spaces in the returned data structure for visualization of the results.
      * **table_decimals** : A dictionary specifying the number of decimal places to use for different statistics.

  -results- returns 3 objects: (1) the fit statistics, (2) the model table (model name and log-likelihood), and (3) the coefficient / odds-ratio table. The underlying ``ModelResults`` dataclass is also stored on the model as ``m.ModelResults`` and additionally exposes the classification table via ``m.ModelResults.details``.

  * **classification_table(threshold = 0.5, return_type = "dataframe")**

      * **threshold** : The probability cutoff used to classify an observation as positive. An observation is classified ``"+"`` if its predicted probability is greater than or equal to the threshold. The default is 0.5.
      * **return_type** : The type of data structure the results should be returned as. ``"dataframe"`` (default) returns Pandas DataFrames; ``"dict"`` / ``"dictionary"`` returns dictionaries.

    -classification_table- returns a tuple ``(confusion_matrix, statistics)``, where the confusion matrix cross-tabulates the classified vs. observed classes and the statistics table reports sensitivity, specificity, predictive values, false-positive/negative rates, and the overall correctly-classified percentage.

  * **predict(estimate = "y", trans = None, decimals = 4)**

      * **estimate** : Desired estimate. Available options are:

          * *"y"* or *"xb"* : Linear prediction (the log-odds). Pass ``trans = numpy.exp`` to obtain the predicted probabilities instead.
          * *"residuals"*, *"res"*, or *"r"* : Residuals
          * *"standardized_residuals"*, *"standardized_r"*, or *"rstand"* : Standardized residuals
          * *"studentized_residuals"*, *"student_r"*, or *"rstud"* : Studentized (jackknifed) residuals
          * *"leverage"*, *"lev"* : Leverage of the observation (diagonal of the H matrix)

      * **trans** : An optional transformation applied to the linear prediction. For logistic regression, ``numpy.exp`` yields the predicted probabilities.
      * **decimals** : The number of decimal places the estimate should be rounded to. The default is 4.

    See :ref:`predict` for formula information.



Model Fit and Effect Measures
=============================
Logistic regression is estimated by maximum likelihood, so it does not use a
sum-of-squares decomposition. The reported model fit statistics are the number
of observations, the likelihood-ratio :math:`\chi^2` test of the overall model,
its p-value, the model log-likelihood, and McFadden's pseudo :math:`R^2`.


Odds Ratio
----------
The odds ratio for a predictor is the exponentiated coefficient.

.. math::

  \text{OR}_j = e^{\beta_j}


Likelihood-Ratio :math:`\chi^2`
--------------------------------

.. math::

  \text{LR }\chi^2 = -2 \left( \ell_{null} - \ell_{full} \right)

Where :math:`\ell_{null}` is the log-likelihood of the intercept-only (null)
model and :math:`\ell_{full}` is the log-likelihood of the fitted model.


McFadden's Pseudo :math:`R^2`
-----------------------------

.. math::

  \text{Pseudo } R^2 = 1 - \frac{\ell_{full}}{\ell_{null}}




Examples
========
First to load the required libraries for this example. Below, an example data set
will be loaded in using researchpy.datasets; the data loaded in is a data set
available through Stata called 'lbw' (low birth weight).

.. code:: python

  import researchpy as rp
  import pandas as pd

  lbw = rp.datasets.lbw()


Now let's get some quick information regarding the data set. The outcome of
interest, ``low``, is a binary indicator of low birth weight (1 = low birth
weight, 0 = normal); ``age`` is the mother's age in years, ``lwt`` is the
mother's weight in pounds at the last menstrual period, and ``smoke`` indicates
smoking status during pregnancy.

.. code:: python

  lbw[["low", "age", "lwt", "smoke"]].info()


.. parsed-literal::

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 189 entries, 0 to 188
    Data columns (total 4 columns):
    #   Column  Non-Null Count  Dtype
    ---  ------  --------------  --------
    0   low     189 non-null    int8
    1   age     189 non-null    int16
    2   lwt     189 non-null    int16
    3   smoke   189 non-null    category



Now to take a look at the distribution of the outcome.

.. code:: python

  rp.crosstab(lbw["low"])

.. raw:: html

  <div style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th></th>      <th>Variable</th>      <th>Outcome</th>      <th>Count</th>      <th>Percent</th>    </tr>  </thead>  <tbody>    <tr>      <th>0</th>      <td>low</td>      <td>0</td>      <td>130</td>      <td>68.78</td>    </tr>    <tr>      <th>1</th>      <td></td>      <td>1</td>      <td>59</td>      <td>31.22</td>    </tr>  </tbody></table>
  </div>



Now to fit the logistic regression model. The suggested approach is to assign the
model to an object so that the built-in methods can be used. By default the
coefficients are reported as odds ratios.


.. code:: python

  from researchpy.models import Logistic

  m = Logistic("low ~ age + lwt + smoke", data = lbw)

  fit_stats, model_table, table = m.results()
  print(fit_stats, table, sep = "\n"*2)

.. raw:: html

  <div style="overflow-x: auto;">
  <table><thead>    <tr style="text-align: right;">    </tr>  </thead>  <tbody>    <tr>      <th>N =</th>      <td>189</td>    </tr>    <tr>      <th>LR Chi^2(3) =</th>      <td>11.7696</td>    </tr>    <tr>      <th>Prob &gt; Chi^2 =</th>      <td>0.0082</td>    </tr>    <tr>      <th>Log likelihood =</th>      <td>-111.4478</td>    </tr>    <tr>      <th>Pseudo R^2 =</th>      <td>0.0502</td>    </tr>  </tbody></table>
  </div>

  <br>
  <br>

  <div style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>low</th>      <th>Odds Ratio</th>      <th>Std. Err.</th>      <th>z</th>      <th>p-value</th>      <th>95% Conf. Interval</th>    </tr>  </thead>  <tbody>    <tr>      <td>Intercept</td>      <td>3.9195</td>      <td>3.9755</td>      <td>1.3468</td>      <td>0.1780</td>      <td>[0.5369, 28.6176]</td>    </tr>    <tr>      <td>age</td>      <td>0.9618</td>      <td>0.0315</td>      <td>-1.1924</td>      <td>0.2331</td>      <td>[0.9020, 1.0254]</td>    </tr>    <tr>      <td>lwt</td>      <td>0.9880</td>      <td>0.0060</td>      <td>-1.9752</td>      <td>0.0482</td>      <td>[0.9762, 0.9999]</td>    </tr>    <tr>      <td>smoke</td>      <td></td>      <td></td>      <td></td>      <td></td>      <td></td>    </tr>    <tr>      <td>Nonsmoker</td>      <td>(reference)</td>      <td></td>      <td></td>      <td></td>      <td></td>    </tr>    <tr>      <td>Smoker</td>      <td>1.9554</td>      <td>0.6373</td>      <td>2.0581</td>      <td>0.0396</td>      <td>[1.0325, 3.7042]</td>    </tr>  </tbody></table>
  </div>


If the raw log-odds coefficients are preferred instead of odds ratios, pass
``report_betas_as = "coef"``.


.. code:: python

  fit_stats, model_table, table = m.results(report_betas_as = "coef")

.. raw:: html

  <div style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>low</th>      <th>Coef.</th>      <th>Std. Err.</th>      <th>z</th>      <th>p-value</th>      <th>95% Conf. Interval</th>    </tr>  </thead>  <tbody>    <tr>      <td>Intercept</td>      <td>1.3660</td>      <td>1.0143</td>      <td>1.3468</td>      <td>0.1780</td>      <td>[-0.6220, 3.3540]</td>    </tr>    <tr>      <td>age</td>      <td>-0.0390</td>      <td>0.0327</td>      <td>-1.1924</td>      <td>0.2331</td>      <td>[-0.1031, 0.0251]</td>    </tr>    <tr>      <td>lwt</td>      <td>-0.0121</td>      <td>0.0061</td>      <td>-1.9752</td>      <td>0.0482</td>      <td>[-0.0241, -0.0001]</td>    </tr>    <tr>      <td>smoke</td>      <td></td>      <td></td>      <td></td>      <td></td>      <td></td>    </tr>    <tr>      <td>Nonsmoker</td>      <td>(reference)</td>      <td></td>      <td></td>      <td></td>      <td></td>    </tr>    <tr>      <td>Smoker</td>      <td>0.6707</td>      <td>0.3259</td>      <td>2.0581</td>      <td>0.0396</td>      <td>[0.0319, 1.3095]</td>    </tr>  </tbody></table>
  </div>


A classification table can also be produced from the fitted model. It returns a
tuple containing the confusion matrix and a table of diagnostic statistics. The
observations are classified as positive when their predicted probability is at
least the ``threshold`` (0.5 by default).


.. code:: python

  confusion, stats = m.classification_table()
  print(confusion, stats, sep = "\n"*2)

.. raw:: html

  <div style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>Classified</th>      <th>low=1</th>      <th>low=0</th>      <th>Total</th>    </tr>  </thead>  <tbody>    <tr>      <td>+</td>      <td>6</td>      <td>5</td>      <td>11</td>    </tr>    <tr>      <td>-</td>      <td>53</td>      <td>125</td>      <td>178</td>    </tr>    <tr>      <td>Total</td>      <td>59</td>      <td>130</td>      <td>189</td>    </tr>  </tbody></table>
  </div>

  <br>
  <br>

  <div style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>Statistic</th>      <th>Percent</th>    </tr>  </thead>  <tbody>    <tr>      <td>Sensitivity Pr(+|1)</td>      <td>10.17</td>    </tr>    <tr>      <td>Specificity Pr(-|0)</td>      <td>96.15</td>    </tr>    <tr>      <td>Positive predictive value Pr(1|+)</td>      <td>54.55</td>    </tr>    <tr>      <td>Negative predictive value Pr(0|-)</td>      <td>70.22</td>    </tr>    <tr>      <td>False + rate for true 0 Pr(+|0)</td>      <td>3.85</td>    </tr>    <tr>      <td>False - rate for true 1 Pr(-|1)</td>      <td>89.83</td>    </tr>    <tr>      <td>False + rate for classified + Pr(0|+)</td>      <td>45.45</td>    </tr>    <tr>      <td>False - rate for classified - Pr(1|-)</td>      <td>29.78</td>    </tr>    <tr>      <td>Correctly classified</td>      <td>69.31</td>    </tr>  </tbody></table>
  </div>


Finally, predicted probabilities can be obtained from the fitted model by
applying the exponential transformation to the linear prediction.


.. code:: python

  import numpy as np

  # Predicted probabilities
  m.predict(estimate = "y", trans = np.exp)

  # Linear prediction (log-odds)
  m.predict(estimate = "xb")


References
==========

.. footbibliography::

