# How FTP Server Works
FTP server works with the client server architecture to communicate and transfer files.

FTP is a stateful protocol, that means connections between clients and servers stay open during an FTP session.

To send or receive files from an FTP server, you can use FTP commands, these commands are executed consecutively. It is like a queue, one by one.

There are two types of FTP connections initiated:

Control connection also called a command connection.
Data connection.
When you establish an FTP connection, the TCP port 21 opens to send your login credentials, this connection is called control connection.

When you transfer a file, a data connection is started.

There are two types of data connection:

-Passive mode.
-Active mode.

Active connections are initiated by the remote server, and the client waits for server requests.

Passive connections initiated by the client to the remote server and the server waits for requests.

When the FTP client starts a transfer, there is an option on your FTP client that controls whether you want to use active or passive FTP connection.

## Active Mode
The client connects from a random ephemeral source port to the FTP control port 21.

You can check your ephemeral port range using this command:
```
nano /proc/sys/net/ipv4/ip_local_port_range
```
When you need to transfer a file, the remote FTP server will open port 20 to connect to the FTP client.

Active mode connections usually have problems with firewalls, TCP ports 20 and 21 should be open on your firewall.

Because of these problems with firewalls of active mode, the passive mode was introduced.

If you are using iptables firewall I recommend you to review Linux iptables firewall to know how to allow specific ports.

## Passive Mode
In passive mode, the client starts the control connection from a random port to the destination port 21 on the remote server.

if the FTP client requests a file, it will issue the PASV FTP command. The server will open a random port and give this port number to the client.

That’s why the FTP is a connection-hungry protocol because every time you make a data connection (like transfer a file) the server will do the above process and this is done with all clients connected to the server.

In passive mode, the control and data connections started by the FTP client.

 



## Vsftpd FTP Server Features
There are several FTP servers available for you to use, commercial and open source.

Vsftpd has some security features which makes it on the top like:

Can run as a normal user with privilege separation.
Supports SSL/TLS FTP connections.
Can jail users into their home directories.
 


## FTP Server Setup
Some Linux distros shipped with vsftpd, anyway, if you want to install it on Red Hat based systems, you can use the following command:
```
sudo dnf -y vsftpd
```

On Debian based distros like Ubuntu, you can install it like this:
```
sudo apt-get install vsftpd
```

Once you’ve installed the package, you can run the service and enable it to run at boot time.
```
systemctl start vsftpd

systemctl enable vsftpd
```

The configuration file for vsftpd FTP server is
```
nano /etc/vsftpd.conf
```

Actually, the FTP server in Linux is one of the easiest servers that you can work with.

There are two types of accessing the FTP server:

-Anonymous FTP access: anyone can login with the username anonymous without a password.
-Local user login: all valid users on /etc/passwd are allowed to access the FTP server.
You can allow anonymous access to FTP server from the configuration, in /etc/vsftpd/vsftpd.conf by enabling anonymous_enable=YES if it is not enabled and reload your service.

Now you can try to connect to the FTP server using any FTP client, I will use the simple FTP command.

You can install it if it’s not on your system:
```
dnf -y install ftp
```

Now you can access your FTP server like this:
```
ftp localhost
```

Then type the username anonymous and with no password, just press enter.

You will see the FTP prompt.
```
ftp>
```
And now you can type any FTP command to interact with the FTP server.

## Connect as Local User
Since there is an option in the settings for allowing local users to access FTP server which is local_enable=YES, now let’s try to access the FTP server using a local user:

```
ftp localhost
```

Then type your local username and the password for that user and you will see Login successful message.

 

## Setup FTP Server as Anonymous Only
This kind of FTP server is useful if your files should be available for users without any passwords or login.

You need to configure vsftpd to allow only anonymous user.

Open /etc/vsftpd/vsftpd.conf file, and change the following options with the corresponding values.

```
listen=NO

listen_ipv6=NO

anonymous_enable=YES

local_enable=NO

write_enable=NO
```

Then we need to create a non-privileged system account to be used for anonymous FTP-type access.

```
useradd -c " FTP User" -d /var/ftp -r -s /sbin/nologin ftp
```
This user has no privileges on the system, so it is safer to use it when accessing an FTP server.

