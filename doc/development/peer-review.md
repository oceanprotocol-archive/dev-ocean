
Table of Contents
=================


   * [Peer Reviews](#peer-reviews)
      * [Basic Guidelines](#basic-guidelines)
      * [Recommendations](#recommendations)
      * [Ocean Configuration](#ocean-configuration)


---


This page describes the Peer Review recommendations


# Peer Reviews

Peer to peer code reviews are necessary in any complex construction of a system. It not only applies to coding, peer reviews are also a good practice in:

- Applications Architecture
- Infrastructure as a Code implementations
- Continuous Integration pipelines implementation
- Scripts to install the applications in different environments
- Configuration files
- Database scripts

## Basic Guidelines

- Establish Peer to Peer reviews practice can be complex in teams that are not used to do it. So initially  it's better to start simple, requiring that one team member review a college implementation.
- Configure the peer reviews in the delivery workflow and reflect it in the Continuous Integration pipelines, allowing to automate the required validations.
- Protect the develop and master branches, disabling to merge feature branches into develop branches without the approval of a team member.
- Use a Code Review tool integrated with the SCM

## Recommendations

During the peer review process we recommend:

- Maybe most important recommendation is to be kind. When you are reviewing the work of a college you must to understand you are evaluating the work where a college put time and effort. So please, be kind and constructive.
- Review fewer than 400 lines of code (LOC) at a time. In practice, a review of 200-400 LOC over 60 to 90 minutes should yield 70-90% defect discovery. So, if 10 defects existed in the code, a properly conducted review would find between seven and nine of them.
- Take time for the review. Inspection rates should be under 500 lines of code per hour
- Don't review for more than 60 minutes at a time. It's not recommended to review code too quickly, either you shouldn't review for too long in one sitting. When people engage in any activity requiring concentrated effort over a period of time, performance starts dropping off after about 60 minutes.
- Set goals and capture metrics. Before implementing a peer review process, the DevOps and delivery teams should decide how you will measure the effectiveness of peer review and name a few tangible goals.
- Authors should annotate code before the review occurs because annotations guide the reviewer through the changes, showing which files to look at first and defending the reason behind each code modification.
- Use checklists. It's very likely that each person on your team makes the same 10 mistakes over and over. Omissions in particular are the hardest defects to find because it's difficult to review something that isn't there. Checklists are the most effective way to eliminate frequently made errors and to combat the challenges of omission finding.
- Establish a process for fixing defects found. The best way to ensure that defects are fixed is to use a collaborative code review tool that allows reviewers to log bugs, discuss them with the author, and approve changes in the code. We recommend the one natively integrated with Github.
- Foster a positive code review culture. Peer review can put strain on interpersonal team relationships. It's difficult to have every piece of work critiqued by peers and to have management evaluating and measuring defect density in your code. Therefore, in order for peer code review to be successful, it's extremely important that mangers create a culture of collaboration and learning in peer review.

## Ocean Configuration

Some specific Ocean configuration regarding the Peer Reviews:

- Because the [Git Branch Protection rules](../devops/github-setup.md#branch-protection) configured by default, any merge to **develop** branch requires a Pull Request, and the approval of a college.
- It's recommended to assign teams rather than individual's. For example, in Ocean it's common to assign the Pull Requests reviews to the **Ocean Core Dev** team.
- By the time being, all the repositories only require one approval. Future configurations could require more approvals, allowing double checks by different team members. This could apply specially to projects requiring higher security standards for example.
- In the future, specific Peer Review tools could be integrated with Github


