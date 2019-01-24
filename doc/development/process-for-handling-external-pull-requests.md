# Process for Handling External Pull Requests

## Overview

Before merging a pull request from an external developer, please follow this process, otherwise our code might end up containing intellectual property that hasn't been licensed under an open source license and _bad things could happen_.

## Are they an external developer?

If they are a _future or former employee_ of BigchainDB GmbH or DEX Pte Ltd, then they are an external developer.

If they are a _current employee_ of BigchainDB GmbH or DEX Pte Ltd, then they are not an external developer and you can ignore this process.

If they did an Ocean Protocol bounty in the past, but this pull request isn't associated with any [Ocean Protocol bounty](https://docs.oceanprotocol.com/concepts/bounties/), then they are an external developer.

If the pull request is from someone who is fulfilling the requirements of a specific [Ocean Protocol bounty](https://docs.oceanprotocol.com/concepts/bounties/), then they are not an external developer and you can ignore this process.

**If you're not sure of their status, ask in the pull request comments.**

Bottom line: If they're not a _current_ employee of BigchainDB GmbH or DEX Pte Ltd and the pull request isn't to fulfill a specific _current_ Ocean Protocol bounty, then they are an external developer.

## If they are an external developer, then do these steps

Copy the following Markdown text into the pull request's comments:

```text
Before we can merge this pull request, we need you to sign off on licensing your code under an open source license. One of the big concerns for people using and developing open source software is that someone who contributed to the code might claim the code infringes on their copyright or patent. To guard against this, we ask all our contributors to take certain steps (detailed below). This gives us the right to use the code contributed and any patents the contribution relies on. It also gives us and our users comfort that they won't be sued for using open source software. We know it's a hassle, but it makes the project more reliable in the long run. Thank you for your understanding and your contribution!

1. Make sure that every file you modified contains the copyright notice described in the Ocean Protocol docs page about [Legal Requirements when Contributing Code](https://docs.oceanprotocol.com/concepts/legal-reqs/).
1. Read and understand the [Developer Certificate of Origin](https://developercertificate.org/).
1. Write a comment below that says, "I have read and understood the Developer Certificate of Origin."
1. Make sure there's a copy of the Developer Certificate of Origin (text file) in the root of this repository. If there isn't one, then please add one (as part of this pull request).
1. Make sure that every commit message in this pull request includes a Signed-off-by line, with your real name and your real email address. Pseudonyms and fake names will not suffice. The Signed-off-by line is akin to an e-signature. GitHub has [a help page about how to change Git commit messages](https://help.github.com/articles/changing-a-commit-message/).
```

Wait for them to fulfill all the above-listed requirements.

Do some checking to convince yourself that the Signed-off-by lines contain a real name and email address.

If (and only if) all the above-listed conditions are met, then you can merge the pull request.
