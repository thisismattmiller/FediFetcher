# Pull missing responses into Mastodon

This GitHub repository provides a GitHub action runs every 10 mins, and has two parts:

1. It can [pull remote replies into your instance](https://blog.thms.uk/2023/03/pull-missing-responses-into-mastodon?utm_source=github), using the Mastodon API. That part itself has two parts:
   1. It gets remote replies to posts that users on your instance have already replied to during the last `REPLY_INTERVAL_IN_HOURS` hours, and adds them to your own server.
   2. It gets remote replies to the last `HOME_TIMELINE_LENGTH` posts from your home timeline, and adds them to your own server.
2. It can also [backfill posts](https://blog.thms.uk/2023/03/backfill-recently-followed-accounts?utm_source=github) from the last `MAX_FOLLOWINGS` users that you have followed.

Each part can be disabled completely, and all of the values are configurable.

**Be aware, that this script may run for a long time, if these values are too high.** Experiment a bit with what works for you, by starting with fairly small numbers (maybe `HOME_TIMELINE_LENGTH = 200`, `REPLY_INTERVAL_IN_HOURS = 12`) and increase the numbers as you see fit.

For full context and discussion on why this is needed, read the following tow blog posts: 

- The original announcement post: [Pull missing responses into Mastodon](https://blog.thms.uk/2023/03/pull-missing-responses-into-mastodon?utm_source=github)
- The announcement for v3.0.0: [Pull missing posts from recently followed accounts into Mastodon](https://blog.thms.uk/2023/03/backfill-recently-followed-accounts?utm_source=github)

## Setup

### 1) Get the required access token:

1. In Mastodon go to Preferences > Development > New Application
   1. give it a nice name
   2. enable `read:search`, `read:statuses` and `admin:read:accounts `
   3. Save
   4. Copy the value of `Your access token`

### 2) Configure and run the GitHub action

1. Fork this repository
2. Add your access token:
   1.  Go to Settings > Secrets and Variables > Actions
   2.  Click New Repository Secret
   3.  Supply the Name `ACCESS_TOKEN` and provide the Token generated above as Secret
3. Provide the required environment variables, to configure your Action:
   1. Go to Settings > Environments
   2. Click New Environment
   3. Provide the name `Mastodon`
   4. Add the following Environment Variables:
      1. Required for all parts of the script:
         - `MASTODON_SERVER`: The domain only of your mastodon server (without `https://` prefix) e.g. `mstdn.thms.uk`. 
      2. Required to pull in remote replies:
         - `HOME_TIMELINE_LENGTH`: An integer number. E.g. `200`. (See above for explanation.) Set to `0` to disable this part of the script.
         - `REPLY_INTERVAL_IN_HOURS`: An integer number. E.g. `24`. (See above for explanation). Set to `0` to disable this part of the script.
      3. Required to backfill posts from your last followings (new in v3.0.0):
         - `MAX_FOLLOWINGS`: An integer number representing how many of your last followings you want to backfill. (e.g. `80`). Leave blank to disable this part of the script.
         - `USER`: The username of the user whose followings you want to pull in (e.g. `michael` for the user `@michael@thms.uk`). Leave blank to disable this part of the script.
4. Finally go to the Actions tab and enable the action. The action should now automatically run approximately once every 10 min. 

## Acknowledgments

This script is mostly taken from [Abhinav Sarkar](https://notes.abhinavsarkar.net/2023/mastodon-context), with just some additions and alterations. Thank you Abhinav!