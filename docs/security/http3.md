# HTTP/3+ Security Impacts

## Upsides

### TLS secure connectivity
To support end-to-end encryption during connections, QUIC relies heavily on cryptographic and transport layer handshakes. Since QUIC directly interacts with the TLS 1.3, it has mandated the encryption used for all originating connections, and there is no option to disable TLS. QUIC also takes the responsibility of ensuring the secure connections are established while taking into consideration the confidentiality and integrity protections to all originating connections. Unlike HTTP/2 + TLS implementation, QUIC handles the TLS handshake and alerting mechanisms on its transport context, which in turn helps QUIC to establish cryptographic protections, using the keys exchanged from the handshake.

If we consider the protocol as a whole, there are two primary communications between the TLS and QUIC:
1. QUIC provides a reliable stream abstraction for the TLS to send and receive messages through the QUIC.
2. TLS updates the QUIC component with the Secret, authenticated encryption algorithms, and a key derivation function (KDF), packet protection keys,protocol state changes (such as handshake statuses, server certificates. 

Unlike HTTP/2, which uses the TLS’s “application_data” records, QUIC uses STREAM frames in the form of QUIC packets. The TLS handshakes happen in the form of CRYPTO frames, which mainly consists of handshake data in a continuous stream. QUIC is designed to send packets in parallel, sometimes bundling different messages into one and encrypting them, considering that those messages have the same encryption level. This feature provides excellent benefits to network performance while ensuring proper encryption modes are applied during transmission.

### Full forward secrecy
Perfect forward secrecy (PFS) in a protocol can be achieved when temporary private keys are exchanged between the user-agents and servers. Every session initiated by the user-agent uses a new unique session key, and it does not have any relationship with the previous session key. By using a separate session key for each transaction, any information from earlier or future sessions cannot be compromised even if any session key is compromised. From a cryptographic perspective, no key exchange can provide PFS. However, a new term, full forward secrecy, gives a realistic expectation of PFS.
QUIC uses TLS 1.3, which supports both pre-shared key (PSK) and Diffie-Hellman (DH) over elliptic curves (EC) DHE key exchanges or finite fields. The 0-RTT key exchanges provide full forward secrecy, as the cryptographic specification only accepts forward secure connections via 0-RTT handshakes. While TLS 1.2 also supports forward secrecy, the forward secrecy is technically lost during session resumption when the user-agent sends a copy of secret material protected by a symmetric key known only to the server. The protocol provides full forward secrecy even to the initial messages between the user-agent and the server. Also, since QUIC protocol does not support long-term secret keys, QUIC, with the help of TLS 1.3, can provide a full forward secrecy feature to the applications using its protocol layer. 

## Replay attack protection (see downsides 0-RTT)
The QUIC implementation is designed to store the client values of the key derivation, in addition to nonces. Any repeated requests with the same key derivation value and the nonces are identified and discarded by the server. This design is known to be a performance nightmare, considering the protocol traffic overhead between the user-agent and the server. Theoretically, the solution may seem applicable, but in practice, the protocol may get bulkier and lead to performance issues. The current design is not the best, but this stops any server from accepting the same key more than once, from the protocol level. Also, QUIC does not provide replay protection during the initial steps and starts the protection right after the server’s initial reply. QUIC’s design is to let the initial transaction be protected by the applications and save on protocol overhead. Considering that the web components may use a derived key from the session key, replay attacks can arise at this stage; however, precautionary measures can be used on the application level to mitigate this.

## IP spoofing protection
QUIC supports address validation during the handshake and requires signed proof of addresses, thereby eliminating any IP spoofing attacks. The IP address spoofing issue is mainly handled in QUIC by extensively utilizing the “source-address token,” which is a server’s authenticated-encryption block containing the user-agent’s IP address and the server’s timestamp. The user-agents can reuse the source address token generated by the servers until their IP addresses have not moved due to connectivity changes. Since the source address tokens are used as bearer tokens, they can be reused, and any IP address restrictions set by the server can be bypassed. Since the server only responds to the IP address in the token, even a stolen cookie or the token may not result in successful IP spoofing. Also, considering that QUIC supports short-lived source address tokens, the time window of a successful IP spoofing attack will be practically almost impossible.

## SSL downgrade prevention
By design, TLS 1.3 protects against TLS downgrade attacks as the protocol mandates a key hash of all handshake communications and requires the handshake receiver to verify the sent key hash. During the handshake, any detected tampering attempts on the client capabilities will result in the handshake termination with an error. Additionally, the CertificateVerify messages between the user-agent and the server include a PKCS RSA hash signature of all previous messages about the specific connection. This checksum implementation in QUIC will prevent a successful TLS downgrade attack.

## Downsides

### 0-RTT resumption vulnerabilities
One of the most advantageous features of HTTP/3 is the 0-RTT resumption, which drastically improves connectivity speed and reduces latency. However, this process only works when there has been a previous connection established successfully, and the current transaction uses the pre-shared secret that was established during the last connection.
There are some security downsides due to the 0-RTT resumption feature. One of the most common attack vectors is replaying attacks that can be caused when an adversary resends the initial packet; in specific scenarios, this may force the server to believe that the request came from a previously known client. Another security downside of the 0-RTT resumption is the partial failure of full-forward secrecy. If an adversary compromises the tokens, they can decrypt the 0-RTT communications sent by the user-agent.

### Connection ID manipulation attacks 
Connection ID manipulation attacks require an attacker to be positioned between the user-agent and the server. They can manipulate the Connection ID during the initial handshake where the client and server hello messages are exchanged. The handshake will proceed as normal and the server will assume that the connection is established, but the user-agent will fail to decrypt because the Connection ID is an input to the encryption key derivation process and the user-agent and server will compute different encryption keys. The user-agent will eventually time out and send an error message back to the server, communicating that the connection has been terminated. Since the client encrypts the error message to the server with the original encryption key, the server will fail to decrypt and will retain the connection state until the idle connection timeout expires (generally in 10 minutes).
When performed on a larger scale, the same attack may create a denial of service attack on the server, with multiple connections are retained untill the connection state expires. Another attack method to keep the connection alive would be to alter other parameters such as source-address tokens, thereby preventing clients from establishing any connection.

### UDP amplification attack
For a successful amplification attack, the adversary has to spoof the victim’s IP address and send a UDP request to the server. If the server sends back a more significant UDP response, the adversary can utilize this server behavior on a larger scale and create a DDOS attack scenario. 
Specifically, in QUIC, UDP amplification attacks occur when an adversary accepts an address validation token from the target and releases the IP address that was initially used to generate the token. An attacker can send a 0-RTT connection back to the server with the same IP address, which may have changed to point to a different endpoint. With the successful execution of this setup, the attacker can potentially instruct the server to send substantial traffic towards the victim server. To prevent this attack, HTTP/3 has rate-limiting features and short-lived validation tokens that can act as a compensating control to DDOS attacks, while partially mitigating the attack scenario.

### Stream exhaustion attack 
A stream exhaustion attack occurs when an adversary intentionally starts multiple connection streams, which may result in an endpoint getting exhausted. The attacker can utilize the exhaustion sequence by flooding the request repeatedly. While specific transport parameters can limit the number of concurrent active streams, there could be scenarios where a server configuration may have intentionally been set to a higher amount. The victim servers may be a target for such attacks due to the server’s protocol configurations to increase protocol performance.

### Connection reset attack 
Connection reset attacks are mainly sending stateless resets to the victims, thereby creating a possibility of denial of service attacks similar to TCP reset injection attacks. The potential attack vector is possible if an adversary can get a reset token generated for a connection with a specific connection ID. Finally, the attacker can use the generated token to reset an active connection having the same connection ID, causing the server to wait for the connection until the timeout happens. If this attack is made at a larger scale, the server has to considerably burn its resources just to wait for connections to complete.

### QUIC version downgrade attack 
The QUIC packet protection provides authentication and encryption for all the packets in the communication, except for the version negotiation packets. The version negotiation packets are designed to negotiate the QUIC’s version between the user-agent and the server. The feature may allow an attacker to perform version downgrades to a potentially insecure version of QUIC. This attack is currently not applicable, as there is only one version of QUIC, but it's something to watch for in the future.

### Lack of monitoring support (direktspeed-cloud-provider) Supports HTTP3/QUIC monitoring
Although several user-agents, servers, and reputable websites support HTTP3/QUIC, many network appliances such as reverse/forward proxies, load balancers, web application firewalls, and security event monitoring tools do not fully support HTTP/3. Unlike TCP, sockets are not needed in a QUIC connection, making it harder to detect hosts and malicious connections. A malicious attacker may be able to relay malicious payloads and perform data exfiltration attacks via QUIC and remain stealthy, as most detection tools would not detect QUIC traffic.