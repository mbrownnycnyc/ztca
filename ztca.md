# ZTCA training
* https://customer.zscaler.com/path/zero-trust-architect
* it seems that there's an intro course, then a newer/updated break down of each section that provides enriched/additional information.  I'll be simply updating these sections, so this isn't linear.

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
  * zscaler branch connector (on prem appliance): forward entire sites
    * supports GRE or IPSEC, and integration with SD-WAN vendors
* for cloud edge forwarding
  * zscaler cloud connector: similar to branch connector, but for cloud service provider footprint.

# building an organizational architecture

## section 1: verify identity & context

* verifying identity via three elements:
  * who is the initiator
  * what are the attributes of the conncetion
  * where is the initiator trying to go

### element 1: who (declaring trusted identity)
![](2024-10-24-04-51-38.png)

* user: authentication: single, multi-factor
* device: fingerprint, certificate, user agent string
* workload: agent (process identification), location (network identifiers such as subnet, segment, ip addr, vpc, workload tags)
* IoT: traffic fingerprint, certificate, location (as above)

#### where do you get the above info?
![](2024-10-24-05-03-33.png)

* IdP (via API integrations via SAML or SCIM)
* location and workload config: VLAN/VPC, network identifiers, cloud posture, local identifiers
* site/infrastructure config: site functions, traffic path and flow, local identifiers

#### how do you get the above info?
* for users: determine the user repo (IAM), onboarding/offboarding process, how are you differentialing devices
* for devices: key asset list, inventory (CMDB), location and network maps
* for IoT: site knowledge, function maps, location and network maps

### element 2: what is the access context?
![](2024-10-24-04-56-11.png)

* users:
  * what department/group are they with?
  * what is their location
  * which type of device (corporate managed vs BYOD, pc/notebook/mobile/tablet)
* workload:
  * which environment (dev/test/prod) is it?
* IoT:
  * what type of device is it?  (camera, printer, sensors)

#### trusted versus untrusted device access paths
![](2024-10-24-04-57-18.png)

#### attributes of access
* determine the initiaing users:
  * geography
  * timing (when)
  * device
* determine the type of device:
  * managed: meets a level of compliance (EDR agent, trusted client, certificate, FDE)
  * unmanaged:

#### application segmentation
* granting access control via application segmentation policies is possible
* first phase should be segmenting by critical applications
* second phase should rely on machine learning to understand patterns

### element 3: where is the connection going?

![](2024-10-24-06-20-26.png)

* known apps:
  * externally managed (SaaS, internet) vs. internally managed (IaaS, PaaS, data center)
  * web or non-web (SSH, RDP)
  * app categories
  * decoy apps
  * risk profile:
    * internet/SaaS: domain risk, risk scoring (CASB), configurations (SSPM)
    * IaaS/PaaS: configurations, vulns, user entitlements (CNAPP)
* unknown and newly discovered apps:
  * api-driven risk posture (CASB, CCPM, CNAPP)
  * ml-driven categorization
    * internet/SaaS: automated
    * IaaS/PaaS: recommended

## section 2: control content and access

* three elements of control content and access:
  * Dynamic Risk Assessment
  * Compromise Prevention
  * Data Loss Prevention

### traditional passthrough vs Zscaler proxy architecture
* application centric/proxy
![](2024-10-24-05-08-59.png)

### element 4: assess dynamic risk (AI enabled)
* determine the risk of access:
  * user/device:
    * behavior: malware downloads, access to malicious sites, c2 traffic, impossible travel, change in bandwidth/transaction volume, unusual app access
    * posture: certificate, domain joined, av/edr installed, disk encryption
  * workload/cloud infra/user entitlements:
    * attack surface, vulns, misconfigs
* review Cyber Risk Report
  * ![](2024-10-24-05-13-08.png)
* review specific user behavioral risk
  * ![](2024-10-24-05-13-33.png)
  * this can be added to policy logic

### element 5: prevent compromise
* inline threat protection
  * block the known bad things: pattern, signature, destination
  * quantify the unknown: destination knowledge and assessment, content knowledge and analysis, behavioral analysis (via sandboxing)
* out-of-band threat protection (API scanning)
  * discover malware in SaaS/PaaS/IaaS
    * API scanning for malware
    * sandbox unknown/suspicious files
* intel sources for the above include 40+ threat feeds (crowdstrike, MSFT, etc)

#### inspection
* zscaler **is** a proxy!  you will access the internet egress from Zscaler's data centers, and Zscaler will establish the connection for your client.
* this includes SSL/TLS offloading
* offered to both ZIA and ZPA

### element 6: prevent data loss
* inline data protections
  * data loss prevention via AI
    * predefined, custom dictionaries
    * advanced classifications: EDM (enterprise data manager), IDM (image data manager), OCR
    * enforce MSFT AIP tags
  * inline CASB to control access/usage
    * defined apps
    * 25 risk attributes per app
  * file type controls to control file transfer
    * file type, bandwidth, time of day controls
* out-of-band data protection (API scanning)
  * protect SaaS data:
    * via CASB, SSPM (to determine what does this do), scan data at rest (DLP policies)
  * protect IaaS/PaaS data:
    * via CNAPP (to determine configurations, vulns, user entitlements), scan data at rest (DLP policies)
* third party API integrations: ICAP for third party DLP products, MSFT information protection, other data protection integrations

#### why protect data
* data theft
* accidental loss and oversharing
* compliance issues
* misconfigs lead to cloud data breach

#### browser isolation
* zscaler now has browser isolation

## section 3: enforce policy
![](2024-10-24-05-37-01.png)

* we've covered:
  * section 1: the who is acting
  * section 2: the what they are acting upon
  * section 3: controlling how the *who* act upon *what* they are acting upon

### element 7: policy
* conditional access via policy actions

#### policy actions
* conditional block: block traffic
* deceive: direct attacker to a decoy app
* quarantine: ensure access is limited and protected
* isolate: stream pixels to the browser, restricts the ability to download, copy and paste data
* warn: alert users of potential risk, policy violation
* steer: optimal and pre-defined path selection
* prioritize: some app traffic is prioritized over others when there is congestion
* conditional allow: allow traffic

# connecting to applications

![](2024-10-24-05-43-02.png)

## older network design versus new network design
* older design relies on sharing context with the application at the lower layers of the OSI model (VPN)
* newer design relies on sharing context with the application at the higher layers of the OSI model (via identity and policy, above layer 4).  This is more flexible.

## initiating connections to apps
* securing external app access: outbound (SaaS/Internet based)
  * new session established to the app on behalf of the user, device, IoT or workload
  * the requester remains anonymous
* securing access to internal apps (our data center, IaaS/PaaS)
  * apps are treated as destinations, not network resources (like SaaS apps)
  * apps are invisible (sit behind Zscaler controls) an can't be discovered from unauthorized sources (internet or connected networks)
  * inside-out connections only, no inbound connections to apps (reduces attack surface)
  * connections are made to apps, not networks (prevents lateral movement)
  * app/workload segmentation without network segmentation
    * user/device to app
    * IoT to app
    * workload to workload
    * identity based micro-segmentation (process level)

# summary
* connection provided to zero trust exchange
* establish identity of user/device/workload/IoT
* establish the context of the user/device/workload/IoT based on attributes of the initiator
* understand what the destination is and categorize it
* assess the risk of the initiator and the internal actions of the session
* prevent high risk actions (inbound or outbound)
* provide application access with policies
  * via ZTNE, route traffic to internal apps or external apps

* https://customer.zscaler.com/path/zero-trust-architect