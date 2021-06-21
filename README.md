# Blueprint for building modern, secure software development pipelines

[![Apache 2.0 License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
![Community Supported](https://img.shields.io/badge/Support%20Level-Community-brightgreen)

_**This open source project is community-supported.** To report a problem or share an idea, use
**[Issues](../../issues)**; and if you have a suggestion for fixing the issue, please include those details, too.
In addition, use **[Pull Requests](../../pulls)** to contribute actual bug fixes or proposed enhancements. We welcome and appreciate all contributions._

## Who's this for?

This document is for engineers and security teams who are driving fast and secure software supply chains.

This document was authored by [Veracode](https://www.veracode.com/) and [Venafi](https://www.venafi.com/) with contributions and support from [Sophos](https://www.sophos.com/en-us.aspx) and [Cloudbees](https://www.cloudbees.com/). Our collective intent is to define a vendor-neutral map of standard controls. These diverse controls drammatically reduce risk and align with agile, high performance software developlment pipelines.  

We encourage your contributions to the Blueprint and project, including examples that implement any of the Controls. <!-- link down to section from here -->

## Audience + outcome

This Blueprint has been created by engineers for engineering leaders, architects and product security engineers. The design of this Blueprint places a priority on the _speed and agility of modern software development_, providing businesses with a competitive advantage while incorporating a _security-always_ mindset.

We propose a standard set of _controls_ to secure software development pipelines for continuous integration and continuous deployment (CI/CD) against attack. Our goal? Minimize the possibility supply chain attacks. As examples, consider the attack that's trojanizing SolarWinds Orion software updates; or the Codecov Bash Uploader attack; or the more recent Passwordstate password manager compromise. 

We can achieve a more secure pipeline by ensuring that
- authentication and authorization are properly managed throughout the pipeline
- the integrity of software artifacts are tested at appropriate stages
- controls are placed on third-party and open-source software, and incorporated into the software

The outcome of adopting the Blueprint? The delivery of assured software at lower risk of supply chain tampering, reduction of attacks during development, and manipulation in staging and production. The Blueprint gives engineering teams an actionable architecture that security teams can get behind and support.

## Situation

### Speed + agility

Engineers are the competitive advantage. The speed and agility built on open source, cloud native design, multi-cloud architectures, and CI/CD pipelines are the ingredients for success.

### Attack on software development

Attacks are changing. Attackers are developers who have grown up with an open source, cloud native mindset. Most of the focus of security in the last few years has been on infrastructure and as everything has become software-defined, attackers have moved to attack surfaces that are less secure and not well understood by traditional security teams. They are shifting _left_ to target developers and supply chains to yield more successful outcomes, whether highly targeted or wide-ranging in impact. So, security teams and controls will be playing catchup forever.

## Design philosophy

The Blueprint is designed to _engineer out_ the attacker, enabling developers and their automated pipelines to move faster, from idea to production. Engineering teams must be responsible for the design and implementation while giving security teams the visibility and assurance that _controls are operational_ and that _policies are being enforced_. When implemented correctly, engineering teams can use their preferred pipeline tools and consume their organization’s preferred cybersecurity services without being impeded by enterprise security controls.

We've based our approach on the following design philosophy:  

- _Least Privilege_: only grant the access and permissions required to accomplish a job, and no more.

- _Immutability_: artifacts and infrastructure are not modified after deployment to an environment. 
 If a change is required, it is done in the image or script in the development environment and then promoted through the higher environments.

- _Everything as Code_: in addition to software artifacts, infrastructure, security policies, and other parts of the pipeline are also implemented as code and subject to the same controls as the software artifacts.

- _Traceability_: _all changes_, whether to infrastructure or business code, are version-controlled. This principle works hand-in-hand with Everything as Code.

Our Blueprint design is pragmatic. Security controls are not to be implemented (or will be routed around) _if they prove an impediment to the timely delivery of new software_. The Blueprint is developer-friendly and designed to create minimal friction while simultaneously improving the quality and assurance of the software that runs the modern world.

## Blueprint for speed, agility and assurance

The Blueprint for engineered assured software is designed on four stages of software development pipelines:

1. _Code_: developers design software and commit code-to-code repositories  

1. _Collaborate_: developers include internal and external libraries and share software for review  

1. _Staging_: software is built and prepared for final delivery  

1. _Production_: software is run anywhere

### Blueprint in action

The Blueprint is built to fit modern development pipelines where developers, pipelines, and execution can be anywhere in the world.

![Blueprint for a modern development pipeline](dia-BlueprintForModernDevPipeline.png)

#### Control-1: Restrict administrative access to CI/CD tools

It's important to ensure that only authorized persons can make administrative changes to the CI/CD system. If an unauthorized person is able to gain access, changes to pipeline definitions enable the subversion of many of the remaining controls in this document.

Both host and application-layer access to CI/CD tools should be protected with multi-factor authentication.

> :skull: 
> Instead of manipulating code, attackers will seek to control the CI/CD pipeline itself. This can enable both breaches without detection and long-term impact far greater than manipulating code. Attackers seek to introduce new pipelines, insert malicious code, or modify pipelines that introduce controls like code signing.

#### Control-2: Only accept commits signed with a developer GPG key

Unsigned code commits are challenging, if not impossible, to trace and pose a risk to the integrity of the code base. Requiring commits to be signed with a developer GPG key helps to ensure nonrepudiation of commits and increases the burden on the attacker seeking to insert malicious code.

> :skull: 
> An easy win for attackers can be to infect developers’ machines or steal credentials that give access. Attackers can then manipulate code or inject new malicious code as they please.

#### Control-3: Automation access keys expire automatically

Ensuring that access keys used by automation expire periodically creates a shorter window of attack when keys are compromised.

> :skull: 
> Automated build systems are a hacker’s dream: they are out of sight of humans and operate at machine speed. Attackers can either steal access keys or mint new ones through a series of attacks. These are especially hard to detect.

#### Control-4: Reduce automation access to read-only

CI systems should have _read access only_ to source code repositories following the principle of least privilege access.

> :skull: 
> Automated build processes are a hacker’s dream: they are out of sight of humans and operate at machine speed. Attackers can bypass usual source code review process to make changes to the source code that may be hard to detect.

#### Control-5: Only dependencies from trusted registries can be used

Modern software dependency managers, including `npm`, `maven`, `Nuget`, `pip`, and others, rely on declaring the dependencies required for the application and then fetching them at build time. By configuring the dependency manager to only allow connections to an authorized list of registries, these attacks can be blunted by keeping malicious packages in the public registries from entering the pipeline.

The trusted repository can also ensure that security policies are enforced on dependencies. For example, trusted repositories could ensure that only dependencies that are free of critical or high vulnerabilities are used. Implementing a control at the repository that returns an error when a component with known vulnerabilities is requested helps to reduce the chances of an attack against a known vulnerability downstream.

Teams should be aware of implicit runtime dependencies as well as explicit buildtime dependencies (see Control 14).

> :skull: 
> Attackers can quickly spread malicious code through dependencies. Attackers might insert malicious code that is then incorporated into the application’s manifest by stealing credentials en masse for sophisticated, targeted thefts.
> Hackers can also target mistakes and oversights through _typo-squatting_ and _dependency confusion_. Adversaries go to great ends to publish packages under a trusted name or even with common typos so that they are included in builds.
> All of the stakes are raised exponentially when public repositories are used.  

#### Control-6: Any critical or high severity vulnerability breaks the build

Supply chain attacks may introduce code with vulnerabilities into the software pipeline. Using static application security testing (SAST) helps to identify serious security issues, including poor cryptographic practices, hard-coded credentials, and injection vulnerabilities. Performing SAST in the pipeline and failing the pipeline on the discovery of a critical- or high-severity discovery helps to minimize the chance of introducing intentionally insecure code.

Likewise, software composition analysis (SCA) identifies software libraries with known vulnerabilities. The pipeline should fail if a library with a critical- or high-severity vulnerability is identified. 

>**NOTE**  This control helps to mitigate against a failure of Control-4.

Both SAST and SCA might identify weaknesses in software that are not true vulnerabilities. So, selecting tools that allow you to set a _baseline_ of acceptable findings can help to ensure that this control is adopted successfully.

This control should be applied at various stages of the pipeline, including IDE-integrated checks, when committing to a branch, at the start of every pull request, and at merge requests into the main branch. Early identification of vulnerabilities can reduce remediation costs.

>**IMPORTANT!**  This control would also need a defined and documented vulnerability exception management process to be in place.

> :skull: 
> Most often the result of unexpected consequences, vulnerabilities are a reality of developer life. But attackers, too, might contribute vulnerabilities that go unseen and undetected and can proliferate through a software supply chain quickly.

#### Control-7: Artifacts are stored in a repository in development, stage and production

All artifacts should be stored in a repository at each stage of the build pipeline so that there is clear traceability between the test results and the actual artifact that was tested. This control also helps to enforce the immutability of the artifacts, such that we can compare artifacts in the development, staging and production repositories and ensure that we maintain a chain of control.

Repositories for dev, stage and production should be segregated so that role-based access control can ensure least privilege at each stage, and so that more stringent policies (such as artifact signing) can be enforced in higher environments.

Artifacts should be promoted from repository to repository in accordance with the principle of immutability.

#### Control-8: Validate artifact digest

Before an artifact is deployed in any environment, its digest should be validated against the artifact in the repository to ensure that it has not been compromised.

> :skull: 
> At all times, attackers are attempting to infiltrate systems and make modifications to code through any variety of means. Adversaries insert targeted attacks or common vulnerabilities to suit their unpredictable needs.

#### Control-9: Pull-requests require two reviewers (including one default reviewer) and a passing build to be merged

Require two code reviews at the time of pull-requests, including one from a developer who is an _owner_ of that code. In addition to supporting good coding practices, this control also helps to ensure that no commits can be made without competent human oversight. Requiring that all tests are passed ensures that the above controls are not circumvented.

> :skull: 
> At all times, attackers are attempting to infiltrate systems and make modifications to code through any variety of means. Adversaries insert targeted attacks or common vulnerabilities to suit their unpredictable needs. We cannot assume that the adversary is a member of your own team.

#### Control-10: Artifacts in higher repositories are signed

Requiring artifacts to be signed in a repository throughout the process ensures visibility and traceability for whatever is deployed to production. Requiring signed artifacts helps to ensure that untrusted binaries are not deployed to customer environments and allows validating the source of the binaries.

> :skull: 
> Through credential theft, vulnerability exploit, targeted attacks or more, attackers succeed in inserting their malicious code into pipelines and repositories. Code should be considered suspect and malicious.

#### Control-11: Available container images don’t have any high or critical vulnerabilities

Just as applications need to be tested for vulnerabilities before being placed in production, so do the container images into which they are packaged for deployment. Container images could have open-source vulnerabilities as long as they contain open-source software (such as web application servers, networking libraries, and databases). Additionally, containers might have configuration vulnerabilities that allow attackers to have more access than they should to the resources that containers manage. Container images can be tested with a container security tool in the pipeline.

#### Control-12: Validate artifact signatures and digests

As part of deploying artifacts from the internal registry, validating the signature of the artifact against the digest ensures that the artifact was not tampered with in the repository and that the artifact being deployed is the same one that was tested.

> :skull: 
> Through credential theft, vulnerability exploit, targeted attack or more, attackers succeed in inserting their malicious code into pipelines and repositories. Code should be considered suspect and malicious.

#### Control-13: Scan deployed images in production

It is always a good idea to validate pre-production controls in production. This helps to ensure that the controls prior were followed for all software in production.

> :skull: 
> At all times, attackers are attempting to infiltrate systems and make modifications to code through any variety of means. Adversaries insert targeted attacks or common vulnerabilities to suit their unpredictable needs. We cannot assume that the adversary is a member of your own team.

#### Control-14: Validate Kubernetes resource manifests

The last line of defense is the _container orchestration layer_. Kubernetes is responsible for deploying the containers of the application into production, and if the resource manifests are tampered with, it could be tricked into deploying a container of the attacker’s choice. It is important to ensure that the Kubernetes resource manifests are controlled and validated just as the actual images are.

#### Control-15: Ensure build environments are ephemeral and immutable

Build environments should be defined in code with automated creation and teardown, and that a fresh environment is created for every build. Build hosts should not be accessible using interactive logins.

> :skull: 
> Attackers who gain access to build environments are able to bypass controls implemented earlier in the build pipeline. Ensuring build environments are themselves defined as code and live only for the duration of a build prevents attackers from persisting in build infrastructures.

## Appendix A: Blueprint reference - threats, controls, and critical services

This appendix summarizes the controls, threats they protect against, and the critical services that are involved.

|  |     | Blueprint Node | Threat | Control | Critical Services
| :---   | :---    |:---  | :---   | :---    | :---
| <td rowspan="4">Code</td>| 1 | Attacker privilege escalation | Restrict admin access to CI/CD tools | Zero trust access  
| | 2 | Attacker submits malicious code | Accept only commits signed with unique identities | Code signing
| | 3 | Attacker credential theft, escalation, or exploit to control CI/CD system | Automation access keys expires automatically | CI/CD policy control
| | 4 | Attacker submits malicious code via CI/CD system | Automation read-only access to source code repo | CI/CD policy control
| <td rowspan="5">Collaboration</td> | 5 | Vulnerability introduced from software supply chain | Only dependencies from trusted registries accepted | Enterprise artifact registry
|             | 6 | Vulnerability introduced from software supply chain | Vulnerability analysts rejects threats | Static Code Analysis Software Component Analysis
|             | 7 | Attackers attempt to manipulate code directly | Artifact repository restrictions | Enterprise artifact registry (including Containers)
|             | 8 | Attackers successfully manipulate code directly | Validate approved artifacts | Code signing CI/CD policy control
|             | 9 | Attackers successfully manipulate code directly | Dual approval controls | Source code revision system
| <td rowspan="5">Staging</td>     | 10 | Attackers target to manipulate code on staging | Only signed code accepted for staging | Enterprise artifact registry (including Containers) <br />Code Signing
|             | 11 | Attackers target to manipulate code moving to production | Only signed code accepted for production | Enterprise artifact registry (including Containers) <br /> Code Signing
|            | 12 | Attackers target to manipulate code moving to production | Containers scanned before production | Container Security Scan
|            | 13 | Attackers target to manipulate code moving to production | Only signed code accepted for production | CI/CD policy control <br />Code Signing
|            | 14 | Attackers target code in production | Containers scanned in production | Container Security Scan
| <td rowspan="1">Production</td> | 15 | Attackers target production configuration | Runtime policy enforced | Container policy enforcement

## Appendix B

Here are the controls prioritized by _complexity of implementation_ and by _level of risk_.

Control | Complexity of Implementation | Level of Risk
:------------ | :-------------: | :--------------:
Control-1: Restrict administrative access to CI/CD tools | Low | High
Control-2: Only accept commits signed with a developer GPG key | Medium | Low
Control-3: Automation access keys expire automatically | Medium | High
Control-4: Reduce automation access to read-only | Medium | High
Control-5: Only dependencies from trusted registries can be used | Medium | High
Control-6: Any critical or high severity vulnerability breaks the build | Low | High
Control-7: Artifacts are stored in a repository in development, stage and production | Medium | Medium
Control-8: Validate artifact digest | Low | High
Control-9: Pull-requests require two reviewers (including one default reviewer) and a passing build to be merged | Low | Medium
Control-10: Artifacts in higher repositories are signed | High | Medium
Control-11: Available container images don’t have any high or critical vulnerabilities | Low | High
Control-12: Validate artifact signatures and digests | Medium | High
Control-13: Scan deployed images in production | Low | Medium
Control-14: Validate Kubernetes resource manifests | Low | Medium
Control-15: Ensure build environments are ephemeral and immutable | High | Medium

## Appendix C

Here are a few Blueprint controls with examples of specific attack vectors:

- Control-2: _Attacker submits malicious code_

- Control-5: _Vulnerabilities introduced from supply chain_

- Control-13: _Attackers target to manipulate code moving to production_
