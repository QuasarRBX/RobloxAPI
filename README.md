# Roblox Web API Reference

A comprehensive reference guide for Roblox web APIs, including endpoints, parameters, and response formats for account, group, and place information retrieval.

---

# [Web version](https://quasarrbx.github.io/RobloxAPI/)

---

## Contents

- [Authentication](#authentication)
- [API Discovery](#api-discovery)
- [Response Handling](#response-handling)
- [Account Endpoints](#account-endpoints)
- [Group Endpoints](#group-endpoints)
- [Place Endpoints](#place-endpoints)
- [Utilities](#utilities)

## Authentication

### Cookie Formats
Roblox authentication cookies follow specific patterns:

```
Pattern: r'_\|(?:_|[^\s\r\n]*?\|_)\S{100,}'

Variants:
    {...} – Random Symbols
    _|_{...}
    _||_{...}
    _|{...}|_{...}
```

## API Discovery

### Finding APIs
Several methods exist for discovering Roblox API endpoints:

1. **Official Documentation**
   - Cloud API: `https://create.roblox.com/docs/en-us/cloud`

2. **Swagger Documentation**
   - Base URL: `https://accountsettings.roblox.com//docs/index.html`
   - Replace `accountsettings` with other categories (e.g., `badges`, `users`, etc.)
   - GitHub mirror: `https://github.com/matthewdean/roblox-web-apis`
   - Fandom wiki: `https://roblox.fandom.com/wiki/List_of_web_APIs`

3. **Browser DevTools**
   - Visit `https://www.roblox.com`
   - Open DevTools (F12) → Network tab
   - Filter by Fetch/XHR (sometimes "All" is useful)

### Enums Reference
For type definitions (asset, bundle, etc.):  
`https://create.roblox.com/docs/reference/engine/enums`

## Response Handling

### HTTP Methods
- `[G]` – GET
- `[P]` – POST

### Response Codes
| Code | Meaning | Notes |
|------|---------|-------|
| 200 | Valid | Success |
| 302 | Invalid | Works only for `/my/settings/json` with disabled redirect |
| 401 | Invalid | Unauthorized |
| 403 | Banned | Account restriction |
| 429 | Rate-limit | Too many requests |
| 500 | Internal Error | Usually Roblox issue, try again |

### Response Content Types
- `[JSON]` – `response.json()`
- `[HEADERS]` – `response.headers`
- `[STATUS]` – `response.status`

### Cookie Requirements
- `[C+]` – Cookie required
- `[C-]` – No cookie required
- `[C=]` – Cookie required only if profile is Private

## Account Endpoints

### Placeholders
- `{UserId}` – User ID (without braces)

### Common Parameters
| Param | Description | Example |
|-------|-------------|---------|
| `[LIM]` | Maximum items per page | `&limit=` |
| `[CNT]` | Maximum items per page | `&count=` |
| `[CSR]` | Next page cursor | `&cursor=` |
| `[NSR]` | Next page cursor | `&nextCursor=` |
| `[ESI]` | Exclusive start ID | `&exclusiveStartId=` |

### Account Information
| Method | Cookie | Description | Endpoint | Key Fields |
|--------|--------|-------------|----------|------------|
| GET | C+ | Account validation | `https://users.roblox.com/v1/users/authenticated` | `[STATUS]` |
| GET | C+ | Ban status | `https://usermoderation.roblox.com/v1/not-approved` | `[JSON]` |
| GET | C+ | Full account info | `https://www.roblox.com/my/settings/json` | See below |
| POST | C+ | Profile info | `https://apis.roblox.com/profile-platform-api/v1/profiles/get` | |

**Detailed Account Info Fields:**
- `UserId` – Account ID
- `Name` – Username
- `DisplayName` – Display name
- `AccountAgeInDays` – Registration age
- `IsPremium` – Premium status
- `CanTrade` – Trading permission
- `IsEmailSet` – Email configured
- `IsEmailVerified` – Email verified
- `IsTwoStepEnabled` – 2FA status
- `IsAccountPinEnabled` – PIN status
- `UserAbove13` – Age category

### Financial Information
| Method | Cookie | Description | Endpoint |
|--------|--------|-------------|----------|
| GET | C+ | Current Robux | `https://economy.roblox.com/v1/users/{UserId}/currency` → `['robux']` |
| GET | C+ | Billing balance | `https://billing.roblox.com/v1/credit` → `['robuxAmount']` |
| GET | C+ | Yearly transactions | `https://economy.roblox.com/v2/users/{UserId}/transaction-totals?timeFrame=Year&transactionType=summary` |
| GET | C+ | All-time transactions | `https://economy.roblox.com/v2/users/{UserId}/transactions?transactionType=2` |

### Inventory & Assets
| Method | Cookie | Description | Endpoint | Data Extraction |
|--------|--------|-------------|----------|-----------------|
| GET | C= | RAP values | `https://inventory.roblox.com/v1/users/{UserId}/assets/collectibles` | `item['recentAveragePrice']` |
| GET | C= | Gamepasses | `https://apis.roblox.com/game-passes/v1/users/{UserId}/game-passes` | `item['name']`, `item['gamePassId']` |
| GET | C= | Badges | `https://badges.roblox.com/v1/users/{UserId}/badges` | `item['name']`, `item['id']` |
| GET | C= | Faster badge check | `https://badges.roblox.com/v1/users/{UserId}/badges/awarded-dates?badgeIds=` | `item['id']` |
| GET | C= | Bundles | `https://catalog.roblox.com/v1/users/{UserId}/bundles/1` | `item['name']`, `item['id']` |
| GET | C- | Favorite places | `https://games.roblox.com/v2/users/{UserId}/favorite/games` | `item['name']`, `item['rootPlace']['id']` |

### Social & Privacy
| Method | Cookie | Description | Endpoint | Key Field |
|--------|--------|-------------|----------|-----------|
| GET | C+ | Inventory privacy | `https://apis.roblox.com/user-settings-api/v1/user-settings/settings-and-options` | `['whoCanSeeMyInventory']['currentValue']` |
| GET | C+ | Trade privacy | `https://accountsettings.roblox.com/v1/trade-privacy` | `['tradePrivacy']` |
| GET | C- | Friends count | `https://friends.roblox.com/v1/users/{UserId}/friends/count` | `['count']` |
| GET | C- | Followers count | `https://friends.roblox.com/v1/users/{UserId}/followers/count` | `['count']` |
| GET | C- | Followings count | `https://friends.roblox.com/v1/users/{UserId}/followings/count` | `['count']` |
| GET | C- | Groups | `https://groups.roblox.com/v1/users/{UserId}/groups/roles?includeLocked=true` | `item['role']['rank']`, `item['group']['id']` |

### Authentication & Security
| Method | Cookie | Description | Endpoint | Response |
|--------|--------|-------------|----------|----------|
| POST | C+ | Get X-CSRF token | `https://auth.roblox.com/v2/logout` | `HEADERS['X-CSRF-Token']` |
| POST | C+ | Get auth ticket | `https://auth.roblox.com/v1/authentication-ticket` | `HEADERS['rbx-authentication-ticket']` |
| POST | C- | Set cookie | `https://auth.roblox.com/v1/authentication-ticket/redeem` | `HEADERS['Set-Cookie']` |

### Additional Info
| Method | Cookie | Description | Endpoint | Notes |
|--------|--------|-------------|----------|-------|
| GET | C+ | Country code | `https://users.roblox.com/v1/users/authenticated/country-code` | `['countryCode']` |
| GET | C+ | Weekly playtime | `https://apis.roblox.com/parental-controls-api/v1/parental-controls/get-top-weekly-screentime-by-universe` | |
| GET | C+ | Active sessions | `https://apis.roblox.com/token-metadata-service/v1/sessions` | `['sessions']` |
| GET | C+ | Phone info | `https://accountinformation.roblox.com/v1/phone` | `['phone']` |
| GET | C+ | Age group | `https://apis.roblox.com/user-settings-api/v1/account-insights/age-group` | See mapping below |
| GET | C+ | Age verification | `https://apis.roblox.com/age-verification-service/v1/age-verification/verified-age` | `['isVerified']` |
| GET | C+ | Voice verification | `https://voice.roblox.com/v1/settings` | `['isVerifiedForVoice']` |
| GET | C- | Roblox badges | `https://accountinformation.roblox.com/v1/users/{UserId}/roblox-badges` | `item['name']` |
| POST | C- | Username check | `https://users.roblox.com/v1/usernames/users` | `item['requestedUsername']` |

**Age Group Mapping:**
- `Label.AgeBandUnder13` – Under 13
- `Label.AgeBandOver13` – Over 13
- `Label.AgeBandUnder18` – Under 18
- `Label.AgeBandOver18` – Over 18

## Group Endpoints

### Placeholders
- `{GroupId}` – Group ID (without braces)

### Group Financials
| Method | Cookie | Description | Endpoint | Key Field |
|--------|--------|-------------|----------|-----------|
| GET | C+ | Pending yearly revenue | `https://apis.roblox.com/transaction-records/v1/groups/{GroupId}/revenue/summary/year` | `['pendingRobux']` |
| GET | C+ | Group funds | `https://economy.roblox.com/v1/groups/{GroupId}/currency` | `['robux']` |

## Place/Universe Endpoints

### Placeholders
- `{PlaceId}` – Place ID
- `{UniverseId}` – Universe ID

### Common Parameters
| Param | Description | Example |
|-------|-------------|---------|
| `[UIS]` | Multiple universe IDs | `?universeIds=` |
| `[LIM]` | Maximum items per page | `&limit=` |
| `[PGS]` | Page size | `&pageSize=` |
| `[PGT]` | Page token | `&pageToken=` |

### Place Information
| Method | Cookie | Description | Endpoint | Data Extraction |
|--------|--------|-------------|----------|-----------------|
| GET | C- | Get universe ID | `https://apis.roblox.com/universes/v1/places/{PlaceId}/universe` | `['universeId']` |
| GET | C- | Place details | `https://games.roblox.com/v1/games` | `item['name']` |

### Place Assets
| Method | Cookie | Description | Endpoint | Data Extraction |
|--------|--------|-------------|----------|-----------------|
| GET | C- | Gamepasses | `https://apis.roblox.com/game-passes/v1/universes/{UniverseId}/game-passes` | `item['name']`, `item['id']` |
| GET | C- | Badges | `https://badges.roblox.com/v1/universes/{UniverseId}/badges` | `item['name']`, `item['id']` |
| GET | C- | Products | `https://apis.roblox.com/experience-store/v1/universes/{UniverseId}/store` | `item['Name']`, `item['ProductId']` |

## ⚙️ Utilities

### Registration Date
- **Days**: Already in account info (`AccountAgeInDays`)
- **Full date**: `https://users.roblox.com/v1/users/{UserId}` → `['created']` (no cookie required)

### Card Information
- **Number of cards**: `https://apis.roblox.com/payments-gateway/v1/payment-profiles` → `len([JSON])` (cookie required)

---


