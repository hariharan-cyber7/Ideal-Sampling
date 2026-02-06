# Ideal, Natural, & Flat-top -Sampling
# Aim
Write a simple Python program for the construction and reconstruction of ideal, natural, and flattop sampling.
# Software required
Google Collab

# Theory

# Ideal or Instantaneous or Impulse Sampling:
sampling signal is a periodic impulse train. The area of each impulse in the sampled signal is equal to the instantaneous value of the input signal.

# Natural Sampling:
Natural sampling is also called practical sampling. In this sampling technique, the sampling signal is a pulse train.
In natural sampling method, the top of each pulse in the sampled signal retains the shape of the input signal during pulse interval.

# Flat Top Sampling:
The flat top sampling is also the practical sampling technique. In the flat top sampling, the sampling signal is also a pulse train. The top of each pulse in the sampled signal remain constant and is equal to the instantaneous value of the input signal 𝑥(𝑛) at the start of the samples.

# Program

# Impulse Sampling
```sci
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import resample

# Parameters
fs = 100
T = 1
f = 5

# Time
t = np.arange(0, T, 1/fs)

# Signal
signal = np.sin(2 * np.pi * f * t)

# Plot Continuous Signal
plt.figure()
plt.plot(t, signal)
plt.title("Continuous Signal")
plt.grid()
plt.show()

# Sampling
t_sampled = np.arange(0, T, 1/fs)
signal_sampled = np.sin(2 * np.pi * f * t_sampled)

# Plot Sampled Signal
plt.figure()
plt.stem(t_sampled, signal_sampled)
plt.title("Impulse Sampling")
plt.grid()
plt.show()

# Reconstruction
reconstructed = resample(signal_sampled, len(t))

plt.figure()
plt.plot(t, reconstructed)
plt.title("Reconstructed Signal")
plt.grid()
plt.show()
```
# Natural Sampling
```sci
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter

# Parameters
fs = 1000
T = 1
fm = 5

t = np.arange(0, T, 1/fs)

# Message Signal
message = np.sin(2 * np.pi * fm * t)

# Pulse Train
pulse_rate = 50
pulse_train = np.zeros_like(t)

pulse_width = int(fs / pulse_rate / 2)

for i in range(0, len(t), int(fs / pulse_rate)):
    pulse_train[i:i+pulse_width] = 1

# Natural Sampling
natural_signal = message * pulse_train

# Low-pass Filter
def lowpass(signal, cutoff, fs):
    nyq = 0.5 * fs
    normal = cutoff / nyq
    b, a = butter(5, normal, btype='low')
    return lfilter(b, a, signal)

reconstructed = lowpass(natural_signal, 10, fs)

# Plots
plt.figure(figsize=(10,8))

plt.subplot(4,1,1)
plt.plot(t, message)
plt.title("Original Signal")

plt.subplot(4,1,2)
plt.plot(t, pulse_train)
plt.title("Pulse Train")

plt.subplot(4,1,3)
plt.plot(t, natural_signal)
plt.title("Natural Sampling")

plt.subplot(4,1,4)
plt.plot(t, reconstructed)
plt.title("Reconstructed Signal")

plt.tight_layout()
plt.show()
```

# Flat-Top Sampling
```sci
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter

# Parameters
fs = 1000
T = 1
fm = 5

t = np.arange(0, T, 1/fs)

# Message Signal
message = np.sin(2 * np.pi * fm * t)

# Sampling
pulse_rate = 50
indices = np.arange(0, len(t), int(fs / pulse_rate))

flat_signal = np.zeros_like(t)

pulse_width = int(fs / (2 * pulse_rate))

# Flat-top Sampling
for i in indices:
    value = message[i]
    flat_signal[i:i+pulse_width] = value

# Low-pass Filter
def lowpass(signal, cutoff, fs):
    nyq = 0.5 * fs
    normal = cutoff / nyq
    b, a = butter(5, normal, btype='low')
    return lfilter(b, a, signal)

reconstructed = lowpass(flat_signal, 10, fs)

# Plots
plt.figure(figsize=(10,8))

plt.subplot(4,1,1)
plt.plot(t, message)
plt.title("Original Signal")

plt.subplot(4,1,2)
plt.stem(t[indices], np.ones_like(indices))
plt.title("Sampling Points")

plt.subplot(4,1,3)
plt.plot(t, flat_signal)
plt.title("Flat-Top Sampling")

plt.subplot(4,1,4)
plt.plot(t, reconstructed)
plt.title("Reconstructed Signal")

plt.tight_layout()
plt.show()


```
# Output Waveform
# Ideal sampling
<img width="568" height="435" alt="dc_exp1_1" src="https://github.com/user-attachments/assets/3d52fdb6-a59f-46f1-8147-9d367ad48b98" />
<img width="568" height="435" alt="dc_exp1_2" src="https://github.com/user-attachments/assets/2a64a7a5-29ec-4baf-a598-0756776ac2d9" />
<img width="568" height="435" alt="dc_exp1_3" src="https://github.com/user-attachments/assets/7089cc34-1d0a-4d4b-85ff-b45259c77164" />

# Natural sampling
<img width="1238" height="985" alt="Screenshot 2026-01-30 140226" src="https://github.com/user-attachments/assets/7f56c4a7-0a0a-4acf-a979-cbef6dc3e4a7" />

# Front top sampling
<img width="1232" height="981" alt="Screenshot 2026-01-30 140241" src="https://github.com/user-attachments/assets/33c77fab-fe7f-43a6-992d-75c2aecab98e" />


# Results

Thus, the construction and reconstruction of Ideal, Natural, and Flat-top sampling were successfully implemented using Python, and the corresponding waveforms were obtained.

