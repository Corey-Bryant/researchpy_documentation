*************
anova()
*************

Description
===========
Performs the analysis-of-variance (ANOVA) and analysis-of-covariance (ANCOVA).



Parameters
==========

Input
-----
**anova(formula_like, data = {}, sum_of_squares = 3)**

  * **formula_like** : A valid formula which will parse the data into a design matrix.
  * **data** : The dataframe which contains the data to be analyzed.
  * **sum_of_squares** : The type of sum of squares which is desired, the default is Type 3.


Returns
-------
Returns an object with class "anova"; this object has accessible methods which are
described below.

anova methods
^^^^^^^^^^^^^

  * **results(return_type = "Dataframe", decimals = 4, pretty_format = True)**

      * **return_type** : The type of data structure the results should be returned as. Supported options are 'Dataframe' which will return a Pandas DataFrame or 'Dictionary' which will return a dictionary.
      * **decimals** : The number of decimal places the data should be rounded too.
      * **pretty_format** : If pretty formatting should be applied. This adds extra empty spaces in the returned data structure for visualization of the results.

  * **regression_table(return_type = "Dataframe", decimals = 4, conf_level = 0.95)**

      * **return_type** : The type of data structure the results should be returned as. Supported options are 'Dataframe' which will return a Pandas DataFrame or 'Dictionary' which will return a dictionary.
      * **decimals** : The number of decimal places the data should be rounded too.
      * **conf_level** : The confidence interval desired.

  * **predict(estimate = None)**

      * **estimate** : Desired estimate. Available options are:

          * *"y"* or *"xb"* : Linear prediction
          * *"residuals"*, *"res"*, or *"r"* : Residuals
          * *"standardized_residuals"*, *"standardized_r"*, or *"r_std"* : Standardized residuals
          * *"studentized_residuals"*, *"student_r"*, or *"r_stud"* : Studentized (jackknifed) residuals
          * *"leverage"*, *"lev"* : Leverage of the observation (diagonal of the H matrix)

    See :ref:`predict` for formula information.



Effect Size Measures Formulas
=============================
By default, this method will return the measures of :math:`R^2`, :math:`\text{Adj. }R^2`, :math:`\eta^2`, :math:`\epsilon^2`, and :math:`\omega^2`. Please
note that for the factor terms, the reported effect sizes are partial, i.e., :math:`\eta^2_p`, :math:`\epsilon^2_p`, and :math:`\omega^2_p` respectively.
See Olejnik and Aligna (2000) :footcite:p:`Olejnik&Algina2000`, Kelley and Preacher (2012) :footcite:p:`Kelly&Preacher2012`, and/or Grissom and Kim (2012) :footcite:p:`Grissom&Kim2012`


Additionally, :math:`R^2` and :math:`\eta^2` are the same but have different names due to coming from different frameworks
which uses different terminology. Formulas for how to calculate these effect sizes
comes from (Olejnik & Aligna, 2000) :footcite:p:`Olejnik2000`; see

Eta-squared (:math:`\eta^2`) and :math:`R^2`
----------------------------------------------

.. math::

  \eta^2 = \frac{\text{SS}_{model}}{\text{SS}_{total}}


Adjusted :math:`R^2`
---------------------

.. math::

  \text{Adj. }R^2 = 1 - \frac{\text{df}_{total}}{\text{df}_{error}} * \frac{\text{SS}_{error}}{\text{SS}_{total}}



Partial Eta-squared (:math:`\eta^2_p`)
-----------------------------------------

.. math::

  \eta^2_p = \frac{\text{SS}_{effect}}{\text{SS}_{effect} + \text{SS}_{error}}


Omega-squared (:math:`\omega^2`)
-----------------------------------

.. math::

  \omega^2 = \frac{\text{SS}_{effect} - (\text{df}_{effect} * \text{MS}_{error})}{\text{SS}_{total} + \text{MS}_{error}}


