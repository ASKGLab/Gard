# About Gard
A Guard in a Information Security setup is used to control information flow between different security classification
levels. The Guard does that by vetting network packet data passed to it for approved data structures and flows before 
allowing the data packets to flow to it's destination. This prevents unapproved network usage to take place between
networks of different classification. More details about a Guard can be found on Wikipedia: 
https://en.wikipedia.org/wiki/Guard_(information_security)

Gard (Guard cARD) is an attempts to create a cheaply available Smartcard-based Guard. The Gard's main software logic 
are securely stored and executed within the secure confines of a tamper-resistant smartcard. 

The secure execution of the Gard software is taken care of by the Secure Execution Environments within the smartcard 
as specified by the GlobalPlatform specifications for smartcards to securely isolate and execute card softwares (card 
applets).


# Limitations of Gard
A smartcard has at most a couple kilobytes of RAM space with about 2KB to 4KB RAM or 8KB for high-end cards. The 
EEPROM (permanent re-writable storage in smartcards) have about 20KB to 60KB permanent storage in most commercial 
smartcards with high-end cards reaching 500KB to 1MB of EEPROM/Flash space. It is impossible to support multiple 
protocols on the tiny amount of resource a smartcard provides so a Gard would be expected to handle at most a single 
simple protocol to allow operation of the Gard within the resource and scripting language limitations of a smartcard 
based on JavaCard 2 platforms.


# TCB
Due to the tiny RAM and permanent storage space within a smartcard, the software TCB would also have to be small
enough to not only fit into the RAM and permanent storage of the smartcard. Additional stored configurations kept
within the Gard, operational caches and cryptographic keys must be taken into account. The small TCB of the software
makes it easily readable and verifiable to humans thus making auditing work much more simple in reality.


# Supported Protocols
Remote access, mailboxes and secure file exchanges are one of the most common usage of Guards in real world scenarios.
Current work is done to locate an already existing protocol that fits the above requirements in the tiny and 
constrained environment of a smartcard which Gard operates with. Due to the limitations of JavaCard (support for byte 
and short datatypes), binary protocols are much more preferred than text-based protocols due to the need to convert
text-based characters to binary before parsing the packet data. The current protocol reviewed as a viable protocol is 
a SSL-VNC port to Gard despite some text being used in the SSL protocol. The worst case scenario is to develop a 
totally new protcol suite for use with Gard.


# How Gard Works

### Assumptions:
* Gard software are loaded into smartcards in a secure environment.
* Gard's secure channel keypairs are securely generated within the smartcard and the public key is securely extracted
and given to the smartcard's owner.
* Gard's whitelist signing secret keys (HMAC-SHA1 / HMAC-SHA256 / HMAC-SHA512) is securely generated within the 
smartcard during software initialization.

### Smartcard Secure Channel
* The Gard user will use a Gard client software called a GardHost (https://github.com/ASKGLab/GardHost) to establish
a secure channel between the client computer with the Gard software in the smartcard via the proper knowledge of the 
Gard's unique secure channel public key which the user would conduct a Diffie-Hellman Ephemeral Key Exchange to derive
a session key.
* The Gard signs a whitelist of data commands, data structures, data flows and optionally allowed target addresses and ports. The cache of whitelist can be stored within the smartcard (if there are space) or stored unencrypted but signed
on a whitelist file on the host computer. Encryption of the whitelist would be explored in later iterations of design.
* Whenever Gard finds a data command, data flow, data structure or essential oeprational parameters not within it's 
internal cache in the smartcard, it will request the GardHost to send it the externally hosted signed whitelist to 
search for the data command, data flow, data structure or other essential operation parameters not found within it's 
internal cache.
* Approved data are allowed to flow to and from the target system.


