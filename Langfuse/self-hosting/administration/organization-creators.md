---
title: Allowlist of organization creators (self-hosted)
description: Learn how to restrict organization creation to a specific set of users in your self-hosted Langfuse deployment.
label: "Version: v3"
sidebarTitle: "Organization Creators (EE)"
---

# Allowlist of organization creators

This is only available in the Enterprise Edition. Please add your [license key](/self-hosting/license-key) to activate it.

By default, all users who have access to a Langfuse instance can create new organizations.

If you want to restrict organization creation to a specific set of users, you can use the `LANGFUSE_ALLOWED_ORGANIZATION_CREATORS` environment variable. In some organizations, there is a certain set of users who create new organizations and then provision access to the single organization or project via [RBAC](/docs/rbac).

```bash filename=".env"
LANGFUSE_ALLOWED_ORGANIZATION_CREATORS=user1@langfuse.com,user2@langfuse.com
```

## Support

If you experience any issues when self-hosting Langfuse, please:

1. Check out [Troubleshooting & FAQ](/self-hosting/troubleshooting-and-faq) page.
2. Use [Ask AI](/ask-ai) to get instant answers to your questions.
3. Ask the maintainers on [GitHub Discussions](/gh-support).
4. Create a bug report or feature request on [GitHub](/issues).

