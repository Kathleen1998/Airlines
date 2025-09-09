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

##Average Flight Delays By Airline s and Day of Week

```
airlines %>%
  mutate(DAYS = weekdays(DayOfWeek)) %>% 
  group_by(Airline, DAYS) %>%
  summarise(Total = sum(Delay)) %>%
  ggplot(aes(x = DAYS, y = Total, fill = Airline)) + # Fill by airline if you want colors per airline
  geom_bar(stat = "identity") +
  facet_wrap(~ Airline, ncol = 3) + # Arrange in 3 columns, adjust as needed
  labs(title = "Average Flight Delays by Airline and Day of Week",
       x = "Day of Week", y = "Average Delay (Minutes)") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1), # Rotate labels if needed
        strip.text = element_text(size = 8)) + # Adjust facet label size
  guides(fill = "none") # Hide redundant legend if using fill

```

<img width="882" height="699" alt="image" src="https://github.com/user-attachments/assets/a2bcac76-8c51-4a12-8487-f403bcbd200a" />
This graph illustrates a significant decrease in airline delays on Wednesdays. This trend appears to be consistent across all airlines in the dataset, with delays reaching their lowest point on this day. In contrast, Sundays and Mondays consistently show the highest number of delays throughout the week.
While the dataset doesn't specify the exact causes of these delays, the consistent pattern suggests that human factors may play a role, particularly at the beginning of the week. Factors such as pilot and crew availability, air traffic, and maintenance issues are often influenced by the weekly cycle.
To mitigate this issue and more evenly distribute delays, airlines could consider implementing staggered schedules for their flight crews, allowing them to begin their work week on different days. This could help smooth out the peaks in delays seen on Sundays and Mondays.

#Day of the week
```
Days <- airlines %>%
  mutate(DAYS = weekdays(DayOfWeek)) %>%
  group_by(DAYS)%>%
  summarise(Total = sum(Delay))

ggplot(Days, aes(x = DAYS, y = Total)) +
  geom_bar(stat = "identity", fill = "steelblue") +
  labs(title = "Delay Per Day",
       x = "Category",
       y = "Value") +
  theme_minimal()
```
<img width="636" height="504" alt="image" src="https://github.com/user-attachments/assets/dca36dd2-8653-451a-9402-af1289dcc650" />

##Total Flgihts
```
Total <- table(airlines$Airline)
Total_df <- as.data.frame(my_table)

CountFlight <- ggplot(Total_df, aes(x = Var1, y = Freq)) +
  geom_bar(stat = "identity", fill = "steelblue") +
  labs(title = "Total Flights",
       x = "Flights",
       y = "Airline") +
  theme_minimal()
  ```
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

#Per airline
```
Delays <- airlines %>%
  group_by(Airline)%>%
  summarise(Total = sum(Delay))

bar_plot <- ggplot(Delays, aes(x = Airline, y = Total)) +
  geom_bar(stat = "identity", fill = "steelblue") +
  labs(title = "Per Airline Delay",
       x = "Airline",
       y = "Delays") +
  theme_minimal()
```
<img width="876" height="695" alt="image" src="https://github.com/user-attachments/assets/00d4eed0-a003-45dc-9a1f-1fc88dc41dd7" />
South west with a significant amount of delays, followed by delta , skywest the american airlines, like I previously mentioned I suggest these 3 airlines to be the first to roll out the staggered schedules for their flight crews

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

This graph shows the amount of flights per airline (Atotal) Airline total in the reddish color with the amount of delays (Adelay) Airline delays in the teal color. Airlines WN (Southwest Airlines) has a significant amount of delays with over half of their flights having delays 



#Prediction ---------------------------------------------
```
plot(Total ~ DAYS, data = Days)
AirModel <- lm(Delay ~ DayOfWeek + Time + Length + Airline, data = airlines)
summary(AirModel)
#simple model building

set.seed(123)
version2 <- airlines[sample(nrow(airlines), 250000),]

plot(Total ~ DAYS, data = Days)
AirModel <- lm(Delay ~ DayOfWeek + Time + Length + Airline, data = airlines)
summary(AirModel)
## breaking down data set for Training and for Testing
```

<img width="671" height="662" alt="image" src="https://github.com/user-attachments/assets/a25cac7f-bace-4a8e-b873-617de08604da" />


<img width="1040" height="733" alt="Prediction Model 1" src="https://github.com/user-attachments/assets/7143b1dc-7f82-43ac-adf7-1ee5ccbdbdf0" />



```
set.seed(123) ## helps to keep the same random selection everytime
sample_size <- floor(0.8 * nrow(FullAirlines)) ## Taking 80 percent of the row from the dataset
train_indices <- sample(seq_len(nrow(FullAirlines)),size = sample_size) #this is randomizing which rows I chose 

train <- FullAirlines[train_indices, ] # this is selecting all of the rows that were randomly selected
test <- FullAirlines[-train_indices, ] # this selects all the rows that werent selected in the randomization 

DelayModel <- glm(Delay ~ DayOfWeek + Time + Length, data = train_data, family = binomial)
summary(DelayModel) # to see the coefficient
```
<img width="643" height="375" alt="image" src="https://github.com/user-attachments/assets/233d98c7-24a7-4439-85a1-d2efb4061a1d" />

```
#Makes some predictions
prediction_df <- predict(DelayModel, newdata = test, type = "response" )
head(prediction_df)
```
<img width="559" height="42" alt="image" src="https://github.com/user-attachments/assets/1a6b0627-a6b0-4976-94a0-f7a4a6b3a07f" />


```
#Turning prediction into binary
predict_binary <- ifelse(prediction_df > 0.5, 1, 0)

#confusion matrix
table(predicted = predict_binary, actual = test$Delay)
```
<img width="264" height="76" alt="image" src="https://github.com/user-attachments/assets/71ed82ba-c753-477a-a95e-930c5af49838" />

```
AirlinesModel <- test

AirlinesModel$prediction_df <- predict(DelayModel, newdata = test, type = "response" )
AirlinesModel$predict_binary <- ifelse(AirlinesModel$prediction_df > 0.5, 1, 0)

library(caret)
conf_matrix <- table(Predicted = AirlinesModel$predict_binary , Actual = AirlinesModel$Delay)
fourfoldplot(conf_matrix, color = c("firebrick", "steelblue"),
             main = "Confusion Matrix")

```


<img width="1040" height="733" alt="Confusion Matrix" src="https://github.com/user-attachments/assets/7f475904-8b4c-4a1d-9378-67c8d4139555" />





