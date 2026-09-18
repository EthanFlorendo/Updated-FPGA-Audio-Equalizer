# Digital Audio Equalizer

A real-time stereo audio equalizer built in Verilog HDL, targeting the **Terasic DE1** development board (**Altera/Intel Cyclone II, EP2C20F484C7**). It reads audio in over the board's WM8731 codec, runs it through selectable Finite Impulse Response (FIR) filters, and writes the result back out live.

## Hardware

| | |
|---|---|
| Board | Terasic DE1 |
| FPGA | Altera/Intel Cyclone II, EP2C20F484C7 (20K LEs, 52 M4K RAM blocks, 26 embedded multipliers) |
| HDL | Verilog |


## How it works

Three FIR filters run in parallel, each generated with Altera's FIR Compiler II IP:

* **Low Pass** — 0–500 Hz
* **Band Pass** — 550–3000 Hz
* **High Pass** — above 3000 Hz

Each filter is instantiated twice (once per stereo channel), fed from either the WM8731's Line-In path or an onboard test-tone generator, and the selected filter's output is streamed back out through Line-Out.



## I/O

* Switches, keys (input)
* Line-In (input, via WM8731 codec)
* Line-Out (output, via WM8731 codec)
* HEX display (output)
* Red LEDs (output)

## Project structure

| File | Role |
|---|---|
| [`AudioEqualizer.v`](AudioEqualizer.v) | Top-level module: source/filter selection, HEX decoding, test-tone generator |
| [`filters/LowFIR500.v`](filters/LowFIR500.v), [`filters/BandFIR550_3000.v`](filters/BandFIR550_3000.v), [`filters/HighFIR3000.v`](filters/HighFIR3000.v) | FIR Compiler II-generated filter cores |
| [`audio/Audio_Controller.v`](audio/Audio_Controller.v), [`audio/Altera_UP_Audio_In_Deserializer.v`](audio/Altera_UP_Audio_In_Deserializer.v), [`audio/Altera_UP_Audio_Out_Serializer.v`](audio/Altera_UP_Audio_Out_Serializer.v), [`audio/Altera_UP_Audio_Bit_Counter.v`](audio/Altera_UP_Audio_Bit_Counter.v), [`audio/Altera_UP_Clock_Edge.v`](audio/Altera_UP_Clock_Edge.v), [`audio/Altera_UP_SYNC_FIFO.v`](audio/Altera_UP_SYNC_FIFO.v) | I2S audio interface to the WM8731 codec |
| [`audio/Audio_Clock.v`](audio/Audio_Clock.v) | PLL generating the codec's master clock |
| [`audio/avconf.v`](audio/avconf.v), [`audio/I2C_Controller.v`](audio/I2C_Controller.v) | WM8731 register configuration over I2C |

## To Fix

* The three FIR filter cores currently target Cyclone V and need to be regenerated from the IP Catalog / FIR Compiler II against `EP2C20F484C7` before they'll synthesize for this board; their underlying implementation files aren't checked into this repo (IP-Catalog output).

