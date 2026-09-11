## 🔬 SYN Flood Pattern Investigation Using TShark

**Author:** Omolara Animashawun
**Environment:** Kali Linux Virtual Machine
**Target:** Local Apache Web Server
**Target IP:** `127.0.0.1`
**Target Port:** TCP/80
**Capture Interface:** `lo` (Loopback)
**Investigation Type:** Controlled Network Traffic Analysis
**Tools:** TShark, Wireshark, Scapy, Apache2, Python 3

## 🎯 Lab Objective

The objective of this laboratory exercise was to investigate TCP SYN traffic using TShark and develop practical digital-forensic skills for identifying, extracting, quantifying and interpreting SYN-based network activity.

The investigation was conducted in two main stages.

First, a normal TCP/HTTP traffic baseline was established against an authorized local Apache web server. This provided a reference for understanding the expected TCP connection lifecycle, including the three-way handshake, HTTP communication and graceful connection termination.

Second, a strictly bounded SYN activity was generated against the same local Apache service using Scapy. The simulation was deliberately restricted to four SYN packets and was captured using TShark for forensic analysis.

The investigation therefore focused on:

* Establishing a normal TCP traffic baseline.
* Capturing and preserving network evidence.
* Identifying TCP SYN packets.
* Identifying SYN-ACK responses.
* Examining ACK and RST behaviour.
* Counting SYN packets.
* Examining TCP source ports.
* Reviewing TCP analysis events.
* Using TShark expert information.
* Comparing normal TCP behaviour with controlled SYN activity.
* Maintaining evidence integrity through SHA-256 hashing.
* Determining whether the observed traffic supports a SYN flood, a scan or simply a bounded SYN simulation.

The investigation was conducted entirely against the authorized local target:

```text
127.0.0.1:80
```

## 🛡️ Safety, Authorization and Scope

This laboratory exercise was performed within an authorized and isolated training environment.

The target was the Apache web server running locally on the Kali Linux virtual machine:

```text
IP Address: 127.0.0.1
Port: 80/TCP
Interface: lo
```

The loopback address `127.0.0.1` ensured that the activity remained within the local laboratory environment.

The SYN simulation was strictly limited to four packets.

No public, third-party or unauthorized systems were targeted.

The exercise did not involve:

* Continuous packet generation.
* High-volume packet flooding.
* Source-address spoofing.
* Testing against external systems.
* Attempts to disrupt an operational service.
* Unrestricted automated traffic generation.

The controlled SYN activity was created solely to provide a safe packet dataset for forensic analysis.

An important limitation of the investigation is that four SYN packets do not, by themselves, constitute evidence of a denial-of-service attack or a real SYN flood. A genuine SYN-flood determination would require additional evidence such as traffic volume, packet rate, duration, incomplete connection behaviour, resource impact and service availability information.

## 📁 Laboratory Workspace

A dedicated investigation directory was created to keep evidence, scripts, screenshots and analysis outputs organized.

The workspace was created using:

```bash
mkdir -p ~/SBT-DF203-Lab3/{evidence,working,exported,reports,screenshots,scripts}
cd ~/SBT-DF203-Lab3
pwd
find . -maxdepth 1 -type d -print
```

The resulting workspace structure was:

```text
SBT-DF203-Lab3/
├── evidence/
├── working/
├── exported/
├── reports/
├── screenshots/
└── scripts/
```

### 📂 Directory Purposes

**`evidence/`**

Contains preserved packet captures and other evidence files.

**`working/`**

Used for temporary working copies and intermediate analysis material.

**`exported/`**

Used for extracted or exported investigation results.

**`reports/`**

Contains case notes, hashes, packet-analysis output and supporting report material.

**`screenshots/`**

Contains screenshots captured during the investigation and used as supporting evidence.

**`scripts/`**

Contains scripts used during the authorized laboratory simulation.

### 📸 Evidence

`01_lab_folder_structure.png`

## 🔧 Tool and Environment Verification

The required tools were installed and verified before the packet-analysis activities began.

The environment included:

* TShark/Wireshark 4.6.6
* Scapy 2.7.0
* Apache 2.4.68
* Python 3
* Kali Linux
* Linux kernel `6.12.33+kali-amd64`

The tools were installed or updated using:

```bash
sudo apt update
sudo apt install -y apache2 tshark wireshark python3-scapy
```

TShark was verified using:

```bash
tshark --version
```

The installed version was:

```text
TShark (Wireshark) 4.6.6
```

Scapy was verified using:

```bash
python3 -c "import scapy; print('Scapy OK')"
```

The result was:

```text
Scapy OK
```

Apache was verified using:

```bash
apache2 -v
```

The installed version was:

```text
Apache/2.4.68 (Debian)
```

The Linux environment was running:

```text
6.12.33+kali-amd64
```

TShark displayed a libxml compatibility warning indicating that the program was compiled against libxml 215 while an older libxml 214 library was available. The warning did not prevent TShark from operating correctly, capturing traffic or reading PCAP files.

### 📸 Evidence

`02_tool_verification.png`

## 🌐 Apache Service Verification

The local Apache service was configured as the authorized target for the investigation.

Apache was enabled and started using:

