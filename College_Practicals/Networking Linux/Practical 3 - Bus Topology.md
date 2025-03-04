To simulate a **bus topology** in Linux, we can create a **virtual network** using a Linux bridge and multiple virtual network interfaces. This setup will mimic how devices communicate over a shared medium. Let's go step by step! 🚀

---

### **Step 1: Install Necessary Packages**

Ensure you have the `bridge-utils` and `iproute2` packages installed. These help in creating network bridges.

```bash
sudo apt update && sudo apt install bridge-utils iproute2 -y
```

---

### **Step 2: Create a Virtual Bridge (Simulating a Bus)**

A bridge acts as the shared communication medium, like a bus cable.

```bash
sudo ip link add name br0 type bridge
sudo ip link set br0 up
```

This creates a network bridge named `br0` and activates it.

---

### **Step 3: Add Virtual Network Interfaces**

We create virtual Ethernet interfaces (`veth` pairs), which act as devices connected to the bus.

```bash
sudo ip link add veth1 type veth peer name veth1-br
sudo ip link add veth2 type veth peer name veth2-br
sudo ip link add veth3 type veth peer name veth3-br
```

Each `veth` pair consists of two interfaces:

- `vethX` (acts like a device's network interface)
- `vethX-br` (connected to the bridge)

---

### **Step 4: Connect Interfaces to the Bridge**

Now, we link the devices to the "bus" (bridge).

```bash
sudo ip link set veth1-br master br0
sudo ip link set veth2-br master br0
sudo ip link set veth3-br master br0
```

Activate the interfaces:

```bash
sudo ip link set veth1 up
sudo ip link set veth1-br up
sudo ip link set veth2 up
sudo ip link set veth2-br up
sudo ip link set veth3 up
sudo ip link set veth3-br up
```

---

### **Step 5: Assign IP Addresses**

Assign IPs to each virtual device to allow communication.

```bash
sudo ip addr add 192.168.1.1/24 dev veth1
sudo ip addr add 192.168.1.2/24 dev veth2
sudo ip addr add 192.168.1.3/24 dev veth3
```

---

### **Step 6: Test Communication**

Now, let's check if all "devices" can communicate.

Try **pinging** from `veth1` to `veth2`:

```bash
ping -c 4 192.168.1.2 -I veth1
```

And from `veth2` to `veth3`:

```bash
ping -c 4 192.168.1.3 -I veth2
```

