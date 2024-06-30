## Entropy: A Mathematical Approach to DDoS Protection

### Introduction to DDoS Attacks

A Distributed Denial of Service (DDoS) attack is a malicious attempt to disrupt the normal traffic of a targeted server,
service, or network by overwhelming it with a flood of Internet traffic.
These attacks are "distributed" because they use multiple compromised computer systems as sources of attack traffic.
DDoS attacks are particularly nasty because they can effectively render online services unavailable to legitimate users,
causing significant financial loss, damage to reputation, and erosion of user trust.

Moreover, DDoS attacks are simple yet highly effective, making them one of the most prevalent forms of cyberattacks
today. Servers expose endpoints for traffic to visit, and each visit consumes resources and energy.
By inundating these endpoints with excessive traffic, DDoS attacks can exhaust the server's resources, leading to a
complete shutdown and making the service unavailable to legitimate users.
This combination of simplicity and effectiveness is why DDoS remains a major threat in the cybersecurity landscape.

### Architecture of a Robust DDoS Protection System

A robust DDoS protection system focuses on three core steps: **logging**, **detecting**, and **blocking**.

- Logging involves continuously recording all incoming traffic data, capturing essential details about each request.
- Detection is the next critical step, where an Intrusion Detection System (IDS) analyses the logged data in real-time,
  looking for patterns indicative of a DDoS attack. This step is particularly challenging as it requires distinguishing
  between legitimate and malicious traffic accurately.
- Finally, blocking involves automatically filtering and blocking the identified malicious traffic, ensuring that
  legitimate users can still access the service.

For example, if you detect `202.62.240.8` as a malicious address and use Nginx as the load balancer, you can block it by
configuring Nginx as follows:

```
server {
    listen 80;
    server_name yourdomain.com;

    # block specific IP address
    deny 202.62.240.8;

    # allow all other IP addresses
    allow all;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

This configuration blocks requests from the IP address `202.62.240.8`.

This streamlined approach allows for effective monitoring and rapid response to potential DDoS attacks.

### Entropy Algorithm

#### Inspiration

The concept of entropy, originating from thermodynamics and later applied to information theory by Claude Shannon, has
inspired various fields of study due to its profound ability to measure randomness and uncertainty.
In the context of network security, entropy provides a mathematical foundation to quantify the unpredictability of data
distributions, making it a valuable tool for detecting anomalies such as DDoS attacks.
The inspiration to use entropy in network security stems from its success in other domains where understanding and
managing uncertainty is crucial, highlighting its potential to uncover irregular patterns in network traffic.

### Essence

The entropy algorithm, in the realm of information theory, calculates the level of unpredictability or randomness in a
dataset.
For network security, it involves analysing the distribution of various network attributes, such as source IP addresses,
destination IP addresses, or packet sizes, over a given period.
The algorithm works by counting the occurrences of each unique attribute, computing the probabilities of these
occurrences, and then applying Shannon's formula to calculate the entropy.
The resulting entropy value provides a snapshot of the distribution's randomness, where higher entropy indicates more
randomness and lower entropy signifies more predictability.

### Why

Using the entropy algorithm for DDoS detection is advantageous for several reasons.

- Firstly, DDoS attacks often involve flooding a network with traffic from multiple sources, drastically altering the
  usual distribution patterns of network attributes. By monitoring entropy levels, sudden drops or spikes can signal an
  ongoing attack.

- Secondly, entropy is a lightweight and efficient metric, capable of real-time computation without significant

  computational overhead. This makes it suitable for continuous monitoring in dynamic network environments.
- Lastly, entropy-based detection is versatile and can be integrated with other detection mechanisms to enhance the
  overall robustness and accuracy of the security system.

By leveraging entropy, network administrators can swiftly identify and mitigate DDoS attacks, ensuring the integrity and
availability of their services.

## Mathematical Explanation

### Shannon Entropy Formula

The Shannon entropy \( H(X) \) of a discrete random variable \( X \) with possible outcomes \( \{ x_1, x_2, \ldots,
x_n \} \) is defined as:

$$ H(X) = -\sum_{i=1}^{n} p(x_i) \log_2 p(x_i) $$

Where:

- \( p(x_i) \) is the probability of occurrence of the outcome \( x_i \).
- The logarithm is taken base 2, reflecting the binary nature of information.

## Entropy Practical Implementation in Real-World

1. **Collect Data**: Start with a dataset of observations. In the context of network traffic, this could be the source
   IP addresses of incoming packets.

2. **Compute Entropy**: Apply the Shannon entropy formula using the probabilities.

3. **Determine Baseline Entropy**: Establish a baseline entropy value during normal traffic conditions. This involves
   monitoring network traffic over a period of time to understand typical behavior.

4. **Monitor and Compare Entropy**: Continuously monitor the network traffic and calculate the entropy for the traffic
   attributes. Compare the current entropy with the baseline entropy to detect significant deviations. Significant drops
   or spikes in entropy can indicate abnormal traffic patterns, potentially caused by a DDoS attack.

5. **Alert and Mitigate**: Set thresholds for acceptable entropy levels. If the entropy deviates beyond the set
   thresholds, trigger an alert and initiate mitigation strategies, such as identify malicious IPs, rate limiting,
   traffic filtering, or activating DDoS protection services.

### Example Implementation in Python

#### Entropy computation:

```
import math
from collections import Counter


def calculate_entropy(data):
    total_count = len(data)
    if total_count == 0:
        return 0

    counter = Counter(data)
    probabilities = [count / total_count for count in counter.values()]

    entropy = -sum(p * math.log2(p) for p in probabilities)
    return entropy


# Example usage with real-world IP addresses
network_data = [
    "203.0.113.5",
    "198.51.100.8",
    "203.0.113.5",
    "192.0.2.16",
    "198.51.100.8",
    "198.51.100.8",
    "203.0.113.5",
    "198.51.100.8",
    "192.0.2.16",
    "203.0.113.5",
]

entropy = calculate_entropy(network_data)
print("Entropy: {entropy}".format(entropy=entropy))
```

#### Identify Malicious IPs

If the entropy deviates beyond the set thresholds, identify the IP addresses contributing most to the anomaly by looking
at their frequency.

```
from collections import Counter


def identify_malicious_ips(data, threshold=0.1):
    total_count = len(data)
    counter = Counter(data)
    malicious_ips = [
        ip for ip, count in counter.items() if count / total_count > threshold
    ]
    return malicious_ips
```

## Summary

Entropy-based detection has proven to be an effective method for identifying DDoS attacks due to its ability to quantify
the randomness or predictability in network traffic.
By continuously monitoring the entropy of incoming IP addresses, significant deviations from the baseline can be quickly
detected, signaling potential DDoS attacks.
This mathematical approach provides a clear, quantifiable metric that can be used in real-time to identify abnormal
traffic patterns, enabling rapid response to mitigate threats. 
