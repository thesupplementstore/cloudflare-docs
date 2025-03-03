---
pcx_content_type: concept
weight: 3
title: Embedded
meta:
  title: Embedded function calling
---

{{<heading-pill style="beta">}} Embedded function calling {{</heading-pill>}}

Cloudflare has a unique [embedded function calling](https://blog.cloudflare.com/embedded-function-calling) feature that allows you to execute function code alongside your tool call inference. Our npm package [`@cloudflare/ai-utils`](https://www.npmjs.com/package/@cloudflare/ai-utils) is the developer toolkit to get started.

Embedded function calling can be used to easily make complex agents that interact with websites and APIs, like using natural language to create meetings on Google Calendar, saving data to Notion, automatically routing requests to other APIs, saving data to an R2 bucket - or all of this at the same time. All you need is a prompt and an OpenAPI spec to get started.

{{<Aside type="warning" header="REST API support">}}

Embedded function calling depends on features native to the Workers platform. This means that embedded function calling is only supported via [Cloudflare Workers](/workers-ai/get-started/workers-wrangler/), not via the [REST API](/workers-ai/get-started/rest-api/).

{{</Aside>}}

## Resources

{{<directory-listing>}}
