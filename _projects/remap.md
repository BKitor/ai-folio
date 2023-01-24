---
layout: page
title: Remap
description: OpenMPI collective component for rank reordering allreduce and broadcast
img: assets/img/remap_bin.png
importance: 2
category: Work
github: https://github.com/BKitor/remap
---

{% include figure.html path="assets/img/remap_bin.png" title="Binary Tree Remapping" class="img-fluid rounded z-depth-1" %}
<div class="caption"> 
	Example of remapping a binary tree broadcast.
	The colored squares represent different nodes in a cluster.
	The left side is a 'by-core' initial mapping where processes are placed on consecutive nodes, and most of the communication is between nodes.  
	After remapping, which is a depth-first search of the binary tree, many of the internode communications were brought within the node.
</div>

This is the first project I tackled during my master's, it is heavily adapted from and extends work done by Mirsadeghi and Afsahi [1].
It's a topology-aware collective communication method implemented in MPI.

When a communicator encounters a collective communication, it selects an algorithm to perform the operation.
Typically, there are flat algorithms like reduce-scatter allgather and recursive doubling 2.
Unfortunately, flat algorithms aren't aware of the hardware they're running on, so the process-to-core bindings tend to impact collective performance significantly.
Rank-reordering is a step between algorithm selection and execution where ranks are renumbered to map the algorithm's communication pattern to the cluster's topology.

### Algorithm: Outline of rank-reordering collective algorithm
{% highlight C linenos %}
Allreduce(buf,size,comm)
	alg <= determine_allreduce_alg(buf_size, comm_size(comm))	// select an algorithm based on msg and comm size
	remapped_comm <= get_remapped_comm_from_cache(alg, comm)	// check for existing remapped comm
	if(NULL == remapped_comm)
		remapped_comm = gen_remapped_comm(alg, comm)
		cache_remapped_comm(alg, comm, remapped_comm)	// if not found, build and save
	
	alg(buf, size, remapped_comm)	// run the collective
{% endhighlight %}

The original work by Mirsadeghi and Afsahi [1] proposed remapping heuristics for recursive doubling and ring allgather, as well as binomial tree broadcast and binomial tree reduce, and they evaluated their work within MPICH.
My work reimplements their method in OpenMPI and extends it to allreduce with the reduce-scatter-allgather, ring, and recursive doubling algorithms, as well as binary-tree, k-nomial tree, and scatter-allgather broadcast.

The code is available on [GitHub](https://github.com/BKitor/remap).

### References
[1] S. H. Mirsadeghi and A. Afsahi, "Topology-Aware Rank Reordering for MPI Collectives," 2016 IEEE International Parallel and Distributed Processing Symposium Workshops (IPDPSW), Chicago, IL, USA, 2016, pp. 1759-1768, doi: [10.1109/IPDPSW.2016.139](https://doi.org/10.1109/IPDPSW.2016.139).

[2] Thakur R, Rabenseifner R, Gropp W. Optimization of Collective Communication Operations in MPICH. The International Journal of High Performance Computing Applications. 2005;19(1):49-66. [doi:10.1177/1094342005051521](https://doi.org/10.1177/1094342005051521)