# This reprot is for assignment2 only 

##  AAE SATELLITE COMMUNICATION AND NAVIGATION ASSIGNMENT 2

## Task 1

**Genai: Deepseek R1**
**Prompt:please introduce the basic idea of DGNSS, inlcuding equations**

**Reason I use: free model and fantastic math ability, can connect to search for more information, can deep think**
### Differential GNSS (DGNSS): Basic Principles and Equations  
Differential GNSS (DGNSS) improves positioning accuracy by using a reference station at a known location to compute and broadcast error corrections to nearby users. Here’s a breakdown of its core principles, equations, and limitations:

---

#### **1. Core Idea**  
A **reference station** calculates errors by comparing its GNSS-measured position to its known true coordinates. These corrections (satellite clock, orbit, and atmospheric errors) are transmitted to users, who apply them to their own measurements. Errors are spatially correlated over short distances (typically <50 km), enabling sub-meter accuracy.

---

#### **2. Mathematical Framework**  

##### **2.1 Pseudorange Correction**  
The measured pseudorange $ \rho_{\text{measured}} $ at the reference station includes geometric distance and errors:  
$$  
\rho_{\text{measured}} = \rho_{\text{true}} + c(\delta t_{\text{sat}} - \delta t_{\text{rec}}) + I + T + \epsilon  
$$  
- $ \rho_{\text{true}} $: True geometric distance (known at the reference station).  
- $ c $: Speed of light.  
- $ \delta t_{\text{sat}}, \delta t_{\text{rec}} $: Satellite and receiver clock errors.  
- $ I, T $: Ionospheric and tropospheric delays.  
- $ \epsilon $: Measurement noise and multipath.  

The **pseudorange correction (PRC)** is calculated as:  
$$  
\text{PRC} = \rho_{\text{true}} - \rho_{\text{measured}}  
$$  
Users apply PRC to their own pseudorange measurements:  
$$  
\rho_{\text{user\_corrected}} = \rho_{\text{user}} + \text{PRC}  
$$  
This cancels spatially correlated errors (e.g., satellite clock, ionospheric delays).  

##### **2.2 Carrier-Phase Differential (RTK)**  
For centimeter-level accuracy, carrier-phase measurements $ \Phi $ are used:  
$$  
\Phi = \rho_{\text{true}} + c(\delta t_{\text{sat}} - \delta t_{\text{rec}}) + \lambda N + I - T + \epsilon  
$$  
- $ \lambda $: Carrier-phase wavelength.  
- $ N $: Integer ambiguity (unknown cycle count).  

The user performs **double differencing** across satellites ($ i, j $) and receivers (reference $ r $, user $ u $):  
$$  
\nabla \Delta \Phi = \Phi_u^i - \Phi_u^j - \Phi_r^i + \Phi_r^j  
$$  
This eliminates satellite/receiver clock errors and atmospheric delays, leaving:  
$$  
\nabla \Delta \Phi = \nabla \Delta \rho_{\text{true}} + \lambda \nabla \Delta N + \nabla \Delta \epsilon  
$$  
Solving $ \nabla \Delta N $ (integer ambiguity resolution) yields millimeter-level precision.  

---

#### **3. Limitations**  
- **Distance Dependency**: Corrections degrade with distance from the reference station (>50 km).  
- **Unmodeled Errors**: Multipath and receiver noise remain uncorrected.  
- **Latency**: Real-time corrections require stable communication links (e.g., radio, cellular).  

---

#### **4. Applications**  
- **Maritime Navigation**: Coastal positioning with <1 m accuracy.  
- **Precision Agriculture**: Sub-meter guidance for tractors.  
- **Surveying**: RTK-enabled centimeter-level land surveys.  

---

### Summary  
DGNSS uses reference stations to mitigate GNSS errors via pseudorange or carrier-phase corrections. While limited by distance and communication requirements, it remains a cornerstone of high-precision positioning for both consumer and professional applications.  
