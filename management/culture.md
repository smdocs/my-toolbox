# Engineering Culture

1. Every service has HA/DR with RTO’s and RPO’s defined. We execute HA/DR plans on a weekly basis to ensure that there is little to no customer impact in the event of a regular incident. This is a best practice that all SaaS products need to plan and execute.

2. Heavy focus on resiliency. A service being unavailable typically does not mean that customers know about it.

3. Services are published in an internal services portal. This allows engineers to reuse services that have been constructed by other team, and reduces service clones.

4. Performance is measured in terms of TP99, TP90 and TP50 (and often higher). TP50 represents the experience that the 50th percentile of customers experience. Our goals is TP50 of 1 second and TP90 of 2 seconds. (i.e. less than 10% of customers experience page load times on any given page greater than 2 seconds).

5. Failed Customer Interactions (FCI) is another key metric we track. Each failed interaction with a customer (HTTP 4xx/5xx) is considered a failed interaction. Our goal is to have FCI’s for every service under 0.025%.

6. Service teams own services end to end. They are responsible for maintenance and uptime of the service, consistent with the devops model. PagerDuty is used to alert on-call personnel of problems and participation in recovery.

7. You cannot improve what you cannot measure. There is wide visibility into the company on our availability, performance, scalability and quality metrics via a near real-time dashboard. This is key in driving behavior in the organization.

8. We are in the process of moving to a reactive, loosely coupled system that increases our ability to scale. This however requires careful thinking about how to deal with eventual consistency in the system.

9. Engineers perform RCA’s (Root Cause Analysis) of every failure in the system. RCA’s are reviewed by Engineering leadership. We apply the 5 why’s and fish-boning to every RCA. Failures are a wonderful teacher.

10. Strong sense of code and domain ownership.

11. Mission driven. People do their best lives when they can see the impact they have on our customers.

12. No compromises on quality, performance, availability and security.

13. Continuous deployments and feature toggles enable us to deliver rapidly
