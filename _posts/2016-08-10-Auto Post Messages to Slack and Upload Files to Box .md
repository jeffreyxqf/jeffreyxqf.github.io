---
layout: post
title: Auto Post Messages to Slack & Upload Files to Box
tags: DevOps Slack Box cURL
category: DevOps
comments: true
---

Use Box & Slack API to Upload Files or Post messages without acessing the website.

- Send Message to Slack :


         curl -X POST -H "Content-type: application/json" --data "{\"text\":\"This is a line of text.\r\nAnd this is another one.\"}"

         https://hooks.slack.com/services/T1D49Q4P3/B1ZTZB1TM/authorization_token

Reference :

       https://api.slack.com/incoming-webhooks#sending_messages

       https://support.codebasehq.com/articles/notification-services/slack-notifications


- Upload File to Box : 

        curl "https://upload.box.com/api/2.0/files/content" -H "Authorization: Bearer authorization_token"
         -X POST -F attributes="{\"name\":\"UploadFile.html\", \"parent\":{\"id\":\"0\"}}" -F file=@UploadFile.html

Reference :

    https://docs.box.com/docs/getting-started-box-integration

    cURL on Windows :

    http://support.gnip.com/articles/curl-on-win7.html
