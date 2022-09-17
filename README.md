go modbus [![Build Status](https://travis-ci.org/goburrow/modbus.svg?branch=master)](https://travis-ci.org/goburrow/modbus) [![GoDoc](https://godoc.org/github.com/goburrow/modbus?status.svg)](https://godoc.org/github.com/goburrow/modbus)
=========
Fault-tolerant, fail-fast implementation of Modbus protocol in Go.

Supported functions
-------------------
Bit access:
*   Read Discrete Inputs
*   Read Coils
*   Write Single Coil
*   Write Multiple Coils

16-bit access:
*   Read Input Registers
*   Read Holding Registers
*   Write Single Register
*   Write Multiple Registers
*   Read/Write Multiple Registers
*   Mask Write Register
*   Read FIFO Queue

Supported formats
-----------------
*   TCP
*   Serial (RTU, ASCII)
*   Encapsulated RTU over TCP

Usage
-----
Basic usage:
```go
slaveId := 0x01

// Modbus TCP
client := modbus.TCPClient("localhost:502")
// Read input register 9
results, err := client.ReadInputRegisters(slaveId, 8, 1)

// Modbus RTU/ASCII
// Default configuration is 19200, 8, 1, even
client = modbus.RTUClient("/dev/ttyS0")
results, err = client.ReadCoils(slaveId, 2, 1)

// Modbus Encapsulated RTU over TCP
client = modbus.EncClient("gateway:20108")
results, err = client.ReadCoils(slaveId, 2, 1)
```

Advanced usage:
```go

slaveId := 0x01

// Modbus TCP
handler := modbus.NewTCPClientHandler("localhost:502")
handler.Timeout = 10 * time.Second
handler.Logger = log.New(os.Stdout, "test: ", log.LstdFlags)
// Connect manually so that multiple requests are handled in one connection session
err := handler.Connect()
defer handler.Close()

client := modbus.NewClient(handler)
results, err := client.ReadDiscreteInputs(slaveId, 15, 2)
results, err = client.WriteMultipleRegisters(slaveId, 1, 2, []byte{0, 3, 0, 4})
results, err = client.WriteMultipleCoils(slaveId, 5, 10, []byte{4, 3})
```

```go
// Modbus RTU/ASCII
handler := modbus.NewRTUClientHandler("/dev/ttyUSB0")
handler.BaudRate = 115200
handler.DataBits = 8
handler.Parity = "N"
handler.StopBits = 1
handler.SlaveId = 1
handler.Timeout = 5 * time.Second

err := handler.Connect()
defer handler.Close()

client := modbus.NewClient(handler)
results, err := client.ReadDiscreteInputs(slaveId, 15, 2)
```

```go
// Modbus Encapsulated RTU over TCP
handler := modbus.NewEncClientHandler("gateway:20108")
err := handler.Connect()
defer handler.Close()

client := modbus.NewClient(handler)
results, err := client.ReadDiscreteInputs(slaveId, 15, 2)
```

References
----------
-   [Modbus Specifications and Implementation Guides](http://www.modbus.org/specs.php)
