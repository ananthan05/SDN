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


## Running Simple SDN Application (Basic SDN)

- A Mininet topology (switch + hosts)

- A Ryu controller (simple_switch_13) that acts like a Layer 2 learning switch

- Basic flow table inspection (OpenFlow rules)

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

in terminal 2;

<img width="1019" height="542" alt="image" src="https://github.com/user-attachments/assets/5c671ce0-871d-42c2-a91e-9f8755e58746" />


## Build a custom Ryu controller app that:

- Learns the network topology.

- Computes the shortest path (or custom path).

- Installs flow rules to steer traffic.

Step 1: Create a Custom Ryu App

```
cd /Desktop/ryu
```
```
cd ryu
```
```
cd app
```
<img width="1085" height="342" alt="image" src="https://github.com/user-attachments/assets/19c1a526-f5e7-45ee-8658-f77d670b8ff9" />

```
gedit traffic_engineering.py 
```
paste the code there

```py
from ryu.base import app_manager
from ryu.controller import ofp_event
from ryu.controller.handler import CONFIG_DISPATCHER, MAIN_DISPATCHER
from ryu.controller.handler import set_ev_cls
from ryu.ofproto import ofproto_v1_3
from ryu.topology import event, switches
from ryu.topology.api import get_switch, get_link
from ryu.lib.packet import packet, ethernet, ipv4
import networkx as nx

class TrafficEngineering(app_manager.RyuApp):
    OFP_VERSIONS = [ofproto_v1_3.OFP_VERSION]
    
    def __init__(self, *args, **kwargs):
        super(TrafficEngineering, self).__init__(*args, **kwargs)
        self.topology_api_app = self
        self.net = nx.DiGraph()
        self.mac_to_port = {}
        self.datapaths = {}

    @set_ev_cls(ofp_event.EventOFPSwitchFeatures, CONFIG_DISPATCHER)
    def switch_features_handler(self, ev):
        dp = ev.msg.datapath
        ofp = dp.ofproto
        parser = dp.ofproto_parser

        match = parser.OFPMatch()
        actions = [parser.OFPActionOutput(ofp.OFPP_CONTROLLER, ofp.OFPCML_NO_BUFFER)]
        self.add_flow(dp, 0, match, actions)
        self.datapaths[dp.id] = dp

    def add_flow(self, datapath, priority, match, actions):
        ofproto = datapath.ofproto
        parser = datapath.ofproto_parser
        inst = [parser.OFPInstructionActions(ofproto.OFPIT_APPLY_ACTIONS, actions)]

        mod = parser.OFPFlowMod(datapath=datapath, priority=priority,
                                match=match, instructions=inst)
        datapath.send_msg(mod)

    @set_ev_cls(event.EventSwitchEnter)
    def get_topology_data(self, ev):
        switch_list = get_switch(self.topology_api_app, None)
        switches = [sw.dp.id for sw in switch_list]
        self.net.add_nodes_from(switches)

        links_list = get_link(self.topology_api_app, None)
        for link in links_list:
            src = link.src
            dst = link.dst
            self.net.add_edge(src.dpid, dst.dpid, port=src.port_no)
            self.net.add_edge(dst.dpid, src.dpid, port=dst.port_no)

    @set_ev_cls(ofp_event.EventOFPPacketIn, MAIN_DISPATCHER)
    def _packet_in_handler(self, ev):
        msg = ev.msg
        dp = msg.datapath
        ofproto = dp.ofproto
        parser = dp.ofproto_parser
        in_port = msg.match['in_port']

        pkt = packet.Packet(msg.data)
        eth = pkt.get_protocol(ethernet.ethernet)
        dst = eth.dst
        src = eth.src
        dpid = dp.id

        self.mac_to_port.setdefault(dpid, {})
        self.mac_to_port[dpid][src] = in_port

        if dst in self.mac_to_port[dpid]:
            out_port = self.mac_to_port[dpid][dst]
        else:
            out_port = ofproto.OFPP_FLOOD

        # Always try to install path if destination is known
        dst_dpid = self.get_host_location(dst)
        if dst_dpid is not None:
            if nx.has_path(self.net, dpid, dst_dpid):
                path = nx.shortest_path(self.net, dpid, dst_dpid)
                self.install_path(path, src, dst, dst_dpid)

        actions = [parser.OFPActionOutput(out_port)]
        out = parser.OFPPacketOut(
            datapath=dp, buffer_id=ofproto.OFP_NO_BUFFER,
            in_port=in_port, actions=actions, data=msg.data)
        dp.send_msg(out)

    def install_path(self, path, src, dst, dst_dpid):
        # Handle destination switch separately
        if dst_dpid in self.mac_to_port and dst in self.mac_to_port[dst_dpid]:
            out_port = self.mac_to_port[dst_dpid][dst]
            dp = self.datapaths.get(dst_dpid)
            if dp:
                parser = dp.ofproto_parser
                match = parser.OFPMatch(eth_src=src, eth_dst=dst)
                actions = [parser.OFPActionOutput(out_port)]
                self.add_flow(dp, 1, match, actions)

        # Install flows for intermediate switches
        for i in range(len(path) - 1):
            cur_switch = path[i]
            next_switch = path[i + 1]
            out_port = self.net[cur_switch][next_switch]['port']
            dp = self.datapaths.get(cur_switch)
            if dp:
                parser = dp.ofproto_parser
                match = parser.OFPMatch(eth_src=src, eth_dst=dst)
                actions = [parser.OFPActionOutput(out_port)]
                self.add_flow(dp, 1, match, actions)

    def get_host_location(self, mac):
        for dpid in self.mac_to_port:
            if mac in self.mac_to_port[dpid]:
                return dpid
        return None
```
install network module 

```
pip install networkx
```
<img width="990" height="86" alt="image" src="https://github.com/user-attachments/assets/5f0bf5ed-9da3-476d-b95f-3190eb587ab4" />


Step 2: Run It
```
ryu-manager ryu.app.traffic_engineering
```

<img width="1141" height="502" alt="image" src="https://github.com/user-attachments/assets/6f67e208-5d25-47df-a98c-90ae17091213" />


Then in a new terminal:

```
sudo mn --topo=single,3 --controller=remote,ip=127.0.0.1 --mac --switch=ovsk,protocols=OpenFlow13
```

Ping between hosts to trigger flow rule installation

```
pingall
```

<img width="1149" height="730" alt="image" src="https://github.com/user-attachments/assets/0d3f01b0-5b4c-4d2e-8467-b0ca910c1b15" />

<img width="1601" height="611" alt="image" src="https://github.com/user-attachments/assets/4b3a9292-a353-4db6-beb3-49b036aafbd0" />

##  Open vSwitch + Ryu = Traffic Engineering Platform

| Component            | Role                                                                 |
|----------------------|----------------------------------------------------------------------|
| **Open vSwitch (OVS)** | The data plane (switch) that executes flow rules from the controller |
| **Ryu Controller**     | The control plane that pushes intelligent flow rules (e.g., shortest path, reroute) |
| **Mininet**            | Emulates virtual network topologies                                 |
| **OpenFlow 1.3**       | The protocol between controller and switches                        |
