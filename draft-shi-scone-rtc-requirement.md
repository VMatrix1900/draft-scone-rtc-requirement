---
stand_alone: true
category: std
submissionType: IETF
ipr: trust200902
lang: en

title: SCONE Real Time Communication Requirement
abbrev: SCONE RTC
docname: draft-shi-scone-rtc-requirement-latest
obsoletes:
updates:
# date: 2022-02-02 -- date is filled in automatically by xml2rfc if not given

area: WIT
workgroup: SCONE

kw:
  - RTC
  - Feedback

author:
 -
  ins: H. Shi
  name: Hang Shi
  organization: Huawei
  email: shihang9@huawei.com
  city: Beijing
  country: China
 -
  ins: X. Geng
  name: Xuesong Geng
  organization: Huawei
  email: gengxuesong@huawei.com
  city: Beijing
  country: China
 -
  ins: Q. Wu
  name: Qinghua Wu
  organization: Chinese Academy of Sciences
  email: wuqinghua@ict.ac.cn
  city: Beijing
  country: China
 -
  ins: J. Zhang
  name: Jiaxing Zhang
  organization: Chinese Academy of Sciences
  email: zhangjiaxing20g@ict.ac.cn
  city: Beijing
  country: China

normative:

informative:


--- abstract

In real-time communication (RTC) applications, low latency is essential, but unstable network conditions make it challenging. Traditional control loop reacts slowly and inaccurately to network changes. A new approach is proposed, communicating bandwidth and queue information from the bottleneck to the end-host for more accurate control.

--- middle

# Introduction {#intro}

In real-time communication (RTC) transmission scenarios, low latency is one of the key requirements, especially in real-time interactive applications such as video conferencing, live streaming, and cloud gaming. For these applications, any significant delay can severely impact the user's quality of experience. However, the network environment especially in mobile networks is often changing dynamically, and factors such as bandwidth fluctuations, and delay jitters can affect transmission performance. Therefore, addressing these network fluctuations to ensure RTC with low latency is a significant technical challenge.

Traditional RTC transmission protocols typically rely on end-to-end network condition detection methods, such as ACKs or NACKs that adjust sending rates based on packet loss and latency changes. The Cubic, Reno and BBR are typical congestion control algorithms (CCAs) that adjusts transmission rates by estimating network congestion. However, end-to-end feedback mechanisms like them have several limitations:

- High feedback latency: Traditional end-to-end feedback mechanisms rely on detecting and adjusting to network conditions. For example, CCAs only react after detecting congestion or latency changes. This mechanism cannot predict sudden changes in network conditions, leading to delayed adjustments and reduced transmission efficiency.
- Insufficient feedback accuracy: Feedback mechanisms based on packet loss or latency measurements only provide rough estimates of network conditions. Due to varying network environments’ sensitivity to packet loss and latency, relying solely on these indicators makes precise adaptation difficult, often resulting in inaccurate adjustment.

To overcome these limitations, Explicit Network Feedback mechanisms like ECN have been proposed. Unlike implicit end-to-end feedback, explicit network feedback obtains real-time status information from network devices (such as routers and switches), providing faster and more accurate feedback on network conditions. However, the limited number of bits in ECN feedback results in low precision, making it difficult to meet the feedback accuracy requirements of RTC applications. Therefore, this document proposes an explicit network feedback mechanism, using bandwidth and queue information to assist the end-host in fine-grained control and reduce RTC latency.

## Terminology

RTC: real-time communication.

## Requirements Language

{::boilerplate bcp14-tagged}


# Network Assisted Real-Time Communication Optimization
The process is shown in the diagram below: the end-to-end RTC stream is sent from the server to the client. The bottleneck node provides real-time feedback of stream-level Capacity and Queue Length to the sender, assisting the sender in rate control.

~~~
                                            Feedback Loop
                                    +--->------->---------->-+
                                    |                        |
     +--------+              +-----------------+              +--------+
     |        |              |                 |              |        |
     | Client +--------------+ bottleneck node +--------------+ Server |
     |        |              |                 |              |        |
     +--------+              +-----------------+              +--------+
     --------<-----------------<------------------<---------------------
                                Data Flow
~~~

Feedback Mechanism: The bottleneck node sends network status information, including current network capacity and queue length, to the sender at fixed intervals (e.g., every 100ms). The feedback is transmitted using a lightweight way (such as emmbedding in IP header) to ensure low overhead and high timeliness.

Rate Control Strategy: The sender adjusts the transmission rate based on the network capacity feedback from the bottleneck node. If capacity is sufficient, the sender increases the transmission rate; if capacity is limited or the queue length increases, the sender reduces the rate to avoid network congestion. Additionally, the  bottleneck node can prioritize scheduling key video frames to ensure smooth playback.

With explicit feedback from the bottleneck node, the sender can respond to network changes more quickly, reducing transmission delays caused by congestion. Compared to traditional end-to-end implicit feedback, explicit feedback is more accurate and better ensures the continuity and quality of the video stream.

# Requirement of the feedback signal
To ensure that signaling is transmitted alongside the data flow, this scheme employs in-band signaling, where feedback is embedded in the headers of returning ACK packets.

The feedback contents includes:
- Network Capacity: The bottleneck node monitors the currently available network bandwidth in real-time and feeds back the available bandwidth capacity to the sender. The sender adjusts the RTC rate control (including sending rate and encoding bitrate) based on this information to maximize bandwidth utilization while avoiding network congestion.
- Queue Length: The bottleneck node also monitors the length of its internal buffer queue. When the queue length increases, it indicates growing transmission delays and potential congestion. Based on this information, the sender can timely reduce the transmission rate to prevent packet loss.

# Security Considerations

This document does not introduce any new security considerations.

--- back
