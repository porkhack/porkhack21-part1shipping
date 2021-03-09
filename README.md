# The Great 2021 Pork Hackathon Series - Part 1: Shipping
---------------------------------------------------------
The goal of this hackathon series is to improve interoperabilty among systems in the pork industry.  Part 1: Shipping is targeted at creating a communication channel to streamline communication during pig shipping.  The history and motivation of how we reached this point is beyond the scope of this document.  This document is targeted primarily at technical background and specs for the hackathon itself.

We are creating a standard, open source "integration" layer between systems.  In other words, the farmer has their own private data platform, the trucking company can have their own private data platform, and the processor can have their own private data platform, and the apps and other software that serve each.  

```
Creating, demonstrating, and publishing the API by which these systems communicate 
is the main technical goal of the hackathon.
```

The [OATS Center at Purdue](https://oatscenter.org) has developed a framework for building these integration layers over the last several years.  It goes by two names: the [Open Ag Data Alliance (OADA)](https://github.com/oada/oada-docs) and the [Trellis Framework](https://github.com/trellisfw).  OADA has become the name of the underlying tech stack and Trellis has come to represent the tooling, services, and apps built on top of OADA for connecting data across platform boundaries.  

```
This hackathon will assume that the farmer, trucking company, and 
processor each have their own data platform, and those platforms 
are all OADA-conformant.
```

# Hackathon
------------------
The hackathon will end with a demo of as many end-to-end features as we can pack into the 3 days.  Brainstorming and open innovation is obviously encouraged, however to provide some structure from which to build, a base storyline for the target demo is:
1. **Farmer** contacts processor to schedule shipment.
2. **Processor**'s app creates an Advance Ship Notice (ASN) JSON document in farmer's platform.
3. **Farmer** or **Processor** adds **Trucker** to ASN once identified and booked.
4. **Farmer** and **Processor** add some form of proof (TBD) of Pork Quality Assurance and Transport Quality Assurance certification.
5. Any additional info identified by hackathon participants can also be added to ASN from any integrated software platforms.
6. When its time to ship, the **Farmer** loads pigs on a truck. 
    a. **Farmer** can text a chatbot provided by a hackathon partner to indicate load has left and # head.
    b. or **Farmer** can use mobile web app to indicate load has left and # head.
6. **Trucker** uses mobile web app to update location mid-route, **Farmer** and **Processor** can monitor.
7. **Trucker** uses mobile web app to indicate arrival in ASN.
8. Pigs are unloaded from truck and counted.  **Processor** uses mobile app to record received head count in ASN.
9. As a result of processor's write to the ASN, the **Farmer** is notified (via txt chatbot, email, or within mobile web app) of arrival and matching/non-matching head count.
10. After slaughter, **Processor** adds JSON version of kill sheet for the load (or collection of loads) to the ASN.

## Teams
------------------
Participants can decide which teams should form in order to achieve the shared goal. As a suggestion,

## Hackathon Setup
------------------
Each hackathon team will be provided with a virtual machine running Linux within our OpenStack cluster at Purdue.  Each team will receive a private key to use to ssh to it as needed.  That machine will have `oada` pre-installed for 3 domains: the farmer's data platform, the trucking company's platform, and the processor's platform.  For simplicity, we'll serve these all from the same machine for the hackathon, but they are for all intents and purposes separate platforms.
 


# Introduction to OADA/Trellis
---------------------------------
OADA is an API specification focused on data synchronization between platforms.  Any platform can be "OADA-conformant" if they implement that API specification alongside their existing API.  OADA provides a reference implementation of that API spec which you can install and run (requires Docker) at (https://github.com/oada/oada-srvc-docker).  It is possible to build an OADA "facade" over top of an existing API in order to make it OADA-conformant.  However, since OADA is designed to facilitate caching and synchronization, and digital storage is very cheap, it is usually simplest to achieve OADA conformance by just installing the reference implementation and writing a single service to keep a cache of your internal data in sync with your OADA deployment's cache.  

OADA does not tell you what data formats you need to use, it only requires that you identify them as the `content-type` on your resources.  This means that in order to have an OADA-conformant API for any application in any industry, you only need to come up with a set of schemas for your data and you get an API spec for free.  OADA standardizes things like:
* Authorization
* Permissions
* Finding the data your app needs
* Reading/writing
* Tracking and subscribing to change notifications

Rather than provide an exhaustive narrative around learning all the features of OADA, we will instead just dive in tutorial-style to the basic setup for this hackathon.

