---
layout: post
title:  "Worldclim data with R"
date:   2023-10-13 20:05:09 +0800
comments: true
tags:
- R
- Spatial data
---
Worldclim data is a great source of environmental data opensource data.

### R API

Worldclim has set a nice API for R users that want to access worldclim data. The R API allows any user with litte experience with R to quickly acess a large set of raster files containing many environmental data.


### Set raster and vector path

You can raster_path = tempdir() if you want to download the data into a temp folder

```r
raster_path_folder = tempdir() 
vector_output_path = "~/nigeria_adm2"
vector_output_file = paste0(vector_output_path, ".zip")
```

### Load data from worldclim

Use path=tempdir() if you want to download the data to a temp folder

```r
raster_stack <- worldclim_country(country = "NGA",path = raster_path_folder ,version="2.1",res=0.5,var="prec")
crs_proj <- crs(raster_stack)
```

### Download the shapefile

You can download data from the OCHC site. The nigeria data is avaiable [here](https://data.humdata.org/dataset/nigeria-admin-level-2). All we have to do is download and unzip the file : 


```r
download.file("https://data.humdata.org/dataset/aac2a1d6-36c6-4f47-beee-34415742180d/resource/d7011402-0a22-4927-82eb-1359d17ff5cd/download/nigeria_admin_level_2.zip" , destfile=vector_output_file)
unzip(vector_output_file, exdir = vector_output_path)
```

### Read the downloaded shape file

If we want to plot them together, we must be sure that they belong to the same coordinate system. Let's apply the coordinate systems saved previously in `crs_proj`
and apply it to the nigeria admin level boundaries

```r
nigeria_vector_adm2 = read_sf(paste0(vector_output_path,"/","Nigeria_Admin_Level_2.shp"))
nigeria_vector_adm2 <- st_transform(nigeria_vector_adm2, st_crs(raster_stack))
```

Now, since we set the `var` parameter of the `worldclim_country` as `prec`, than we will have 12 raster inside our `raster_stack` variable. All we must do now is to set the plot to 12 subplots, using the `par` function, loop over the raster stack and plot nigeria boundaries.

```r
# Set up plot area
par(mfrow=c(3,4))

# Loop layers and plot
for (ii in 1:length(raster_stack[1])){
  plot(raster_stack[[ii]], main=names(raster_stack[[ii]]))
  plot(st_geometry(nigeria_vector_adm2),bg='transparent', border = "lightblue", add=T)
}
```
 Et voilà the result :

<p align="center">
  <img src="/img/worldclim.png">
</p>


