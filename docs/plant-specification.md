# Plant Specification: Solar-Thermal System Theory

## 1. Overview
This document outlines the technical and operational principles of a solar-thermal hot water heating system, based on general industry standards.

## 2. System Classification
A Solar water heating systems are classified based on their operation and fluid handling:

### 2.1. Operational Mode
*   **Active Systems:** that uses a pump to circulate the working fluid between a collector and a storage tank controlled by the temperature difference measured with a 1st sensor at the collector and a 2nd sensor just above a heat-exchanger which is in the bottom half of the storage tank. The system has a backup heating with an independen heat exchanger which is in the upper half of the storage tank. The control of the backup heating system is independent from the solar-thermal heating system and operates when a lower temperature threshold in the upper half of the storage system is reached, provided that the backup heating is on-line.

They are more expensive but offer higher efficiency and precise control, allowing for integration with backup heating systems.

### 2.2. Fluid Handling
*   **Indirect (Closed Loop):** A separate heat-transfer fluid (HTF), typically a water/antifreeze mix (e.g., propylene glycol), is circulated through the collectors. Heat is then transferred to the potable water via a heat exchanger. This offers superior freeze and overheat protection.
    *   **closed loop, hydraulic properties** the closed loop consists of a pair of thermo-insulated pipes with 16mm inner diameter and 10m length. The pump circulates the HTF in 40s which, may lead to observable oszillations in the temperature of the fluid in the collector. The HTF volume in the collector can be inferred from the observed oszillations, especially from the time it takes for the solar radiation to heat up cold fluid from the pipe in the collector.

## 3. Collector Technology

### 3. Flat Plate Collector Technology
The primary components for heat capture flat plate collector:
**Design:** An insulated box with a glass cover and an absorber plate (copper fins).

## 4. System Management and Safety
*   **Heat Transfer Fluid (HTF) with Freeze Protection:**
    *   **Antifreeze:** Using fluids like propylene glycol prevents freezing
*   **Overheat Protection:**
    *   **Controllers/Valves:** an active system uses the controller and safety valves to prevent excessive temperatures, which can degrade the HTF or damage components. The cutoff temperature for heating the water in the storage tank is 80C, measured with the storage temperature sensor.
    *   **Glycol/Water Mix:** Used in indirect systems for freeze protection.

## 5. Data Acquisition Considerations
For accurate RL parameter identification, the following data points are critical:
*   **Collector Temperature:** Inlet and outlet temperatures from the Flat Plate Collector.
*   **Storage Temperature:** Temperature measured by:
    * the storage temperature sensor just above the heat exchanger (for pump control), and
    * the at storage tank outlet sensor.
*   **Pump Status:** Binary state (ON/OFF) of the circulation pump.
*   **Control Status:** System setpoints (on - off with 5C deadband) and auxiliary heater activity status.
    * the auxiliary heater gets active when the temperature at the storage tank outlet sensor drops below 40C.

## 6. Inferred Data
*   **Inferred Irradiance:** Estimated solar radiation based on time-of-day, weather, and seasonal models.
*   **Inferred Storrage Heat Loss:** Estimated heat loss based on ambient temperature (10C to 30C).
*   **Inferred Hot Water Consumption** ussage depending on time-of-day (e.g., shower) and current temperature in the upper half of the storage (less wate is needed for a shower rwhen the water is very hot)
*   **Inferred Storage Temperature Gradient** storage water temperature of the vertical storage tank caused by gravitational effects based on geometry data (hight, volume).
*   **Inferred Pipe Transport Delay** from differences of the temperature of the HTF in the pipe as indicated by fluctuations of the collector temperature.