Don’t forget to restart your FTP server after you modify the configuration file.

You can access the FTP server from the browser, just type ftp://youdomain/

 


## FTP Server Security
We can configure vsftpd to use TLS, so the transferred files over the network is a bit more secure.

First, we generate a certificate request using openssl command:

```
openssl genrsa -des3 -out FTP.key
```
Then we generate a certificate request:

```
openssl req -new -key FTP.key -out certificate.csr
```

Now we remove the password from the key file:

```
cp FTP.key FTP.key.orig

openssl rsa -in FTP.key.orig -out ftp.key
```

Finally, we generate our certificate:

```
openssl x509 -req -days 365 -in certificate.csr -signkey ftp.key -out mycertificate.crt
```

Now we copy the certificate file and the key and to /etc/pki/tls/certs:

```
cp ftp.key /etc/pki/tls/certs/

cp mycertificate.crt /etc/pki/tls/certs
```

Now, all we need to do is to configure vsftpd to support secure connections.

Open / etc/vsftpd/vsftpd.conf file and add the following lines:

```
ssl_enable=YES

allow_anon_ssl=YES

ssl_tlsv1=YES

ssl_sslv2=NO

ssl_sslv3=NO

rsa_cert_file=/etc/pki/tls/certs/mycertificate.crt

rsa_private_key_file=/etc/pki/tls/certs/ftp.key

ssl_ciphers=HIGH

require_ssl_reuse=NO
```

Restart your service to reflect these changes. And that’s it.

Try to connect to your FTP server from any client on any system like Windows and choose the secured connection or FTPS, and you will successfully see your folders.

 


## SFTP vs. FTPS
In the last example, we saw the FTP over SSL layer (FTPS) and we’ve successfully connected to the FTP server, however, with the tightly secured firewall, it is difficult to manage this kind of connection since FTPS uses multiple port numbers.

The best solution, in this case, is to use SFTP (FTP over SSH).SFTP uses port 22 only.

This port is used for all connections during FTP sessions.

If you are using a firewall, it’s recommended to choose SFTP, since it needs only one port.

 

## Jailing FTP Users
You can secure your FTP server by jailing your FTP users in their home directories and allow only specific users to access the service.

Open /etc/vsftpd/vsftpd.conf and uncomment the following options:

```
chroot_local_user=YES

chroot_list_enable=YES

chroot_list_file=/etc/vsftpd.chroot_list
```

The file /etc/vsftpd.chroot_list contains the list of jailed users one per line.

Save the files and restart your service.

```
systemctl restart vsftpd
```

## Linux FTP Server Commands
You can use any GUI client to upload and download your files, but you need to know some FTP server commands also.

You can print the current working directory using pwd command:
```
ftp> pwd
```
You can list files using the ls command:
```
ftp> ls
```
Also, you can use the cd command to change the working directory:
```
ftp> cd /
```
If you want to exit your FTP session use the bye command:

```
ftp> bye
```

lcd command is used to display the local folder, not the FTP folder:
```
ftp> lcd
```
You can change the local directory using the lcd command:
```
ftp> lcd /home
```
You can download a file using the get command:
```
ftp> get myfile
```
Also, you can download multiple files using the mget command:
```
ftp> mget file1 file2
```
Use delete command to delete a file from the server:
```
ftp> delete filename
```
Use put command to upload a file to the server:
```
ftp> put filename
```
To upload multiple files, use the mput command:
```
ftp> mput file1 file2
```
You can create a directory using the mkdir command:
```
ftp> mkdir dirName
```
Or you can delete a directory from the server using the rmdir command.
```
ftp> rmdir dirName
```
There are two modes for file transfer when using FTP server, ASCII mode, and binary mode, you can change the mode like this:
```
ftp> binary

ftp> ascii
```
The FTP server is one of the easiest servers in Linux to configure and work with.

## To allow the FTP user have full control of the HTML folder type commands below:
```
sudo chown -R ftpgame /var/www/html/*
```
https://unix.stackexchange.com/questions/39466/vsftpd-553-could-not-create-file-permissions

If you need help on making a user like defined above please read link below:

https://www.tecmint.com/add-users-in-linux/