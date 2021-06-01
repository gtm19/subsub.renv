# `subsub.renv`

This repo details how `renv` does not properly install packages / recognise the 
`Remote` declaration in the `DESCRIPTION` file for packages in subdirectories of
subdirectories unless they are specified in such a way that `devtools` / native
installation does not support.

Personally, this was an issue for me since my employer uses a [monorepo](https://en.wikipedia.org/wiki/Monorepo), meaning my packages 
cannot be in the top level of a repository, and sometimes have to be nested 
within a subdirectory, rather than at the top level of a subdirectory.

The following steps should recreate the problem

## Clone this repository

```bash

```
