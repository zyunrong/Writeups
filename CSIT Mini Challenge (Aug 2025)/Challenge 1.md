# Challenge 1

## Challenge Instructions
```
The control platform was just the beginning — this shadowy group has its sights set on far bigger targets.
Intelligence indicates their true objective is to infiltrate our transport infrastructure.
Your first entry point: the control platform's development environment. Dig through the wreckage, recover the broken pipelines, and uncover clues to their next target!
```

## Hints and Resources
```
Two roads diverged, A corrupted commit not meant to merge. Reclaim the feat, Escape your past, A path through will be revealed at last, (Look for magnifying glass on this page)
```

## Walkthrough
Upon loading the webpage, we can see a magifying glass emoji located near the top of the page

<img width="2772" height="1132" alt="Screenshot 2025-08-08 135109" src="https://github.com/user-attachments/assets/322d9168-7532-4c26-8cd6-157590c4a734" />

Upon clicking, we are redirected to solve our first challenge, which is a fork of the original `Learning Git Branching` project

<img width="2774" height="1489" alt="image" src="https://github.com/user-attachments/assets/185f9691-b7e3-4c63-a91b-38e511eaf03e" />

We are then given a short introduction and description of what we need to do, along with a demonstration of `git rebase` and `git reset`

```
CSIT Mini Challenge #1

You have some development branches (dev1 & dev2) alongside your main branch, with a few bad commits in your main branch.

The remote repository has already committed a mistake onto the main branch followed by a correct commit.
Orientation

The left tree nodes are your 'working' tree, i.e. your local git

The right tree nodes are your 'remote' git, i.e. GitHub/GitLab
Objective

Your goal is to merge/rebase the various dev branches with main, while removing the mistake c8 commit from main remote branch, but also keep the correct commit c9 after the aforementioned mistake.

Make sure you finally push the changes to remote to reflect the correct git history.

The expected order of commits in main are as follows.

    c0 > c1 > c7 > c4 > c5 > c6 > c2 > c3 > c9
    c0 > c1 > c7 (main branch's base commits)
    c4 > c5 > c6 (dev2 branch's commits)
    c2 > c3 (dev 1 branch's commits)
    c9 (main branch's correct commits after mistake c8)

Tips

use help level to bring up this dialog again

use show goal to visualize what the end state should be, hide goal to get it out of your way

use reset to bring you back to the starting point if you need it, use undo to remove your last action

use git help to show what commands you can use

All the best!

```
We are then shown the goal state that we must reach, which fits the description given above

<img width="948" height="1121" alt="image" src="https://github.com/user-attachments/assets/7d4aa655-4ee6-46dd-a77a-67b1384532a4" />

We are also shown the initial state with the bad commits, with a hint to use `git push --force` to overwrite the history of the main branch on the remote, this is likely to be the last step once we have fixed the bad commits

<img width="2782" height="1476" alt="image" src="https://github.com/user-attachments/assets/b5d3024c-d310-4def-9fab-1814eb162ef4" />

As the bad commit is on `c8`, first we need to `git reset c7` to restore the main state to `c7`. The `*` shows which branch we are currently working on

<img width="1710" height="895" alt="Screenshot 2025-08-08 140010" src="https://github.com/user-attachments/assets/e9c345be-0ef1-4bea-98c1-1c403e8e7616" />

Next, we need to rebase the commits `c4`, `c5`, and `c6` by `dev2` to after `c7` with `git rebase main dev2`

<img width="1743" height="1093" alt="Screenshot 2025-08-08 140328" src="https://github.com/user-attachments/assets/aef6c375-7b6e-4ab9-b0e2-d7990f684217" />

Next, we rebase the commits `c2` and `c3` by `dev1` to after `c6` with `git rebase dev2 dev1`

<img width="1729" height="1440" alt="Screenshot 2025-08-08 140740" src="https://github.com/user-attachments/assets/ba0ead56-4d12-452a-ad60-e95b7690d8f6" />

Then we use `git checkout main` to switch to the `main` branch

<img width="1734" height="1448" alt="Screenshot 2025-08-08 140849" src="https://github.com/user-attachments/assets/1e2805ec-7e7d-44e3-ac19-387755000220" />

Then we merge `main` with `dev1` with `git merge dev1`

<img width="1742" height="1453" alt="Screenshot 2025-08-08 140903" src="https://github.com/user-attachments/assets/5715d9e3-1144-4283-b8ea-7cfa9ec3f182" />

Next we need to only add commit `c9` by `git cherry-pick c9`

<img width="1720" height="1443" alt="Screenshot 2025-08-08 141239" src="https://github.com/user-attachments/assets/92dee3d7-37a0-445f-9462-0184161af69d" />

We can see that we have already reached the end goal state, as such we just need to `git push --force` to overwrite the history of the main branch on the remote

<img width="1800" height="791" alt="image" src="https://github.com/user-attachments/assets/9e1cf59d-8618-434e-955c-eb2811b96636" />

We are given the flag to `Challenge 1`, when submitted unlocks `Challenge 2`
