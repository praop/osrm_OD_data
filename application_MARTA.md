# Application of OSRM to getting shortest route for a list of boarding and deboarding points of public transit system

Clear global environment at any point in time (as needed)
```{r}
rm(list=ls())
```
Installing required libraries
```{r}
install.packages("osrm")
install.packages("cartography")
install.packages("GISTools")
install.packages("rgdal")
```
Loading required packages
```{r}
library(osrm)
library(cartography)
library(GISTools)
library(rgdal)
```
Setting working directory

path: "C:/.../data_folder"
```{r}
setwd("C:/.../data_folder") #edit data_folder and path as per your data structure
getwd()
```
Loading data with geographic information (latitude and longitude)

csv name: MARTA_BusRail_id.csv

df name: marta_rail_bus

unique id for each trip: u_id

origin/boarding latitude column: on_lat

origin longitude/boarding column: on_lon

destination/deboarding latitude column: off_lat

destination/deboarding longitude column: off_lon

```{r}
marta_rail_bus<-read.csv(file="MARTA_BusRail_id.csv",header=TRUE,sep=",") #edit file name as per your needs
```
Finding shortest route path for the data points (output as SpatialLinesDataFrame)
```{r}
route_path <- osrmRoute(src=marta_rail_bus[1, c("u_id","on_lon","on_lat")], #edit column name as per your needs
dst=marta_rail_bus[1, c("u_id","off_lon","off_lat")], #edit column name as per your needs
sp=TRUE,overview="full")
for(i in 2:nrow(marta_rail_bus))
{
route_path <- rbind.SpatialLinesDataFrame (route_try_path, osrmRoute (src=marta_rail_bus [i,
c("u_id","on_lon","on_lat")],
dst=marta_rail_bus[i,c("u_id","off_lon","off_lat")],
sp=TRUE,overview="full"))
Sys.sleep(0.5) #adding time delay between each pull, to ensure no error due to overlapping pull requests
}
```
Writing SpatialLinesDataFrame to shapefile
```{r}
fp=file.path("C:/.../data_folder") #edit data_folder and path as per your data structure
writeOGR(route_path, fp, layer="route", driver="ESRI Shapefile", verbose=TRUE) #edit file name as per your needs
```

