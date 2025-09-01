# Airlines
Figuring out air travel delays

Analyzed a comprehensive dataset of airline and airport delays to identify key factors contributing to flight disruptions. Utilizing the R programming language, I developed statistical models to predict flight delays and visualize patterns. The project provided valuable insights into operational inefficiencies, which could inform strategic decision-making for airlines and airport management.


What the project is. A clear, concise description.
Analayze flight delays, predicting 

Why it exists. What problem does it solve?
We need to be able to predictice if a flight will be delayed and what to look out for

How to use it. Instructions on how to install, run, and interact with your code.
This code is in written in the R porgamming langauge 

Screenshots or GIFs (if applicable). Visuals are very helpful!

https://www.kaggle.com/datasets/jimschacko/airlines-dataset-to-predict-a-delay

<img width="882" height="699" alt="image" src="https://github.com/user-attachments/assets/a2bcac76-8c51-4a12-8487-f403bcbd200a" />

<img width="636" height="504" alt="image" src="https://github.com/user-attachments/assets/dca36dd2-8653-451a-9402-af1289dcc650" />


<img width="633" height="419" alt="image" src="https://github.com/user-attachments/assets/8e790768-4caf-455f-a221-cdff9611fbfa" />

```
USA <- gisco_get_countries(country = "USA", resolution = 1) ## SF spatial dataset; creating the shape of the us
US  <- st_as_sf(maps::map("state", fill=TRUE, plot =FALSE))
US <- states(cb = TRUE, resolution = "20m") %>%
  shift_geometry()
data <- world.cities %>% filter(country.etc == "UK")

# Create breaks for the color scale
mybreaks <- c(5,50,500,5000, 10000)

# The hopeful answer to my prayers 
ToPortDelay3 <- ToPortDelay2
Port_sf <- st_as_sf(ToPortDelay2, coords = c("LONGITUDE", "LATITUDE"), crs = 4326)
PortShift<- shift_geometry(Port_sf, position = "below")
# Download US county geo data to reproduce this example but filter out US territories  
tigcounties <- counties(cb = TRUE, resolution = "5m", year = 2020)
tigcounties <- tigcounties[as.numeric(tigcounties$STATEFP) < 60, ]

tigstates <- states(cb = FALSE, resolution = "5m", year = 2020)
tigstates <- tigstates[as.numeric(tigstates$STATEFP) <= 60, ]



geo_shifted <- shift_geometry(
  tigcounties,
  position = "below", # other option: "outside"
  preserve_area = FALSE
)

# Example: shift Alaska and Hawaii manually (approximate)
ToPortDelay3 <- ToPortDelay3 %>%
  mutate(
    lon_shifted = case_when(
      STATE == "AK" ~ LONGITUDE + 35,  # Alaska
      STATE == "HI" ~ LONGITUDE + 52,  # Hawaii
      TRUE ~ LONGITUDE
    ),
    lat_shifted = case_when(
      STATE == "AK" ~ LATITUDE - 15,   # Alaska
      STATE == "HI" ~ LATITUDE - 5,    # Hawaii
      TRUE ~ LATITUDE
    )
  )
```
<img width="633" height="421" alt="image" src="https://github.com/user-attachments/assets/bc556d9b-a93d-4d9c-9fe5-c727f4cdbcf8" />


<img width="876" height="695" alt="image" src="https://github.com/user-attachments/assets/00d4eed0-a003-45dc-9a1f-1fc88dc41dd7" />

Delays vs Total Flight
```
Total <- table(airlines$Airline)
Total_df <- as.data.frame(my_table)

CountFlight <- ggplot(Total_df, aes(x = Var1, y = Freq)) +
  geom_bar(stat = "identity", fill = "steelblue") +
  labs(title = "Total Flights",
       x = "Flights",
       y = "Airline") +
  theme_minimal()


Delays2 <- airlines %>%
  group_by(Airline)%>%
  summarise(Total = sum(Delay))


Aline <- (Total_df$Var1)

Atotal <- (Total_df$Freq)

Adelay <- (Delays$Total)

overLap <- data.frame(Aline, Atotal, Adelay)

overLap %>%
  melt(value.name = 'DelaysVsFlights',id.vars = 'Aline',variable.name = 'Airlines')  %>%
  ggplot(aes(x=Aline,y=DelaysVsFlights,fill=Airlines))+
  geom_col(position = position_jitterdodge(dodge.width = 0.5,jitter.height = 0,jitter.width = 0,seed = 25)) -> gg_output


plotly_object <- plotly::ggplotly(gg_output)

plotly_object


```
<img width="910" height="732" alt="delaysnondelays" src="https://github.com/user-attachments/assets/791d877f-1983-4478-8e19-aede7bae772e" />










