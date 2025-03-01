---
title: admin/reports API methods
description: Perform moderation actions with reports.
menu:
  docs:
    name: admin/reports
    parent: methods-admin
    identifier: methods-admin-reports
aliases: [
  "/methods/admin/reports",
  "/api/methods/admin/reports",
]
---

<style>
#TableOfContents ul ul ul {display: none}
</style>

## View all reports {#get}

```http
GET https://mastodon.example/api/v1/admin/reports HTTP/1.1
```

View information about all reports.

**Returns:** Array of [Admin::Report]({{< relref "entities/Admin_Report" >}})\
**OAuth:** User token + `admin:read:reports`\
**Permissions:** Manage Reports\
**Version history:**\
2.9.1 - added\
4.0.0 - support custom roles and permissions

#### Request

##### Headers

Authorization
: {{<required>}} Provide this header with `Bearer <user token>` to gain authorized access to this API method.

##### Query parameters

resolved
: Boolean. Filter for resolved reports?

account_id
: String. Filter for reports filed by this account.

target_account_id
: String. Filter for reports targeting this account.

#### Response
##### 200: OK

```json
[
	{
		"id": "3",
		"action_taken": false,
		"action_taken_at": null,
		"category": "spam",
		"comment": "",
		"forwarded": false,
		"created_at": "2022-09-09T21:19:23.085Z",
		"updated_at": "2022-09-09T21:19:23.085Z",
		"account": {
			"id": "108965218747268792",
			"username": "admin",
			"domain": null,
			"created_at": "2022-09-08T22:48:07.985Z",
			"email": "admin@mastodon.local",
			// ...
			"account": {
				"id": "108965218747268792",
				"username": "admin",
				"acct": "admin",
				// ...
			}
		},
		"target_account": {
			"id": "108965430868193066",
			"username": "goody",
			"domain": null,
			"created_at": "2022-09-08T23:42:04.731Z",
			"email": "goody@mastodon.local",
			// ...
			"account": {
				"id": "108965430868193066",
				"username": "goody",
				"acct": "goody",
				// ...
			}
		},
		"assigned_account": null,
		"action_taken_by_account": null,
		"statuses": [],
		"rules": []
	},
	// ...
]
```

##### 403: Forbidden

Authorized user is not allowed to perform this action, or invalid or missing Authorization header

```json
{
  "error": "This action is not allowed"
}
```

---

## View a single report {#get-one}

```http
GET https://mastodon.example/api/v1/admin/reports/:id HTTP/1.1
```

**Returns:** [Admin::Report]({{< relref "entities/Admin_Report" >}})\
**OAuth:** User token + `admin:read:reports`\
**Permissions:** Manage Reports\
**Version history:**\
2.9.1 - added\
4.0.0 - support custom roles and permissions

#### Request

##### Path parameters

:id
: {{<required>}} String. The ID of the Report in the database.

##### Headers

Authorization
: {{<required>}} Provide this header with `Bearer <user token>` to gain authorized access to this API method.

#### Response
##### 200: OK

```json
{
  "id": "2",
  "action_taken": true,
  "action_taken_at": "2022-09-09T21:38:54.679Z",
  "category": "spam",
  "comment": "",
  "forwarded": false,
  "created_at": "2022-09-09T21:19:44.021Z",
  "updated_at": "2022-09-09T21:38:54.681Z",
  "account": {
    "id": "108965218747268792",
    "username": "admin",
    "domain": null,
    "created_at": "2022-09-08T22:48:07.985Z",
    "email": "admin@mastodon.local",
    // ...
    "account": {
      "id": "108965218747268792",
      "username": "admin",
      "acct": "admin",
      // ...
    }
  },
  "target_account": {
    "id": "108965430868193066",
    "username": "goody",
    "domain": null,
    "created_at": "2022-09-08T23:42:04.731Z",
    "email": "goody@mastodon.local",
    // ...
    "account": {
      "id": "108965430868193066",
      "username": "goody",
      "acct": "goody",
      // ...
    }
  },
  "assigned_account": null,
  "action_taken_by_account": {
    "id": "108965218747268792",
    "username": "admin",
    "domain": null,
    "created_at": "2022-09-08T22:48:07.985Z",
    "email": "admin@mastodon.local",
    // ...
    "account": {
      "id": "108965218747268792",
      "username": "admin",
      "acct": "admin",
      // ...
    }
  },
  "statuses": [],
  "rules": []
}
```

