# AAE6102 Assigment1 Report (HAI DI 23037537R)

## Task 1: Acquisition
The purpose of acquisition is to determine visible satellites and coarse value of **carrier phase** and **code phase** of the satellites signals.  

**code phase**: To generate a local PRN code to align with the incoming code.  

**carrier phase**: For downconversion (including doppler effect) 

Acquisition algorithm used here: **Parallel Code Phase search acquisition**  

**Step 1**: Generate carrier frequency with different doppler shift, the freqstep is 500 Hz here, the frequency range is -10k HZ to 10K hz.
```
for freqband = 1 : acq.freqNum
    dopplershift = acq.freqMin + acq.freqStep*(freqband-1);
    carrier(freqband,:) = exp(1i*2*pi*(signal.IF + dopplershift) * sampleindex ./ signal.Fs);  
end
```
**Step 2**：For each satellite, Generate C/A code  
**Step 3**：Parallel Code Phase search acquisition for one satellite with 20 ms data
```
    for idx = 1 : 20  % 20ms data to accumulate 
        for freqband = 1 : acq.freqNum %parallel code phase
            replica = scode;
            temp1 = rawsignal(1+(idx-1)*signal.Sample:idx*signal.Sample).* carrier(freqband,:);
            temp2 = conj(fft(temp1));
            temp3 = fft(replica);
            correlation(freqband,:) = correlation(freqband,:) + abs(ifft(temp3.*temp2)).^2;
        end
    end

    [peak, fbin] = max(max(abs(correlation'))); %the index of doppler shift
    [peak, codePhase] = max(max(abs(correlation))); %the index of code phase
    Doppler = acq.freqMin + acq.freqStep * (fbin-1);
    codechipshift = ceil(signal.Fs/signal.codeFreqBasis);
```
**Step 4**：Setting the acquisition threshold, here the threshold is over 15 db-hz
```
  if SNR >= 15  % acquisition thredhold
        Acquired.sv        = [Acquired.sv        svindex];
        Acquired.SNR       = [Acquired.SNR       SNR];
        Acquired.Doppler   = [Acquired.Doppler   Doppler];
        Acquired.codedelay = [Acquired.codedelay codePhase-1];
  end
```
### Acquisiton result from data open sky

**9 GPS Satellites are acquired with code delay and doppler shown below**
![image](https://github.com/user-attachments/assets/246f0812-814f-4198-9c3e-8b1372ba4778)

### Acquisiton result from data ubrn

## Task 2: Tracking
The motivation of tracking is to refine carrier frequency and code phase values, keep track.  

Code tracking: early,late prompt discriminator with spacing 0.5 chips
```
       CodeEarly      = Code(svindex,(ceil(t_CodeEarly) + indx));
       CodePrompt     = Code(svindex,(ceil(t_CodePrompt) + indx));
       CodeLate       = Code(svindex,(ceil(t_CodeLate) + indx));
```
Calculate the correlator output
```
        E_i  = sum(CodeEarly    .*InphaseSignal);
        E_q = sum(CodeEarly    .*QuadratureSignal);
        P_i  = sum(CodePrompt   .*InphaseSignal);
        P_q = sum(CodePrompt   .*QuadratureSignal);
        L_i  = sum(CodeLate     .*InphaseSignal);
        L_q = sum(CodeLate     .*QuadratureSignal);
```
Using Delay Lock Loop discriminator to adjust the code phase
```
        E	= sqrt(E_i^2+E_q^2);
        L	= sqrt(L_i^2+L_q^2);
        codeError(svindex)	= -0.5*(E-L)/(E+L); 
```

**To obtain the Autocorrelation function, multiple correlators must be implemented**

Here, multiple correlator with spacing 0.05 chip from -0.5 chip to 0.5 chip are applied
### Tracking result from data open sky
### Tracking result from data urban

## Task 3: Navigation data decoding

## Task 4: Position and velocity estimation
## Task 5: Kalman-filter based positioning
