Documentation for IIT_Guwahati.ipynb and Project Details
Project Overview
Background and Motivation
The project, titled Dynamic Pricing for Urban Parking Lots, is a Capstone Project for Summer Analytics 2025, hosted by the Consulting & Analytics Club in collaboration with Pathway. The goal is to develop an intelligent, data-driven pricing engine for 14 urban parking spaces using real-time data streams, basic economic theory, and machine learning models built from scratch. The motivation stems from the inefficiencies of static pricing in urban parking, which can lead to overcrowding or underutilization. Dynamic pricing adjusts prices based on demand, competition, and real-time conditions to optimize parking space utilization.
Data Description
The dataset covers 14 urban parking spaces over 73 days, with data sampled at 18 time points per day (every 30 minutes from 8:00 AM to 4:30 PM). Key features include:

Vehicle Information: Type of incoming vehicle (car, bike, truck).
Environmental Conditions: Nearby traffic congestion level, special day indicators (e.g., holidays, events).
Demand Fluctuations: Each time step reflects the state of the parking lot, with demand varying based on the above features.

Project Objective
The objective is to build a dynamic pricing model for each parking space that:

Updates prices in real-time based on:
Historical occupancy patterns
Queue length
Nearby traffic
Special events
Vehicle type
Competitor parking prices


Starts from a base price of $10.
Ensures smooth and explainable price variations (not erratic).
Optionally suggests rerouting vehicles to nearby lots if the current lot is overburdened.

Core Requirements

Pricing Logic Implementation:
Implement all pricing models from scratch using only Python, Pandas, NumPy, and Pathway.


Pricing Model Stages:
Model 1: Baseline Linear Model:
A simple model where the next price is a function of the previous price and current occupancy.
Price increases linearly with occupancy.
Serves as a reference point.


Additional models (not detailed in the provided PDF excerpt) are expected to increase in complexity.


Real-Time Simulation with Pathway:
Use Pathway to:
Ingest data streams with delays, preserving timestamp order.
Process features in real-time.
Emit pricing predictions continuously.


A sample notebook is provided with data simulation code, Pathway integration, and hooks for custom pricing logic.


Execution Environment:
All code must be written and executed in Google Colab.


Visualization Requirements:
Provide real-time visualizations using Bokeh.
Suggested plots:
Real-time pricing line plots for each parking space.
Comparison with competitor prices.
Visualizations should justify pricing behavior.




Submission Guidelines:
Submit a well-commented Google Colab notebook and a report explaining:
Steps and justifications.
Details of all models used.
Demand function, assumptions, and how prices change with demand and competition.
Include Bokeh visualizations and graphs in both the notebook and report.


Evaluation is subjective, with no objective metric specified, as this is a project, not a hackathon.



Resources

Pathway documentation for deploying real-time applications:
From Jupyter to Deploy
First Real-Time App with Pathway


Summer Analytics 2025: Course Details

Code Documentation: IIT_Guwahati.ipynb
Overview
The provided Jupyter notebook (IIT_Guwahati.ipynb) sets up the environment and initializes a visualization framework for the dynamic pricing project. It includes package installations, library imports, and a Bokeh-based interactive visualization to display pricing data for different parking lots.
Notebook Structure
The notebook consists of two main cells:

Cell 1: Package Installation
Installs required Python libraries: pathway, pandas, numpy, bokeh, and haversine.


Cell 2: Library Imports and Visualization Setup
Imports necessary libraries.
Initializes Bokeh for notebook output.
Sets up a visualization framework (though the actual data processing and plotting code is incomplete in the provided excerpt).



Detailed Code Breakdown
Cell 1: Package Installation
!pip install pathway pandas numpy bokeh haversine


Purpose: Installs the required Python libraries for the project.
Libraries Installed:
pathway: For real-time data streaming and processing.
pandas: For data manipulation and analysis.
numpy: For numerical computations.
bokeh: For interactive visualizations.
haversine: For calculating distances between geographic coordinates (likely used for rerouting or proximity-based pricing logic).


Output: The cell output confirms successful installation of the libraries and their dependencies, with specific versions listed (e.g., pathway==0.24.1, pandas==2.2.2, etc.).

Cell 2: Library Imports and Visualization Setup
import pandas as pd
import numpy as np
from haversine import haversine
import pathway as pw
from bokeh.plotting import figure, output_notebook, show
from bokeh.models import ColumnDataSource, Select, Slider, CustomJS
from bokeh.layouts import column, row

output_notebook()


Purpose: Imports libraries and initializes Bokeh for rendering visualizations in the notebook.
Library Details:
pandas and numpy: Used for data manipulation and numerical operations.
haversine: Likely used to calculate distances between parking lots for rerouting logic or competitor price analysis.
pathway: Handles real-time data streaming and processing.
bokeh.plotting: Provides functions (figure, show) for creating and displaying plots.
bokeh.models: Provides components (ColumnDataSource, Select, Slider, CustomJS) for interactive visualizations.
bokeh.layouts: Provides layout managers (column, row) for organizing Bokeh widgets and plots.


Bokeh Initialization:
output_notebook(): Configures Bokeh to render plots inline in the Jupyter notebook.


