[中文](README.zh.md) | English
# Macaw - A Secure Chat Application

**Macaw** is an open-source encrypted chat application built with Rust. It focuses on providing a secure and private messaging experience across platforms. Whether you’re on desktop or mobile, Macaw keeps your messages safe.

**Currently under development.**

## Features
- End-to-end encryption for private messaging
- Cross-platform (Windows, macOS, Linux, and mobile)
- Open-source with a focus on security and performance

### **Macaw Server-Side Principle Overview**

**Goal**: Build a simple server to store encrypted messages and ensure messages are recycled after a certain period. The server does not decrypt messages or send messages actively, but only stores, queries, and recycles them.

#### **Basic Workflow**:
1. **Receiving Messages**:
    - The message format sent by the client is: `[storage time][recipient code][encrypted message]`.
    - Upon receiving a message, the server records the storage time and stores it in the message pool.

2. **Message Storage**:
    - The server stores the received messages in an in-memory data structure (such as `HashMap`), where each message is accompanied by its storage time and expiration time (current time plus storage duration).

3. **Recycling Mechanism**:
    - To prevent the message pool from growing too large, the server periodically checks all stored messages and deletes the expired ones.
    - The recycling mechanism runs periodically (e.g., once every hour), checking each message's expiration time and cleaning up expired messages.

4. **Message Query**:
    - The client queries the server for messages related to its own recipient code. The server only returns the relevant encrypted message contents and does not decrypt the messages.

#### **Brief Steps**:
1. **Storage**: The server stores messages in memory, including the message's storage time, recipient code, and encrypted content.
2. **Recycling**: Periodically cleans up expired messages to prevent the message pool from growing too large.
3. **Querying**: Clients can query relevant encrypted messages using their recipient code, and the server only returns the encrypted content, leaving decryption to the client.

#### **Message Format**:
- Each message contains:
    - **Storage Time**: The time when the message is stored, which determines the message's lifespan.
    - **Recipient Code**: The identifier of the message's recipient, used for querying and filtering.
    - **Encrypted Message**: The message content, encrypted for privacy.

#### **Recycling Mechanism**:
- The server periodically checks the message pool and deletes expired messages to ensure the pool does not grow indefinitely.

#### **Core Technologies**:
- Use of the `tokio` asynchronous framework to handle concurrent requests.
- In-memory `HashMap` for storage, with the option to extend it to file or database storage.
- Recycling mechanism triggered periodically by a scheduled task to clean up expired messages.

### **Summary**
- **Server Responsibilities**: Store encrypted messages, provide message querying and recycling, ensure message privacy and validity.
- **Client Responsibilities**: Encrypt and decrypt messages, query relevant messages using the recipient code.

### **Macaw Client-Side Overview**

**Goal**: Build a client that can encrypt and decrypt messages, send encrypted messages to the server, and query messages from the server using a unique recipient code. The client ensures message privacy through encryption, while the server only stores and returns encrypted data.

#### **Basic Workflow**:
1. **Encrypting Messages**:
    - The client encrypts the message content before sending it to the server.
    - The message is packaged with the recipient’s code and an expiration time.
    - The encrypted message is sent to the server for storage.

2. **Decrypting Messages**:
    - The client queries the server for messages related to its recipient code.
    - The server returns the encrypted message.
    - The client decrypts the message and displays it to the user.

3. **Message Sending**:
    - The client sends the message (with encryption) to the server, which is then stored and made available for the recipient to retrieve.

4. **Privacy**:
    - Messages are encrypted using a secure algorithm (e.g., AES).
    - The server cannot read or modify the message content as it only stores encrypted data.

#### **Message Format**:
- Messages sent from the client are encrypted and include:
    - **Recipient Code**: The identifier of the recipient, used by the server to store and retrieve messages.
    - **Encrypted Message**: The actual content, which is encrypted to ensure privacy.
    - **Expiration Time**: The time after which the message will be considered expired and can be deleted by the server.

### **Summary**
- **Client Responsibilities**: Encrypt and decrypt messages, send messages to the server, and query relevant encrypted messages using the recipient code.
- **Server Responsibilities**: Store encrypted messages and allow clients to query and retrieve them, without being able to decrypt the messages.
