**********
crosstab()
**********

Description
===========
Returns up to 3 DataFrames depending on what desired. Can calculate row, column,
or cell percentages if requested. Otherwise, counts are returned as the default.

DataFrame 1 is always the crosstabulation results, the other 2 DataFrames
returned depends on the options selected which is determined by the arguments
*test* and *expected_freqs*. If all 3 options are returned, then the order
of the returned DataFrames is as follows: crosstabulation results, :math:`\chi^2`
test results with effect size of Cramer's Phi or V depending on size of table, and
the expected frequency table.



Parameters
==========

Input
------
**crosstab(group1, group2, prop= None, test = False, margins= True,
correction = None, cramer_correction = None, exact = False, expected_freqs= False)**

  * **group1** and **group2**, requires the data to be a Pandas Series
  * **prop**, can either be 'row', 'col', or 'cell'. 'row' will calculate
    the row percentages, 'column' will calculate the column percentages, and 'cell'
    will calculate the cell percentage based on the entire sample
  * **test**, can take "chi-square", "g-test", "mcnemar", or "fisher".

      * If "chi-square", the chi-square (:math:`\chi^2`) test of independence :footcite:p:`scipy_chi2` will
        be calculated and returned in a second DataFrame.
      * If "g-test", will conduct the G-test (likelihood-ratio :math:`\chi^2`) :footcite:p:`scipy_chi2` and
        the results will be returned in a second DataFrame.
      * If "fisher", will conduct Fisher's exact test :footcite:p:`scipy_fisher`.
      * If "mcnemar", will conduct the McNemar :math:`\chi^2` :footcite:p:`statsmodels_mcnemar` test for paired
        nominal data.

  * **margins**, if False will return a crosstabulation table without the total
    counts for each group. This argument is only supported for counts; the margins
    will always be returned for the percentages
  * **correction**, if True, applies the Yates' correction for continuity. Valid
    argument for *chi-square"*, *"g-test"*, and *"mcnemar"*.
  * **cramer_correction**, if True, applies the bias correction developed by Tschuprow (1925) to Cramer's V.
  * **exact**, is only a valid option for when the *"mcnemar"* test is selected. In that
    case, *exact = True* will then the binomal distribution will be used. If false
    (default), the :math:`\chi^2` distribution is used.
  * **expected_freqs**, if True, will return a DataFrame that contains the
    expected counts for each cell. Not a valid argurment for *mcnemar* test.

Returns
--------

Up to 3 Pandas DataFrames will be returned within a tuple:

      * First DataFrame is always the crosstab table with either the counts,
        cell, row, or column percentages
      * Second DataFrame is either the test results or the expected frequencies.
        If a test is selected and expected frequencies are desired, the second
        DataFrame will be the test results; otherwise, if just expected frequencies
        are desired, the second DataFrame will be that and there will not be a
        third DataFrame returned.
      * Third DataFrame is always the expected frequencies



.. note:: If conducting a McNemar test, make sure the outcomes in both variables
  are labelled the same.



Effect Size Measures Formulas
=============================
.. note::
  If adjusted :math:`\chi^2` values are used in the test's calculation, then those
  adjusted :math:`\chi^2` values are also used to calculate effect size.

Cramer's Phi (2x2 table)
------------------------
For analyses were it's a 2x2 table, the following formula is used to
calculate Cramer's Phi (:math:`\phi`) :footcite:p:`cramer2016`:

.. math::
  \phi = \sqrt{\frac{\chi^2}{N}}

Where N = total number of observations in the analysis



Cramer's V (RxC where R or C > 2)
---------------------------------
For analyses were it's a table that is larger than a 2x2, the
following formula is used to calculate Cramer's V :footcite:p:`cramer2016`:

.. math::
  V = \sqrt{\frac{\chi^2}{(N*(k - 1))}}

Where K is the number of categories for either R or C (whichever has fewer
categories)

.. math::
 \tilde{V} = \sqrt\frac{\tilde{\phi}^2}{\text{min}(\tilde{r} - 1, \tilde{c} - 1)}

Where r is the number of rows and c is the number of columns, and

.. math::
 \tilde{\phi}^2 = \text{max}(0, \frac{\chi^2}{n} - \frac{(c - 1)(r - 1)}{n - 1}) \\
 \tilde{c} = c - \frac{(c - 1)^2}{n - 1} \\
 \tilde{r} = r - \frac{(r - 1)^2}{n - 1}


