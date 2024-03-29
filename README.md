# quantum-vpn

Starting point is Microsoft project:

https://github.com/microsoft/PQCrypto-VPN

This is a repo used to play with post quantum crypto project from Microsoft.

client folder builds a vagrant machine with all the necessary requiremetns to run a PQ-VPN client
server folder builds a vagrant machine with all the necessary requirements to run a PQ-VPN server.

## Installing

```
wget https://github.com/microsoft/PQCrypto-VPN/releases/download/PQCrypto-1.1/pq-openvpn-linux-staged.tar.gz
```

Unpack pq-openvpn-linux-staged.tgz from the root directory as root. This will drop the installation in /usr/local/openvpn as well as an automatic startup script suitable for Ubuntu hosts running systemd.

```
cd /
tar -xvzf pq-openvpn-linux-staged.tar.gz
```
## Generate easy-rsa certificates

Then install `easy-rsa` on the vpn server to generate RSA certs:

```
apt-get install easy-rsa
cd /usr/share/easy-rsa
```

Generate CA with easy-rsa:


```
./build-ca 

Generating a 2048 bit RSA private key
................................................+++
...........+++
writing new private key to 'ca.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [US]:SP
State or Province Name (full name) [CA]:MA
Locality Name (eg, city) [SanFrancisco]:Madrid
Organization Name (eg, company) [Fort-Funston]:Telef
Organizational Unit Name (eg, section) [MyOrganizationalUnit]:ConnInnov
Common Name (eg, your name or your server's hostname) [Fort-Funston CA]:joseCA
Name [EasyRSA]:
Email Address [me@myhost.mydomain]:caovpn@telefonica.com
```

In /usr/share/easy-rsa/keys you can find all keys:

Now in /usr/share/easy-rsa/ do all these commands:

```
./build-key-server serverovpn

/build-key-server serverovpn
Generating a 2048 bit RSA private key
....................+++
...+++
writing new private key to 'serverovpn.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [US]:SP
State or Province Name (full name) [CA]:MA
Locality Name (eg, city) [SanFrancisco]:Madrid
Organization Name (eg, company) [Fort-Funston]:Telef
Organizational Unit Name (eg, section) [MyOrganizationalUnit]:ConnInnov
Common Name (eg, your name or your server's hostname) [serverovpn]:serverovpn
Name [EasyRSA]:
Email Address [me@myhost.mydomain]:serverovpn@telefonica.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
Using configuration from /usr/share/easy-rsa/openssl-1.0.0.cnf
Check that the request matches the signature
Signature ok
The Subject's Distinguished Name is as follows
countryName           :PRINTABLE:'SP'
stateOrProvinceName   :PRINTABLE:'MA'
localityName          :PRINTABLE:'Madrid'
organizationName      :PRINTABLE:'Telef'
organizationalUnitName:PRINTABLE:'ConnInnov'
commonName            :PRINTABLE:'serverovpn'
name                  :PRINTABLE:'EasyRSA'
emailAddress          :IA5STRING:'serverovpn@telefonica.com'
Certificate is to be certified until Dec 13 14:46:10 2029 GMT (3650 days)
Sign the certificate? [y/n]:y


1 out of 1 certificate requests certified, commit? [y/n]y
Write out database with 1 new entries
Data Base Updated
```

Build easy-rsa client certificate:

```
./build-key clientovpn

root@ubuntu1604:/usr/share/easy-rsa# ./build-key clientovpn
Generating a 2048 bit RSA private key
...+++
...........................+++
writing new private key to 'clientovpn.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [US]:SP
State or Province Name (full name) [CA]:MA
Locality Name (eg, city) [SanFrancisco]:Madrid
Organization Name (eg, company) [Fort-Funston]:Telef
Organizational Unit Name (eg, section) [MyOrganizationalUnit]:ConnInnov
Common Name (eg, your name or your server's hostname) [clientovpn]:
Name [EasyRSA]:
Email Address [me@myhost.mydomain]:clientovpn@telefonica.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
Using configuration from /usr/share/easy-rsa/openssl-1.0.0.cnf
Check that the request matches the signature
Signature ok
The Subject's Distinguished Name is as follows
countryName           :PRINTABLE:'SP'
stateOrProvinceName   :PRINTABLE:'MA'
localityName          :PRINTABLE:'Madrid'
organizationName      :PRINTABLE:'Telef'
organizationalUnitName:PRINTABLE:'ConnInnov'
commonName            :PRINTABLE:'clientovpn'
name                  :PRINTABLE:'EasyRSA'
emailAddress          :IA5STRING:'clientovpn@telefonica.com'
Certificate is to be certified until Dec 13 14:49:08 2029 GMT (3650 days)
Sign the certificate? [y/n]:y

We have the certificates created in the server.
```

Generate Diffie Hellman parameters. Diffie Hellman parameters must be generated for the OpenVPN server. On Linux/BSD/Unix:

```
./build-dh 

Generating DH parameters, 2048 bit long safe prime, generator 2
This is going to take a long time
.........+...............+..........................................................+...
```

## Server VPN

Start the server as user root (very secure), on the vagrant machine:

```
/usr/local/openvpn/sbin/openvpn --config ../etc/server.ovpn
```

## Client VPN
Start the client as user root (very secure), on the vagrant machine:

```
/usr/local/openvpn/sbin/openvpn --config ../etc/client.ovpn
```

### Check PQ-OVPN version


The release under test has been tested against the open quantum safe library fork of OpenSSL 1.0.2. 

```
 /usr/local/openvpn/sbin/openvpn --version
liboqs 1.0.0 [git:notices/65fd62a9e8038cf2] x86_64-pc-linux-gnu [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [MH/PKTINFO] [AEAD] built on Jul 24 2018
library versions: OpenSSL 1.0.2m-dev  xx XXX xxxx, LZO 2.08
```
where liboqs is the library for quantum safe cryptographic algorithms within OpenSSL.

For TLS 1.2 on the control connection, tls-cipher is the OpenVPN configuration directive we used to select the relevant PQ ciphersuites. In particular our configured choice:

```
tls-cipher OQSKEX-SIDH-MSR-ECDHE-RSA-AES256-GCM-SHA384:OQSKEX-LWE-FRODO-RECOMMENDED-ECDHE-RSA-AES256-GCM-SHA384
```


## TODO

- Use picnic to sign certificates
