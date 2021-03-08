# The Great 2021 Pork Hackathon Series - Part 1: Shipping
---------------------------------------------------------
The "goal" of this hackathon series is to improve interoperabilty among systems in the pork industry.  Part 1: Shipping is targeted at creating a communication channel to streamline communication during pig shipping.  The history and motivation of how we reached this point is beyond the scope of this document.  This document is targeted primarily at technical background and specs for the hackathon itself.

The goal of the hackathon is the creation of standard, open source "integration" layer between systems.  In other words, the farmer has their own private data platform, the trucking company can have their own private data platform, and the processor can have their own private data platform.  Interoperability means a common API by which these platforms can communicate without requiring the data owners themselves to do any more work than provide permission for data connections.  **Creating, demonstrating, and publishing the API by which these systems communicate is the main goal of the hackathon.**

The [OATS Center at Purdue](https://oatscenter.org) has developed a framework for building these integration layers over the last several years.  It goes by two names: the [Open Ag Data Alliance (OADA)](https://github.com/oada/oada-docs) and the [Trellis Framework](https://github.com/trellisfw).  OADA has become the name of the underlying tech stack and Trellis represents the tooling, services, and apps built on top of OADA for connecting data across platform boundaries.  

At it's core, OADA is an API specification focused on provides synchronizability between platforms.  Any platform can be "OADA-conformant" if they implement that API specification alongside their existing API.  OADA provides a reference implementation of that API specification which you can install and run (requires Docker) at (https://github.com/oada/oada-srvc-docker).  It is possible to build an OADA "facade" over top of an existing API in order to make it OADA-conformant.  However, since OADA is designed to facilitate caching and synchronization, and digital storage is cheap, it is usually simplest to achieve OADA conformance by just installing the reference implementation and writing a service to keep a cache of you internal data platform in sync with your OADA deployment.  **This hackathon will assume that the farmer, trucking company, and processor each have their own data platform, and those platforms are all OADA-conformant.**

# Introduction to OADA/Trellis
---------------------------------
OADA is designed to be agnostic to data formats.  This means that in order to have an OADA-conformant API for any application in any industry, you only need to come up with a set of schemas for your data and you get an API for free.  To understand this, 
