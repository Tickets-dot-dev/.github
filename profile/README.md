<div align="center">

<a href="https://tickets.dev/?ref=github-org-readme">
  <img src="https://raw.githubusercontent.com/Tickets-dot-dev/.github/main/profile/banner.png" alt="Tickets.dev: API for live ticket data" width="100%">
</a>

### Live ticket data from 15 marketplaces, in one schema.

Send an event URL and get back every listing on it with section, row, seats, quantity, price, fees and all-in price.<br>
Each request reads the marketplace at that moment, with no cache in between.

[Docs](https://tickets.dev/docs?ref=github-org-readme) •
[Playground](https://tickets.dev/playground?ref=github-org-readme) •
[Events API](https://tickets.dev/events-api?ref=github-org-readme) •
[Pricing](https://tickets.dev/pricing?ref=github-org-readme) •
[Get an API key](https://tickets.dev/signup?ref=github-org-readme)

</div>

```bash
curl "https://api.tickets.dev/v1/capture?url=https://www.ticketmaster.com/eagles-live-at-sphere-las-vegas-nevada-12-11-2026/event/17006483D6FAFC60" \
  -H "x-api-key: $TICKETS_API_KEY"
```

```jsonc
{
  "source": "ticketmaster",
  "eventId": "17006483D6FAFC60",
  "eventName": "Eagles Live at Sphere",
  "venueName": "Sphere",
  "venueCity": "Las Vegas",
  "eventDateUtc": "2026-12-12T04:30:00+00:00",
  "currency": "USD",
  "capturedAt": "…",
  "listings": [
    {
      "inventoryType": "primary",
      "section": "101",
      "row": "34",
      "seats": "1,2",
      "quantity": 2,
      "ticketPrice": 243.5,
      "fee": 59.58,
      "totalPrice": 303.08
    }
    // …every other listing on the event
  ]
}
```

[![API](https://img.shields.io/website?url=https%3A%2F%2Fapi.tickets.dev%2Fv1%2Fhealth&label=api.tickets.dev&up_message=up&down_message=down&color=026cdf)](https://api.tickets.dev/v1/health)
[![Marketplaces](https://img.shields.io/badge/marketplaces-15-026cdf)](#marketplaces)
[![Docs](https://img.shields.io/badge/docs-tickets.dev-555)](https://tickets.dev/docs?ref=github-org-readme)
[![llms.txt](https://img.shields.io/badge/llms.txt-tickets.dev-555)](https://tickets.dev/llms.txt)

No key yet? Try any event URL in the [Playground](https://tickets.dev/playground?ref=github-org-readme), or use `tk_test_sandbox` to get a sample response.

## Marketplaces

The response shape is the same for every source, so one parser covers all of them. The marketplace comes from the URL's host, so country domains and subdomains work too. The hosts below are examples.

| Marketplace | `source` | Example host |
|---|---|---|
| [Ticketmaster](https://tickets.dev/ticketmaster-api?ref=github-org-readme) | `ticketmaster` | ticketmaster.com |
| [StubHub](https://tickets.dev/stubhub-api?ref=github-org-readme) | `stubhub` | stubhub.com |
| [SeatGeek](https://tickets.dev/seatgeek-api?ref=github-org-readme) | `seatgeek` | seatgeek.com |
| [TickPick](https://tickets.dev/tickpick-api?ref=github-org-readme) | `tickpick` | tickpick.com |
| [Vivid Seats](https://tickets.dev/vivid-seats-api?ref=github-org-readme) | `vividseats` | vividseats.com |
| [Gametime](https://tickets.dev/gametime-api?ref=github-org-readme) | `gametime` | gametime.co |
| [Viagogo](https://tickets.dev/viagogo-api?ref=github-org-readme) | `viagogo` | viagogo.com |
| [GoTickets](https://tickets.dev/gotickets-api?ref=github-org-readme) | `gotickets` | gotickets.com |
| [Paciolan](https://tickets.dev/paciolan-api?ref=github-org-readme) | `paciolan` | evenue.net |
| [ATG Tickets](https://tickets.dev/atg-tickets-api?ref=github-org-readme) | `atg` | atgtickets.com |
| [ProVenue](https://tickets.dev/provenue-api?ref=github-org-readme) | `provenue` | mlb.tickets.com |
| [GoFevo](https://tickets.dev/gofevo-api?ref=github-org-readme) | `gofevo` | gofevo.com |
| [Spotlight.Vegas](https://tickets.dev/spotlight-vegas-api?ref=github-org-readme) | `spotlight` | spotlight.vegas |
| [Vegas.com](https://tickets.dev/vegas-com-api?ref=github-org-readme) | `vegascom` | vegas.com |
| [Cirque du Soleil](https://tickets.dev/cirque-du-soleil-api?ref=github-org-readme) | `cirquedusoleil` | tickets.cirquedusoleil.com |

> [!NOTE]
> `GET /v1/capture/sources` returns every marketplace and the full list of hosts that route to each.

## What you can call

Base URL `https://api.tickets.dev`. Send your key in the `x-api-key` header.

| Endpoint | Costs | Does |
|---|---|---|
| `GET /v1/capture?url=` | 1 capture | Every listing on one event, live. Add `includeVenueMaps=true` for hosted section and seat maps plus the full venue layout |
| `GET /v1/capture/{source}?url=` | 1 capture | The same capture, with the marketplace in the path |
| `GET /v1/events?query=` | free | Search events by name, performer, venue or date, or send an event URL to find the same event on other marketplaces |
| `GET /v1/capture/sources` | free | The marketplaces you can capture and the hosts that route to each |
| `GET /v1/health` | free | Service status, no key needed |

## Match one event across marketplaces

Send an event URL from one marketplace to `/v1/events` and get back the same event's ID and URL on every other marketplace that sells it. Search by name works too, with `query=eagles`.

```bash
curl "https://api.tickets.dev/v1/events?query=https://www.stubhub.com/the-eagles-las-vegas-tickets-12-11-2026/event/161551904" \
  -H "x-api-key: $TICKETS_API_KEY"
```

```jsonc
{
  "events": [{
    "name": "The Eagles",
    "eventDateLocal": "2026-12-11T20:30:00-08:00",
    "venue": { "name": "Sphere at The Venetian Resort", "city": "Las Vegas", "state": "NV" },
    "sources": [
      { "marketplace": "ticketmaster", "eventId": "17006483D6FAFC60",         "url": "https://www.ticketmaster.com/…" },
      { "marketplace": "vividseats",   "eventId": "7256971",                  "url": "https://www.vividseats.com/…" },
      { "marketplace": "stubhub",      "eventId": "161551904",                "url": "https://www.stubhub.com/…" },
      { "marketplace": "seatgeek",     "eventId": "18355052",                 "url": "https://seatgeek.com/…" },
      { "marketplace": "tickpick",     "eventId": "8113666",                  "url": "https://www.tickpick.com/…" },
      { "marketplace": "gametime",     "eventId": "6a4d17db36420619f2010560", "url": "https://gametime.co/…" },
      { "marketplace": "viagogo",      "eventId": "161551904",                "url": "https://www.viagogo.com/…" }
    ]
  }]
}
```

Pass any of those URLs to `/v1/capture` to get that marketplace's listings for the same show. Matching is free and doesn't use captures. The catalog covers Ticketmaster, StubHub, Viagogo, SeatGeek, Vivid Seats, TickPick, Gametime, GoTickets, Paciolan and Cirque du Soleil.

<details>
<summary><strong>JavaScript and Python</strong></summary>

<br>

There's no SDK to install. It's one GET with one header.

```typescript
const res = await fetch(
  "https://api.tickets.dev/v1/capture?" +
    new URLSearchParams({ url: "https://www.stubhub.com/the-eagles-las-vegas-tickets-12-11-2026/event/161551904" }),
  { headers: { "x-api-key": process.env.TICKETS_API_KEY } },
);
const snapshot = await res.json();
const cheapest = snapshot.listings.toSorted((a, b) => a.totalPrice - b.totalPrice)[0];
```

```python
import os, requests

snapshot = requests.get(
    "https://api.tickets.dev/v1/capture",
    params={"url": "https://www.stubhub.com/the-eagles-las-vegas-tickets-12-11-2026/event/161551904"},
    headers={"x-api-key": os.environ["TICKETS_API_KEY"]},
    timeout=60,
).json()

cheapest = min(snapshot["listings"], key=lambda l: l["totalPrice"])
```

</details>

## Pricing

You pay per completed capture, and one capture is one event with every listing on it. Failed requests and not-found events cost nothing, and unused captures roll over. Plans start at $50 a month for 1,000 captures, and every plan includes every marketplace, live seat maps and free event matching. See [pricing](https://tickets.dev/pricing?ref=github-org-readme) for the rest.

## Start

1. [Create an account](https://tickets.dev/signup?ref=github-org-readme). Your first key is waiting on the account page.
2. `export TICKETS_API_KEY=tk_live_...`
3. Run the `curl` above with any event URL.

[Docs](https://tickets.dev/docs?ref=github-org-readme) ·
[Quickstart](https://tickets.dev/quickstart?ref=github-org-readme) ·
[Guides](https://tickets.dev/guides?ref=github-org-readme) ·
[Playground](https://tickets.dev/playground?ref=github-org-readme) ·
[Contact](https://tickets.dev/contact-us?ref=github-org-readme)

<!-- Marketplace table mirrors `marketplaces` (live: true) in landing-v2-real-time/lib/marketplaces.ts,
     and the starting price mirrors the Starter plan in lib/pricing.ts. Update this file when either changes. -->
