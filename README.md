
[![Travis build
status](https://travis-ci.org/ropensci/arkdb.svg?branch=master)](https://travis-ci.org/ropensci/arkdb)
[![Coverage
status](https://codecov.io/gh/ropensci/arkdb/branch/master/graph/badge.svg)](https://codecov.io/github/ropensci/arkdb?branch=master)
[![AppVeyor Build
Status](https://ci.appveyor.com/api/projects/status/github/cboettig/arkdb?branch=master&svg=true)](https://ci.appveyor.com/project/cboettig/arkdb)
[![CRAN\_Status\_Badge](http://www.r-pkg.org/badges/version/arkdb)](https://cran.r-project.org/package=arkdb)
[![](https://badges.ropensci.org/224_status.svg)](https://github.com/ropensci/onboarding/issues/224)
[![lifecycle](https://img.shields.io/badge/lifecycle-stable-brightgreen.svg)](https://www.tidyverse.org/lifecycle/#stable)

<!-- README.md is generated from README.Rmd. Please edit that file -->

# arkdb

The goal of arkdb is to provide a convienent way to move data from large
compressed text files (tsv, csv, etc) into any DBI-compliant database
connection (e.g. MYSQL, Postgres, SQLite; see
[DBI](https://db.rstudio.com/dbi/)), and move tables out of such
databases into text files. The key feature of arkdb is that files are
moved between databases and text files in chunks of a fixed size,
allowing the package functions to work with tables that would be much to
large to read into memory all at once.

## Links

  - A more detailed introduction to package design and use can be found
    in the package
    [Vignette](https://ropensci.github.io/arkdb/articles/arkdb_intro.html)
  - [Online versions of package
    documentation](https://ropensci.github.io/arkdb)

## Installation

You can install arkdb from GitHub with:

``` r
# install.packages("devtools")
devtools::install_github("cboettig/arkdb")
```

# Basic use

``` r
library(arkdb)

# additional libraries just for this demo
library(dbplyr)
library(dplyr)
library(fs)
```

## Creating an archive of a database

Consider the `nycflights` database in SQLite:

``` r
db <- dbplyr::nycflights13_sqlite(".")
#> Caching nycflights db at ./nycflights13.sqlite
#> Creating table: airlines
#> Creating table: airports
#> Creating table: flights
#> Creating table: planes
#> Creating table: weather
```

Create an archive of the database:

``` r
ark(db, ".", lines = 50000)
#> Exporting airlines in 50000 line chunks:
#>  ...Done! (in 0.02494812 secs)
#> Exporting airports in 50000 line chunks:
#>  ...Done! (in 0.03492808 secs)
#> Exporting flights in 50000 line chunks:
#>  ...Done! (in 11.74483 secs)
#> Exporting planes in 50000 line chunks:
#>  ...Done! (in 0.03923202 secs)
#> Exporting weather in 50000 line chunks:
#>  ...Done! (in 0.8512921 secs)
```

## Unarchive

Import a list of compressed tabular files (i.e. `*.tsv.bz2`) into a
local SQLite database:

``` r
files <- fs::dir_ls(glob = "*.csv.bz2")
new_db <- src_sqlite("local.sqlite", create=TRUE)

unark(files, new_db, lines = 50000)
#> Warning in assert_files_exist(files): no file specified

new_db
#> src:  sqlite 3.22.0 [local.sqlite]
#> tbls:
```

-----

Please note that this project is released with a [Contributor Code of
Conduct](CODE_OF_CONDUCT.md). By participating in this project you agree
to abide by its
terms.

[![ropensci\_footer](https://ropensci.org/public_images/ropensci_footer.png)](https://ropensci.org)
