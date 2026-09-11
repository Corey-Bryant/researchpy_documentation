.. _predict:

************************
predict()
************************

Description
===========
Calculates linear predictions, various residuals, leverage statistics, distance statistics, and others.
Included in classes which support this method, it's also available as separate method using -predict()-.

Classes where predict included
------------------------------

  * ols
  * anova



Parameters
==========

Input
-----
**predict(mdl_data = {}, estimate = None)**

  * **mdl_data** : Class object from ols or anova
  * **estimate** : Desired estimate. Available options are:


      * *"y"* or *"xb"* : Linear prediction
      * *"residuals"*, *"res"*, or *"r"* : Residuals
      * *"standardized_residuals"*, *"standardized_r"*, or *"rstand"* : Standardized residuals
      * *"studentized_residuals"*, *"student_r"*, or *"rstud"* : Studentized (jackknifed) residuals
      * *"leverage"*, *"lev"* : Leverage of the observation (diagonal of the H matrix)



Returns
-------
Numpy array with n x 1 dimensions; where n is the number of observations



Formulas
==================

.. note::


    * Y is the dependent variable array/vector
    * X is the independent variable design matrix
    * H is the hat matrix
    * :math:`^T` indicates a transpose, i.e. :math:`X^T` is the transpose of X


Linear prediction :footcite:p:`kutner2005`
----------------------------------------------
The linear prediction is calculated as:

.. math::

  Y_e = X @ \text{betas}


Residuals :footcite:p:`kutner2005`
----------------------------------------------
Residuals are calculated as:

.. math::

  \text{e} = Y_{obs} - Y_e


Standardized residuals :footcite:p:`kutner2005`
--------------------------------------------------
Standardized residuals are calculated using the following formula:

.. math::

  t_i = \frac{e_i}{\sqrt{\text{MSE} * (1 - H_{ii})}}


Studentized (jackknifed) residuals :footcite:p:`kutner2005`
-------------------------------------------------------------
Studentized (jackknifed) residuals are calculated using the following formula:

.. math::

  t_i = r_i * (\frac{n - k -2}{n - k - 1 - r_i^2})^\frac{1}{2} \\


where:

  * n = number of observations
  * k = number of predictors
  * r = standardized residual


Leverage :footcite:p:`kutner2005`
-------------------------------------------------------------
Calculate the leverage of the observation; the leverage is the diagonal of the
H (hat) matrix. The H matrix is calculated using:

.. math::

  X @ (X^TX)^{-1}@X^T





Examples
========
First to load required libraries for this example. Below, an example data set will be loaded
in using researchpy.datasets; the data loaded in is a data set available through Stata
called 'systolic'.

.. code:: python

   import researchpy as rp
   import pandas as pd

   systolic = rp.datasets.systolic()


Now let's get some quick information regarding the data set.

.. code:: python

    systolic.info()


.. parsed-literal::

      <class 'pandas.core.frame.DataFrame'>
       Int64Index: 58 entries, 0 to 57
      Data columns (total 3 columns):
      #   Column    Non-Null Count  Dtype
      ---  ------    --------------  -----
      0   drug      58 non-null     int16
      1   disease   58 non-null     int16
      2   systolic  58 non-null     int16



Now to take a look at the descriptive statistics of the univariate data. The output
indicates that there are no missing observations and that each variable is stored
as an integer.


.. code:: python

    rp.summarize(systolic["systolic"])

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th></th>      <th>Name</th>      <th>N</th>      <th>Mean</th>      <th>Median</th>      <th>Variance</th>      <th>SD</th>      <th>SE</th>      <th>95% Conf. Interval</th>    </tr>  </thead>
    <tbody>    <tr>      <th>0</th>      <td>systolic</td>      <td>58</td>      <td>18.8793</td>      <td>21</td>      <td>163.862</td>      <td>12.8009</td>      <td>1.6808</td>      <td>[15.5135, 22.2451]</td>    </tr>  </tbody>
    </table>
    </div>







