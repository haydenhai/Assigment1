# This reprot is for assignment2 only 

##  AAE SATELLITE COMMUNICATION AND NAVIGATION ASSIGNMENT 2

# Task 1: Differential GNSS positioning

**Genai: Deepseek R1**

**Prompt 1:please introduce the basic idea of DGNSS, RTK, PPP, PPP-RTK, inlcuding equations, applications and limitations**

**Prompt 2:please tell me the difference of application among them, which one is widely used in which areas, which can not and why**

**Prompt 3: the difference between them, some of them use double difference and requiring base station**

**Prompt 4: the application in smartphone navigation**

**Prompt 5: pros and cons of each of them**

**Reason I use: free model and fantastic math ability, can connect to search for more information, can deep think**

Differential GNSS (DGNSS) enhances positioning accuracy by leveraging a reference station at a known location to compute and broadcast error corrections to nearby users. The reference station calculates discrepancies between its GNSS-derived position and its true coordinates, generating corrections for spatially correlated errors like satellite clock/orbit deviations and ionospheric/tropospheric delays. These corrections are applied to the user’s measurements, improving accuracy to sub-meter or centimeter levels. The measured pseudorange $` \rho_{\text{measured}} `$ at the reference station is modeled as:  

```math
 \rho_{\text{measured}} = \rho_{\text{true}} + c(\delta t_{\text{sat}} - \delta t_{\text{rec}}) + I + T + \epsilon  
```

where $` \rho_{\text{true}} `$ is the true geometric distance, $` c `$ is the speed of light, $` \delta t_{\text{sat}} `$ and $` \delta t_{\text{rec}} `$ are satellite/receiver clock errors, $` I `$ and $` T `$ are ionospheric and tropospheric delays, and $` \epsilon `$ represents noise. The **pseudorange correction (PRC)** is calculated as $` \text{PRC} = \rho_{\text{true}} - \rho_{\text{measured}} `$, which users apply to their own measurements:  

```math
\rho_{\text{user\_corrected}} = \rho_{\text{user}} + \text{PRC}.  
``` 
However, DGNSS is limited by distance-dependent error decorrelation (>50 km), unmodeled multipath/noise, and reliance on real-time communication links. It is widely used in maritime navigation, precision agriculture, and surveying, where sub-meter to centimeter accuracy is critical.  

Real-Time Kinematic (RTK) is a high-precision GNSS positioning technique that achieves centimeter-level accuracy by using carrier-phase measurements from a fixed base station to correct errors in a rover receiver's signals. The base station calculates real-time corrections for satellite orbit/clock errors and atmospheric delays, transmitting them to the rover via radio or cellular link. The rover then performs **double differencing** of carrier-phase measurements between satellites and receivers (e.g., $`\nabla\Delta\Phi = \nabla\Delta\rho + \lambda\nabla\Delta N + \nabla\Delta\epsilon`$), resolving integer ambiguities ($`\nabla\Delta N`$) to determine precise relative positions. While RTK requires short baselines (<10 km) and stable communication, it enables applications like surveying, precision agriculture, and autonomous vehicle navigation where sub-meter GPS alone is insufficient.

Precise Point Positioning (PPP) is a GNSS positioning technique that achieves decimeter-to-centimeter level accuracy without requiring a nearby base station. Unlike differential methods, PPP uses precise satellite orbit and clock corrections (typically from global networks like IGS) along with advanced error modeling to compensate for atmospheric delays and other biases. The technique processes dual-frequency measurements to eliminate ionospheric errors and employs carrier-phase ambiguity resolution (e.g., $'\Phi = \rho + c(\delta t_r - \delta t^s) + T + \lambda N + \epsilon'$) for high precision. While PPP has global coverage and doesn't need local infrastructure, it requires longer convergence times (15-30 minutes) and is sensitive to signal interruptions. This makes it particularly valuable for applications in remote areas, marine navigation, and scientific research where traditional RTK/DGNSS is impractical.

