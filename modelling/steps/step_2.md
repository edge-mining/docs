## Identify Entities and Value Objects within Each Subdomain

- 🟢 **Energy Optimization & Mining Automation** (Core):
    - `OptimizationPolicy` (Entity? Aggregate Root?): A collection of rules and goals driving the automation decisions. It makes decisions based on input (e.g. "*stabilizes hashrate*", "*battery health*" or "*heats the room*").
    - `AutomationRule` (Entity? Maybe Value Object within a Policy?): Represents a user-defined or system rule (e.g., "*turn on if battery > 80% AND forecast > X*"). The *logic* itself.
    - `EnergyStateSnapshot` (Value Object): Represents the state of the energy system at a point in time (*production*, *consumption*, *battery level*, *forecast*). Used as input for decisions.
    - `MiningDecision` (Value Object): The output of the policy (e.g., `StartMining`, `StopMining`, `MaintainState`, `ChangeState`).
    - `ForecastData` (Value Object): Represents the relevant solar/energy forecast.
- 🟣 **Energy System Monitoring** (Supporting):
    - `EnergyPlant` (Entity or Aggregate Root): Represents the overall energy production system (is it necessary? maybe implicit or it can be an energy source container?).
    - `EnergySource` (Entity): e.g., Solar Panel Array, Wind Turbine. Has properties like `CurrentProduction` (VO).
    - `EnergyStorage` (Entity): e.g., Battery. Has properties like `StateOfCharge` (VO), `NominalCapacity` (VO), `ActualCapacity` (VO).
    - `EnergyLoad` (Entity): Represents the user's main energy load. Contains `CurrentConsumption` (VO)
    - `EnergyReading` (Value Object): A specific measurement (e.g., 5 kW production at timestamp T).
- 🟣 **Mining Device Management** (Supporting):
    - `Miner` (ASIC) (Entity): Represents a physical mining device. Has a `MinerId` (VO), `Status` (VO: *On, Off, Error*), maybe `PowerConsumption` (VO).
    - `MiningFarm` (Entity? Aggregate Root? Optional): If managing multiple miners as a group.
    - `ControlCommand` (Value Object): e.g., `TurnOn`, `TurnOff`, `SetHashboard`.
- 🟣 **Heat Utilization** (Supporting):
    - (Potentially) `HeatOutput` (Value Object?): Associated with a Miner's operation.
    - (Potentially) `HeatingZone` (Entity?): If actively managing heat distribution.
    - (Potentially) `TemperatureSensor` (Entity?): To monitor effectiveness. (Restricting the analysis to current needs alone may be an overreach, since heat is not a primary concern).
- 🟣 **Mining Performance Analysis** (Supporting):
    - `MiningSession` (Entity?): Represents a period when a miner was active.
    - `MiningReward` (Value Object): Satoshi earned.
    - `HashRate` (Value Object): Mining speed.
    - `PoolConnectionDetails` (Value Object/Entity?): Details about the mining pool being used.
- 🟣 **Home Consumption Analytics** (Supporting):
    - `HomeLoadsProfile` (Aggregate Root): Represents the typical energy load profile of a household. It encapsulates the knowledge of how energy is consumed in the domestic environment over time.
    - `LoadDevice` (Entity): It represents a specific energy load inside the home (e.g. Dishwasher, Boiler, EV Charger). It has `ConsumptionForecast` that is a forecast of energy consumption for a given future period.
- 🟣 **User Configuration & Interaction** (Supporting):
    - `User` (Entity): The system user (initially admin only).
    - `SystemSettings` (Entity/VO): Global or specific settings (e.g. thresholds, goals).
    - `NotificationPreference` (VO): How, when and which notifications to receive.
- 🟣 **External Integrations** (Supporting):
    - `ForecastProvider` (Interface/Adapter): Represents the connection to a weather/solar forecast service.
    - (Potentially) `GridDataService` (Interface/Adapter): Potential future integration for grid prices.

## Define Relationships Between Subdomains (Context Mapping)

- **Energy System Monitoring** ➡️ **Energy Optimization & Mining Automation**: Provides `EnergyStateSnapshot` (data).
- **External Integrations** (Forecast) ➡️ **Energy Optimization & Mining Automation**: Provides `ForecastData`.
- **User Configuration & Interaction** ➡️ **Energy Optimization & Mining Automation**: Provides user goals/parameters for `OptimizationPolicy`.
- **Energy Optimization & Mining Automation** ➡️ **Mining Device Management**: Sends `ControlCommand` (e.g., `TurnOn` Miner X).
- **Mining Device Management** ➡️ **Energy Optimization & Mining Automation**: Reports `Miner`'s' `Status` (e.g., successfully turned on, error).
- **Mining Device Management** ➡️ **Mining Performance Analysis**: Provides data on miner uptime, possibly hash rate if monitored directly.
- **External Integrations** (Mining Pool API) ➡️ **Mining Performance Analysis**: Provides `MiningReward` data.
- **Home Consumption Analytics** ➡️ **Energy Optimization & Mining Automation**: Provides sum of `ConsumptionForecast`.
- **Energy System Monitoring**, **Mining Device Management**, **Mining Performance Analysis**, **Heat Utilization** ➡️ **User Configuration & Interaction**: Provides data for display to the user.