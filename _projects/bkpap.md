---
layout: page
title: BKpap
description: OMPI coll component for pap-aware allreduce
img: assets/img/4n_pap_sketch.png
importance: 1
category: Work
---
<div class="float-right ml-3" style="width:60%">
	{% include figure.html path="assets/img/4n_pap_sketch.png" title="4n PAP sketch" class="img-fluid rounded z-depth-1" %}
</div>

The second project I tackled during my master's was a process-arrival-pattern aware allreduce.
PAP-Awareness mitigates process imbalance by leveraging arrival time information to structure collective algorithms intelligently [1].

There are two components, 1) a UCX-based PAP distribution mechanism, 2) an Allreduce algorithm built on top of the mechanism. 
The distribution mechanism utilizes network atomics to increment a counter denoting arrival positions.
The algorithm is a hierarchical chain reduce + broadcast, and the inter+intranode broadcast phase is optimized to be as fast as possible to minimize execution for the last arriving rank.

<div class="row">
	<div class="col-9">
		{% include figure.html path="assets/img/2n_pap_ss.png" title="2 Node PAP Sync Structure Demo" class="img-fluid rounded z-depth-1"  %}
	</div>
	<div class="caption col-3 align-self-center"> 
		Two ranks synchronizing at the sync-structure.
		The `ucp_atomix_nbx()` calls atomically increment a counter to determine the arrival position.
		Processes place their rank in a shared arrival-indexed array.
	</div>
</div>

We used a chain algorithm for simplicity, but it did limit scalability.
Microbenchmark evaluations were positive, and we did manage to beat NCCL under light PAP imbalance on Calcule Quebec's [Narval GPU cluster](https://docs.alliancecan.ca/wiki/Narval/en).
Further evaluations with Horovod showed improved image throughput.
Writing is in progress and will be submitted to a venue soon.


### References
[1] Faraj, A., Patarasuk, P. & Yuan, X. A Study of Process Arrival Patterns for MPI Collective Operations. Int J Parallel Prog 36, 543–570 (2008). [https://doi.org/10.1007/s10766-008-0070-9](https://doi.org/10.1007/s10766-008-0070-9)

[2] Pedram Alizadeh, Amirhossein Sojoodi, Yiltan Hassan Temucin, and Ahmad Afsahi. 2022. Efficient Process Arrival Pattern Aware Collective Communication for Deep Learning. In Proceedings of the 29th European MPI Users' Group Meeting (EuroMPI/USA'22). Association for Computing Machinery, New York, NY, USA, 68–78. [https://doi.org/10.1145/3555819.3555857](https://doi.org/10.1145/3555819.3555857)
