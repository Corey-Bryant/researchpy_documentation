About
*****

What ResearchPy Is
==================
ResearchPy is an open-source Python library that is designed to be easy to use and deliver commonly required univariate,
bivariate, and multivariate models and statistical tests with friendly outputs for downstream export and analysis; for
example, summary tables, t-tests, correlations, ANOVA, OLS linear regression, and effect sizes are returned by default
as Pandas DataFrames.

It fills gaps not directly covered by SciPy, NumPy, and Statsmodels, and simplifies use through design principals. With
formulas cited and outputs rigorously tested and cross-checked against Stata, R, SAS, and SPSS. For more information
on the design principles and verification process, see the :doc:`Technical Design Rationale <technical_design_rationale>`.

Authorship and Scope
====================
ResearchPy is developed and maintained by Corey Bryant, a statistician, data scientist, and open-science-focused developer
with roots in academic and government health research and practical data science. It embodies transparent, reproducible,
and methodologically sound statistical practice, emphasizing clarity and accessibility over abstraction
or optimization for niche domains.

Relationship to the Python Statistical Ecosystem
==================================================
Initially designed for univariate and bivariate analysis, the scope of ResearchPy is expanding to include
multivariate and multivariable modeling, and has started to provide multivariate analyses of this expanded scope.
The design principals of ResearchPy are about elegance through the emphasis of ease of use and interpretability, with a
focus on user-friendly outputs and comprehensive documentation. ResearchPy is not intended to be a replacement for other
statistical libraries such as Numpy, SciPy, and Statsmodels. Instead, it is intended to complement them to support
exploratory analysis, statistical checking, reporting, and validation workflows.

The first expansion in scope includes adding linear regression, AN(C)OVA, logistic regression, Poisson regression,
and a general regression method that will allow user to specify the family and link function. Future expansions will be
guided by user needs and the evolving landscape of statistical methods in research.

Readers will find integrated documentation and references to companion libraries throughout to facilitate verification
and further exploration.

Broader Context
=================
ResearchPy is part of a broader effort to advance open, accessible research tools and educational resources. These
efforts are being informally aligned under the name Hearts of Science, an emerging initiative that will eventually serve
as a connective, coherent home for related open-science projects.

ResearchPy remains fully usable and independent. Its functionality does not depend on any existing organizational
infrastructure.