.. code:: python

    rp.crosstab(systolic["disease"], systolic["drug"])

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th></th>      <th>Variable</th>      <th>Outcome</th>      <th>Count</th>      <th>Percent</th>    </tr>  </thead>  <tbody>    <tr>      <th>0</th>      <td>drug</td>      <td>4</td>      <td>16</td>      <td>27.59</td>    </tr>    <tr>      <th>1</th>      <td></td>      <td>2</td>      <td>15</td>      <td>25.86</td>    </tr>    <tr>      <th>2</th>      <td></td>      <td>1</td>      <td>15</td>      <td>25.86</td>    </tr>    <tr>      <th>3</th>      <td></td>      <td>3</td>      <td>12</td>      <td>20.69</td>    </tr>    <tr>      <th>4</th>      <td>disease</td>      <td>3</td>      <td>20</td>      <td>34.48</td>    </tr>    <tr>      <th>5</th>      <td></td>      <td>2</td>      <td>19</td>      <td>32.76</td>    </tr>    <tr>      <th>6</th>      <td></td>      <td>1</td>      <td>19</td>      <td>32.76</td>    </tr>  </tbody></table>
    </div>

  Now to conduct the ANOVA; by default Type 3 sum of squares are used. There are a few
  ways one can conduct an ANOVA using Researchpy, the suggested approach is to assign
  the ANOVA model to an object that way one can utilize the built-in methods. If
  one does not want to do that, then running the model with and displaying the results
  in one-line will work too; the output will be returned as a tuple. The suggested
  approach will be shown in this example.


.. code:: python

    m = anova("systolic ~ C(drug) + C(disease) + C(drug):C(disease)", data = systolic, sum_of_squares = 3)

     desc, table = m.results()
     print(desc, table, sep = "\n"*2)


.. raw:: html

    <p>Note: Effect size values for factors are partial.</p>

    <div style="overflow-x: auto;">
    <table><thead>    <tr style="text-align: right;">    </tr>  </thead>  <tbody>    <tr>     <th>Number of obs =</th>      <td>58.0000</td>    </tr>    <tr>      <th>Root MSE =</th>      <td>10.5096</td>    </tr>    <tr>      <th>R-squared =</th>      <td>0.4560</td>    </tr>    <tr>      <th>Adj R-squared =</th>      <td>0.3259</td>    </tr>  </tbody></table>
    </div>

    <div style="overflow-x: auto;">
    <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>Source</th>      <th>Sum of Squares</th>      <th>Degrees of Freedom</th>      <th>Mean Squares</th>      <th>F value</th>      <th>p-value</th>      <th>Eta squared</th>      <th>Omega squared</th>    </tr>  </thead>  <tbody>    <tr>      <td>Model</td>      <td>4,259.3385</td>      <td>11</td>      <td>387.2126</td>      <td>3.5057</td>      <td>0.0013</td>      <td>0.4560</td>      <td>0.3221</td>    </tr>    <tr>      <td></td>      <td></td>      <td></td>      <td></td>      <td></td>      <td></td>      <td></td>      <td></td>    </tr>    <tr>      <td>drug</td>      <td>2,997.4719</td>      <td>3.0000</td>      <td>999.1573</td>      <td>9.0460</td>      <td>0.0001</td>      <td>0.3711</td>      <td>0.2939</td>    </tr>    <tr>      <td>disease</td>      <td>415.8730</td>      <td>2.0000</td>      <td>207.9365</td>      <td>1.8826</td>      <td>0.1637</td>      <td>0.0757</td>      <td>0.0295</td>    </tr>    <tr>      <td>drug:disease</td>      <td>707.2663</td>      <td>6.0000</td>      <td>117.8777</td>      <td>1.0672</td>      <td>0.3958</td>      <td>0.1222</td>      <td>0.0069</td>    </tr>    <tr>      <td></td>      <td></td>      <td></td>      <td></td>      <td></td>      <td></td>      <td></td>      <td></td>    </tr>    <tr>      <td>Residual</td>      <td>5,080.8167</td>      <td>46</td>      <td>110.4525</td>      <td></td>      <td></td>      <td></td>      <td></td>    </tr>    <tr>      <td>Total</td>      <td>9,340.1552</td>      <td>57</td>      <td>163.8624</td>      <td></td>      <td></td>      <td></td>      <td></td>    </tr>  </tbody></table>
    </div>



.. code:: python

    m.predict(estimate="r")[:10]


.. parsed-literal::

    array([[ 12.6667],
       [ 14.6667],
       [  6.6667],
       [-16.3333],
       [-10.3333],
       [ -7.3333],
       [  4.75  ],
       [ -2.25  ],
       [  4.75  ],
       [ -7.25  ]])



References
===========

.. footbibliography::
