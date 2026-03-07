
# BoundlessFi – Bug Test Report

### Environment

* Frontend running **locally**
* Backend: **staging API (`stage-api.boundlessfi.xyz`)**



# General Functionality Tests

* **Organizations page** opens and functions properly.
* **Navigation (`nav/organization`)** correctly directs to the organizations page.
* **Organization creation** works properly.
* After creating an organization, **redirect to `/organization/settings` works correctly**.
* **Organization search feature** works properly.
* **Inviting members** works correctly.
* **Canceling invites** works properly.
* **Sign out** works perfectly and redirects to the sign-in form.



# Suggestions / UX Observations

### Password Validation UX

The sign-up form does not follow a good UX pattern for password validation.

**Issue**

* If a weak password is entered, the form simply refuses submission.
* No message or warning is shown explaining that the password is weak.

**Suggestion**

* Show a clear validation message such as:

  `"Password must meet the required strength criteria"`

This would improve the sign-up user experience.



# Bugs Identified

## 1. Slug Validation Issue During Organization Creation

**Issue**

* The slug validation always displays **“Slug available”** even when the slug is already used.

**Actual Behavior**

* When attempting to create the organization, the system prevents creation because the slug already exists.
* However, the UI does not inform the user beforehand.

**Expected Behavior**

* If the slug already exists, the validation should immediately show something like:

  `"Slug already taken"`

This would prevent confusion during organization creation.



## 2. Delete / Archive Organization Not Working

**Issue**

* The **delete/archive organization feature does not work**.

**Expected Result**

* The organization should either be archived or removed depending on the selected action.

**Actual Result**

* failed to delete/archive.



## 3. Invite Link Leads to 404 Page

When inviting members to an organization, the generated invite link leads to a **404 page**.

Example invite link:

https://staging.boundlessfi.xyz/signup?invitationId=L9IXtMcqyu3EZgH40zXq4tGabPHQggoD&email=magic.cicada.umac%40hidingmail.com


**Issue**

* The invite link points to `/signup`, but the application's authentication route is actually `/auth`.

**Expected Behavior**

https://staging.boundlessfi.xyz/auth?invitationId=...


**Suggestion**

* Update the function that generates invitation URLs (`getInvitationUrl` or equivalent) to use `/auth` instead of `/signup`.



## 4. Invite Acceptance Does Not Add Member to Organization

When manually correcting the invite link to:

http://localhost:3000/auth?invitationId=L9IXtMcqyu3EZgH40zXq4tGabPHQggoD&email=magic.cicada.umac%40hidingmail.com


**Observed Behavior**

1. The user is redirected to the **sign-up form**, which is correct.
2. After signing up:

   * The user is redirected to the **home page**, not the organization page.
   * The **invite status still shows "Pending Invite"**.
   * The user is **not added as a member of the organization**.

**Expected Behavior**

* After completing sign-up:

  * The user should automatically **join the inviting organization**.
  * The invite status should change from **Pending → Accepted**.
  * The user should be redirected to the **organization page**.



## 5. Hackathon Publish Fails

### Steps to Reproduce

1. Create a hackathon draft.
2. Navigate to the **Preview tab**.
3. Click **Publish**.

**Expected Result**

* The hackathon should publish successfully.

**Actual Result**

* Publishing fails and the hackathon remains in **Draft**.

**Console Error**

PUT https://stage-api.boundlessfi.xyz/api/organizations/{orgId}/hackathons/draft/{hackathonId}/publish
404 (Not Found)


This indicates the publish endpoint may not exist or the route is incorrect.



## 6. Invalid Organization Settings Route Still Loads

While testing routes, manually navigating to a non-existent organization route still loads the organization settings page.

Example tested route:

https://staging.boundlessfi.xyz/organizations/random123/settings


**Expected Behavior**

* The application should either:

  * return a **404 page**
  * redirect to `/organizations`
  * or display **“Organization not found”**

**Actual Behavior**

* The **organization settings page still loads** even though the organization ID does not exist.

**Impact**

* Users can access invalid routes.
* This may cause confusion or incorrect organization context.

**Suggestion**

* Add validation to check if the organization ID exists before rendering the settings page.



# Summary

Overall, most core organization features such as **creation, navigation, searching, and invites** work as expected. However, several issues were identified including:

* Slug validation feedback
* Organization archive/delete not working
* Broken invite link routing
* Invite acceptance not adding members to organizations
* Hackathon publishing endpoint returning **404**
* Invalid organization routes still loading settings pages
* Password validation UX issue



