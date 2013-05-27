
# Patched version of R package texreg

this is the development version of a patch for [texreg](http://cran.r-project.org/web/packages/texreg/index.html).

> The texreg package was written by Philip Leifeld.
> http://r-forge.r-project.org/projects/texreg/ 

## what it does

I added a slot `meta` to the main class called **texreg**. **meta** is a list with meta data for the model. the function `JSONreg` patches together regression output and metadata into a JSON string,
to be used in a relational database.

### Look at an example

you need to source each file separately in the order `classDefinition`,`internal`,`extract`,`texreg` into R. (I have not bothered yet to make a proper package out of this).
after sourcing `texreg.r` go back and search for `PATCH` in `texreg.r` to see an example. type `print(myTexreg)` to see an internal representation of the model to be submitted to 
Metametrik. type `Json.string` to see the resulting JSON string. you need to `install.packages(rjson)` to get the `rjson` package.

### how to work with this
do a search for `PATCH` to see where I inserted things.
