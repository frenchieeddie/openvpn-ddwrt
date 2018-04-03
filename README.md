# Configure DD-WRT OpenVPN client to connect to OpenVPN server

After installing an OpenVPN server on a server, here is an explanation for newbies on how to set-up your DD-WRT (open-source router firmware) client to connect to the server. The server is configured exactly as Digital Ocean explains in their blog post.

## Set up an Open-VPN server
I simply followed the [tutorial on Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-openvpn-server-on-ubuntu-16-04): the install takes no more than 1 hour and is very straightforward. If you followed the same tuto, that means that we have the exact same type of configuration file. Otherwise, your file may differ, so you might not found this post useful to your own case. 

**DISCLOSURE** Please note that it worked for me, but I do not gurantee anything for you: I just thought that this might help some people who run into the same questions that I did.

## Client configuration file
The file generated by the bash script in the Digital Ocean tutorial looks like this
```
client
dev tun
proto udp
remote some.ip.address.XXX 1154
resolv-retry infinite
nobind
user nobody
group nogroup
persist-key
persist-tun
remote-cert-tls server
cipher AES-128-CBC
auth SHA256
key-direction 1
comp-lzo
verb 3

<ca>
-----BEGIN CERTIFICATE-----
XXXXXXXXXXXXXX
-----END CERTIFICATE-----
</ca>
<cert>
Certificate:
    DATA FROM CERTIFICATE...
    ...
    ...
    
-----BEGIN CERTIFICATE-----
XXXXXXX
-----END CERTIFICATE-----
</cert>
<key>
-----BEGIN PRIVATE KEY-----
XXXXXXXX
-----END PRIVATE KEY-----
</key>
<tls-auth>
#
# 2048 bit OpenVPN static key
#
-----BEGIN OpenVPN Static key V1-----
XXXXXX
-----END OpenVPN Static key V1-----
</tls-auth>
```

`note` I replaced the values of key and certificates for obvious reasons :) 

## Set up the DD-WRT OpenVPN client
It seems that a lot of the configuration commands (at the top of the config file) are not necessary, as DD-WRT allows you to select the correct values for each field [via the GUI](https://www.dd-wrt.com/wiki/index.php/OpenVPN#GUI:_Client_Mode).

The only remaning commands I kept were
```
resolv-retry infinite
persist-key
persist-tun
```

Put these in the `Additionnal Config` field.

The rest of the commands must be asign to the correct fields of the GUI: 
* `server IP/name` and `port` : pretty obvious
* `Tunnel Device` **TUN**
* `Tunnel Protocol` **UDP**
* `Encryption Cipher` **AES-128-CBC**
* `Hash algorithm` **SHA256**
* `Advanced Options` **ENABLE**
* `TLS Cipher` **NONE**
* `LZO Compression` **YES**
* `NAT` and `Firewall protection` **ENABLE**
* `IP Address` | `Subnet Mask` | `Tunnel UDP Fragment` *left blank*
* `Tunnel MTU setting` **1500**
* `Tunnel UDP MSS-Fix` **ENABLE**

`Note` I had to **UNCHECK** the `nsCertType verification` checkbox, otherwise it didn't work.

Other than that, here are the security fields to fill-up:
* `TLS Auth Key` copy/paste the content between the `<tls-auth></tls-auth>` tags
* `CA Cert` copy/paste the content between the `<ca></ca>` tags
* `Public Client Cert` copy/paste the content between the `<cert></cert>` tags (obviously, only the part that within *-- BEGIN CERTIFICATE --* and *-- END CERTIFICATE --*
* `Private Client Key` copy/paste the content between the `<key></key>` tags 

The others fields were left blank.

`note` All of those keys/certificate need to be paste with the header `---- BEGIN ... ---` and the footer `--- END ... ---` **included**.

You should be all set! (at least, I am :))

--------

*Thank to **egc** and **eibrad** on the [DD-WRT forum](https://www.dd-wrt.com/phpBB2/viewtopic.php?p=1124095#1124095) for their help*
