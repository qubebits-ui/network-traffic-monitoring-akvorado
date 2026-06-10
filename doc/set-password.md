# 🔐 How to Set or Change the Web UI Password

By default, the **Akvorado Web Console** is secured using **Traefik Basic Authentication**.

## Default Credentials

| Field | Value |
|---------|---------|
| Username | `admin` |
| Password | `admin` |

> ⚠️ For security reasons, change these credentials before exposing the dashboard to your network.

---

## Step 1: Generate a Password Hash

Traefik requires passwords to be stored as a secure hash (MD5, SHA1, or BCrypt).

Run the following command in your terminal, replacing `YourNewSecurePassword` with your desired password:

```bash
docker run --rm -it httpd:alpine htpasswd -nb admin YourNewSecurePassword
```

Example:

```bash
docker run --rm -it httpd:alpine htpasswd -nb admin MyStrongPassword123!
```

Example output:

```text
admin:$apr1$OQ.H71wM$HkY9L6i.9GvR6R3w2E/s.0
```

---

## Step 2: Escape the `$` Symbols (IMPORTANT)

Docker Compose interprets `$` as an environment variable reference.

Before using the generated hash, replace every `$` with `$$`.

### Example

**Raw Output**

```text
admin:$apr1$OQ.H71wM$HkY9L6i.9GvR6R3w2E/s.0
```

**Escaped Output**

```text
admin:$$apr1$$OQ.H71wM$$HkY9L6i.9GvR6R3w2E/s.0
```

---

## Step 3: Update `docker-compose.yml`

Open your Docker Compose file:

```bash
nano docker-compose.yml
```

Locate the **Traefik** service and find the following label:

```yaml
traefik:
  # ... other configuration ...

  labels:
    # ... other labels ...

    - "traefik.http.middlewares.auth.basicauth.users=admin:$$apr1$$OQ.H71wM$$HkY9L6i.9GvR6R3w2E/s.0"
```

Replace the existing hash with your newly generated and escaped hash.

### Example

```yaml
traefik:
  labels:
    - "traefik.http.middlewares.auth.basicauth.users=admin:$$apr1$$NEW_HASH_HERE$$REPLACE_ME"
```

---

## Optional: Change the Username

If you decide to use a username other than `admin`, update both locations.

### Basic Authentication User

```yaml
- "traefik.http.middlewares.auth.basicauth.users=myuser:$$apr1$$HASH"
```

### Remote User Header

Under the `akvorado-console` service, update:

```yaml
- traefik.http.middlewares.console-auth.headers.customrequestheaders.Remote-User=myuser
```

The username in both locations must match.

---

## Step 4: Apply the Changes

Save the file and redeploy the stack:

```bash
sudo docker compose up -d
```

Docker Compose will automatically detect the configuration change and recreate only the affected containers.

---

## Verify Access

Open your browser and navigate to:

```text
http://<YOUR_SERVER_IP>:8081
```

Log in using your newly configured credentials.

---

## Security Recommendations

- Use a strong password (12+ characters minimum).
- Prefer a password manager-generated password.
- Never expose the dashboard with the default `admin/admin` credentials.
- Restrict dashboard access using a firewall or reverse proxy whenever possible.
- Rotate credentials periodically.

---

## Quick Reference

```bash
# Generate hash
docker run --rm -it httpd:alpine htpasswd -nb admin YourNewSecurePassword

# Escape all $ characters by doubling them
$  ->  $$

# Apply changes
sudo docker compose up -d
```
