---
title: Remembering the Users in Cloud Transformations
description: Far too often, users aren't the primary focus of cloud-based transformations. It's important that at the end, they aren't left wondering what it was all about.  
author: greglow-sdu
ms.author: mlcc-owners
ms.service: azure
ms.topic: concept-article
ms.date: 09/24/2023
ms.custom: template-concept
contributor-type: community
content_well_notification: 
  - Human-created-Community
---

# Remembering the Users in Cloud Transformations

---

**Principal author**: [Dr Greg Low](https://learn.microsoft.com/users/greglow/)

---

IT teams drive most cloud transformations. It's obvious from the outcomes. Far too often, the impact on users isn't a primary focus. It's critical to consider the impacts both during and after the work is complete. It's important that at the end, they aren't left wondering what the pain was all about.

## Transformations vs Migrations
I deal with a variety of organizations. At one end of the spectrum,  I have startups. At the other end, I have tier one financials. Some of the financials that I work with are banks. Most of my work in the financial area, is around what we call superannuation companies. In other countries they're called retirement fund companies.

I've seen a common pattern happen in these companies in recent years. 

We hear one day that the CTO or CIO that has worked for the company for a long time is leaving. Their replacement is someone who talks about making a cloud transformation. The boards in the organization love hearing this. That's despite them not understanding what's involved and it sounds buzz-word compliant. Everyone in their business circles are doing the same. 

It sounds good, but what happens in most cases, is that what they make is a cloud migration. they don't make a transformation at all. Far too often, they take their existing mess and migrate it to the cloud.

## The Usual Outcome
So, several issues occur when you do this.

The first issue is that the migration process takes much longer than planned for. Things end up being far more complicated than expected. Often, they're trying to replicate in the cloud, the same things that they were doing before. But making a move to the cloud isn't only about moving datacenters. 

The second issue is that they decide to follow a pattern of:
* migrate to the cloud first
* transform when we get there.

I know that cloud vendors push this all the time. Their aim is to get users into the cloud as fast as possible, to start billing for their services.

What happens time and again, is that by the time the migration limps over the line, there is no energy left. The company has endured enormous pain and disruption. The project took far longer than they planned. And it cost far, far more than they planned. Worse, they now have much higher ongoing costs than expected.

There is then zero energy or budget left for transformation. Yet that's where the real benefit should have come from. The only upside might be in cases where their infrastructure was so old. They have been holding off upgrading it. 

## Applications
The teams are often surprised by how much worse their applications now perform. These are applications they relied on and thought would be ok. 

We do a large amount of work, trying to get applications working well in cloud environments. 

There are many reasons for this. The most common issue is that applications are very chatty. They endlessly communicate with databases and with other services. As soon as you introduce latency into those connections, there is a problem. 

As an example, I've recently done work for a bank. They had an overnight process that ran for 6 hours on-premises. They moved it to the cloud. It now took more than 12 hours. What's worse, is that they were providing double the on-premises resources
 
There are many such scenarios. There's no guarantee that what you had before will will be well suited to run well in the cloud. 

## But what about the users?
The most important aspect of this for me, is the business users. 

Far too often, all they see is a massive disruption to their lives and to the systems they use. And in the end, if they're lucky, the systems are back doing what they were doing before the migration.

The users are wondering: "What was all that about?"

They don't see any benefit. If they had old systems before, they might be more reliable now. Often though, they're worse off than where they started. Their systems could be more complex to use than before. 
   
## Getting the users onside
If you want your users onside when making a move to the cloud, it's critical that you bring them with you. Spend time early on in the process, thinking about a bigger picture, and ways that you can make your users smile. Tackle something they will notice and appreciate.  

Transformations deliver the true benefits about moving to the cloud. They are also what the users will notice, and are far too important to leave until a second phase happens (if ever). 

## Next steps
Moving to the cloud is not about moving virtual machines between hosting environments. An important first step is to consider the use of Platform as a Service (PaaS) offerings. Consider how you could make use of PaaS offerings. 

[What is PaaS?](https://azure.microsoft.com/resources/cloud-computing-dictionary/what-is-paas)
