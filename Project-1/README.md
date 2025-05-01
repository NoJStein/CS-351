**Project 1 Report:**

Here is my table showing all the results of my performance tests:
| Program Name  | Optimization Level | Real Time (s) | User Time (s) | Sys Time (s) | Mem Usage (KB) | Throughput (hashes/s) | Perf Improvement over hash-00 |
|:-------------:|:------------------:|:-------------:|:-------------:|:------------:|:--------------:|:---------------------:|:-----------------------------:|
| hash-00       | 00                 | 356.93        | 342.65        | 10.19        | 2900           | 2,801.67              | N/A                           |
| hash-01       | 00                 | 19.10         | 16.44         | 1.52         | 2884           | 52,356.02             | 18.69                         |
| hash-02       | 00                 | 15.60         | 14.16         | 1.32         | 2900           | 64,102.56             | 22.88                         |
| hash-03       | 00                 | 16.41         | 14.93         | 1.27         | 2892           | 60,938.45             | 21.75                         |
| hash-04       | 00                 | 14.48         | 13.85         | 0.50         | 5011796        | 69,060.77             | 24.65                         |
| hash-00       | 01                 | 342.10        | 332.68        | 4.28         | 2880           | 2,923,12              | N/A                           |
| hash-01       | 01                 | 9.63          | 6.74          | 1.43         | 2888           | 103,842.16            | 35.52                         |
| hash-02       | 01                 | 7.84          | 6.57          | 1.15         | 3456           | 127,551.02            | 43.64                         |
| hash-03       | 01                 | 8.01          | 6.64          | 1.11         | 3472           | 124,843.95            | 42.71                         |
| hash-04       | 01                 | 7.26          | 6.65          | 0.50         | 5012368        | 137,741.05            | 47.12                         |
| hash-00       | 02                 | 354.24        | 331.14        | 5.55         | 2880           | 2822.94               | N/A                           |
| hash-01       | 02                 | 9.13          | 6.99          | 1.24         | 2900           | 109,529.03            | 38.80                         |
| hash-02       | 02                 | 8.37          | 6.92          | 1.27         | 2896           | 119,474.31            | 42.32                         |
| hash-03       | 02                 | 7.96          | 6.59          | 1.30         | 2888           | 125,628.14            | 44.50                         |
| hash-04       | 02                 | 7.30          | 6.67          | 0.50         | 5012356        | 136,986.30            | 48.53                         |
| hash-00       | 03                 | 354.15        | 330.95        | 4.18         | 2888           | 2823.74               | N/A                           |
| hash-01       | 03                 | 7.93          | 6.68          | 1.13         | 2880           | 126,103.40            | 43.52                         |
| hash-02       | 03                 | 8.12          | 6.90          | 1.09         | 3472           | 123,152.71            | 42.51                         |
| hash-03       | 03                 | 8.04          | 6.85          | 1.06         | 2880           | 124,378.11            | 42.82                         |
| hash-04       | 03                 | 7.27          | 6.65          | 0.49         | 5012368        | 137,551.58            | 47.48                         |
| hash-00       | 02 -funroll-loops  | 336.42        | 330.33        | 4.29         | 2880           | 2972.47               | N/A                           |
| hash-01       | 02 -funroll-loops  | 8.18          | 6.86          | 1.24         | 3464           | 122,249.39            | 41.13                         |
| hash-02       | 02 -funroll-loops  | 7.87          | 6.79          | 1.01         | 2880           | 127,064.80            | 42.74                         |
| hash-03       | 02 -funroll-loops  | 7.92          | 6.82          | 1.03         | 3456           | 126,262.62            | 42.48                         |
| hash-04       | 02 -funroll-loops  | 7.13          | 6.59          | 0.47         | 5011776        | 140,252.45            | 47.18                         |
| hash-00       | 0fast -march=native| 342.80        | 330.43        | 10.18        | 2880           | 2917.15               | N/A                           |
| hash-01       | 0fast -march=native| 11.66         | 7.21          | 1.75         | 2888           | 85,763.29             | 29.40                         |
| hash-02       | 0fast -march=native| 8.18          | 6.87          | 1.21         | 3460           | 122,249.39            | 41.91                         |
| hash-03       | 0fast -march=native| 8.20          | 6.76          | 1.36         | 2892           | 121,951.22            | 41.80                         |
| hash-04       | 0fast -march=native| 7.09          | 6.48          | 0.51         | 50117766       | 141,043.72            | 48.35                         |