```bash
sudo systemctl enable --now apache2
```

The service status was checked using:

```bash
sudo systemctl status apache2 --no-pager
```

The service was confirmed to be active and running.

TCP port 80 was then checked:

```bash
sudo ss -ltnp | grep ':80'
```

Apache was confirmed to be listening on TCP port 80.

The local web service was tested with:

```bash
curl --no-keepalive http://127.0.0.1/
```

The request returned the Apache Debian default page, confirming that the service was available.

A subsequent HTTP test was also performed:

```bash
curl --no-keepalive http://127.0.0.1/ >/dev/null && echo "Apache HTTP test: SUCCESS"
```

The test returned:

```text
Apache HTTP test: SUCCESS
```

This confirmed that the authorized target was operational before packet capture and analysis.

### 🔌 Loopback Interface

The loopback interface was verified as the capture interface.

The interface contained:

```text
lo
127.0.0.1/8
::1/128
```

The use of `lo` ensured that the investigation remained local to the Kali Linux environment.

### 📸 Evidence

`03_apache_tcp80.png`

## 📝 Case Identification and Evidence Worksheet

The system environment was documented using:

```bash
date
hostname
ip addr
```

A case worksheet was created at:

```text
reports/case_notes.txt
```

The worksheet contained:

```text
Case/Lab Identifier: SBT-DF203-Lab3
Student: Kafayat Animashawun
Course: SBT-DF203 Basic Networking Skills for Digital Forensics
Lab: Lab 3 - SYN Flood Pattern Investigation Using TShark
Environment: Kali Linux VM
Target: Local Apache service
Target IP: 127.0.0.1
Target Port: TCP/80
Scope: Authorized isolated laboratory environment
```

The worksheet established:

* The laboratory identity.
* The student conducting the investigation.
* The course.
* The laboratory exercise.
* The operating environment.
* The authorized target.
* The target IP address.
* The target TCP port.
* The authorized scope of the activity.

### 📸 Evidence

`04_system_environment.png`

`05_case_notes.png`

## 📡 Normal TCP Traffic Baseline

A normal HTTP connection was captured before the controlled SYN activity.

The purpose of the baseline was to establish the expected TCP behaviour of the Apache service before analyzing the controlled SYN traffic.

TShark was started on the loopback interface with a TCP/80 capture filter:

```bash
sudo tshark -i lo -f 'tcp port 80' -w /tmp/normal_http.pcapng
```

While the capture was active, an HTTP request was generated:

```bash
curl --no-keepalive http://127.0.0.1/
```

The resulting PCAP was preserved as:

```text
evidence/normal_http.pcapng
```

The capture was moved from `/tmp` into the evidence directory:

```bash
sudo mv /tmp/normal_http.pcapng evidence/normal_http.pcapng
sudo chown kali:kali evidence/normal_http.pcapng
```

The baseline evidence was then hashed using:

```bash
sha256sum evidence/normal_http.pcapng | tee reports/normal_http_sha256.txt
```

The recorded SHA-256 hash was:

```text
b64a4e26331fab5548158e46f5ba15554ac7016d340a9fdd5a3b6f6db84be6a3
```

This hash provides an integrity reference for the preserved baseline PCAP.

## 🤝 Baseline TCP Three-Way Handshake

The normal baseline showed the expected TCP three-way handshake.

The primary baseline session contained:

```text
Frame 1
127.0.0.1:57920 → 127.0.0.1:80
[SYN]
```

The server responded:

```text
Frame 2
127.0.0.1:80 → 127.0.0.1:57920
[SYN, ACK]
```

The client then acknowledged the server response:

```text
Frame 3
127.0.0.1:57920 → 127.0.0.1:80
[ACK]
```

The sequence demonstrates successful TCP connection establishment:

```text
Client                     Server
  |                          |
  | -------- SYN ----------> |
  | <------ SYN-ACK -------- |
  | -------- ACK ----------> |
  |                          |
```

This provided the normal TCP reference required for comparison with the controlled SYN activity.

## 🌍 Baseline HTTP Exchange

Following the successful TCP handshake, the client sent an HTTP request.

The baseline included:

```text
HTTP GET /
```

The Apache server responded with:

```text
HTTP/1.1 200 OK
```

This confirmed successful application-layer communication with the Apache service.

The baseline therefore demonstrated that TCP connection establishment was followed by successful HTTP communication.

## 🔚 Baseline TCP Termination

The normal HTTP session subsequently terminated through a graceful TCP shutdown.

The observed sequence was:

```text
Client → Server
[FIN, ACK]

Server → Client
[FIN, ACK]

Client → Server
[ACK]
```

This demonstrated normal TCP connection termination.

The complete baseline lifecycle was therefore:

```text
SYN
↓
SYN-ACK
↓
ACK
↓
HTTP GET
↓
HTTP 200 OK
↓
ACK
↓
FIN-ACK
↓
FIN-ACK
↓
ACK
```

Additional TShark output showed `TCP Port numbers reused` during subsequent traffic. This was treated as port reuse rather than as a replacement for the primary baseline session. The first ten frames represented the clean baseline session used for the investigation.

## 📊 Baseline Finding

The normal baseline established the following:

