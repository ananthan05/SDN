# Introduction

Traditional computer networks are made up of end devices, switches, and routers. These devices communicate by sending packets to each other. Routers and switches are responsible for making sure packets reach the correct destination.

Each of these networking devices has two main parts:
- **Data Plane** – Forwards packets.
- **Control Plane** – Decides how packets should be routed.

The control plane is like the brain of the device. It manages routing tables and runs routing algorithms. As the network grows, more devices are added, and managing all the routing data on each device becomes difficult. This causes a drop in network performance.

**Software-Defined Networking (SDN)** was created to solve this issue in large networks by centralizing the control plane.

<img width="351" height="205" alt="image" src="https://github.com/user-attachments/assets/a9fa4349-b4a4-43e8-8a20-c7328e986332" />

# Software-Defined Networking

SDN is a network architecture that moves the "brain" (control plane) of all network devices into one central controller. All network devices are then managed by this central controller. The devices themselves only handle packet forwarding and are known as **forwarding devices**.

In technical terms, SDN separates the control plane from network devices and centralizes it. The devices are left with just the data plane, responsible for forwarding packets.

<img width="351" height="225" alt="image" src="https://github.com/user-attachments/assets/b47264bc-a4c1-4b68-99e7-6f300963286b" />

# SDN Architecture

The SDN architecture has three main layers:

1. **Application Layer**  
   - Hosts the applications that manage and monitor the network.

2. **Control Layer**  
   - Contains the SDN controller which makes routing decisions and enforces network policies.

3. **Infrastructure Layer**  
   - Contains the forwarding devices (SDN switches) that actually move the packets from one point to another.

<img width="496" height="291" alt="image" src="https://github.com/user-attachments/assets/8a255c67-99c1-491c-9439-bce10a9036df" />

# How SDN Works

1. Applications are built on the **Application Layer**.
2. These applications talk to the **Network Operating System (NOS)** using **Northbound APIs**.
3. The NOS (or controller) then sends instructions to the forwarding devices using **Southbound APIs**.
4. End devices are connected to forwarding devices, which then carry out the instructions to send packets across the network.

# Key SDN Terminologies

- **Network Operating System (NOS)**  
  Also known as the **SDN controller**, it is the central brain of the SDN architecture. It handles all control functions and communicates with the network devices.

- **OpenFlow**  
  OpenFlow is the main protocol used in SDN. It allows the controller to communicate with switches. It’s vendor-neutral, meaning it works across devices from different manufacturers.

- **SDN Switches (Forwarding Devices)**  
  Unlike traditional switches, SDN switches only have a data plane. They don't make routing decisions on their own. These can be hardware-based or software-based.  
  A popular virtual SDN switch is **Open vSwitch (OVS)**, often used to connect virtual machines and end devices.

# Implementation

## Setting Up Environment

### install python3.10

```
sudo apt update`
sudo apt install software-properties-common -y
```

```
 sudo add-apt-repository ppa:deadsnakes/ppa
```
```
sudo apt install python3.10 python3.10-venv python3.10-dev -y
```
check the version

```
python3.10 --version
```
### install Ryu

```
git clone https://github.com/osrg/ryu.git
```
```
cd ryu
```
```
python3.10 -m venv venv310
source venv310/bin/activate
```

```
pip install --upgrade pip setuptools
```
```
gedit  ryu/hooks.py
```
remove the marked items
![51a37154-16ad-4077-a998-7e3e23684f61](https://github.com/user-attachments/assets/532664c3-8a31-48ef-8ac9-23f8117d2cf6)

and place it like this 

<img width="1225" height="619" alt="image" src="https://github.com/user-attachments/assets/c31e0213-e66c-43b4-a115-efc81d997f18" />

```
pip install -e .
```

```
pip install eventlet==0.33.3
```

```
pip install --upgrade dnspython
```

```
ryu-manager --version
```
<img width="698" height="94" alt="image" src="https://github.com/user-attachments/assets/9900f17e-a2e4-41f3-b7bd-987ef6c501df" />



### install Open vSwitch

```
sudo apt-get install openvswitch-switch
```

<img width="685" height="67" alt="image" src="https://github.com/user-attachments/assets/2c312ccb-642b-4077-9962-eb0feec98767" />

### install Mininet

```
sudo apt-get install mininet
```

```
mn --version
```
<img width="665" height="47" alt="image" src="https://github.com/user-attachments/assets/93e853ac-25b5-49fd-baa8-1f752c53a4c1" />


## Running Simple SDN Application

Terminal 1:

```
sudo mn --topo=single,3 --controller=remote,ip=127.0.0.1 --mac --switch=ovsk,protocols=OpenFlow13
```
<img width="1113" height="595" alt="image" src="https://github.com/user-attachments/assets/48ba6e7b-3f08-4622-890f-a22ce318a06c" />


The command in Terminal 1 creates a simple topology in mininet. The topology consists of a single switch and three hosts that are connected to the switch. The topology can be tested by using pingall command on the same terminal. At this point, the hosts are unreachable. For making the hosts communicate with each other, there must be some rules which are implemented by the controller.


Terminal 2:

```
ryu-manager ryu.app.simple_switch_13
```
<img width="1072" height="519" alt="image" src="https://github.com/user-attachments/assets/0b8e6f0d-e117-472c-8950-3ba1899469c0" />

The command in Terminal 2 runs a simple application of switch. The program file for simple_switch_13 locates in /usr/lib/python2.7/ryu/ryu/app/directory. The simple_switch_13 program sets rules and policies in ryu controller, which are necessary for routing packets in a fashion similar to that of L2 Switch of conventional networking. Switch to Terminal 1 and run pingall command again. The hosts will be able to communicate with each other this time. A simple SDN infrastructure has been implemented at this point!

Terminal 3:

```
sudo ovs-vsctl show
```
```
sudo ovs-ofctl -O OpenFlow13 dump-flows s1
```

<img width="1113" height="488" alt="image" src="https://github.com/user-attachments/assets/bc591bb7-989b-4b0f-9b66-c15fe9a5d994" />

The commands in Terminal 3 show the brief configuration and flow table of Open vSwitch. These commands are used for inspecting the behavior and statistics of SDN switches upon receiving packets from the hosts.


Terminal 1
try ping command

```
pingall
```
<img width="1077" height="604" alt="image" src="https://github.com/user-attachments/assets/2dfd30f7-322c-4ada-89fc-ad1707d75f45" />


