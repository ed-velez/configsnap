# configsnap

Records useful system state information, and compare to previous state if run
with PHASE containing "post" or "rollback".

Tested and packaged for RHEL and it's derivatives through the EPEL repository.
Packages are also supplied for recent Debian based systems, however they are
less tested.

```
Usage: configsnap [options]

Record useful system state information, and compare to previous state if run
with PHASE containing "post" or "rollback". A default config file,
/etc/configsnap/additional.conf, can be customised to include extra files, directories
or commands to register during configsnap execution.

Options:
  -h, --help            show this help message and exit
  -w, --overwrite       if phase files already exist in tag dir, remove
                        previously collected data with that tag
  -a, --archive         pack output files into a tar archive
  -v, --verbose         print debug info
  -V, --version         print version
  -s, --silent          no output to stdout
  --force-compare       Force a comparison after collecting data
  -t TAG, --tag=TAG     tag identifer (e.g. a ticket number)
  -d BASEDIR, --basedir=BASEDIR
                        base directory to store output
  -p PHASE, --phase=PHASE
                        phase this is being used for. Can be any string.
                        Phases containing  post  or  rollback  will perform
                        diffs
  -C, --compare-only    Compare existing files with tags specified with --pre
                        and --phase
  --pre=PRE_SUFFIX      suffix for files captured at previous state, for
                        comparison
  -c CONFIG, --config=CONFIG
                        additional config file to use. Setting this will
                        override default.
```

Example output:
```
# ./configsnap -t junepatching -p pre
Getting storage details (LVM, partitions, PowerPath)...
Getting process list...
Getting package list and enabled services...
Getting network details and listening services...
Getting cluster status...
Getting misc (dmesg, lspci, sysctl)...
Getting Dell hardware information...
Copying files...
 /boot/grub/grub.conf
 /etc/fstab
 /etc/hosts
 /etc/sysconfig/network
 /etc/yum.conf
 /proc/cmdline
 /proc/cpuinfo
 /proc/meminfo
 /proc/mounts
 /proc/scsi/scsi
 /etc/sysconfig/network-scripts/ifcfg-eth3
 /etc/sysconfig/network-scripts/ifcfg-lo
 /etc/sysconfig/network-scripts/ifcfg-eth1
 /etc/sysconfig/network-scripts/ifcfg-eth0
 /etc/sysconfig/network-scripts/ifcfg-eth2
 /etc/sysconfig/network-scripts/route-eth2


Finished! Backups were saved to /root/junepatching/configsnap/*.pre
```

Custom collection of additional command output (Type: command) and files (Type:
file) can be configured in the file /etc/configsnap/additional.conf, for
example:

```
[psspecial]
Type: command
Command: /bin/ps -aux
Compare: True
     # Recording the output of a command into a "psspecial.<phase>" file containing the output.

[debconf.conf]
Type: file
File: /etc/debconf.conf
Failok: True
     # Recording an additional file, stored as "debconf.<phase>"

[ssh]
Type: directory
Directory: /etc/ssh/
     # Recursively Recording all files from /etc/ssh/ directory, with sub-files appended with ".<phase>".

[fail2ban]
Type: directory
Directory: /etc/fail2ban
File_Pattern: .*\.local$
     #  Recording  all  files  from  /etc/fail2ban/  directory  matching  '.*\.local$', with sub-files
     appended with ".<phase>"
```