##### 403: Forbidden

Authorized user is not allowed to perform this action, or invalid or missing Authorization header

```json
{
  "error": "This action is not allowed"
}
```

---

## Update a report {#update}

```http
PUT https://mastodon.example/api/v1/admin/reports/:id HTTP/1.1
```

Change metadata for a report.

**Returns:** [Admin::Report]({{< relref "entities/Admin_Report" >}})\
**OAuth:** User token + `admin:write:reports`\
**Permissions:** Manage Reports\
**Version history:**\
3.5.0 - added\
4.0.0 - support custom roles and permissions

#### Request

##### Path parameters

:id
: {{<required>}} String. The ID of the Report in the database.

##### Headers

Authorization
: {{<required>}} Provide this header with `Bearer <user token>` to gain authorized access to this API method.

##### Form data parameters

category
: String. Change the classification of the report to `spam`, `violation`, or `other`.

rule_ids[]
: Array of Integer. For `violation` category reports, specify the ID of the exact rules broken. Rules and their IDs are available via [GET /api/v1/instance/rules]({{< relref "methods/instance#rules" >}}) and [GET /api/v1/instance]({{< relref "methods/instance#get" >}}).

#### Response
##### 200: OK

The report category and/or rule IDs should now be updated.

```json
{
  "id": "3",
  "action_taken": false,
  "action_taken_at": null,
  "category": "other",
  // ...
  "rules": []
}
```

##### 403: Forbidden

Authorized user is not allowed to perform this action, or invalid or missing Authorization header

```json
{
  "error": "This action is not allowed"
}
```

---

## Assign report to self {#assign_to_self}

```http
POST https://mastodon.example/api/v1/admin/reports/:id/assign_to_self HTTP/1.1
```

Claim the handling of this report to yourself.

**Returns:** [Admin::Report]({{< relref "entities/Admin_Report" >}})\
**OAuth:** User token + `admin:write:reports`\
**Permissions:** Manage Reports\
**Version history:**\
2.9.1 - added\
4.0.0 - support custom roles and permissions

#### Request

##### Path parameters

:id
: {{<required>}} String. The ID of the Report in the database.

##### Headers

Authorization
: {{<required>}} Provide this header with `Bearer <user token>` to gain authorized access to this API method.

#### Response
##### 200: OK

The report should now be assigned to you, or it was already assigned to you.

```json
{
  "id": "3",
  "action_taken": false,
  "action_taken_at": null,
  "category": "other",
  "comment": "",
  "forwarded": false,
  "created_at": "2022-09-09T21:21:01.204Z",
  "updated_at": "2022-09-11T14:39:01.531Z",
  // ...
  "assigned_account": {
    "id": "108965218747268792",
    "username": "admin",
    "domain": null,
    "created_at": "2022-09-08T22:48:07.985Z",
    "email": "admin@mastodon.local",
    // ...
    "account": {
      "id": "108965218747268792",
      "username": "admin",
      "acct": "admin",
      // ...
    }
  },
  "action_taken_by_account": null,
  "statuses": [],
  "rules": []
}
```

##### 403: Forbidden

Authorized user is not allowed to perform this action, or invalid or missing Authorization header

```json
{
  "error": "This action is not allowed"
}
```

---

## Unassign report {#unassign}

```http
POST https://mastodon.example/api/v1/admin/reports/:id/unassign HTTP/1.1
```

Unassign a report so that someone else can claim it.

