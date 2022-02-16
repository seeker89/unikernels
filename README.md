# unikernels

> Unikernels are specialised, single address space machine images constructed by using library operating systems -- [Wikipedia](https://en.wikipedia.org/wiki/Unikernel)

Unikernels promise lightweight, secure, minimalistic image to boot and run. They assume being run in a secure hypervisor, so can do away with a lot of complexity of a modern OS. There are two main approaches:

1. clean slate, where a new, minimal unikernel is written in a modern language
2. strip-down, where an existing kernel is simplified to reduce its overhead and remove unnecessary features


## TL;DR

_Feb 2022_ (is this out of date? Send a [PR](https://github.com/seeker89/unikernels/edit/main/README.md))

Despite a lot of hype around [2015-2016](https://trends.google.com/trends/explore?date=all&q=Unikernel), the unikernels didn't blow up in popularity the way that containers or Kubernetes did.

The main blockers for a wider adoption of unikernels appear to be:

- compatibility with existing code
- lacking debuggability (monitoring, logging, familar tools, ...)
- lacking universality - each unikernel only supports a subset of languages
- lacking accepted standard (like Linux or Docker)
- running as VMs means applications can't share some resources (unused RAM) like they can in Linux containers


A few thoughts ([correct me](https://github.com/seeker89/unikernels/edit/main/README.md)):

* __Security__ one of the strongest arguments for a unikernel is the reduced attack surface. While that's difficult to argue with, that same approach makes for more difficult debugging. Similar to debugging a FROM SCRATCH container from inside the container. While this can be alleviated by baking in debugging and monitoring tools, this goes against the whole idea.
* __Lightweight__ another strong argument for unikernels: low overhead and small memory footprint. The flip side, is that you will need to allocate a certain amount of resources for your VM, that can't be shared with other applications like they can with Linux containers. So you might not be able to flatpack as much as you can with containers.
* __Performance__ while a simple kernel without the overhead of syscall switch does increase the [performance of some operations](https://unikraft.org/docs/features/performance/), it's not clear how well that applies to applications for which the syscalls aren't bottlecks. Besides, the high performance comes at a cost, so will only be an acceptable tradeoff for a subset of applications.
* __Debugging/Monitoring__ with the advent of eBPF, the visibility into the kernel has increased dramatically, so the standard have risen. Unikernels would need to provide a viable alternative for massive adoption to happen.
* __Scheduling__ containers now have a few options for scheduling ([Kubernetes](https://kubernetes.io/), [Nomad](https://www.nomadproject.io/), etc). Scheduling VMs with something like OpenStack would require a lot of improvement to get to the level of ease/speed that the container-specific solutions provide. This could be alleviated with things like [Firecracker](https://firecracker-microvm.github.io/) that can integrate with Kubernetes, but these haven't exactly blown up in popularity either.
* __Compatibility__ containers package existing software and run it unchaged. Unikernels require compiling the kernel together with the application. There are some solutions that can produce a Linux binary for debugging and a kernel image for production. Keeping your unikernel (mostly) Linux-compatible [is possible](https://unikraft.org/docs/features/posix-compatibility/), but requires some glue.


Currently, the only two with commercial support I could find are [Unikraft](https://unikraft.org/) and [NanoOS](https://nanos.org/). If you're looking to learn to make your own, check out [StardustOS](https://github.com/StardustOS/) used to teach at St Andrews university, which includes a [minimal kernel for Xen](https://github.com/StardustOS/minimal) that's easy to browse through.


## Unikernels


| Project | language | src | started | last commit | status |
|---------|----------|-----|---------|-------------|--------|
| [Unikraft](https://unikraft.org/) | C | https://github.com/unikraft/unikraft | 2017 | recent | active |
| [NanoOS](https://nanos.org/) | C | https://github.com/nanovms/nanos | 2017 | recent | active |
| [Mirage OS](https://mirage.io/) | OCaml | https://github.com/mirage/mirage | 2014 | recent | active |
| [OSv](http://osv.io/) | C | https://github.com/cloudius-systems/osv | 2012 | recent | active |
| [RustyHermit](https://rust-osdev.com/showcase/rusty-hermit/) | Rust | https://github.com/hermitcore/rusty-hermit | 2017 | recent | active |
| [HermiTux](https://ssrg-vt.github.io/hermitux/) | C | https://github.com/ssrg-vt/hermitux | 2017 | recent | active |
| [StardustOS](https://github.com/StardustOS/) | C | https://stardustos.gitbook.io/stardust/ | 2019 | recent | active |
| [Rumprun](https://github.com/rumpkernel/rumprun) | C | https://github.com/rumpkernel/rumprun | 2014 | 2020 | active? |
| [Click OS](http://cnp.neclab.eu/projects/clickos/) | C++ | https://github.com/sysml/clickos | 2007 | 2014 | dead |
| [Clive](https://lsub.org/clive/) | Go | https://github.com/fjballest/clive | 2015 | 2016 | dead |
| [HermitCore](https://hermitcore.org) | C | https://github.com/hermitcore/libhermit | 2015 | 2021 | superseded |
| [HaLVM](https://galois.com/project/halvm/) | Haskell | https://github.com/GaloisInc/HaLVM | 2010 | 2018 | dead |
| [GUK](https://github.com/Leonidas-from-XIV/guestvm-guk) | C | https://github.com/Leonidas-from-XIV/guestvm-guk |  | 2011 | dead |
| [runtime.js](http://runtimejs.org/) | C++, JS | https://github.com/runtimejs/runtime | 2014 | 2019 | dead |
| [Include OS](http://www.includeos.org/) |  |  |   |   | dead |
| [LING](http://erlangonxen.org/) |  |  |   |   | dead |
| [Drawbridge](https://www.microsoft.com/en-us/research/project/drawbridge/) |  |  |  |  | ??? |


## Toolchains

| Project | language | src | started | last commit | status |
|---------|----------|-----|---------|-------------|--------|
| [Unik](https://www.microsoft.com/en-us/research/project/drawbridge/) | Go | https://github.com/solo-io/unik | 2016 | 2019 | dead? |





## Links

- https://github.com/cetic/unikernels
- http://unikernel.org
- [Hacker News discussion of this doc](https://news.ycombinator.com/item?id=30358036)


## Papers

- [“Unikernels: Library Operating Systems for the Cloud” (Madhavapeddy, et al., 2013)](https://anil.recoil.org/papers/2013-asplos-mirage.pdf)
- ["Unikernels: The Next Stage of Linux’s Dominance" (Ali Raza, Parul Sohal, James Cadden, Jonathan Appavoo, Ulrich Drepper, Richard Jones, Orran Krieger, Renato Mancuso, and Larry Woodman. 2019)](https://www.bu.edu/rhcollab/files/2019/04/unikernel.pdf)
- ["Unikraft: fast, specialized unikernels the easy way"](https://dl.acm.org/doi/10.1145/3447786.3456248)
- https://wiki.linuxfoundation.org/_media/realtime/events/rt-summit2017/unikernelized-real-time-linux-and-iot_tiejun-chen.pdf
- [NanoVMs in depth](https://storage.googleapis.com/dp-assets/nanovms_in_depth.pdf)
- [Jaradat, W., Dearle, A. and Lewis, J. Unikernel support for the deployment of light-weight, self-contained, and latency avoiding services.](https://research-repository.st-andrews.ac.uk/bitstream/handle/10023/13099/Jaradat_2018_Unikernel_support_3rdSRCW.pdf?sequence=1&isAllowed=y)
- [HermitCore: A Unikernel for Extreme Scale Computing](https://dl.acm.org/doi/10.1145/2931088.2931093)
- [Evaluation of Rust for Operating System Development and Porting Key Components of the HermitCore Unikernel](https://colinfinck.de/Master_Thesis_Colin_Finck.pdf)
- [A Binary-Compatible Unikernel](https://www.ssrg.ece.vt.edu/papers/vee2019.pdf)

## Books

- https://www.oreilly.com/library/view/unikernels/9781492042815/


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