Partial Omega-squared (:math:`\omega^2_p`)
----------------------------------------------

.. math::

  \omega^2_p = \frac{\text{SS}_{effect} - (\text{df}_{effect} * \text{MS}_{error})}{\text{SS}_{effect} + (\text{N} - \text{df}_{effect}) * \text{MS}_{error}}

Where N is the total number of observations included in the model.



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

If it's of interest, one can also access the underlying regression table.


.. code:: python

  m.regression_table()


.. raw:: html

  <div style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>systolic</th>      <th>Coef.</th>      <th>Std. Err.</th>      <th>t</th>      <th>p-value</th>      <th>95% Conf. Interval</th>    </tr>  </thead>  <tbody>    <tr>      <td>Intercept</td>      <td>29.3333</td>      <td>4.2905</td>      <td>6.8367</td>      <td>0.0000</td>      <td>[20.6969, 37.9697]</td>    </tr>    <tr>      <td>drug</td>      <td></td>      <td></td>      <td></td>      <td></td>      <td></td>    </tr>    <tr>      <td>1</td>      <td>(reference)</td>      <td></td>      <td></td>      <td></td>      <td></td>    </tr>    <tr>      <td>2</td>      <td>-1.3333</td>      <td>6.3639</td>      <td>-0.2095</td>      <td>0.8350</td>      <td>[-14.1432, 11.4765]</td>    </tr>    <tr>      <td>3</td>      <td>-13.0000</td>      <td>7.4314</td>      <td>-1.7493</td>      <td>0.0869</td>      <td>[-27.9587, 1.9587]</td>    </tr>    <tr>      <td>4</td>      <td>-15.7333</td>      <td>6.3639</td>      <td>-2.4723</td>      <td>0.0172</td>      <td>[-28.5432, -2.9235]</td>    </tr>    <tr>      <td>disease</td>      <td></td>      <td></td>      <td></td>      <td></td>      <td></td>    </tr>    <tr>      <td>1</td>      <td>(reference)</td>      <td></td>      <td></td>      <td></td>      <td></td>    </tr>    <tr>      <td>2</td>      <td>-1.0833</td>      <td>6.7839</td>      <td>-0.1597</td>      <td>0.8738</td>      <td>[-14.7387, 12.572]</td>    </tr>    <tr>      <td>3</td>      <td>-8.9333</td>      <td>6.3639</td>      <td>-1.4038</td>      <td>0.1671</td>      <td>[-21.7432, 3.8765]</td>    </tr>    <tr>      <td>drug:disease</td>      <td></td>      <td></td>      <td></td>      <td></td>      <td></td>    </tr>    <tr>      <td>2:2</td>      <td>6.5833</td>      <td>9.7839</td>      <td>0.6729</td>      <td>0.5044</td>      <td>[-13.1107, 26.2774]</td>    </tr>    <tr>      <td>2:3</td>      <td>-0.9000</td>      <td>8.9999</td>      <td>-0.1000</td>      <td>0.9208</td>      <td>[-19.0159, 17.2159]</td>    </tr>    <tr>      <td>3:2</td>      <td>-10.8500</td>      <td>10.2435</td>      <td>-1.0592</td>      <td>0.2950</td>      <td>[-31.4692, 9.7692]</td>    </tr>    <tr>      <td>3:3</td>      <td>1.1000</td>      <td>10.2435</td>      <td>0.1074</td>      <td>0.9150</td>      <td>[-19.5192, 21.7192]</td>    </tr>    <tr>      <td>4:2</td>      <td>0.3167</td>      <td>9.3017</td>      <td>0.0340</td>      <td>0.9730</td>      <td>[-18.4066, 19.04]</td>    </tr>    <tr>      <td>4:3</td>      <td>9.5333</td>      <td>9.2022</td>      <td>1.0360</td>      <td>0.3056</td>      <td>[-8.9897, 28.0564]</td>    </tr>  </tbody></table>
  </div>

References
==========

.. footbibliography::
