# Domain Restriction

A lightweight **client-side domain restriction system** that allows you to **authorize, block, and validate domains** where your script is allowed to run.  
Useful for basic protection, form validation, license-like checks, and preventing casual misuse of scripts.

> ⚠️ Note: This is **not server-side security**. It is intended for **basic control and deterrence**, not for high-security use cases.

---

## How to Use This Script

### 1️⃣ Use the script
```txt
https://cdn--skunkplatform.netlify.app/free/scripts/domain-restriction.js
````

### 2️⃣ Include it in your HTML

```html
<script src="https://cdn--skunkplatform.netlify.app/free/scripts/domain-restriction.js"></script>
```

Once loaded, the script exposes a global object:

```js
window.sp_domain_restriction
```

---

## Global Object

```js
sp_domain_restriction = {
  matchDomain,
  addAuthorizedDomain,
  addBannedDomain,
  security,
  _data
}
```

---

## Variables / Data Structure

### Internal data storage

```js
sp_domain_restriction._data = {
  authorizedDomains: {},
  bannedDomains: {},
  settings: {
    blockAllUnauthorizedSubdomains: false,
    blockAllUnauthorizedDomains: true
  }
}
```

### Settings

| Setting                          | Description                                  |
| -------------------------------- | -------------------------------------------- |
| `blockAllUnauthorizedDomains`    | Blocks all domains not explicitly authorized |
| `blockAllUnauthorizedSubdomains` | Blocks subdomains unless explicitly allowed  |

---

## Authorizing a Domain

### Add an authorized domain

```js
await sp_domain_restriction.addAuthorizedDomain(
  "example.com",
  "MY_SECRET_PASSWORD",
  ["token", "id"]
);
```

**Explanation:**

* `example.com` → allowed domain
* `"token", "id"` → allowed URL parameters (`?token=`, `?id=`)

If you don’t want to restrict parameters:

```js
await sp_domain_restriction.addAuthorizedDomain(
  "example.com",
  "MY_SECRET_PASSWORD"
);
```

---

## Blocking a Domain

```js
await sp_domain_restriction.addBannedDomain(
  "malicious-site.com",
  "MY_SECRET_PASSWORD"
);
```

Any domain added here will always be blocked.

---

## Domain Validation (IMPORTANT)

This function **must be called manually**.
The script does **nothing automatically** unless you validate the domain.

```js
const allowed = await sp_domain_restriction.matchDomain(
  window.location.hostname,
  [...new URLSearchParams(window.location.search).keys()]
);

if (!allowed) {
  document.body.innerHTML = "403 - Forbidden";
  throw new Error("Unauthorized domain");
}
```

---

## Security Utilities

### Generate a secure password

```js
const result = await sp_domain_restriction.security.addGenerated({
  domain: "example.com"
});

console.log(result.generatedPassword);
```

### Use a manual password

```js
await sp_domain_restriction.security.add({
  domain: "example.com",
  password: "StrongPassword123!"
});
```

---

## Full Example

```js
(async () => {
  await sp_domain_restriction.addAuthorizedDomain(
    "skunkplatform.netlify.app",
    "SKUNK_SECURE_2026",
    ["token"]
  );

  const isAllowed = await sp_domain_restriction.matchDomain(
    location.hostname,
    [...new URLSearchParams(location.search).keys()]
  );

  if (!isAllowed) {
    document.body.innerHTML = "Access denied";
    throw new Error("Blocked domain");
  }

  console.log("Domain authorized");
})();
```

---

## Security Notes ⚠️

* This system is **client-side only**
* JavaScript can be modified by advanced users
* Do **not** rely on this for sensitive data protection

### Recommended Use Cases

* Basic license checks
* Form/domain validation
* Hotlink prevention
* Casual script misuse prevention

### Not Recommended For

* Authentication
* Payment validation
* Sensitive data protection

---

## License

Free to use.
Attribution appreciated but not required.

---

## Author

SkunkPlatform
Domain Restriction Script
