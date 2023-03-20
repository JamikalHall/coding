---
title: "Intro to Pipeline Automation"
date: 2023-02-04T10:03:48-06:00
draft: false
tags:
    - DevOps
    - Notes
    - TryHackMe
---

In my last post, I built a simple pipeline to automatically update my website whenever I make a push to my Git repository. In this post, I am posting the notes I have taken from [TryHackMe's](https://tryhackme.com/room/introtopipelineautomation) room on pipeline automation. I am not going to copy verbatim, rather summarize the notes in my own words.

**Learning Objectives**

This room will teach you about the following concepts:

- Introduction to the DevOps pipeline
- Introduction to DevOps tools and automation
- Introduction to security principles for the DevOps pipeline

A pipeline likely consists of the following:
1. Source Code Storage/Version Control
2. Continuous Integration
3. Environments
4. Automated Testing
5. Dependency Management


Question: Where in the pipeline is our end product deployed?
Answer: Environment

## Source Code and Version Control
The start of a pipeline, as you will need somewhere to store your code as well as have version history since you are constantly adding features and improvements. What if you have some code that introduces a bug? With version history you could roll the codebase back to before the bad code was introduced.

Two Common Tools: Git and Subversion(SVN). Git is a distributed source control tool, so each developer would get their own copy of the source code(i.e. cloning from a repo). SVN is centralized, meaning the repo is centrally managed somewhere.

Secret Management is important. The source cannot obfuscate everything, so plaintext credentials should never be stored in your code.

Question: Who is the largest online provider of Git?
Answer: Github

Question: What popular Git product is used to host your own Git server?
Answer: Gitlab

Question: What tool can be used to scan the commits of a repo for sensitive information?
Answer: GittyLeaks

## Dependencies

In development, a lot of the heavy lifting is done through libraries and SDKs, and a dev's job is usually to make sure everything works well together. It is important to manage these dependencies as if there is a security risk in one, your entire application could be at risk. 

Developing internal software has risks because its up to the company to maintain it; this can cause problems if the code is widely used within the codebase and not frequently updated for security. Using external libraries can be dangerous too, as you must investigate to make sure that this third party library is secure.

Question: What do we call the type of dependency that was created by our organisation?
Answer: Internal

Question: What type of dependency is JQuery?
Answer: External

Question: What is the name of Python's public dependency repo?
Answer: PyPi

Question: What dependency 0day vulnerability set the world ablaze in 2021?
Answer: Log4j

## Automated Testing

Testing is vital to ensure the application runs smoothly. In the past, testers would have to manually document every test case, however now, automated testing within pipelines can handle a good bit of this.
**Unit Testing** - A unit test is a test case for a small part of the application or service. The point is to test a small portion of the code to make sure the code works properly.
**Integration Testing** - Integration testing focuses on how the code works with other parts of code. Another part of integration testing is regression testing, which makes sure that new features do not negatively impact existing features.
**Security Testing** - comes in two main flavors:
1. SAST - Static Application Security Testing scans the source code for security vulnerabilities.
2. DAST - Dynamic Application Security Testing tests by actually running the code; some vulnerabilities cannot be detected by just analyzing the source code.

There is also the traditonal *Penetration Testing* which is just manually testing the application for vulnerabilities. Though security tools have advanced rapidly, it is still important because some vulnerabilities require context, such as exploiting business logic.

Question: What type of tool scans code to look for potential vulnerabilities?
Answer: SAST

Question: What type of tool runs code and injects test cases to look for potential vulnerabilities?
Answer: DAST

Question: Can SAST and DAST be used as a replacement for penetration tests?
Answer: No

## CI/CD

Continuous Integration and Continuous Deployment is the process of rapidly making improvements and add features to an application to deliver constant value. The pipelines typically have these elements:
 - Starting trigger - what begins the entire process, such as a push to a certain branch or repo.
 - Building Actions - actions taken to build the application along with the new feature
 - Testing Actions - testing to ensure the application works as expected, without ruining existing features
 - Deployment Actions - once the builds complete, and testing passes, what should happen after, i.e. the build should be pushed to a testing environment/branch
 - Delivery Actions - now that the build is deployed, we add monitoring or other actions.

Question: What does CI in CI/CD stand for?
Answer: Continuous Integration

Question: What does CD in CI/CD stand for?
Answer: Continuous Delivery

Question: What do we call the build infrastructure element that controls all builds?
Answer: build orchestrators

Question: What do we call the build infrastructure element that performs the build?
Answer: build agents

## Environments

Most pipelines have several environments. Common ones include:

**Dev (Development)** - The playground for developers to add code and test features. Likely, the security controls are the most lax, but with proper segmentation having the dev enviroment be compromised is a lower impact.

**UAT (User Acceptance Testing)** - UAT is used to test the application or certain features before being pushed to production. This usually includes unit tests and security tests as well.

**PreProd** - Pre-production is used to mirror Prod but without customer data. It should have similar security controls to Prod, be kept stable and be used to perform final tests before pushing code to Production.

**Production** - The active enviroment that customers will actually use. 

**DR/HA** - Depending on how critical the system is, there may be a Disaster Recovery environment or High Availability environment as well. DR is self explanatory, High Availability is used for critical applications where uptime is the most important feature.

**Blue/Green Environments** - A deployment strategy where there are two Prod environments. Blue runs the current app version and Green runs the new one. Using a proxy/router, traffic can be switched to the Green environment when necessary, and the blue environment is still kept in case of issues.

**Canary Environments** - Similarly, in a canary deployment two Prod environments are used and users are gradually moved from one environment to the new environment. For example, 10% of users could be migrated, and if the environment is stable with no issues, another 10% and so on.

Question: Which environment usually has the weakest security configuration?
Answer: Dev

Question: Which environment is used to test the application?
Answer: UAT

Question: Which environment is similar to PROD but is used to verify that everything is working before it is pushed to PROD? 
Answer: PreProd

Question: What is a common class of vulnerabilities that is discovered in PROD due to insecure code creeping in from DEV?
Answer: Developer Bypasses