Examples
========

Loading Packages and Data
-------------------------
.. code:: python

    import researchpy, pandas, numpy

    numpy.random.seed(123)

    df = pandas.DataFrame(numpy.random.randint(3, size= (101, 3)),
                      columns= ['disease', 'severity', 'alive'])

    df.head()

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>disease</th>
          <th>severity</th>
          <th>alive</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>2</td>
          <td>1</td>
          <td>2</td>
        </tr>
        <tr>
          <th>1</th>
          <td>2</td>
          <td>0</td>
          <td>2</td>
        </tr>
        <tr>
          <th>2</th>
          <td>2</td>
          <td>1</td>
          <td>2</td>
        </tr>
        <tr>
          <th>3</th>
          <td>1</td>
          <td>2</td>
          <td>1</td>
        </tr>
        <tr>
          <th>4</th>
          <td>0</td>
          <td>1</td>
          <td>2</td>
        </tr>
      </tbody>
    </table>
    </div>


Crosstabulation with Frequency
------------------------------

.. code:: python

    # If only two Series are passed it will output a crosstabulation with margin totals.
    # This is the same as pandas.crosstab(), except for researchpy.crosstab() returns
    # a table with hierarchical indexing for better exporting format style.

    researchpy.crosstab(df['disease'], df['alive'])

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">
      <thead>
        <tr>
          <th></th>
          <th colspan="4" halign="left">alive</th>
        </tr>
        <tr>
          <th></th>
          <th>0</th>
          <th>1</th>
          <th>2</th>
          <th>All</th>
        </tr>
        <tr>
          <th>disease</th>
          <th></th>
          <th></th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>9</td>
          <td>14</td>
          <td>7</td>
          <td>30</td>
        </tr>
        <tr>
          <th>1</th>
          <td>7</td>
          <td>9</td>
          <td>15</td>
          <td>31</td>
        </tr>
        <tr>
          <th>2</th>
          <td>7</td>
          <td>17</td>
          <td>16</td>
          <td>40</td>
        </tr>
        <tr>
          <th>All</th>
          <td>23</td>
          <td>40</td>
          <td>38</td>
          <td>101</td>
        </tr>
      </tbody>
    </table>
    </div>


Crosstabulation with Cell Percentages
-------------------------------------
Cell percentages are calculated by taking the frequency of the cell and dividing it by the total N.
For example, the cell proportion for :math:`\text{disease}_0` and :math:`\text{alive}_0` = :math:`\frac{9}{101}`.

.. code:: python

    crosstab = researchpy.crosstab(df['disease'], df['alive'], prop= "cell")
    crosstab

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">
      <thead>
        <tr>
          <th></th>
          <th colspan="4" halign="left">alive</th>
        </tr>
        <tr>
          <th></th>
          <th>0</th>
          <th>1</th>
          <th>2</th>
          <th>All</th>
        </tr>
        <tr>
          <th>disease</th>
          <th></th>
          <th></th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>8.91</td>
          <td>13.86</td>
          <td>6.93</td>
          <td>29.70</td>
        </tr>
        <tr>
          <th>1</th>
          <td>6.93</td>
          <td>8.91</td>
          <td>14.85</td>
          <td>30.69</td>
        </tr>
        <tr>
          <th>2</th>
          <td>6.93</td>
          <td>16.83</td>
          <td>15.84</td>
          <td>39.60</td>
        </tr>
        <tr>
          <th>All</th>
          <td>22.77</td>
          <td>39.60</td>
          <td>37.62</td>
          <td>100.00</td>
        </tr>
      </tbody>
    </table>
    </div>


Crosstabulation with Row Percentages
-------------------------------------
.. code:: python

    crosstab = researchpy.crosstab(df['disease'], df['alive'], prop= "row")
    crosstab

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">
      <thead>
        <tr>
          <th></th>
          <th colspan="4" halign="left">alive</th>
        </tr>
        <tr>
          <th></th>
          <th>0</th>
          <th>1</th>
          <th>2</th>
          <th>All</th>
        </tr>
        <tr>
          <th>disease</th>
          <th></th>
          <th></th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>30.00</td>
          <td>46.67</td>
          <td>23.33</td>
          <td>100.0</td>
        </tr>
        <tr>
          <th>1</th>
          <td>22.58</td>
          <td>29.03</td>
          <td>48.39</td>
          <td>100.0</td>
        </tr>
        <tr>
          <th>2</th>
          <td>17.50</td>
          <td>42.50</td>
          <td>40.00</td>
          <td>100.0</td>
        </tr>
        <tr>
          <th>All</th>
          <td>22.77</td>
          <td>39.60</td>
          <td>37.62</td>
          <td>100.0</td>
        </tr>
      </tbody>
    </table>
    </div>


