Pushdown FUSE
================

```
XX              XXXXX XXX         XX XX           XX       XX XX XXX         XXX
XX             XXX XX XXXX        XX XX           XX       XX XX    XX     XX   XX
XX            XX   XX XX XX       XX XX           XX       XX XX      XX XX       XX
XX           XX    XX XX  XX      XX XX           XX       XX XX      XX XX       XX
XX          XX     XX XX   XX     XX XX           XX XXXXX XX XX      XX XX       XX
XX         XX      XX XX    XX    XX XX           XX       XX XX     XX  XX
XX        XX       XX XX     XX   XX XX           XX       XX XX    XX   XX
XX       XX XX XX XXX XX      XX  XX XX           XX XXXXX XX XX XXX     XX       XX
XX      XX         XX XX       XX XX XX           XX       XX XX         XX       XX
XX     XX          XX XX        X XX XX           XX       XX XX         XX       XX
XX    XX           XX XX          XX XX           XX       XX XX          XX     XX
XXXX XX            XX XX          XX XXXXXXXXXX   XX       XX XX            XXXXXX
```

This is the prototype implementation of the FUSE module to be run at each pNFS data server to provide a file-based interface for pNFS clients to securely offload analysis operations and retrieve results.

# Prerequisites

We need a recent c++ compiler, cmake, pkgconf, libfuse3, and nfs utilities.

On ubuntu, these can be installed as below.

```bash
sudo apt install build-essential cmake cmake-curses-gui pkg-config fuse3 libfuse3-dev nfs-kernel-server nfs-common
```

# Compile

Follow the standard cmake procedure to compile the codebase.

```bash
git clone https://github.com/lanl-remote-contour/fuse0.git
cd fuse0
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
```

# Run

Create a mount point of choice (e.g., /fuse) and run the main executable (`fuse0`) to mount the FUSE filesystem at that mount point. We expect the fuse program to be run as root with `-o allow_other` to allow access from non-root users. One may use `-f` to run the FUSE daemon as a foreground process rather than in the background. One may also use `-d` to turn on debugging implemented by the FUSE library.

```bash
sudo mkdir /fuse
sudo ./fuse0 -o allow_other /fuse
```

After mounting the FUSE filesystem, configure the Linux kernel to export it to remote clients via standard NFS. To do this, first add the `/fuse *(rw,no_subtree_check,no_root_squash,insecure,sync,no_acl,fsid=1000)` line to `/etc/export`. Please make sure that the FUSE mount point (`/fuse`) matches the moint point used in the previous step. Please also pick a unique id for fsid, which is needed by NFSv4. Next, run the following commands to export the FUSE mount.

```bash
sudo exportfs -av
```

To tear down, first remove (or comment) the line in `/etc/export`. Then run the following commands to un-export the FUSE mount point and umount the FUSE.

```bash
sudo exportfs -r
sudo umount /fuse
```

# Acknowledgement

This codebase is authored by an employee of Triad National Security, LLC which operates Los Alamos National Laboratory for the U.S. Department of Energy/National Nuclear Security Administration.


