# 🔐 How to Set or Change the Web UI Password

By default, the Akvorado Web Console is secured using **Traefik Basic Authentication**. The default credentials in the deployment template are:
* **Username:** `admin`
* **Password:** `admin`

For security, **you must change this password** before exposing the dashboard to your network.

---

### Step 1: Generate a Password Hash
Traefik requires passwords to be securely hashed (MD5, SHA1, or BCrypt). You can generate a hash instantly using a temporary Docker container. 

Run the following command in your terminal. Replace `YourNewSecurePassword` with your actual desired password:

```bash
docker run --rm -it httpd:alpine htpasswd -nb admin YourNewSecurePassword
Step 2: Escape the $ Symbols (CRITICAL)
When you run the command above, it will output a generated hash. Because Docker Compose treats the $ symbol as a system variable, you must double every $ symbol in the output before using it.
Example Conversion:
Raw Output from Step 1:
admin:$apr1$OQ.H71wM$HkY9L6i.9GvR6R3w2E/s.0
Escaped Output (Double $$):
admin:$$apr1$$OQ.H71wM$$HkY9L6i.9GvR6R3w2E/s.0
Step 3: Update docker-compose.yml
Open your docker-compose.yml file using a text editor (like nano or vim):
code
Bash
nano docker-compose.yml
Scroll to the very bottom to the traefik service. Find the basicauth.users label and replace the existing hash with your Escaped Output from Step 2.
code
Yaml
traefik:
    # ... [other config]
    labels:
      # ... [other labels]
      
      # REPLACE THE HASH ON THIS LINE WITH YOUR ESCAPED HASH:
      - "traefik.http.middlewares.auth.basicauth.users=admin:$$apr1$$OQ.H71wM$$HkY9L6i.9GvR6R3w2E/s.0"
(Note: If you decide to change the username from admin to something else, you must also update the - traefik.http.middlewares.console-auth.headers.customrequestheaders.Remote-User=admin line under the akvorado-console service to match your new username).
Step 4: Apply the Changes to Docker
Save the file and exit. To apply the new security settings, run the following command to update your running Docker containers:
code
Bash
sudo docker compose up -d
(Docker will automatically detect the configuration change and recreate only the Traefik container).
You can now navigate to http://<YOUR_SERVER_IP>:8081 and log in securely with your new credentials!
