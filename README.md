# go modbus
[![GoDoc](https://godoc.org/github.com/thinkgos/mb?status.svg)](https://godoc.org/github.com/thinkgos/mb)
[![Build Status](https://www.travis-ci.org/thinkgos/mb.svg?branch=master)](https://www.travis-ci.org/thinkgos/mb)
[![codecov](https://codecov.io/gh/thinkgos/mb/branch/master/graph/badge.svg)](https://codecov.io/gh/thinkgos/mb)
![Action Status](https://github.com/thinkgos/mb/workflows/Go/badge.svg)
[![Go Report Card](https://goreportcard.com/badge/github.com/thinkgos/mb)](https://goreportcard.com/report/github.com/thinkgos/mb)
[![Licence](https://img.shields.io/github/license/thinkgos/mb)](https://raw.githubusercontent.com/thinkgos/mb/master/LICENSE)


### Supported formats

- modbus Serial(RTU,ASCII) Client
- modbus TCP Client
- modbus TCP Server

### Features

- object pool design,reduce memory allocation
- fast encode and decode
- interface design
- simple API and support raw data api

### Installation

Use go get.
```bash
    go get github.com/thinkgos/mb
```

Then import the modbus package into your own code.
```bash
    import modbus "github.com/thinkgos/mb"
```

### Supported functions

---

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

### Example

---

```go
    // mb simple example
    func main() {
        p := modbus.NewRTUClientProvider(modbus.WithEnableLogger(),
            modbus.WithSerialConfig(serial.Config{
                Address:  "/dev/ttyUSB0",
                BaudRate: 115200,
                DataBits: 8,
                StopBits: 1,
                Parity:   "N",
                Timeout:  modbus.SerialDefaultTimeout,
            }))
        client := mb.NewClient(p, mb.WitchHandler(&handler{}))
        err := client.Start()
        if err != nil {
            panic(err)
        }
    
        err = client.AddGatherJob(mb.Request{
            SlaveID:  1,
            FuncCode: modbus.FuncCodeReadHoldingRegisters,
            Address:  0,
            Quantity: 300,
            ScanRate: time.Second,
        })
    
        if err != nil {
            panic(err)
        }
    
        select {}
    }
    
    type handler struct {
	    mb.NopProc
    }

    func (handler) ProcResult(_ error, result *mb.Result) {
        log.Printf("Tx=%d,Err=%d,SlaveID=%d,FC=%d,Address=%d,Quantity=%d,SR=%dms",
            result.TxCnt, result.ErrCnt, result.SlaveID, result.FuncCode,
            result.Address, result.Quantity, result.ScanRate/time.Millisecond)
    }
```

### References

---

- [Modbus Specifications and Implementation Guides](http://www.modbus.org/specs.php)
- [goburrow](https://github.com/goburrow/modbus)
- [gomodbus](https://github.com/thinkgos/gomodbus)