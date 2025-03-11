# AAE6102 Assigment1 Report (HAI DI 23037537R)

## Task 1: Acquisition
The purpose of acquisition is to determine visible satellites and coarse value of **carrier phase** and **code phase** of the satellites signals.  

**code phase**: To generate a local PRN code to align with the incoming code.  

**carrier phase**: For downconversion (including doppler effect) 

Acquisition algorithm used here: **Parallel Code Phase search acquisition**  

**Step 1**: Generate carrier frequency with different doppler shift, the freqstep is 500 Hz here, the frequency range is -7k HZ to 7K hz.
```
  frqBins(frqBinIndex) = settings.IF - settings.acqSearchBand + ...
                                             0.5e3 * (frqBinIndex - 1);
```
**Step 2**：Parallel Code Phase search acquisition for one satellite with 20 ms data
```
  caCodeFreqDom = conj(fft(caCodesTable(PRN, :)));
......
  IQfreqDom1 = fft(I1 + 1i*Q1);
  IQfreqDom2 = fft(I2 + 1i*Q2);
......
 convCodeIQ1 = IQfreqDom1 .* caCodeFreqDom;
 convCodeIQ2 = IQfreqDom2 .* caCodeFreqDom;
......
   acqRes1 = abs(ifft(convCodeIQ1));
   acqRes2 = abs(ifft(convCodeIQ2));
```
**Step 3**：Setting the acquisition threshold and go to fine acquisition
### Acquisiton result from data open sky

