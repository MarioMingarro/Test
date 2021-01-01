Untitled
================

``` r
library(raster)
library(tidyverse)
library(rasterVis)
library(ggpubr)
library(microclima)
library(magrittr)
library(sf)
library(rgdal)
library(rgl)
library(magick)
library(greenbrown)
library(rayshader)
```

## Generar modelo digital de elevaciones, pendientes y orientaciones

``` r
C_1x1KM <- readOGR("T:/MICROCLIMA/CHANGES/Cuadriculas_seleccionadas.shp") %>%
  spTransform(CRS("+proj=merc +lon_0=0 +k=1 +x_0=0 +y_0=0 +datum=WGS84 +units=m +no_defs"))
```

    ## OGR data source with driver: ESRI Shapefile 
    ## Source: "T:\MICROCLIMA\CHANGES\Cuadriculas_seleccionadas.shp", layer: "Cuadriculas_seleccionadas"
    ## with 4 features
    ## It has 5 fields

``` r
centr <- st_as_sf(C_1x1KM) %>%
  st_centroid()%>%
  as_Spatial()%>%
  spTransform(CRS("+proj=merc +lon_0=0 +k=1 +x_0=0 +y_0=0 +datum=WGS84 +units=m +no_defs"))

lat_long <- coordinates(spTransform(centr, CRS("+proj=longlat + datum=WGS84")))
lat_comp <- lat_long[,2]
long_comp <- lat_long[,1]

Nombres<- c("30TUK15B","30TVL11C","30TXK17D","30TXK64D")


for (j in 1:4){
  lat <- lat_comp[j]
  long <- long_comp[j]
  assign(paste("MDT_" ,Nombres[j], sep = ""),microclima::get_dem(lat = lat, long = long, resolution = 30))
}

  PENDIENTE_30TUK15B <- terrain(MDT_30TUK15B, opt = "slope", unit = "degrees", neighbors = 8)
  PENDIENTE_30TVL11C <- terrain(MDT_30TVL11C, opt = "slope", unit = "degrees", neighbors = 8)
  PENDIENTE_30TXK17D <- terrain(MDT_30TXK17D, opt = "slope", unit = "degrees", neighbors = 8)
  PENDIENTE_30TXK64D <- terrain(MDT_30TXK64D, opt = "slope", unit = "degrees", neighbors = 8)
  
  ORIENTACION_30TUK15B <- terrain(MDT_30TUK15B, opt = "aspect", unit = "degrees", neighbors = 8)
  ORIENTACION_30TVL11C <- terrain(MDT_30TVL11C, opt = "aspect", unit = "degrees", neighbors = 8)
  ORIENTACION_30TXK17D <- terrain(MDT_30TXK17D, opt = "aspect", unit = "degrees", neighbors = 8)
  ORIENTACION_30TXK64D <- terrain(MDT_30TXK64D, opt = "aspect", unit = "degrees", neighbors = 8)
```

### PENDIENTE

``` r
plot(PENDIENTE_30TUK15B, col=brgr.colors(20))
```

![](tst_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
histogram(PENDIENTE_30TUK15B,
          xlab = "Pendiente (%)", ylab= "Frecuencia", col="green")
```

![](tst_files/figure-gfm/unnamed-chunk-3-2.png)<!-- -->

### ORIENTACIÓN

``` r
plot(ORIENTACION_30TUK15B, col=brgr.colors(20))
```

![](tst_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
histogram(ORIENTACION_30TUK15B,
          xlab = "orientación (0º-360º)", ylab= "Frecuencia", col="green")
```

![](tst_files/figure-gfm/unnamed-chunk-4-2.png)<!-- -->
