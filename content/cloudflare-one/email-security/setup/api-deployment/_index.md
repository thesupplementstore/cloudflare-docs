---
title: API deployment
pcx_content_type: concept
weight: 1
---

# API deployment

When you choose an API deployment, email messages only reach Email Security after they have already reached a user's inbox.

Then, through an integration with your email provider, Email Security can [auto-move messages](/cloudflare-one/email-security/auto-moves/) based on your organization's policies.

![With API deployment, messages travel through Email Security's email filter after reaching your users.](/images/email-security/deployment/api-setup/e-sec-api-deployment-diagram.png)

## Benefits

When you choose API deployment, you get the following benefits:

- Easy protection for complex email architectures, without requiring any change to mailflow operations.
- Agentless deployment for Microsoft 365.
- The initial email protection measures offered by your current email provider.

## Limitations

However, API deployment also has the following disadvantages:

- Email Security is dependent on Microsoft's Graph API, and outages will increase the message dwell time in the inbox.
- Your email provider may throttle API requests from Email Security.
- Email Security requires read and write access to mailboxes.
- Requires API support from your email provider (does not typically support on-premise providers).
- Detection rates may be lower if multiple solutions exist.
- Messages cannot be modified or quarantined.