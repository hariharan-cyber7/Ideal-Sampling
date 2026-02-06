# Ideal, Natural, & Flat-top -Sampling
# Aim
Write a simple Python program for the construction and reconstruction of ideal, natural, and flattop sampling.
# Software required
1.Google Collab

2.jupyter
# Program
```sci
# """ #Impulse Sampling
# import numpy as np
# import matplotlib.pyplot as plt
# from scipy.signal import resample
# fs = 100
# t = np.arange(0, 1, 1/fs) 
# f = 5
# signal = np.sin(2 * np.pi * f * t)
# plt.figure(figsize=(10, 4))
# plt.plot(t, signal, label='Continuous Signal')
# plt.title('Continuous Signal (fs = 100 Hz)')
# plt.xlabel('Time [s]')
# plt.ylabel('Amplitude')
# plt.grid(True)
# plt.legend()
# plt.show()
# t_sampled = np.arange(0, 1, 1/fs)
# signal_sampled = np.sin(2 * np.pi * f * t_sampled)
# plt.figure(figsize=(10, 4))
# #plt.plot(t, signal, label='Continuous Signal', alpha=0.7)
# plt.stem(t_sampled, signal_sampled, linefmt='r-', markerfmt='ro', basefmt='r-', label='Sampled Signal (fs = 100 Hz)')
# plt.title('Sampling of Continuous Signal (fs = 100 Hz)')
# plt.xlabel('Time [s]')
# plt.ylabel('Amplitude')
# plt.grid(True)
# plt.legend()
# plt.show()
# reconstructed_signal = resample(signal_sampled, len(t))
# plt.figure(figsize=(10, 4))
# #plt.plot(t, signal, label='Continuous Signal', alpha=0.7)
# plt.plot(t, reconstructed_signal, 'r--', label='Reconstructed Signal (fs = 100 Hz)')
# plt.title('Reconstruction of Sampled Signal (fs = 100 Hz)')
# plt.xlabel('Time [s]')
# plt.ylabel('Amplitude')
# plt.grid(True)
# plt.legend()
# plt.show() """

# #Natural sampling
# import numpy as np
# import matplotlib.pyplot as plt
# from scipy.signal import butter, lfilter
# # Parameters
# fs = 1000  # Sampling frequency (samples per second)
# T = 1  # Duration in seconds
# t = np.arange(0, T, 1/fs)  # Time vector
# # Message Signal (sine wave message)
# fm = 5  # Frequency of message signal (Hz)
# message_signal = np.sin(2 * np.pi * fm * t)
# # Pulse Train Parameters
# pulse_rate = 50  # pulses per second
# pulse_train = np.zeros_like(t)
# # Construct Pulse Train (rectangular pulses)
# pulse_width = int(fs / pulse_rate / 2)
# for i in range(0, len(t), int(fs / pulse_rate)):
#     pulse_train[i:i+pulse_width] = 1    
# # Natural Sampling
# nat_signal = message_signal * pulse_train
# # Reconstruction (Demodulation) Process
# sampled_signal = nat_signal[pulse_train == 1]
# # Create a time vector for the sampled points
# sample_times = t[pulse_train == 1]
# # Interpolation - Zero-Order Hold (just for visualization)
# reconstructed_signal = np.zeros_like(t)
# for i, time in enumerate(sample_times):
#     index = np.argmin(np.abs(t - time))
#     reconstructed_signal[index:index+pulse_width] = sampled_signal[i]
# # Low-pass Filter (optional, smoother reconstruction)
# def lowpass_filter(signal, cutoff, fs, order=5):
#     nyquist = 0.5 * fs
#     normal_cutoff = cutoff / nyquist
#     b, a = butter(order, normal_cutoff, btype='low', analog=False)
#     return lfilter(b, a, signal)
# reconstructed_signal = lowpass_filter(reconstructed_signal,10, fs)
# plt.figure(figsize=(14, 10))
# # Original Message Signal
# plt.subplot(4, 1, 1)
# plt.plot(t, message_signal, label='Original Message Signal')
# plt.legend()
# plt.grid(True)
# # Pulse Train
# plt.subplot(4, 1, 2)
# plt.plot(t, pulse_train, label='Pulse Train')
# plt.legend()
# plt.grid(True)
# # Natural Sampling
# plt.subplot(4, 1, 3)
# plt.plot(t, nat_signal, label='Natural Sampling')
# plt.legend()
# plt.grid(True)
# # Reconstructed Signal
# plt.subplot(4, 1, 4)
# plt.plot(t, reconstructed_signal, label='Reconstructed Message Signal', color='green')
# plt.legend()
# plt.grid(True)
# plt.tight_layout()
# plt.show()

#Flat-top Sampling
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter

fs = 1000  # Sampling frequency (samples per second)
T = 1      # Duration in seconds
t = np.arange(0, T, 1/fs)  # Time vector
fm = 5     # Frequency of message signal (Hz)
message_signal = np.sin(2 * np.pi * fm * t)
pulse_rate = 50  # pulses per second
pulse_train_indices = np.arange(0, len(t), int(fs / pulse_rate))
pulse_train = np.zeros_like(t)
pulse_train[pulse_train_indices] = 1
flat_top_signal = np.zeros_like(t)
sample_times = t[pulse_train_indices]
pulse_width_samples = int(fs / (2 * pulse_rate)) # Adjust pulse width as needed

for i, sample_time in enumerate(sample_times):
    index = np.argmin(np.abs(t - sample_time))
    if index < len(message_signal):
        sample_value = message_signal[index]
        start_index = index
        end_index = min(index + pulse_width_samples, len(t))
        flat_top_signal[start_index:end_index] = sample_value

def lowpass_filter(signal, cutoff, fs, order=5):
    nyquist = 0.5 * fs
    normal_cutoff = cutoff / nyquist
    b, a = butter(order, normal_cutoff, btype='low', analog=False)
    return lfilter(b, a, signal)

cutoff_freq = 2 * fm  # Nyquist rate or slightly higher
reconstructed_signal = lowpass_filter(flat_top_signal, cutoff_freq, fs)

plt.figure(figsize=(14, 10))

plt.subplot(4, 1, 1)
plt.plot(t, message_signal, label='Original Message Signal')
plt.title('Original Message Signal')
plt.xlabel('Time (s)')
plt.ylabel('Amplitude')
plt.legend()
plt.grid(True)

plt.subplot(4, 1, 2)
plt.stem(t[pulse_train_indices], pulse_train[pulse_train_indices], basefmt=" ", label='Ideal Sampling Instances')
plt.title('Ideal Sampling Instances')
plt.xlabel('Time (s)')
plt.ylabel('Amplitude')
plt.legend()
plt.grid(True)

plt.subplot(4, 1, 3)
plt.plot(t, flat_top_signal, label='Flat-Top Sampled Signal')
plt.title('Flat-Top Sampled Signal')
plt.xlabel('Time (s)')
plt.ylabel('Amplitude')
plt.grid(True)
plt.legend()

plt.subplot(4, 1, 4)
plt.plot(t, reconstructed_signal, label=f'Reconstructed Signal (Low-pass Filter, Cutoff={cutoff_freq} Hz)', color='green')
plt.title('Reconstructed Signal')
plt.xlabel('Time (s)')
plt.ylabel('Amplitude')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()


```
# Output Waveform


<img width="568" height="435" alt="dc_exp1_1" src="https://github.com/user-attachments/assets/cc483ac5-1d1f-4245-80fb-da6bd0127b4d" />
<img width="568" height="435" alt="dc_exp1_2" src="https://github.com/user-attachments/assets/8b08b3b8-a994-4658-a39e-086f3e7a430d" />

<img width="568" height="435" alt="dc_exp1_3" src="https://github.com/user-attachments/assets/77208948-a8c1-4a19-aba3-90a716c39b9e" />




<img width="1238" height="985" alt="Screenshot 2026-01-30 140226" src="https://github.com/user-attachments/assets/234212e4-5e46-491a-ba67-e75402321b2c" />

<img width="1232" height="981" alt="Screenshot 2026-01-30 140241" src="https://github.com/user-attachments/assets/0f831100-8702-4db1-a84d-45da1d94d381" />


# Results
```
Attach the output waveform
```
# Hardware experiment output waveform.
