

**Video:** [Containers From Scratch – Liz Rice (GOTO 2018)](https://www.youtube.com/watch?v=8fi7uSYlOdc   )
**Speaker:** Liz Rice  
**Goal:** To demystify what containers *actually* are by building one from scratch using only Linux primitives — without Docker or any existing container runtimes.

---

## High-Level Objective

The talk demonstrates how Linux itself provides all the necessary components to isolate and restrict processes — the same things Docker uses under the hood. By building a container manually, Liz shows that:

- Docker is a convenience layer.
- Containers are just regular Linux processes with isolation and restrictions configured at runtime.
- You can replicate container behavior using:  
  - Namespaces (to isolate views)
  - chroot and mount (to isolate filesystem)
  - cgroups (to restrict resource usage)

---

## 1. Namespaces: Isolating Visibility

**Linux namespaces** restrict what a process can see or interact with in the system. They’re the foundation for container isolation. There are multiple namespace types, each with specific functions:

- **UTS (Unix Timesharing System)**  
  Controls the hostname and domain name. A container can have a hostname that differs from the host, making prompts inside shells visually distinct.
  
- **PID Namespace**  
  Isolates process IDs. Inside the container, a process will see itself as PID 1 (like `init`), and won’t see host processes. However, the host can still see the container’s processes.
  
- **Mount Namespace**  
  Isolates filesystem mount points. A container can mount/unmount filesystems without affecting the host. Required for preventing container mounts from being visible system-wide.
  
- **Network Namespace** (not demoed in full)  
  Isolates network interfaces. Each container can have its own virtual network stack.
  
- **User Namespace** (briefly mentioned)  
  Allows containers to map user IDs independently of the host, enabling non-root containers.
  
- **IPC Namespace**  
  Isolates inter-process communication mechanisms like shared memory.

**Key Insight:**  
Each namespace gives the process a limited or alternate view of some system resource, contributing to the “illusion” of being a separate machine.

---

## 2. Filesystem Isolation: `chroot` and Mounting

The Linux `chroot` system call changes the root directory seen by a process. This is how a container is shown its own isolated filesystem.

- Liz demonstrates unpacking an Ubuntu root filesystem to a folder and using `chroot()` to restrict the container process to that folder.
- The container sees that folder as `/`, with no access to the host's actual filesystem outside of it.

**Combined with mount namespaces**, this allows full filesystem isolation:
- Host mounts remain invisible to the container.
- Container can mount `/proc`, etc., within its own isolated tree.

This mimics what Docker does when it runs a container from an image: it unpacks the image into a directory and then `chroot`s the process into that path.

---

## 3. /proc: Enabling Process Info in Containers

The `/proc` directory is a special pseudo-filesystem that contains metadata about running processes.

- Tools like `ps` and `top` don’t access kernel internals directly — they read from `/proc`.
- Inside a new PID namespace, if `/proc` isn't remounted, those tools will still see host process IDs or error out.
- To fix this, Liz shows that you need to **mount a new instance of `/proc`** inside the container after `chroot`.

Without this, `ps` inside the container won’t reflect isolated PIDs — making debugging and observability unreliable.

**Important Detail:**  
The process running inside the new PID namespace becomes PID 1 — so it behaves like an init process. This also means it doesn't automatically clean up orphaned children unless explicitly handled.

---

## 4. Control Groups (cgroups): Enforcing Limits

**Cgroups** control the amount of system resources that a group of processes can use. These include:

- Memory
- CPU shares
- I/O bandwidth
- Number of processes (PIDs)

Cgroups are configured by writing values to special files under `/sys/fs/cgroup/`.

**Example:**
- Writing a memory limit to `/sys/fs/cgroup/memory/containerX/memory.limit_in_bytes` restricts RAM usage.
- Writing `20` to `/sys/fs/cgroup/pids/containerX/pids.max` limits the container to 20 processes.

**Demo highlight:**  
Liz creates a container with a process cap of 20, then runs a fork bomb inside it. The container hits the process limit and cannot spawn more — but the host remains unaffected and stable.

**Key Point:**  
This demonstrates how cgroups protect the host from misbehaving or malicious containers. Without this, something like a fork bomb could exhaust all process slots and crash the entire system.

---

## 5. How Docker Uses These Concepts

Liz explains that Docker and similar tools do not create isolation or limits themselves — they just apply these Linux features programmatically:

| Docker Feature           | Linux Mechanism                   |
|--------------------------|-----------------------------------|
| Image root filesystem    | chroot + unpacked tarballs        |
| Isolated hostname        | UTS namespace                     |
| Own PID space            | PID namespace + `/proc` mount     |
| Filesystem separation    | Mount namespace + chroot          |
| Memory/CPU/process caps  | cgroups                           |
| Network interfaces       | Network namespaces                |

**Memory limits**, for example, are just values written to files under `/sys/fs/cgroup/memory/...` — there’s no "memory manager" in Docker itself.

---

## 6. Security and VM Boundaries

- Containers share the **host kernel** — this is the biggest limitation.
- A container breakout via a kernel exploit affects all containers.
- For secure isolation (e.g. in multi-tenant systems), **containers should run inside VMs**.
  - Each VM has its own kernel.
  - Even if a container breaks out inside the VM, the blast radius is smaller.

**Docker-in-Docker Warning:**  
Running Docker inside Docker requires privileged mode (full access to the host kernel), which breaks isolation and is dangerous unless absolutely necessary.

---

## Additional Notes

- Tools like Java and the JVM may misreport available memory because they read from host-level interfaces unless explicitly made container-aware.
- Persistent data (e.g. for databases like Postgres) is a separate concern — containers should mount volumes or external storage to ensure state survives container restarts.
- Virtualization and containers serve different purposes: VMs provide stronger isolation, containers provide lighter-weight packaging and runtime behavior.

---

## Final Message

- You can build containers without Docker.
- Understanding the underlying mechanisms gives you better insight into container behavior, limitations, and security.
- The full demo code is available at: https://github.com/lizrice/containers-from-scratch

This talk is a must-watch for anyone looking to understand containers beyond just Docker CLI usage.
