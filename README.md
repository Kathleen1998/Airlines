# Airlines
Analyzed a comprehensive dataset of airline and airport delays to identify key factors contributing to flight disruptions. Utilizing the R programming language, I developed statistical models to predict flight delays and visualize patterns. The project provided valuable insights into operational inefficiencies, which could inform strategic decision-making for airlines and airport management. 

What the project is. A clear, concise description.
Analayze flight delays, predicting 

Why it exists. What problem does it solve?
We need to be able to predictice if a flight will be delayed and what to look out for

How to use it. Instructions on how to install, run, and interact with your code.
This code is in written in the R porgamming langauge 

Screenshots or GIFs (if applicable). Visuals are very helpful!

https://www.kaggle.com/datasets/jimschacko/airlines-dataset-to-predict-a-delay

## Average Flight Delays By Airline s and Day of Week
<img width="882" height="699" alt="image" src="https://github.com/user-attachments/assets/a2bcac76-8c51-4a12-8487-f403bcbd200a" />
  This graph illustrates a significant decrease in airline delays to. This trend appears to be consistent across all airlines in the dataset, with delays reaching their lowest point on this day. In contrast, Sundays and Mondays consistently show the highest number of delays throughout the week.
While the dataset doesn't specify the exact causes of these delays, the consistent pattern suggests that human factors may play a role, particularly at the beginning of the week. Factors such as pilot and crew availability, air traffic, and maintenance issues are often influenced by the weekly cycle.
To mitigate this issue and more evenly distribute delays, airlines could consider implementing staggered schedules for their flight crews, allowing them to begin their work week on different days. This could help smooth out the peaks in delays seen on Sundays and Mondays.

Code:
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

# Analyzes flight delay data to visualize and compare the total delays
# for each airline across different days of the week. The goal is to identify
# which airlines or days have the highest overall delays
```




## Day of the week
<img width="636" height="504" alt="image" src="https://github.com/user-attachments/assets/dca36dd2-8653-451a-9402-af1289dcc650" />
  This is a more macro view of airlines delays, showing a more obvious trend in less delays on one perticualr day of the week. Sundays having the most delays during the week totaling in 42,254 delays across all airline then Wendesday only having 23,615 a significant a 56.6% decresase in delay by the middle of the week. 

Code:
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
## Analyzes flight delay data to visualize and compares the days to see wheater there are certain day with more or less delays
```

## Map of Airports
<img width="633" height="421" alt="image" src="https://github.com/user-attachments/assets/bc556d9b-a93d-4d9c-9fe5-c727f4cdbcf8" />

Code:
```
Total <- table(airlines$Airline)
Total_df <- as.data.frame(my_table)

CountFlight <- ggplot(Total_df, aes(x = Var1, y = Freq)) +
  geom_bar(stat = "identity", fill = "steelblue") +
  labs(title = "Total Flights",
       x = "Flights",
       y = "Airline") +
  theme_minimal()

## Visualizing with a bar chart flight and airlines identify how many flight an airline has had
  ```
```
##
USA <- gisco_get_countries(country = "USA", resolution = 1) ## SF spatial dataset; creating the shape of the us
US  <- st_as_sf(maps::map("state", fill=TRUE, plot =FALSE))
US <- states(cb = TRUE, resolution = "20m") %>%
  shift_geometry()
data <- world.cities %>% filter(country.etc == "UK")

# Create breaks for the color scale
mybreaks <- c(5,50,500,5000, 10000)
ToPortDelay2 <- ToPortDelay2[-9, ] # Removes the 9th row
ToPortDelay2 <- ToPortDelay2[-87, ]

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


airports_shifted_sf <- st_as_sf(ToPortDelay3, coords = c("lon_shifted", "lat_shifted"), crs = 4326)

st_crs(geo_shifted)
st_crs(PortShift)

tigcounties <- counties(cb = TRUE, resolution = "5m", year = 2020)
tigcounties <- tigcounties[as.numeric(tigcounties$STATEFP) < 60, ]

Port_sf <- st_as_sf(ToPortDelay2, coords = c("LONGITUDE", "LATITUDE"), crs = 4326)
PortShift<- shift_geometry(Port_sf, position = "below",preserve_area = FALSE)

theme_update(plot.title = element_text(hjust = 0.5))

PortShift %>% 
  ggplot() +
  geom_sf(data = geo_shifted, fill = "grey80", color = "white") +
  geom_sf(data = PortShift, color = "red", size = 1) +
  geom_point(
    aes( size = Total, geometry = geometry),
    stat = "sf_coordinates"
  ) +
    theme_void() +
  guides(colour = guide_legend()) +
    ggtitle("Delays per airport in the USA") 
  theme(
    legend.position.inside = c(1, 0.6),
    text = element_text(color = "#22211d"),
    plot.margin = margin(r = 0.4, l = 0.1, unit = "cm"),
    plot.background = element_rect(fill = "#f5f5f2", color = NA),
    panel.background = element_rect(fill = "#f5f5f2", color = NA),
    plot.title = element_text(size = 14, hjust = 0.5, color = "#4e4d47"),
    legend.title = element_text(size = 8),
    legend.text = element_text(size = 8)
  )

```




## Delays vs Total Flight
<img width="910" height="732" alt="delaysnondelays" src="https://github.com/user-attachments/assets/791d877f-1983-4478-8e19-aede7bae772e" />

