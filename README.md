Step 14 — Configuration of Nginx Proxy Server


Update packages: apt update
2. Install Nginx = apt install nginx -y
3. Install certbot to generate SSL certificate = apt install certbot python3-certbot-nginx -y
4. Navigate to sites available directory = cd /etc/nginx/sites-available
5. Create a config file using the domain name = nano /etc/nginx/sites-available/digital.girlsvendor.com
5. Paste basic config = nano /etc/nginx/sites-available/digital.girlsvendor.com
6. Issue certificate for the domain name = certbot --nginx -d digital.girlsvendor.com
7. Enable the new certificate via symlink (this replicates the config in sites-enabled dir, where nginx loads configs)  = ln -sf /etc/nginx/sites-available/digital.girlsvendor.com \
   /etc/nginx/sites-enabled/
7. Test nginx config after editing = nginx -t
8. Reload Nginx server = systemctl reload nginx
9. Nginx server status = systemctl status nginx


Note*****:
Run the commands
Identify “location / {} “ object and edit =

Note*****:
HTTP  → never serve app directly (redirect only)
HTTPS → always proxy to backend

All subdomains should be added to config and separate certificates generated for them

##### FOR 80 SERVER Block (HTTP) =====> :
For redirects only:

No change, configurations added automatically by Certbot

———————————————————————————————————————————————————————————————————————————————————————————


##### FOR 443 SSL SERVER Block (HTTPS) =====> :

**** REPLACE ****:

	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}



**** With ****:



location / {
proxy_pass http://localhost:8080;

proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
}


==>>> Fix to the error (a duplicate default server for 0.0.0.0:80 in /etc/nginx/sites-enabled/default.save:22) =
a. Check status:
b. Verify if backup file was added:  ls -la /etc/nginx/sites-enabled
c. Delete any file aside: default -> /etc/nginx/sites-available/default
d. Delete file: rm /etc/nginx/sites-enabled/default.save
e. Test connection: nginx -t
f. Start server: systemctl start nginx # GSL-Monitoring-Nginx-Config
