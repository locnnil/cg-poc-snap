# PoC: Using the Cgroups Filesystem from a Strictly Confined Snap

This project demonstrates how to isolate a workload from within a strictly confined Snap using `cpusets` from cgroup v2.

The Snap packages a dummy application called `rt-app`, along with a script responsible for creating a new cgroup v2 and moving the application execution into it.

## Testing the Application

Since this Snap is not available on the Snap Store, you must build, install, and connect the required interfaces manually before running it.

### Build the Snap

```bash
snapcraft -v
````

### Install the Snap

```bash
sudo snap install poc-cgsnap*.snap --dangerous
```

### Connect Required Interfaces

```bash
sudo snap connect poc-cgsnap:sys-fs-cgroup
sudo snap connect poc-cgsnap:log-observe  # Optional
```

### Run the `run` Script

The `run` script must be executed as root. It accepts a single argument: the CPU shield string in [cpulist][cpulists] format.

```bash
sudo poc-cgsnap.run <cpu-list>
```

### Verify Cgroup Isolation

To confirm that the specified CPUs were isolated, check their presence in the `/sys/fs/cgroup/cpuset.cpus.isolated` file:

```bash
cat /sys/fs/cgroup/cpuset.cpus.isolated
```

### Verify That the App Is Running on the Specified CPUs

For example, if the `cpulist` is `22`, you can confirm the app is running on that CPU:

```bash
ps -eLo psr,comm,args,pid | grep '^ 22' | grep rt-app
 22 rt-app          /bin/bash /snap/poc-cgsnap/   79996
```

<!-- Links -->
[cpulists]: https://docs.kernel.org/admin-guide/kernel-parameters.html#cpu-lists
