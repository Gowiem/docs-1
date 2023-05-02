# Neon Integration Guide

This guide will help you use Snaplet to create and subsequently restore a safe, anonymized snapshot of your production data into your Neon database. 


## Prerequisites

- You’ll need a Neon account, and an existing Neon project with a PostgreSQL database, and that database's connection string. If you haven’t set this up yet, Neon has a [tutorial](https://neon.tech/docs/tutorial/project-setup) to guide you through the getting started process.
- You’ll need a Snaplet account, and you’ll need to have connected to your existing production database and captured a snapshot of it. If you’re new to Snaplet, you can follow our [Getting Started](https://docs.snaplet.dev/getting-started/start-here) guide.


## Restoring your snapshot to Neon

### Step 1: Have a snapshot ready
Ensure you have an active snapshot of your production database - it's easiest to check this from inside Snaplet Cloud. You can create a new snapshot if your previous snapshot has been deleted, or is out of date. 

![Snaplet snapshot ready to go](/screenshots/neon/00-snaplet-snapshot.png "Snaplet snapshot ready to go")

If you haven’t previously created a snapshot at all, follow our [Getting Started](https://docs.snaplet.dev/getting-started/start-here) guide. Alternatively, you can follow along in the video guide below.

<div style={{"position":"relative","paddingBottom":"64.98194945848375%","height":"0"}}><iframe src="https://www.loom.com/embed/26f6aae49d8b425fb31358664d17e8a6" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen style={{"position":"absolute","top":"0","left":"0","width":"100%","height":"100%"}}></iframe></div>


### Step 2: CLI installation and setup

You’ll need the Snaplet CLI to be installed and setup to restore your snapshot to Neon. If you haven’t installed the Snaplet CLI, our [Getting Started](https://docs.snaplet.dev/getting-started/start-here) guide and our [Configuration](https://docs.snaplet.dev/getting-started/configuration) guide will take you through the process. 

At the point where you’re prompted to provide a target database URL, instead of using a local development database URL, provide your Neon database connection string:
![Snaplet CLI config - providing the Neon database string](/screenshots/neon/01-snaplet-cli.png "Snaplet CLI config - providing the Neon database string")


The connection string to your Neon database can be found on your Neon database dashboard:
![Neon connection string](/screenshots/neon/02-neon-conn.png "Neon connection string")

If you have installed and used the Snaplet CLI previously, jump ahead to step 4. 


### Step 3: Restore your Snapshot to Neon (new CLI users)
From within the Snaplet CLI, use the `snaplet snapshot restore` command to restore your production database snapshot to your Neon development database. 

Because you’re not restoring a snapshot to a local database, Snaplet will prompt you to confirm the overwrite, as this will result in your Neon database being overwritten with the schema and data from your snapshot. Press ‘Y’ to confirm and proceed.

![Confirmation when restoring your snapshot to Neon](/screenshots/neon/03-restore-setup.png "Restore confirmation prompt")


### Step 4: Change development database to your Neon database (existing CLI users)

If you’ve already installed the Snaplet CLI and configured Snaplet to restore snapshots to a different development database, you can change this easily by either editing the `config.json` file for your current working directory, or using the `SNAPLET_TARGET_DATABASE_URL` environment variable when running the `snaplet snapshot restore` command: 

```terminal
SNAPLET_TARGET_DATABASE_URL=postgresql://username:password@hostname:5432/database_name snaplet ss restore
```


## Conclusion
We hope this guide has helped. If you experience any problems restoring your snapshots to your Neon database, come chat to us on [Discord](https://app.snaplet.dev/chat).
