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

GPS PRN 16 22 26 27 31 are acquired!

### Acquisiton result from data ubrn

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

Key parameters extracted from navigation message,
## Task 4: Position and velocity estimation

## Task 5: Kalman-filter based positioning