This visualizes both the total amount of flight and delays per airline. There is an average of 29,965 flights, with the top carriers being WN (Southwest) at 94,097 flights (214.0% more than the average) and DL (Delta Airlines) at 60,940 flights (103.4%); conversely, the least frequent are HA (Hawaiian Airlines) with 5,578 (-81.4% less )and F9 (Frontier Airlines) with 6,456 (-78.5% less) flights. Given that Southwest with 69.7% of its flights delayed, CO (Continental Airlines) with 56.6% delayed, and B6 (JetBlue) with 46.7% delayed, show a significant amount of delays, I suggest the high-volume and high-delay airlines—Southwest, Delta, and SkyWest—should be the first to roll out staggered schedules for their flight crews.

Code:
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
##  creating a dual par chart plot to better visulaize an airline delays vs total flight to find who has a better ration to worse

```




# Genralized Linear Model // Making predictions

<img width="671" height="662" alt="image" src="https://github.com/user-attachments/assets/a25cac7f-bace-4a8e-b873-617de08604da" />

This is a general linear regression model to predict flight delays based on several key factors. It includes steps for data exploration and preparing the data for model training.
Based on Day of the week, departure time, length of the flight, and airline we    will be deliberating which factors play a key role.

Above is the result after inputting the data into the regression model. Looking at DayOfWeek, Time, and Length first. 
This model is telling us that Dayofweek has an estimate of -6.457e-03 (0.006457) which means by an increase of one day there is a 0.7% increase in the less likelihood of a delay. In simpler terms as the days go on there will be a compounded decrease in the chance of a delay. 

# Interpreting the numbers
Day of week (M-S). E-0.006457 = 0.993 | 0.993 - 1 = -0.007 | -0.007*100 = -0.7% 
Time(mintutes from zero) e0.000273 = 1.000273037 | 1.000273037 - 1 = 0.00111662 | 0.00111662* 100 = .11% 
Length(Length of Flight) 

Code:
```
set.seed(123)
version2 <- airlines[sample(nrow(airlines), 250000),]
#This ensures that the random sample we take is the same every single time
# Randomly select a subset of 250,000 rows from the original dataset.


plot(Total ~ DAYS, data = Days)
AirModel <- lm(Delay ~ DayOfWeek + Time + Length + Airline, data = airlines)
summary(AirModel)
## This is to predict Delays based off of Day of the week, time of the fligh, length of the flight

```

## Trained Model // Testing Set

<img width="643" height="375" alt="image" src="https://github.com/user-attachments/assets/233d98c7-24a7-4439-85a1-d2efb4061a1d" />

Code:
```
set.seed(123) ## helps to keep the same random selection everytime
sample_size <- floor(0.8 * nrow(FullAirlines)) ## Taking 80 percent of the row from the dataset
train_indices <- sample(seq_len(nrow(FullAirlines)),size = sample_size) #this is randomizing which rows I chose 

train <- FullAirlines[train_indices, ] 
test <- FullAirlines[-train_indices, ] 
# The training set is used to 'teach' the model to find patterns in the data.
# It consists of all the randomly selected rows from the original dataset.

DelayModel <- glm(Delay ~ DayOfWeek + Time + Length, data = train_data, family = binomial)
summary(DelayModel) # to see the coefficient
# Then the testing set is used to evaluate the model's predictive performance.
# It contains all the rows that were not included in the training set,
# ensuring the model has never seen this data before.
```

<img width="1040" height="733" alt="Prediction Model 1" src="https://github.com/user-attachments/assets/7143b1dc-7f82-43ac-adf7-1ee5ccbdbdf0" />

```
prediction_df <- predict(DelayModel, newdata = test, type = "response" )
head(prediction_df)
# Then using trained model to predict the probability of a delay for each flight in
# the unseen 'test' dataset. The 'response' type ensures the output is a probability
# value between 0 and 1.

```
## Confusion Matrix

<img width="1040" height="733" alt="Confusion Matrix" src="https://github.com/user-attachments/assets/6d9f01fc-5046-4bb5-b17d-cec0f92b61f4" />

```
predict_binary <- ifelse(prediction_df > 0.5, 1, 0)
# Convert the predicted probabilities into a binary classification (0 or 1). if the predicted probability of a delay
# is greater than 50%, we classify it as a '1' (Delay), otherwise it's a '0'

table(predicted = predict_binary, actual = test$Delay)
# The confusion matrix to evaluate the model's performance.
# This table compares our model's predictions to the actual outcomes in the test dataset.

AirlinesModel <- test
#Creating a copy of the test data to store our predictions.

AirlinesModel$prediction_df <- predict(DelayModel, newdata = test, type = "response" )
AirlinesModel$predict_binary <- ifelse(AirlinesModel$prediction_df > 0.5, 1, 0)
# Now using the trained model to generate predicted probabilities for the test data.
# We store these probabilities in a new column called 'prediction_df'.


library(caret)
conf_matrix <- table(Predicted = AirlinesModel$predict_binary , Actual = AirlinesModel$Delay)
# Creating a confusion matrix by comparing our model's binary predictions to the
# actual outcomes in the test data. This is the primary tool for evaluating a classification model's performance.


fourfoldplot(conf_matrix, color = c("firebrick", "steelblue"),
             main = "Confusion Matrix")
# Lastly using a  fourfoldplot from the caret package to visualize the confusion matrix.
# This plot displays the proportions of true positives, true negatives, false positives, and false negatives, giving
# a quick and intuitive view of the model's performance.
```


















