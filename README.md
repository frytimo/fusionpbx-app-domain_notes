# FusionPBX Domain Notes

Domain Notes is a small FusionPBX application that adds a dashboard note area for the current domain. It stores one note per domain and lets authorized users view and update that note from the FusionPBX dashboard.

## Features

- Adds a **Notes** dashboard widget for domain-level notes.
- Stores notes in the `v_domain_notes` table.
- Associates each note with the active FusionPBX `domain_uuid`.
- Supports add, view, and edit permissions.
- Returns JSON status responses when notes are updated.
- Includes multilingual status labels for update success and failure messages.

## Repository

```text
https://github.com/frytimo/fusionpbx-app-domain_notes
```

## Requirements

- FusionPBX
- PHP supported by your FusionPBX installation
- FusionPBX database layer
- Access to the FusionPBX app directory, usually:

```text
/var/www/fusionpbx/app
```

## Installation

Clone or copy this repository into your FusionPBX `app` directory as `domain_notes`.

```bash
cd /var/www/fusionpbx/app
git clone https://github.com/frytimo/fusionpbx-app-domain_notes.git domain_notes
```

Then run the normal FusionPBX upgrade steps from the web interface:

1. Sign in as a superadmin.
2. Go to **Advanced > Upgrade**.
3. Run **App Defaults**.
4. Run **Schema**.
5. Run **Menu Defaults** if needed.
6. Log out and back in if permissions or menu entries do not appear immediately.

## App Defaults

The application is registered as:

| Setting | Value |
| --- | --- |
| Name | Domain Notes |
| Version | 1.0 |
| License | Mozilla Public License 1.1 |
| App UUID | `25351d47-2ca6-405b-b989-1b8f2f899dff` |

## Permissions

The app defines the following permissions:

| Permission | Purpose | Default Group |
| --- | --- | --- |
| `domain_note_view` | View domain notes | `superadmin` |
| `domain_note_add` | Add domain notes | `superadmin` |
| `domain_note_edit` | Edit domain notes | `superadmin` |

After installing, confirm that the appropriate groups have these permissions in FusionPBX.

## Database Table

The app defines the `v_domain_notes` table.

| Field | Purpose |
| --- | --- |
| `domain_note_uuid` | Primary key for the note record |
| `domain_uuid` | Links the note to a FusionPBX domain |
| `domain_note_text` | The note body |
| `insert_user` | User UUID that created the record |
| `insert_date` | Date/time the record was created |
| `update_user` | User UUID that last updated the record |
| `update_date` | Date/time the record was last updated |

Conceptually, this is like a sticky note attached to a FusionPBX domain rather than to a user or extension.

## Dashboard Widget

The app includes dashboard resources intended to display the domain note on the FusionPBX dashboard.

Relevant files:

```text
resources/dashboard/config.php
resources/dashboard/domain_notes.php
```

The widget reads the current `domain_uuid` from the session and retrieves the note from `v_domain_notes`.

## Update Endpoint

The note update handler is:

```text
resources/domain_note_edit.php
```

It reads the submitted note from the `domain_note` request value, checks the current domain from the session, then inserts or updates the matching `v_domain_notes` record.

The endpoint returns a JSON response similar to:

```json
{
	"status": "Note Updated",
	"note": "Example note text"
}
```

If the save fails, the status is returned as:

```json
{
	"status": "Update Failed",
	"note": "Example note text"
}
```

## File Structure

```text
fusionpbx-app-domain_notes/
├── app_config.php
├── app_languages.php
└── resources/
	├── classes/
	│   └── domain_notes.php
	├── dashboard/
	│   ├── config.php
	│   └── domain_notes.php
	└── domain_note_edit.php
```

## Troubleshooting

### Dashboard widget does not appear

Run **Advanced > Upgrade > App Defaults** and confirm the dashboard record exists. Also confirm the user group has the required `domain_note_view` permission.

### Save button does not update the note

Confirm the signed-in user has `domain_note_edit` permission and that the `v_domain_notes` table exists after running the schema upgrade.

### Database table is missing

Run **Advanced > Upgrade > Schema** as a superadmin.

### Dashboard path mismatch

If the widget is registered but does not load, verify the dashboard path in the dashboard configuration matches the installed app directory and file name.

For an install path like:

```text
/var/www/fusionpbx/app/domain_notes
```

verify the dashboard path points to the matching dashboard PHP file.

## Development Notes

- The app currently stores one note per domain.
- The class file `resources/classes/domain_notes.php` exists as a placeholder for future domain note logic.
- The update handler uses FusionPBX database save conventions and the app UUID from `app_config.php`.
- The language file contains localized labels for update success and failure messages.

## License

This app follows the Mozilla Public License 1.1, matching the license declared in `app_config.php`.
