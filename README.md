# GitHub Action notifying mentioned PRs 

In this repository, a GitHub action is implemented to notify each mentioned PR (in other repositories) in a PR. This is done by respecting a given task format. The task format is defined as follows:

```markdown
- [ ] depends on [URL to the upstream PR]
```

This should be commented in the child PR (the PR that depends on the upstream PR). GitHub's mecanisms automatically create a link to the upstream PR. As stated in the exercise sheet, the child PR cannot be merged before the upstream PR is merged. 

When the upstream PR is merged, the action automatically notifies the child PR. The notification is done by ticking the checkbox in the task list of the child PR.

## How does it work?

The GitHub action must be implemented in the upstream repository. (We rely on a *push* approach). The action is triggered when a PR is merged. 

The action then looks in the timeline of the PR references of other PRs. If a PR is found, the action will retrieve all comments from the found PR. Then, it will scrap it to find the URL of the upstream PR. For every comment in which the upstream PR's URL is found, it will tick the checkbox of every line containing the URL. 

## Pull vs push
The instructions strongly suggested using a push approach: *Once the mentioned PR is merged, it should trigger a GitHub action which ticks the check box on the
PR where it was mentioned to unblock the merge.*

This approach has several advantages:
* It is more efficient: the action is triggered only when the upstream PR is merged.
* It is immediate: every checkbox is ticked as soon as the upstream PR is merged. (within a few seconds)

However, I found that this approach is not ideal for the following reasons:
* To be able to tick checkboxes from other repositories, we need to use a special GitHub Token (in this case I used a PAT). This is not ideal because of security & permissions issues.
* It needs to modify the upstream repository, which can not be always done, especialy if using third-party repositories. (which is not the case here but anyway).
* This approach mixes a fancy feature (ticking checkboxes) and a core feature (merging PRs). This does not a provide a strong security because anyone may be able to tick the checkbox while the PR is not merged.

A pull approach may be more appropriate:
* Indeed, the feature is only implemented in the repositories we want.
* In terms of permissions, the action only requires write permissions on the repository where it is implemented.
  
However, this approach has a major drawback: it is not immediate. We may need to implement a schedule (a cron for instance) to run periodically the action.

Last but not least, there is an implementation of this *pull* approach (without the ticking thing but the core is there) : [z0al/dependent-issues](https://github.com/z0al/dependent-issues)
