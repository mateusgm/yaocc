Spin up a parallel sub agent for each completed story in $ARGUMENTS:
- create a new git worktree in .trees/<story-name> to not clash with existing work
- use the `review` skill to review the work that was done
- save the output to docs/{spec-name}/{story-id}/review.md on the branch of the story
- push to origin
