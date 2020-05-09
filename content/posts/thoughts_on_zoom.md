---
title: $ thoughts on zoom
date: 2020-05-09
author: Shain Singh
draft: false
---

The past few months have placed a greater emphasis on remote working and particularly video-conferencing tools. This has placed greater scrutiny on the security practices of companies like Zoom which has received a great deal of press - resulting in better [transparency](https://blog.zoom.us/wordpress/2020/04/01/facts-around-zoom-encryption-for-meetings-webinars/) and a [commitment](https://blog.zoom.us/wordpress/2020/04/08/update-on-zoom-90-day-plan-to-bolster-key-privacy-and-security-initiatives/) to its users for improving its controls.

Putting opinions and conjecture aside for a moment, Zoom's current posture and response got me thinking - in particular, the following aspects were of interest to me:

* Zoom currently have [SOC2, TRUSTe, EU-US Privacy Shield, FedRAMP](https://zoom.us/docs/doc/Zoom-Security-White-Paper.pdf) security and privacy certifications
* Due to the high demand, Zoom had traffic tunneled via China to meet capacity demands, which can now be controlled by [explicitly choosing where your traffic should be routed](https://blog.zoom.us/wordpress/2020/04/13/coming-april-18-control-your-zoom-data-routing/)
* In order to meet the [lack of end-to-encyption](https://blog.zoom.us/wordpress/2020/04/01/facts-around-zoom-encryption-for-meetings-webinars/), [Zoom has taken steps to hire  Alex Stamos (ex-CISO for Facebook)](https://cisac.fsi.stanford.edu/people/alex-stamos-0) and has recently [purchased Keybase](https://blog.zoom.us/wordpress/2020/05/07/zoom-acquires-keybase-and-announces-goal-of-developing-the-most-broadly-used-enterprise-end-to-end-encryption-offering/).

#### Compliance

Without going into details around each of the security and privacy certifications, most organisations look for at least SOC2 and FedRAMP from their cloud service providers (various aaS offerings). Some organisations will not only choose these certifications purely as a baseline upon which to build their own GRC (governance, risk and compliance), but may use them as the ONLY checks they do.

* Do organisations place too much emphasis on compliance to standards without doing their own set of checks?

#### Data Sovereignty

* Do we need the same level of transparency and data routing that Zoom is now providing out of all our cloud-based providers?
* What levels of checks are typically done for supply chains when analysing data transfer and storage? (to belabour the point, Zoom's data was routed through China, [deployed on infrastructure provided by an American-owned public cloud provider, located in a data centre owner by an Australian communication service provider](https://blog.zoom.us/wordpress/2020/04/03/response-to-research-from-university-of-torontos-citizen-lab/))

#### Encryption

End-to-end encryption typically makes use of PKI (Public Key Infrastructure) to ensure data in transit and at rest is secure.

* Have we taken stock of all forms of work/personal communication tools to see whether similar practices are followed, and if they are not do we move towards safer tools or demand better security from the existing tools?
* If end-to-end encryption is not deployed, should we look to ensuring that PFS (Perfect Forward Secrecy) ciphers are used to mitigate against being able to store data and decrypt at a future point in time?

I don't propose to know the answer to any of these questions, and I am not apologising for any of Zoom's mishaps - but I am interested in whether we are placing as much scrutiny across all the software and tools we use in our daily lives (without having to wait for a new article to reactively respond to my security posture)

* Have we taken stock of all forms of work/personal communication tools to see whether similar practices are followed, and if they are not do we move towards safer tools or demand better security from the existing tools?
* If end-to-end encryption is not deployed, should we look to ensuring that PFS (Perfect Forward Secrecy) ciphers are used to mitigate against being able to store data and decrypt at a future point in time?

I don't propose to know the answer to any of these questions, and I am not apologising for any of Zoom's mishaps - but I am interested in whether we are placing as much scrutiny across all the software and tools we use in our daily lives (without having to wait for a new article to reactively respond to my security posture).

I will certainly be keeping a close watch on the 90-day plan that Zoom has outlined, and look to seeing whether there is practices that they will be undertaking that I need to take into account myself (in terms of choice of software for personal use) as well as in conversations with my customers and partners (when discussing security aspects of their migration to public cloud and cloud-based services).