* The local Apache service was operational.
* TCP/80 was reachable.
* The TCP three-way handshake completed successfully.
* Application-layer HTTP traffic was successfully exchanged.
* The server returned `HTTP/1.1 200 OK`.
* The TCP connection terminated gracefully.

The baseline therefore represents normal TCP/HTTP behaviour against the authorized target.

### 📸 Evidence

`06_normal_baseline_capture.png`

`07_normal_baseline_handshake.png`

`08_normal_baseline_http_exchange.png`

`09_normal_baseline_termination.png`

`10_normal_baseline_hash.png`

## 🧪 Controlled SYN Simulation

A Scapy script was created to generate a strictly bounded SYN activity.

The script was saved as:

```text
scripts/bounded_syn.py
```

The script was:

```python
from scapy.all import IP, TCP, RandShort, send

TARGET = '127.0.0.1'
PORT = 80
COUNT = 4

packets = [IP(dst=TARGET)/TCP(sport=RandShort(), dport=PORT, flags='S') for _ in range(COUNT)]
send(packets, verbose=False)
print(f'Sent {COUNT} authorized training SYN packets to {TARGET}:{PORT}')
```

The key controls were:

```python
TARGET = '127.0.0.1'
PORT = 80
COUNT = 4
```

These settings restricted the activity to:

```text
Target: 127.0.0.1
Port: 80
Packet count: 4
```

The TCP flag:

```python
flags='S'
```

caused the generated packets to contain the SYN flag.

The use of:

```python
RandShort()
```

provided different ephemeral source-port values for the generated packets, allowing source-port characteristics to be examined during packet analysis.

## 📡 Controlled SYN Capture

TShark was used to capture the controlled traffic on the loopback interface.

An initial attempt was made to save the PCAP directly into the evidence directory:

```bash
sudo tshark -i lo -f 'tcp port 80' -c 20 -w evidence/bounded_syn_activity.pcapng
```

This resulted in:

```text
Permission denied
```

The capture was therefore redirected to `/tmp`:

```bash
sudo tshark -i lo -f 'tcp port 80' -c 20 -w /tmp/bounded_syn_activity.pcapng
```

The `-c 20` option limited the capture to a maximum of twenty packets and provided an additional capture boundary.

While TShark was actively capturing, the Scapy script was executed:

```bash
cd ~/SBT-DF203-Lab3
sudo python3 scripts/bounded_syn.py
```

The script returned:

```text
Sent 4 authorized training SYN packets to 127.0.0.1:80
```

This confirmed that the controlled simulation generated exactly four training SYN packets.

The resulting PCAP was checked using:

```bash
ls -lh /tmp/bounded_syn_activity.pcapng
```

The capture was approximately 1.3 KB.

The completed capture was moved into the evidence directory:

```bash
sudo mv /tmp/bounded_syn_activity.pcapng evidence/bounded_syn_activity.pcapng
sudo chown kali:kali evidence/bounded_syn_activity.pcapng
```

The resulting evidence file was verified:

```bash
ls -lh evidence/bounded_syn_activity.pcapng
```

The preserved file was owned by:

```text
kali:kali
```

and stored as:

```text
evidence/bounded_syn_activity.pcapng
```

## 🔎 Controlled SYN Packet Analysis

The completed controlled capture was examined offline using TShark:

```bash
sudo tshark -r evidence/bounded_syn_activity.pcapng
```

Offline analysis allowed the captured evidence to be reviewed without generating additional network traffic.

The investigation examined:

* TCP flags.
* Source addresses.
* Destination addresses.
* Source ports.
* Destination ports.
* TCP sequence numbers.
* TCP acknowledgment numbers.
* SYN packets.
* SYN-ACK packets.
* ACK packets.
* RST packets.
* TCP analysis events.
* Packet counts.

## 🧭 Initial SYN Identification

Initial SYN packets were identified using the TShark display filter:

```text
tcp.flags.syn==1 && tcp.flags.ack==0
```

The analysis command was:

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-Y 'tcp.flags.syn==1 && tcp.flags.ack==0' \
-T fields \
-e frame.number \
-e frame.time_epoch \
-e ip.src \
-e tcp.srcport \
-e ip.dst \
-e tcp.dstport \
-e tcp.seq \
| tee reports/initial_syns.tsv
```

The filter identifies packets where:

```text
SYN = 1
ACK = 0
```

This combination represents an initial TCP connection request.

The extracted fields included:

* Frame number.
* Packet timestamp.
* Source IP address.
* Source TCP port.
* Destination IP address.
* Destination TCP port.
* TCP sequence number.

The resulting analysis file was:

```text
reports/initial_syns.tsv
```

The destination for the controlled activity was the authorized Apache service:

```text
127.0.0.1:80
```

## 🔄 SYN-ACK Response Identification

SYN-ACK packets were identified using:

```text
tcp.flags.syn==1 && tcp.flags.ack==1
```

The extraction command was:

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-Y 'tcp.flags.syn==1 && tcp.flags.ack==1' \
-T fields \
-e frame.number \
-e frame.time_epoch \
-e ip.src \
-e tcp.srcport \
-e ip.dst \
-e tcp.dstport \
-e tcp.ack \
| tee reports/syn_ack_responses.tsv
```

