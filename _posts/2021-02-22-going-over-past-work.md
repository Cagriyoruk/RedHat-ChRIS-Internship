---
layout: post
author: Cagri
title: "Going Over Past Work"
---

If you've red my introduction, you might know that I was involved in ChRIS project for developing plugins as a student developer. I was assigned to build a plugin that classifies brain MRI's using convolutional neural networks and spits out a segmentation of the brain as a pdf text report with 4 other people. The project was called containerizing neural networks for medical compute and it was a semester project for EC528 Cloud Computing class. If you want to learn more about the project, here is the GitHub repo for it -> [Containerizing Neural Nets](https://github.com/BU-CLOUD-F20/Containerizing_Neural_Nets). There is extensive explanation and how to replicate and use it.

It's been around 2 months since I've done any work related to ChRIS. Therefore I decided to replicate some of the work I did in the fall. Since I've worked with ChRIS before I already had access to Mass Open Cloud and OpenShift/OpenStack platforms. I've started with deploying our most essential services `pfioh` and `pman`. `pfioh` is a service that handles files and does a push/pull operation from OpenStack swift storage. `pman` is a process manager that handles and execute jobs.

![Screenshot from 2021-03-25 12-13-05](https://user-images.githubusercontent.com/55101879/112505829-89510880-8d63-11eb-80a6-7aa382aafc86.png)

Personally I have a love hate relationship with MOC. When we build plugins or deploy new services we're so excited to test them on the cloud. When we come accross a problem that prevents us deploying/testing the applications, I can say that it's quite annoying. There are times when there's a problem with the cluster and we usually don't know if it's a problem with the services we're deploying or the cluster. Apparently, the routes of the services were down for a week, and I thought there was a problem on my end I was going over every documentation I had to solve the problem 😅. Finally I created a ticket and found out the routes were down in a few days. Meanwhile I focused on going over and fixing a documentation we had for integrating ChRIS with MOC which ultimately led me to create another documentation for MOC integration.

The documentation I used to get MOC working with ChRIS was a little bit outdated so as a person who was working closely with MOC, I updated the documentation whenever I think a change was necessary. This helped new people get onboard with ChRIS services as fast as possible. At some point, one of my mentors wanted me to create an asciidoc that can be considered as a ground truth document for MOC integration. Therefore, I've started writing this document by replicating the things I do to get ChRIS up and running. 
If working with ChRIS is something you want to do, definitely check out the document I wrote here -> [ChRIS MOC Integration](https://github.com/Cagriyoruk/CHRIS_docs/blob/master/usecases/MOC_integration/moc_integration.adoc)
I'm finishing this post with sharing the beginning of the documentation I wrote. Thanks for reading 😊

![Screenshot from 2021-03-25 12-53-55](https://user-images.githubusercontent.com/55101879/112511908-3da15d80-8d69-11eb-9314-5fd5bb3579c0.png)
