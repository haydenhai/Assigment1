# This reprot is for assignment2 only 

##  AAE SATELLITE COMMUNICATION AND NAVIGATION ASSIGNMENT 2

## Task 1

**Genai: Deepseek R1**

**Prompt 1:please introduce the basic idea of DGNSS, RTK, PPP, PPP-RTK, inlcuding equations, applications and limitations**

**Reason I use: free model and fantastic math ability, can connect to search for more information, can deep think**

Differential GNSS (DGNSS) enhances positioning accuracy by leveraging a reference station at a known location to compute and broadcast error corrections to nearby users. The reference station calculates discrepancies between its GNSS-derived position and its true coordinates, generating corrections for spatially correlated errors like satellite clock/orbit deviations and ionospheric/tropospheric delays. These corrections are applied to the user’s measurements, improving accuracy to sub-meter or centimeter levels. The measured pseudorange $ \rho_{\text{measured}} $ at the reference station is modeled as:  
$$  \rho_{\text{measured}} = \rho_{\text{true}} + c(\delta t_{\text{sat}} - \delta t_{\text{rec}}) + I + T + \epsilon  $$  
where $ \rho_{\text{true}} $ is the true geometric distance, $ c $ is the speed of light, $ \delta t_{\text{sat}} $ and $ \delta t_{\text{rec}} $ are satellite/receiver clock errors, $ I $ and $ T $ are ionospheric and tropospheric delays, and $ \epsilon $ represents noise. The **pseudorange correction (PRC)** is calculated as $ \text{PRC} = \rho_{\text{true}} - \rho_{\text{measured}} $, which users apply to their own measurements:  
$$  
\rho_{\text{user\_corrected}} = \rho_{\text{user}} + \text{PRC}.  
$$  
For higher precision, carrier-phase measurements $ \Phi $ are used:  
$$  
\Phi = \rho_{\text{true}} + c(\delta t_{\text{sat}} - \delta t_{\text{rec}}) + \lambda N + I - T + \epsilon,  
$$  
where $ \lambda $ is the carrier wavelength and $ N $ is the integer ambiguity. Users perform **double differencing** ($ \nabla \Delta \Phi $) across satellites and receivers to eliminate common errors, yielding:  
$$  
\nabla \Delta \Phi = \nabla \Delta \rho_{\text{true}} + \lambda \nabla \Delta N + \nabla \Delta \epsilon.  
$$  
Resolving $ \nabla \Delta N $ enables centimeter-level accuracy. However, DGNSS is limited by distance-dependent error decorrelation (>50 km), unmodeled multipath/noise, and reliance on real-time communication links. It is widely used in maritime navigation, precision agriculture, and surveying, where sub-meter to centimeter accuracy is critical.  