The filter identifies packets containing both:

```text
SYN = 1
ACK = 1
```

For a response from Apache, the expected direction is:

```text
127.0.0.1:80 → 127.0.0.1:<source-port>
```

The acknowledgment value can be compared against the sequence number of the corresponding SYN to establish packet correlation.

The resulting file was:

```text
reports/syn_ack_responses.tsv
```

## ⚠️ ACK and RST Analysis

ACK and RST candidates were extracted using:

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-Y 'tcp.flags.reset==1 || (tcp.flags.ack==1 && tcp.len==0)' \
-T fields \
-e frame.number \
-e frame.time_epoch \
-e ip.src \
-e tcp.srcport \
-e ip.dst \
-e tcp.dstport \
-e tcp.flags \
| tee reports/ack_reset_candidates.tsv
```

This filter identifies:

* TCP packets with the RST flag set.
* TCP packets containing an ACK with zero TCP payload.

The output was saved as:

```text
reports/ack_reset_candidates.tsv
```

### 🧠 Important TCP Interpretation

The filter:

```text
tcp.flags.reset==1 || (tcp.flags.ack==1 && tcp.len==0)
```

must not be described as a filter for final ACK packets only.

A pure ACK can occur at several points during a TCP conversation.

Therefore, a final ACK should be identified by correlating:

* The preceding SYN.
* The corresponding SYN-ACK.
* Sequence numbers.
* Acknowledgment numbers.
* Packet direction.
* Packet ordering.
* TCP stream information where available.

This distinction prevents a generic pure ACK from being incorrectly classified as the final handshake ACK.

## 📊 SYN Quantitative Analysis

The number of initial SYN packets was analyzed using:

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-Y 'tcp.flags.syn==1 && tcp.flags.ack==0' \
-T fields \
-e ip.src \
-e ip.dst \
-e tcp.dstport \
| sort | uniq -c | sort -nr \
| tee reports/syn_counts_by_pair.txt
```

The resulting file was:

```text
reports/syn_counts_by_pair.txt
```

This analysis grouped the observed initial SYN traffic according to:

* Source IP.
* Destination IP.
* Destination port.

The controlled simulation was intentionally limited to four generated SYN packets, providing a small and clearly bounded dataset for quantitative analysis.

## 🔢 Unique SYN Source Ports

The source ports used by the initial SYN packets were extracted using:

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-Y 'tcp.flags.syn==1 && tcp.flags.ack==0' \
-T fields \
-e tcp.srcport \
| sort -n | uniq \
| tee reports/unique_syn_source_ports.txt
```

The resulting file was:

```text
reports/unique_syn_source_ports.txt
```

The analysis was used to identify the ephemeral source ports associated with the generated SYN packets.

Because the Scapy script used:

```python
RandShort()
```

the packets could use different source-port values.

Different source ports alone do not establish malicious activity. Source-port diversity must be interpreted together with traffic volume, packet timing, source-address diversity, response behaviour and service impact.

## 🧩 TCP Analysis Events

The controlled capture was also examined for selected TCP analysis events:

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-Y 'tcp.analysis.retransmission || tcp.analysis.lost_segment || tcp.analysis.duplicate_ack' \
-T fields \
-e frame.number \
-e frame.time \
-e _ws.col.Info \
| tee reports/tcp_analysis_events.tsv
```

The output was saved as:

```text
reports/tcp_analysis_events.tsv
```

The analysis considered:

* TCP retransmissions.
* Lost segments.
* Duplicate acknowledgements.

These events can provide useful context when investigating abnormal TCP behaviour.

Their presence or absence must, however, be interpreted in relation to the complete packet sequence.

## 🧠 TShark Expert Information

