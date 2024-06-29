# spektral-rotation
```python
import numpy as np
import soundfile as sf
from scipy.fftpack import fft, ifft

def rotateSignal(signal,flip):
    if flip:
        signal = signal[::-1]
    
    x = np.concatenate((signal, signal[1:][::-1]))
    rotSig = ifft(x)
    
    rotSig = rotSig[:len(rotSig) // 2 + 1]
    
    energyRatio = np.sqrt(np.sum(np.abs(signal)**2) / np.sum(np.abs(rotSig)**2))
    rotSig *= energyRatio
    
    return rotSig

def skibidi(input, output,flip=True):
    signal, sampleRate = sf.read(input, dtype='float64')
    
    if signal.ndim == 1: # mono
        rotSig = rotateSignal(signal,flip)
    else: # stereo
        rotSig = np.column_stack((rotateSignal(signal[:, 0],flip), rotateSignal(signal[:, 1],flip)))
    
    sf.write(output, rotSig.astype(np.float64), sampleRate, format='WAV', subtype='FLOAT')
    print(f"saved {output}")

if __name__ == "__main__":
    inputFile = input()
    skibidi(inputFile, 'rotated.wav') # roteret 90 grader med uret.
    skibidi(inputFile, 'swapped.wav',False) # swappet spektrogram
    skibidi('swapped.wav', 'inverted.wav') # lodret flip af inputtet
    # 180 graders rotation kan dannes ved at reverse inverted.wav
    # 270 graders rotation kan dannes ved at reverse swapped.wav
```
