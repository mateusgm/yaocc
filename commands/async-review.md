# PRD
$ARGUMENTS

# Instructions
Spin up a parallel sub agent for each completed story:
- create a new git worktree in .trees/<story-name> to not clash with existing work
- delegate @reviewer to execute the prompt <prompt-review>. DO NOT GIVE ANY ADDITIONAL CONTEXT OR INSTRUCTIONS.
- save the output to docs/{spec-name}/{story-id}/review.md on the branch of the story
- push to origin

<prompt-review>
    # To review
    {EXTRACTED STORY DESCRIPTION FROM PRD INCLUDING TECHNICAL AND FUNCTIONAL REQUIREMENTS WITHOUT ANY ADDITIONAL CONTEXT OR INSTRUCTIONS}
</prompt-review>
