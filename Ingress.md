---

## 🔹 `nginx.ingress.kubernetes.io/rewrite-target`

### 📌 Purpose:

Used to **rewrite the incoming request URL path** before forwarding it to the backend service.

### 🧠 How It Works:

Say the incoming URL is `/foo`, and you want the backend service to see it as `/`.

### 💡 Example:

```yaml
nginx.ingress.kubernetes.io/rewrite-target: /
```

### 🚀 Use Case:

With the following Ingress:

```yaml
path: /foo(/|$)(.*)
```

And:

```yaml
nginx.ingress.kubernetes.io/rewrite-target: /$2
```

A request to `/foo/hello` will go to the backend as `/hello`.

---

## 🔹 `nginx.ingress.kubernetes.io/ssl-redirect`

### 📌 Purpose:

Automatically **redirect HTTP to HTTPS**.

### 🧠 How It Works:

If this is set to `"true"`, any request on port `80` is redirected to `443`.

### 💡 Example:

```yaml
nginx.ingress.kubernetes.io/ssl-redirect: "true"
```

### ✅ Default:

`true` by default **if TLS is configured**.

---

## 🔹 `nginx.ingress.kubernetes.io/force-ssl-redirect`

### 📌 Purpose:

Forcefully redirects all requests to HTTPS even if TLS isn't explicitly set in the Ingress.

### 💡 Difference from `ssl-redirect`:

* `ssl-redirect` only works **if TLS block is present**.
* `force-ssl-redirect` overrides that and forces HTTPS **regardless of TLS block presence**.

```yaml
nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
```

---

## 🔹 `nginx.ingress.kubernetes.io/use-regex`

### 📌 Purpose:

Enable **regular expression paths** in the Ingress `path` field.

### 🧠 Why It’s Needed:

Without this, regex-like paths won’t be parsed correctly.

### 💡 Example:

```yaml
nginx.ingress.kubernetes.io/use-regex: "true"
```

```yaml
path: /api/v[0-9]+/.*
```

This matches `/api/v1/`, `/api/v2/`, etc.

---

## 🔹 `nginx.ingress.kubernetes.io/app-root`

### 📌 Purpose:

Redirect requests with empty path (`/`) to a specific subpath like `/app`.

### 🧠 How It Works:

* Visiting `/` will redirect to `/app`

```yaml
nginx.ingress.kubernetes.io/app-root: /app
```

### 💡 Example Use Case:

```yaml
# Redirect root of domain to a UI app
spec:
  rules:
    - http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: placeholder
                port:
                  number: 80
```

And annotation:

```yaml
nginx.ingress.kubernetes.io/app-root: /dashboard
```

Then `/` will redirect to `/dashboard`.

---

## ✅ Summary Table

| Annotation           | Purpose                                      |
| -------------------- | -------------------------------------------- |
| `rewrite-target`     | Rewrites request path for backend            |
| `ssl-redirect`       | Redirects HTTP to HTTPS if TLS is enabled    |
| `force-ssl-redirect` | Forces HTTPS redirect even without TLS block |
| `use-regex`          | Enables regex matching in paths              |
| `app-root`           | Redirects `/` to a subpath like `/dashboard` |

---






## ✅ **Top Ingress Annotations You Should Know**

### 🔸 1. `nginx.ingress.kubernetes.io/proxy-body-size`

* **Controls the maximum allowed size of the client request body.**
* Default is `1m`, which is often too small for uploads.

```yaml
nginx.ingress.kubernetes.io/proxy-body-size: "10m"
```

---

### 🔸 2. `nginx.ingress.kubernetes.io/proxy-read-timeout`

* **Defines the timeout for reading a response from the backend.**

```yaml
nginx.ingress.kubernetes.io/proxy-read-timeout: "60"
```

---

### 🔸 3. `nginx.ingress.kubernetes.io/proxy-send-timeout`

* **Defines timeout for sending requests to the backend.**

```yaml
nginx.ingress.kubernetes.io/proxy-send-timeout: "60"
```

---

### 🔸 4. `nginx.ingress.kubernetes.io/configuration-snippet`

* **Inject custom NGINX config** into the Ingress location block.

```yaml
nginx.ingress.kubernetes.io/configuration-snippet: |
  more_set_headers "X-Frame-Options: DENY";
  more_set_headers "X-Content-Type-Options: nosniff";
```

🔐 Useful for security headers and advanced tweaks.

---

### 🔸 5. `nginx.ingress.kubernetes.io/whitelist-source-range`

* **Restrict access to the Ingress based on IPs.**

```yaml
nginx.ingress.kubernetes.io/whitelist-source-range: "203.0.113.0/24,192.168.1.1/32"
```

🚫 Blocks all others by default.

---

### 🔸 6. `nginx.ingress.kubernetes.io/backend-protocol`

* **Specify the protocol used between the Ingress and the backend service.**
* Can be: `HTTP`, `HTTPS`, or `GRPC`

```yaml
nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
```

---

### 🔸 7. `nginx.ingress.kubernetes.io/session-cookie-name`

* Enable **sticky sessions** by setting cookie name for session affinity.

```yaml
nginx.ingress.kubernetes.io/affinity: "cookie"
nginx.ingress.kubernetes.io/session-cookie-name: "my-app-cookie"
```

---

### 🔸 8. `nginx.ingress.kubernetes.io/limit-connections`

* **Limit the number of concurrent connections** from a single IP.

```yaml
nginx.ingress.kubernetes.io/limit-connections: "20"
```

---

### 🔸 9. `nginx.ingress.kubernetes.io/service-upstream`

* **Pass traffic directly to service’s ClusterIP instead of pod IPs**.

```yaml
nginx.ingress.kubernetes.io/service-upstream: "true"
```

---

### 🔸 10. `nginx.ingress.kubernetes.io/server-snippet`

* Inject config at **server block level**, broader than `configuration-snippet`.

```yaml
nginx.ingress.kubernetes.io/server-snippet: |
  return 301 https://$host$request_uri;
```

---

## 🧠 BONUS: Useful Security-Related Annotations

| Annotation                                       | Purpose                                 |
| ------------------------------------------------ | --------------------------------------- |
| `nginx.ingress.kubernetes.io/secure-backends`    | Enforce SSL between ingress and backend |
| `nginx.ingress.kubernetes.io/x-forwarded-prefix` | Preserve original path prefix           |
| `nginx.ingress.kubernetes.io/hsts`               | Enable HTTP Strict Transport Security   |

---
