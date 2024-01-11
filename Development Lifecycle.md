![Poarch Creek Indians Federal Services Logo](https://pcifs.com/user/images/assets/SdWFPGMerCAgnyu.svg)
# PCIFS Development Lifecycle

Whenever a customer requires a change or new development, it begins like any other IT request: with the creation of a ticket. However, unlike any standard ticket, we must know that the requestor is authorized to make said request. That is why each site we support will have a list of employees who are authorized to request web changes for that company. This list will be always kept updated in the README of that site, so that someone can easily look up who is authorized just by looking at that repository's page.

<br/>
Example README Footer:

### Authorized for Development Requests:
-----
| Employee | Title | Email | Phone |
|----------------|-------------|--------------|-----------|
Byrian Ramsey | Corporate IT Director | bramsey@pcifs.com | (251) 368-0819 x4513
Wesley Hall | Website & Intranet Developer | whall@pcifs.com  | (251) 359-3751  
<br/>


Should the requesting user not be on this list, the request should be forwarded to the entire list of authorized employees (with the requesting user copied) and approval confirmed before any work is done. At that point, a ticket can be created. The ticket # should be included on any commit messages related to that change and the SysAid ticket should be fully linked in any Pull Requests to the main branch resulting from the ticket.

## Change Requests
Changes to an existing web property after approval is gained and a ticket created in SysAid move as follows:

1. An issue should be made in Github, attached to the affected repository. If this is a part of a larger Project, an Action Item should be created in that Project & Linked to this newly created Issue.
1. A new branch should be created, linked to the Issue created in Step 1.
1. Development should be done locally, on a dev workstation using the Dev Docker Image as a local server. They can be viewed (locally only) at https://dev.sitename.com. Changesets should be kept as small as possible before being combined into the all-encompassing Pull Request.
1. When the developer is happy with the changes, the branch should be checked out on the staging server (ie. https://staging.pcifs.com) so that it can be viewed by the Senior Developer who will be signing off on a pull request. Make sure that the staging server is currently on __MAIN__ branch or it might already be staging other changes. Should it be staging other changes, coordinate with the developer who is currently staging.
1. A pull request can then be initiated. The comments should include: 
    - A link to the original SysAid ticket
    - A link to the effected page on the staging server
    - A description of the changes made
1. A senior dev will then do code review and merge the change if it passes. If it doesnt, comments as to why will be added to the pull request, and development continues.
1. Upon merge, the pull request will be run through an automated test suite. PRs that do not pass this step cannot be merged to the main branch, so development will continue.
1. After the PR has been merged and passed automated testing, a Github action will automatically update the production server & clear the cache.
1. Now, the github Issue can be closed so that the team is notified, and the SysAid ticket can be marked closed as well. A link to the merge commit in github should be added to the notes on the SysAid ticket when closing.
<br/>
<br/>

__IMPORTANT!__ -- Any changes that require documentation being updated must have either the documentation updated as part of that changeset, or a PR for the Documentation updated submitted SIMULTANEOUSLY. Otherwise the PR will be denied.




