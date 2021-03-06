# sctransform
## R package for normalization and variance stabilization of single-cell RNA-seq data using regularized negative binomial regression

This package was developed by Christoph Hafemeister in [Rahul Satija's lab](https://satijalab.org/) at the New York Genome Center. Core functionality of this package has been integrated into [Seurat](https://satijalab.org/seurat/), an R package designed for QC, analysis, and exploration of single cell RNA-seq data.

## Quick start
`devtools::install_github(repo = 'ChristophH/sctransform')`  
`normalized_data <- sctransform::vst(umi_count_matrix)$y`

(you can also install from CRAN: `install.packages('sctransform'))`)

## Help
For usage examples see vignettes in inst/doc or use the built-in help after installation  
`?sctransform::vst`  

Available vignettes:  
[Variance stabilizing transformation](https://rawgit.com/ChristophH/sctransform/master/supplement/variance_stabilizing_transformation.html)  
[Using sctransform in Seurat](https://rawgit.com/ChristophH/sctransform/master/supplement/seurat.html)  

## Known Issues

* `error in evaluating the argument 'x' in selecting a method for function 't': missing value where TRUE/FALSE needed` - this is happens when poisson regression fails due to poor initialization (affected methods are poisson, qpoisson, nb_fast). The problem has been fixed in the develop branch. ([issue #71](https://github.com/ChristophH/sctransform/issues/71))
* There can be problems deploying shiny apps due to the `biocViews:` line in the DESCRIPTION file. Fixed in the develop branch. ([issue #72](https://github.com/ChristophH/sctransform/issues/72))
* No residuals when using `method = 'glmGamPoi'` while specifying a `batch_var`. Fixed in the develop branch. ([issue #74](https://github.com/ChristophH/sctransform/issues/74))

To install from the develop branch run `remotes::install_github("ChristophH/sctransform@develop")`

## News
For a detailed change log have a look at the file [NEWS.md](https://github.com/ChristophH/sctransform/blob/master/NEWS.md)

### v0.3.1
This release fixes a performance regression when `sctransform::vst` was called via `do.call`, as is the case in the Seurat wrapper. 

Additionally, model fitting is significantly faster now, because we use a fast Rcpp quasi poisson regression implementation (based on `Rfast` package). This applies to methods `poisson`, `qpoisson` and `nb_fast`.

The `qpoisson` method is new and uses the dispersion parameter from the quasi poisson regression directly to estimate `theta` for the NB model. This can speed up the model fitting step considerably, while giving similar results to the other methods. [This vignette](https://rawgit.com/ChristophH/sctransform/master/supplement/method_comparison.html) compares the methods.

### v0.3
The latest version of `sctransform` now supports the [glmGamPoi](https://github.com/const-ae/glmGamPoi) package to speed up the model fitting step. You can see more about the different methods supported and how they compare in terms of results and speed [in this new vignette](https://rawgit.com/ChristophH/sctransform/master/supplement/method_comparison.html).

Also note that default theta regularization is now based on overdispersion factor (`1 + m / theta` where m is the geometric mean of the observed counts) not `log10(theta)`. The old behavior is still available via `theta_regularization` parameter. You can see how this changes (or doesn't change) the results [in this new vignette](https://rawgit.com/ChristophH/sctransform/master/supplement/theta_regularization.html).


## Reference
Hafemeister, C. & Satija, R. Normalization and variance stabilization of single-cell RNA-seq data using regularized negative binomial regression. Genome Biol 20, 296 (December 23, 2019). [https://doi.org/10.1186/s13059-019-1874-1](https://doi.org/10.1186/s13059-019-1874-1)

An early version of this work was used in the paper [Developmental diversification of cortical inhibitory interneurons, Nature 555, 2018](https://github.com/ChristophH/in-lineage).
