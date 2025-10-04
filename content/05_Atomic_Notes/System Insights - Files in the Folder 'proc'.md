# /proc/cpuinfo

```bash
cat /proc/cpuinfo
```
![[Screenshot 2025-08-23 at 18.53.13.png]]
Flags extend the instruction set that our process can understand.
The ==flags== line in the ==/proc/cpuinfo== output lists the ==CPU features and capabilities== supported by your processor. These are also known as ==CPU flags== or ==instruction set extensions==.

Each flag represents a specific feature or instruction set that the CPU supports, which can be used by the operating system and applications to optimize performance or enable advanced functionality.

## 🔍 What Are CPU Flags?

CPU flags are ==bit-level indicators== that inform the OS and software about the CPU's capabilities. They are typically exposed via the `cpuid` instruction and are read by the kernel during boot.
These list the ==CPU features and instruction set extensions== supported by your processor. These flags represent hardware capabilities that can be utilized by the operating system, compilers, and applications to optimize performance, enable advanced functionality, or improve security.

- Each flag corresponds to a specific feature or instruction set supported by the CPU.
- Examples include:
    - `sse`: Streaming SIMD Extensions (vectorized math).
    - `avx`: Advanced Vector Extensions (parallelized computations).
    - `hypervisor`: Indicates the CPU is running in a virtualized environment.
    - `nx`: No-eXecute bit (prevents execution of code in data regions).
### How Are CPU Flags Used?
1. **By the Operating System**:
    - The Linux kernel reads these flags during boot to determine which CPU features are available.
    - It uses this information to enable optimizations, apply security mitigations, and configure subsystems like scheduling and memory management.
2. **By Compilers**:
    - Compilers like GCC or Clang use these flags to generate optimized machine code.
    - For example, if the `avx` flag is present, the compiler may use AVX instructions for faster computations
3. **By Applicati.ons**:
    - Libraries and applications check these flags at runtime to adapt their behavior.
    - Example: OpenSSL checks for the `aes` flag to use hardware-accelerated encryption.
4. **For Debugging and Profiling**:
    - Tools like `lscpu`, `perf`, and `cpuid` use these flags to provide insights into CPU architecture and performance.

### Example of CPU Flags in Action

If your CPU supports the `avx` flag:
- A video encoding application like FFmpeg might use AVX instructions for parallelized processing.
- A cryptographic library like OpenSSL might use AVX for faster encryption.

## 🛠️ Why Are These Important?

1. **Performance Optimization**:
    - Compilers use these flags to generate optimized code (e.g., using `AVX`, `AES`, or `SSE`).
    - Example: If AES is present, cryptographic libraries like OpenSSL will use hardware-accelerated AES.

2. **Security Mitigations**:
    - Flags like `pti`, `ssbd`, `ibrs`, `ibpb`, `stibp` indicate that the CPU supports mitigations for vulnerabilities like ==Spectre==, ==Meltdown==, and ==L1TF==.

3. **Virtualization Support**:
    - A `hypervisor` indicates you're running in a VM.
    - `vmx` (not shown here) would mean Intel VT-x support.

4. **Compatibility**:
    - Software may check for certain flags before installing or running.
![[Screenshot 2025-08-23 at 19.16.44.png]]

# /proc/meminfo

Displays the current memory usage of the system, including physical and swap memory

# /proc/version:

Contains version information about the kernel, gcc, and the operating system

# /proc/uptime:

Displays the time the system has been up (in seconds) and the amount of time it has been idle

# /proc/loadavg:

The `/proc/loadavg` file in Linux provides a quick snapshot of the system's load average and some additional information about the running processes.
![[Screenshot 2025-08-23 at 19.36.39.png]]

## Load Averages
The first three numbers represent the load average for the system over three time intervals:
1. 1-minute load average: 0.00
2. 5 minutes load average: 0.00
3. 15 minutes load average: 0.00

>[!Load Average]
>Measure of the amount of computational work the system is performing. It represents the average number of processes that are either:
>1. Running on CPU or
>2. Waiting CPU time

- A load average of `0.00` means the system is completely idle—no processes are waiting for CPU time.
- If the load average is higher than the number of CPU cores, it indicates that the system is under heavy load, and processes may be waiting for CPU resources.
>[!Example]
>If your system has 4 CPU cores:
>A load average of **4.00** means the CPUs are fully utilized.
>A load average of **8.00** means the system is overloaded, with processes waiting for CPU time.

In your case, all three load averages are `0.00`, which means the system is idle, and there is no significant computational workload.

## Running Processes

The next part, `1/119`, provides information about the current state of processes on the system:

- **`1`**: This is the number of processes currently **running** (i.e., actively using the CPU).
- **`119`**: This is the total number of processes in the system, including those that are sleeping, stopped, or in other states.

### Explanation:
- In your case, there is **1 process running** out of a total of **119 processes**. This indicates that most of the processes are either idle or waiting for some event (e.g., I/O operations).

## Last Process ID

The final number, `9263`, is the **Process ID (PID)** of the most recently created process on the system.

### What is a PID?
- Every process in Linux is assigned a unique identifier called a **Process ID (PID)**. 
- When a new process is created, it gets a new PID. The kernel increments the PID value sequentially, starting from 1.
- In your case, the most recent process created on the system has a PID of `9263`

