*************
summarize()
*************

Description
===========
Calculates univariate descriptive statistics and returns the information as either a
Pandas DataFrame object (the default) or Python dictionary object.

The following calculations are available:

  * count of non-missing (N),
  * the average (Mean),
  * the median (Median),
  * variance (Variance),
  * standard deviation (SD),
  * standard error (SE),
  * confidence interval (CI),
  * minimum value (Min),
  * maximum value (Max),
  * range (Range),
  * the kurtosis (Kurtosis), and
  * the skew (Skew).

If no univariate descriptive statistics are specified, by default the N, Mean, Median,
Variance, SD, SE, and 95% confidence interval are calculated.


Parameters
==========

Input
-----
**summarize(data = {}, name = None, stats = [], ci_level = 0.95, decimals = 4, return_type = "Dataframe")**

  * **data** : The Pandas DataFrame or array_like object which contains the data to be analyzed.
  * **stats** : The univariate statistics to be calculated entered as a list; the default is ["N", "Mean", "Median", "Variance", "SD", "SE", "CI"].
  * **ci_level** : The confidence interval to be calculated; the default is 0.95, i.e., 95% confidence interval.
  * **decimals** : The rounding to be applied to the data; the default is 4.
  * **return_type** : The data structure to be returned; available options are "Dataframe" or "Dictionary" with the default being "Dataframe".

Returns
-------
Pandas DataFrame or Python dictionary object containing the univariate descriptive statistics.


Examples
========

Loading Packages and Data
-------------------------
First to load required libraries for this example. Below, an example data set will be loaded
in using researchpy.datasets; the data loaded in is a data set available through Stata
called 'auto'.

.. code:: python

 import researchpy as rp
 import pandas as pd

 auto = rp.datasets.auto()
 auto.info()


.. parsed-literal::

  <class 'pandas.core.frame.DataFrame'>
  Int64Index: 74 entries, 0 to 73
  Data columns (total 12 columns):
  #   Column        Non-Null Count  Dtype
  ---  ------        --------------  -----
  0   make          74 non-null     object
  1   price         74 non-null     int16
  2   mpg           74 non-null     int16
  3   rep78         69 non-null     float64
  4   headroom      74 non-null     float32
  5   trunk         74 non-null     int16
  6   weight        74 non-null     int16
  7   length        74 non-null     int16
  8   turn          74 non-null     int16
  9   displacement  74 non-null     int16
  10  gear_ratio    74 non-null     float32
  11  foreign       74 non-null     category
  dtypes: category(1), float32(2), float64(1), int16(7), object(1)
  memory usage: 3.5+ KB



Single Variable
---------------
First demonstration will show how to get descriptive statistics for a single variable.

.. code:: python

  rp.summarize(auto.price)

.. raw:: html

  <div style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>Name</th>      <th>N</th>      <th>Mean</th>      <th>Median</th>      <th>Variance</th>      <th>SD</th>      <th>SE</th>      <th>95% Conf. Interval</th>    </tr>  </thead>  <tbody>    <tr>      <td>price</td>      <td>74</td>      <td>6,165.2568</td>      <td>5,006.5000</td>      <td>8,699,525.9743</td>      <td>2,949.4959</td>      <td>342.8719</td>      <td>[5481.914, 6848.5995]</td>    </tr>  </tbody></table>
  </div>


Two Variables
-------------
Now let's get information from 2 variables at the same time.

.. code:: python

  rp.summarize(auto[["price", "mpg"]])

.. raw:: html

  <div style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>Name</th>      <th>N</th>      <th>Mean</th>      <th>Median</th>      <th>Variance</th>      <th>SD</th>      <th>SE</th>      <th>95% Conf. Interval</th>    </tr>  </thead>  <tbody>    <tr>      <td>price</td>      <td>74</td>      <td>6,165.2568</td>      <td>5,006.5000</td>      <td>8,699,525.9743</td>      <td>2,949.4959</td>      <td>342.8719</td>      <td>[5481.914, 6848.5995]</td>    </tr>    <tr>      <td>mpg</td>      <td>74</td>      <td>21.2973</td>      <td>20.0000</td>      <td>33.4720</td>      <td>5.7855</td>      <td>0.6726</td>      <td>[19.9569, 22.6377]</td>    </tr>  </tbody></table>
  </div>


Pandas Groupby Objects
----------------------
This method also supports calculations for Pandas Series and Pandas DataFrame Groupby objects.


Pandas Series Groupby Object
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

  rp.summarize(auto.groupby("foreign")["price"])


.. raw:: html

  <div style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>foreign</th>      <th>N</th>      <th>Mean</th>      <th>Median</th>      <th>Variance</th>      <th>SD</th>      <th>SE</th>      <th>95% Conf. Interval</th>    </tr>  </thead>  <tbody>    <tr>      <td>Domestic</td>      <td>52</td>      <td>6,072.4231</td>      <td>4,782.5000</td>      <td>9,592,054.9155</td>      <td>3,097.1043</td>      <td>429.4911</td>      <td>[5210.1837, 6934.6624]</td>    </tr>    <tr>      <td>Foreign</td>      <td>22</td>      <td>6,384.6818</td>      <td>5,759.0000</td>      <td>6,874,438.7035</td>      <td>2,621.9151</td>      <td>558.9942</td>      <td>[5222.1898, 7547.1738]</td>    </tr>  </tbody></table>
  </div>


Pandas Dataframe Groupby Object
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: python

  rp.summarize(auto.groupby(["foreign"])[["price", "mpg"]])

.. raw:: html

  <div style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr>      <th>foreign</th>      <th colspan="7" halign="left">price</th>      <th colspan="7" halign="left">mpg</th>    </tr>    <tr>      <th></th>      <th>N</th>      <th>Mean</th>      <th>Median</th>      <th>Variance</th>      <th>SD</th>      <th>SE</th>      <th>95% Conf. Interval</th>      <th>N</th>      <th>Mean</th>      <th>Median</th>      <th>Variance</th>      <th>SD</th>      <th>SE</th>      <th>95% Conf. Interval</th>    </tr>  </thead>  <tbody>    <tr>      <td>Domestic</td>      <td>52</td>      <td>6,072.4231</td>      <td>4,782.5000</td>      <td>9,592,054.9155</td>      <td>3,097.1043</td>      <td>429.4911</td>      <td>[5210.1837, 6934.6624]</td>      <td>52</td>      <td>19.8269</td>      <td>19.0000</td>      <td>22.4989</td>      <td>4.7433</td>      <td>0.6578</td>      <td>[18.5064, 21.1475]</td>    </tr>    <tr>      <td>Foreign</td>      <td>22</td>      <td>6,384.6818</td>      <td>5,759.0000</td>      <td>6,874,438.7035</td>      <td>2,621.9151</td>      <td>558.9942</td>      <td>[5222.1898, 7547.1738]</td>      <td>22</td>      <td>24.7727</td>      <td>24.5000</td>      <td>43.7078</td>      <td>6.6112</td>      <td>1.4095</td>      <td>[21.8415, 27.704]</td>    </tr>  </tbody></table>
  </div>
