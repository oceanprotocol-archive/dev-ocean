
Table of Contents
=================

   * [Delivery Pipeline](#delivery-pipeline)
      * [Pipeline Steps](#pipeline-steps)
         * [Business Definition](#business-definition)
         * [Research](#research)
         * [Development](#development)

---

This page describes the expected Ocean Delivery Pipeline.


# Delivery Pipeline

The Delivery Pipeline describes the process of building Ocean, starting from the definition to its delivery.
This process is organized in the following steps:

## Pipeline Steps

![Delivery Phases](../img/delivery-phases.png)

The pipeline is organised into the following 3 high-level steps:

* Business Definition - Gathering requirements from internal or external users
* Research - Investigation of open technical questions
* Development - Software implementation and delivery


### Business Definition

The Business Definition process is primarily intended to gather all the requirements needed to be implemented in Ocean.
It should include enough details to have an understanding of what is necessary for implemention. All this information should be documented in the
[Business Board](https://github.com/oceanprotocol/ocean/projects/1).

A new issue or bug should include the following information:

* A clear title describing the issue
* A detailed enough description describing the business case
* A list of labels that categorize the issue
* An associated milestone in order to help to understand when it's necessary to implement the issue
* Optionally, any additional links or resources giving additional information

Having this information, the business and technical people can discuss the details/scope of the implementation.

![Business Board](../img/board-business.png)


### Research

After creating an initial business definition, if we still have open technical questions to discuss it could be necessary to start a research process. Typically in Ocean we are dealing with different kinds of technologies, some of those in early stage, so this process is quite important to understand what can be accomplished, the maturity state, etc.

When a Business issue requires further technical investigation, a new issue should be raised in the [Research Board](https://github.com/oceanprotocol/ocean/projects/3).

![Delivery Pipeline](../img/delivery-pipeline.png)

The output of this Research process should then become an input of the development process. This will require the following:

* Github repository with working code (repos: `poc-*`)
* Documentation detailing the research made
* Conclusions

The following projects can be used as a reference of good PoC's:

* [PoC TCR](https://github.com/oceanprotocol/poc-tcr)
* [PoC Salpchain](https://github.com/oceanprotocol/poc-salpchain)



### Development

Once there are enough business details, and optionally a Research PoC demonstrating the technical next steps, the final development process can be started.
A new issue can be raised in the [Development Board](https://github.com/oceanprotocol/ocean/projects/2), including all the technical details necessary to accomplish the delivery.

The Development Requirements should include the following information:

* A clear title describing the issue
* A detailed enough description describing the technical implementation 
* A list of labels that categorize the issue
* Links to the expected milestone(s)
* Links to Business or Research issues in the other boards

A development issue can be considered an **Epic** issue when it is big enough. We don't have a formal definition of this, but if the development of the technical issue can't be accomplished in the scope of one sprint, the issue is big enough to be considered an **Epic** story and should be split in smaller issues.
