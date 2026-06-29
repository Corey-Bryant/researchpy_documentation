*************
fetch_dta()
*************

Description
===========
Fetch a dataset directly from Stata Press web repository.



Parameters
==========

Input
-----
**fetch_dta(name: str, version: Optional[str] = None, timeout: int = 30) -> pd.DataFrame**

  * **name**: str; Dataset filename without extension (e.g., 'auto', 'nlsw88').
  * **version**: str, optional; Stata version directory (e.g., 'r19', 'r18'). Defaults to 'r19'.
  * **force_download**: bool, default False; If True, re-download even if available locally.
  * **timeout** : int, default 30; Maximum time in seconds to wait for a response from the server.


.. note:: **Fetching Common Datasets**

    Convience wrapper functions for common datasets are provided. These functions are named after the dataset and can
    be called directly without needing to specify the dataset name or version.

    *These functions only accept the **version** parameter*.


Returns
-------
pandas.DataFrame
  * A DataFrame containing the dataset fetched from the Stata Press web repository with appropriate dtypes preserved.


Raises
------
    * **ValueError**: If the dataset name is not found in the specified version directory.
    * **requests.exceptions.RequestException**: If there is an issue with the network request (e.g., timeout, connection error).



Examples
========
.. code:: python

    import researchpy as rp


Get dataset (default latest version)
------------------------------------------
.. code:: python

    import researchpy as rp

    df = rp.datasets.stata_webuse.fetch_dta('auto')

.. raw:: html
    
    <div style="overflow-x: auto;">
    <table class="dataframe">  <thead>    <tr style="text-align: right;">      <th></th>      <th>make</th>      <th>price</th>      <th>mpg</th>      <th>rep78</th>      <th>headroom</th>      <th>trunk</th>      <th>weight</th>      <th>length</th>      <th>turn</th>      <th>displacement</th>      <th>gear_ratio</th>      <th>foreign</th>    </tr>  </thead>  <tbody>    <tr>      <th>0</th>      <td>AMC Concord</td>      <td>4099</td>      <td>22</td>      <td>3.0</td>      <td>2.5</td>      <td>11</td>      <td>2930</td>      <td>186</td>      <td>40</td>      <td>121</td>      <td>3.58</td>      <td>Domestic</td>    </tr>    <tr>      <th>1</th>      <td>AMC Pacer</td>      <td>4749</td>      <td>17</td>      <td>3.0</td>      <td>3.0</td>      <td>11</td>      <td>3350</td>      <td>173</td>      <td>40</td>      <td>258</td>      <td>2.53</td>      <td>Domestic</td>    </tr>    <tr>      <th>2</th>      <td>AMC Spirit</td>      <td>3799</td>      <td>22</td>      <td>NaN</td>      <td>3.0</td>      <td>12</td>      <td>2640</td>      <td>168</td>      <td>35</td>      <td>121</td>      <td>3.08</td>      <td>Domestic</td>    </tr>    <tr>      <th>3</th>      <td>Buick Century</td>      <td>4816</td>      <td>20</td>      <td>3.0</td>      <td>4.5</td>      <td>16</td>      <td>3250</td>      <td>196</td>      <td>40</td>      <td>196</td>      <td>2.93</td>      <td>Domestic</td>    </tr>    <tr>      <th>4</th>      <td>Buick Electra</td>      <td>7827</td>      <td>15</td>      <td>4.0</td>      <td>4.0</td>      <td>20</td>      <td>4080</td>      <td>222</td>      <td>43</td>      <td>350</td>      <td>2.41</td>      <td>Domestic</td>    </tr>  </tbody>
    </table>'
    </div>



Specify dataset version
-------------------------
.. code:: python

    import researchpy as rp

    df_old = rp.datasets.stata_webuse.fetch_dta('auto',version='r15')



Force refresh download from server
-----------------------------------
.. code:: python

    import researchpy as rp

    df_new = rp.datasets.stata_webuse.fetch_dta('auto',force_download=True)



Fetching Common Datasets
-------------------------
.. code:: python

    import researchpy as rp

    auto = rp.datasets.stata_webuse.auto()
    nlsw88 = rp.datasets.stata_webuse.nlsw88()
    systolic = rp.datasets.stata_webuse.systolic()
    lbw = rp.datasets.stata_webuse.lbw()
    census = rp.datasets.stata_webuse.census()
    citytemp = rp.datasets.stata_webuse.citytemp()
    cancer = rp.datasets.stata_webuse.cancer()
    lifeexp = rp.datasets.stata_webuse.lifeexp()
    sp500 = rp.datasets.stata_webuse.sp500()
    uslifeexp = rp.datasets.stata_webuse.uslifeexp()
    voter = rp.datasets.stata_webuse.voter()



References
===========

.. footbibliography::