Additional TShark protocol-analysis information was generated using:

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-q -z expert \
| tee reports/expert_info.txt
```

The resulting file was:

```text
reports/expert_info.txt
```

The expert information provided additional protocol-level observations generated by the Wireshark/TShark dissectors.

## 🔐 Evidence Integrity

Evidence integrity was maintained through SHA-256 hashing.

The normal baseline PCAP was hashed using:

```bash
sha256sum evidence/normal_http.pcapng | tee reports/normal_http_sha256.txt
```

The recorded baseline hash was:

```text
b64a4e26331fab5548158e46f5ba15554ac7016d340a9fdd5a3b6f6db84be6a3
```

The controlled SYN capture was hashed using:

```bash
sha256sum evidence/bounded_syn_activity.pcapng \
| tee reports/bounded_syn_sha256.txt
```

The controlled capture hash was recorded in:

```text
reports/bounded_syn_sha256.txt
```

SHA-256 provides an integrity reference that can be recalculated later to determine whether an evidence file has changed.

## 🧪 Evidence Preservation and Troubleshooting

An important practical issue encountered during the investigation was a permission error when TShark attempted to write directly to the evidence directory.

The initial command:

```bash
sudo tshark -i lo -f 'tcp port 80' -c 20 -w evidence/bounded_syn_activity.pcapng
```

returned:

```text
Permission denied
```

Instead of changing the evidence-directory permissions unnecessarily, the capture was redirected to `/tmp`:

```bash
sudo tshark -i lo -f 'tcp port 80' -c 20 -w /tmp/bounded_syn_activity.pcapng
```

After capture completion, the file was moved into the evidence directory:

```bash
sudo mv /tmp/bounded_syn_activity.pcapng evidence/bounded_syn_activity.pcapng
sudo chown kali:kali evidence/bounded_syn_activity.pcapng
```

This provided a practical way to preserve the completed capture without modifying the laboratory directory permissions.

### 🧪 Baseline Capture Troubleshooting

An earlier baseline capture attempt resulted in an extremely small PCAP because the HTTP request was not generated while TShark was actively capturing.

This demonstrated an important packet-capture principle:

> The capture must be running at the time the network activity occurs.

The baseline capture was therefore repeated correctly by:

1. Starting TShark.
2. Generating the HTTP request.
3. Allowing the traffic to be captured.
4. Stopping the capture.
5. Preserving the resulting PCAP.
6. Calculating its SHA-256 hash.

### 🔎 Live Capture Validation

A live TShark test was also performed before relying on the final saved captures:

```bash
sudo tshark -i lo -f 'tcp port 80'
```

An HTTP request was then generated.

The live capture successfully displayed:

* SYN.
* SYN-ACK.
* ACK.
* HTTP GET.
* HTTP response.
* FIN/ACK termination.

This confirmed that:

* The loopback interface was capturing correctly.
* TCP/80 traffic was visible.
* The Apache service was responding.
* TShark was correctly decoding the captured traffic.

## ⚖️ Normal Traffic vs Controlled SYN Activity

The normal baseline and controlled activity provide two different datasets for comparison.

### 🌐 Normal TCP/HTTP Behaviour

The normal session demonstrated:

```text
SYN
↓
SYN-ACK
↓
ACK
↓
HTTP GET
↓
HTTP 200 OK
↓
ACK
↓
FIN-ACK
↓
FIN-ACK
↓
ACK
```

This represents successful TCP connection establishment, application-layer communication and graceful TCP termination.

### 🧪 Controlled SYN Activity

The controlled simulation generated exactly four SYN packets against:

```text
127.0.0.1:80
```

The traffic was intentionally created to allow SYN packet characteristics to be investigated using TShark.

The controlled activity was therefore not designed to reproduce the scale of a real-world SYN flood.

## 🕵️ Forensic Interpretation

The analysis demonstrates an important distinction between **SYN activity** and a **SYN flood**.

A SYN packet is simply part of the TCP connection-establishment process. The presence of SYN packets does not automatically indicate malicious activity.

A genuine SYN-flood investigation would normally require consideration of:

* SYN packet volume.
* SYN packet rate.
* Duration.
* Number of unique source IP addresses.
* Number of unique source ports.
* Destination-port concentration.
* SYN-ACK responses.
* Final ACK behaviour.
* RST packets.
* Retransmissions.
* Connection backlog.
* Server resource utilisation.
* Service availability.
* Firewall or IDS/IPS alerts.
* Server and application logs.
* Other corroborating network evidence.

The controlled laboratory activity was limited to four SYN packets.

Therefore, the evidence supports the conclusion that a **bounded SYN traffic simulation was conducted for forensic training and packet analysis**.

It does not support a conclusion that a real SYN-flood denial-of-service attack occurred.

## 🔎 Flood, Scan or Bounded Simulation

### 🧪 Bounded SYN Simulation

The activity is best characterized as a bounded SYN simulation because:

* The target was explicitly authorized.
* The target was the local loopback address.
* The destination was TCP/80.
* The packet count was intentionally restricted to four.
* The Scapy script explicitly reported four authorized training packets.
* The traffic was captured for forensic analysis.

### 🔍 SYN Scan

A SYN scan is generally associated with probing ports to determine which services are available.

A typical SYN scan investigation would consider:

* Multiple destination ports.
* SYN requests to different services.
* SYN-ACK responses from open ports.
* RST responses from closed ports.

The current laboratory activity was restricted to TCP/80 and therefore should not automatically be classified as a port scan.

### 🌊 SYN Flood

A SYN flood requires considerably stronger evidence than the presence of SYN packets.

A forensic conclusion of SYN flooding would generally require evidence of sustained or high-volume SYN activity, incomplete connections and potentially service degradation or resource exhaustion.

The four-packet laboratory simulation is insufficient to establish such a finding.

## 🛡️ Detection and Mitigation Considerations

In a real production environment, SYN-flood detection should consider both network and host-level indicators.

Useful detection mechanisms include:

* Monitoring SYN packet rates.
* Monitoring incomplete TCP connections.
* Monitoring connection backlog utilisation.
* Monitoring unusual increases in SYN-ACK responses.
* Monitoring source-address and source-port patterns.
* IDS/IPS detection rules.
* Firewall traffic monitoring.
* Server resource monitoring.
* Correlation with application and operating-system logs.

Potential defensive controls include:

### 🔹 SYN Cookies

SYN cookies can help protect TCP services from excessive half-open connections by reducing reliance on connection-state resources before the handshake is completed.

### 🔹 Rate Limiting

Network or host-based controls can limit excessive connection attempts from a source or toward a service.

### 🔹 Firewall Controls

Firewalls can provide filtering and rate-control mechanisms for suspicious TCP traffic.

### 🔹 IDS/IPS Monitoring

Intrusion detection and prevention systems can identify abnormal SYN rates, unusual connection patterns and known attack signatures.

### 🔹 Service and Resource Monitoring

Network evidence should be correlated with:

* CPU utilisation.
* Memory utilisation.
* TCP connection tables.
* Application availability.
* Apache logs.
* Operating-system logs.

This correlation helps distinguish genuine service-impacting activity from isolated or benign SYN traffic.

## 📋 Evidence Register

| Evidence ID | Evidence Description                          | Location                               |
| ----------- | --------------------------------------------- | -------------------------------------- |
| E01         | Laboratory directory structure                | Screenshot                             |
| E02         | Tool and environment verification             | Screenshot                             |
| E03         | Apache TCP/80 verification                    | Screenshot                             |
| E04         | System date, hostname and network information | Screenshot                             |
| E05         | Case identification worksheet                 | `reports/case_notes.txt`               |
| E06         | Normal HTTP baseline PCAP                     | `evidence/normal_http.pcapng`          |
| E07         | Normal baseline SHA-256 hash                  | `reports/normal_http_sha256.txt`       |
| E08         | Controlled SYN simulation                     | `scripts/bounded_syn.py` / Screenshot  |
| E09         | Controlled SYN PCAP                           | `evidence/bounded_syn_activity.pcapng` |
| E10         | Initial SYN packet extraction                 | `reports/initial_syns.tsv`             |
| E11         | SYN-ACK response extraction                   | `reports/syn_ack_responses.tsv`        |
| E12         | ACK/RST candidate analysis                    | `reports/ack_reset_candidates.tsv`     |
| E13         | SYN counts by source/destination              | `reports/syn_counts_by_pair.txt`       |
| E14         | Unique SYN source ports                       | `reports/unique_syn_source_ports.txt`  |
| E15         | TCP analysis events                           | `reports/tcp_analysis_events.tsv`      |
| E16         | TShark expert information                     | `reports/expert_info.txt`              |
| E17         | Controlled SYN PCAP SHA-256                   | `reports/bounded_syn_sha256.txt`       |

## 📸 Screenshot and Evidence Naming

The investigation evidence screenshots were organized under:

```text
screenshots/
```

The following naming convention was used:

```text
01_lab_folder_structure.png
02_tool_verification.png
03_apache_tcp80.png
04_system_environment.png
05_case_notes.png
06_normal_baseline_capture.png
07_normal_baseline_handshake.png
08_normal_baseline_http_exchange.png
09_normal_baseline_termination.png
10_normal_baseline_hash.png
11_controlled_syn_simulation.png
12_controlled_syn_pcap.png
13_initial_syn_analysis.png
14_syn_ack_analysis.png
15_ack_rst_analysis.png
16_syn_counts.png
17_unique_source_ports.png
18_tcp_analysis_events.png
19_expert_information.png
20_controlled_capture_hash.png
```

The screenshots provide visual evidence of the major stages of the investigation.

## 🔑 Important TShark Filters

### 🟢 Initial SYN

```text
tcp.flags.syn==1 && tcp.flags.ack==0
```

Identifies initial TCP SYN packets.

### 🔵 SYN-ACK

```text
tcp.flags.syn==1 && tcp.flags.ack==1
```

Identifies TCP SYN-ACK responses.

### 🔴 RST

```text
tcp.flags.reset==1
```

Identifies TCP reset packets.

### 🟡 ACK/RST Candidates

```text
tcp.flags.reset==1 || (tcp.flags.ack==1 && tcp.len==0)
```

Identifies RST packets and ACK packets with zero TCP payload.

This should not be interpreted as a filter that identifies only final handshake ACKs.

### 🧩 TCP Analysis Events

```text
tcp.analysis.retransmission || tcp.analysis.lost_segment || tcp.analysis.duplicate_ack
```

Identifies selected TCP analysis events.

## 🧰 Key Commands Used

### 📁 Create laboratory workspace

```bash
mkdir -p ~/SBT-DF203-Lab3/{evidence,working,exported,reports,screenshots,scripts}
cd ~/SBT-DF203-Lab3
```

### 🔧 Verify tools

```bash
tshark --version
python3 -c "import scapy; print('Scapy OK')"
apache2 -v
```

### 🌐 Start Apache

```bash
sudo systemctl enable --now apache2
sudo systemctl status apache2 --no-pager
```

### 🔌 Verify TCP/80

```bash
sudo ss -ltnp | grep ':80'
```

### 🌍 Test Apache

```bash
curl --no-keepalive http://127.0.0.1/
```

### 📡 Capture normal traffic

```bash
sudo tshark -i lo -f 'tcp port 80' -w /tmp/normal_http.pcapng
```

### 📦 Preserve normal baseline

```bash
sudo mv /tmp/normal_http.pcapng evidence/normal_http.pcapng
sudo chown kali:kali evidence/normal_http.pcapng
```

### 🔐 Hash normal baseline

```bash
sha256sum evidence/normal_http.pcapng | tee reports/normal_http_sha256.txt
```

### 🧪 Execute controlled SYN simulation

```bash
sudo python3 scripts/bounded_syn.py
```

### 📡 Capture controlled SYN activity

```bash
sudo tshark -i lo -f 'tcp port 80' -c 20 -w /tmp/bounded_syn_activity.pcapng
```

### 📦 Preserve controlled SYN capture

```bash
sudo mv /tmp/bounded_syn_activity.pcapng evidence/bounded_syn_activity.pcapng
sudo chown kali:kali evidence/bounded_syn_activity.pcapng
```

### 🔎 Read controlled capture

```bash
sudo tshark -r evidence/bounded_syn_activity.pcapng
```

### 🧭 Extract initial SYN packets

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-Y 'tcp.flags.syn==1 && tcp.flags.ack==0' \
-T fields \
-e frame.number \
-e frame.time_epoch \
-e ip.src \
-e tcp.srcport \
-e ip.dst \
-e tcp.dstport \
-e tcp.seq \
| tee reports/initial_syns.tsv
```

