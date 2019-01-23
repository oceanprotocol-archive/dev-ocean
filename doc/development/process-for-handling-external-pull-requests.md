# Process for Handling External Pull Requests

## Overview

Before merging a pull request from an external developer, please follow this process, otherwise our code might end up containing intellectual property that hasn't been licensed under an open source license (and the owner might sue, or worse).

## Are they an external developer?

If they are a _future or former employee_ of BigchainDB GmbH or DEX Pte Ltd, then they are an external developer.

If they are a _current employee_ of BigchainDB GmbH or DEX Pte Ltd, then they are not an external developer and you can ignore this process.

If they did an Ocean Protocol bounty in the past, but this pull request isn't associated with any [Ocean Protocol bounty](https://docs.oceanprotocol.com/concepts/bounties/), then they are an external developer.

If the pull request is from someone who is fulfilling the requirements of a specific [Ocean Protocol bounty](https://docs.oceanprotocol.com/concepts/bounties/), then they are not an external developer and you can ignore this process.

Otherwise they are an external developer.

## Extra things external developers must do

See the things listed in the Ocean Protocol docs page about [Legal Requirements when Contributing Code](https://docs.oceanprotocol.com/concepts/legal-reqs/).

1. Every file they modified contains the copyright notice described in the Ocean Protocol docs page about [Legal Requirements when Contributing Code](https://docs.oceanprotocol.com/concepts/legal-reqs/).
1. In the pull request comments, ask them if they read and understood the [Developer Certificate of Origin](https://developercertificate.org/). They must answer "yes" (or equivalent) in the comments.
1. There's a copy of the Developer Certificate of Origin (text file) in the root of the repository where they are contributing. If not, ask them to put one there.
1. Every commit message in the pull request includes a Signed-off-by line, with a real name and a real email address. Pseudonyms and fake names will not suffice. The Signed-off-by line is akin to an e-signature. **Do some checking to convince yourself that it's a real name and email address.**

If any of the the external developer's Git commit messages do _not_ include a Singed-off-by line, then ask them to redo their pull request such that all commit messages _do_ include a Signed-off-by line. (They could use git rebase to modify their existing pull request, or they could just close the pull request and create a whole new one.) See [GitHub's help page about changing Git commit messages](https://help.github.com/articles/changing-a-commit-message/).

If (and only if) all the above are true/done, then you can merge the pull request.
