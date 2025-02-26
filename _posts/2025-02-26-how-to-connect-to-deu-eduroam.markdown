---
layout: post
title:  "How to Connect to Eduroam at DEU on Arch Linux"
---

Connecting to WiFi networks can sometimes be troublesome on Arch. Most of the time, it's a breeze if you're comfortable with NetworkManager or iwctl. However, connecting to eduroam is a bit more challenging on Linux because it uses something called *802.1X authentication*.

In this blog, I will explain how I connected to my university's eduroam on my Arch setup so others don’t have to struggle as much as I did.

Im using Arch linux with Awesome WM, and for networking, I use NetworkManager and iwd. No matter what your OS is you should be able to connect with the last two.
Firstly you will need to install the <u>[(CAT)configuration assistant tool](https://cat.eduroam.org/)</u> which suppose to do the configuration for us. But in my attemp it wasn't enough for me. 

After the configuration, we need to write a *wpa_supplicant* file because of the 802.1X authentication.
Create a file in /var/lib/iwd/eduroam.8021x. And paste the following text with your credentials.
```
[Security]
EAP-Method=TTLS
EAP-Identity=<your-name-surname>@ogr.deu.edu.tr
EAP-Anonymous-Identity=anonymous@deu.edu.tr
EAP-TTLS-Phase2-Method=Tunneled-PAP
EAP-TTLS-Phase2-Identity=<your-name-surname>@ogr.deu.edu.tr
EAP-TTLS-Phase2-Password=<your-password>
EAP-TTLS-CACert=/var/lib/iwd/eduroam.pem
```
The eduroam installer we ran places a certificate to ~/.config/cat_installer/ca.pem. If you are running the iwd as root, you need to copy that certificate to a root location. So, copy the certificate as needed to feed the *wpa_supplicant*.
```
sudo cp ~/.config/cat_installer/ca.pem /var/lib/iwd/eduroam.pem
```

Finally you should restart the NetworkManager and the iwd to make changes take place.

```
sudo systemctl restart NetworkManager
sudo systemctl restart iwd 
```

And thats it now you can connect using nmcli or nmtui.

If an error occurs while connecting, you can always check the iwd journal for a diagnosis like this.
```
journalctl -u iwd --no-pager | tail -50
```

And if you want to read more about iwd or eduroam, you can always check the <u>[wiki](https://wiki.archlinux.org/title/Iwd)</u>.

