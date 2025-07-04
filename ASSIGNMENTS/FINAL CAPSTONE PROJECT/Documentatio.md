### What’s the Project About?

This is a Capstone Project for Summer Analytics 2025, run by the Consulting & Analytics Club with Pathway. The goal is to build a smart pricing system for 14 urban parking lots that adjusts prices in real-time based on demand, competition, and conditions like traffic or special events. Static prices cause issues like packed lots or empty spaces, so dynamic pricing aims to balance things out. You’re tasked with creating this system from scratch using only Python, Pandas, NumPy, and Pathway, and visualizing it with Bokeh.

#### The Data
You get data for 14 parking lots over 73 days, sampled every 30 minutes from 8:00 AM to 4:30 PM (18 data points per day). It includes:
- **Vehicle Types**: Cars, bikes, or trucks.
- **Conditions**: Traffic congestion levels and special day flags (e.g., holidays or events).
- **Demand**: Varies based on these factors, reflecting each lot’s state.

#### Project Goals
You need to build a pricing model for each lot that:
- Updates prices live, using:
  - Past occupancy trends
  - Queue length
  - Traffic nearby
  - Special events
  - Vehicle types
  - Competitor lot prices
- Starts at a base price of $10.
- Keeps price changes smooth and logical, not jumpy.
- Optionally suggests rerouting vehicles to nearby lots if one’s too full.

#### Must-Haves
- **Pricing Logic**: Code everything from scratch using Python, Pandas, NumPy, and Pathway.
- **Models**:
  - **Baseline Linear Model**: Price adjusts linearly with occupancy (e.g., more cars = higher price). It’s the starting point.
  - More complex models (not fully detailed in the PDF) should build on this, adding factors like traffic or competitor prices.
- **Real-Time Processing**: Use Pathway to handle data streams, process features live, and output prices continuously.
- **Environment**: Run it all in Google Colab.
- **Visuals**: Use Bokeh for real-time plots, like:
  - Price trends for each lot.
  - Comparisons with competitor prices.
  - Visuals should explain why prices change.
- **Submission**: Deliver a commented Colab notebook and a report covering:
  - Your steps and why you took them.
  - Details of all models.
  - Demand function, assumptions, and how prices react to demand/competition.
  - Bokeh plots with explanations.
- **Evaluation**: It’s a project, not a hackathon, so there’s no strict scoring—just make it clear and justified.

#### Resources
- Pathway guides: “From Jupyter to Deploy” and “First Real-Time App with Pathway.”
- Summer Analytics 2025 course page from the Consulting & Analytics Club.

---

### What’s in the Notebook?

The `IIT_Guwahati.ipynb` notebook lays the groundwork for the project with two main parts: setting up the environment and initializing a Bokeh visualization. Let’s break it down.

#### Part 1: Installing Libraries
The first cell installs the necessary tools:

```python
!pip install pathway pandas numpy bokeh haversine
```

- **What It Does**: Installs five libraries:
  - `pathway`: For real-time data streaming.
  - `pandas`: Data manipulation.
  - `numpy`: Math and number crunching.
  - `bokeh`: Interactive plots.
  - `haversine`: Distance calculations (likely for rerouting or comparing nearby lots).
