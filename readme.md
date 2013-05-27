
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

## Example session

```r
# example metadata
# from help(lm)
ctl <- c(4.17,5.58,5.18,6.11,4.50,4.61,5.17,4.53,5.33,5.14)
trt <- c(4.81,4.17,4.41,3.59,5.87,3.83,6.03,4.89,4.32,4.69)
group <- gl(2,10,20, labels=c("Ctl","Trt"))
weight <- c(ctl, trt)
lm.D9 <- lm(weight ~ group)
lm.D9$meta <- list(dataset   = "Annette Dobson (1990) An Introduction to Generalized Linear Models. Page 9: Plant Weight Data.",
				   modeltype = class(lm.D9),
			          depvar = "plant weight measured in grams",
		          	 expvars = list(varname = c("Intercept","treatment"),metadata = c("Intercept","group indicator of whether treated with substance XYZ")))

# example for meta slot object
# extract() needs to make a call like that
myTexreg <- createTexreg(coef.names=c("(Intercept)","group"),
			 coef=coef(lm.D9),
			 se=coef(summary(lm.D9))[,2],
			 meta=lm.D9$meta)
```

Here is the print method of the upgraded texreg object:

```r
print(myTexreg)


No p-values were defined for this texreg object.
No decimal places were defined for the GOF statistics.

             coef.      s.e.
(Intercept)  5.032 0.2202177
group       -0.371 0.3114349

     GOF


Meta Data for this object
=========================

          model part                                                                                       metadata
1            dataset Annette Dobson (1990) An Introduction to Generalized Linear Models. Page 9: Plant Weight Data.
2         model type                                                                                             lm
3 dependent variable                                                                 plant weight measured in grams
4        (Intercept)                                                                                      Intercept
5              group                                          group indicator of whether treated with substance XYZ
```

and here is the resulting JSON string. As I said, this will have to be changed to fit into metametrik. Also, there are many packages that write R to JSON, so the one I'm using may not be the best one.

```r
[1] "{\"dataset\":\"Annette Dobson (1990) An Introduction to Generalized Linear Models. Page 9: Plant Weight Data.\",\"modeltype\":\"lm\",\"depvar\":\"plant weight measured in grams\",\"expvars\":{\"varname\":[\"Intercept\",\"treatment\"],\"metadata\":[\"Intercept\",\"group indicator of whether treated with substance XYZ\"],\"value\":{\"(Intercept)\":5.032,\"groupTrt\":-0.371},\"se\":{\"(Intercept)\":0.220217695322908,\"groupTrt\":0.311434851400203}},\"gof\":{\"gname\":[\"R$^2$\",\"Adj. R$^2$\",\"Num. obs.\"],\"gval\":[0.0730775989903856,0.021581910045407,20]}}"
```
