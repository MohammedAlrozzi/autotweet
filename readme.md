### GH Twitter Queue Manager

This little project is the spiritual successor to a similar project I did to automatically update Twitter using an image from a queue that posts to my 365 site and my Twitter feed. This particular feed was to create a simple UI to manage my tweet queue directly on GH without needing any supporting services. This combines the GH API to manage the queue through a Web App and GH Actions to automate the posts to Twitter.

The GUI for the application is in the `index.html`. The index.html can be hosted anywhere. All the dependencies are included in the index. html file, so move it to a web server and load it in a browser. The first time you run it, you'll need to configure it.


Categories is a comma delimited list of categories.
categories : "Category1, Category2"

Your GH username used to log on.
username:"user"

Your password is a [GH Pat Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token).
pw : "ghp_abc123"

The repo you wan to hosts this in.
repo : "gh-twitter"

This is the name used on the commits from the apps.
name : "John Doe"

This is the email used on the commits from the apps.
email : "john.doe@example.com"		

Also, include a file called `tweets.json` in the root of the target repo.

```
[
  {
    "category": "shared",
    "evergreen": false,
    "tweet": "This is my tweet.",
    "guid": "11509dbf-8cbf-4c63-90f7-f6a17927737c"
  },
  {
    "guid": "6eb18a3d-cf2a-4a9c-96b1-8007133ac1da",
    "category": "tech",
    "evergreen": true,
    "tweet": "#StarSchemas is a way to organize data for performant queries. In this video, we'll look at the essence of a start schema, then look at how one can be implemented using Data Factory pipelines, data flows, and a #DataLake.\n\nhttps://youtu.be/_SxpkmwXtvo"
  }
 ]
 ```
 
Once the application is configured, set up the automation with a GH Action. The Action uses a cron to schedule the jobs. You can configure the cron using [this site](https://crontab.guru/#0_11,14_*_*_*). Replace the `cron` parameter when you want to run your posts.

You'll also need to [get application credentials for the Twitter API](https://developer.twitter.com/en/docs/apps/overview). These credentials are needed to make the tweets work. [Store these in a project's secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-a-repository) for each of the variables TW_CONSUMER_KEY, TW_CONSUMER_SECRET, TW_ACCESS_TOKEN_KEY and TW_ACCESS_TOKEN_SECRET as appropriate.

Create as many actions as you would like for each category you want to post. Change the line `npm install && node twitter.js YOURCATEGORY` with your category name from your app.

 
```
name: Lazy Tweeter Tech

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  schedule:
    - cron:  '0 11,14 * * *'
  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

jobs:
  job1:
    name: Post something to Twitter
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2.3.2

      - name: Post something to Twitter
        env:
          TW_CONSUMER_KEY: ${{ secrets.TW_CONSUMER_KEY}}
          TW_CONSUMER_SECRET: ${{ secrets.TW_CONSUMER_SECRET }}
          TW_ACCESS_TOKEN_KEY: ${{ secrets.TW_ACCESS_TOKEN_KEY }}
          TW_ACCESS_TOKEN_SECRET: ${{ secrets.TW_ACCESS_TOKEN_SECRET }}
        run: |          
          npm install && node twitter.js tech
      - name: Commit and push changes
        run: |
          git config --global user.name "theonemule"
          git config --global user.email "blaize@example.com"
          git add -A
          git commit -m "New Tweet"
          git push          
```
 