Output: The cell outputs JavaScript and HTML code to initialize Bokeh's plotting environment, including loading necessary BokehJS libraries and setting up the notebook's rendering context.

Bokeh Visualization Details
The output of Cell 2 includes a Bokeh plot configuration (embedded as JSON), which describes an interactive visualization with the following components:

Select Widget:
A dropdown menu (Select) with the title "Select Lot:" allows users to choose from 14 parking lot IDs (e.g., "BHMBCCMKT01", "Broad Street", "Others-CCCPS105a", etc.).
Default value: "BHMBCCMKT01".


Plot:
A line plot (Figure) with dimensions 700x350 pixels.
Displays three lines representing:
Baseline price (solid line).
Demand-Based price (dashed line, line_dash=[6]).
Competitor-Based price (dash-dot line, line_dash=[2,4,6,4]).


X-axis: Datetime axis (DatetimeAxis) for timestamps.
Y-axis: Linear axis (LinearAxis) for prices.
Title: Dynamically updates to "Lot [selected_lot] Pricing Comparison" (e.g., "Lot BHMBCCMKT01 Pricing Comparison").
Includes a legend in the top-left corner identifying the three pricing lines.


CustomJS Callback:
A JavaScript callback updates the plot when a new parking lot is selected from the dropdown.
Code snippet:const data = full_source.data;
const sel = lot_select.value;
const ts = [], b = [], d = [], c = [];
for (let i = 0; i < data[lot_id_col].length; i++) {
    if (String(data[lot_id_col][i]) === sel) {
        ts.push(new Date(data['timestamp'][i]));
        b.push(data['price_baseline'][i]);
        d.push(data['price_demand'][i]);
        c.push(data['price_competitor'][i]);
    }
}
plot_source.data = {
    timestamp: ts,
    baseline: b,
    demand: d,
    competitor: c
};
plot_source.change.emit();
p.title.text = "Lot " + sel + " Pricing Comparison";


Functionality:
Filters data from a full_source (a ColumnDataSource containing all parking lot data) based on the selected lot ID.
Updates a plot_source (another ColumnDataSource) with filtered data for timestamps, baseline prices, demand-based prices, and competitor-based prices.
Updates the plot title dynamically.
Triggers a re-render of the plot with plot_source.change.emit().





Missing Components
The provided notebook excerpt does not include:

Data Loading and Processing: Code to load the dataset (likely dataset.csv) and process it using Pathway.
Pricing Logic Implementation: The actual logic for the baseline linear model or other pricing models.
Pathway Integration: Code to simulate real-time data ingestion and emit pricing predictions.
Additional Visualizations: The notebook only sets up one plot; additional visualizations (e.g., for other parking lots or metrics) are not included.
Rerouting Logic: Optional logic for suggesting nearby parking lots is not implemented.

Assumptions in the Code

The dataset contains columns: SystemCodeNumber (lot ID), timestamp, price_baseline, price_demand, and price_competitor.
The timestamps are in a format that can be converted to JavaScript Date objects.
The pricing data is precomputed (baseline, demand-based, and competitor-based prices) and stored in a ColumnDataSource.

Project Implementation Notes
To complete the project based on the provided code and PDF:

Data Loading:
Load the dataset (dataset.csv) using pandas or pathway.
Ensure the dataset includes columns for lot ID, timestamp, occupancy, queue length, traffic congestion, special events, vehicle type, and competitor prices.


Pathway Integration:
Use Pathway to ingest data as a stream, preserving timestamp order.
Implement real-time feature processing (e.g., calculating demand based on occupancy, traffic, etc.).
Emit pricing predictions continuously using Pathway's streaming capabilities.


Pricing Models:
Baseline Linear Model:
Implement a simple linear function, e.g., price_next = price_previous + k * occupancy, where k is a constant.
Ensure the base price starts at $10 and increases smoothly with occupancy.


Develop additional models (e.g., incorporating traffic, events, or competitor prices) as required.


Visualizations:
Extend the Bokeh visualization to include all 14 parking lots, possibly using tabs or multiple plots.
Add plots for occupancy, queue length, or other features to justify pricing behavior.
Ensure visualizations update in real-time as new data is streamed.


Optional Rerouting:
Use the haversine library to calculate distances between parking lots.
Suggest rerouting to nearby lots based on occupancy or queue length thresholds.


Report:
Document the demand function, assumptions (e.g., linear relationship between occupancy and price), and how prices respond to demand and competition.
Include Bokeh visualizations with explanations.
Justify all modeling choices and implementation steps.



Execution Environment

The code is designed to run in Google Colab, as specified.
Ensure all dependencies (pathway, pandas, numpy, bokeh, haversine) are installed in the Colab environment.

Conclusion
The provided notebook sets up the foundation for a dynamic pricing system by installing necessary libraries and initializing a Bokeh visualization framework. However, it lacks the core pricing logic, data processing, and Pathway integration required to meet the project objectives. To complete the project, you must implement the pricing models, integrate real-time data streaming with Pathway, and enhance the visualizations to cover all requirements. The documentation and report should clearly explain the models, assumptions, and visualizations to justify the pricing strategy.
