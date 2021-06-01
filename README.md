# `subsub.renv`

This repo details how `renv` does not properly install packages / recognise the 
`Remote` declaration in the `DESCRIPTION` file for packages in subdirectories of
subdirectories unless they are specified in such a way that `devtools` / native
installation does not support.

Personally, this was an issue for me since my employer uses a [monorepo](https://en.wikipedia.org/wiki/Monorepo), meaning my packages 
cannot be in the top level of a repository, and sometimes have to be nested 
within a subdirectory, rather than at the top level of a subdirectory.

The following steps should highlight the problem

# Clone this repository

Run the following in the terminal

```bash
git clone git@github.com:gtm19/subsub.renv.git
cd subsub.renv
Rscript -e "renv::restore()"
```

# Attempt 1

Run the following in the terminal:

```bash
Rscript -e 'desc::desc_set_remotes("github::gtm19/skeleton:subdir/subsubdir")'
```

or the following in RStudio / R GUI:

```r
desc::desc_set_remotes("github::gtm19/skeleton:subdir/subsubdir")
```

The `Remotes` in the `DESCRIPTION` file will thus be specified as follows:

```
Remotes:
    github::gtm19/skeleton:subdir/subsubdir
```

## Run `renv::restore()`

Either run the following in the terminal

```bash
Rscript -e "renv::restore()"
```

or open RStudio (other GUIs are available) and run:

```r
renv::restore()
```

This should run without incident.

## Try to build / install using `devtools::`

Either run the following in the terminal

```bash
Rscript -e 'devtools::build()' \
        -e 'devtools::install_local("../subsub.renv_0.0.0.9000.tar.gz")'
```

or open RStudio (other GUIs are available) and run:

```r
devtools::build()
devtools::install_local("../subsub.renv_0.0.0.9000.tar.gz")
```

This should return the following error:

```
Error: Failed to install 'subsub.renv' from local:
  HTTP error 404.
  Not Found

  Did you spell the repo owner (`gtm19`) and repo name (`skeleton:subdir`) correctly?
  - If spelling is correct, check that you have the required permissions to access the repo.
Execution halted
```

So we have the situation:

* `renv` ✅
* `devtools` install ❌

# Attempt 2

## Changing the `Remotes::` specification

Next we change the way the `Remotes::` is specified in the `DESCRIPTION` file, 
by running (in the terminal):

```bash
Rscript -e 'desc::desc_set_remotes("github::gtm19/skeleton/subdir/subsubdir")'
```

or, in RStudio / R GUI:
```r
desc::desc_set_remotes("github::gtm19/skeleton/subdir/subsubdir")
```

Now the `Remotes::` specification in the `DESCRIPTION` file looks like this:

```
Remotes:
  github::gtm19/skeleton/subdir/subsubdir
```

## Try to run `renv::install()`

Either run the following 

```bash
Rscript -e "renv::install()"
```

or open RStudio (other GUIs are available) and run:

```r
renv::install()
```

This should throw the following error

```
Error: failed to resolve remote 'github::gtm19/skeleton/subdir/subsubdir' -- failed to retrieve 'https://api.github.com/repos/gtm19/skeleton/subdir' [error code 22]
In addition: Warning message:
curl: (22) The requested URL returned error: 404
```

## Try to build / install using `devtools::`

Either run the following in the terminal

```bash
Rscript -e 'devtools::build()' \
        -e 'devtools::install_local("../subsub.renv_0.0.0.9000.tar.gz")'
```

or open RStudio (other GUIs are available) and run:

```r
devtools::build()
devtools::install_local("../subsub.renv_0.0.0.9000.tar.gz")
```

The installation should be successful.

So we have the situation:

* `renv` ❌
* `devtools` install ✅

WHY NOT BOTH?!
