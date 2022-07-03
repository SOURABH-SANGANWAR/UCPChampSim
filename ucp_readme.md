How to run the code?
The following is the structure to compile the file

$ ./build_champsim.sh ${BRANCH} ${L1I_PREFETCHER} ${L1D_PREFETCHER} ${L2C_PREFETCHER} ${LLC_PREFETCHER} ${LLC_REPLACEMENT} ${NUM_CORE}
As we need to implement UCP write LLC replacement as UCP
Ex:
$ ./build_champsim.sh bimodal no no no no ucp 1

The following is the structure to run the file
./run_champsim.sh [BINARY] [N_WARM] [N_SIM] [TRACE] [OPTION]
Ex.
$ ./run_champsim.sh bimodal-no-no-no-no-ucp-1core 1 10 400.perlbench-41B.champsimtrace.xz




UCP is implemented for multicore and LRU is underlying replacement policy.





The following are the additional functions required for updating LLC accordingly:

=>lru_victim
o Parameters: CPU, set, cycle count
o Return: way
o Initially, it checks whether the allotted partition by the partitioning algorithm needs to be updated or not using the cycle count. If needed it gets updated using the function get_new_part. Then eviction of the block takes place considering the allotted partition and the current partition of ways for cpu’s in the corresponding set.

=>lru_victim_llc
o Parameters: CPU, set
o Return: way
o It finds the way of the victim block. Initially, it looks for an invalid block, however, if not found it returns the block with the maximum LRU value.

=>lru_update
o Parameters: CPU, set, way
o Return: NULL
o It updates the LRU values of the set containing the victim block.



The following are the additional functions required for the implementation of the lookahead based Partitioning Algorithm:

=> getmumax
o Parameters: CPU, number of unassigned ways and an array containing the
o Return: NULL
o The function finds the Marginal Utility. If missa and missb are the number of misses that an application incurs when it receives a and b ways respectively (a < b), then the Marginal Utility of increasing the number of ways from a to b is defined as

                                                        Uab = (missa – missb)/(b-a)

=>get_new_part
o Parameters: CPU, way
o Return: NULL
o The function finds the optimal partitioning for the given number of CPUs and the associativity of the set using Marginal Utility. For each CPU and for each possible partition, it calculates the marginal utility and the partition that gives the maximum Marginal Utility with the minimum number of blocks is the optimal partition.



The following are the additional functions required for the implementation of UMON:

=>atd_replacement
o Parameters: cpu, set, tag
o Return: NULL
o It implements all functions of the additional tag directory, including checking hits, finding victims, and updating victims’ LRU. It also updates the UMON global counter.

=>umon_lru_victim
o Parameters: CPU, set, address
o Return: way
o It finds the way of the victim block in ATD. Initially, it looks for an invalid block, however, if not found it returns the block with the maximum LRU value.

=>umon_lru_updat
o Parameters: CPU, set, way
o Return: NULL
o It updates the LRU values of the set containing the victim block in ATD