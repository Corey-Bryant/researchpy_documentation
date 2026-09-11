*************
signrank()
*************

Description
===========
Conducts the Wilcoxon signed-ranks test for paired-sample data. Data can be entered using the formula_like structure,
or by passing two array like structures. How to use both of these approaches will be
demonstrated. The results can be returned as Pandas DataFrame object (default) or as a Python
dictionary object.

The data model is passed to *signrank* and then the *conduct* method needs to be applied. This
method returns 3 data objects within a tuple.



Parameters
==========

Input
-----
**signrank(formula_like = None, data = {}, group1 = None, group2 = None, zero_method = "pratt", correction = False, mode = "auto")**

  * **formula_like** : A valid `formula <https://patsy.readthedocs.io/en/latest/formulas.html>`_ which will parse the data into a design matrix.
  * **data** : The dataframe which contains the data to be analyzed; required if using *formula_like*.
  * **group1** : The array like object which contains data for the paired-sample.
  * **group2** : The array like object which contains data for the paired-sample.
  * **zero_method** : How to handle the zero-differences in the ranking process. Available options are (see `scipy.stats.wilcoxon <https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.wilcoxon.html#scipy.stats.wilcoxon>`_):

    * *"pratt"* : Includes zero-differences in the ranking process, but drops the ranks of the zeros (default).
    * *"wilcox"* : Discards all zero-differences.

  * **correction** : Boolean value indicating if the continuity correction should be applied; see `scipy.stats.wilcoxon <https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.wilcoxon.html#scipy.stats.wilcoxon>`_ for more information.
  * **mode** : Method to calculate the p-value, see `scipy.stats.wilcoxon <https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.wilcoxon.html#scipy.stats.wilcoxon>`_ for more information. Options are:

    * *"auto"* : Use the exact distribution if there are no more than 25 observations and no ties, otherwise a normal approximation will be used (default).
    * *"exact"* : Use the exact distribution, can be used if there are no more than 25 observations and no ties.
    * *"approx"* : Use a normal approximation.


Returns
-------
Returns an object with class "signrank"; this object has an accessible method which is described below.

signrank methods
^^^^^^^^^^^^^^^^

  * **conduct(return_type = "Dataframe", effect_size = [])**

      * **return_type** : The type of data structure the results should be returned as. Supported options are 'Dataframe' which will return a Pandas DataFrame or 'Dictionary' which will return a dictionary.
      * **effect_size** : A list object which indicates which effect size measures should be calculated. Available options are:
        * *pd* : Calculates the Rank-Biserial r coefficient.
        * *pearson* : Calculates the Pearson r coefficient.

      After using the conduct method three objects will be returned within a tuple. The first object
      provides descriptive information regarding the ranks, the second object contains the adjustment information,
      and the third object contains the test results.



Effect size measures formulas
=============================
By default no effect size measures are calculated; Rank-Biserial r calculation is
from Kerby (2012) :footcite:p:`Kerby2012` while the Pearson r calculation is from Fritz, Morris, and Richler (2012) :footcite:p:`Fritz_Morris_Richler2012`

Rank-Biserial r
------------------
.. math::

  \text{Rank-Biserial r = } \frac{\sum{Ranks}_{+} - \sum{Ranks}_{-}}{\sum{Ranks}_{total}}

Pearson r
------------
.. math::

  \text{Pearson r = } \frac{Z}{\sqrt{N}}

Where N is the total number of observations included in the model.



Examples
========

Loading Packages and Data
-------------------------
First to load required libraries for this example. Below, an example data set will be loaded
in using researchpy.datasets; the data loaded in is a data set available through Stata
called 'fuel'.

.. code:: python

 import researchpy as rp
 import pandas as pd

 fuel = rp.datasets.fuel()
 fuel["id"] = range(1, fuel.shape[0] + 1)
 fuel.info()

.. raw:: html

  <div style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>mpg1</th>      <th>mpg2</th>      <th>id</th>    </tr>  </thead>  <tbody>    <tr>      <td>20.0000</td>      <td>24.0000</td>      <td>1</td>    </tr>    <tr>      <td>23.0000</td>      <td>25.0000</td>      <td>2</td>    </tr>    <tr>      <td>21.0000</td>      <td>21.0000</td>      <td>3</td>    </tr>    <tr>      <td>25.0000</td>      <td>22.0000</td>      <td>4</td>    </tr>    <tr>      <td>18.0000</td>      <td>23.0000</td>      <td>5</td>    </tr>  </tbody></table>
  </div>

The data is currently in a wide structure where each column, mpg1 and mpg2, represent a value for the same ID. This format
is supported by signrank. The long format structure is also supported using the *formula_like* approach, in order to have
the data ready for this demonstration section the transformation will be conducted here.

