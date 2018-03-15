---
title: Map your Google Location Data with R Shiny
author: James Smythe
date: '2018-01-31'
slug: map-your-google-location-data-with-r-shiny
twitterImg: img/heatmap.png
description: "Visualise your Google location data with our new web-app."
categories: []
tags:
  - R
  - shiny
  - data visualisation
  - mapping
draft: false
---

## I Know What You Vizzed Last Summer

<a href="https://cultureofinsight.shinyapps.io/location_mapper/"><img src="/img/heatmap.png" alt="" style="width: 100%;"></a>
_tl;dr click the image to launch the app_

I guess I’m of that school of thought, I don’t mind my mobile tracking me.

As long as I don’t go breaking the law, or tweeting an ill-advised truth about a politician, it’s unlikely that anyone will be typing the Google Location of my front room into a cruise missile control unit.
But I confess a stirring of nerves when I decided to map my own Google Location data using R’s Leaflet package. Can I be sure I didn’t wander anywhere I shouldn’t have…?

Apart from evident South London phobia, it didn’t reveal any particularly dodgy secrets so safe to share I reckon. It showed up my last couple of holidays in Amsterdam and Barcelona, and my roamings out of London at the weekends. As I use Google Maps as a satnav in the car, the motorways are lit up, and regular haunts are marked with clusters of hotspots.

---

## R can do that...

