
# CSIT5970 Assignment-1: EC2 Measurement (2 questions, 4 marks)

### Deadline: 11:59PM, Feb, 28, Friday

---

### Name: Xining Cui

### Student Id:21142447

### Email: xcuiai@connect.ust.hk

---

## Question 1: Measure the EC2 CPU and Memory performance

1. (1 mark) Report the name of measurement tool used in your measurements (you are free to choose *any* open source measurement software as long as it can measure CPU and memory performance). Please describe your configuration of the measurement tool, and explain why you set such a value for each parameter. Explain what the values obtained from measurement results represent (e.g., the value of your measurement result can be the execution time for a scientific computing task, a score given by the measurement tools or something else).

    > Phoronix Test Suite

    ```bash
    ssh -i "/Users/maxsinacc/Desktop/ssh/ccincloud.pem" ubuntu@ec2-54-146-203-101.compute-1.amazonaws.com
    wget https://phoronix-test-suite.com/releases/repo/pts.debian/files/phoronix-test-suite_10.8.4_all.deb
    sudo apt update
    sudo apt install -y php-cli php-xml
sudo dpkg -i phoronix-test-suite_10.8.4_all.deb
    phoronix-test-suite --version
    sudo apt install php-zip unzip
    phoronix-test-suite run pts/compress-7zip
    phoronix-test-suite benchmark pts/ramspeed
    ```
    
    **CPU Test - 7-Zip Compression Benchmark (pts/compress-7zip)**
    
    Measures **CPU computation performance** using **7-Zip compression**.
    
    **Test Configuration:**

    1. Default **3 trial runs** were executed to ensure stability.

    2. The test is **multi-threaded**, meaning it utilizes available vCPUs effectively.

    **Memory Test - RAMSpeed Floating Point (pts/ramspeed)**

    Measures **memory bandwidth** for floating-point operations.

    **Test Configuration:**

    1. **Floating Point (Scale) benchmark** was used to assess **memory bandwidth in floating-point calculations**.

    2. **3 trial runs** were conducted to ensure reliable results.

2. (1 mark) Run your measurement tool on general purpose `t2.micro`, `t2.medium`, and `c5d.large` Linux instances, respectively, and find the performance differences among these instances. Launch all the instances in the **US East (N. Virginia)** region. Does the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource?

    In order to answer this question, you need to complete the following table by filling out blanks with the measurement results corresponding to each instance type.

    | Size        | CPU performance | Memory performance |
    | ----------- | --------------- | ------------------ |
    | `t2.micro` | 3467 MIPS | 9,343 MB/s |
    | `t2.medium`  | 7084 MIPS | 12646.09 MB/s |
    | `c5d.large` | 4914MIPS | 13159.17 MB/s |

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI.

- **CPU Performance**: When the number of vCPUs increases, CPU performance generally scales linearly, especially between instances like t2.micro and t2.medium, which don’t have other optimizations. However, for compute-optimized instances like c5d.large, even though it has the same number of vCPUs as t2.medium, its **CPU performance doesn’t scale proportionally** because it is optimized for storage and networking rather than pure computational power.

- **Memory Performance**: Memory performance increases with the amount of memory. t2.medium has **36% better memory performance** than t2.micro, while c5d.large shows only a slight improvement of **4%** over t2.medium, though its overall memory performance is still higher, partly due to its optimized local storage and network bandwidth.

## Question 2: Measure the EC2 Network performance

1. (1 mark) The metrics of network performance include **TCP bandwidth** and **round-trip time (RTT)**. Within the same region, what network performance is experienced between instances of the same type and different types? In order to answer this question, you need to complete the following table.

    ```bash
    sudo apt update && sudo apt install -y iperf
    iperf -s -w 256k
    iperf -c 172.31.45.89 -w 256k
    ping -c 10 172.31.45.89
    ```

    | Type                      | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | `t3.medium` - `t3.medium` | 3820           | 0.260    |
    | `m5.large` - `m5.large`   | 4860           | 0.214    |
    | `c5n.large` - `c5n.large` | 4960           | 0.160    |
    | `t3.medium` - `c5n.large` | 2670           | 0.593    |
    | `m5.large` - `c5n.large`  | 2610           | 0.635    |
    | `m5.large` - `t3.medium`  | 1920           | 0.903    |

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. Note: Use private IP address when using iPerf within the same region. You'll need iPerf for measuring TCP bandwidth and Ping for measuring Round-Trip time.

- Within the same region,  same instances communication is faster than different types.

2. (1 mark) What about the network performance for instances deployed in different regions? In order to answer this question, you need to complete the following table.

| Connection                | TCP b/w (Mbps) | RTT (ms) |
| ------------------------- | -------------- | -------- |
| N. Virginia - Oregon      | 0.294          | 62.722   |
| N. Virginia - N. Virginia | 4960           | 0.136    |
| Oregon - Oregon           | 4960           | 0.159    |

> Region: US East (N. Virginia), US West (Oregon). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. All instances are `c5.large`. Note: Use public IP address when using iPerf within the same region.

- Intra-region communication is significantly faster.

- Inter-region communication is much slower, with a drastic reduction in TCP bandwidth and a significantly higher RTT, likely due to the physical distance and internet routing between regions.