### 🔄 Extract SYN-ACK responses

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-Y 'tcp.flags.syn==1 && tcp.flags.ack==1' \
-T fields \
-e frame.number \
-e frame.time_epoch \
-e ip.src \
-e tcp.srcport \
-e ip.dst \
-e tcp.dstport \
-e tcp.ack \
| tee reports/syn_ack_responses.tsv
```

### ⚠️ Extract ACK/RST candidates

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-Y 'tcp.flags.reset==1 || (tcp.flags.ack==1 && tcp.len==0)' \
-T fields \
-e frame.number \
-e frame.time_epoch \
-e ip.src \
-e tcp.srcport \
-e ip.dst \
-e tcp.dstport \
-e tcp.flags \
| tee reports/ack_reset_candidates.tsv
```

### 📊 Count SYN packets

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-Y 'tcp.flags.syn==1 && tcp.flags.ack==0' \
-T fields \
-e ip.src \
-e ip.dst \
-e tcp.dstport \
| sort | uniq -c | sort -nr \
| tee reports/syn_counts_by_pair.txt
```

### 🔢 Extract unique SYN source ports

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-Y 'tcp.flags.syn==1 && tcp.flags.ack==0' \
-T fields \
-e tcp.srcport \
| sort -n | uniq \
| tee reports/unique_syn_source_ports.txt
```

