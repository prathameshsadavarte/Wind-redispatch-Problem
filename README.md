# Wind-redispatch-Problem
import pandas as pd


# Step 1: Load input data from a CSV file
input_data = pd.read_csv("data.csv")


# Step 2: Define geographic zones (East, North, West, South) and their respective farms
zones = {
    'East': [f'E{i}' for i in range(1, 25)],
    'North': [f'N{i}' for i in range(1, 27)],
    'West': [f'W{i}' for i in range(1, 16)],
    'South': [f'S{i}' for i in range(1, 36)]
}


# Step 3: Define a function to compute redistributed output for each zone
def calculate_redistributed_output(zone_data):
    # Step 3a: Calculate the total state-level forecast for the current zone
    total_state_forecast = zone_data['Forecast'].sum()


    # Step 3b: Calculate the weight for each farm based on its individual forecast and capacity
    zone_data['Weight'] = (zone_data['Forecast'] / zone_data['Capacity']) / (zone_data['Forecast'].sum() / total_state_forecast)


    # Step 3c: Calculate the redistributed power output for each farm
    zone_data['Redistributed_Output'] = zone_data['Weight'] * total_state_forecast
    return zone_data[['Plant_Name', 'Redistributed_Output']]


# Step 4: Apply the calculate_redistributed_output function to each zone's data
redistributed_output_df = pd.concat([calculate_redistributed_output(input_data[input_data['Plant_Name'].isin(farms)]) for _, farms in zones.items()], ignore_index=True)


# Step 5: Save the output to a CSV file
redistributed_output_df.to_csv("redistributed_wind_farm_output.csv", index=False)


# Step 6: Display the redistributed output DataFrame
print(redistributed_output_df)
