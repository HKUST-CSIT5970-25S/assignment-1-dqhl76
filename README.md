[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/IAASVEAZ)
# CSIT5970 Assignment-1: EC2 Measurement (2 questions, 4 marks)

### Deadline: 11:59PM, Feb, 28, Friday

---

### Name: YUE, Liuqing
### Student Id: 21073397
### Email:lyueaa@connect.ust.hk

---

## Question 1: Measure the EC2 CPU and Memory performance

1. (1 mark) Report the name of measurement tool used in your measurements (you are free to choose *any* open source measurement software as long as it can measure CPU and memory performance). Please describe your configuration of the measurement tool, and explain why you set such a value for each parameter. Explain what the values obtained from measurement results represent (e.g., the value of your measurement result can be the execution time for a scientific computing task, a score given by the measurement tools or something else).

I use `phoronix-test-suite` as the measurement tool.
```
> phoronix-test-suite version
Phoronix Test Suite v10.8.4
```
For the detail configure:
- **CPU performance**: I use `pts/compress-gzip`. This test measures the time needed to archive/compress with Gzip compression. Test run 3 times.
  - Why choose this: Gzip compression can reflect the CPU performance. Gzip is widely used in the real world, can repsent the CPU workload.
  - Result explain: The result is time to complete Gzip compression archiving to .tar.gz in seconds. Lower values indicate better CPU performance (faster processing).
- **Memory performance**: I use `pts/ramspeed`. This test measures the memory read performance. Test run 3 times. `pts/ramspeed` test need configure: I choose `{run-type: Average, benchmark: Integer}`
  - Why choose this: I choose this because https://openbenchmarking.org/test/pts/ramspeed the websites show the most popular run-type is Average(22.7%), and the benchmark is Integer(52.1%). If we run all the test, it will take longer than 1 hour. So I choose the most popular to represent the memory performance.
  - Result explain: The result is Memory(RAM) speed in MB/s. The higher the better.

2. (1 mark) Run your measurement tool on general purpose `t2.micro`, `t2.medium`, and `c5d.large` Linux instances, respectively, and find the performance differences among these instances. Launch all the instances in the **US East (N. Virginia)** region. Does the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource?

    In order to answer this question, you need to complete the following table by filling out blanks with the measurement results corresponding to each instance type.

    | Size         | CPU performance (Seconds)| Memory performance (MB/s)|
    | -----------  | --------------- | ------------------ |
    | `t2.micro`   |129.43                 |10621.66      |
    | `t2.medium`  |52.654                 |19127.54      |
    | `c5d.large`  |46.057                 |14969.13              |

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI.

For CPU performance, `t2.micro` is 1 vCPUs and `t2.medium` is 2 vCPUs. The result shows that the CPU performance of `t2.medium` is better than `t2.micro`. `t2.medium` has a better CPU performance may because of the more vCPUs. However, `c5d.large` is also 2 vCPUs, but the CPU performance is better than `t2.medium`. This may because of the different CPU type. `t2.medium` is general purpose, but `c5d.large` is compute optimized. The CPU performance of `c5d.large` is better than `t2.medium`.

For memory performance, `t2.micro` is 1 GiB, `t2.medium` is 4 GiB, and `c5d.large` is 4 GiB. However, we test not the memory size, but the memory speed. So the memory performance result may not direct related to the memory size.

## Question 2: Measure the EC2 Network performance

1. (1 mark) The metrics of network performance include **TCP bandwidth** and **round-trip time (RTT)**. Within the same region, what network performance is experienced between instances of the same type and different types? In order to answer this question, you need to complete the following table.

    | Type                      | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | `t3.medium` - `t3.medium` |                |          |
    | `m5.large` - `m5.large`   |                |          |
    | `c5n.large` - `c5n.large` |                |          |
    | `t3.medium` - `c5n.large` |                |          |
    | `m5.large` - `c5n.large`  |                |          |
    | `m5.large` - `t3.medium`  |                |          |

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. Note: Use private IP address when using iPerf within the same region. You'll need iPerf for measuring TCP bandwidth and Ping for measuring Round-Trip time.

2. (1 mark) What about the network performance for instances deployed in different regions? In order to answer this question, you need to complete the following table.

    | Connection                | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | N. Virginia - Oregon      |                |          |
    | N. Virginia - N. Virginia |                |          |
    | Oregon - Oregon           |                |          |

    > Region: US East (N. Virginia), US West (Oregon). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. All instances are `c5.large`. Note: Use public IP address when using iPerf within the same region.
