# Potts On This Day Email for webtrees

Potts On This Day Email sends personalised daily anniversary emails to registered
webtrees users who opt in from My Page.

## Features

- Includes births, deaths and marriages occurring on the current day.
- Lets each registered user opt in or out.
- Uses each subscriber's linked or selected individual as the relationship root.
- Filters events by a configurable relationship distance.
- Can optionally limit results to people who are still living.
- Builds each email using the subscriber's webtrees privacy permissions.
- Sends only when matching events exist.
- Adds age, death and wedding-anniversary details.
- Links relationship descriptions to the webtrees relationship chart.
- Shows administrators subscriber and delivery status.
- Supports an optional integration with Potts Historical Facts.
- Adds starter translations for Dutch, German, French, Spanish, Polish and Portuguese.
- Uses webtrees' native `EmailService`.

## Requirements

- webtrees 2.2.6 or a compatible webtrees 2.2.x release
- PHP 8.3 or later
- A scheduler capable of requesting a secure HTTPS URL once per day
- Correctly configured webtrees email delivery

## Installation

Use the release ZIP attached to the GitHub release, not GitHub's automatic "Source code" ZIP, when installing into webtrees.

1. Download and extract the release ZIP.
2. Upload the `potts_on_this_day_email` folder to `modules_v4`.
3. In webtrees, go to **Control panel > Modules > All modules**.
4. Enable **Potts On This Day Email**.
5. Add the block to My Page.
6. Select the module's settings icon under **Control panel > Modules > All modules**.
7. Choose the family tree and save the sender name, sender email and daily timezone.
8. Configure authenticated SMTP under **Control panel > Website > Sending email**.
9. Add the block to My Page and send yourself a test email.
10. Return to the module settings page, prepare the secure scheduler link and configure it to run daily.

The final module path should be:

`modules_v4/potts_on_this_day_email/module.php`

## Email Delivery

The module does not open its own SMTP connection and does not use PHP `mail()`.
It passes every message to the webtrees `EmailService`.

Configure delivery under:

**Control panel > Website > Sending email**

Authenticated SMTP is strongly recommended. If webtrees uses the hosting server's
local sendmail service, receiving providers may reject messages because of the
server IP address or its reputation. A successful test sent from Outlook, Gmail
or another mail application does not test the route used by webtrees.

The sender address entered on the module settings page should be accepted by the
configured SMTP provider. Some providers require it to match the authenticated
mailbox or an approved alias.

## Administrator Settings

Open:

**Control panel > Modules > All modules > Potts On This Day Email settings**

The administrator page provides:

- family-tree selection
- sender name and sender email
- daily timezone
- authenticated SMTP guidance
- scheduler URL and cron command
- scheduler-token regeneration
- delivery status and diagnostics
- registered-user subscriber status

Site-wide controls are kept out of My Page. The My Page block contains only each
signed-in user's personal subscription and relationship settings.

## Scheduling

webtrees 2.2.x does not provide a general exact-time scheduler for custom modules.
The module therefore provides a token-protected HTTPS endpoint that can be called
by cPanel cron, another hosting scheduler or an external uptime/scheduling service.

The administrator settings page displays both the secure URL and a `curl` command. Keep the URL
private because it contains the scheduler token.

The settings page cannot normally create the scheduled task itself. A PHP module
does not usually have permission to modify the hosting account's cron table,
Plesk scheduler, Windows Task Scheduler or an external service. It prepares
everything the scheduler needs and provides instructions for:

- cPanel Cron Jobs
- Plesk Scheduled Tasks using **Fetch a URL**
- DirectAdmin Cron Jobs
- Linux `crontab` over SSH
- Windows Task Scheduler
- NAS task schedulers
- external URL scheduling services
- requesting assistance from the hosting provider

For a Linux server with SSH access, a typical entry for 6:00 am is:

```cron
0 6 * * * /usr/bin/curl -L -sS --fail 'SECURE_SCHEDULER_URL' >/dev/null 2>&1
```

For Plesk, choose **Fetch a URL** and paste the secure scheduler URL directly.
For other control panels, use their Cron Jobs or Scheduled Tasks feature and
paste the generated command.

If the hosting account has no scheduler, use a reputable external URL scheduling
service to request the secure HTTPS URL once each day. Keep the complete URL
private because it contains the token.

### Scheduler Timezone

The timezone saved in this module determines which calendar date is treated as
today. It does not change the timezone used by cron or an external scheduler.
Check the server, hosting panel or external service timezone when choosing the
delivery time.

The endpoint:

- runs at most once per local calendar day unless `force=1` is added
- prevents overlapping runs with a file lock
- skips subscribers who have no matching events
- records a diagnostic log in `data/scheduler.log`

## User Settings

Each signed-in user can choose:

- whether to receive the daily email
- their root individual
- whether relationship filtering is enabled
- the maximum relationship distance
- whether to include only events for people who are still living

The module reads the user's current account email address at delivery time.

## Privacy

Each subscriber's email is generated temporarily under that subscriber's
webtrees user context. Living and private records are included only when the
subscriber is allowed to view them in webtrees.

Do not publish or commit files generated in the module's `data` directory.
They can contain subscriber details, delivery history and a scheduler token.

## Optional Historical Context

If `potts_historical_facts` is installed beside this module, matching regional
historical facts can be included in emails. Potts On This Day Email continues to
work normally when that module is absent.

## Upgrading

Preserve the existing `data` directory when replacing module files. It contains
site settings, subscriber preferences and delivery status.

## Known Limitations

- Exact daily delivery requires a scheduler.
- Delivery reputation, SPF, DKIM and DMARC are controlled by the site's email
  provider and DNS configuration, not by this module.
- Starter translations are included for Dutch, German, French, Spanish, Polish and Portuguese, but native-speaker corrections are welcome.
- Version 1.0.1 is a maintenance release for webtrees 2.2.6 and later 2.2.x releases.

## Licence

GPL-3.0-or-later. See `LICENSE`.

## Support

Report bugs through GitHub Issues. Include the webtrees version, PHP version,
theme, scheduler result and relevant webtrees log entries. Remove email
addresses, tokens and private genealogy data before posting logs.
