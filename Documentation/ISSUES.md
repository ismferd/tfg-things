## ISSUES
### ISSUES Installing Kubernetes 1.14.1 in Raspberry (with cloudy) from scratch

- Firstable, the docket package that Cloudy installed is an old one (docker.io), currently, kubernets doesn't support that one. In order to get the correct package we need to follow the [documentation](https://docs.docker.com/install/linux/docker-ce/debian/) in docker.com.
- The next issue was running the preflight checking, Kubernetes before start check the correct status to work properly (cgroups, swappoff, and so on).
```sh
[init] Using Kubernetes version: v1.14.1
[preflight] Running pre-flight checks
[preflight] The system verification failed. Printing the output from the verification:
KERNEL_VERSION: 4.14.98-v7+
CONFIG_NAMESPACES: enabled
CONFIG_NET_NS: enabled
CONFIG_PID_NS: enabled
CONFIG_IPC_NS: enabled
CONFIG_UTS_NS: enabled
CONFIG_CGROUPS: enabled
CONFIG_CGROUP_CPUACCT: enabled
CONFIG_CGROUP_DEVICE: enabled
CONFIG_CGROUP_FREEZER: enabled
CONFIG_CGROUP_SCHED: enabled
CONFIG_CPUSETS: enabled
CONFIG_MEMCG: enabled
CONFIG_INET: enabled
CONFIG_EXT4_FS: enabled
CONFIG_PROC_FS: enabled
CONFIG_NETFILTER_XT_TARGET_REDIRECT: enabled (as module)
CONFIG_NETFILTER_XT_MATCH_COMMENT: enabled (as module)
CONFIG_OVERLAY_FS: enabled (as module)
CONFIG_AUFS_FS: not set - Required for aufs.
CONFIG_BLK_DEV_DM: enabled (as module)
DOCKER_VERSION:
OS: Linux
CGROUPS_CPU: enabled
CGROUPS_CPUACCT: enabled
CGROUPS_CPUSET: enabled
CGROUPS_DEVICES: enabled
CGROUPS_FREEZER: enabled
CGROUPS_MEMORY: missing
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR IsDockerSystemdCheck]: cgroup driver is not defined in 'docker info'
	[ERROR Swap]: running with swap on is not supported. Please disable swap
	[ERROR SystemVerification]: unsupported docker version:
	[ERROR SystemVerification]: missing cgroups: memory
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`

problems with images not are compatibles with arm
```
  - swappoff: The idea of kubernetes is to tightly pack instances to as close to 100% utilized as possible. All deployments should be pinned with CPU/memory limits. So if the scheduler sends a pod to a machine it should never use swap at all. You don't want to swap since it'll slow things down.
  - enbaling cgroups: The Linux kernel accepts a command line of parameters during boot. On the Raspberry Pi, this command line is defined in a file in the boot partition, called cmdline.txt. This is a simple text file that can be edited using any text editor, e.g. Nano. So, We need to enable Cgroups doing:

  ```sh
  echo Adding " cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory" to /boot/cmdline.txt
  sudo cp /boot/cmdline.txt /boot/cmdline_backup.txt
  orig="$(head -n1 /boot/cmdline.txt) cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory"
  echo $orig | sudo tee /boot/cmdline.txt
  ```