Crosstabulation with Column Percentages
---------------------------------------

.. code:: python

    crosstab = researchpy.crosstab(df['disease'], df['alive'], prop= "col")
    crosstab

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">
      <thead>
        <tr>
          <th></th>
          <th colspan="4" halign="left">alive</th>
        </tr>
        <tr>
          <th></th>
          <th>0</th>
          <th>1</th>
          <th>2</th>
          <th>All</th>
        </tr>
        <tr>
          <th>disease</th>
          <th></th>
          <th></th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>39.13</td>
          <td>35.0</td>
          <td>18.42</td>
          <td>29.70</td>
        </tr>
        <tr>
          <th>1</th>
          <td>30.43</td>
          <td>22.5</td>
          <td>39.47</td>
          <td>30.69</td>
        </tr>
        <tr>
          <th>2</th>
          <td>30.43</td>
          <td>42.5</td>
          <td>42.11</td>
          <td>39.60</td>
        </tr>
        <tr>
          <th>All</th>
          <td>100.00</td>
          <td>100.0</td>
          <td>100.00</td>
          <td>100.00</td>
        </tr>
      </tbody>
    </table>
    </div>


Chi Squared (:math:`\chi^2`) Test of Independence
--------------------------------------------------

.. code:: python

    # To conduct a Chi-square test of independence, pass "chi-square" in the "test =" argument.
    # This will also output an effect size; either Cramer's Phi if it a 2x2 table, or
    # Cramer's V is larger than 2x2.

    # This will return 2 DataFrames as a tuple, 1 with the crosstabulation and the other with the
    # test results. It's rather ugly, the recommended way to output is in the next example

    researchpy.crosstab(df['disease'], df['alive'], test= "chi-square")

