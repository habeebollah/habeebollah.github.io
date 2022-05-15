# Panduan montiR

Berikut adalah contoh sederhana penggunaan montiR

```{r example}
library(montiR)
## membuat input data
df <- data.frame(tahun=c(...),
                 tangkapan=c(...),
                 upaya=c(...))


## Surplus Production dengan asumsi equilibrium
                 
## melihat bentuk data
plotInit(df=df)

## mencari pilihan parameter awal sebagai input untuk estimasi
K <- 1000
B0 <- K
r <- 0.3
q <- 0.00025

inpars <- c(log(K), log(B0), log(r), log(q))
SPparS(inpars=inpars, df=goodcontrast)
```


page ini berisi panduan untuk menggunakan montiR