### 🧩 Extract TCP analysis events

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-Y 'tcp.analysis.retransmission || tcp.analysis.lost_segment || tcp.analysis.duplicate_ack' \
-T fields \
-e frame.number \
-e frame.time \
-e _ws.col.Info \
| tee reports/tcp_analysis_events.tsv
```

### 🧠 Generate expert information

```bash
tshark -r evidence/bounded_syn_activity.pcapng \
-q -z expert \
| tee reports/expert_info.txt
```

### 🔐 Hash controlled evidence

```bash
sha256sum evidence/bounded_syn_activity.pcapng \
| tee reports/bounded_syn_sha256.txt
```

## 🧠 Digital Forensic Lessons Demonstrated

### 🔹 Evidence Must Be Collected Before Interpretation

The investigation first established the environment and captured traffic before drawing conclusions from the packets.

### 🔹 A Baseline Provides Context

The normal HTTP session established what expected TCP behaviour looked like:

```text
SYN → SYN-ACK → ACK
```

followed by application traffic and graceful termination.

This provided the reference required to interpret the controlled SYN activity.

### 🔹 TCP Flags Are Valuable Investigation Indicators

The investigation used:

```text
SYN
ACK
RST
FIN
```

to understand TCP connection behaviour.

### 🔹 Packet Counts Matter

A SYN packet should not be considered suspicious simply because it is a SYN.

Traffic volume, rate, timing and persistence are important when investigating a potential SYN flood.

### 🔹 Source-Port Diversity Is Not Sufficient Evidence

The presence of multiple source ports does not automatically indicate malicious activity.

Source ports must be considered together with:

* Source IP addresses.
* Destination ports.
* Packet rate.
* Traffic duration.
* Response behaviour.
* Service impact.

### 🔹 Packet Capture and Forensic Interpretation Are Different

TShark can identify and extract packet characteristics, but the investigator must interpret those characteristics in context.

The existence of SYN packets is a technical observation.

Calling the activity a SYN flood is a forensic conclusion that requires significantly more evidence.

## 🧾 Final Forensic Finding

The investigation successfully established a normal TCP/HTTP baseline and captured a controlled SYN activity against the authorized local Apache service.

The baseline demonstrated:

```text
SYN
→ SYN-ACK
→ ACK
→ HTTP communication
→ FIN/ACK termination
```

The controlled simulation was deliberately restricted to four SYN packets against:

```text
127.0.0.1:80
```

The packet capture was preserved as evidence and analyzed using TShark filters covering:

* Initial SYN packets.
* SYN-ACK responses.
* ACK/RST behaviour.
* SYN counts.
* Source ports.
* TCP analysis events.
* TShark expert information.

The evidence demonstrates a **controlled and bounded SYN traffic simulation conducted for digital-forensics training**.

The evidence does **not** establish a real SYN-flood denial-of-service attack.

The four-packet simulation does not provide sufficient scale, duration or service-impact evidence to support such a conclusion.

A real-world SYN-flood investigation would require additional evidence including sustained packet rates, traffic volume, incomplete connection accumulation, system-resource impact, service availability information and corroborating network or host logs.

## 🏁 Conclusion

This laboratory exercise demonstrated the practical use of TShark for network-forensic investigation of TCP SYN activity.

A normal HTTP baseline was first established against the local Apache service. The baseline confirmed successful TCP connection establishment, normal HTTP communication and graceful TCP termination.

A controlled Scapy-based SYN simulation was subsequently conducted. The simulation was intentionally restricted to four SYN packets and targeted only the authorized local Apache service at `127.0.0.1:80`.

TShark was used to capture, filter, extract and quantify the resulting network traffic. The investigation also demonstrated the importance of packet correlation when distinguishing SYN-ACK responses, ACK packets and RST behaviour.

Evidence preservation was supported through dedicated directories and SHA-256 hashing of the captured PCAP files.

The main forensic conclusion is that the exercise demonstrates a **bounded SYN activity pattern for training and analysis rather than a confirmed SYN flood**.

The exercise therefore successfully demonstrates the core skills required for the laboratory: controlled network capture, TCP flag analysis, quantitative packet analysis, evidence preservation, integrity verification and cautious forensic interpretation.

## 📂 Final Laboratory Directory Structure

The completed laboratory workspace is expected to contain:

```text
SBT-DF203-Lab3/
│
├── evidence/
│   ├── normal_http.pcapng
│   └── bounded_syn_activity.pcapng
│
├── working/
│
├── exported/
│
├── reports/
│   ├── case_notes.txt
│   ├── normal_http_sha256.txt
│   ├── bounded_syn_sha256.txt
│   ├── initial_syns.tsv
│   ├── syn_ack_responses.tsv
│   ├── ack_reset_candidates.tsv
│   ├── syn_counts_by_pair.txt
│   ├── unique_syn_source_ports.txt
│   ├── tcp_analysis_events.tsv
│   └── expert_info.txt
│
├── screenshots/
│   ├── 01_lab_folder_structure.png
│   ├── 03_apache_tcp80.png
│   ├── 12_syn_ack_responses.png
│   ├── ACK_RST analysis of the controlled SYN activity.png
│   ├── Apache Tool Verification.png
│   ├── Bounded syn activities.png
│   ├── Count of initial SYN packets by source, destination, and destination port.png
│   ├── Local Apache Service Verification.png
│   ├── Normal Baseline Capture and Evidence Preservation.png
│   ├── Normal TCP_HTTP Baseline Capture.png
│   ├── Preserved baseline evidence.png
│   ├── System and Network Context.png
│   ├── TShark analysis of the normal TCP connection establishment and termination.png
│   ├── TShark identification of SYN-ACK responses to the four controlled SYN packets.png
│   ├── Timeline of the four initial SYN packets.png
│   ├── Unique source ports observed in the controlled SYN activity.png
│   ├── Wireshark Tool Verification.png
│   ├── baseline handshake.png
│   ├── bounded_syn.png
│   ├── captured traffic.png
│   ├── captured traffic on the loopback interface.png
│   ├── case_notes.png
│   └── curl.png
│
└── scripts/
    └── bounded_syn.py
