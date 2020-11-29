# urtwm
FreeBSD driver for RTL8812AU/RTL8821AU

### TO BUILD A DRIVER FOR PFSENSE YOU NEED TO DOWNLOAD THE VERSION FROM FREEBSD-downloads

In this example 11.3 was downloaded and some tweaks were needed.

Install the iso image from the selected version inside a Virtual Box machine.

After that download and compile the source.

Remember to make the changes to the files. The original file doesn't have the version for the raw REALTEK RTL8812AU.

### Alternative to svn PATCH

Add this line to kernel source in:

```sh
/usr/src/sys/dev/usb/usbdevs
```
> ...
> product REALTEK RTL8192SU 0xc512 RTL8192SU
> product REALTEK RTL8812AU 0x8812 RTL8812AU 802.11

### **How-to-build:**

1) Clone / download this repository.  
2) Apply 'patch-usbdevs.diff' to your source tree checkout; e.g.  
  *cd /usr/src/ && svn patch \<patch-usbdevs.diff location\>*  
3) Build and install firmware:  
   *cd \<repository location\>/sys/modules/urtwmfw && make && make install*  
4) Build and install driver:  
   *cd ../urtwm && make && make install*  
     
     
   
### **How-to-test:**  
1) Load the driver:  
   *kldload if_urtwm*  
In case if device was recognized successfully driver will report about that:  
> urtwm0: <802.11n WLAN Adapter> on usbus4  
> urtwm0: MAC/BB RTL8821AU, RF 6052 1T1R

or  
> urtwm0: MAC/BB RTL8812AU, RF 6052 2T2R  

2) Load necessary modules (if not already loaded):  
   *kldload wlan_amrr wlan_ccmp wlan_tkip wlan_wep*  
3) Create wlan(4) interface:  
   *ifconfig wlan1 create wlandev urtwm0*  
3) Start wpa_supplicant(8):  
   wpa_supplicant -i wlan1 -c /etc/wpa_supplicant.conf  
4) Start dhclient(8) after association / 4-Way handshake:  
   *dhclient wlan1*  
