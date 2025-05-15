**Questions:**

1. Are the results what you expected? Speculate as to why it looks like CUDA isn't a great solution for this problem.

I think the CUDA version of this program takes longer to run because the GPU is limited by the bandwidth of PCIE whereas 
the CPU is limited by the bandwidth of system memory and its cache. The bandwidth of PCIE tops out at around 16 GB/s 
while the available bandwidth to the CPU is much higher: 50-100 GB/s if I am understanding this correctly. This limited 
PCIE bandwidth is probably challenged by the scheduling of threads for millions of blocks and the overhead of invoking 
the kernel itself. The Streaming Multiprocessor of a GPU that runs CUDA kernels is also running at about 1.4 GHz while 
server-grade CPUs run anywhere from 3.0 - 4.0 GHz. I would imagine that also plays a role in this scenario. This strikes 
me as an "embarrassingly parallel" problem, but despite that, the overhead of the CUDA kernel and the limitations of 
PCIE make iota.cpu faster.

**Generated Images:**

**Julia CPU**

![julia-CPU](Project-4/julia-CPU.png)

**Julia GPU**

![julia-GPU](Project-4/julia-GPU.png)