**Returns:** [Admin::Report]({{< relref "entities/Admin_Report" >}})\
**OAuth:** User token + `admin:write:reports`\
**Permissions:** Manage Reports\
**Version history:**\
2.9.1 - added
4.0.0 - support custom roles and permissions

#### Request

##### Path parameters

:id
: {{<required>}} String. The ID of the Report in the database.

##### Headers

Authorization
: {{<required>}} Provide this header with `Bearer <user token>` to gain authorized access to this API method.

#### Response
##### 200: OK

The report should no longer be assigned to you, or it was already not assigned to anyone.

```json
{
  "id": "3",
  "action_taken": false,
  "action_taken_at": null,
  "category": "other",
  "comment": "",
  "forwarded": false,
  "created_at": "2022-09-09T21:21:01.204Z",
  "updated_at": "2022-09-11T14:39:01.531Z",
  // ...
  "assigned_account": null,
  "action_taken_by_account": null,
  "statuses": [],
  "rules": []
}
```

##### 403: Forbidden

Authorized user is not allowed to perform this action, or invalid or missing Authorization header

```json
{
  "error": "This action is not allowed"
}
```

---

## Mark report as resolved {#resolve}

```http
POST https://mastodon.example/api/v1/admin/reports/:id/resolve HTTP/1.1
```

Mark a report as resolved with no further action taken.

**Returns:** [Admin::Report]({{< relref "entities/Admin_Report" >}})\
**OAuth:** User token + `admin:write:reports`\
**Permissions:** Manage Reports\
**Version history:**\
2.9.1 - added\
4.0.0 - support custom roles and permissions

#### Request

##### Path parameters

:id
: {{<required>}} String. The ID of the Report in the database.

##### Headers

Authorization
: {{<required>}} Provide this header with `Bearer <user token>` to gain authorized access to this API method.

#### Response
##### 200: OK

The report is now resolved, or it was already resolved.

```json
{
  "id": "2",
  "action_taken": true,
  "action_taken_at": "2022-09-11T14:46:22.936Z",
  "category": "spam",
  "comment": "",
  "forwarded": false,
  "created_at": "2022-09-09T21:19:44.021Z",
  "updated_at": "2022-09-11T14:46:22.945Z",
  // ...
}
```

##### 403: Forbidden

Authorized user is not allowed to perform this action, or invalid or missing Authorization header

```json
{
  "error": "This action is not allowed"
}
```
---

## Reopen a closed report {#reopen}

```http
POST https://mastodon.example/api/v1/admin/reports/:id/reopen HTTP/1.1
```

Reopen a currently closed report, if it is closed.

**Returns:** [Admin::Report]({{< relref "entities/Admin_Report" >}})\
**OAuth:** User token + `admin:write:reports`\
**Permissions:** Manage Reports\
**Version history:**\
2.9.1 - added\
4.0.0 - support custom roles and permissions

#### Request

##### Path parameters

:id
: {{<required>}} String. The ID of the SOMETHING in the database.

##### Headers

Authorization
: {{<required>}} Provide this header with `Bearer <user token>` to gain authorized access to this API method.

#### Response
##### 200: OK

The report no longer has an action taken, or it already had no action taken.

```json
{
  "id": "2",
  "action_taken": false,
  "action_taken_at": null,
  "category": "spam",
  "comment": "",
  "forwarded": false,
  "created_at": "2022-09-09T21:19:44.021Z",
  "updated_at": "2022-09-11T14:42:21.855Z",
  // ...
}
```

##### 403: Forbidden

Authorized user is not allowed to perform this action, or invalid or missing Authorization header

```json
{
  "error": "This action is not allowed"
}
```

---

## See also

{{< page-relref ref="methods/admin/accounts#action" caption="POST /api/v1/admin/accounts/:id/action" >}}

{{< caption-link url="https://github.com/mastodon/mastodon/blob/main/app/controllers/api/v1/admin/reports_controller.rb" caption="app/controllers/api/v1/admin/reports_controller.rb" >}}