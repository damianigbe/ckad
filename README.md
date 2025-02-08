# ckad
CKAD Certification preparation

Objectives
This course prepares students for the CKAA Certification.

Who is this for ?
This course is for someone who has already taken docker fundamentals course/has equivalent knowledge, and would like to learn how to extend it to orchestrate container deployments at production scale with kubernetes.
If you are a Operations/Systems personnel and would like to learn how scalable, fault tolerant and high available infrastructures are built on or off cloud to orchestrate container based deployments, this course it for you.
If you are a developer and would like to learn how to deploy your application stacks in production, on top of scalable, highly available and leverage features provided by kubernetes, this course is for you.
If you are a QA, and if your organization has a staging/QA environment built on kubernetes, and you would like to understand how to leverage it for setting up automated test workflow and learn the primitives offered by kubernetes, this course is for you.
You could be developer/operations personnel and be in charge of securing application infrastructure and setup auxiliary services such as monitoring, centralized logging etc. this course is for you.

Who is this not for ?
If you are a advanced user of Kubernetes already, this course is definitely not for you.
If you are looking to start with Kubernetes concepts, this course is not for you as it assumes basic understanding of Kubernetes and COEs. There is a free resource Kubernetes Quick Dive which you should go through before attending this workshop.
If you are interested in learning docker/container orchestration on windows, this course is not ideal for you as it focuses on linux containers.
If you expect to learn a technology which is mainly driven through a GUI, this course is not suitable for you. Using kubernetes requires you to write YAML specifications, use command line tools etc.
What will you do as part of this course ?
As part of this course you will,

Design a scalable, resilient, secure solution for deploying microservices application stack in production using primitives offered by kubernetes.
Install and configure a simple(non HA) multi node kubernetes cluster with kubeadm. You would also have a conceptual understanding of how to build a production quality cluster with high availability, scalability, redundancy and security considerations.
Learn how applications from a microservices stack interconnect with services as well as expose public facing services with various options including nodeport, externalIP, ingress etc.
Achieve Continuous Deployment with different release strategies such as Zero Downtime, Blue/Green, Canary etc.
Learn how to manage persistent storage in a kubernetes environment
Learn about the network primitives including CNI plugins, service networking and iptables, ingress etc.
Deploy an application which is auto scalable, high available, and resilient to failures.
What is not covered ?
Even though this course covers many concepts related to kubernetes, since its a very vast topic, it still has the following areas uncovered.

Advanced RBAC
Cloud specific provisioning and integration
HA deployment of a kubernetes cluster with multi masters
In depth kubernetes administration
Writing Microservices Applications
Alternate container runtimes e.g. rocker/rkt, runc
Pre Requisites
Following are the pre requisite skills to attend this course. Since its a beginner level course, no prior experience with linux containers is assumed.

Courses
You should have attended the following course, or have demonstrable knowledge with the topics included in the following course.

Docker Fundamentals
Pre Assessment test will be conducted at the beginning of the course to asses the skills.

Skills
Docker Basics
Running Containers
Building Images and writing Dockerfiles
Docker Compose
Docker Networking and Storage
Linux/Unix Systems Fundamentals
Familiarity with Command Line Interface (CLI)
Fundamental knowledge of editors on linux (any one of vi/nano/emacs)
Understanding of YAML syntax and familiarity with reading/writing basic YAML specifications
Hardware and Software Requirements
These are the prerequisites for each attendee.

Hardware Requirements	Software Requirements
Laptop/Desktop with high speed internet connection	Base Operating System : Windows / Mac OSX
8 GB RAM	VirtualBox
4 CPU Cores	Vagrant
20 GB Disk Space available	ConEmu (Windows Only)
Git for Windows (windows only)
Lab Setup : Instructions can be found at xxx

Supporting Content/Materials
Following is the supporting material which will be provided to you before/during the course

Slides (online)
Workshop (online link)
Video Course - XXX by School of Devops
Pre Class Checklist
All participants should have completed the following checklist before attending the course .

Successfully Completed Docker Fundamentals Course, or have equivalent skills.
Verify your system meets the hardware pre requisites.
Validate the setup : verify all pre requisite software is installed on your system and is functional.
Join our kubernetes channel on gitter
