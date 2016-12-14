# In-band Power Monitoring Tool Survey

## RAPL: Intel's Power API

Contemporary Intel cores (Sandy Bridge or later) use a Running Average Power Limit to monitor the power consumption of the CPU cores and memory. Internally this improves the power model, allowing the processor to run up to its power limit as much as possible using Turbo Boost when running single-core on a multi-core chip. [[1]][rapl] RAPL *does not* perform any analog measurements but is a software model that estimates the power consumption of the processor.

It's also accessible via software *without root* using Linux kernel 3.13+, or installing it(?...). See if it's on with `lsmod | grep rapl`. The folder `/sys/class/powercap/intel-rapl` should also exist.

Anyways, reading from files in that folder allows you to see power information on the sockets and DRAM banks. It does not provide per-core information.

### API?

Trying to find some reference as to what files mean what....

* https://www.kernel.org/doc/Documentation/power/powercap/powercap.txt
* http://lxr.free-electrons.com/source/drivers/powercap/intel_rapl.c
* http://web.eece.maine.edu/~vweaver/projects/rapl/
* http://icl.cs.utk.edu/projects/papi/wiki/PAPITopics:RAPL_Access
* https://www-ssl.intel.com/content/dam/www/public/us/en/documents/manuals/64-ia-32-architectures-software-developer-vol-3b-part-2-manual.pdf

## P-states

P-states are processor operation states. Roughly, the processor can control core voltage and frequency independently. The higher the frequency, the higher the performance (for compute tasks). The higher the voltage, the lower the efficiency. Both contribute to increased power dissipation. Some high-power states may not be sustainable, especially when other cores begin working.

For maximum performance in practice, the processor is allowed to do its own thing, freely adjusting the P-state of cores to give maximum performance when needed (not sure how processes are balanced, OS?) then slowing (P-states other than P0) or stopping (C-states other than C0) cores down if idle.

Because the states are at the whim of the processor and might be influenced by temperature sensors (analog, slightly random), performance between two identical runs of a program might vary. Disabling Turbo Boost can eliminate this source of variation by reducing performance to the lowest "guaranteed" performance level.

Refs:
* [Linux Intel P-state Driver](https://www.kernel.org/doc/Documentation/cpu-freq/intel-pstate.txt)
* [Some basics on CPU P states on Intel processors - Arjan van de Ven](https://plus.google.com/+ArjanvandeVen/posts/dLn9T4ehywL)
* [Balancing Power and Performance
in the Linux Kernel - Kristen Accardi](https://software.intel.com/en-us/blogs/2008/05/29/what-exactly-is-a-p-state-pt-1)
* [Power Management States: P-States, C-States, and Package C-States - Taylor Kidd](https://software.intel.com/en-us/articles/power-management-states-p-states-c-states-and-package-c-states)

## Monitoring Tools

Tools that allow reading of the power model statistics.

### Intercoolr

Provides `etrace2`, a utility written in C that can kick out power information. Can be used to run a process then return how much power was used as it ran (**not** how much *it* used; other processes running concurrently will affect the results).

[GitHub](https://github.com/coolr-hpc/intercoolr/)

### Intel Power Gadget

> Intel® Power Gadget is a software utility and library, which allows developers to monitor power at very fine time granularities (few tens of milliseconds).  To do this, the tool uses an architected capability in Intel® processors which is called RAPL (Runtime Average Power Limiting). RAPL is available on Intel® codename Sandy Bridge and later processors.

* [Official](https://software.intel.com/en-us/articles/intel-power-gadget-20)
* [GitHub (posted by 3rd party)](https://github.com/edlf/powergadgetlinux)

## Management Tools

Tools that allow the control of the processor's power settings (e.g. disabling Turbo Boost).

### Coolr-HPC/turbofreq

> This driver implements a few policies that provide different trade-offs between average performance and performance variability, and provides more fine-grained control to user-space to select the appropriate behavior.

[GitHub](https://github.com/coolr-hpc/turbofreq)

[rapl]: https://01.org/blogs/2014/running-average-power-limit-%E2%80%93-rapl
