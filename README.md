# The Great 2021 Pork Hackathon Series - Part 1: Shipping
---------------------------------------------------------
The "goal" of this hackathon series is to improve interoperabilty among systems in the pork industry.  Part 1: Shipping is targeted at creating a communication channel to streamline communication during pig shipping.  The history and motivation of how we reached this point is beyond the scope of this document.  This document is targeted primarily at technical background and specs for the hackathon itself.

The goal of the hackathon is the creation of standard, open source "integration" layer between systems.  In other words, the farmer has their own private data platform, the trucking company can have their own private data platform, and the processor can have their own private data platform.  Interoperability means a common API by which these platforms can communicate when given permission by the data owner.  **Creating, demonstrating, and publishing the API by which these systems communicate is the main goal of the hackathon.**

The [OATS Center at Purdue](https://oatscenter.org) has developed a framework for building these integration layers over the last several years.  It goes by two names: the [Open Ag Data Alliance (OADA)](https://github.com/oada/oada-docs) and the [Trellis Framework](https://github.com/trellisfw).  OADA has become the name of the underlying tech stack and Trellis has come to represent the tooling, services, and apps built on top of OADA for connecting data across platform boundaries.  

At it's core, OADA is an API specification focused on provides synchronizability between platforms.  Any platform can be "OADA-conformant" if they implement that API specification alongside their existing API.  OADA provides a reference implementation of that API spec which you can install and run (requires Docker) at (https://github.com/oada/oada-srvc-docker).  It is possible to build an OADA "facade" over top of an existing API in order to make it OADA-conformant.  However, since OADA is designed to facilitate caching and synchronization, and digital storage is very cheap, it is usually simplest to achieve OADA conformance by just installing the reference implementation and writing a service to keep a cache of your internal data in sync with your OADA deployment's cache.  

**This hackathon will assume that the farmer, trucking company, and processor each have their own data platform, and those platforms are all OADA-conformant.**

# Hackathon Shared Goal
------------------
The hackathon will end with a demo of as many end-to-end features as we can pack into the 3 days.  A base storyline for the target demo is:
1. **Farmer** contacts processor to schedule shipment.
2. **Processor**'s app creates an Advance Ship Notice in farmer's platform.
3. When its time to ship, the **farmer** loads pigs on a truck.  **Farmer** uses a mobile web app to indicate # head and that load has shipped.
4. Trucker company uses mobile web app 

## Teams
------------------
Participants can decide which teams should form in order to achieve the shared goal. As a suggestion,
## Hackathon Setup
Each hackathon team will be provided with a virtual machine running Linux that you can ssh into.  That machine will have `oada` pre-installed for 3 domains: the farmer's data platform, the trucking company's platform, and the processor's platform.  For simplicity, we'll serve these all from the same machine for the hackathon, but they are for all intents and purposes separate platforms.
 


# Introduction to OADA/Trellis
---------------------------------
OADA does not tell you what data formats you need to use, it only requires that you identify them as the `content-type` on your resources.  This means that in order to have an OADA-conformant API for any application in any industry, you only need to come up with a set of schemas for your data and you get an API spec for free.  OADA standardizes things like:
* Authorization
* Permissions
* Finding the data your app needs
* Reading/writing
* Subscribing to changes

Rather than provide an exhaustive narrative around learning all the features of OADA, we will instead just dive in tutorial-style to the basic setup for this hackathon.

