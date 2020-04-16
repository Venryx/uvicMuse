
# UVic MUSE

An application for streaming from MUSE headsets to MATLAB and other 
platforms. 

## Requirements

The code relies on [pygatt](https://github.com/peplin/pygatt) for BLE communication 
    and [pylsl](https://github.com/chkothe/pylsl) for Muse streaming. For the best result use [BLED112](http://www.farnell.com/datasheets/2674198.pdf?_ga=2.79024144.587051681.1584504877-1039421750.1584504877&_gac=1.255907449.1584504893.Cj0KCQjw6sHzBRCbARIsAF8FMpWVas72rjYW8HkIbpjfUe97CBonZR71Yi22iGbSvDSER9rcJJ1JbqsaAit0EALw_wcB).
We highly recommend installing on a virtual environment (VE). You can build and manage those VEs using [Anaconda](https://www.anaconda.com/), 
        the instructions to install and setup a conda environment is described [here](https://docs.anaconda.com/anaconda/install/).



**Compatible with Python 3.x**

**Compatible with MUSE _MU-02_ and _MU-03_**

_Note: if you run into any issues, first check out out Common Issues
and then the Issues section of [this](https://github.com/bardiabarabadi/uvicMUSE) repository_

## Getting Started

To stream from MUSE to MATLAB ro other platforms, a _Streamer_ application is required. 
Transmitted data from _Streamer_ side then can be received and used by the _Receiver_ application. 
Take a look at this chart below:


![Top](image.png)

This project has two sections, first, *UVic MUSE* that connects to MUSE 
over Bluetooth and streams its data over UDP and LSL.
Second, a MATLAB toolbox (and `MuseUdp.m`) that allows the user to receive 
*UVic MUSE* transmitted data over UDP protocol. 
In the following sections we go through installation and usage of *UVic MUSE*
and then explain about part two, MuseUdp.   

### UVic MUSE Installation

On Windows we suggest the user to install Anaconda and run all of the following commands
(including optional commands) in an Anaconda Prompt. You may need to install
 [Microsoft Visual C++ Build Tools](https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2017) (~1GB) from microsoft website if you don't use Anaconda.
 
On MacOS and linux, install Anaconda (or miniconda), 
open a terminal, and follow these commands:

**If you don't want to use a virtual environment, use Terminal (Linux and OSx) 
or Command Prompt (Windows) and skip the optional steps.**

(optinal) Create a new conda environment.
    
    conda create --name muse_env python=3.7
    
(optinal) Activate conda environment
    
    conda activate muse_env
    
Install dependencies

    pip install pylsl pygatt # For Windows and MacOS
    pip install pylsl==1.10.5 pygatt # For Linux
          
    
Install UVicMUSE using `pip`

    pip install --upgrade uvicmuse
    


    
#### Running UVicMUSE:

To run and use *UVic MUSE* open a Terminal (Linux & OSx) or Command Prompt (Windows)
and type in the commands below:

(optional) Activate the virtual environment
    
    conda activate muse_env

Run UVicMUSE:
    
    uvicmuse
    
**If you manage to install UVicMUSE without conda in Windows, you can run it by opening the start menu
and typing uvicmuse**

#### GUI: 

![Top](uvicmuse_.png)

#### Streaming Procedure:
Follow steps shown below to stream the MUSE sensory data. Remember to correctly specify the **Required Entries** variables
before moving on to the next step. 

1. Search to get a list of available MUSEs
2. Connect to one of the MUSEs. **Required Entries** = Checkboxes (UDP, LSL, EEG, PPG, ACC, GYRO) 
3. Start Streaming over UDP and LSL (if enabled). **Required Entries** = Filters (Highpass, Lowpass, Notch)

Notes:
* Stopping the stream won't disconnect the MUSE (use this feature for changing filters configurations)
* Search is required after disconnecting from a MUSE 


## Receiver Toolbox (MuseUdp)

In this section we explain the methods available in the MATLAB toolbox. The main responsibility of a receiver
is to connect to UDP socket (same socket as UVic MUSE) and receive data that is being transmitted from MUSE device.

#### MATLAB Toolbox

Donwload [MuseUdp Toolbox](https://www.mathworks.com/matlabcentral/fileexchange/74583-museudp) from MATLAB file exchange. 
Open and install the toolbox on MATLAB. Moreover, you need to install [Instrument Control Toolbox](https://www.mathworks.com/products/instrument.html)
 to establish UDP connections.
 
To see all of the available methods (functions), create an object from MuseUdp and call methods for it:

    mu = MuseUdp();
    methods(mu);

To get a single sample from UVic MUSE use:
    
    mu = MuseUdp();
    [data, timestamp, success] = mu.get_xxx_sample()

Repace `xxx`with `eeg`, `ppg`, `acc` or `gyro`. The sampled data may have different size according to `xxx`, `eeg` has 5 
channels per sample, the rest of the sensors return 3 channels data. 

To read sampled data in chunks, you need to specify the chunk size and call `mu.get_xxx_chunk(###)`, replace `xxx` with sensor type
and `###` with the chunk size. The output size, `size(data)`, will be `[chunk_size, 5]` for `eeg` and `[chunk_size, 3]` for others. 

*Note: since the buffer size is limited for UDP protocols, each sample of `eeg` contains four bytes for timestamp and
4 * 5 = 20 bytes for data (24B total). Since default buffer size in UDP is 1kB, one cannot get a chunk larger that 40 samples.
We suggest using multiple instances of `get_xxx_chunk()`, but you can change the buffer size by calling the function below:
    
    mu.set_udp_buffer_size(2048) % 2kB buffer

## Issues

On MacOSx: Application crashes after running:

    pip uninstall serial pyserial
    conda uninstall serial pyserial
    pip install pyserial esptool

## Citing UVicMUSE

```
@misc{UVicMUSE,
  author       = {Bardia Barabadi, Jamieson Fregeau},
  title        = {uvic-muse},
  month        = March,
  year         = 2020,
}
```