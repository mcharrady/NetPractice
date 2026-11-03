*This project has been created as part of the 42 curriculum by mecharra.*

# NetPractice

## Description

NetPractice is an introduction to computer networking. The goal of the project is to understand how devices communicate inside a network and across several networks, by configuring simulated networks until every connection works.

The project is made of **10 levels** in a browser-based training interface. In each level, a network diagram is broken or incomplete (hosts, switches, routers, and the Internet). The task is to fill in the editable fields so that all the objectives of the level are met. The fields are:

- IP addresses of hosts and router interfaces
- Subnet masks
- Default gateways
- Routing table entries

The interface shows logs that explain why a packet fails (for example, an invalid gateway, a loop, or a broadcast address used by a host), which helps to find and fix each mistake.

## Instructions

### 1. Running the training interface

1. Download the project files and extract them.
2. Open a terminal in the extracted folder and run:

   ```sh
   ./run.sh
   ```

   The script starts a local web server and opens the training interface in the browser.
3. If the script does not work (for example, a permission error or a busy port), start a server manually from the same folder:

   ```sh
   python3 -m http.server 49242
   ```

   Then open `http://localhost:49242` in the browser. The port number can be changed if it is already in use.
4. **Enter your login** in the interface before doing anything else.

### 2. Working on a level

1. Read the **objectives** at the top of the level (for example, "Goal 1: host my PC needs to communicate with host my little brother's computer - Status: KO - No forward way, try again ...").
2. Only the **unshaded** fields can be edited. Shaded fields are fixed and cannot be changed.
3. Fill in the IP addresses, masks, gateways, and routes.
4. Click **Check again** to test. The **logs** at the bottom explain each failure.
5. When every objective is green (statuseOK), the level is solved and the next one is unlocked.

Method used to solve a level:

- Compute the network address, broadcast address, and usable range of each subnet from its IP and mask.
- Check that devices that talk directly are in the same subnet.
- Check that each gateway is an address of the directly connected router, in the same subnet as the host.
- Check the routing tables of routers and of the Internet, in both directions (the forward way and the reverse way).

### 3. Exporting the configuration

1. Make sure your login is entered in the interface.
2. On a solved level, click the **Get my config** button.
3. Save the exported file. Keep the name given by the interface.
4. Repeat for all **10 levels**.

### 4. Submission requirements

- There are 10 levels, so **10 exported configuration files (one per level) must be placed at the root of the Git repository**, next to this `README.md`.
- Expected layout of the repository:

  ```text
  .
  ├── README.md
  ├── <config file for level 1>
  ├── <config file for level 2>
  ├── ...
  └── <config file for level 10>
  ```


### 5. Defense

- 3 random levels must be completed successfully, in a limited amount of time.
- No external tools are allowed. A simple calculator such as `bc` is tolerated, but it is the limit.

## Resources

**TCP/IP addressing**
- An IPv4 address is 32 bits, written as four decimal numbers (octets) from 0 to 255, for example `192.168.1.10`.
- An address has two parts: the **network part** (identifies the subnet) and the **host part** (identifies the device inside it). The subnet mask decides where the split is.
- In every subnet, two addresses cannot be given to a device: the **network address** (all host bits at 0) and the **broadcast address** (all host bits at 1).
- **Private ranges** (RFC 1918) are used inside local networks and are not routed on the Internet: `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.
- The **loopback range** `127.0.0.0/8` means "this machine itself". Packets sent there never leave the host, so it is invalid on an outside interface.

**Subnet masks**
- A mask marks which bits are the network part. It can be written in dotted form (`255.255.255.128`) or in CIDR form (`/25`, the number of network bits).
- The **block size** of a subnet is `2^(host bits)`. For `/25` it is 128, for `/28` it is 16, for `/30` it is 4.
- To find the subnet of an address, round its last relevant octet down to a multiple of the block size. Example: `65.116.204.227/25` is in the block `.128` to `.255`, so network `.128`, broadcast `.255`, usable `.129` to `.254`.
- Two devices can talk directly only if they are in the **same subnet**.
- Common masks: `/24` = `255.255.255.0`, `/25` = `255.255.255.128`, `/26` = `255.255.255.192`, `/27` = `255.255.255.224`, `/28` = `255.255.255.240`, `/29` = `255.255.255.248`, `/30` = `255.255.255.252`.

**Default gateways**
- The default gateway is the router a host sends packets to when the destination is **not** in its own subnet.
- It must be an address of the **directly connected router**, in the **same subnet** as the host. It is the next hop, never the final destination and never the host's own address.

**Routers and switches**
- A **router** connects different networks (layer 3). It has one interface per network and uses a **routing table** to decide where to forward a packet.
- A **switch** connects devices inside the same network (layer 2). It has no IP address of its own in these exercises and does not change subnets.

**Routing tables**
- Each entry has a **destination** (network and mask) and a **next hop** (gateway) or an exit interface.
- The **default route** (`0.0.0.0/0`) matches everything that has no more specific route.
- Routing must work in **both directions**: the forward way and the reverse way. If a router or the Internet has no route back to the source, the reply is lost.
- Common errors: a next hop that is not in a directly connected subnet, a missing route back, a route pointing out of the interface the packet came from (a **loop**).

**OSI layers**
| Layer | Name | Role in this project |
|---|---|---|
| 7 | Application | Services such as web servers (HTTP) |
| 6 | Presentation | Data format and encryption |
| 5 | Session | Opening and closing sessions |
| 4 | Transport | TCP and UDP, ports |
| 3 | Network | IP addresses, subnets, routing (**main focus**) |
| 2 | Data link | Switches, MAC addresses (**switching**) |
| 1 | Physical | Cables and signals |

### References

- RFC 791 - Internet Protocol: https://www.rfc-editor.org/rfc/rfc791
- RFC 1918 - Address Allocation for Private Internets: https://www.rfc-editor.org/rfc/rfc1918
- RFC 4632 - Classless Inter-domain Routing (CIDR): https://www.rfc-editor.org/rfc/rfc4632
- Wikipedia - Subnetwork: https://en.wikipedia.org/wiki/Subnetwork
- Wikipedia - OSI model: https://en.wikipedia.org/wiki/OSI_model
- Wikipedia - Default gateway: https://en.wikipedia.org/wiki/Default_gateway

### Use of AI

AI was used as a learning and explanation tool, not to generate the submitted configurations blindly. It was used for:

- Explaining the subject and the structure of the project.
- Explaining networking concepts (subnet masks, block sizes, network and broadcast addresses, gateways, routing).
- Helping to read and understand the simulator's log messages when a level failed.
