---
category: 'members'
locale: 'en'
updatedAt: '2026-10-30'
---

# FAQ — Teams & Permissions

## What roles are available?

Both Workspaces and Projects use four roles.

| Role | What they can do |
| ---- | ---------------- |
| Owner | Everything. Only Owners can delete Workspaces and Projects. |
| Admin | Invite members and change roles, manage billing and plans, manage Tables, Columns, API Keys, and settings |
| Editor | Create Projects, manage content, translations, variables, categories, files, and Webhooks, view usage |
| Guest | Read-only |

See [Member Roles](/docs/developers/member-roles) for detailed permissions.

## How do I invite teammates?

Go to **Workspace → Members → Invite Members** and enter email addresses and a role. You can invite several people at once by separating addresses with commas. People who already have an Onttle account are added as members immediately, and people who don't receive an invitation email. Only Owners and Admins can invite members.

## Can I invite someone who doesn't have a Google account?

Yes. You can invite any email address. Invitees can sign up with whichever method they prefer: email, Google, or GitHub. However, they must **sign up with the invited email address** to join the Workspace automatically.

## How long is an invitation valid?

Invitations are valid for **7 days** after they are sent. For an expired invitation, cancel it in the **Pending Members** tab and invite the person again.

## It says I can't invite any more members.

You have reached your plan's member limit. **Pending invitations also count toward the limit.** Cancel unused invitations, remove members who no longer need access, or upgrade your plan. Member limits by plan are Free 3, Pro 10, Pro+ 25, and unlimited for Ultra and above.

## Can I add a teammate to only a specific Project?

Yes. First invite them to the Workspace, then add them to the Project in **Project → Members** and assign a role. Only Workspace members can be added to a Project. We recommend giving external collaborators the Guest or Editor role.

## How do I transfer Workspace ownership to someone else?

1. In **Workspace → Members**, change the role of the member taking over to **Owner**.
2. Change your own role to Admin or lower, or leave the Workspace.

A Workspace must have at least one Owner, so the last Owner cannot change their role or leave.

## What happens to my team Workspace if I delete my account?

**Workspaces where you are an Owner are deleted along with their Projects, contents, and files**, and their subscriptions are canceled. This applies even if there are other Owners. To keep a team Workspace, transfer ownership as described above and step down from Owner before deleting your account. Workspaces where you are not an Owner are kept, and only your membership is removed.
