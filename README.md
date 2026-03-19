# APC AP7920 Switched Rack PDU Driver for Control4

A Control4 driver for the APC AP7920 Switched Rack PDU that provides individual outlet control and real-time monitoring via SNMPv1.
![Control4](https://img.shields.io/badge/Control4-OS%203.x-blue)
  <img src="https://img.shields.io/badge/Control4-OS%204.x-blue" alt="Control4 OS 4.x">
![Outlets](https://img.shields.io/badge/Outlets-8-green)
## Overview

This driver enables full integration of the APC AP7920 8-outlet switched PDU with Control4 home automation systems. It communicates directly with the PDU using the SNMP protocol, allowing you to control and monitor outlets from Composer, touchscreens, or programmed automation.

## Features

- Individual outlet ON/OFF/Reboot control for all 8 outlets
- Real-time outlet state monitoring
- Power load monitoring (Amps)
- Input voltage monitoring
- PDU information display (model, serial number, firmware, uptime)
- Automatic outlet name synchronization from PDU
- 8 relay bindings for integration with other Control4 devices
- Configurable polling interval (5-300 seconds)
- Debug mode for troubleshooting

## Requirements

- APC AP7920 Switched Rack PDU (may work with similar APC switched PDU models)
- Control4 controller running OS 3.x
- SNMPv1 enabled on the PDU
- Network connectivity between Control4 controller and PDU

## Installation

1. Download `APC_AP7920_PDU.c4z`
2. In Composer Pro, go to File, then Add to Project, then Add Device Driver
3. Browse to and select the downloaded .c4z file
4. Add the driver to your project
5. Configure the driver properties (see below)

## Configuration

### Driver Properties

| Property | Default | Description |
|----------|---------|-------------|
| IP Address | (empty) | IP address of the APC PDU |
| Port | 161 | SNMP UDP port |
| Read Community | public | SNMP read community string |
| Write Community | private | SNMP write community string |
| Polling Interval | 30 | Status refresh interval in seconds |
| Debug Mode | Off | Enable verbose logging for troubleshooting |

### PDU Setup

Before using the driver, ensure SNMP is properly configured on your PDU:

1. Access the PDU web interface at `http://<pdu-ip-address>`
2. Log in with your credentials (default: apc/apc)
3. Navigate to Configuration, then Network, then SNMPv1
4. Enable SNMPv1 access
5. Set the Read Community string (default: public)
6. Set the Write Community string (default: private)
7. If using access control, ensure your Control4 controller IP is allowed

## Usage

### Actions

The following actions are available in Composer Pro under the Actions tab:

| Action | Description |
|--------|-------------|
| Refresh Status | Manually refresh all outlet states and power readings |
| Get Outlet Names | Retrieve outlet names configured on the PDU |
| Get Device Info | Retrieve PDU information (model, serial, firmware, uptime) |
| All Outlets On | Turn on all 8 outlets |
| All Outlets Off | Turn off all 8 outlets |

### Programming Commands

The following commands are available for use in Control4 programming:

| Command | Parameter | Description |
|---------|-----------|-------------|
| Outlet On | OUTLET (1-8) | Turn the specified outlet on |
| Outlet Off | OUTLET (1-8) | Turn the specified outlet off |
| Outlet Reboot | OUTLET (1-8) | Power cycle the specified outlet |

### Relay Bindings

The driver provides 8 relay connections (Outlet 1 through Outlet 8) that can be bound to other Control4 devices. These bindings:

- Send CLOSED notification when outlet turns on
- Send OPENED notification when outlet turns off
- Respond to CLOSE, OPEN, and TOGGLE commands

This allows you to bind PDU outlets directly to devices that expect relay control.

## Status Properties

The following read-only properties display PDU status information:

| Property | Description |
|----------|-------------|
| Driver Version | Current driver version |
| Driver Status | Connection status |
| PDU Name | System name configured on PDU |
| Model | PDU model number (e.g., AP7920) |
| Serial Number | PDU serial number |
| Firmware Version | PDU firmware version |
| Up Time | Time since last PDU reboot |
| Total Load (Amps) | Current power draw |
| Input Voltage | Input voltage reading |
| Outlet 1-8 Name | Names configured on PDU for each outlet |
| Outlet 1-8 Status | Current state (ON/OFF) for each outlet |

## Troubleshooting

### No Communication or Status Not Updating

- Verify the IP address is correct
- Confirm network connectivity by pinging the PDU from the Control4 network
- Check that SNMP is enabled on the PDU
- Verify the Read Community string matches the PDU configuration
- Enable Debug Mode and check the Lua output tab for detailed logs

### SNMP Error 5 on Outlet Control

Error 5 (genErr) indicates the PDU rejected the write command. This is typically a permissions issue on the PDU side:

- Verify the Write Community string matches exactly (case-sensitive)
- Check that SNMP write access is enabled on the PDU
- Review PDU access control settings to ensure the Control4 controller IP is allowed
- Some PDUs require specific user account permissions for outlet control

### Outlets Show Wrong State

- Use the Refresh Status action to force an update
- Check the polling interval setting
- Verify the PDU is responding to SNMP queries (enable Debug Mode)

## Technical Details

### Protocol

- SNMPv1 over UDP port 161
- Uses LuaSocket library for non-blocking UDP communication
- Response polling at 1-second intervals

### SNMP OIDs

| OID | Description |
|-----|-------------|
| 1.3.6.1.4.1.318.1.1.4.4.2.1.3.n | Outlet control (1=ON, 2=OFF, 3=Reboot) |
| 1.3.6.1.4.1.318.1.1.4.4.2.1.4.n | Outlet name |
| 1.3.6.1.4.1.318.1.1.4.1.4.0 | Model number |
| 1.3.6.1.4.1.318.1.1.4.1.5.0 | Serial number |
| 1.3.6.1.4.1.318.1.1.4.1.2.0 | Firmware version |
| 1.3.6.1.2.1.1.5.0 | System name |
| 1.3.6.1.2.1.1.3.0 | System uptime |
| 1.3.6.1.4.1.318.1.1.12.2.3.1.1.2.1 | Load in tenths of Amps |
| 1.3.6.1.4.1.318.1.1.12.1.15.0 | Input voltage |

## Compatibility

This driver was developed for the APC AP7920 but may work with other APC switched PDUs that use the same SNMP MIB structure
- AP7920 (8 outlets, tested)

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-03-18 | Initial release |

## Support

For issues or questions, please open an issue on GitHub.