**5 GPS Satellites are acquired with code delay and doppler shown below**
![image](https://github.com/user-attachments/assets/dfc85cc2-599e-4d19-a48a-a67c41c1d902)

GPS PRN 16 22 26 27 31 are acquired! The skyplot is shown below:

![image](https://github.com/user-attachments/assets/5ebe4e29-f3e7-4747-a05d-3d260cfa8b4f)

### Acquistion result from urban

** Four Satellites are acquired**

![image](https://github.com/user-attachments/assets/84d3597b-a623-4d48-a4a9-ba3a3e2e0afc)

Comapred to open-sky environments, less GPS satellites are acquired due to the blockage of buildings.

## Task 2: Tracking
### 2.1 Code Analysis
The motivation of tracking is to refine carrier frequency and code phase values, keep track.  

Code tracking: early,late prompt discriminator with spacing 0.5 chips

Calculate the correlator output
```
            I_E = sum(earlyCode  .* iBasebandSignal);
            Q_E = sum(earlyCode  .* qBasebandSignal);
            I_P = sum(promptCode .* iBasebandSignal);
            Q_P = sum(promptCode .* qBasebandSignal);
            I_L = sum(lateCode   .* iBasebandSignal);
            Q_L = sum(lateCode   .* qBasebandSignal);

```
Using Delay Lock Loop discriminator to adjust the code phase
```
 codeError = (sqrt(I_E * I_E + Q_E * Q_E) - sqrt(I_L * I_L + Q_L * Q_L)) / ...
                (sqrt(I_E * I_E + Q_E * Q_E) + sqrt(I_L * I_L + Q_L * Q_L));
```
### 2.2 Multi-correlator Gerneration

**To obtain the Autocorrelation function, multiple correlators must be implemented**
Here, multiple correlator with spacing 0.1 chip from -0.5 chip to 0.5 chip are applied
```
            tcode       = (remCodePhase-0.4) : codePhaseStep : ((blksize-1)*codePhaseStep+remCodePhase-0.4);
            tcode2      = ceil(tcode) + 1;
            earlyCode04    = caCode(tcode2);
            tcode       = (remCodePhase-0.3) : codePhaseStep : ((blksize-1)*codePhaseStep+remCodePhase-0.3);
            tcode2      = ceil(tcode) + 1;
            earlyCode03    = caCode(tcode2);
            tcode       = (remCodePhase-0.2) : codePhaseStep : ((blksize-1)*codePhaseStep+remCodePhase-0.2);
            tcode2      = ceil(tcode) + 1;
            earlyCode02    = caCode(tcode2);
            tcode       = (remCodePhase-0.1) : codePhaseStep : ((blksize-1)*codePhaseStep+remCodePhase-0.1);
            tcode2      = ceil(tcode) + 1;
            earlyCode01    = caCode(tcode2);
            tcode       = (remCodePhase+0.1) : codePhaseStep : ((blksize-1)*codePhaseStep+remCodePhase+0.1);
            tcode2      = ceil(tcode) + 1;
            lateCode01    = caCode(tcode2);
            tcode       = (remCodePhase+0.2) : codePhaseStep : ((blksize-1)*codePhaseStep+remCodePhase+0.2);
            tcode2      = ceil(tcode) + 1;
            lateCode02   = caCode(tcode2);
            tcode       = (remCodePhase+0.3) : codePhaseStep : ((blksize-1)*codePhaseStep+remCodePhase+0.3);
            tcode2      = ceil(tcode) + 1;
            lateCode03    = caCode(tcode2);
            tcode       = (remCodePhase+0.4) : codePhaseStep : ((blksize-1)*codePhaseStep+remCodePhase+0.4);
            tcode2      = ceil(tcode) + 1;
            lateCode04   = caCode(tcode2);

```

### 2.3 Tracking result from data open sky (PRN 16 as an Example)
![image](https://github.com/user-attachments/assets/9f877a1b-f4e7-4335-84ec-78406e80fb90)

The Q-channel tends to zero: Ideally, the Q-channel contains only noise and residual error, and its value fluctuates around zero， which verifies the carrier phase is aligned.

![image](https://github.com/user-attachments/assets/3861d8ca-689e-40c2-bd93-3bc4a3a47df4)

DLL output (Code Discriminator Output) near zero: indicates that the local code is aligned with the code phase of the received signal and the code tracking error is minimal.

![image](https://github.com/user-attachments/assets/5a1c4749-6351-4903-aa04-a4b4d573adaa)

PLL output (Phase/Frequency Discriminator Output) near zero: indicates that the local carrier is synchronized with the received signal carrier and the carrier tracking is stable.

![image](https://github.com/user-attachments/assets/e7c34ef3-821f-441e-8537-66dd71ca2b07)

The Prompt correlation is significantly greater than Early/Late, indicating that the code phases are precisely aligned and the DLL is in a stable tracking state.

**Autocorrelation Function from Multi-correlator output**
![image](https://github.com/user-attachments/assets/5feecdfe-e1bb-4038-b1a0-592dc2db1b07)

The shape of ACF is symmetric and undistorted, indicating the satellite is undistorted and not under the influence of multipath, which is in agreement with the experiment scenario (Open Sky);
**Above proves that the satellite in open sky is well acquired and tracked**

### Tracking result from data urban

## Task 3: Navigation data decoding (PRN 16 Open-Sky as an Example)
![image](https://github.com/user-attachments/assets/25346100-71d7-473d-af67-2d54fb2ac657)

This is the navigation data message decoded from incoming signal.

Key parameters extracted from navigation message.
**Ephemeris Data (31 parameters)**

![image](https://github.com/user-attachments/assets/8726c04c-6d76-4b80-94af-26dca388a5ef)

## Task 4: Position and velocity estimation
**Weighted least square for positioning**

**Elevation weighted**

```
     weight(i)=sin(el(i))^2;
......
    W=diag(weight);
    C=W'*W;
    x=(A'*C*A)\(A'*C*omc);
```

**Weighted least square for velocity**

```
%===To calculate receiver velocity=====HD
b=[];
lamda=settings.c/1575.42e6;
rate=-lamda*doppler;
rate=rate';
satvelocity=satvelocity';
for i=1:nmbOfSatellites
    b(i)=rate(i)-satvelocity(i,:)*(A(i,1:3))';
end
v=(A'*C*A)\(A'*C*b');
```

### The positioning result of open-air scenario is shown below, where the yellow dot is the ground truth
![image](https://github.com/user-attachments/assets/f9209b1d-1ba0-40dc-acae-dcfad9d21c58)

The weighted least squares (WLS) solution demonstrates **high accuracy** in open-air environments, exhibiting close alignment with ground truth measurements. This observed precision can be attributed to the absence of significant signal propagation impairments such as multipath interference and non-line-of-sight (NLOS) errors under unobstructed atmospheric conditions.

![image](https://github.com/user-attachments/assets/eae97665-a27f-470b-b9df-61c1d169205f)
![image](https://github.com/user-attachments/assets/b5f481d8-2eed-43a6-9e54-5564f1663bad)




The Velocity by WLS varies very significantly if no filtering.
## Task 5: Kalman-filter based positioning and velociy
Extended Kalman Filter is applied here
···
**state=[x,y,z,vx,vy,vz,dt,ddt](Position, velocity, clock error and clock drift)**
```
% prediction 
X_kk = F * X;
P_kk = F*P*F'+Q;
...
r = Z - h_x;
S = H * P_kk * H' + R;
K = P_kk * H' /S; % Kalman Gain

% Update State Estimate
X_k = X_kk + (K * r);
I = eye(size(X, 1));
P_k = (I - K * H) * P_kk * (I - K * H)' + K * R * K';
```

### The positioning result of EKF under open air.
![image](https://github.com/user-attachments/assets/4775e670-9c09-48aa-915e-b9680190e555)

Compared to traditional Weighted Least Squares (WLS), Kalman Filter-based positioning exhibits smoother trajectories with fewer abrupt jumps or outliers. This enhanced stability stems from the Kalman Filter’s inherent advantages in dynamic state estimation.
The Kalman Filter offers superior performance over Weighted Least Squares (WLS) by integrating temporal continuity, dynamic noise adaptation, and recursive state estimation, resulting in smoother, more stable positioning. Unlike WLS, which processes each epoch independently and is prone to measurement noise-induced jumps, the Kalman Filter leverages a state-space model to propagate estimates forward using motion dynamics (velocity and clock drift), while dynamically balancing process noise (Q) and measurement noise (R) to suppress outliers and model uncertainties. 

![image](https://github.com/user-attachments/assets/2e2dec31-5c69-4bdc-984a-d33901594efc)

The velocity after Extended Kalman Filter is alos well improved compared to WLS.

![image](https://github.com/user-attachments/assets/6ac7d05a-1bcb-46e9-aadc-746a770ceb2b)