Inspired by [BrandonMorelli’s](https://twitter.com/BrandonMorelli) [article on codeburst.io](https://codeburst.io/how-i-created-a-heatmap-of-my-location-history-with-javascript-google-maps-972a2d1be240) about mapping his Google Location data using the Google Maps API and JavaScript, I thought it would take it out of the Google family a bit and create an app using R `shiny` and the `leaflet` package, to let other people explore their own location data without having to code anything themselves. With so many big tech companies tracking our every move, we may as have a bit of fun with the data they gather on us! It just requires you to upload your own location data[^1] and the app does the rest for you – mapping a heatmap, or location icons which indicate whether you were moving or stationary.

[The result is here, fill your boots…](https://cultureofinsight.shinyapps.io/location_mapper/)

---

## The Process

For the data curious among you, here’s the tech process.

Your Google location data (along with heaps of other stuff) can be downloaded from [takeout.google.com](https://takeout.google.com). To keep it simple I de-selected everything except Location, and downloaded this as a JSON file (a kind of text file used extensively for data transfer, but relatively easy to read and understand).

Within R, I needed the packages `jsonlite` (to read the JSON into an R data frame), and `leaflet` (to map the data), along with the universally helpful `tidyverse`, `lubridate` and `shiny` packages.

`jsonlite` allows you to read in a JSON file as you would a CSV or any other data type. The result was a list, containing one data frame (`locations`), which in turn contains the eight location variables stored by Google. For the purposes of the map, I decided to keep the variables for latitude, longitude, velocity and time.

```{r}
locationdata <- fromJSON("Location History.json")

myData <- locationdata$locations %>% 
  select(latitudeE7, longitudeE7, `timestampMs`, velocity)
```
---

## Mutating the Google variables to be more viz-friendly

For reasons I didn't tax my brain with, the latitude and logitude co-ordinates are saved in the E7 format, so that needed undoing to create standard GPS co-ordinates for mapping. In R, this is easily adjusted by dividing by `1E7`. Then the timestamp needed converting from UNIX millisecond format (eat my dust, Jack Bauer), stored first as a string, into numbers and then a more readable date/time format.

```{r}
myDataClean <- myData %>%
  mutate(lat = latitudeE7 / 1E7, lon = longitudeE7 / 1E7) %>% 
  mutate(timestampMs = as.numeric(timestampMs)) %>%
  mutate(Date = as.POSIXct(timestampMs/1000, origin="1970-01-01"))
```

---

## Map fun

My first explorations with mapping the data got me curious about whether I'd been driving through the locations mapped, or visiting. The `velocity` variable could help, so I decided to create a factor of either "standing" or "driving" that depended on the velocity being above or below 10. It's crude, as I'm not even sure what the units of `velocity` are, but I reckoned at above 10 whatevers per hour I was probably on wheels. I passed this factor to a new variable called `image`, which would then be referred to by the map to decide which of two icons would be shown at each data point.

```{r}
myDataClean <- myDataClean %>%
  mutate(image = case_when(
        velocity > 10 ~ "drive",
        TRUE ~ "stand")
        ) %>% 
  mutate(image = factor(image, levels = c("drive","stand")))
```

To create these icons for walking or driving, I used the `iconList()` function, and saved two icon PNG files to the working directory.

```{r}
newIcons <- iconList(
      stand = makeIcon("stand.png", "stand.png", 36, 36),
      drive = makeIcon("drive.png", "drive.png", 36, 36)
    )
```

The mapping bit used the `leaflet` package, which makes available a number of different open-source maps and marker options.

The map bounds were set using the max and min lon and lat values from the dataset. I then added two optional marker layers, depending on whether the user chose a heat map or markers.

The `icon` argument in the `addMarkers()` function called up the relevant image from the `newIcons` list I'd created, and also took a `clusterOptions` argument to group and count markers that were too close together to map clearly.

```{r}
myMap <- leaflet(myDataClean) %>% 
  fitBounds(~min(lon), ~min(lat), ~max(lon), ~max(lat)) %>%  
  addHeatmap(lng = ~lon, lat = ~lat, group = "HeatMap", blur = 20, max = 0.01, radius = 15) %>%
  addMarkers(data = head(myData, 50000), ~lon, ~lat, icon = ~newIcons[image], clusterOptions = markerClusterOptions(), 
             label = ~ format(Date, format = "%H:%M %d-%b-%Y"), group = "Points") %>% 
  addLayersControl(
     baseGroups = c("Default Maptile", "Dark Maptile", "Satellite Maptile"),
     overlayGroups = c("HeatMap", "Points"),
     options = layersControlOptions(collapsed = FALSE)
   )
```

---

## Full Code

The whole thing was packaged into a Shiny Dashboard, so the code was amended a bit as well as having the various `shinydashboard` options added. Below is the complete code in Shiny Dashboard format.

Happy mapping!

```{r}
library(shiny)
library(shinydashboard)
library(leaflet)
library(jsonlite)
library(tidyverse)
library(leaflet.extras)
library(lubridate)

# Define UI for data upload app ----
ui <- dashboardPage(skin = "red",
                    title = "Google Location Map",
                    dashboardHeader(title = "Google Location Map", titleWidth = 300),

                    # interactive sidebar with menu and widgets
                    dashboardSidebar(width = 300,
                                     
                         tags$div(
                           tags$blockquote("Use this app to see where Google has tracked you!"),
                           tags$h4("How to get your Google location data:"),
                           tags$p("Visit ", tags$a(href="https://takeout.google.com/", "Google Takeout")," to see and download any of the data Google holds on you."),
                           tags$p("Click on SELECT NONE, then scroll down to Location History and click on the slider to select it."),
                           tags$p("Scroll to the bottom and click NEXT, then CREATE ARCHIVE, and finally DOWNLOAD when it is ready. You will need to verify by logging into your Google account."),
                           tags$p("This will download a ZIP file to your downloads directory. Extract this ZIP file which will create a directory called Takeout"),
                           tags$p("Upload the JSON file found in Takeout/Location History using the selector below..."),
                           style = "padding: 10px;"
                           
                         ),
                         
                         tags$hr(),
                        
                        # Input: Select a file ----
                        fileInput("file1", "Upload Location History.json",
                                  multiple = FALSE,
                                  accept = ".json",
                                  placeholder = "Max file size 100Mb"),
                        
                        tags$hr(),
                        
                        tags$div(
                          p("Once data is loaded, select a maptile of your preference and whether you want to see a heatmap, clustered point data, or both at the top right of the map. Point data is capped at the most recent 50,000 locations."),
                          tags$i("Your data will only be used for the purpose of rendering this visualisation and will not be stored permamently by this application!"),
                          style = "padding: 10px;"
                        ),
                        
                        # CoI credit tag
                        div(style = "padding: 10px;",
                            helpText("Powered by", a("Culture of Insight", 
                                                     href = "https://cultureofinsight.com", 
                                                     target = "_blank")))
                        
                      ),
                      
                      # Main panel for displaying outputs ----
                      dashboardBody(
                        
                        tags$head(tags$style("#myMap{height:90vh !important;}")),
                        
                        leafletOutput("myMap")
                                        
                      )
)

# Define server logic to read selected file ----
server <- function(input, output) {
  
  options(shiny.maxRequestSize = 100*1024^2)
  
  output$myMap <- renderLeaflet({
    leaflet() %>% 
      addProviderTiles(providers$CartoDB.Positron, group = "Default Maptile") %>% 
      addProviderTiles(providers$CartoDB.DarkMatter, group = "Dark Maptile") %>%
      addProviderTiles(providers$Esri.WorldImagery, group = "Satellite Maptile") %>%
      setView(24, 27, zoom = 2) %>% 
      addLayersControl(
        baseGroups = c("Default Maptile", "Dark Maptile", "Satellite Maptile"),
        options = layersControlOptions(collapsed = FALSE)
      )
  })
  
  observe({
    
    withProgress(message = 'Please wait...',
                 value = 0/4, {
                   
                   req(input$file1)
                   
                   incProgress(1/4, detail = "reading data")
                   
                   locationdata <- fromJSON(input$file1$datapath, simplifyVector = TRUE, simplifyDataFrame = TRUE)
                   
                   newIcons <- iconList(
                     stand = makeIcon("stand.png", "stand.png", 36, 36),
                     drive = makeIcon("drive.png", "drive.png", 36, 36)
                   )
                   
                   incProgress(1/4, detail = "cleaning data")
                   
                   myData <- locationdata$locations %>% 
                     select(latitudeE7, longitudeE7, `timestampMs`, velocity) %>% 
                     mutate(lat = latitudeE7 / 1E7, lon = longitudeE7 / 1E7) %>% 
                     mutate(timestampMs = as.numeric(timestampMs)) %>%
                     mutate(Date = as.POSIXct(timestampMs/1000, origin="1970-01-01")) %>%
                     select(-latitudeE7, -longitudeE7) %>% 
                     mutate(image = case_when(
                       velocity > 10 ~ "drive",
                       TRUE ~ "stand"
                     )) %>% 
                     mutate(image = factor(image, levels = c("drive","stand")))
                   
                   incProgress(1/4, detail = "rendering map")
                   
                   leafletProxy("myMap", data = myData) %>%
                     fitBounds(~min(lon), ~min(lat), ~max(lon), ~max(lat)) %>%  
                     addHeatmap(lng = ~lon, lat = ~lat, group = "HeatMap", blur = 20, max = 0.01, radius = 15) %>%
                     addMarkers(data = head(myData, 50000), ~lon, ~lat, icon = ~newIcons[image], clusterOptions = markerClusterOptions(), 
                                label = ~ format(Date, format = "%H:%M %d-%b-%Y"), group = "Points") %>% 
                     addLayersControl(
                       baseGroups = c("Default Maptile", "Dark Maptile", "Satellite Maptile"),
                       overlayGroups = c("HeatMap", "Points"),
                       options = layersControlOptions(collapsed = FALSE)
                     )
                   
                   incProgress(1/4)
                   
                 })
  })

  }

# Create Shiny app ----
shinyApp(ui, server)
```

[^1]: Location data available [here](https://takeout.google.com) if you have a google account and location tracking is turned on. Our application does not store the data you upload permanently, it is deleted when your session ends.