- **Output**: Confirms installation with versions (e.g., `pathway==0.24.1`, `pandas==2.2.2`, etc., plus dependencies like `scikit-learn` and `google-cloud-pubsub`. Everything’s set up for Colab.

#### Part 2: Setting Up Visualization
The second cell imports libraries and starts Bokeh for plotting:

```python
import pandas as pd
import numpy as np
from haversine import haversine
import pathway as pw
from bokeh.plotting import figure, output_notebook, show
from bokeh.models import ColumnDataSource, Select, Slider, CustomJS
from bokeh.layouts import column, row

output_notebook()
```

- **What It Does**:
  - Imports Pandas, NumPy, Haversine, Pathway, and Bokeh modules for plotting (`figure`, `show`), interactivity (`ColumnDataSource`, `Select`, `Slider`, `CustomJS`), and layout (`column`, `row`).
  - Calls `output_notebook()` to make Bokeh plots show up inline in Colab.
- **Visualization Setup**:
  - Creates an interactive Bokeh plot (700x350 pixels) with a dropdown to pick a parking lot and a line graph comparing three price types:
    - **Baseline Price**: Solid line.
    - **Demand-Based Price**: Dashed line.
    - **Competitor-Based Price**: Dash-dot line.
  - The x-axis shows timestamps, the y-axis shows prices, and the title updates (e.g., “Lot BHMBCCMKT01 Pricing Comparison”).
  - A legend in the top-left labels the lines.
  - A JavaScript callback updates the plot when you pick a different lot from the dropdown (options like “BHMBCCMKT01”, “Broad Street”, or “Others-CCCPS105a”).
- **Callback Logic** (from the output, not visible as code but embedded in Bokeh’s JSON):
  - Filters data for the selected lot.
  - Updates the plot with timestamps and prices (baseline, demand, competitor).
  - Changes the title to match the lot.
  - Assumes a dataset with columns: `SystemCodeNumber` (lot ID), `timestamp`, `price_baseline`, `price_demand`, `price_competitor`.

#### What’s Not There
The notebook is just a starting point. It’s missing:
- **Data Loading**: No code to load `dataset.csv` or process it.
- **Pricing Logic**: No implementation of the baseline model or others.
- **Pathway Streaming**: No real-time data ingestion or prediction logic.
- **More Visuals**: Only one plot for one lot; needs visuals for all 14 lots or other metrics (e.g., occupancy).
- **Rerouting**: No logic for suggesting nearby lots.

#### Assumptions
- The dataset has columns like lot ID, timestamp, and prices (baseline, demand, competitor).
- Timestamps work with JavaScript’s Date for plotting.
- Prices are precomputed for now, not calculated.

---

### How to Build the Full Project

To make this work, you’ll need to flesh out the notebook based on the project requirements. Here’s the game plan:

1. **Load the Data**:
   - Use Pandas or Pathway to load `dataset.csv`.
   - Expect columns like `SystemCodeNumber` (lot ID), `timestamp`, `occupancy`, `queue_length`, `traffic_congestion`, `special_event`, `vehicle_type`, and `competitor_price`.

   ```python
   import pandas as pd
   df = pd.read_csv('dataset.csv')
   ```

2. **Baseline Linear Model**:
   - Start with a simple model: price increases with occupancy.
   - Example: `next_price = base_price + k * occupancy`, where `base_price = 10` and `k` is a constant (e.g., 0.5 for a $0.50 increase per occupied spot).

   ```python
   def baseline_price(previous_price, occupancy, max_capacity):
       k = 0.5  # Price increase per occupied spot
       return max(10, previous_price + k * (occupancy / max_capacity))
   ```

3. **Advanced Models**:
   - Build models factoring in traffic, events, vehicle types, or competitor prices. For example:
     - Increase prices by 20% on special event days.
     - Adjust based on competitor prices (e.g., set price slightly below nearby lots).

   ```python
   def advanced_price(occupancy, max_capacity, traffic, is_special_event, competitor_price):
       base = 10
       demand_factor = 0.7 * (occupancy / max_capacity)
       traffic_factor = 0.3 * traffic  # Traffic score (0 to 1)
       event_adjustment = 1.2 if is_special_event else 1.0
       price = base + (demand_factor + traffic_factor) * base * event_adjustment
       return min(price, competitor_price * 0.95)  # Stay competitive
   ```

4. **Pathway for Real-Time**:
   - Use Pathway to stream data and compute prices live.
   - Example: Ingest data, process features, and output prices.

   ```python
   import pathway as pw
   schema = pw.schema_from_csv('dataset.csv')
   data = pw.io.csv.read('dataset.csv', schema=schema)
   def compute_price(row):
       return baseline_price(row['previous_price'], row['occupancy'], row['max_capacity'])
   prices = data.select(price=compute_price(data))
   pw.io.csv.write(prices, 'output_prices.csv')
   ```

5. **Expand Visualizations**:
   - Add Bokeh plots for all 14 lots or metrics like occupancy.
   - Example: Plot occupancy alongside prices.

   ```python
   from bokeh.plotting import figure, show
   from b.constants import ColumnDataSource
   p = figure(width=700, height=350, x_axis_type='datetime')
   source = ColumnDataSource({'timestamp': df['timestamp'], 'occupancy': df['occupancy']})
   p.line('timestamp', 'occupancy', source=source, color='green')
   show(p)
   ```

6. **Rerouting (Optional)**:
   - Use Haversine to find nearby lots based on coordinates.
   - Example: Suggest a lot if occupancy > 80%.

   ```python
   from haversine import haversine
   def suggest_reroute(current_lot, lots, occupancy_threshold=0.8):
       current = lots[lots['SystemCodeNumber'] == current_lot]
       if current['occupancy'].iloc[0] / current['max_capacity'].iloc[0] > occupancy_threshold:
           nearby = lots[lots['SystemCodeNumber'] != current_lot]
           distances = nearby.apply(lambda row: haversine(
               (current['lat'].iloc[0], current['lon'].iloc[0]),
               (row['lat'], row['lon'])
           ), axis=1)
           return nearby[distances < 1]['SystemCodeNumber'].tolist()  # Lots within 1km
       return []
   ```

7. **Report**:
   - Document in the notebook with comments like:

     ```python
     # Baseline model: Increases price linearly with occupancy ratio
     # Assumption: Price starts at $10, adjusts smoothly up to max capacity
     ```

   - In the report, explain:
     - Demand function (e.g., occupancy-driven with traffic/event modifiers).
     - Assumptions (e.g., linear occupancy effect, $10 base).
     - Price response (e.g., rises with demand, capped by competitors).
     - Include Bokeh plots with captions.

---

### Wrapping Up
The notebook sets up the environment and a single interactive plot comparing baseline, demand, and competitor prices for one lot. It’s a solid start but needs work to:
- Load and stream data with Pathway.
- Implement pricing models (starting with linear).
- Add more plots for all lots and metrics.
- Optionally handle rerouting with Haversine.
Run it in Colab, and make sure your report and comments clearly explain your approach. Want me to zoom in on any part, like coding the pricing logic or tweaking the Bokeh plot? Just say the word!
