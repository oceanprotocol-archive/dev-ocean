
Table of Contents
=================




---


This page is a central point for documenting the Peer Review recommendations


# Peer Reviews

Peer to peer code reviews are necessary in any complex construction of a system.
It not only applies to coding, peer reviews are recommended in:

- Applications code
- Infrastructure as a Code implementations
- Continuous Integration pipelines implementation
- Scripts to install the applications in different environments
- Configuration files
- Database scripts

Establish Peer to Peer code review practice can be complex in teams that are not used to do it.
It's recommended to introduce this practice in the software delivery workflow and reflect it in the Continuous Integration pipelines, allowing to automate those validations.
Also it’s recommended to start simple, requiring that one team member review the implementation of a college.
We recommend don’t allow to merge feature branches into develop branches without the approval of a team member.
If Github is the SCM Git implementation to use, we recommend to use the code review shipped with the tool. It’s totally integrated and easy to use.
During the peer review process we recommend:

- Review fewer than 400 lines of code (LOC) at a time. In practice, a review of 200-400 LOC over 60 to 90 minutes should yield 70-90% defect discovery. So, if 10 defects existed in the code, a properly conducted review would find between seven and nine of them.
- Take time for the review. Inspection rates should be under 500 lines of code per hour
- Don't review for more than 60 minutes at a time. It’s not recommended to review code too quickly, either you shouldn’t review for too long in one sitting. When people engage in any activity requiring concentrated effort over a period of time, performance starts dropping off after about 60 minutes.
- Set goals and capture metrics. Before implementing a peer review process, the DevOps and delivery teams should decide how you will measure the effectiveness of peer review and name a few tangible goals.
- Authors should annotate code before the review occurs because annotations guide the reviewer through the changes, showing which files to look at first and defending the reason behind each code modification.
- Use checklists. It´s very likely that each person on your team makes the same 10 mistakes over and over. Omissions in particular are the hardest defects to find because it´s difficult to review something that isn´t there. Checklists are the most effective way to eliminate frequently made errors and to combat the challenges of omission finding.
- Establish a process for fixing defects found. The best way to ensure that defects are fixed is to use a collaborative code review tool that allows reviewers to log bugs, discuss them with the author, and approve changes in the code. We recommend the one natively integrated with Github.
- Foster a positive code review culture. Peer review can put strain on interpersonal team relationships. It´s difficult to have every piece of work critiqued by peers and to have management evaluating and measuring defect density in your code. Therefore, in order for peer code review to be successful, it´s extremely important that mangers create a culture of collaboration and learning in peer review.