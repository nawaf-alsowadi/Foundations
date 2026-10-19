## Microsoft Policy Analyzer 
Provide a security template to compare it with current configuration of windows.

To do it, we will have to install Microsoft Security Compliance Toolkit based on the windows version.
## Get windows version
![[Pasted image 20260223183249.png]]

## Start Policy Analyzer
Install the corresponding toolkit for the version from Microsoft, unzip the file, execute the **PolicyAnalyzer.exe**, and select the folder **Documentation** from  **Policy Rule sets**.

![[Pasted image 20260223184741.png]]

Compare the current conf with security template
![[Pasted image 20260223184906.png]]

Result:
![[Pasted image 20260223185040.png]]

## LAB Question
- What is the baseline value from the security template for the policy setting item of LockoutBadCount?
10
- What is the baseline value from the security template for the policy setting item of MinimumPasswordLength?
14

Is the PC10 system in compliance with the security template based on the gap analysis results?

 Yes
 *No*

- Performing gap analysis forces systems into compliance.

 True
 *False*

- Gap analysis is a process that identifies how an organization's security systems deviate from those required or recommended by a framework.

 *True*
 False

- When should gap analysis be performed? (Select all that apply)

	 - *when first adopting a framework*
	 - *when meeting a new industry or legal compliance requirement*
	 - *after significant time has past*
	 - when decommissioning legacy hardware


- What is the purpose of a gap analysis?

	 - *discovering the differences between the intended or expected configuration of a system and its actual operating configuration*
	 - exploring the source code of a open source application for flaws and vulnerabilities
	 - determining the probability and likelihood of a threat causing harm to an asset
	 - evaluating the level of compliance to a regulation or contractual obligation

- Which of the following statements is false in regard to gap analysis?
	-  Some variations from a baseline may be more secure
	- Some variations from a baseline are less secure
	- *A single security template is sufficient to analyze all systems*
	- Security template selection should be specific to its product version and build number