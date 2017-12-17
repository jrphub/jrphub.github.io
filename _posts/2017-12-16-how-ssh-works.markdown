---
title:  "23 - How SSH Works!"
date:   2017-12-16 11:30:23
categories: [Unix]
tags: [ssh]
---
Secure Shell (SSH) provides **Encryption** and **Authentication** features, which enhance security.  Other two important features are
1. SSH Tunneling
2. TCP Port forwarding

For authentication, two commonly used mechanism:
1. Password based
2. Key based (recommended)

SSH protocol also has different iterations and among them the most widely used versions are 

 - SSH version 1
 - SSH version 2 (recommended)

When we discuss encryption and data security, there are two types of primarily used cryptographic systems. 

1. Public Key cryptography(or sometimes called as asymmetric cryptography)
2. Secret key cryptography (or sometimes called as symmetric cryptography). 

Most of the modern day security system's use these two types, in multiple ways to ensure security in communication. 

Asymmetric encryption has a lot of overhead involved, and is a little bit time consuming to decrypt data using it. So Asymmetric encryption is only used to share the secret key, which will be used for symmetric encryption.

In order to establish SSH connection, we need to make a secure channel between server and client. First client authenticates the server as the connection is initiated by client. Then only, a secure symmetric channel is formed between them. This secure channel is used for authenticating client, sharing keys, passwords etc. 

Let's go step by step:

 - Server discloses its identity to the client

This identity is a rsa public key of the server and it is mostly located in /etc/ssh directory .

![rsa pub key](http://i.imgur.com/r6Q1tNs.png)

if the client is communicating with server for the first time, the client will get the warning like this:

    Are you sure you want to continue connecting (yes/no)?

After the first connection, this host (server) identity key will be saved in a file called "**known_hosts**" in client machine, so that in future client will not get such warning while connecting to the server.

> The above key is a host identity, not a user identity. So, any client
> connecting to that server will get same host key as a server identity.

 - Server key

This key is generated each and every hour according to default configuration, mentioned in ssh configuration file (/etc/ssh/sshd_config)

![server-key generation](http://i.imgur.com/ZmbwHtm.png)

 - Check bytes
8 random bytes which are also called checkbytes. It is necessary for the client to send these bytes in its reply to the server, during its next reply.

 - Encryption

	According to the list of encryption algorithms supported by the server, the client simply creates a random symmetric key and sends that symmetric key to the server. This symmetric key will be used to encrypt as well as decrypt the whole communication during this session.

	The client takes an additional care while sending this session key(symmetric key for this session) to the server, by doing a double encryption. The first encryption is done by the server host key(which was shared by the server), and the second encryption is done by the server key(which will keep on changing every one hour) This double encryption increases the security, because even if somebody has the host private key of the server (/etc/ssh/ssh_host_rsa_key), he will not be able to decrypt the message, because its still encrypted by the server key, which keeps on changing on an hourly basis. Along with this double encrypted session key, the client will also send the selected algorithm from the list of supported algorithm given by the server.

 - Authenticating the Server
In order to authenticate the server, the client needs to be sure, that the server was able to decrypt the session key send during step 4. So after sending the session key(which is double encrypted with server key and server host key), the client waits for a confirmation message from the server.
The confirmation from the server must be encrypted with the symmetric session key, which the client send. This step of waiting for a confirmation message is very important for the client, because the client has no other way to verify whether the server host key send, was from the intended server.
Once the client receives a confirmation from the server, both of them can now start the communication with this symmetric encryption key.

 - Authenticating the Client
The client authentication happens over this encrypted channel. There are multiple methods that can be used to authenticate the client

 - Public Key
 - Password
 - Kerberos
 - Rhosts

		**Password Authentication**: (Most commonly used) - Similar to login to local user using the correct password. The password transmitted by the client to the server is encrypted through the session symmetric key(which only the server and the client knows)

**Public Key Authentication**: (Strongest)
	1. Client needs to create private (id_rsa) and public (id_rsa.pub) key by doing ssh-keygen
				![ssh-keygen](http://i.imgur.com/YGu1O8z.png)
	2. Share public key with the server(s) by adding the content of id_rsa.pub file to the **server side authorized_keys** file. This authorized_key file should be present in .ssh directory of server.
	3. Server on receiving public key authentication request , will first generate a random 256 bit string as a challenge for the client, and encrypt it with the client public key, which is inside the authorized_keys file.
	4. The client on receiving the challenge, will decrypt it with the private key(id_rsa).
	5. Client combine challenge string with session key (which was previously negotiated and is being used for symmetric encryption.) and will generate md5 hash value and sent to the server.
	6. Server will generate its own md5 hash value using challenge String and session key and compare the value with the value sent by the client. If it matches, client authentication succeeds.


**SSH protocol version 2**
SSH protocol version 2 is the default protocol used these days. 
Difference between SSH1 and SSH2

 - Diffie-Hellman key is used instead of the server key for sharing the
   session key in version 2 protocol

 - No Rhosts support in ssh 2

 - SSH protocol version 1 only allows negotiation of the symmetric
   encryption algorithm, all other things are hard corded(mac,
   compression etc)

 - SSH 2 supports certificates for public keys used

 - SSH 2 server can dictate the client to use multiple authentication
   methods in a single session to succeed. However ssh version 1 only
   supports one method per session

 - SSH version 2 allows the change of session key periodically.

