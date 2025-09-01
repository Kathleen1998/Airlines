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

Call:
lm(formula = Delay ~ DayOfWeek + Time + Length + Airline, data = airlines)

Residuals:
    Min      1Q  Median      3Q     Max 
-0.9031 -0.3995 -0.2423  0.5024  1.0133 

Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept)  1.603e-01  4.112e-03  38.985  < 2e-16 ***
DayOfWeek   -6.457e-03  3.358e-04 -19.229  < 2e-16 ***
Time         2.736e-04  2.314e-06 118.237  < 2e-16 ***
Length       4.740e-04  1.041e-05  45.527  < 2e-16 ***
AirlineAA   -4.708e-02  4.034e-03 -11.670  < 2e-16 ***
AirlineAS   -9.475e-02  5.551e-03 -17.070  < 2e-16 ***
AirlineB6    2.531e-02  4.868e-03   5.198 2.01e-07 ***
AirlineCO    1.233e-01  4.727e-03  26.082  < 2e-16 ***
AirlineDL    2.211e-02  3.851e-03   5.741 9.43e-09 ***
AirlineEV    5.053e-03  4.329e-03   1.167  0.24313    
AirlineF9    2.139e-02  6.754e-03   3.167  0.00154 ** 
AirlineFL   -1.237e-01  4.650e-03 -26.595  < 2e-16 ***
AirlineHA   -7.618e-02  7.122e-03 -10.696  < 2e-16 ***
AirlineMQ   -5.260e-02  4.107e-03 -12.807  < 2e-16 ***
AirlineOH   -1.269e-01  5.333e-03 -23.804  < 2e-16 ***
AirlineOO    5.166e-02  3.900e-03  13.245  < 2e-16 ***
AirlineUA   -1.192e-01  4.448e-03 -26.792  < 2e-16 ***
AirlineUS   -8.898e-02  4.184e-03 -21.265  < 2e-16 ***
AirlineWN    2.874e-01  3.632e-03  79.123  < 2e-16 ***
AirlineXE   -2.465e-02  4.237e-03  -5.817 5.99e-09 ***
AirlineYV   -1.615e-01  5.199e-03 -31.074  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.4721 on 539362 degrees of freedom
Multiple R-squared:  0.09786,	Adjusted R-squared:  0.09782 
F-statistic:  2925 on 20 and 539362 DF,  p-value: < 2.2e-16


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
Call:
glm(formula = Delay ~ DayOfWeek + Time + Length, family = binomial, 
    data = train_data)

Coefficients:
              Estimate Std. Error z value Pr(>|z|)    
(Intercept) -1.176e+00  1.302e-02  -90.30   <2e-16 ***
DayOfWeek   -2.962e-02  1.620e-03  -18.28   <2e-16 ***
Time         1.116e-03  1.129e-05   98.87   <2e-16 ***
Length       1.302e-03  4.425e-05   29.43   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 593031  on 431505  degrees of freedom
Residual deviance: 581980  on 431502  degrees of freedom
AIC: 581988

Number of Fisher Scoring iterations: 4

```
#Makes some predictions
prediction_df <- predict(DelayModel, newdata = test, type = "response" )
head(prediction_df)
```
        1         2         3         4         5         6 
0.2752546 0.2820586 0.2800290 0.2792418 0.3061413 0.2925599 

```
#Turning prediction into binary
predict_binary <- ifelse(prediction_df > 0.5, 1, 0)

#confusion matrix
table(predicted = predict_binary, actual = test$Delay)
```
         actual
predicted     0     1
        0 45457 32433
        1 14340 15647

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





