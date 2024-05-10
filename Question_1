# Import library
library(ggplot2)
library(dplyr)
library(lubridate)
library(readr)
library(nortest)

# Import data set
load("DATA_LOGGER_Nant.RData")
data<-df
# Convert to data frame

data<-data.frame(data)

# visulize type of the data
str_data<-str(data)

# First Question:
variable_names <- names(data)
variable_types <- as.character(sapply(data, class))
variable_table <- data.frame(Variable = variable_names, Type = variable_types)
print(variable_table)


#Second Question:

# ggplot code with histogram and density overlay for each sensor
ggplot(data, aes(x = Temperature, fill = factor(id))) +
  geom_histogram(aes(y = ..density..), alpha = 0.5, position = "identity", bins = 150) +  
  geom_density(alpha = 0.7, color = "black", size = 0.5) +  # Adding density plot
  labs(title = "Hourly Temperature Distributions by Sensor", x = "Temperature", y = "Density") + 
  scale_fill_discrete(name = "Sensor ID") +
  facet_wrap(~ id, nrow = 5) +
  theme_minimal()  


# Create a boxplot for all sensors
boxplot_all_sensors <- ggplot(data, aes(x = factor(id), y = Temperature, fill = factor(id))) +
  geom_boxplot(alpha = 0.7) +
  labs(title = "Boxplot of Temperature by Sensor", x = "Sensor ID", y = "Temperature") +
  scale_fill_discrete(name = "Sensor ID") +  
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 90, hjust = 1))

# Print the boxplot
print(boxplot_all_sensors)


#Plot Histogram
ggplot(data, aes(x = Temperature, fill = factor(id))) +
  geom_histogram(alpha = 0.5, position = "identity", bins = 150) +  
  labs(title = "Hourly Temperature Distributions by Sensor", x = "Temperature", y = "Frequency") + 
  scale_fill_discrete(name = "Sensor ID") +
  facet_wrap(~ id, nrow = 5)  
theme_minimal()  



#Question 5:

data_selected <- data %>% select(id, Temperature)
# Apply Lilliefors test for normality
normality_results <- data_selected %>%
  group_by(id) %>%
  summarise(
    Sample_Size = n(),
    Lilliefors_p_value = if(n() > 3) lillie.test(Temperature)$p.value else NA_real_  
  )
# Add a column to decide if the data is normally distributed (alpha = 0.05)
normality_results <- normality_results %>%
  mutate(Is_Normal = ifelse(is.na(Lilliefors_p_value), NA, Lilliefors_p_value > 0.05))
# Print results
print(normality_results)
# QQ plot
sensor_ids <- unique(data$id)
sorted_sensor_ids <- sort(sensor_ids)
index <- which(sorted_sensor_ids == 14)
group1 <- sorted_sensor_ids[sorted_sensor_ids < 14]
group2 <- sorted_sensor_ids[sorted_sensor_ids >= 14]
generate_qq_plots <- function(sensor_group, filename) {
  num_sensors <- length(sensor_group)
  num_cols <- ceiling(sqrt(num_sensors))  # Number of columns in the layout
  num_rows <- ceiling(num_sensors / num_cols)  # Number of rows in the layout
  pdf(filename, width = 10, height = 10) 
  par(mfrow = c(num_rows, num_cols))
  for(i in 1:length(sensor_group)) {
    sensor_temperatures <- data$Temperature[data$id == sensor_group[i]]
    # Create QQ-plot
    qqnorm(sensor_temperatures, main = paste("QQ-plot for Sensor", sensor_group[i]))
    qqline(sensor_temperatures, col = "red", lwd = 2) # Add a reference line
  }
  dev.off()
}
generate_qq_plots(group1, "Temperature_QQ_Plots_Group1.pdf")
generate_qq_plots(group2, "Temperature_QQ_Plots_Group2.pdf")

#6


# Step 1: Calculate the minimum temperature for each sensor
adjustments <- data %>%
  group_by(id) %>%
  summarise(min_temp = min(Temperature, na.rm = TRUE)) %>%
  mutate(c = ifelse(min_temp < 0, abs(min_temp) + 1, 0))  
# Step 2: Apply the square root transformation using calculated 'c'
data_adjusted <- data %>%
  left_join(adjustments, by = "id") %>%
  mutate(Normalized_Temperature = sqrt(Temperature + c))

# Step 3: Apply Lilliefors test for normality on transformed data for each sensor
normality_results <- data_adjusted %>%
  group_by(id) %>%
  summarise(
    Sample_Size = n(),
    Lilliefors_p_value = if(n() > 3) lillie.test(Normalized_Temperature)$p.value else NA_real_  
  ) %>%
  mutate(Is_Normal = ifelse(is.na(Lilliefors_p_value), NA, Lilliefors_p_value > 0.05))

# Print the normality test results
print(normality_results)

# Function to generate QQ plots for each sensor
generate_qq_plots <- function(sensor_group, filename) {
  pdf(filename, width = 10, height = 10)
  num_sensors <- length(sensor_group)
  num_cols <- ceiling(sqrt(num_sensors))
  num_rows <- ceiling(num_sensors / num_cols)
  
  par(mfrow = c(num_rows, num_cols))
  
  for(i in 1:length(sensor_group)) {
    sensor_temperatures <- data_adjusted$Normalized_Temperature[data_adjusted$id == sensor_group[i]]
    
    qqnorm(sensor_temperatures, main = paste("QQ-plot for Sensor", sensor_group[i]))
    qqline(sensor_temperatures, col = "red", lwd = 2)
  }
  
  dev.off()
}

generate_qq_plots(group1, "Normalized_Temperature_QQ_Plots_Group1.pdf")
generate_qq_plots(group2, "Normalized_Temperature_QQ_Plots_Group2.pdf")



#q 7
# List of categorical variables to test
categorical_vars <- c("Logger", "Mois", "Mat", "ORIENTATION", "PENTE", "LandCover")
# Initialize a data frame to store the results
results_df <- data.frame(Variable = character(),
                         Chi_squared = numeric(),
                         P_value = numeric(),
                         Decision = character(),
                         stringsAsFactors = FALSE)
# Function to perform Chi-squared test for uniform distribution and store results
perform_chisq_test <- function(data, variable) {
  # Create frequency table
  freq_table <- table(data[[variable]])
  # Expected frequencies assuming uniform distribution
  expected_freq <- rep(sum(freq_table) / length(freq_table), length(freq_table))
  
  # Perform the Chi-squared test
  chisq_result <- chisq.test(x = freq_table, p = rep(1/length(freq_table), length(freq_table)))
  
  # Decision based on p-value (common threshold of 0.05)
  decision <- ifelse(chisq_result$p.value < 0.05, "Reject H0", "Fail to Reject H0")
  
  # Store results
  results_df <<- rbind(results_df, data.frame(Variable = variable,
                                              Chi_squared = chisq_result$statistic,
                                              P_value = chisq_result$p.value,
                                              Decision = decision))
}

# Apply the function to each categorical variable
lapply(categorical_vars, function(var) perform_chisq_test(data, var))

# Print the results table
print(results_df)






