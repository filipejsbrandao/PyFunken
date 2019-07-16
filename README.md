![Funken logo](https://github.com/ar0551/PyFunken/blob/master/materials/FNK_LOGO_GITHUB.png)

# PyFunken
Python interface (GPL) to the [Funken](https://github.com/astefas/Funken) Serial Protocol Toolkit.

PyFunken is a Pyhton-based interface to the Funken Serial Protocol Toolkit. It offers classes to initiate communication with Funken-Enabled Arduino devices, send commands and handle responses.

Funken is a [Arduino](https://www.arduino.cc) [library](https://www.arduino.cc/en/Reference/Libraries) that enables [callbacks](https://en.wikipedia.org/wiki/Callback_(computer_programming)) on an arduino. It is part of a workflow that simplifies communication between your Arduino and any host, that is able to speak to it via serial messages.
For more info on Funken, see the [Funken Repository](https://github.com/astefas/Funken).

PyFunken is build on top of [PySerial](https://github.com/pyserial/pyserial).

## How to use PyFunken
PyFunken acts as an interface to send commands to an Arduino devices running a Funken implementation. Hence, to work properly, PyFunken assumes you kwow what protocol is defined in the Funken implementation running on the Arduino. For more details on how to define your own protocol using Funken, see [here](https://github.com/astefas/Funken#how-to-use-funken). For basic Arduino protocols which are shipped with Funken, see [here](https://github.com/astefas/Funken/blob/master/README.md#quickstart).

PyFunken requires PySerial to be installed on your computer. To install PySerial, see [here](https://github.com/pyserial/pyserial/blob/master/documentation/pyserial.rst#installation). PyFunken is currently provided as a Python module. In order to use it, make sure to have the funken.py file in the same folder you are working, or to copy it into one directory which is part of your system's search path.

### Initialize Communication with a Funken Device
Communication between Python and a Funken device is initialized by creating a PyFunken object. This object can handle any number of serial connections, and routes different commands and responses to the right connection and device. The communication baudrate is set to 57600, which is the default used by Funken (change it if you are using a different badurate in your Funken implementation file). After initializing the PyFunken object with "COM3" as the port, the ```.register_devices()``` command scans the serial connection for available Funken devices, and stores all available commands and response tokens in memory.

```python
import funken
import time

## serial port to connect to
port = "COM6"

## initialize the main PyFunken object
pyf = funken.PyFunken([port], [57600])

## register devices on the selected com port
pyf.ser_conn[port].register_devices()
time.sleep(1)
```

### Send Commands to a Funken Device
Using ```pyf.send_command("PM 13 1\n", port, 1)``` it is possible to send commands as string objects to the selected port and device. All Funken commands must include a ```\n``` EOL delimited to be recognized as separate commands by Funken. In the code below, you can see an example of a PyFunken file blinking an LED on pin 13 (on a board running the [02BasicArduino](https://github.com/astefas/Funken/tree/master/src/Funken/examples/02BasicArduino) example file).
```python
## set pin 13 as output
pyf.send_command("PM 13 1\n", port, 1)
while True:
	## turn led on pin 13 on
	pyf.send_command("DW 13 1\n", port, 1)
	time.sleep(1)
	## turn led on pin 13 off
	pyf.send_command("DW 13 0\n", port, 1)
	time.sleep(1)
```

### Ask a Response from a Funken Device
The ```.send_command()``` instruction sends a message to a Funken device without expecting a response. If a response is generated by Funken, it will not be recorded. For different cases (e.g., reading data from an analog sensor), it is necessary to be able to record the response. For these situations ```.get_response()``` is used. The commands ```response = pyf.get_response("AR 0\n", "AR", port, 1)``` asks to the Arduino for the analog value of the pin A0, and returns it in the ```response``` variable. Please note the is necessary to specify the ```"AR"``` token associated with the ```AnalogRead``` function in Arduino, in order to enable Funken to detect the response.
```python
response = pyf.get_response("AR 0\n", "AR", port, 1)
if response is not None:
	print(response)
time.sleep(0.01)
```

### Read all Available Commands on a Funken Device
If you have an Arduino device running Funken, but you do not know what Funken implementation is running on the device, you can request the list of all available commands using the ```"CLIST\n"``` commands. In the code below, the ```response``` variable will return a list of strings with all the token of the available commands on the connected board.
```python
response = pyf.get_response("CLIST\n", "CLIST", port, 1)
if response is not None:
	print(response)
time.sleep(0.01)
```

### Examples
[Go to the examples folder in this repository and see a more detailed explanation of what is going on inside these examples.](https://github.com/ar0551/PyFunken/tree/master/examples)


## License
PyFunken: Python interface (GPL) to the [Funken](https://github.com/astefas/Funken) Serial Protocol Toolkit.

Copyright (c) 2019, Andrea Rossi

PyFunken is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License version 3.0 as published by the Free Software Foundation.

Funken is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with Funken; If not, see http://www.gnu.org/licenses/.

@license GPL-3.0 https://www.gnu.org/licenses/gpl.html


![DDU logo](https://github.com/ar0551/PyFunken/tree/master/materials/DDU-logo_BLACK_RGB.png)

Significant parts of Funken have been developed by Andrea Rossi at [DDU Digital Design Unit - Prof. Oliver Tessmann - Technische Universität Darmstadt](http://www.dg.architektur.tu-darmstadt.de/dg/startseite_3/index.de.jsp).

## References
[Stefas, A, Rossi, A and Tessmann, O. 2018. Funken: Serial Protocol Toolkit for Interactive Prototyping, In Proceedings of ECAADE 2018, Lodz. Poland](http://papers.cumincad.org/data/works/att/ecaade2018_388.pdf)





