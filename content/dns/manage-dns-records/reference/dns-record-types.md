---
pcx_content_type: reference
title: DNS record types
weight: 2
---

# DNS record types

This page provides information about some of the different types of DNS records that you can manage on Cloudflare. For guidance on how to add, edit, or delete DNS records, refer to [Manage DNS records](/dns/manage-dns-records/how-to/create-dns-records/).

---

## IP address resolution

At least one **IP address resolution** record is required for each domain on Cloudflare. These records are the only ones you can [proxy](/dns/manage-dns-records/reference/proxied-dns-records/) through Cloudflare.

### A and AAAA

{{<render file="_a-aaaa-definition.md">}}

These records include the following fields:

- **Name**: A subdomain or the zone apex (`@`), which must:
  - Be 63 characters or less
  - Start with a letter and end with a letter or digit
  - Only contain letters, digits, or hyphens (underscores allowed but discouraged)
- **IPv4/IPv6 address**: Your origin server address (cannot be a [Cloudflare IP](https://www.cloudflare.com/ips))
{{<Aside type="note">}}
Cloudflare uses the [canonical notation](https://www.rfc-editor.org/rfc/rfc5952.html#section-4.2) to store DNS records. This means that an `AAAA` record with content `fe80::0:0:1` is stored and returned as `fe80::1`, for example.

Alternative notations of IPv4 addresses (`1.1` for `1.0.0.1`, for example) are not supported for `A` records.
{{</Aside>}}
- **TTL**: Time to live, which controls how long DNS resolvers should cache a response before revalidating it.
  - If the **Proxy Status** is **Proxied**, this value defaults to **Auto**, which is 300 seconds.
  - If the **Proxy Status** is **DNS Only**, you can customize the value.
- **Proxy status**: For more details, refer to [Proxied DNS records](/dns/manage-dns-records/reference/proxied-dns-records/).

#### Example API call

When creating `A` or `AAAA` records [using the API](/dns/manage-dns-records/how-to/create-dns-records/#create-dns-records):

- The `content` of the records is an IP address (IPv4 for `A` or IPv6 for `AAAA`).
- The `proxied` field affects the record's [proxy status](/dns/manage-dns-records/reference/proxied-dns-records/).

{{<render file="_api-field-definitions.md">}}

```bash
---
header: Request
highlight: [8, 10]
---
curl "https://api.cloudflare.com/client/v4/zones/<ZONE_ID>/dns_records" \
--header "X-Auth-Email: <EMAIL>" \
--header "X-Auth-Key: <API_KEY>" \
--header "Content-Type: application/json" \
--data '{
  "type": "A",
  "name": "www.example.com",
  "content": "192.0.2.1",
  "ttl": 3600,
  "proxied": false
}'
```

```json
---
header: Response
---
{
  "result": {
    "id": "<ID>",
    "zone_id": "<ZONE_ID>",
    "zone_name": "example.com",
    "name": "www.example.com",
    "type": "A",
    "content": "192.0.2.1",
    "proxiable": true,
    "proxied": false,
    "ttl": 1,
    "locked": false,
    "meta": {
      "auto_added": false,
      "managed_by_apps": false,
      "managed_by_argo_tunnel": false,
      "source": "primary"
    },
    "comment": null,
    "tags": [],
    "created_on": "2023-01-17T20:37:05.368097Z",
    "modified_on": "2023-01-17T20:37:05.368097Z"
  },
  "success": true,
  "errors": [],
  "messages": []
}
```

### CNAME

{{<render file="_cname-definition.md">}}

These records include the following fields:

- **Name**: A subdomain or the zone apex (`@`), which must:
  - Be 63 characters or less
  - Start with a letter and end with a letter or digit
  - Only contain letters, digits, or hyphens (underscores are allowed but discouraged)
- **Target**: The hostname where traffic should be directed (`example.com`).
- **TTL**: Time to live, which controls how long DNS resolvers should cache a response before revalidating it.
  - If the **Proxy Status** is **Proxied**, this value defaults to **Auto**, which is 300 seconds.
  - If the **Proxy Status** is **DNS Only**, you can customize the value.
- **Proxy status**: For more details, refer to [Proxied DNS records](/dns/manage-dns-records/reference/proxied-dns-records/).

You can use `CNAME` records to point to other `CNAME` records (`www.example2.com` --> `www.example1.com` --> `www.example.com`), but the final record must point to a hostname with a valid IP address (and therefore a valid `A` or `AAAA` record) if this hostname is meant to proxy traffic.

Cloudflare uses a process called `CNAME` flattening to deliver better performance. This process supports a few features and can interact with [different setups that depend on `CNAME` records](/dns/cname-flattening/#aspects-to-keep-in-mind). Refer to the [`CNAME` flattening section](/dns/cname-flattening/) to learn more about this.

#### Example API call

When creating `CNAME` records [using the API](/dns/manage-dns-records/how-to/create-dns-records/#create-dns-records):

- The `content` of the records is a [fully qualified domain name](https://en.wikipedia.org/wiki/Fully_qualified_domain_name).
- The `proxied` field affects the record's [proxy status](/dns/manage-dns-records/reference/proxied-dns-records/)

{{<render file="_api-field-definitions.md">}}

```bash
---
header: Request
highlight: [8, 10]
---
curl "https://api.cloudflare.com/client/v4/zones/<ZONE_ID>/dns_records" \
--header "X-Auth-Email: <EMAIL>" \
--header "X-Auth-Key: <API_KEY>" \
--header "Content-Type: application/json" \
--data '{
  "type": "CNAME",
  "name": "www.example.com",
  "content": "www.another-example.com",
  "ttl": 3600,
  "proxied": false
}'
```

```json
---
header: Response
---
{
  "result": {
    "id": "<ID>",
    "zone_id": "<ZONE_ID>",
    "zone_name": "example.com",
    "name": "www.example.com",
    "type": "A",
    "content": "www.another-example.com",
    "proxiable": true,
    "proxied": false,
    "ttl": 1,
    "locked": false,
    "meta": {
      "auto_added": false,
      "managed_by_apps": false,
      "managed_by_argo_tunnel": false,
      "source": "primary"
    },
    "comment": null,
    "tags": [],
    "created_on": "2023-01-17T20:37:05.368097Z",
    "modified_on": "2023-01-17T20:37:05.368097Z"
  },
  "success": true,
  "errors": [],
  "messages": []
}
```

---

## Email authentication

These records are recommended regardless of whether your domain sends email messages. Creating [secure email records](https://blog.cloudflare.com/tackling-email-spoofing/) can help protect your domain against email spoofing.

If your domain is not used to send email messages, learn more about creating recommended [restrictive records](https://www.cloudflare.com/learning/dns/dns-records/protect-domains-without-email/).

### MX

A mail exchange (MX) record is required to deliver email to a mail server.

- [MX record syntax](https://www.cloudflare.com/learning/dns/dns-records/dns-mx-record/)
- [Create an MX record](/dns/manage-dns-records/how-to/email-records/#send-and-receive-email)

{{<render file="_api-field-definitions.md">}}

### DKIM

A DomainKeys Identified Mail (DKIM) record ensures email authenticity by cryptographically signing emails:

- [DKIM record syntax](https://www.cloudflare.com/learning/dns/dns-records/dns-dkim-record/)
- [Create a DKIM record](/dmarc-management/security-records/#create-security-records)

### SPF

A Sender Policy Framework (SPF) record lists authorized IP addresses and domains that can send email on behalf of your domain.

- [SPF record syntax](https://www.cloudflare.com/learning/dns/dns-records/dns-spf-record/)
- [Create an SPF record](/dmarc-management/security-records/#create-security-records)

### DMARC

A Domain-based Message Authentication Reporting and Conformance (DMARC) record helps generate aggregate reports about your email traffic and provide clear instructions for how email receivers should treat non-conforming emails.

- [DMARC record syntax](https://www.cloudflare.com/learning/dns/dns-records/dns-dmarc-record/)
- [Create a DMARC record](/dmarc-management/security-records/#create-security-records)

---

## Specialized records

### TXT

A [text (TXT) record](https://www.cloudflare.com/learning/dns/dns-records/dns-txt-record/) lets you enter text into the DNS system.

At Cloudflare, these are most commonly used to demonstrate domain ownership prior to issuing SSL/TLS certificates for [your domain](/ssl/edge-certificates/changing-dcv-method/) or a [Cloudflare for SaaS domain](/cloudflare-for-platforms/cloudflare-for-saas/security/certificate-management/issue-and-validate/).

You could also use these to create email authentication records, but we recommend that you use our [Email Security Wizard](/dns/manage-dns-records/how-to/email-records/#prevent-domain-spoofing) instead.

{{<render file="_api-field-definitions.md">}}

{{<Aside type="note">}}

The **Content** for each TXT record must be 2,048 characters or less.

If you have multiple TXT records with the same **Name**, there is also a limit for the sum of their **Content** characters, which must be 8,192 or less.

{{</Aside>}}

### CAA

A [Certificate Authority Authorization (CAA) record](/ssl/edge-certificates/caa-records/) specifies which Certificate Authorities (CAs) are allowed to issue certificates for a domain.

{{<render file="_api-field-definitions.md">}}

### SRV

A [service record (SRV)](https://www.cloudflare.com/learning/dns/dns-records/dns-srv-record/) specifies a host and port for specific services like voice over IP (VOIP), instant messaging, and more.

#### Example API call

{{<render file="_api-field-definitions.md">}}

```bash
---
header: Request
---
curl "https://api.cloudflare.com/client/v4/zones/<ZONE_ID>/dns_records" \
--header "X-Auth-Email: <EMAIL>" \
--header "X-Auth-Key: <API_KEY>" \
--header "Content-Type: application/json" \
--data '{
  "type": "SRV",
  "name": "_xmpp._tcp.example.com",
  "data": {
    "priority": 10,
    "weight": 5,
    "port": 5223,
    "target": "server.example.com"
  }
}'
```

```json
---
header: Response
---
{
  "result": {
    "id": "<ID>",
    "zone_id": "<ZONE_ID>",
    "zone_name": "example.com",
    "name": "_xmpp._tcp.example.com",
    "type": "SRV",
    "content": "5 5223 server.example.com",
    "priority": 10,
    "proxiable": false,
    "proxied": false,
    "ttl": 1,
    "locked": false,
    "data": {
      "port": 5223,
      "priority": 10,
      "target": "server.example.com",
      "weight": 5
    },
    "meta": {
      "auto_added": false,
      "managed_by_apps": false,
      "managed_by_argo_tunnel": false,
      "source": "primary"
    },
    "comment": null,
    "tags": [],
    "created_on": "2022-11-08T15:57:39.585977Z",
    "modified_on": "2022-11-08T15:57:39.585977Z"
  },
  "success": true,
  "errors": [],
  "messages": []
}
```

### SVCB and HTTPS

Service Binding (SVCB) and HTTPS Service (HTTPS) records allow you to provide a client with information about how it should connect to a server upfront, without the need of an initial plaintext HTTP connection.

If your domain has [HTTP/2 or HTTP/3 enabled](/speed/optimization/protocol/), Cloudflare automatically generates HTTPS records on the fly, to advertise to clients how they should connect to your server.

For more details and context, refer to the [announcement blog post](https://blog.cloudflare.com/speeding-up-https-and-http-3-negotiation-with-dns/) and [RFC 9460](https://www.rfc-editor.org/rfc/rfc9460.html).

{{<render file="_api-field-definitions.md">}}

### PTR

A [pointer (PTR) record](https://www.cloudflare.com/learning/dns/dns-records/dns-ptr-record/) specifies the allowed hosts for a given IP address.

Within Cloudflare, PTR records are used for reverse DNS lookups and should preferably be added to [reverse zones](/dns/additional-options/reverse-zones/).

{{<render file="_api-field-definitions.md">}}

### SOA

A [start of authority (SOA)](https://www.cloudflare.com/learning/dns/dns-records/dns-soa-record/) record stores information about your domain such as admin email address, when the domain was last updated, and more.

If you are using Cloudflare for your [authoritative DNS](/dns/zone-setups/full-setup/), you do not need to create an SOA record. Cloudflare creates this record automatically when you start using Cloudflare's authoritative nameservers.

{{<render file="_api-field-definitions.md">}}

### NS

A [nameserver (NS) record](https://www.cloudflare.com/learning/dns/dns-records/dns-ns-record/) indicates which server should be used for authoritative DNS.

You only need to add NS records when you are [creating custom or vanity nameservers](/dns/nameservers/custom-nameservers/), using [subdomain setup](/dns/zone-setups/subdomain-setup/), or [delegating subdomains outside of Cloudflare](/dns/manage-dns-records/how-to/subdomains-outside-cloudflare/).

{{<render file="_api-field-definitions.md">}}

### DS and DNSKEY

[DS and DNSKEY](https://www.cloudflare.com/learning/dns/dns-records/dnskey-ds-records/) records help implement DNSSEC, which cryptographically signs DNS records to prevent domain spoofing.

Most Cloudflare domains do not need to add these records and should instead follow our [DNSSEC setup guide](/dns/dnssec/).

{{<render file="_api-field-definitions.md">}}
