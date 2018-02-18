# IFT372 Rayleigh Channel Activity 3 
## Nicholas Finch

1. At the command prompt, enter help, select comm/comm., Channels, rayleighchan. Also, see doc rayleighchan.
What is another channel function available besides rayleighchan? 

>comm.AWGNChannel            - Add white Gaussian noise to input signal
>comm.BinarySymmetricChannel - Introduce binary errors
>comm.RicianChannel          - Filter input through a multipath Rician fading SISO channel
>comm.MIMOChannel            - Filter input through a multipath fading MIMO channel
>comm.LTEMIMOChannel         - Filter input through an LTE specific multipath fading MIMO channel
>awgn                        - Add white Gaussian noise to a signal
>bsc                         - Model a binary symmetric channel
>stdchan                     - Construct a channel object from a set of standardized channel models


2.	Go to doc rayleighchan available at communications toolbox. State what function properties are writeable and which are read-only.

```
The Rayleigh fading channel object has the following properties:
ChannelType: 'Rayleigh'
This is Read-only.
InputSamplePeriod: Input signal sample period (s)
DopplerSpectrum: Doppler spectrum object(s)
MaxDopplerShift: Maximum Doppler shift (Hz)
PathDelays: Discrete path delay vector (s)
AvgPathGaindB: Average path gain vector (dB)
NormalizePathGains: Normalize path gains (0 or 1)
StoreHistory: Store channel state information (0 or 1)
StorePathGains: Store current complex path gain vector (0 or 1)
PathGains: Current complex path gain vector
This is Read only.
ChannelFilterDelay: Channel filter delay (samples)
This is Read-only.
ResetBeforeFiltering: Resets channel state every call (0 or 1)
NumSamplesProcessed: Number of samples processed
This is Read-only.
```

3.	Enter the following code:
```
ts= 1e-3;
fd=100;
ral=rayleighchan(ts,fd); ral
```

what do 1e-3, and 100 indicate?

>InputSamplePeriod
>MaxDopplerShift

What parameters of the channels are displayed? 

```
InputSamplePeriod: 1.0000e-03
DopplerSpectrum: [1x1 doppler.jakes]
MaxDopplerShift: 100
NormalizePathGains: 
PathGains: 1.1157 – 0.2778i
ResetBeforeFiltering: 1
```

Why is PathDelays = 0? 

>Only one discrete path

What type of Rayleigh channel did you generate, flat or frequency selective?

> frequency-flat, because our value is a scalar

Vary the input sampling period: InputSamplePeriod, by entering the following: 

```
ral.InputSamplePeriod= 1 e-6 
```

What changes do you observe?	

```
InputSamplePeriod: 1.0000e-06
PathGains: -0.3628 + 0.3997i
```

Next, we shall plot a channel's power. To do so, we will have to filter a random signal with the generated channel. First, generate the random signal, and then use the filter function as shown.

```
m=30000;	
n=1;
x=randi([0,1],m,n);
y=filter(ral,x);
plot(20*log10(abs(y)))
```

Save your plot.

