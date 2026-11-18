# Inputs for the network and compute blueprints

Both blueprints list their inputs in one table, with a Section column and a Default column. The Section column names the launch form section and is filled in only on the first row of each section.

## Network: NDFC fabric

Blueprint: `blueprints/network/ndfc-create-fabric.yaml`. All 23 inputs are required. 18 come pre-filled with the default shown, and the other 5 have no default and must be entered. The Section column names the launch form section. It is filled in only on the first row of each section.

| Section | Input | Type | Default | What to enter |
|---|---|---|---|---|
| Infrastructure | agent | agent | none | Torque agent that runs the steps (select one that can reach the target). |
|  | ndfc_credentials | credentials | none | Nexus Dashboard login (created under Integrations > Credentials). |
| Switch Discovery | switch_password | string | none | Login password for the switches being discovered. Sensitive (hidden in logs). |
|  | switch_ip_1 | string | none | Management IP of the first switch to discover and add to the fabric. |
|  | switch_ip_2 | string | none | Management IP of the second switch to discover and add to the fabric. |
| Bare-metal Network | mgmt_vlan_id | string | `10` | VLAN ID of the Bare-metal management network. |
|  | mgmt_vlan_gateway | string | `10.102.2.254` | Gateway of the Bare-metal management network. Also used as the next hop of the VRF static route (the upstream router). |
| VM Network | vm_vlan_id | string | `1024` | VLAN ID of the VM network. |
|  | vm_gateway | string | `10.102.4.1/24` | Gateway address of the VM network with its prefix length (the HSRP virtual IP). |
|  | vm_svi_a | string | `10.102.4.3` | First switch SVI address of the VM network. |
|  | vm_svi_b | string | `10.102.4.4` | Second switch SVI address of the VM network. |
| NFS Network | nfs_vlan_id | string | `3052` | VLAN ID of the NFS network. |
|  | nfs_gateway | string | `192.168.52.1/24` | Gateway address of the NFS network with its prefix length (the HSRP virtual IP). |
|  | nfs_svi_a | string | `192.168.52.3` | First switch SVI address of the NFS network. |
|  | nfs_svi_b | string | `192.168.52.4` | Second switch SVI address of the NFS network. |
| iSCSI A Network | iscsi_a_vlan_id | string | `3012` | VLAN ID of the iSCSI A network. |
|  | iscsi_a_gateway | string | `192.168.12.1/24` | Gateway address of the iSCSI A network with its prefix length (the HSRP virtual IP). |
|  | iscsi_a_svi_a | string | `192.168.12.3` | First switch SVI address of the iSCSI A network. |
|  | iscsi_a_svi_b | string | `192.168.12.4` | Second switch SVI address of the iSCSI A network. |
| iSCSI B Network | iscsi_b_vlan_id | string | `3022` | VLAN ID of the iSCSI B network. |
|  | iscsi_b_gateway | string | `192.168.22.1/24` | Gateway address of the iSCSI B network with its prefix length (the HSRP virtual IP). |
|  | iscsi_b_svi_a | string | `192.168.22.3` | First switch SVI address of the iSCSI B network. |
|  | iscsi_b_svi_b | string | `192.168.22.4` | Second switch SVI address of the iSCSI B network. |

## Compute: OpenShift on bare metal

Blueprint: `blueprints/physical/bmaas-openshift-greenfield.yaml`. 30 inputs: 7 must be entered (Default shows `none`), 9 can be left blank (Default shows `blank allowed`), and 14 come pre-filled with the default shown. The three Hardware inputs are planned and are not in the blueprint yet, so they are not in these counts. The Section column names the launch form section. It is filled in only on the first row of each section.