PPP-RTK (Precise Point Positioning-Real Time Kinematic) combines the advantages of PPP's global coverage and RTK's fast convergence to deliver centimeter-level positioning accuracy worldwide. This hybrid technique utilizes precise satellite orbit/clock corrections (like PPP) while also incorporating atmospheric delay models and ambiguity resolution techniques (like RTK) through state-space representation (SSR) corrections transmitted via satellite or internet. By applying these corrections to dual-frequency measurements (e.g., $'\nabla\Delta\Phi = \nabla\Delta\rho + \lambda\nabla\Delta N + \nabla\Delta\epsilon_{atm}'$), PPP-RTK achieves rapid ambiguity resolution (1-5 minutes) and maintains accuracy even when moving far from reference stations. The method is particularly valuable for autonomous vehicles, precision agriculture, and marine applications where both global availability and high precision are required, though it depends on reliable correction data streams and advanced receiver processing capabilities.

| Technique | Accuracy  | Typical Applications                  | Limitations                          | When Not Suitable                     |
|-----------|-----------|---------------------------------------|--------------------------------------|---------------------------------------|
| **DGNSS** | 0.5-2 m   | Maritime navigation, basic surveying  | Degrades beyond 50 km from reference | Remote areas without reference stations |
| **RTK**   | 1-5 cm    | Surveying, precision agriculture      | Requires nearby base station (<10 km)| Areas without local CORS network       |
| **PPP**   | 10-30 cm  | Oceanic navigation, scientific research | Slow convergence (15-30 min)        | Real-time applications, urban canyons  |
| **PPP-RTK** | 2-5 cm | Autonomous vehicles, global mapping   | Needs stable SSR correction stream   | Areas without satellite/cellular coverage |

The key differences between these GNSS techniques lie in their error correction methods and infrastructure requirements. **RTK and DGNSS** rely on **double differencing** (e.g., $`\nabla\Delta\Phi = \nabla\Delta\rho + \lambda\nabla\Delta N`$) between a rover and nearby base station (<10 km for RTK, <50 km for DGNSS) to eliminate common errors, making them dependent on local reference stations. In contrast, **PPP** uses precise orbit/clock products without base stations but suffers from slow convergence, while **PPP-RTK** hybridizes both approaches: it applies state-space corrections (like PPP) but also resolves ambiguities using atmospheric models (like RTK), enabling global coverage with faster convergence than PPP. Base station-free PPP excels in remote areas, whereas RTK/DGNSS fail without local infrastructure, and PPP-RTK struggles where correction networks are unavailable.

| Feature       | Uses Double Differencing | Requires Base Station | Max Range from Base |
|---------------|--------------------------|-----------------------|---------------------|
| **DGNSS**     | ❌ (code-based)          | ✅                    | 50 km               |
| **RTK**       | ✅ (carrier-phase)       | ✅                    | 10 km               |
| **PPP**       | ❌                       | ❌                    | Global              |
| **PPP-RTK**   | ⚠️ (SSR-aided)          | ❌*                   | Global*             |

Modern smartphones leverage **RTK**, **PPP**, and **PPP-RTK** GNSS techniques to enhance navigation accuracy, though their applications vary significantly in performance and practicality. **PPP** on dual-frequency smartphones (e.g., Xiaomi Mi 8) achieves ~20–40 cm static accuracy after ~100 minutes of convergence but suffers from kinematic fluctuations (3–5 m errors) due to low-cost antennas and noisy measurements. **PPP-RTK**, combining PPP's global coverage and RTK's rapid ambiguity resolution, reduces convergence to 1–5 minutes using state-space corrections and regional atmospheric models, enabling decimeter-level kinematic accuracy even with smartphone-grade hardware. For instance, PPP-RTK integrated with low-cost helical antennas achieves rapid integer ambiguity resolution, making it viable for lane-level automotive navigation. **RTK** remains less common in smartphones due to strict base station proximity requirements (<10 km), though experimental setups show sub-meter accuracy in controlled environments. Challenges persist in urban canyons where multipath and signal blockages degrade all methods, necessitating fusion with inertial sensors (e.g., IMU-aided PPP/INS models) to maintain <2 m accuracy in real-world driving scenarios. While PPP-RTK emerges as the most promising for mass-market adoption (privacy-preserving, global L-band corrections), its reliance on stable SSR streams and computational demands highlight ongoing tradeoffs between precision, latency, and smartphone hardware limitations.