```

## 📌 Key Evidence Files

The primary packet-capture evidence consists of:

```text
evidence/normal_http.pcapng
evidence/bounded_syn_activity.pcapng
```

The baseline integrity reference is:

```text
b64a4e26331fab5548158e46f5ba15554ac7016d340a9fdd5a3b6f6db84be6a3
```

The controlled SYN evidence integrity reference is stored in:

```text
reports/bounded_syn_sha256.txt
```

The controlled SYN generation script is:

```text
scripts/bounded_syn.py
```

The principal TShark analysis outputs are:

```text
reports/initial_syns.tsv
reports/syn_ack_responses.tsv
reports/ack_reset_candidates.tsv
reports/syn_counts_by_pair.txt
reports/unique_syn_source_ports.txt
reports/tcp_analysis_events.tsv
reports/expert_info.txt
```

## ✅ Laboratory Completion Summary

The laboratory exercise covered the complete investigation workflow:

* Laboratory workspace preparation.
* Tool installation and verification.
* Apache service configuration.
* Local TCP/80 verification.
* Case identification.
* System-environment documentation.
* Normal HTTP baseline capture.
* Baseline TCP analysis.
* Baseline evidence preservation.
* SHA-256 integrity verification.
* Controlled Scapy SYN simulation.
* Bounded four-packet traffic generation.
* Controlled SYN packet capture.
* Controlled evidence preservation.
* TShark packet inspection.
* Initial SYN identification.
* SYN-ACK identification.
* ACK/RST analysis.
* SYN quantitative analysis.
* Source-port analysis.
* TCP analysis-event review.
* TShark expert analysis.
* Normal-versus-controlled traffic comparison.
* Forensic interpretation.
* Evidence documentation.
* Final conclusion.

The investigation demonstrates that effective network forensics requires not only identifying suspicious-looking packets, but also establishing a baseline, preserving evidence, quantifying observed activity and avoiding conclusions that are not supported by the available evidence.
