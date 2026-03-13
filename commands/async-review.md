# PRD
$ARGUMENTS

# Instructions
1. Identify completed stories from the corresponding todo.md
2. Spin up a parallel @reviewer agent for each story to execute <prompt-review>. DO NOT GIVE ANY ADDITIONAL CONTEXT OR INSTRUCTIONS to the sub agents.
3. Summarize the results in a table

<prompt-review>
    # User story implementation to be reviewed
    {EXTRACTED STORY DESCRIPTION FROM PRD INCLUDING TECHNICAL AND FUNCTIONAL REQUIREMENTS WITHOUT ANY ADDITIONAL CONTEXT OR INSTRUCTIONS}

    Implemention Worktree: .trees/<story-name> 

    # Additional instructions (before the review)
    - create a new git worktree in .trees/<story-name> using the story branch to not clash with existing work
    - change your working direction to the worktree
    - if there is already a review in docs/{spec-name}/{story-id}/review.md just summarize the findings and skip this review
    - save the output to docs/{spec-name}/{story-id}/review.md
    - push to origin
    - delete the worktree
</prompt-review>
