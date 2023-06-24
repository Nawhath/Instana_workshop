# Instana Labs

Time: 9:30am to 5:00pm (Lunch 12noon to 1pm)

Lab Information:

- IBM Box:

  - Please accept box invitation.
  - Box link: [https://ibm.ent.box.com/folder/214139504216](https://ibm.ent.box.com/folder/214139504216)
    - Contain access information to your respective VMs. (Target and Server VM)
      - "Target" is the VM to be monitored by Instana. It is also called Managed-To VM.
      - "Server" is the VM for Instana backend. It is also called Managed-From VM.
    - Also contain two spreadsheets "List of Attendee_Instana Workshop June.xlsx" and "Labs progress.xlsx".

- Lab material: [https://github.com/yangkwang/Instana_workshop](https://github.com/yangkwang/Instana_workshop)

- Please check your trainee’s identifier (ID) in "List of Attendee_Instana Workshop June.xlsx"

- Please update your lab progress in "Labs progress.xlsx". Mark "Done" for the lab completed.

# Day 1
Lab introduction - 30 min
  - Self Introduction - 15 min
    - Name and Company
    - Your role
    - Expectation from the workshop
  - Lab Enviroment - 10 min
    - [IBM Box](https://ibm.ent.box.com/folder/214139504216)
    - [IBM Techzone](https://techzone.ibm.com/my/workshops/view/646b057541e9fa001716ddf4https://techzone.ibm.com/my/workshops/view/646b057541e9fa001716ddf4)
  - End Goal - 5 min
    - Demo environment
    - Stand and deliver

Instana Overview - 30 min

Play with Instana - 30 min

**Preparation**

- Prepare the Managed-To VM - 60 min
  - [ssh-with-putty.md](https://github.com/yangkwang/Instana_workshop/blob/main/preparation/ssh-with-putty.md) (For Window user)
  - [manage-to-on-ubuntu.md](https://github.com/yangkwang/Instana_workshop/blob/main/preparation/manage-to-on-ubuntu.md)

Lunch - 60 min

Instana Architecture and Deployment model - 60 min

**Lab 1.1**

- Install Instana Backend (Server) - 60 min
  ([Lab 1.1 Instructions](https://github.com/yangkwang/Instana_workshop/blob/main/part-1/lab-part1.md#lab-11--install-instana-server-manually))

Tea break - 30 min

- Self monitoring - 60 min
  ([Lab 1.1 Instructions](https://github.com/yangkwang/Instana_workshop/blob/main/part-1/lab-part1.md#81-install-agent-for-instana))

Question and feedback - 30 min

# Day 2
Instana UI - 30 min

Concept of End User Monitoring (EUM) - 15 min

**Lab 2.1** - 15 min
- Familiarize with Instana UI
  ([Lab 2.1 Instructions](https://github.com/yangkwang/Instana_workshop/blob/main/part-2/lab-part2.md#lab-21--a-quick-tour))

**Lab 2.2** - 30 min
- Setup Website monitoring on Robot-Shop
  ([Lab 2.2 Instructions](https://github.com/yangkwang/Instana_workshop/blob/main/part-2/lab-part2.md#lab-22--website-monitoring))

**Lab 2.3** - 60 min
- Deploy agents to monitor the Kubernetes cluster and Linux servers.
  ([Lab 2.3 Instructions](https://github.com/yangkwang/Instana_workshop/blob/main/part-2/lab-part2.md#lab-23--install--manage-agents))

Lunch - 60 min

What is Application Perspective? - 15 min

Transaction tracing - 30 min

Run through Application monitoring - 30 min

**Lab 2.4** - 30 min
- Configure application monitoring for Robot-Shop
  ([Lab 2.4 Instructions](https://github.com/yangkwang/Instana_workshop/blob/main/part-2/lab-part2.md#lab-24--application-monitoring))

- Run through Infrastructure monitoring - 30 min

Tea break - 30 min

**Lab 2.5** - 60 min
- Infrastructure monitoring
  ([Lab 2.5 Instructions](https://github.com/yangkwang/Instana_workshop/blob/main/part-2/lab-part2.md#lab25--infrastructure-monitoring))

Question and feedback - 30 min

# Day 3

Let's talk about Event, Analytics and how to troubleshoot a problem - 30 min

How notifications are sent? - 30 min

**Lab 3.1** - 60 min
- Try out and get familiarize with Instana eventing mechanism.
  ([Lab 3.1 Instructions](https://github.com/yangkwang/Instana_workshop/blob/main/part-3/lab-part3.md#lab-31--events-analytics-and-troubleshooting))

**Lab 3.2** - 30 min
- Try out and get familiarize with how alert is notified.
  ([Lab 3.2 Instructions](https://github.com/yangkwang/Instana_workshop/blob/main/part-3/lab-part3.md#lab-32--alerts--channels))

Lunch - 60 min

Introduce Service Level Objective (SLO) and how it is used in Instana. - 15 min

**Lab 3.3** - 30 min
- Try out the configuration of SLO.
  ([Lab 3.3 Instructions](https://github.com/yangkwang/Instana_workshop/blob/main/part-3/lab-part3.md#lab-33--slo-monitoring-with-custom-dashboard))

Introduce RBAC - 15 min

**Lab 3.4** - 30 min
- Create group and understand how users are invited.
  ([Lab 3.4 Instructions](https://github.com/yangkwang/Instana_workshop/blob/main/part-3/lab-part3.md#lab-34--rbac--user-onboarding))

How to create custom metrics? - 15 min

**Lab 3.5** - 30 min
- Create custom metrics and display them in dashboard as well as event rules.
  ([Lab 3.5 Instructions](https://github.com/yangkwang/Instana_workshop/blob/main/part-3/lab-part3.md#lab-34--rbac--user-onboarding))

Tea break and preparation for Stand and Delivery - 30 min

- Stand and Delivery - 90 min
  - Group of 2
    - Instana Overview and Demo using your setup


---
> After the workshop please acquire and complete your IBM Instana Technology badges:
[IBM Instana Technology badges](https://learn.ibm.com/course/view.php?id=9927)

> Leverage IBM Techzone tool for tests and POC environments.
[IBM Techzone](https://techzone.ibm.com/)

