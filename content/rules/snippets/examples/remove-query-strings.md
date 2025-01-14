---
type: example
summary: Remove certain query strings from a request before passing to the origin.
goal:
  - Other
operation:
  - Request modification
product:
  - Snippets
pcx_content_type: example
title: Remove query strings before sending request to origin
layout: example
---

```js
export default {
    async fetch(request) {
        // Define the query strings you want to remove
        const queryStringsToRemove = ["utm_source", "utm_medium", "utm_campaign"];

        // Get the URL from the request
        const url = new URL(request.url);

        // Remove the specified query strings
        queryStringsToRemove.forEach(query => {
            url.searchParams.delete(query);
        });

        // Create a new request with the modified URL
        const modifiedRequest = new Request(url, request);

        // Pass the modified request to the origin
        const response = await fetch(modifiedRequest);

        return response;
    },
};
```