### **GNSS Techniques for Smartphones: Pros and Cons**

| Technique | Pros | Cons |
|-----------|------|------|
| **DGNSS** | ✅ Low computational load<br>✅ Works with single-frequency receivers<br>✅ Real-time corrections (1-2m accuracy) | ❌ Limited range (<50km from base station)<br>❌ Degrades in urban canyons<br>❌ Doesn't resolve carrier-phase ambiguities |
| **RTK** | ✅ Centimeter-level accuracy (1-5cm)<br>✅ Fast initialization (seconds-minutes)<br>✅ Works with smartphone-grade antennas | ❌ Requires nearby base station (<10km)<br>❌ Signal interruptions cause re-initialization<br>❌ High cellular data usage for corrections |
| **PPP** | ✅ Global coverage (no base stations needed)<br>✅ Decimeter-level accuracy (10-30cm)<br>✅ Better for remote areas | ❌ Slow convergence (15-30+ minutes)<br>❌ Requires dual-frequency smartphones<br>❌ Sensitive to signal outages |
| **PPP-RTK** | ✅ Fast convergence (1-5 minutes)<br>✅ Global + centimeter-level accuracy<br>✅ Works with SSR corrections (e.g., via L-band satellites) | ❌ High processing power needed<br>❌ Dependent on correction networks<br>❌ Limited smartphone compatibility |

# Task 2: GNSS in urban area

**According to the feedback of assignment1, the acquisiton process is replaced with a more sensitive acquisition code**

**using new acquisiton code, 6 GPS satellites in total can be acquired and tracked**

**satellites that can be acquired are showned below**
![image](https://github.com/user-attachments/assets/36640ab8-ceed-4412-aa39-1e9b8626fb96)

**the original weighted least square positioning result based on elevation angle**

![image](https://github.com/user-attachments/assets/4576eeaf-b2a4-4553-924f-813116ffd992)

In urban environments with sky masking, GNSS positioning is often limited by severe signal obstruction, reducing the number of visible satellites to as few as six. Under such conditions, outright exclusion of NLOS signals—typically caused by reflections off buildings or other structures—would further degrade satellite availability, potentially leading to positioning failures. To address this, the NLOS deweighting method is adopted instead of NLOS exclusion.

**Deweighting when it is classified as NLOS**
```
            if settings.hasSkymask 
                if el(i)>skymask(floor(az(i)),2)
                    weight(i)=10;
                else
                    weight(i)=cosd(abs(el(i)-skymask(floor(az(i)),2)));
                end
            end
```
Satellites identified as NLOS are assigned reduced weights in the positioning solution rather than being discarded entirely. This approach minimizes their contribution to the navigation fix while maintaining geometric diversity, thereby improving robustness in challenging urban canyons. The deweighting factor can be dynamically adjusted based on the elevation angle difference between satellite and building boundary.

Compared to exclusion, this method provides a more balanced trade-off between mitigating multipath errors and retaining sufficient satellite observations for reliable positioning.

The result is ploted below

![image](https://github.com/user-attachments/assets/19425ef5-fb9a-478c-be4a-e14e87df1139)

Error comparison
| **Metric**               | **WLS** | **WLS with NLOS deweighting** |
|---------------------------|-------------------------|-------------------------|
| **Positioning Error**  | 70 m                 | 42 m                 |

