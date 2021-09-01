Overview
----------------------
Zaiyu Tone Generator is a tone generator framework for embedded system, capable for hardware accerating on VLIW-DSP.

It makes light-weight modular digital synthesis framework possible.

Signal Chain
-----------------------

A MIDI channel can only loads up to 1 MIDI program. A MIDI Program is made of :

|`Synthesis Algorithm` * N  (for all poly) <-> | `Routing/Mixing` -> |  `Output` (One channel) |
| --- | ----------- | ----|
|`Effect Processor` * N (Just for one channel) <-> | `Routing/Mixing`  |                 |
|`Effect Processor` * N (for all poly) <-> | `Routing/Mixing`  |                 |

Currently, Synthesis algorithm can be:

* `Anti-aliasing FM Synthesis & Naive Formant Synthesis`
* `Ganular Wavetable Synthesis`
* `Waveguide Synthesis`
* `String Modeling Synthesis`
* `Organ Synthesis`
* `Virtual Analog synthesis`

If the program includes Effect for all poly, we call it a EPDSP (Each Polyphony DSP) program.

If the program includes Effect for just one channel, we call it a FDSP (Flexible DSP) program.

The routing/mixing logic can create full-mesh connection between algorithm/effect inputs/outputs, by each polyphony or down mix into channel.

Control chain
-----------------------

`MIDI messages` -> `ZTG MIDI processor` -> `MIDI Translation Layer` -> `ZTG auditing` -> `All synthesis algorithms & Effect processors`

The synthesis algorithm and effect algorithm should provide all stateless (?) interface to the MTL.

MTL can be customize for each program, but the ZTG framework should handle each channel MIDI message and do some properly remap (to global effects).

The bank-select and program change message must handled by the ZTG framework. When a channel receives a program change command. The MTL should be terminated by MIDI processor at once. And all channel setting must to be reset to normal for new MTL being loaded.

If the user want to change their program dynamically and without be cutoff by the ZTG framework. The framework should create a new channel, remap original channel's input to dumb input and map new channel to a proper channel number. When the old channel's synthesis algorithm stops outputing valid sample, the old channel should be deconstructed at once or delay for several time (can be set globally).

The MTL also has to register a callback function to the framework, when the framework detects processing resource hit the limit, the framework should terminate some synthesis instance and use callback function to notify MTL there's some change.


FM Synthesizer Signal Inputs and Outputs
------------------------------------------

|Signal name | Stereo | Direction | Descrption|
| --- | --- | ----------- | ----|
|External Signal In| Y |Input | External signal to input FM modulation matrix for modulation|
|Waveshaper In| Y |Input | External signal for non-linear waveshaper to shape the waveform|
|Ring Modulator In| Y |Input | External signal for internal multiplier |
|Signal Out| Y | Output | The synthesis result | 