| Section | Input | Type | Default | What to enter |
|---|---|---|---|---|
| Infrastructure | Intersight | target | none | The Intersight account (and the servers it manages) to provision the OpenShift cluster on. Selecting it automatically wires up authentication and the management agent - no separate agent or credential selection needed. |
|  | Red Hat | credentials | none | Red Hat credentials (pull secret for registry.redhat.io). |
| Hardware | Number of compute nodes | string | none | Number of UCS X210 compute servers (1 to 20). |
|  | Number of PCIe nodes | string | none | Number of UCS X580p PCIe nodes that hold the GPUs. |
|  | Number of GPUs | string | none | Total number of GPUs to assign to the servers. |
| Fabric Interconnect | FI IP | string | none | Management IP of one Fabric Interconnect of the pair (one is enough - the pair is claimed together). |
|  | FI User | string | `admin` | Login username for the Fabric Interconnect. |
|  | FI Password | string | none | Login password for the Fabric Interconnect. Sensitive (hidden in logs). |
|  | FI NTP Servers | string | `171.68.38.65` | Comma-separated NTP servers pushed to the Fabric Interconnects (their domain NTP policy). Must be reachable from the Fabric Interconnects. |
|  | FI Timezone | string | `America/New_York` | Timezone of the Fabric Interconnects' NTP policy (for example America/New_York). |
|  | FI DNS Primary | string | `171.170.168.183` | Preferred DNS server pushed to the Fabric Interconnects. |
|  | FI DNS Secondary | string | `172.22.251.23` | Alternate DNS server pushed to the Fabric Interconnects. |
| Cluster Configuration | Cluster Name | string | `cluster` | Name of the OpenShift cluster. |
|  | Base Domain | string | `org.local` | Base domain for the OpenShift cluster. |
|  | OpenShift Version | string | `4.20.8` | OpenShift version to install (referenced by other deployment steps and scripts). |
| Network | Subnet | string | none | Network subnet of the nodes in CIDR notation, including the /prefix (e.g. 10.122.114.0/23). Used to allocate node IP addresses and for the cluster network configuration; the prefix sets the node netmask, the CIMC pool netmask and the cluster machineNetwork. Host bits are normalized automatically (10.122.114.5/23 becomes 10.122.114.0/23). |
|  | Gateway | string | none | Default gateway IP address of the OpenShift nodes. |
|  | VLAN ID | string | none | The VLAN that carries the node subnet. Validated against the VLANs the Fabric Interconnect carries. |
|  | NTP | string | `pool.ntp.org` | Comma-separated list of NTP server addresses for the OpenShift nodes, reachable from the nodes' deployment network. On disconnected/air-gapped sites, replace the pool.ntp.org default with an NTP server reachable from your site - a preflight check verifies reachability before the install starts. |
| Proxy | HTTP Proxy | string | blank allowed | HTTP proxy URL (leave empty if direct internet access). |
|  | HTTPS Proxy | string | blank allowed | HTTPS proxy URL (leave empty if direct internet access). |
|  | No Proxy | string | `.local,.internal,.svc,localhost,127.0.0.1,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16,169.254.0.0/16,::1` | Comma-separated list of domains/IPs to exclude from proxy. Registry mirror should be included here. |
| Storage | Boot Disk Type | string | `Auto-detect (recommended)` | Disk the OS boots from. Auto-detect resolves the best boot controller per server (M.2 > SAS > NVMe) and is the only correct choice for mixed-hardware clusters. Picking a type forces it on ALL servers and fails clearly on servers without that controller. |
| Security | OpenShift Node SSH Public Key | string | blank allowed | public SSH key for accessing the OpenShift nodes. If no key provided, a new key pair will be generated for you. |
|  | OpenShift Node SSH Private Key | string | blank allowed | SSH private key for monitoring assisted-service logs on the rendezvous machine. If not provided, the key generated during OpenShift preparation is used. |
| Advanced | Exclude IPs | string | blank allowed | (Advanced) Comma-separated IPs to never allocate (e.g. switch management IPs, known conflicts). Infrastructure device IPs from Intersight are auto-excluded. |
|  | Include IPs | string | blank allowed | (Advanced) Comma-separated IPs to force-use without scanning. Use when you know specific IPs are free and want deterministic allocation. |
|  | Preload Harbor Images | string | `true` | (Advanced) Set to 'false' to skip preloading OpenShift release images into Harbor cache. Useful when images are already cached or for faster iteration. |
|  | Preflight Min RAM GB | string | blank allowed | Override the preflight RAM minimum in GB (empty = OpenShift baseline, 32). For lab/test runs on hardware below the production floor - validates the flow, not the customer sizing. |
|  | Preflight Min CPU Cores | string | blank allowed | Override the preflight CPU-cores minimum (empty = OpenShift baseline, 8). |
|  | Preflight Min Disk GB | string | blank allowed | Override the preflight boot-disk minimum in GB (empty = OpenShift baseline, 120). |
|  | Torque URL | string | `https://stackautomation.cisco.com` | (Advanced) Base URL of the Torque instance the cluster's agent/target is registered with (the portal URL, not the cluster API). Keep the default unless the agent is registered to a different instance. |
|  | Cluster Storage | string | `hostpath` | (Advanced) 'hostpath' gives the new cluster a default StorageClass on the node root filesystem, which every terraform grain needs. 'none' if the cluster already has one. |