.. parsed-literal::

    (        alive
                 0   1   2  All
     disease
     0           9  14   7   30
     1           7   9  15   31
     2           7  17  16   40
     All        23  40  38  101,                 Chi-square test  results
     0  Pearson Chi-square ( 4.0) =    5.1573
     1                    p-value =    0.2715
     2                 Cramer's V =    0.1598)



.. code:: python

    # To clean up the output, assign each DataFrame to an object. This allows
    # for a cleaner view and each DataFrame to be exported

    crosstab, res = researchpy.crosstab(df['disease'], df['alive'], test= "chi-square")

    crosstab

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">
      <thead>
        <tr>
          <th></th>
          <th colspan="4" halign="left">alive</th>
        </tr>
        <tr>
          <th></th>
          <th>0</th>
          <th>1</th>
          <th>2</th>
          <th>All</th>
        </tr>
        <tr>
          <th>disease</th>
          <th></th>
          <th></th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>9</td>
          <td>14</td>
          <td>7</td>
          <td>30</td>
        </tr>
        <tr>
          <th>1</th>
          <td>7</td>
          <td>9</td>
          <td>15</td>
          <td>31</td>
        </tr>
        <tr>
          <th>2</th>
          <td>7</td>
          <td>17</td>
          <td>16</td>
          <td>40</td>
        </tr>
        <tr>
          <th>All</th>
          <td>23</td>
          <td>40</td>
          <td>38</td>
          <td>101</td>
        </tr>
      </tbody>
    </table>
    </div>



.. code:: python

    res

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>Chi-square test</th>
          <th>results</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>Pearson Chi-square ( 4.0) =</td>
          <td>5.1573</td>
        </tr>
        <tr>
          <th>1</th>
          <td>p-value =</td>
          <td>0.2715</td>
        </tr>
        <tr>
          <th>2</th>
          <td>Cramer's V =</td>
          <td>0.1598</td>
        </tr>
      </tbody>
    </table>
    </div>



.. code:: python

    # To get the expected frequencies, pass "True" in "expected_freqs="

    crosstab, res, expected = researchpy.crosstab(df['disease'], df['alive'], test= "chi-square", expected_freqs= True)

    expected

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">
      <thead>
        <tr>
          <th></th>
          <th colspan="3" halign="left">alive</th>
        </tr>
        <tr>
          <th></th>
          <th>0</th>
          <th>1</th>
          <th>2</th>
        </tr>
        <tr>
          <th>disease</th>
          <th></th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>6.831683</td>
          <td>11.881188</td>
          <td>11.287129</td>
        </tr>
        <tr>
          <th>1</th>
          <td>7.059406</td>
          <td>12.277228</td>
          <td>11.663366</td>
        </tr>
        <tr>
          <th>2</th>
          <td>9.108911</td>
          <td>15.841584</td>
          <td>15.049505</td>
        </tr>
      </tbody>
    </table>
    </div>


G-test
--------
.. code:: python

    crosstab, res = researchpy.crosstab(df['disease'], df['alive'], test= "g-test")
    res

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>G-test</th>
          <th>results</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>Log-likelihood ratio ( 4.0) =</td>
          <td>5.3808</td>
        </tr>
        <tr>
          <th>1</th>
          <td>p-value =</td>
          <td>0.2504</td>
        </tr>
        <tr>
          <th>2</th>
          <td>Cramer's V =</td>
          <td>0.3264</td>
        </tr>
      </tbody>
    </table>
    </div>


Fisher's Exact test
-------------------
.. code:: python

    # Need 2x2 data for Fisher's test.
    numpy.random.seed(345)

    df = pandas.DataFrame(numpy.random.randint(2, size= (90, 2)),
                          columns= ['tx', 'cured'])

    crosstab, res = researchpy.crosstab(df['tx'], df['cured'], test= "fisher")

    crosstab

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">
      <thead>
        <tr>
          <th></th>
          <th colspan="3" halign="left">cured</th>
        </tr>
        <tr>
          <th></th>
          <th>0</th>
          <th>1</th>
          <th>All</th>
        </tr>
        <tr>
          <th>tx</th>
          <th></th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>25</td>
          <td>17</td>
          <td>42</td>
        </tr>
        <tr>
          <th>1</th>
          <td>20</td>
          <td>28</td>
          <td>48</td>
        </tr>
        <tr>
          <th>All</th>
          <td>45</td>
          <td>45</td>
          <td>90</td>
        </tr>
      </tbody>
    </table>
    </div>



.. code:: python

    res

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>Fisher's exact test</th>
          <th>results</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>Odds ratio =</td>
          <td>2.0588</td>
        </tr>
        <tr>
          <th>1</th>
          <td>2 sided p-value =</td>
          <td>0.1387</td>
        </tr>
        <tr>
          <th>2</th>
          <td>Left tail p-value =</td>
          <td>0.9717</td>
        </tr>
        <tr>
          <th>3</th>
          <td>Right tail p-value =</td>
          <td>0.0694</td>
        </tr>
        <tr>
          <th>4</th>
          <td>Cramer's phi =</td>
          <td>0.1782</td>
        </tr>
      </tbody>
    </table>
    </div>


McNemar test
-------------
Make sure that the outcomes are labelled the same in both variables.

.. code:: python

    numpy.random.seed(345)

    df = pandas.DataFrame(numpy.random.randint(2, size= (90, 2)),
                          columns= ['time1', 'time2'])

    crosstab, res = researchpy.crosstab(df['time1'], df['time2'], test= "mcnemar")

    crosstab

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">
      <thead>
        <tr>
          <th></th>
          <th colspan="3" halign="left">time2</th>
        </tr>
        <tr>
          <th></th>
          <th>0</th>
          <th>1</th>
          <th>All</th>
        </tr>
        <tr>
          <th>time1</th>
          <th></th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>25</td>
          <td>17</td>
          <td>42</td>
        </tr>
        <tr>
          <th>1</th>
          <td>20</td>
          <td>28</td>
          <td>48</td>
        </tr>
        <tr>
          <th>All</th>
          <td>45</td>
          <td>45</td>
          <td>90</td>
        </tr>
      </tbody>
    </table>
    </div>



.. code:: python

    res

.. raw:: html

    <div style="overflow-x: auto;">
    <table class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>McNemar</th>
          <th>results</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>0</th>
          <td>McNemar's Chi-square ( 1.0) =</td>
          <td>0.2432</td>
        </tr>
        <tr>
          <th>1</th>
          <td>p-value =</td>
          <td>0.6219</td>
        </tr>
        <tr>
          <th>2</th>
          <td>Cramer's phi =</td>
          <td>0.0520</td>
        </tr>
      </tbody>
    </table>
    </div>








References
==========

.. footbibliography::
