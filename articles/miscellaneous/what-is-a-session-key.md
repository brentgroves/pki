https://www.cloudflare.com/learning/ssl/what-is-a-session-key/
What is a session key?
A session key is any symmetric cryptographic key used to encrypt one communication session only. In other words, it's a temporary key that is only used once, during one stretch of time, for encrypting and decrypting datasent between two parties; future conversations between the two would be encrypted with different session keys. A session key is like a password that someone resets every time they log in.

In TLS (historically known as "SSL"), the two communicating parties (the client and the server) generate session keys at the start of any communication session, during the TLS handshake. The official RFC for TLS does not actually call these keys "session keys", but functionally that's exactly what they are.

What is a session?
A session is essentially a single conversation between two parties. A session takes place over a network, and it begins when two devices acknowledge each other and open a virtual connection. It ends when the two devices have obtained the information they need from each other and send "close_notify" messages, terminating the connection, much like if two people are texting each other, and they close the conversation by saying, "Talk to you later." The connection can also time out due to inactivity, like if two people are texting and simply stop responding to each other.

A session can either be a set period of time, or it can last for as long as the two parties are communicating. If the former, the session will expire after a certain amount of time; in the context of TLS encryption, the two devices would then have to exchange information and generate new session keys to reopen the connection.

What is a cryptographic key?
In cryptography, it is common to talk about keys (usually a short piece of data) to refer to special inputs of a cryptographic algorithm. The most common keys are those used for data encryption; however, other types of keys exist for different purposes.

A data encryption algorithm uses a (secret) key to convert a message into a ciphertext — that is, a scrambled, unreadable version of the message. One can recover the original message from the ciphertext by using a decryption key.

In a symmetric encryption algorithm, both the encryption and decryption keys are the same. Thus, anyone holding the secret key can encrypt and decrypt data, and this is why the term symmetric keys is often used.

Contrarily, in an asymmetric encryption algorithm, also known as public-key encryption, there exist two keys: one is public and can only be used for encrypting data, whereas the other one remains private and is used only for decrypting ciphertexts.

Does HTTPS use symmetric or asymmetric cryptography?
HTTPS, which is HTTP in combination with the TLS protocol, uses both types of cryptography. All communications over TLS start with a TLS handshake. Asymmetric cryptography is crucial for making the TLS handshake work.

During the course of a TLS handshake, the two communicating devices will establish the session keys, and these will be used for symmetric encryption for the rest of the session (unless the devices choose to update their keys during the session). Usually, the two communicating devices are a client, or a user device like a laptop or a smartphone, and a server, which is any web server that hosts a website. (For more, see What is the client-server model?)

In the TLS handshake, the client and server also:

Negotiate which cryptographic algorithms to use (doing so securely via asymmetric cryptography)
Authenticate the server's identity against its TLS certificate (using asymmetric cryptography)

What is the 'master secret' in a TLS handshake? How does it relate to session keys?
The master secret is the result from combining a string of random data sent by the client, random data sent by the server, and another string of data called the "premaster secret" via an algorithm. The client and the server each have those three messages, so they should arrive at the same result for the master secret.

The client and server then use the master secret to calculate several session keys for use in that session only. They should end up with the same session keys.

Learn more about how TLS works: What happens in a TLS handshake?