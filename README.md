SAS to R guide
==============

Save data to disk
-----------------

```SAS
data save_lib.save_ds;
    set in_lib.in_ds;
run;
```

```r
# Here save_lib is a string representing a directory. '.rda' is sometimes used instead of '.RData'
save(in_lib, file=paste(save_lib, "save_ds.RData", sep=""))
```


`proc freq`
-----------

```SAS
proc freq data=mydata;
    tables myvar / nocol nopercent nocum;
run;
```

```r
table(mydata$myvar)
```


#### sort by frequency ####
```SAS
proc freq order=freq data=mydata;
	tables myvar / nocol nopercent nocum;
run;
```

```r
sort(table(mydata$myvar), decreasing=TRUE)
```


#### with missing ####

```SAS
proc freq data=mydata;
    tables myvar / nocol nopercent nocum missing;
run;
```

```r
table(mydata$myvar, useNA="ifany")  # only displays NA count if positive, to always include use "always"
```

#### percentages, missing & sorted by frequency ####

```SAS
proc freq data=mydata;
    tables myvar / nocol nopercent nocum missing;
run;
```

```r
# no concise way to combine counts w/ percentages core R
counts = sort(table(mydata$myvar, useNA="ifany"), decreasing=True)
counts
prop.table(counts)

# we can do better with dplyr
library(dplyr)
pfreq <- function(...) {
    group_by(...) %>%
        summarise(n=n()) %>%
        mutate(perc=paste0(round(100 * n/sum(n), 1), "%")) %>%
        arrange(desc(n))
}
pfreq(mydata, myvar)
```


`proc means`
------------

```SAS
proc means data=mydata n mean std min max p25 median p75;
    var myvar;
run;
```

```r

```


#### more percentiles ####

```SAS
proc means data=mydata n mean std min max p1 p5 p10 p25 median p75 p90 p95 p99;
	var myvar;
run;
```

```r

```

`proc sort`
-----------

#### find duplicated rows ####

```SAS
proc sort data=mydata NODUPKEY out=_null_ dupout=dups; by _all_; run;
```

```r
dups = duplicated(mydata)
```


#### find duplicated values ####

```SAS
proc sort data=mydata NODUPKEY out=_null_ dupout=dups; by myvar; run;
```

```r
dups = duplicated(mydata$myvar)
```


`data` step
-----------

#### concatenate datasets ####

```SAS
data concatenated;
    set mydata1 mydata2;
run;
```

```r

```


`proc contents`
---------------

```SAS
proc contents data=mydata;
run;
```

```r

```

#### save output ####

```SAS
proc contents noprint data=mydata out=contents;
run;
```

```r

```


Misc
----

#### number of rows in a datastep ####

```SAS
* haha nice try. Try this for size: http://www2.sas.com/proceedings/sugi26/p095-26.pdf;
```

```r
nrow(mydata)  # or NROW(mydata)
```
