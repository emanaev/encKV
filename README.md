# EncKV: An Encrypted Key-value Store with Rich Queries

 * Introduction
 * Requirements
 * Installation
 * Configuration
 * Compiling
 * Usage
 * Redo Experiment
 * Maintainers

# INTRODUCTION

EncKV is an encrypted key-value store with secure rich query support. It stores encrypted data records with multiple secondary attributes in the form of encrypted key-value pairs. EncKV leverages the latest practical primitives for search over encrypted data, i.e., searchable symmetric encryption and order-revealing encryption, and provides encrypted indexes with guaranteed security respectively to enable exactmatch and range-match queries via secondary attributes of data records. EncKV carefully integrates the above indexes into a distributed index framework to facilitate secure query processing in parallel. To mitigate recent inference attacks on encrypted database systems, EncKV protects the order information during range queries, and presents
an interactive batch query mechanism to further hide the associations across data values on different attributes.

We implement the EncKV prototype on a Redis cluster, and conduct performance evaluation on Amazon Cloud. The results show that EncKV preserves the efficiency and scalability of plaintext distributed key-value stores.

# PUBLICATION

Xingliang Yuan, Yu Guo, Xinyu Wang, Cong Wang, Baochun Li, and Xiaohua Jia, "EncKV: An Encrypted Key-value Store with Rich Queries", In the 12th ACM Asia Conference on Computer and Communications Security (AISACCS'17). [Link](https://www.google.ru/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=0ahUKEwjfiqy7p9nWAhUMIJoKHds3AVQQFggmMAA&url=http%3A%2F%2Fiqua.ece.toronto.edu%2Fpapers%2Fxyuan-asiaccs17.pdf&usg=AOvVaw2pBoA4qoNt41giSL6mAzYf)

# REQUIREMENTS

Recommended Environment: Docker 1.2 or higher

# INSTALLATION

Build Docker image for EncKV:

```shell
git clone https://github.com/emanaev/encKV.git
cd encKV
./build.sh
```

# USAGE

 Start 10 Docker containers with EncKV nodes
 
```shell
./start.sh
```

Run some tests

```shell
./test.sh
```

Stop EncKV nodes
```shell
./stop.sh
```

# ADVANCED USAGE

Log into shell

```shell
docker run -it --rm --network enckv enckv bash
cd encKV/Client
```

Use scripts

  * Test_insert.sh

	Scripts that insert test data into EncKV.

	```
	Usage :	encKV/Client/Test_insert [DataNodeNum] [BegNum] [EndNum] [EqualIndexVersion] [OrderIndex] [BlockSizeInBit] [ModNum]

	- [DataNodeNum]: the number of nodes.
	- [BegNum]: the begin of random insertion number.
	- [EndNum]: the End of random insertion number.
	- [EqualIndexVersion]: 0 means NO index, 1 means strategy I equal index, 2 means strategy II equal index, 3 means plaintext equal index.
	- [OrderIndex]: 0 means NO index, 1 means encrypted order index, 2 means plaintext order index.
	- [BlockSizeInBit]: the blocksize of ORE in bit.
	- [ModNum]: This param is to insert multiple records with a same value. 0 means no mod operation, for others, the insertion value will mod this param.
	```


  * Test_Throughput.sh
	
	Scripts that evaluate throughput of EncKV.

	```
	Usage : ASIACCS-17/Client/Test_Throughput.sh [NodeNum] [LOOP] [OPTION] [TIME] [MaxBoundary] [SEEDS] [BLOCK_SIZE_INBITS] 

	- [NodeNum]: the number of nodes.
	- [LOOP]: the number of clients.
	- [OPTION]: 0 means use strategy I index, 1 means use strategy II index, 2 means use encrypted order index.
	- [TIME]: time duration for the throughput test.
	- [MaxBoundary]: the boundary of generated data.	
	- [SEEDS]:  random seed for generating test data.
	- [BlockSizeInBit]: the blocksize of ORE in bit.
	```

  * Test_latency

	Test the latency of each operations

	```
	Usage : ASIACCS-17/Client/Test_latency [DataNodeNum] [BegNum] [EndNum] [Times] [QueryIndexType] [BlockSizeInBit]

	- [DataNodeNum]: the number of nodes.
	- [BegNum]: the begin of random insertion number.
	- [EndNum]: the End of random insertion number.
	- [ValLen]: the length of value in bytes.
	- [Times]: times of operations.
	- [QueryIndexType]: 0 means use strategy I, 1 means use strategy II, 2 means use encrypted order index, 3 means use plaintext equal index, 4 means use plaintext order index.
	- [BlockSizeInBit]: the blocksize of ORE in bit.
	```

# REDO EXPERIMENT
	
 * You can follow these steps to redo our experiment.
	
 * Figure A
 
   ```
   ./Test_insert  9 0   1000 2 0 8 0
   ./Test_insert  9 0   2000 2 0 8 0
   ./Test_insert  9 0   4000 2 0 8 0
   ./Test_insert  9 0   8000 2 0 8 0
   ./Test_insert  9 0  16000 2 0 8 0
   ```
 
 * Figure B
 
   ```
   ./Test_insert  9 0   1000 0 1 8 0
   ./Test_insert  9 0   2000 0 1 8 0
   ./Test_insert  9 0   4000 0 1 8 0
   ./Test_insert  9 0   8000 0 1 8 0
   ./Test_insert  9 0  16000 0 1 8 0
   ```
 * Figure C
 
   ```
   ./Test_insert  3 0   10000 2 0 8 0
   ./Test_Throughput.sh 3 40 1 time 1 seed 8
   （redis-cli flushall)

   ./Test_insert  6 0   10000 2 0 8 0
   ./Test_Throughput.sh 6 40 1 time 1 seed 8
   （redis-cli flushall)

   ./Test_insert  9 0   10000 2 0 8 0
   ./Test_Throughput.sh 9 40 1 time 1 seed 8
   ```
 
 * Figure D
 
   ```
   ./Test_insert  3 0   10000 0 1 8 0
   ./Test_Throughput.sh 3 40 2 time 1 seed 8
   （redis-cli flushall)

   ./Test_insert  6 0   10000 0 1 8 0
   ./Test_Throughput.sh 6 40 2 time 1 seed 8
   （redis-cli flushall)

   ./Test_insert  9 0   10000 0 1 8 0
   ./Test_Throughput.sh 9 40 2 time 1 seed 8
   ```
 
 * Figure E
 
   ```
   ./Test_insert  9 -2000    0 2 0 8 0
   ./Test_insert  9 -4000    0 2 0 8 0
   ./Test_insert  9 -8000    0 2 0 8 0
   ./Test_insert  9 -16000   0 2 0 8 0
   ./Test_insert  9 -32000   0 2 0 8 0
   (when the BegNum is below zero, this command will insert the abs(BegNum) in abs(BegNum) times.)
   ```
 * Figure F
 
   ```
   (YWWQL16)
   ./Test_insert  9 -2000    0 1 0 8 0
   ./Test_insert  9 -4000    0 1 0 8 0
   ./Test_insert  9 -8000    0 1 0 8 0
   ./Test_insert  9 -16000   0 1 0 8 0
   ./Test_insert  9 -32000   0 1 0 8 0
   
   ./Test_latency 9 2000     2000    10 0 8
   ./Test_latency 9 4000     4000    10 0 8
   ./Test_latency 9 8000     8000    10 0 8
   ./Test_latency 9 16000    16000   10 0 8
   ./Test_latency 9 32000    32000   10 0 8
   
   (Ext)
   ./Test_insert  9 -2000    0 2 0 8 0
   ./Test_insert  9 -4000    0 2 0 8 0
   ./Test_insert  9 -8000    0 2 0 8 0
   ./Test_insert  9 -16000   0 2 0 8 0
   ./Test_insert  9 -32000   0 2 0 8 0
   
   ./Test_latency 9 2000     2000    10 1 8
   ./Test_latency 9 4000     4000    10 1 8
   ./Test_latency 9 8000     8000    10 1 8
   ./Test_latency 9 16000    16000   10 1 8
   ./Test_latency 9 32000    32000   10 1 8
   ```

 * Figure G
 
   ```
   ./Test_insert  9 -2000    0 0 1 8 0
   ./Test_latency 9 2000     2000    10 2 8
   （redis-cli flushall)

   ./Test_insert  9 -4000    0 0 1 8 0
   ./Test_latency 9 4000     4000    10 2 8
   （redis-cli flushall)

   ./Test_insert  9 -8000    0 0 1 8 0
   ./Test_latency 9 8000     8000    10 2 8
   （redis-cli flushall)

   ./Test_insert  9 -16000   0 0 1 8 0
   ./Test_latency 9 16000    16000   10 2 8
   （redis-cli flushall)

   ./Test_insert  9 -32000   0 0 1 8 0
   ./Test_latency 9 32000    32000   10 2 8
   ```

 * Figure H
 
   ```
   (No Index)
   ./Test_insert  9 0   1000 0 0 8 0
   ./Test_insert  9 0   2000 0 0 8 0
   ./Test_insert  9 0   4000 0 0 8 0
   ./Test_insert  9 0   8000 0 0 8 0
   ./Test_insert  9 0  16000 0 0 8 0

   (Ext Index)
   ./Test_insert  9 0   1000 2 0 8 0
   ./Test_insert  9 0   2000 2 0 8 0
   ./Test_insert  9 0   4000 2 0 8 0
   ./Test_insert  9 0   8000 2 0 8 0
   ./Test_insert  9 0  16000 2 0 8 0

   (Rng Index)
   ./Test_insert  9 0   1000 0 1 8 0
   ./Test_insert  9 0   2000 0 1 8 0
   ./Test_insert  9 0   4000 0 1 8 0
   ./Test_insert  9 0   8000 0 1 8 0
   ./Test_insert  9 0  16000 0 1 8 0

   ```
 