![](https://i.imgur.com/mzzqDi9.png)

When did the deep fades occur? 

>t = 0.4, 1.8, and 2.9

What is the implication of this on system performance? 

> signal loss and bad quality

How does n, differ from ts?

> n is the period, and ts is the sampling rate.

6.	Modify the channel properties such that `ts=1e-5` and `fd = 120`. Verify parameters have been modified by `ral`

7.	Repeat filter operation.
Save your new plot.

![](https://i.imgur.com/F6lbqcx.png)

How does the new plot compare with the previous?

> Drop offs increased and channel worsened

8.	Now observe the channel properties by typing ral and Enter. What difference do you observe?

```
PathGains: -0.2251 + 0.2509i
```

9.	Reduce the maximum Doppler shift to `50`, while holding all other parameters constant by entering the following code:

```
fd=50;
ral=rayleighchan(ts,fd);
y=filter(ral,x);
figure;
plot(20*log10(abs(y)))
```

10.	Repeat steps 7 and 8. 

![](https://i.imgur.com/cKN7m0i.png)

`PathGains: -0.8364 + 0.5100i`


## Part III: Multipath Rayleigh Channel:

1.	Generate a three path Rayleigh channel by entering the following code:
```
ts=1e-5;
fd=120;
tau=[0, 1e-5, 1e-6];
ral=rayleighchan(ts,fd,tau); ral
```

What difference do you notice, as far as the AvgPathGaindB parameter is concerned? 

>Channel has more than one path.

Can you explain this?

>AvgPathGaindB - Vector listing the average gain of the discrete paths in decibels.

Now use this multipath channel to filter the input signal and create a plot using the syntax provided earlier.

```
m=30000;	
n=1;
x=randi([0,1],m,n);
y=filter(ral,x);
plot(20*log10(abs(y)))
```

Save your plot and discuss the result through comparison to the frequency-flat plot.

![image](https://user-images.githubusercontent.com/11879769/36349595-0771579e-1448-11e8-8620-2a29a292a992.png)

>The plot drops off, but in this case, this is normal. We can also see that there is increased signal now reaching the receiver.

2.	Generate a three-path Rayleigh Channel with variable average path gains. The syntax applicable here is: chan=rayleighchan(ts,fd,tau,pdb) ts,fd, and tau is as stated earlier, and pdb is a vector of average path gains.

```
ts=1e-5;
fd=120;
tau=[0, 1e-5, 1e-6];
pdb=[0,-10,-20];
ral=rayleighchan(ts,fd,tau,pdb);
ral
y=filter(ral,x);
figure;
plot(20*log10(abs(y)))
```

Save your plot and comment.

![image](https://user-images.githubusercontent.com/11879769/36349769-45879678-1449-11e8-88d2-e29ba259c822.png)

What difference did you notice with the AvgPathGaindB parameter?

> The channel has more than one path.

3. Using a Rayleigh Channel to filter a Modulated Signal, and Obtaining channel responses: (Impulse and frequency etc): Modulate the input data using 16QAM

```
M=16;
k=log2(M);
Input=reshape(x,length(x)/k,k);
xdec = bi2de(reshape(x,k,length(x)/k).' ,'left-msb');
modout=qammod(xdec,M);
ral % Enter( Notice that the StoreHistory parameter =0).  
```

             ChannelType: 'Rayleigh'
       InputSamplePeriod: 1.0000e-05
         DopplerSpectrum: [1×1 doppler.jakes]
         MaxDopplerShift: 120
              PathDelays: [0 1.0000e-05 1.0000e-06]
           AvgPathGaindB: [0 -10 -20]
      NormalizePathGains: 1
            StoreHistory: 0
          StorePathGains: 0
               PathGains: [-0.1382 - 0.8340i -0.0868 - 0.3198i -0.0150 + 0.0664i]
      ChannelFilterDelay: 4
    ResetBeforeFiltering: 1
     NumSamplesProcessed: 30000

```
ral.StoreHistory=true; % Change this to 1 by
ral
```

Did the StoreHistory parameter change? 
> Yes

```
y=filter(ral,modout); 
plot(ral)
ral % Enter
```

Why is the number of processed samples only 7500? Save and discuss your plot.

![image](https://user-images.githubusercontent.com/11879769/36350070-37ad8f76-144e-11e8-8f74-7edb85a17c4c.png)

>Because the plot shows three paths for the signal and number of samples. It shows how the paths and signal are affected by the number of bits per level and possible states.

The channel used to filter the modulated signal was a three-path channel. Can you see all three paths? 

>Yes

Why are two of these so close and how can you modify the code to resolve these? 

>The two paths are close due to the sampling index and by making the number smaller but not smaller than -7 we are able to separate the two paths.

As you increase or decrease the sample index, what do you observe?

>By increasing the sample index value we see more of the signal. When we approach 7500 the signal will start to fade.

Frequency Response:

Select the frequency response option from the visualization drop box. Again record your observations as the sample index is varied. Save and discuss your plot.

> By increasing the sample index value we see more of the signal. When we approach 7500 the signal will start to fade.

*Waterfall Plot*

Select the IR Waterfall option. Vary the sample index from 1 to 30000. Discuss your observation. Save your plot and discuss the meaning of this plot.

>By decreasing ts we see a broader view of the signal and by running the index we see what I think to be the harmonics of the almost periodic signal. In addition the waterfall shows us the fading point of the signal

Doppler Spectrum:

Obtain the Doppler spectrum for the multipath channel. Save and discuss your plot.

>The filter is passing the signal but not the noise