![PorkHack21 - Part 1: Shipping](./docs/img/porkhack-logo-landscape-large.png)

# The Great 2021 Pork Hackathon Series - Part 1: Shipping
---------------------------------------------------------

1. [Overview](#overview)  
1. [Hackathon](#hackathon-final-demo)
    1. [Teams](#teams)
    2. [Contingency Planning](#contingency-planning)
    3. [Hackathon Setup and Logistics](#hackathon-setup-and-logistics)  
1. [Introduction to OADA and Trellis](#introduction-to-oada-and-trellis)
    1. [Installation and Setup](#installation-and-setup)


# Overview
--------------
The goal of this hackathon series is to improve interoperabilty among systems in the pork industry.  Part 1: Shipping is targeted at creating a communication channel to streamline communication during pig shipping.  The history and motivation of how we reached this point is beyond the scope of this document.  This document is targeted primarily at technical background and specs for the hackathon itself.

We are creating a standard, open source "integration" layer between systems.  In other words, the farmer has their own private data platform, the trucking company (hauler) can have their own private data platform, and the processor can have their own private data platform, and the apps and other software that serve each.  

>**Creating, demonstrating, and publishing the API by which these systems communicate is the main technical goal of the hackathon.**


The [OATS Center at Purdue](https://oatscenter.org) has developed a framework for building these integration layers.  It goes by two names: the [Open Ag Data Alliance (OADA)](https://github.com/oada/oada-docs) and the [Trellis Framework](https://github.com/trellisfw).  OADA has become the name of the underlying tech stack and Trellis has come to represent the tooling, services, and apps built on top of OADA for connecting data across platform boundaries.  

>**This hackathon will assume that the farmer, hauler, and processor each have their own data platform, and those platforms are all OADA-conformant.**

Because it is open source and easily installable, anyone, including any participant company in the hackathon, can offer an OADA-conformant platform as a service for their customers.

# Hackathon Final Demo
------------------
The hackathon will end with a demo of as many end-to-end features as we can pack into the 3 days.  Brainstorming and open innovation is strongly encouraged, however to provide some structure upon which to build, a base storyline for the target demo is:

Step | Nickname | Actors | Description
-----|----------|--------|------------
1 | Schedule | Farmer | **Farmer** contacts processor to schedule shipment.
2 | Create ASN | Processor | **Processor**'s app creates an Advance Ship Notice (ASN) JSON document that ends up at farmer's platform.
3 | Add Parties | &lt;Any&gt; | **Farmer** or **Processor** adds **Hauler** to ASN once identified and booked, hauler's platform receives ASN.
4 | PQA/TQA | Farmer, Hauler | **Farmer** and **Hauler** add proof of Pork Quality Assurance (PQA) and Transport Quality Assurance (TQA) certification.
5 | More Certs | &lt;Any&gt; | Any additional info identified by hackathon participants can also be added to ASN from any integrated software platforms.
6 | Ship Pigs | Farmer | When it's time to ship, the **Farmer** loads pigs on a truck. 
6a | Chatbot - Ship | Farmer | **Farmer** can text a chatbot provided by a hackathon partner to indicate load has left and # head.
6b | WebApp - Ship | Farmer | **Farmer** can use web app to indicate load has left and # head.
7 | EnRoute | Hauler | **Hauler** uses mobile web app to update location mid-route, **Farmer** and **Processor** can monitor.
8 | Arrival | Hauler | **Hauler** uses mobile web app to indicate arrival in ASN.
9 | Receive Pigs | Processor | Pigs are unloaded from truck and counted.  **Processor** uses mobile app to record received head count in ASN.
10 | Arrival - Notify | Farmer | As a result of processor's write to the ASN, the **Farmer** is notified (via txt chatbot, email, or within mobile web app) of arrival and matching/non-matching head count.
11 | Grade Sheet | Processor, Farmer | After slaughter, **Processor** adds JSON version of kill sheet for the load (or collection of loads) to the ASN and **Farmer** receives.

## Teams
------------------
Participants can decide which teams should form in order to achieve the shared goal. As a suggestion, the outline above seems to have 4 types of teams based on tech stack/skill set:

1: **Web Apps Team** (can have separate "farmer", "processor" and "trucker" teams if enough people)
This team would create in-browser, open source web app(s), hostable via Github pages, to enable interaction with the ASN for the farmer, trucker, and processor.

2: **Chatbot Team**
This team would focus on the text-message-based experience for the farmer to simplify day-of shipping activities.

3: **Certifications Team**
This team will focus on constructing appropriate means to privde PQA,TQA certifications and any other certifications that are available, ideally via OATS AGAPECert automated certifications framework.

4: **Sharing and Sync Team**
This team will build the microservice(s) that perform the 2-way sync between the farmer's platform, the hauler's platform, and the processor's platform.

5: **Integrations Team**
This team will focus on integrating any of this process with existing apps or services.  For example, if a company's existing UI can already make use of some form of shipping records, they could create a microservice to keep their internal api/database in sync with their customer's list of ASN's and then get a great chance to show their platform's features.

## Contingency Planning
------------------------------
In order to maximize team independence and modularity, it is critical that the Shaing and Sync Team's work not hinder any of the other team's work.  Trellis has a convenient means of achieving this which allows us to have a Plan A (everything works as planned) and a Plan B (some things don't work) and Plan C (only one thing works).  In essence, all apps and services (except the sharing service) will see the same API regardless of which platform and which user they are operating as.  This will manifest itself as follows:

![Plan C](./docs/img/PlanC.png)
Plan C is the simplest of all the plans.  Just write any app/service using the standardized ASN API and have them all use the same user/token.  If the sharing team isn't done yet, or they don't get done, this works regardless because everybody is editing the same docs as the same user.

![Plan B](./docs/img/PlanB.png)
Plan B adds an indexer so that only a subset of the Farmer's ASN's are shared with separate users on his/her platform.  The Hauler and the Processor will have a user on the farmer's platform, and the farmer's internal indexer will handle only sharing ASN's with the hauler and processor that they should be allowed to see.  The sharing is done as an internal link, meaning that all the users are editing the same underlying resource, but the farmer can now have ASN's for different haulers and processors that remain private.

From a coding perspective, this just means the apps from Plan C need to use different tokens, but they still work the same.

![Plan A](./docs/img/PlanA.png)
Plan A is the primary goal.  The hauler and processor will run their own 2-way sync services that will maintain consistency between the ASN copies on all the respective platforms.  The Hauler's app(s) will interact only with the ASN copy on the hauler's platform, the Processor's app(s) will interact only with the ASN copy on the processor's platform, and the farmer's app(s) will interact only with the copy on the farmer's platform.  

The Hauler will have a user on the farmer's platform and a user on their own platform (and similarly for the processor).  The 2-way syncer will use a separate token for each in order to keep both copies of the ASN in sync.

From a coding perspective for all the other teams, this just means the apps from Plan B will need to change the domain they use.


## Hackathon Setup and Logistics
------------------
Each hackathon team will be provided with a virtual machine running Linux within our OpenStack cluster at Purdue.  Each team will receive a private key to use to ssh to it as needed.  That machine will have `oada` pre-installed for 3 domains: the farmer's data platform, the trucking company's platform, and the processor's platform.  For simplicity during the hackathon, we'll serve all three domains from the same machine for a given team, but they are for all intents and purposes separate platforms.

OATS students and staff will be available throughout the hackathon to answer questions and help with understanding any OATS-provided tools, libraries, or services.

The hackathon will take place in the virtual events platform [Remo.co](https://remo.co).  The main "virtual floor" is a set of tables that participants can move freely between as they have discussions.  We intend that each team will have a dedicated table, and any participants can move between teams to collaborate or ask questions.  It is also easy to have ad-hoc small discussions by just having a few people pop over to an unoccupied table.  The "virtual floor" for this hackathon will look like this:

![Remo floor](./docs/img/remo_floor.png)

Each team will elect a "speaker" who will be in charge of making a "pitch" presentation and reporting out during the hackathon on the team's activities and progress.  The organizers will be coming around to the tables to help identify areas of needed collaboration between teams, and to create 5-minute summaries of progress that the organizers will present live back at OATSCON21 throughout the event.

# Introduction to OADA and Trellis
---------------------------------
OADA is an API specification focused on data synchronization between platforms.  Any platform can be "OADA-conformant" if they implement that API specification alongside their existing API.  Any party in the pork supply chain can provide an OADA-conformant platform for any other party if needed.  OADA provides a reference implementation of that API spec which you can install and run (requires Docker) at (https://github.com/oada/oada-srvc-docker).  It is possible to build an OADA "facade" over top of an existing API in order to make it OADA-conformant.  However, since OADA is designed to facilitate caching and synchronization, and digital storage is very cheap, it is usually simplest to achieve OADA conformance by just installing the reference implementation and writing a single service to keep a cache of your internal data in sync with your OADA deployment's cache.  

OADA does not tell you what data formats you need to use, it only requires that you identify them as the `content-type` on your resources.  This means that in order to have an OADA-conformant API for any application in any industry, you only need to come up with a set of schemas for your data and you get an API spec for free.  OADA standardizes things like:
* Authorization
* Permissions
* Finding the data your app needs
* Reading/writing
* Tracking and subscribing to change notifications

Rather than provide an exhaustive narrative around learning all the features of OADA, we will instead just dive in tutorial-style to the basic setup for this hackathon.

## Installation and Setup
----------------------------
If you want to run `oada` locally for development, you need `docker`, `docker-compose`, and a non-Windows OS (Mac or Linux) w/ `bash`.  Then do this:
```bash
# Get the deployment script
mkdir oada && cd oada
curl -OfsSL https://raw.githubusercontent.com/oada/oadadeploy/master/oadadeploy && chmod u+x oadadeploy
# Install oada using defaults
oadadeploy init -y
source .oadadeploy/bash-completion
# Tell oada to serve localhost
oadadeploy domain add -y localhost
```
You can also deploy the same way in a cloud VM, however you'll need to use your own domain instead of `localhost` in the last command.

Now in order to make requests against `oada`, you'll need a token, and to get a token you'll need a user:
```bash
# Add username bob on domain localhost as an admin (who can make other users)
oadadeploy admin user add -u bob -p testpass1 -d localhost -a
# Create a token w/ scope "all:all" (i.e. that can do anything bob can do
oadadeploy admin token create -u bob -s all:all
```

Armed with your new token, you are ready to start making REST requests against OADA with a REST client like [Insomnia](https://insomnia.rest/).

## Making API requests by hand
--------------------------------

< setup script for hackathon >

## Making API requests in Javascript/Typescript
------------------------------------------------
