
# m-bus-parser (wired)

[![Crates.io](https://img.shields.io/crates/v/m-bus-parser.svg)](https://crates.io/crates/m-bus-parser) [![Downloads](https://img.shields.io/crates/d/m-bus-parser.svg)](https://crates.io/crates/m-bus-parser) [![License](https://img.shields.io/crates/l/m-bus-parser.svg)](https://crates.io/crates/m-bus-parser) [![Documentation](https://docs.rs/m-bus-parser/badge.svg)](https://docs.rs/m-bus-parser) [![Build Status](https://github.com/maebli/m-bus-parser/actions/workflows/rust.yml/badge.svg)](https://github.com/maebli/m-bus-parser/actions/workflows/rust.yml)

### Introduction 

m-bus-parser is an open source parser (or decoder) for **wired** m-bus portocol written in rust. 

"M-Bus or Meter-Bus is a European standard (EN 13757-2 physical and link layer, EN 13757-3 application layer) for the remote reading of water, gas or electricity meters. M-Bus is also usable for other types of consumption meters, such as heating systems or water meters. The M-Bus interface is made for communication on two wires, making it cost-effective." - [Wikipedia](https://en.wikipedia.org/wiki/Meter-Bus)

An outdated specification is available freely on the [m-bus website](https://m-bus.com/documentation). This document is a good starting point for understanding the protocol. There have been many other implementationso the specification such as a no longer maitained [ m-bus encoder and decoder by rscada](https://github.com/rscada/libmbus) written in c the [java jMbus implementation ](https://github.com/qvest-digital/jmbus) or [Valley.Net.Protocols.MeterBus in C#](https://github.com/sympthom/Valley.Net.Protocols.MeterBus/).


### Visualization of Library Function

![](./function.png)

## Aim

- suitable for embedded targets `no_std`
- Follow the Rust API Guideline https://rust-lang.github.io/api-guidelines/
- minimal copy

## Development status 

The library is currently under development. It is able to parse the link layer but not the application layer. The next goal is to parse the application layer. Once this is achieved the library will be released as `v0.1.0`. Further goals, such as decryption, will be set after this milestone is achieved. 

## Example of current function

Examples taken from https://m-bus.com/documentation-wired/06-application-layer:

1. Set the slave to primary address 8 without changing anything else:

``INPUT: 68 06 06 68 | 53 FE 51 | 01 7A 08 | 25 16``

Parsing the frame using the library (the data is not yet parsable with the lib):

```rust
   
    use m_bus_parser::frames::{Address, Frame, Function};

    let example = vec![ 
        0x68, 0x06, 0x06, 0x68, 
        0x53, 0xFE, 0x51, 
        0x01, 0x7A, 0x08, 
        0x25, 0x16,
    ];

    let frame = Frame::try_from(example.as_slice())).unwrap();

    if let Frame::ControlFrame { function, address, data } = frame {
        assert_eq!(address, Address::Broadcast { reply_required: true });
        assert_eq!(function, Function::SndUd { fcb: (false)});
        assert_eq!(data, &[0x51,0x01, 0x7A, 0x08]);
    }

```
