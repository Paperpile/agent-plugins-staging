---
name: send-paperpile-feedback
description: Send a bug report, friction log, feature request, testimonial, or general feedback to the Paperpile team. Use when the user wants to share feedback about Paperpile's tools or skills, or invokes this with no instruction of their own.
---

# Send Paperpile feedback

## Paperpile tone and style

Paperpile is a tool for academics. Be calm, efficient and specific, and assume a seasoned researcher in the field is reading. Be direct about what you did and did not find.

- No emojis.
- No hype or overly-enthusiastic conclusions.
- Bold and italics are fine where they aid scanning
- Say "I could not find it" rather than padding a weak result.

This skill helps the user send structured feedback — a bug report, workflow friction, a feature request, or something that worked well — to the Paperpile team via the `send_paperpile_feedback` MCP tool.

## When invoked with no argument

If the user invokes this skill without providing specific feedback, do not guess or submit anything. Briefly explain:

1. **How feedback submission works** — You can draft a standalone feedback report and send it to the Paperpile team using the `send_paperpile_feedback` tool, tagged with one or more categories (`Bug report`, `Friction log`, `Feature request`, `Testimonial`, `General feedback`) and any relevant Paperpile tools or skills. You will always show the drafted message for the user's approval before sending. Submitting feedback does not modify the user's Paperpile library, and while the team reads submitted reports, it is a one-way channel that does not guarantee a direct reply.
2. **Emailing support directly** — Users can also email `support@paperpile.com` directly at any time, which is the best option when they want a direct reply or help with their account.

Then ask what feedback they would like to share.

## When invoked with proposed feedback

When the user provides feedback (either when invoking the skill or in reply):

1. **Draft the feedback report for approval first.** Do not call `send_paperpile_feedback` until the user approves the draft. Write the message so it stands on its own without assuming access to the current conversation transcript, and include only the summarized context needed to make the report clear and actionable.
2. **Present the draft clearly for review:**
   - **Categories (`feedback_type`)**: one or more of `Bug report`, `Friction log`, `Feature request`, `Testimonial`, `General feedback`
   - **Relevant tools / skills** (`relevant_tools`, `relevant_skills`), if applicable
   - **Message (`message`)**: the Markdown message to be sent
3. **Ask for approval** — Ask the user to confirm sending the report as drafted or tell you what to change.
4. **Send and confirm** — Once approved, call `send_paperpile_feedback` with the agreed arguments and confirm that the report has been recorded (noting that they can also reach `support@paperpile.com` if they need a direct response).
