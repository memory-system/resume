---
title: What is SRE?
layout: "study"
draft: true

---

### I: SRE Anti-patterns
---
An *anti-pattern* is a trap or less-than-ideal practice that gets in the way of stability or dependability in some way. An essential role in SRE is the discovery and mitigation of anti-patterns. 
#### Summary
One main goal is to prioritize incident management to prevent and resolve SRE anti-patterns.
When issues do arise the priority is to resolve the issue and get the system running as normal but once this has been done the next step is post mortem or root cause analysis to minimize the likelihood of the reoccurrence of similar issues.
Another factor for mitigation of issues is through cross functional collaboration. 
#### Videos
- [Persistent SRE Antipatterns with Blake Bisset and Jonah Horowitz](https://www.youtube.com/watch?v=7Y06GIHlZl8)
	##### Summary
	"If you need a monitor to tell you something is wrong then your alerts are not configured correctly."
	Alerts should be actionable.
	First alerts: user experience driven
	###### Noise floor
	When gathering data from large systems there will be a lot of false positives that need to be ignored. This is the *noise floor*.
	###### SRE Burnout
	Attempt to solve programmatically before fully relying on human intervention to prevent *SRE burnout*
	50/50 building new systems: fixing issues
	###### War Rooms
	*War Rooms* should be dynamic and built up when issues need to be investigated. Think of high severity alert calls with several engineers each with access to a Graphana dashboard remotely.
	I once had to respond to an alert in a bar in las vegas because I was getting dinner on saturday and the last few weekend rotations were quiet so I thought I could get dinner and enjoy some live music. I wasn't even on call I was assisting the on call engineer.
	Build distributed monitoring systems that can be accessed remotely.
	##### Incident Response
	Big alerts generate a big response. Coordinate who owns the problem and do that with an on call schedule and raci table to determine who is responsible, accountable, consulted, and informed when and which specific alerts are thrown.
	##### Pet, Cattle, Chickens, and Snowflakes
	Configuration management is a great tool but can be thought of as hiring a dog walker to handle all your pets. This does not convert them to cattle. Strive to make immutable infra and bake the application code in the immutable infrastructure.
	##### Production Ready Checklist
	As release approach there should be a list of features that the new version needs to maintain: tests it should pass.
	Use frameworks and templated configs can guarantee small wins for new implementations of SRE in a software stack.
	##### N+1 Nines
	How do we get 100% uptime? Make no changes to a stable system.
	What is a better way to measure success as an SRE. Find Target values: SLOs. We will cover that more next module.
	When changes are made reconsider the measurement of SLOs that determine SLAs.
	##### Innovation vs Availability
	The application sits on a curve of innovation and availability
	##### Complex Systems Fail
	99.999% uptime is not a dogma. Look at your application and see if that is a fair or necessary goal. If you are building market trading software does it matter that there is downtime when the market closed? Ask questions like these for your application.
	
#### Case Studies
- [Defense in Depth works for Reliability](https://youtu.be/OUYTNywPk-s?t=148)
	- Cast
		- K8s
		- etcd
		- Linkerd
		- Humans
	- Plot
		- etcd upgrade ver 2 to ver 3
	- Setting
		- A faulty service scaled down to zero nodes in k8s
		- Ledger change deployed. This is done frequently but important
		- Issue in ledger discovered and rolled back
	- Action
		- Linkered watches k8s api and load balances.
		- Linkered determined to be unhealthy
		- Followed sop and restarted Linkerd pods
		- New Linkerd pods cannot start back up
		- K8s needed to be restarted
		- Linkerd NullPointerException 
		- Discovered Linkerd and k8s are incompatible
		- Rolled back to compatible versions
	- Summary
		- 1hr 21 min downtime
		- No real customer impact
	- Credits
		- Root cause analysis
		- Bug in gRPC client lib affected etcd
		- Linkerd and k8s incompat
		- Human error
	- Moral of the Story
		- Defence in depth: multiple layers of reliability to ensure things are reliable.
		- Transactions could still happen by circumventing the disabled pods
		- Implement a "Chaos Monkey". A chaos monkey randomly and in a controlled way shuts down nodes to determine how the system will respond to dropped nodes.
		- Monitor more and in a more visible way.
		- Transparency and community is essential.

#### Articles
- [Pitfalls on the Road to Creating a Successful SRE Program Like Netflix and Google](https://www.usenix.org/conference/lisa17/conference-program/presentation/bisset)
- [Google Explains Why Others Are Doing SRE Wrong](https://www.infoq.com/news/2018/07/google-explains-sre/)
	- Notes: 
		- example SLOs at Google include 99.9% monthly uptime(43 mins of downtime a month), 99.99% of HTTP requests succeed with 200, and 50% HTTP requests respond< 300ms.
		- "The error budget is the gap between perfect reliability and our SLO"
		- "SRE needs SLOs with consequences that balance an acceptable level of failure with the necessary cost and speed of delivery"
		- These are five key steps to get on the right path to SRE:
			1. Define contextual, customer-focused SLOs
			2. Define sensible error budget policies
			3. Hire (internally or externally) SREs and empower them via leadership support
			4. Allow SREs to fine-tune SLOs and enforce error budget policies
			5. Assign responsibility for mission-critical systems' reliability to SRE teams, other systems under the responsibility of the corresponding development team
- [Pets, Cattle, Chickens, and Snowflakes](https://subscription.packtpub.com/book/virtualization_and_cloud/9781785882753/1/ch01lvl1sec08/pets-cattle-chickens-and-snowflakes)
	- *Pets* get a lot of attention akin to traditional servers or vms
		- Each pet has a name; for example, `myserver.domain.com`.
		- When they're not well, you take them to the vet to help them get better. You employ SysAdmins to look after them.
		- You pay close attention to them, sometimes for years. You take backups, patch them, and ensure that they are fully documented.
	- *Cattle* too many to name, like modern cloud infra
		- You've got too many to name, so you give them numbers; for example, the URL could look something like `ip123123123123.eu.public-cloud.com`.
		- When they get sick, you shoot them and if your herd requires it, you replace anything you've killed: A server crashes or shows signs that it is having problems, you terminate it and your configuration automatically replaces it with an exact replica.
		- You put them in a field and watch them from far and you don't expect them to live long. Rather than monitoring the individual instances, you monitor the cluster. When more resources are needed, you add more instances and once the resource is no longer required, you terminate the instances to get you back to your base configuration.
	- *Chickens* not precious at all, like containers
		- They're more efficient than cattle when it comes to resource use. A container can boot in seconds where a instance or server can take minutes; it also uses less CPU power than a typical virtual machine or cloud instance.
		- There are many more chickens than cattle. You can quite densely pack containers onto your instances or servers.
		- Chickens tend to have a shorter lifespan than cattle and pets. Containers lend themselves to running micros-services; these containers may only be active for a few minutes.
	- *Snowflakes* unique and requires special handling: manual intervention
		- Snowflakes are delicate and are treated with kid gloves. Typically, the server has been in the data center since you started. No one knows who originally configured it and there is no documentation of it; all you know is that it is important.
		- Each one is unique and is impossible to exactly reproduce. Even the most hardened SysAdmin fears to reboot the machine incase it doesn't boot afterwards, as it is running end-of-life software that can not easily be reinstalled.
- [TechBiz Do you know what SRE is and what it can do for your business?](https://en.paradigmadigital.com/techbiz/do-you-know-what-sre-is-and-what-it-can-do-for-your-business/)
	- One of the biggest problems Google had to manage its services was the tussle between the development people and the operations people.
	- Dev's goal is functionality. Ops goal is stability. These goals are antithetical. To solve this problem, Google created SRE. SRE has these as its basic principles.
	- **Only hire people who know how to program**, since the first obligation of a site reliability engineer (SRE) is to write code. The consequence of putting someone who develops to carry out operations is that they will try to automate their work.
	- **Define the Service-Level Objective (SLO) for each service** – which will typically be its level of availability.
	- **Use SLOs to measure the performance of every service and make reports about them**.
	- **Use an error budget as a launch criterion** (we will further explain this concept below).
	- **Both SREs and developers belong to the same staff pool** and they are all treated as developers. There is no line separating them. Developers are invited to try the job of SRE for a while and to stay in it if they so desire it.
	- **Any surplus ops work will fall on the development team**.
	- **Limit the operational load of each SRE to 50% of their time** in order for them to at least spend the remaining 50% automating things and improving reliability.
	- **The SRE team shares 5% of the ops work with the dev team**. If functionalities are added that make the system unstable, the SRE team may send the product back to the dev team because it is nor ready to support it. Programmers will support of the product on a full-time basis if it is not ready to be supported in production.
	- **A standby team is made up of 8 engineers at 1 location – or 6 engineers at 2 locations – at the very least**. Having enough engineers guarantees each will have an acceptable workload without leading to exhaustion.
	- **Every engineer may deal with 2 incidents per shift at the most**. The actual solving of the problem may only take a few minutes but the full resolution process takes longer since it includes the post-mortem analysis, its review, establishing a set of actions, entering them the ticketing system, and so on. Thus, on average engineers cannot take care of anything else in one shift.
	- **Every event is subjected to a post-mortem analysis**.
	- **The purpose of these port-mortem analyses is not to blame people as they do not focus on people**: they focus on the process and the technology since in most cases when something goes wrong the problem usually lies in the system, the process, the environment or the technology stack
	- The error budget is 1 - availability: if availability is 99.99% error budget is 1-99.99% or 0.01% in a month that is 4.3 minutes. An error budget is used for deployment or something else that causes downtime.
	- Three kinds of monitoring: Alerts, Tickets, and Logs. Act immediately, take your time, look at every once in a while.
- [SRE Anti-patterns in everyday life and what they teach us](https://research.google/pubs/sre-antipatterns-in-everyday-life-and-what-they-teach-us/)
	- https://www.youtube.com/watch?v=51_ITZcoVu8
	- Antipatterns? There is a lot to learn when things go wrong. SRE looks at the wreckage and determines the cause of failure.
	- Users should never notice an outage before you do.
	- Engineer solutions to eliminate classes of errors rather than creating point fixes.
	- Don't feed the machine with human toil.
	- Failure is an opportunity to improve.
	- Busy coffee shop with one server. When asked if they need more staff they say everything is fine.
	- If you don't meet customer expectation they will leave: Set and meet SLOs aligned with customer expectation.
	- Overloading the system leads to failure: avoid cascading failure.
	- Remember People over software: if your people burnout your system will fail and not recover.
	- Provision adequately.
	- MTTD MTTR, mean time to detect, mean time to resolve.
- [How to "SRE" a Travel Emergency](https://www.sidewalksafari.com/2018/12/sre-in-a-travel-emergency.html)
- [Site Reliability Engineering; that’s music to my ears! SRE@bol.com](https://techlab.bol.com/site-reliability-engineering-thats-music-to-my-ears/)
- [[Do it. Do it again. Then do it again.]]
	- Use the ticketing system to track and group errors into types and strive to define and resolve error classes.
- [[4 DevOps Anti-patterns That Lead to Disaster]]
	- The Hero: this can also be described as siloing or compartmentalizing. Avoid this by doing better management of scope and deadlines and expectations, by doing a risk analysis so the mitigation of error can be collectivized and not stuck in the head of one or a few people.
	- The CB: CB stands for continuous build. When implementing CI without doing unit testing CB may occur. Avoid this by implementing a way to regularly check the codebase. This is a legacy app problem.
	- The DevOps Silo: When a DevOps team is created they become a third silo between dev and ops. Avoid this by integrating devops into ops.
	- Selective Automation: When a owner of an application does not want automation in their area for whatever reason. This turns that application into a bottleneck and that issue is not the problem of that team. Avoid this by automating based on specific criteria with a focus on the highest ROI.
- [Postmortem Culture: Learning from Failure](https://sre.google/workbook/postmortem-culture/)
	- Postmortems should be blameless. Blame incentivizes actors to sweep issues under the rug.
### II: SLO is a Proxy for Customer Happiness
---
SRE is about achieving the appropriate levels of reliability. Have an SLA for your service. Measure and report performance against and SLA. Use Error Budgets and gate launches on them.
#### Summary
*SLO* SLIs measure over time: percentiles 99.9%, 99.99%, 99.999%
*SLA* are defined by some external party and should be after an SLO is reached. If an alert triggers when something goes below 90% the SLA should be a lower percent than that.
*SLI* is a ratio/proportion + how: 50 http requests out of 100 = 50%
#### Videos
- [SLI/SLOs Deep Dive with](https://www.youtube.com/watch?v=dplGoewF4DA)
#### Case Studies
- [Home Depot](https://sre.google/workbook/slo-engineering-case-studies/)
- [Kudos Engineering](https://youtu.be/KmVDkBmnb4U?t=63)
- [Google Cloud SLAs](https://cloud.google.com/terms/sla)
- [AWS SLAs](https://aws.amazon.com/legal/service-level-agreements/)
- [Azure SLAs](https://azure.microsoft.com/en-us/support/legal/sla/summary/)
#### Articles
- [SLOs 101: How to establish and define service level objectives](https://www.datadoghq.com/blog/establishing-service-level-objectives/)
- [I want all the 9s….. in my SLO](https://www.youtube.com/watch?v=KhJbbrKy1pw&t=2268s)
- [SLO is the proxy for Customer Happiness Evernote’s SLO Story (Case Study)](https://sre.google/workbook/slo-engineering-case-studies/)
- [The Home Depot Case Story VALET](https://sre.google/workbook/slo-engineering-case-studies/)
- [ERROR BUDGET Practical application when 3rd party software is involved](https://youtu.be/uBbE8HTXbaw?t=882)
### III: Building Secure and Reliable Systems
---
*Security* and *Reliability* should be a primary focus of an application. It is the role of an SRE to ensure that Security and Reliability are part of the development process.
#### Summary
Use these articles and case studies to see examples of implementations of secure practices 
#### Videos
- [Building Secure & Reliable Systems with Heather Adkins](https://youtu.be/0LlBmPW3F1c?t=690)
- [SRE Classroom: Design a Distributed System in One Hour](https://www.youtube.com/watch?v=bOXkgMuVuYY)
#### Case Studies
 - [[Chrome Security Team]]
#### Articles
- [Introducing Non-Abstract Large System Design](https://sre.google/workbook/non-abstract-design/)
- [[Intersection of Reliability and Security]]
- [[How Do You Design a Changing Landscape]]
- [Varonis: Data Privacy and Security](https://www.varonis.com/blog/data-privacy)
- [Clarifying Containers, Microservices, and Kubernetes](https://www.devopsinstitute.com/clarifying-containers-microservices-and-kubernetes-with-tracy-ragan-of-deployhub-e10/)
### IV: Full-Stack Observability
---
#### Summary
#### Videos
- [OpenTelemetry](https://www.hashicorp.com/pt/resources/hashicast-episode-18-opentelemetry)
#### Case Studies
- [How Planet Migrated 7+ PB and 40k Machines to GCP in 90 days](https://www.youtube.com/watch?v=5aNeNhKNlUM&t=1283s)
#### Articles
- [A Collection of Best Practices for Production Services](https://sre.google/sre-book/service-best-practices/)
- [Differences Between Synthetic Monitoring and Real User Monitoring](https://stackify.com/rum-vs-synthetic-monitoring/)
- [Instrumenting Observability: Getting Started and Pro Tips](https://www.youtube.com/watch?v=pY44UX8j4Pc)
- [Observability — A 3-Year Retrospective](https://thenewstack.io/observability-a-3-year-retrospective/)
- [Monitoring and Observability — What’s the Difference and Why Does It Matter?](https://thenewstack.io/monitoring-and-observability-whats-the-difference-and-why-does-it-matter/)
### V: Platform Engineering and AIOPs
---
#### Summary
#### Videos
#### Case Studies
#### Articles
### VI: SRE & Incident Response Management
---
#### Summary
#### Videos
#### Case Studies
#### Articles
### VII: Chaos Engineering
---
#### Summary
#### Videos
#### Case Studies
#### Articles
### VIII: SRE is the Purest form of DevOps
---
#### Summary
#### Videos
#### Case Studies
#### Articles


