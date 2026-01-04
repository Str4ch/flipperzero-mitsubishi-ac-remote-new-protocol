# Mitsubishi AC Remote

Mitsubishi Electric AC remote control for Flipper Zero.
This fork is implementing IR protocol that is being used in newer Mitsubishi AC models.

Сompatible models:
- SRK20ZSP-S
- SRK25ZSP-S
- SRK35ZSP-S
- SRK45ZSP-S

![Screenshot](img/screenshot.png)

Based on [Mitsubishi AC Remote](https://github.com/achistyakov/flipperzero-mitsubishi-ac-remote).

[Protocol Description](https://github.com/Str4ch/flipperzero-mitsubishi-ac-remote-new-protocol/blob/master/PROTOCOL.md)

## Building
```shell
# Clone repository
git clone https://github.com/Str4ch/flipperzero-mitsubishi-ac-remote-new-protocol.git
cd mitsubishi_ac_remote

# Build the application
ufbt build

# Build and launch the application
ufbt launch
```
