# driving_range_dispenser
Raspberry Pi Architecture to control driving range in bay golf ball dispenser with MQTT communication to dispensers over local network and web connection to AWS server for integration with 3rd party webservices

# Setup DHCP and MQTT over Ethernet on Raspberry Pi

## 1. Install Mosquitto Broker
```
sudo apt update
sudo apt install -y mosquitto mosquitto-clients
sudo systemctl enable mosquitto
sudo systemctl start mosquitto
```
## 2. Install `dnsmasq`
```
sudo apt update
sudo apt install -y dnsmasq
```
## 3. Set Static IP on Ethernet Port
a. Edit DHCP config:
```
sudo nano /etc/dhcpcd.conf
```
b. Add to the end of the file:
```
interface eth0
static ip_address=192.168.4.1/24
```
c. Save and exit: `Ctrl+O`, `Enter`, then `Ctrl+X` 
d. Reboot:
```
sudo reboot
```
e. Confirm IP:
```
ip addr show eth0
```
## 4. Configure `dnsmasq`
a. Edit config:
```
sudo nano /etc/dnsmasq.conf
```
b. Add to the end:
```
interface=eth0
# Set IP range from .10 to .100 (expandable later)
dhcp-range=192.168.4.10,192.168.4.100,255.255.255.0,24h
```
c. Save and exit: `Ctrl+O`, `Enter`, then `Ctrl+X` 
d. Restart `dnsmasq`:
```
sudo systemctl restart dnsmasq
```
e. Check status:
```
sudo systemctl status dnsmasq
```
## 5. Configure Mosquitto to Bind Only to Ethernet
a. Edit config file:
```
sudo nano /etc/mosquitto/mosquitto.conf
```
b. Add to the end:
```
# Start listener on TCP port 1883, bind to Ethernet IP (use "0.0.0.0" for all interfaces)
listener 1883 192.168.4.1
# Allow anonymous access (set to false and use "password_file /etc/mosquitto/passwd" for
authentication)
allow_anonymous true
```
c. Save and exit: `Ctrl+O`, `Enter`, then `Ctrl+X` 
d. Reboot:
```
sudo reboot
```
## 6. Test MQTT
```
mosquitto_sub -h 192.168.4.1 -t test/topic &
mosquitto_pub -h 192.168.4.1 -t test/topic -m "Hello IoT"
```
## 7. Set Up File Architecture on Pi