.. code:: python

  fuel2 = pandas.melt(fuel, id_vars = "id",
                       value_vars = ["mpg1", "mpg2"],
                       var_name = "mpg")

  fuel2.head()

.. raw:: html

  <div  style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>id</th>      <th>mpg</th>      <th>value</th>    </tr>  </thead>  <tbody>    <tr>      <td>1</td>      <td>mpg1</td>      <td>20.0000</td>    </tr>    <tr>      <td>2</td>      <td>mpg1</td>      <td>23.0000</td>    </tr>    <tr>      <td>3</td>      <td>mpg1</td>      <td>21.0000</td>    </tr>    <tr>      <td>4</td>      <td>mpg1</td>      <td>25.0000</td>    </tr>    <tr>      <td>5</td>      <td>mpg1</td>      <td>18.0000</td>    </tr>  </tbody></table>
  </div>


Signrank using Wide Structured Datasets
---------------------------------------
Since the test returns 3 data objects, this demonstration will assign each data object to variable. This is not required, but
it makes the output look cleaner.

.. code:: python

  desc, var_adj, res = signrank(group1 = fuel.mpg1, group2 = fuel.mpg2).conduct()

  print(desc, var_adj, res, sep = "\n"*2)

.. raw:: html

  <div  style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>sign</th>      <th>obs</th>      <th>sum ranks</th>      <th>expected</th>    </tr>  </thead>  <tbody>    <tr>      <td>positive</td>      <td>3</td>      <td>13.5000</td>      <td>38.5000</td>    </tr>    <tr>      <td>negative</td>      <td>8</td>      <td>63.5000</td>      <td>38.5000</td>    </tr>    <tr>      <td>zero</td>      <td>1</td>      <td>1.0000</td>      <td>1.0000</td>    </tr>    <tr>      <td>all</td>      <td>12</td>      <td>78.0000</td>      <td>78.0000</td>    </tr>  </tbody></table>
  </div>

  <div style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>unadjusted variance</th>      <th>adjustment for ties</th>      <th>adjustment for zeros</th>      <th>adjusted variance</th>    </tr>  </thead>  <tbody>    <tr>      <td>162.5000</td>      <td>-1.6250</td>      <td>-0.2500</td>      <td>160.6250</td>    </tr>  </tbody></table>
  </div>

  <div  style="overflow-x: auto;">
  <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>z</th>      <th>w</th>      <th>pval</th>    </tr>  </thead>  <tbody>    <tr>      <td>-1.9726</td>      <td>13.5000</td>      <td>0.0485</td>    </tr>  </tbody></table>
  </div>


If one does not assign each object to a variable, the output is still readable.

.. code:: python

  signrank(group1 = fuel.mpg1, group2 = fuel.mpg2).conduct()

.. raw:: literal

(       sign  obs  sum ranks  expected
0  positive    3    13.5000   38.5000
1  negative    8    63.5000   38.5000
2      zero    1     1.0000    1.0000
3       all   12    78.0000   78.0000,
unadjusted variance  adjustment for ties  adjustment for zeros  adjusted variance
0             162.5000              -1.6250               -0.2500           160.6250,
     z       w   pval
0 -1.9726 13.5000 0.0485)



Signrank using Long Structured Datasets
---------------------------------------

.. code:: python

    desc, var_adj, res = signrank("value ~ C(mpg)", fuel2).conduct()

    print(desc, var_adj, res, sep = "\n"*2)

.. raw:: html

       <div style="overflow-x: auto;">
       <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>sign</th>      <th>obs</th>      <th>sum ranks</th>      <th>expected</th>    </tr>  </thead>  <tbody>    <tr>      <td>positive</td>      <td>3</td>      <td>13.5000</td>      <td>38.5000</td>    </tr>    <tr>      <td>negative</td>      <td>8</td>      <td>63.5000</td>      <td>38.5000</td>    </tr>    <tr>      <td>zero</td>      <td>1</td>      <td>1.0000</td>      <td>1.0000</td>    </tr>    <tr>      <td>all</td>      <td>12</td>      <td>78.0000</td>      <td>78.0000</td>    </tr>  </tbody></table>
       </div>

       <div style="overflow-x: auto;">
       <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>unadjusted variance</th>      <th>adjustment for ties</th>      <th>adjustment for zeros</th>      <th>adjusted variance</th>    </tr>  </thead>  <tbody>    <tr>      <td>162.5000</td>      <td>-1.6250</td>      <td>-0.2500</td>      <td>160.6250</td>    </tr>  </tbody></table>
       </div>

       <div style="overflow-x: auto;">
       <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th>z</th>      <th>w</th>      <th>pval</th>    </tr>  </thead>  <tbody>    <tr>      <td>-1.9726</td>      <td>13.5000</td>      <td>0.0485</td>    </tr>  </tbody></table>
       </div>



References
==========

.. footbibliography::