**Questions:**

  **1. What operation do you think accounts for most of hash-00's runtime?**
 
  The stream extraction operations account for how long hash-00 takes to complete. It is an expensive operation which verifies that the stream only contains ascii 
  numeric values. It does this by skipping all leading whitespace, parsing digit by digit checking for signs (+ or -), and updating error flags as non-numeric ascii 
  characters are detected. It is an operation which is generalized to work with a variety of different data types, so it needs to determine the correct parsing behavior 
  based on the data type it is extracting into. We know that we are dealing with integers in our input file, so this over-generalized approach is causing too much 
  performance overhead. Most significantly, this occurs numHashes * numBytes times! The ifstream::read operation is much faster because it simply copies raw bytes into 
  the buffer without any parsing, skipping of whitespace, or data type determination (locale and facet selection).
	
  **2. hash-01 and hash-02 both dynamically allocate memory for each hash computation. Is there much difference time-wise between their two allocation methods?**
     
 Hash-02 is significantly faster. Here are the performance improvement percentages versus hash-01 at different optimization levels:

  | Optimization Level | hash-02 vs. hash-01 |
  |:------------------:|:-------------------:|
  | 00                 | 22.0%               |
  | 01                 | 22.8%               |
  | 02                 | 9.1%                |
  | 03                 | -2.5%               |

  hash-02 is faster because it uses stack memory allocation versus heap memory allocation. The data for each hash computation is stored in a contiguous region 
  of memory, which is much faster. Additionally, with each use of the new operator, the delete operator needs to be called to locate and delete all the fragmented 
  data previously stored in the heap.

  It seems that a point of diminishing returns is reached at higher optimization levels, even negating all performance advantages of hash-02 over hash-01. 

  **3. hash-03 avoids the allocation by using a fixed-size array.  Is there an appreciable speed difference?**
   
  Hash-03 isn't significantly faster than hash-02, and its performance lead quickly vanishes at higher optimization levels.
  
  | Optimization Level | hash-03 vs. hash-02 |
  |:------------------:|:-------------------:|
  | 00                 | 5.2%                |
  | 01                 | 2.1%                |
  | 02                 | -4.9%               |
  | 03                 | -1.0%               |

  I believe this suggests that using stack-based memory allocation is very similar to using a fixed size array when it comes to performance. In both cases the data is
  stored contiguously in memory, so the performance differences almost seem to cancel each other out. They are both improvements over hash-01 and especially hash-00.

  **4. Why is hash-04's memory usage so much larger than any of the other versions?  Hint: recall when we discussed how the operating system reads a file and makes it 
  available to an application.  Specifically, the O/S will transfer data from disk to its own memory, and then copy from there into buffers provided by the application.  
  In the memory map case, the O/S is merely sharing the copy of the file's data that is in its (the O/S's) memory, and not making an additional copy from its memory into
  memory only in the application**

   In my opinion hash-04 is the most interesting program here, and I will do my best to explain it. Hash-04 uses much more memory because it opts out of parsing through
   a huge text file; returning small chunks of data from disk to memory and into buffers for each hash operation. Instead, it maps through all the data in Data.bin page
   by page (4 KB in size) into about 4.78 GB of RAM. Since we are using all our data in Data.bin, each 4 KB page is stored in the kernel's page cache. This data can then
   be fetched in a linear manner using pointer arithmetic, very similarly to using a fixed-length array or alloca for stack-based dynamic memory allocation. The main
   difference is that the program shares the O/S's page cache. 
	
  **5. What other compiler options did you try, and did they help at all?**

  In addition to compiling with optimization levels 0 and 2, I also included levels 1 and 3 in my report. The biggest jump in performance came from level 0 to level 1.
  With the exception of hash-00, there was a near doubling of performance on hash-01 through hash-04 simply by enabling the lowest level of optimization. Optimization
  levels 2 and 3 saw quick diminishing returns with hash-02 through hash-04. All three of those programs were seemingly unaffected by any of the optimizations after
  optimization level 1. I also tried O2 -funroll-loops and 0fast -march=native, however the performance uplifts weren't signicant, and sometimes were much slower.

  **Here is a graph of all hash programs at all optimization levels:**
![hash-0X all](https://github.com/user-attachments/assets/ccc7f090-8d21-4519-a173-75cab16a75c3)

  Here it is obvious that hash-00 took considerably longer than the other programs. What I didn't expect were the benefits of using the 02 -funroll-loops and 
  0fast -march=native compiler options. Considering the inefficiency of the stream extraction operations, these two levels of optimization helped hash-00 significantly.

  **Here is another graph without hash-00 for the sake of visibility:**
![hash-01 - hash-04 all](https://github.com/user-attachments/assets/bb3fe5b6-5d1a-4e57-ad5e-7f7eea0251b7)

  The graph above helped me come to the conclusion that out of all the compiler options that I tested with, using an optimization level of 3 was the best choice for 
  hash-01 through hash-04. 01 - 03 all converged at around 8 seconds while 04 sat at just above 7 seconds. What surprised me was that 0fast -march=native made hash-01
  almost 50% slower! My understanding of 0fast -march=native is that it is like 03 except it enables every instruction your CPU supports, picking the fastest instructions. 
  Perhaps this outlier in my data was caused by other users running intensive programs on Blue at the same time? 

  One thing that really stood out to me with these tests was the importance of writing good code to begin with. Despite all the attempts to patch up sub-optimal code in these
  hashing programs, hash-04 was the only program to consistently see improvements with each new level of optimization I threw at it. I wouldn't be surprised if more aggressive
  compiler options made that program run even faster. I can't say the same about hash-00 through hash-03 as some of these compiler options gave unexpected results.

  

  
