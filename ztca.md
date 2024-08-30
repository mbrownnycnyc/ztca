# ZTCA training
* https://customer.zscaler.com/path/zero-trust-architect

# an overview of zero trust

## From Legacy network & security to zero trust

* historically, there has been a reliance on hub-and-spoke and castle/moat design.
* This no longer works
  
### three zero trust principles
* verify identity and context
  * performed by IdP
    * who: user/device, workload, IoT/OT
    * what: attributes (user group, device type, location, time)
    * where: destination app
* control risk
  * prevent data loss: inline DLP, data in SaaS/IaaS/PaaS
  * prevent compromise: cyber threats
  * assess risk: unusual behavior, device/workload posture
* enforce policy
  * per session decision and enforcement via:
    * conditional allow: allow, caution, isolate, prioritize, steer
    * conditional block: block, quarantine, deceive

### why the control plane needed a redesign
* app transformation: apps moved outof the dta center to the cloud
* network transformation: to deliver a productive collaboration experience, traffic must go direct.
* work-from-anywhere: users moved off the network and are connecting from everywhere

### high level comparison of legacy vs zero trust

| .. | legacy network and security architecture | zero trust architecture | 
| -- | -- | -- |
| attack surface | firewalls/vpns published on the internet; can be exploited, susceptible to DDoS | apps not exposed to the internet.  No attack globally exposed surface. | 
| connection | app access requires corporate network access, allows lateral movement of users and threats | connects a specific, authorized user to a specific authorized resource | 
| proxy/pass-through | firewall/pass-through:<br> * inspects a limited data bugger<br> * unknown files pass through<br> * alerts after infection | proxy:<br> * full content inspection (inc. TLS/SSL)<br> * hold and inspectunknown files before reaching the endpoint
| tenancy | VMs of single-tenant appliances in a public cloud | cloud-native, multitenant design like salesforce/workday | 

## Establishing Zero Trust Connections

### connecting to the Zero Trust Exchange (ZTE)
* zero trust controls are in the cloud.
* in order to utilize the zero trust components, users/devices and workloads must connect to the zero trust controls.
* to establish a zero trust connection, the initiator and app/data does not need to share the same network.

### client-to-ZTE connection options
* for user/device client based forwarding:
  * zscaler client connector: client resident agent, creates a tunnel to ZTE for SaaS and internet bound traffic via TLS.  This also provides a point of access control for the endpoint's access to internal applications.
    * internal applications can be pushed into the ZTE mesh utilizing a connectivity tunnel called Zscaler App Connector.
    * This combination creates a "cloud" of apps, where app forwarding policies can be dynamically assigned to the zscaler client connector in order to grant access.
  * zscaler browser access: for unmanaged user devices with no possible agent install, DNS redirects using a CNAME to protect web-based apps (via HTTPS).
* for network forwarding
  * zscaler branch connector (on prem appliance)
* for cloud edge forwarding
  * zscaler cloud connector

* https://customer.zscaler.com/path/zero-trust-architect/zero-trust-architect/1393779/scorm/8mocuj4h333s