# Investigating Azure Resource Groups & Correcting Policy Misconfiguration

## Scenario
An intern with temporary Contributor access, deployed a test environment over the weekend and raised a Policy "Non-Compliant".
A investigation was tasked for me to trace down what policy was used and why the test environment was allowed exemption.

## Environment
Live multi-user Azure training tenant, with Reader access

## Investigation

Objective #1: Locate the Resource Group that was Launched by the intern:

- I navigated to "Resource Groups" and starting looking for any resource groups that did not follow the Naming Convention, One stood right out, so the investigation continues here.
<img width="1919" height="889" alt="#1" src="https://github.com/user-attachments/assets/014698fc-6e85-4487-b188-ebc4a916e4c7" />

Objective #2: Expand my knowledge about this resource group

- I went into the Resource Group and noticed that the intern launched a singular resource (a storage account). To find more information about this resource (e.g. Cost-Center, Owner, ect) I navigated towards its "Tags" blade.
<img width="1680" height="780" alt="#2" src="https://github.com/user-attachments/assets/25d22151-3b79-4374-8580-cedfc158128b" />

Objective #3: Inspect the Deployment Name associated with this resource

- Knowing the Deployment name should have helped me confirm when and who deployed this resource, however this was not the case. The intern did not follow Naming Convention. (I was able to confirm the information through context clues and Azure's Timestamps) 
<img width="1372" height="698" alt="#3" src="https://github.com/user-attachments/assets/37b299f8-bca8-46c8-9640-41698c3df82d" />

Objective #4: Understand what Compliance is in place, and why did it not stop the Resource from being created

- I wanted to understand what Policies where in place and what Parameter Values did they hold.
- So I steered to the "Policies" blade and found it interesting that there was a 'Naming Convention' policy being enforced.
- I thought to myself, "So how did the intern create the resource if they were not compliant?"
- To answer this question I decided to dive into the Parameter Value that the policy had, and well, it was using "Audit". Meaning that the desired resource could not comply and still be created.
**NOTE:** 'Deny' should be the enforced Parameter Values in this scenario to ensure neat and traceable operations 
<img width="1675" height="773" alt="#4-A" src="https://github.com/user-attachments/assets/b4ea807d-496d-4690-bc1c-c9620c6bb10d" />
<img width="1677" height="701" alt="#4-B" src="https://github.com/user-attachments/assets/77ac4b7e-c016-4f6f-8cc1-ee4d3d9ef252" />

## What Failed and Recommendations
**Two Things Failed:**
- The Parameter Value 'Audit' that was configured.
- Awareness of 'Audit' being in place

To the intern I would suggest reading over and understanding Azure Naming Convention and the importance of it. I would also suggest not deploying Testing Environments right before the weekend, it racks up Cost Without Use.  

## What I learned

* How to Investigate and navigate through Resource Groups and individual resources
* The importance of having the proper Parameter Values in place
* If I were to put myself in the interns shoes, I would ensure Compliance to the Naming Convention and I would make sure this stays true to Deployment Documentation.
