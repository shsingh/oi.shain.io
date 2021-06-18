---
title: $ the three most important api specs i see at work
date: 2021-01-22
author: Shain Singh
draft: false
---

One of the most rewarding things about my role at F5 is that it involves talking to customers about their digital transformation initiatives.
The benefit of working for a company that provides a [gateway](https://www.nginx.com/solutions/api-management-gateway/), [web application and API protection](https://www.f5.com/solutions/application-security/web-app-and-api-protection) and [fraud/automated threat defence](https://www.shapesecurity.com/attacks) means that my conversations and workshops are never boring!

Through my customers I have learnt about how APIs have become a critical piece of infrastructure. My real sense of satisfaction comes not only when I help them plug any security gaps they may have, but when I combine with my partner-in-crime [Shahnawaz Backer](https://www.linkedin.com/in/backers/) to provide them with threat data and insights (something they don't need to wait to read in front page news).

From amongst my travels, I have compiled a list of the top 3 most important API specs that any citizen/budding entrepreneur/technology hobbyist/pen-tester should become familiar with in my personal order of noteworthiness.

### 1. [HL7 FHIR](http://hl7.org/fhir/) (Health industry)

Arguably the most important API definition you may have not heard of. Patient records? Somewhat topical post-2020, and yes its all here. HL7 is a standard development organisation that has defined an API for exchanging electronic health records (EHR) called Fast Healthcare Interoperability Resources (FHIR). Examples of uses for healthcare include patient records, admissions details, diagnostic reports and medications which can all be received in JSON or XML formats. FHIR is [seen by many](https://apievangelist.com/2019/09/18/creating-a-postman-collection-for-the-fast-healthcare-interoperability-resources-fhir-specification/) as the most sophisticated openly defined industry schema with government and industry support with the potential to affect all of our lives.

### 2. [3GPP 5G](https://forge.3gpp.org/rep/all/5G_APIs) (Telecommunications industry)

The real transformation in in the telecommunication industry with 5G is around the use of modern application technologies including containers, microservices and the move to protocols such as HTTP/2 for data transport. The 3GPP set of standards include defining interactions between all the components in a 5GC (5G Core) in terms of API contracts. While most of the APIs defined in this standard relate between service-to-service communication, service providers are looking to deploy a more centralised gateway approach with the use of a [Service Communication Proxy (SCP)](https://www.f5.com/content/dam/f5/corp/global/pdf/solution-guides/overcoming-4g-to-5g-migration-challenges-overview.pdf). These standards and technology bring along with it use cases and scenarios that make the rollout of 5G networks relevant not only for common Internet services but also infrastructure (such as smart cities), manufacturing (such as Industrial IoT (IIoT)), and even national defence.

### 3. [PSD2 Open Banking](https://standards.openbanking.org.uk/api-specifications/) (Banking industry)

Perhaps the most talked about set of API standards is for Open Banking. Undoubtedly this standard gets the lion's share of interest because it involves finance and is in a highly regulated industry. Variations of the PSD2 standard include examples which I work with constantly including Australia's [Consumer Data Standards](https://consumerdatastandardsaustralia.github.io/standards/#introduction) which apply for the banking, energy and telecommunications sectors and are for use by third-party providers looking to provide solutions for consumers.

The above standards are the most impactful API specifications I work with day to day with customers given their potential reach, but they are not the only APIs in use. We rely more on APIs every day, sometimes without being aware, from your 'kubectl' command to interact with your Kubernetes cluster, to your 'aws' command-line tool to interface with your public cloud infrastructure. What we do lose sight of sometimes is that all these interactions need inspection, security and management, as it is highly likely that their use extends beyond the organisation itself to third parties and external entities - beyond the protection of IP address and location-based restrictions.  
