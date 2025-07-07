# Dynamic Pricing for Urban Parking Lots

## Project Overview

This project implements a dynamic pricing engine for 14 urban parking lots using real-time data. The system uses features such as occupancy, queue length, traffic congestion, special day indicators, and vehicle type to set parking prices that reflect current demand. The goal is to optimize utilization and revenue while ensuring fair and explainable pricing for users.

## Tech Stacks Used

- Python 3.x
- pandas, numpy
- Bokeh (visualization)
- (Documented) Pathway (for real-time streaming)
- Jupyter Notebook / Google Colab

## Architecture Diagram

''''mermaid
flowchart TD
    A[Parking Data Source] --> B[Preprocessing & Feature Engineering]
    B --> C{Model Selection}
    C -- Baseline Model --> D[Occupancy-based Pricing]
    C -- Demand Model --> E[Multi-Feature Demand Pricing]
    D --> F[Price Output]
    E --> F
    F --> G[Visualization (Bokeh)]
    F --> H[Export Results]
    F --> I[Pathway Streaming (Optional)]
```

## Project Architecture and Workflow

- **Data Ingestion:**  
  Loads parking data with features: occupancy, capacity, queue length, traffic level, special day, vehicle type, and timestamp.

- **Preprocessing:**  
  - Maps categorical features to numeric values.
  - Combines date and time into a single datetime column.
  - Engineers features like hour_fraction for time-of-day effects.
  - Handles missing or invalid data.

- **Model Selection:**  
  - **Baseline Linear Model:** Adjusts price based solely on occupancy rate.
  - **Demand-Based Model:** Uses a weighted sum of occupancy, queue, traffic, special day, vehicle type, and hour_fraction, with normalization and clamping for smooth, bounded prices.

- **Simulation:**  
  Processes data chronologically for each parking lot, updating and recording prices at every time step.

- **Visualization:**  
  Interactive Bokeh plots for each lot, showing price evolution and occupancy trends.

- **Export:**  
  Saves simulation results to CSV files for further analysis.

- **(Optional) Real-Time Streaming:**  
  Pathway integration for true real-time data ingestion and continuous price prediction.

## Detailed Explanation of Project Architecture and Workflow

### Baseline Linear Model

- Adjusts price up or down based on occupancy rate.
- Formula:  
  P_{t+1} = P_t + alpha.({Occupancy}/{Capacity} - 0.5) 
- Price is clamped between 0.5× and 2× the base price.

### Demand-Based Model

- Computes a weighted sum of:
  - Occupancy rate
  - Queue length (normalized)
  - Traffic level
  - Special day indicator
  - Vehicle type weight
  - Hour fraction (time of day)
- Normalizes demand using tanh for smoothness.
- Updates price:  
  $$ \text{Price}_t = \text{Base Price} \times (1 + \lambda \cdot (\text{Normalized Demand} - 0.5)) $$
- Price is clamped to [0.5×base, 2×base].

### Real-Time Simulation

- Each parking lot is simulated independently.
- Data is processed in chronological order to mimic real-time updates.
- Results are visualized and exported for analysis.

## Any Other Relevant Documentation

- **Assumptions:**
  - All model coefficients are hand-tuned for initial deployment.
  - Unknown vehicle types default to a weight of 1.0.
  - Data-driven normalization is used for queue length.
  - Prices are always clamped to prevent extreme values.
  - Rows with missing or invalid data are skipped.

- **Limitations:**
  - No competitive pricing (Model 3) or rerouting logic implemented.
  - Batch simulation is used; Pathway streaming is documented but not fully deployed.
  - Model parameters are not data-optimized.

- **Future Work:**
  - Integrate competitive pricing and rerouting logic.
  - Tune coefficients using historical data and cross-validation.
  - Deploy the full pipeline with Pathway for live, real-time updates.

- **References:**
  - Pathway documentation for real-time streaming pipelines.
  - Bokeh documentation for interactive visualization.
