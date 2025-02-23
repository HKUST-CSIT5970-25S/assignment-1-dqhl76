[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/IAASVEAZ)
# CSIT5970 Assignment-1: EC2 Measurement (2 questions, 4 marks)

### Deadline: 11:59PM, Feb, 28, Friday

---

### Name: YUE, Liuqing
### Student Id: 21073397
### Email: lyueaa@connect.ust.hk

---

## Question 1: Measure the EC2 CPU and Memory performance

1. (1 mark) Report the name of measurement tool used in your measurements (you are free to choose *any* open source measurement software as long as it can measure CPU and memory performance). Please describe your configuration of the measurement tool, and explain why you set such a value for each parameter. Explain what the values obtained from measurement results represent (e.g., the value of your measurement result can be the execution time for a scientific computing task, a score given by the measurement tools or something else).

I use `phoronix-test-suite` as the measurement tool:

```
> phoronix-test-suite version
Phoronix Test Suite v10.8.4
```

For the detail configure:
- **CPU performance**: I use `pts/compress-gzip`. This test measures the time needed to archive/compress with Gzip compression. Test run 3 times.
  - Why choose this: Gzip compression can reflect the CPU performance. Gzip is widely used in the real world, can repsent the CPU workload.
  - Result explain: The result is time to complete Gzip compression archiving to .tar.gz in seconds. Lower values indicate better CPU performance (faster processing).
- **Memory performance**: I use `pts/ramspeed`. This test measures the memory read performance. Test run 3 times. `pts/ramspeed` test need configure: I choose `{run-type: Average, benchmark: Integer}`
  - Why choose this: I choose this because [the openbenchmark website](https://openbenchmarking.org/test/pts/ramspeed) show the most popular run-type is Average(22.7%), and the benchmark is Integer(52.1%). If we run all the test, it will take longer than 1 hour. So I choose the most popular to represent the memory performance.
  - Result explain: The result is Memory(RAM) speed in MB/s. The higher the better.


2. (1 mark) Run your measurement tool on general purpose `t2.micro`, `t2.medium`, and `c5d.large` Linux instances, respectively, and find the performance differences among these instances. Launch all the instances in the **US East (N. Virginia)** region. Does the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource?

    In order to answer this question, you need to complete the following table by filling out blanks with the measurement results corresponding to each instance type.

    | Size         | CPU performance (Seconds)| Memory performance (MB/s)|
    | -----------  | --------------- | ------------------ |
    | `t2.micro`   |129.43                 |10621.66      |
    | `t2.medium`  |52.654                 |19127.54      |
    | `c5d.large`  |46.057                 |14969.13      |

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI.

- **For CPU performance**: the `t2.micro` instance offers 1 vCPU, while the `t2.medium` provides 2 vCPUs. The results indicate that the `t2.medium` has better CPU performance than the `t2.micro`, likely due to the higher number of vCPUs. However, despite also having 2 vCPUs, the `c5d.large` instance outperforms the `t2.medium` in CPU performance. This discrepancy is likely due to differing CPU architectures, as `t2.medium` is a general-purpose instance, whereas `c5d.large` is compute-optimized.

- **For memory performance**: the `t2.micro` instance comes with 1 GiB of memory, whereas both the `t2.medium` and `c5d.large` have 4 GiB of memory each. It's important to note that the benchmark tests focus on memory speed rather than size, meaning the results may not be directly correlated with the amount of memory available in each instance.

## Question 2: Measure the EC2 Network performance

I also want to specify the measurement configuration for the network performance test. `<server-ip>` for sub-question 1 is private IP address, and for sub-question 2 is public IP address.

- **TCP Bandwidth**: I use `iperf` to measure the TCP bandwidth between instances. The test will run three times and I will take the average value. The command I used as follows:
```
# server side:
> iperf -s -w 256K
```

```
# client side:
> iperf -c <server-ip> -w 256K -f m
```
- **Round-Trip Time (RTT)**: I use `ping` to measure the round-trip time between instances. The test will run 10 times and I will take the average value. The command I used as follows:
```
> ping -c 10 <server-ip>
```

1. (1 mark) The metrics of network performance include **TCP bandwidth** and **round-trip time (RTT)**. Within the same region, what network performance is experienced between instances of the same type and different types? In order to answer this question, you need to complete the following table.

    | Type                      | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | `t3.medium` - `t3.medium` |3891            |0.250     |
    | `m5.large` - `m5.large`   |4962            |0.194     |
    | `c5n.large` - `c5n.large` |5911            |0.158     |
    | `t3.medium` - `c5n.large` |2516            |0.795     |
    | `m5.large` - `c5n.large`  |2724            |0.663     |
    | `m5.large` - `t3.medium`  |4174            |0.258     |

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. Note: Use private IP address when using iPerf within the same region. You'll need iPerf for measuring TCP bandwidth and Ping for measuring Round-Trip time.


- **Same-Type Instances**: Achieve higher TCP bandwidth and lower RTT due to optimized network paths and consistent hardware capabilities (e.g., c5n.large leverages enhanced networking for ~5.9 Gbps).
- **Cross-Type Instances**: Performance is limited by the instance with lower network bandwidth. I also found cross-type instances have lower TCP bandwidth and higher RTT compared to same-type instances. This may be due to the difference types of instances having more complex network paths and varying hardware capabilities.

2. (1 mark) What about the network performance for instances deployed in different regions? In order to answer this question, you need to complete the following table.

    | Connection                | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | N. Virginia - Oregon      |32              |62.355    |
    | N. Virginia - N. Virginia |4237            |0.236     |
    | Oregon - Oregon           |4677            |0.198     |

    > Region: US East (N. Virginia), US West (Oregon). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. All instances are `c5.large`. Note: Use public IP address when using iPerf within the same region.

- **Cross-Region**: Low TCP bandwidth (~32 Mbps) and high RTT (~62 ms) due to traffic routing over the public internet.
- **Intra-Region**: High TCP bandwidth (~4.2–4.6 Gbps) and ultra-low RTT (~0.2 ms) via AWS’s internal backbone, even with public IPs.
