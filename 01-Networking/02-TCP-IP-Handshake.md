# TCP/IP Handshake & Flags: The Language of Connection

### Overview
Transmission Control Protocol (TCP) is a connection-oriented protocol. Before any data (like a password or webpage) is sent, a reliable connection must be established. This is done via the **3-Way Handshake**.

In Cybersecurity, we analyze this handshake to detect attacks like **DoS Floods** and **Stealth Scans**.

---

### **1. The 3-Way Handshake (Connection Start)**
*Think of this as a polite phone call.*

1.  **SYN (Synchronize):** Client sends a packet with the SYN flag set.
    * *Translation:* "Hello Server, I want to talk. My sequence number is X."
2.  **SYN-ACK (Synchronize-Acknowledge):** Server replies with SYN and ACK flags.
    * *Translation:* "I hear you (ACK). I am open and ready to talk (SYN). My sequence number is Y."
3.  **ACK (Acknowledge):** Client sends a final ACK packet.
    * *Translation:* "Great, connection established. I am sending data now."

**State:** Once this is done, the connection is **ESTABLISHED**.

---

### **2. The 4-Way Teardown (Connection End)**
We also need to know how connections end gracefully.
1.  **FIN:** Client says "I'm done."
2.  **ACK:** Server says "Received."
3.  **FIN:** Server says "I'm done too."
4.  **ACK:** Client says "Received. Bye."

---

### **3. TCP Flags (The "Traffic Lights")**
These are 1-bit indicators in the TCP header that tell the receiver *what* kind of packet this is.

| Flag | Name | Function | Security Context / Attack Vector |
| :--- | :--- | :--- | :--- |
| **SYN** | Synchronize | Initiates a connection. | Mass SYNs = **SYN Flood (DoS)**. |
| **ACK** | Acknowledge | Confirms receipt of data. | Used to map firewall rules (ACK Scan). |
| **RST** | Reset | Abruptly kills a connection. | "Stop talking." Used in **Stealth Scans** or by Firewalls to reject traffic. |
| **FIN** | Finish | Gracefully ends a connection. | Normal termination. |
| **PSH** | Push | Forces data to the application immediately. | Often seen in interactive shells (Telnet/SSH). |
| **URG** | Urgent | Data is high priority. | Rarely used; often suspicious in modern traffic. |

---

### **4. Security Perspective (Attack & Defense)**

#### **A. The SYN Flood (DoS Attack)**
* **The Attack:** The attacker sends thousands of **SYN** packets but **never** sends the final **ACK**.
* **The Result:** The server waits (typically 60+ seconds) for the final ACK. Its memory fills up with "half-open" connections (State: `SYN_RECV`), preventing real users from connecting.
* **The Defense:**
    * **SYN Cookies:** The server doesn't store state (RAM) until the final ACK arrives.
    * **Rate Limiting:** Firewalls limit how many SYNs one IP can send per second.

#### **B. Nmap Stealth Scan (Half-Open Scan)**
* **The Trick:** An attacker wants to check if a Port is open *without* creating a full connection log on the server.
* **The Method:**
    1.  Attacker sends **SYN**.
    2.  Server replies **SYN-ACK** (Port is Open).
    3.  Attacker immediately sends **RST** (Reset) instead of ACK.
* **Why?** The application layer never sees the connection, so it usually won't appear in standard application logs.

---

### **Interview Q&A Practice**

**Q1: You see thousands of lines in** `netstat` **showing** `SYN_RECV` **from various IPs. What is happening?**  
**A:** This is a **SYN Flood Attack.** The server has completed the second step of the handshake (SYN-ACK) but the final **ACK** is missing, leaving the server "hanging" and consuming system resources.

**Q2: In a SYN Flood scenario, who is waiting for whom?**  
**A:** The **Server is waiting for the Client.** The server has sent its SYN-ACK and is holding the connection slot open in memory until the client confirms it with the final ACK.

**Q3: To block new incoming connection attempts while allowing employees to browse the web, which flag should the firewall block on the INBOUND interface?**  
**A:** The **SYN** flag. Blocking inbound SYNs prevents outsiders from initiating a new connection, but a stateful firewall will still allow the inbound **SYN-ACK** packets to return for sessions your employees started from the inside. 

**Q4: You scan Port A and get a RST/ACK. You scan Port B and get no response (Timeout). Which is behind a firewall?**  
**A:** **Port B.** A timeout indicates the firewall is configured to **DROP** the packet silently (acting like a black hole). Port A is likely just a closed port on a live host that sent an active **REJECT (RST).**

**Q5: Why is Port 80 usually the "Server" in logs like** `192.168.1.5:80` **?**  
**A:** Port 80 is the standard port for **HTTP (Web Traffic).** In networking logs, the side using the "well-known" service port (0-1023) is almost always the server providing the service.

**Q6: What is the difference between TCP and UDP?**  
**A:** **TCP:** Connection-oriented, reliable (3-way handshake), error recovery. (e.g., HTTPS, SSH).  
&nbsp;&nbsp;&nbsp; **UDP:** Connectionless, "fire and forget," faster but unreliable. (e.g., DNS, Streaming).  

---
