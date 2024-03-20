# grafana-setup
Notes on installation and SSO connection

sudo apt-get install -y apt-transport-https software-properties-common wget

sudo mkdir -p /etc/apt/keyrings/

wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null

echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

(optional) If need be  add Beta release.

echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com beta main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

apt update

sudo apt-get install grafana
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl status grafana-server


Edit  nginx default site file 
```
vi /etc/nginx/sites-available
```
```
server {
    listen 80;
    server_name grafana.hungry-howard.com;

    location / {
        proxy_pass http://localhost:3000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```


apt install nginx
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx

Edit Grafana config file.
```
[server]
root_url = https://grafana.hungry-howard.com

[users]
allow_sign_up = false
allow_org_create = true
auto_assign_org = true
auto_assign_org_id = 1
auto_assign_org_role = Viewer
verify_email_enabled = false
login_hint = email or username
password_hint = password
[auth.generic_oauth]
enabled = true
name = zitadel
allow_sign_up = true
client_id = 259048395543485137@grafana 
scopes = openid email profile offline_access roles 
email_attribute_name = email
login_attribute_path = username
name_attribute_path = fullname
auth_url =  https://zitadel-build.hungry-howard.com/oauth/v2/authorize
token_url = https://zitadel-build.hungry-howard.com/oauth/v2/token
api_url = https://zitadel-build.hungry-howard.com/oidc/v1/userinfo 
use_pkce = true
```
Zitadel confgiuration

Create a project  Grafana
create an Application  Web OIDC PKCE Use a random hash instead of a static client secret for more security

![image](https://github.com/HungryHowies/grafana-setup/assets/22652276/cdfcc538-5f7f-41d9-b114-fe907a3d9f3c)

Token Settings

For  token Option  enable Auth Token Type JWT and check the tic box called ```User Info inside ID Token```.

Redirect
https://grafana.hungry-howard.com/login/generic_oauth
Post logout
https://grafana.hungry-howard.com/logout








