# Draft & Scheduled Post Attachment Security — Engineering Scoping Document

**Date:** March 10, 2026
**Feature Area:** Viva Engage File Attachments

---

## 1. Problem Statement

When a user attaches a file to a post in Viva Engage, that file is immediately uploaded to its final storage location — OneDrive for Storyline posts and the community's SharePoint library for Community posts. This happens before the post is published, meaning files attached to draft or scheduled posts are accessible to community members through the underlying storage even while the post is still in progress.

A user with sufficient intent — browsing the community's Files tab or navigating SharePoint directly — can discover and access these files before the author ever intended them to be visible. For scheduled posts, this window of exposure can last days. The risk is highest for sensitive communications: upcoming announcements, unredacted drafts, or posts containing information that isn't meant to be public until a specific time.

This problem was first identified in 2022 in the context of Draft Posts (Adrian Popa, Feb 2022), but was never shipped. It is now compounded by Scheduled Posts, which extend the exposure window further and introduce a new category of time-sensitive content that must remain restricted until its scheduled publish time.

Historically, the issue has gone largely unnoticed because users tend to post quickly after uploading, rarely abandon posts with attachments, and rarely browse a community's files tab. However, as draft and scheduled post usage grows, so does the frequency and duration of unintended file exposure.

---

## 2. Goals

**Goals:**
- Prevent community members from accessing files attached to draft or scheduled posts until the post is published
- Restrict visibility of files attached to abandoned posts to their author and delegates only
- Ensure that upon publish (immediate or scheduled), files become accessible to the intended audience immediately and without user action
- Handle the scheduled post case explicitly: files must remain restricted until the scheduled publish fires, not when the post is initially created
- Support native-mode networks only

**Non-goals:**
- Non-native network support
- Retroactively securing attachments on already-published posts
- Automated cleanup or deletion of abandoned files (may be a future follow-up)
- Changing the final storage destination — ODB for Storyline and SPO for Communities remains the same
- Inline images or link preview content — file attachments only

---

## 3. Success Metrics

| Metric | Description |
|---|---|
| Pre-publish file exposure incidents | Zero unauthorized access to draft or scheduled post attachments, measured via SPO/ODB access logs |
| Attachment restriction coverage | % of draft and scheduled post attachments correctly stored in the draft directory with restricted ACLs at upload time |
| Publish success rate | % of post publishes where the file move and ACL clear operation completes successfully |
| ACL limit proximity | Per-community tracking of SPO ACL usage relative to the 5k soft and 50k hard limits, with alerting before the soft limit is reached |
| Abandoned file access | % of abandoned-post attachments that are inaccessible to non-authors after abandonment (future) |

---

## 4. High Level Requirements

| # | Requirement | Priority | Considerations |
|---|---|---|---|
| 1 | On file upload to a draft or scheduled post, store the file in a restricted "Drafts" subdirectory within the same storage surface — `Apps/Yammer/Drafts/<guid>/filename.ext` for SPO and `Attachments/Storyline/Drafts/<guid>/filename.ext` for ODB — with ACLs granting access only to the author, delegates, and tenant admins | P1 | The `<guid>` subdirectory scopes ACLs per post to limit total ACL usage |
| 2 | On post publish (immediate or when a scheduled post fires), move attachments from the draft directory to their final location and clear the custom ACLs so files inherit the default community or Storyline permissions | P1 | The file's download URL is database-id based and must remain valid after the move; `sharepoint_web_url` in the DB must be updated to reflect the new physical path |
| 3 | After files are moved out of a draft subdirectory on publish, delete the empty `<guid>` subdirectory to reduce ACL count and minimize exposure to SPO's ACL limits | P1 | The root `Drafts/` folder must not be deletable by community admins or members |
| 4 | Community admins must NOT have read access to files in the draft directory, to prevent early visibility into draft or scheduled post content | P1 | This is an explicit exclusion from the draft directory ACL; differs from normal community admin permissions |
| 5 | On post deletion — whether a draft is discarded or a scheduled post is cancelled — clean up files from the draft directory; notify authors if a community is deleted while draft attachments are still pending | P1 | The ACL-clear-and-move (and delete) operations must be retriable; failures should not block the publish or delete action |
| 6 | The ACL-clear-and-move operation on publish must be retriable in case of transient failure, so files are never permanently hidden after a post is published | P1 | Consider a retry queue or background job; failure in this step should be alarmed |
| 7 | SPO's ACL limit (5k soft / 50k hard per document library) is not expected to be a practical blocker: the limit applies only to concurrent in-flight uploads plus the count of unpublished drafts, not total files; only 5 communities exceeded 50k total file uploads in the past 12 months | P1 | Monitor per-community ACL usage and alert before the 5k soft limit; validate that ODB does not have an equivalent limit |
| 8 | Roll out behind a feature flag, staged per cell | P1 | Rollout is the highest-risk phase; anticipate issues and have a rollback path |
| 9 | Update Viva Engage public documentation to describe how files attached to drafts and abandoned posts are handled | P2 | |
