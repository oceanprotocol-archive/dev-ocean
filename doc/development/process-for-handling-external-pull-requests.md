# Process for Handling External Pull Requests

## Overview

Before merging a pull request from an external developer, please follow this process. To understand why it's necessary, read the first paragraph after BEGIN TEXT BLOCK below.

## Are they an external developer?

If they are a _future or former employee_ of BigchainDB GmbH or DEX Pte Ltd, then they are an external developer.

If they are a _current employee_ of BigchainDB GmbH or DEX Pte Ltd, then they are not an external developer and you can ignore this process.

If they did an Ocean Protocol bounty in the past, but this pull request isn't associated with any [Ocean Protocol bounty](https://docs.oceanprotocol.com/concepts/bounties/), then they are an external developer.

If the pull request is from someone who is fulfilling the requirements of a specific _currently-open_ [Ocean Protocol bounty](https://docs.oceanprotocol.com/concepts/bounties/), then they are not an external developer and you can ignore this process.

If you're not sure whether they're an external developer, then ask in the pull request comments. Copy and paster this text:

```text
- Are you a past, present or future employee of BigchainDB GmbH or DEX Pty Ltd? If yes, which one?
- Is this pull request to fulfill a currently-open official Ocean Protocol bounty? If yes, which one?
```

## If they are an external developer, then do this

Copy the following text into the pull request's comments:

**Note: Copy the raw Markdown source of the following text, not the rendered HTML, because GitHub comments are written in Markdown.**

BEGIN TEXT BLOCK

Before we can merge this pull request, we need you to sign off on licensing your code under the [Apache License version 2.0](https://www.apache.org/licenses/LICENSE-2.0). One of the big concerns for people using and developing open source software is that someone who contributed to the code might claim the code infringes on their copyright or patent. To guard against this, we ask all our contributors to take certain steps (detailed below). This gives us the right to use the code contributed and any patents the contribution relies on. It also gives us and our users comfort that they won't be sued for using open source software. We know it's a hassle, but it makes the project more reliable in the long run. Thank you for your understanding and your contribution!

1. Make sure that every file you modified or created contains a copyright notice comment like the following (at the top of the file):

   ```text
   # Copyright Ocean Protocol contributors
   # SPDX-License-Identifier: Apache-2.0
   ```

   - If a copyright notice is not present, then add one.
   - If the first line of the file is a line beginning with `#!` (e.g. `#!/usr/bin/python3`) then leave that as the first line and add the copyright notice afterwards.
   - If a copyright notice is present but it says something like `Copyright 2018 Ocean Protocol Foundation` then please change it to say the above.
   - Make sure you're using the correct syntax for comments (which varies from language to language). The example shown above is for a Python file.
1. Read the [Developer Certificate of Origin, Version 1.1](https://developercertificate.org/).
1. You will be asked to include a Signed-off-by line in all your commit messages. (Instructions are given in the next step.) Make sure you understand that including a Signed-off-by line in your commits certifies that you can make the statements in the Developer Certificate of Origin. If you have questions about this, then please ask them in the comments below. Do not continue until you fully understand.
1. Make sure that every commit message in this pull request includes a Signed-off-by line of the form:

   ```text
   Signed-off-by: Random J Developer <random@developer.example.org>
   ```

   with your real name and your real email address. Sorry, no pseudonyms or anonymous contributions. Tip: You can tell Git to include a Signed-off-by line in a commit message by using `git commit --signoff` or `git commit -s`. If you didn't include a Signed-off-by line in your commits so far, you have several options. Probably the simplest is to close this pull request on GitHub, then on your local machine [do an interactive rebase of the last n commits](https://help.github.com/en/articles/changing-a-commit-message) (e.g. the last 3 commits, if your pull request contains 3 commits), then rename your local Git branch (`git branch -m <new-name>`), then push the newly-named branch to GitHub to create a new pull request.

END TEXT BLOCK

Wait for them to fulfill all the above-listed requirements.

Do some checking to convince yourself that each Signed-off-by line contains a real name and email address.

If (and only if) all the above-listed conditions are met, then you can merge the pull request.
