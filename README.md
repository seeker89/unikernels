# State of the art for Unikernels

> Unikernels are specialised, single address space machine images constructed by using library operating systems -- [Wikipedia](https://en.wikipedia.org/wiki/Unikernel)

Unikernels are library operating systems that provide the ability to run applications within a protected, single-address space environment. Such operating systems delegate the management of physical resources to an underlying hypervisor, which is treated as a trusted platform. Due to the absence of a large number of software components that commonly exist in monolithic operating systems, Unikernels can provide a more stable, small and secure environment in comparison. Currently, there are two main approaches:

1. Clean slate, where a new minimal kernel is written using a high-level programming language
2. Strip-down, where an existing kernel implementation is modified accordingly to remove unnecessary components to reduce both complexity and overhead.

Unlike monolithic operating systems such as Linux that permit libraries to be modified, recompiled, shared and dynamically linked, Unikernels typically rely on static linking to combine the application code and associated libraries with the kernel into an immutable, single-purpose virtual appliance and may therefore be seen to be more trustworthy. Other Unikernel approaches implement the ability to use shared libraries at run-time, which could in some circumstances be seen to present a security risk, but provide more portability for running Linux-based applications.

## TL;DR

_Oct 2023_ (is this out of date? Send a [PR](https://github.com/seeker89/unikernels/edit/main/README.md))

Despite a lot of hype around [2015-2016](https://trends.google.com/trends/explore?date=all&q=Unikernel), Unikernels did not gain much popularity, and have not been adopted by practitioners as software containers and related orchestration frameworks such as Docker and Kubernetes respectively.

The main blockers for a wider adoption of unikernels appear to be:

- Compatibility with existing code and difficulty in porting applications as this requires specialised skills to port standard libraries or modify applications to run on a newly developed kernel
- Lack of adequate system debugging capabilities and instrumentation tools which may be useful for developers
- Difficulty in integrating monitoring, logging, and instrumentation tools that gather information about running applications although this may have been achieved in some examples such as NanoOS
- Currently, there is no universal or general-purpose approach as each Unikernel supports a unique programming language run-times and may provide different functional abstractions
- Lack of standardisation similar to Linux or Docker
- Running VMs means applications cannot share some resources (unused RAM) like they can in Linux containers


A few thoughts ([correct me](https://github.com/seeker89/unikernels/edit/main/README.md)):

* __Security__: Unikernels provide smaller attack surfaces compared to Unix-like operating systems. This is one of the strongest arguments for adopting Unikernels. However, the counter argument is that Unikernels have a focused attack surface, which allow attackers to focus on a small set of functionality to attack although this is practically very difficult to achieve. Moreover, Unikernels present a minimal interface that is determined by the needs of the application that is linked with the kernel. Security does not necessarily make the ability to debug the application and kernel difficult but more adequate debugging tools are needed. There has been some progress in improving the debugging capabilities of paravirtualised Unikernels written in C that run Xen hypervisor for example, see the [Duster](https://github.com/StardustOS/duster) project for more details. 
* __Lightweight__: One strong argument of adopting Unikernels is that they have a small memory footprint, and are small in size due to the lack of software components that commonly exist in Linux which increase overhead. Moreover, this permits Unikernels to be deployed onto virtualisation platforms relatively faster than Linux virtual machines.
Memory resources may have to be pre-allocated for the virtual machine and therefore memory may not be shared with other applications running in isolated environments as the case in Linux containers.
* __Performance__: While a simple kernel without the overhead of syscall switch does increase the [performance of some operations](https://unikraft.org/docs/features/performance/), it's not clear how well that applies to applications for which the syscalls aren't bottlecks. Besides, the high performance comes at a cost, so will only be an acceptable tradeoff for a subset of applications.
* __Debugging/Monitoring__: With the advent of eBPF, the visibility into the kernel has increased dramatically, so the standard have risen. Unikernels would need to provide a viable alternative for massive adoption to happen. However, it is useful to note that as Unikernels run as single-address space environments, the notion of kernel and user spaces do not exist and therefore technologies such as eBPF may not be tailored towards Unikernels. 
* __Scheduling__: Software containers now have a few options for scheduling such as [Kubernetes](https://kubernetes.io/), [Nomad](https://www.nomadproject.io/), etc. Scheduling virtual machines, however, with something like OpenStack would require some improvement to provide easier and adequate support for deploying virtual machines quickly in a similar manner to container-related solutions. This could be alleviated with things like [Firecracker](https://firecracker-microvm.github.io/) that can integrate with Kubernetes, but these haven't exactly blown up in popularity either.
* __Compatibility__: Software containers provide the ability to package existing software applications and associated libraries which may be needed for their execution without modification. Unikernels require compiling the kernel together with the application. There are some solutions that can produce a Linux binary for debugging and a kernel image for production. Keeping the Unikernel (mostly) Linux-compatible [is possible](https://unikraft.org/docs/features/posix-compatibility/), but requires some gluecode.


Currently, the only two with commercial support I could find are [Unikraft](https://unikraft.org/) and [NanoOS](https://nanos.org/). If you're looking to learn to make your own, check out the [StardustOS](https://github.com/StardustOS/) project which is used to support the teaching and research activities at the University of St Andrews. This project actively maintains several Unikernel implementations including [stardust](https://github.com/StardustOS/stardust) which is written completely in C and [stardust-oxide](https://github.com/StardustOS/stardust-oxide) which is written in Rust, along with a [minimal kernel for Xen](https://github.com/StardustOS/minimal) that's easy to browse through.

## Unikernels


| Project | language | src                                            | started | last commit | status |
|---------|----------|------------------------------------------------|---------|-------------|--------|
| [Unikraft](https://unikraft.org/) | C        | https://github.com/unikraft/unikraft           | 2017 | recent | active |
| [NanoOS](https://nanos.org/) | C        | https://github.com/nanovms/nanos               | 2017 | recent | active |
| [Mirage OS](https://mirage.io/) | OCaml    | https://github.com/mirage/mirage               | 2014 | recent | active |
| [OSv](http://osv.io/) | C        | https://github.com/cloudius-systems/osv        | 2012 | recent | active |
| [RustyHermit](https://rust-osdev.com/showcase/rusty-hermit/) | Rust     | https://github.com/hermitcore/rusty-hermit     | 2017 | recent | active |
| [HermiTux](https://ssrg-vt.github.io/hermitux/) | C        | https://github.com/ssrg-vt/hermitux            | 2017 | recent | active |
| [Kontain](https://kontain.app/) | C        | https://github.com/kontainapp/km               | 2018 | recent | active |
| [StardustOS](https://github.com/StardustOS/) | C, Rust  | https://stardustos.gitbook.io/stardust/        | 2019 | 2022 | dead |
| [Rumprun](https://github.com/rumpkernel/rumprun) | C        | https://github.com/rumpkernel/rumprun          | 2014 | 2020 | dead |
| [HermitCore](https://hermitcore.org) | C        | https://github.com/hermitcore/libhermit        | 2015 | 2021 | superseded |
| [Click OS](http://cnp.neclab.eu/projects/clickos/) | C++      | https://github.com/sysml/clickos               | 2007 | 2014 | dead |
| [Clive](https://lsub.org/clive/) | Go       | https://github.com/fjballest/clive             | 2015 | 2016 | dead |
| [HaLVM](https://galois.com/project/halvm/) | Haskell  | https://github.com/GaloisInc/HaLVM             | 2010 | 2018 | dead |
| [GUK](https://github.com/Leonidas-from-XIV/guestvm-guk) | C        | https://github.com/Leonidas-from-XIV/guestvm-guk |  | 2011 | dead |
| [runtime.js](http://runtimejs.org/) | C++, JS  | https://github.com/runtimejs/runtime           | 2014 | 2019 | dead |
| [Include OS](http://www.includeos.org/) | C++      | https://github.com/includeos/IncludeOS |   |   | dead |
| [LING](http://erlangonxen.org/) |          |                                                |   |   | dead |
| [Drawbridge](https://www.microsoft.com/en-us/research/project/drawbridge/) |          |                                                |  |  | ??? |


## Toolchains

| Project | language | src | started | last commit | status |
|---------|----------|-----|---------|-------------|--------|
| [Unik](https://www.microsoft.com/en-us/research/project/drawbridge/) | Go | https://github.com/solo-io/unik | 2016 | 2019 | dead? |


## Various related

| Project | description | status |
|---------|-------------|--------|
| [Qubes OS](https://www.qubes-os.org/) | everything is a VM | active |
| [gVisor](https://github.com/google/gvisor) | userspace Kernel | active |
| [Firecracker](https://firecracker-microvm.github.io/) | micro VMs (using KVM) | active |



## Links

- https://github.com/cetic/unikernels
- http://unikernel.org
- [Hacker News discussion of this doc](https://news.ycombinator.com/item?id=30358036)


## Papers

- [“Unikernels: Library Operating Systems for the Cloud” (Madhavapeddy, et al., 2013)](https://anil.recoil.org/papers/2013-asplos-mirage.pdf)
- ["Unikernels: The Next Stage of Linux’s Dominance" (Ali Raza, Parul Sohal, James Cadden, Jonathan Appavoo, Ulrich Drepper, Richard Jones, Orran Krieger, Renato Mancuso, and Larry Woodman. 2019)](https://www.bu.edu/rhcollab/files/2019/04/unikernel.pdf)
- https://wiki.linuxfoundation.org/_media/realtime/events/rt-summit2017/unikernelized-real-time-linux-and-iot_tiejun-chen.pdf
- ["Unikernel support for the deployment of light-weight, self-contained, and latency avoiding services" (Jaradat, W., Dearle, A. and Lewis, J., 2018)](https://research-repository.st-andrews.ac.uk/bitstream/handle/10023/13099/Jaradat_2018_Unikernel_support_3rdSRCW.pdf?sequence=1&isAllowed=y), *Presentation slides can be found [here](https://uksystems.org/workshop/2018/42-jaradat-services.pdf)*.
- ["HermitCore: A Unikernel for Extreme Scale Computing" (S. Lankes, S. Pickartz, J. Breitbart, 2016)](https://dl.acm.org/doi/10.1145/2931088.2931093)
- ["Evaluation of Rust for Operating System Development and Porting Key Components of the HermitCore Unikernel" (Colin Finck)](https://colinfinck.de/Master_Thesis_Colin_Finck.pdf)
- ["A Binary-Compatible Unikernel" (P. Olivier, D. Chiba, S. Lankes, C. Min, B. Ravindran, 2019)](https://www.ssrg.ece.vt.edu/papers/vee2019.pdf)
- ["Debugging Unikernel Operating Systems" (Ahmad, K., Dearle A., Lewis, J. and Jaradat, W.)](https://raw.githubusercontent.com/uksystems/uksystems.github.io/c0a49180fa66acccb687fbde5603494c7a051620/uksystems2020-abstract40.txt), *Presentation slides can be found [here](https://github.com/mallok-s/Duster-Systems-Research-Challenges-Talk/blob/main/presentation.pdf)*.
- ["Unikraft: Fast, Specialized Unikernels the Easy Way" (Kuenzer, et al., 2021)](https://dl.acm.org/doi/10.1145/3447786.3456248)
- ["FlexOS: Towards Flexible OS Isolation" (Lefeuvre, et al., 2021)](https://dl.acm.org/doi/10.1145/3503222.3507759)
- ["Want More Unikernels? Inflate Them!" (G. Gain, C. Soldani, F. Huici and L. Mathy, 2021)](https://dl.acm.org/doi/10.1145/3542929.3563473)

## Books

- [Unikernels by Russell Pavlicek](https://www.oreilly.com/library/view/unikernels/9781492042815/)


## Talks


- [UniLinux -- Unikernelized Linux - Tiejun Chen- FOSSASIA 2018](https://www.youtube.com/watch?v=4XIThgGLUNY)
- [LISA18 - Unikraft: Unikernels Made Easy (2018)](https://www.youtube.com/watch?v=9PRKBZHArhI)
- [Unikernel Technologies - Michael Bright (2017)](https://www.youtube.com/watch?v=24rvIB4_v4U)
- [Unikernels - Keep It Simple to the Bare Metal - Tomek Kalinowski (2017)](https://www.youtube.com/watch?v=1iSdnU3aewA)
- [Unikernels and Event-driven Serverless Platforms -- Madhuri Yechuri (2017)](https://www.youtube.com/watch?v=0uTp8xYvaaY)
- [Unikernels and Docker: From Revolution to Evolution - Black Belt Track - Mindy Preston (2016)](https://www.youtube.com/watch?v=0AZVCGTxkTU)
- [Unik: Unikernel Runtime for Kubernetes by Idit Levine, EMC (2016)](https://www.youtube.com/watch?v=wcZWg3YtvnY)
- [Unikernels: the rise of the library hypervisor in MirageOS (2016)](https://www.youtube.com/watch?v=dn4ARS4lDlQ)
- ["Look ma, no OS! Unikernels and their applications" by Matt Bajor (2015)](https://www.youtube.com/watch?v=W9F4pn9Lngc)
- [Unikernels: Who, What, Where, When, Why - Adam Wick, Galois (2014)](https://www.youtube.com/watch?v=2NuKkGWGg8I)


## Articles

- [7 Unikernel Projects to Take On Docker in 2015](https://www.linux.com/news/7-unikernel-projects-take-docker-2015/)
- [Unikernels are unfit for production](https://www.joyent.com/blog/unikernels-are-unfit-for-production)
  - [Hacker News discussion](https://news.ycombinator.com/item?id=10953766)
- [Just-in-Time Summoning of Unikernels (v0.2) (2015)](http://www.skjegstad.com/blog/2015/08/17/jitsu-v02/)
- [Google Trends: unikernel](https://trends.google.com/trends/explore?date=all&q=Unikernel)
- [A Rust-based Unikernel - First version of a Rust-based libOS](https://hermitcore.org/2018/06/06/A-Rust-based-Unikernel/)


## Linux

- https://github.com/libos-nuse/linux-libos-tools
- https://github.com/lkl/linux
  - https://ieeexplore.ieee.org/document/5541547
- https://www.usenix.org/legacy/publications/library/proceedings/als00/2000papers/papers/full_papers/dike/dike.pdf
