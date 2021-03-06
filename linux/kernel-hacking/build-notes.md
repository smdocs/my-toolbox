# Quick kernel hacking with QEMU + buildroot



Use QEMU. QEMU is often used in conjunction with KVM for virtualization of complete images and hardware.
A full image is overkill for what I want. 99% of the time, I want to boot a kernel I just built and get to a shell 
so I can run some commands. 

[Buildroot](https://buildroot.org/) is a project primarily designed to create an embedded Linux distribution. It's also useful for 
creating a quick stripped down system.

Getting this set up is fairly fast. Grab a kernel from kernel.org:

```bash
$ git clone git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
$ cd linux
```

The kernel comes with a set of config files in tree:
```bash
$ ls arch/x86/configs/
```

i386_defconfig  kvm_guest.config  tiny.config  x86_64_defconfig  xen.config
These contain a minimal set of config options to boot. I typically just start with the x86_64_defconfig

```bash
$ make x86_64_defconfig
```

Then build
```
$ make
```
You can add -j(number of cpus -1) to speed up. Compared to building a Fedora kernel, this will finish quickly. This gives you a 
kernel ready to boot but you still need a root file system.

Start by cloning buildoot

```
$ git clone git://git.buildroot.net/buildroot
$ cd buildroot
```

Buildroot uses the same interface as the kernel for configuration (ncurses based, make sure you have ncurses-devel installed)
```
$ make menuconfig
```

Start out by setting the appropriate architecture (assuming x86_64)

Target options -> Target Architecture -> x86_64
And then set the file system

Filesystem images -> cpio the root filesystem
install some dependencies
```
$ dnf install perl-Thread-Queue #and build
$ make
```

This will take some time, mostly because buildroot has to build a lot of things (gcc). Once that's done, all 
components necessary to boot in QEMU. The following script is used for booting and is based on one that 0-day testing.

```bash
#!/bin/bash

kernel=$1
initrd=$2

if [ -z $kernel ]; then
    echo "pass the kernel argument"
    exit 1
fi

if [ -z $initrd ]; then
    echo "pass the initrd argument"
    exit 1
fi

kvm=(
    qemu-system-x86_64
    -enable-kvm
    -cpu kvm64,+rdtscp
    -kernel $kernel
    -m 300
    -device e1000,netdev=net0
    -netdev user,id=net0
    -boot order=nc
    -no-reboot
    -watchdog i6300esb
    -rtc base=localtime
    -serial stdio
    -vga qxl
    -initrd $initrd
    -spice port=5930,disable-ticketing
    -s
)

append=(
    hung_task_panic=1
    earlyprintk=ttyS0,115200
    systemd.log_level=err
    debug
    apic=debug
    sysrq_always_enabled
    rcupdate.rcu_cpu_stall_timeout=100
    panic=-1
    softlockup_panic=1
    nmi_watchdog=panic
    oops=panic
    load_ramdisk=2
    prompt_ramdisk=0
    console=tty0
    console=ttyS0,115200
    vga=normal
    root=/dev/ram0
    rw
    drbd.minor_count=8
)
```

"${kvm[@]}" --append "${append[*]}"

And invoke it with ./qemu-cmd.sh ~/linux/arch/x86/boot/bzImage ~/buildroot/output/images/rootfs.cpio. 
You may need to poke options in your BIOS to make kvm work (I had to do so on one laptop). If all goes well, you should end up 
with a login prompt. Enter username root to login (this is the default for buildroot).

If all does not go well you can use gdb to help you along. Enable CONFIG_DEBUG_INFO and CONFIG_GDB_SCRIPTS in the kernel. 
In another terminal
```
$ gdb path/to/vmlinux
(gdb) target remote localhost:1234
(gdb)
```

You are now attached. I haven't used this too much for actual runtime debugging. I mostly use it for grabbing dmesg output
when I crash the kernel before the console gets initialized

```
(gdb) lx-dmesg
```
Note you may need to add add-auto-load-safe-path path/to/kernel/scripts/gdb/vmlinux-gdb.py to .gdbinit.
This setup is easily expandable for testing other architectures. I often test arm and arm64. My arm boot tricks are really 
hacky but arm64 is relatively standard. Fedora provides cross toolchains

```
$ dnf install gcc-aarch64-linux-gnu.x86_64
```
and building a cross compiled kernel is not too difficult.

```
$ make ARCH=arm64 defconfig
$ make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j8
```
For buildroot, you change the architecture to 'aarch64 little endian' and rebuild. I use a much simpler command for booting arm64:

```
qemu-system-aarch64 \
    -s \
    -machine virt \
    -cpu cortex-a57 \
    -smp 4  \
    -machine type=virt \
    -nographic \
    -m 2048 \
    -kernel ~/arm64_kernel/arch/arm64/boot/Image \
    --append "console=ttyAMA0" \
    -initrd ~/buildroot/output/images/rootfs.cpio
```

To avoid needing to rebuild buildroot each time I change architectures, I typically save the rootfs in a folder somewhere.

