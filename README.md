# Grafana Setup

This step documentation is a basic installation/configurations for Grafana. It will show the configuration needed for a OIDC connection.

## Prerequisite:
* Ubuntu-22.0.4
* Updates/Upgrades Completed
* Network Configured (Static address and DNS)
* Date/Time is set
* Zitadel Instance
  

```
sudo apt-get install -y apt-transport-https software-properties-common wget
```
```
sudo mkdir -p /etc/apt/keyrings/
```
```
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
```
```
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```


(optional) If need be  add Beta release.
```
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com beta main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

```
apt update
```
```
sudo apt-get install grafana
```
```
sudo systemctl daemon-reload
```
```
sudo systemctl start grafana-server
```
```
sudo systemctl status grafana-server
```
### Install Nginx
```
apt install nginx
```

Edit  nginx default site file 
```
vi /etc/nginx/sites-available/default
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


```
sudo apt install certbot python3-certbot-nginx
```
```
sudo certbot --nginx
```
## Zitadel confgiuration

Create a project Grafana the settings needed to configure Grafana Configuration file will be need.
Log into Zitadel and go to Projects then click *Create New Project*.
Name it Grafana and save.

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/3f007267-e6f8-49fa-b15a-bb9041f2be4a)

Create an Application and name it Grafana. Next choose the type of Application called WEB OIDC, click continue.

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/08124c77-d59a-414d-8aad-8903799e6389)

Use Recommeneded called PKCE, click continue.

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/1fc75b30-22b4-4fa8-aff5-a499944e4c9e)

Redirect login.
```
https://grafana.hungry-howard.com/login/generic_oauth
```
Post logout
```
https://grafana.hungry-howard.com/logout
```
When completed click the *+* sign on the right of each URI. Click continue then click create.

Results:

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/47e22a58-ddf3-4e6f-ac15-8dc2aa8b1a5f)



Copy you ClientID and save it for Grafana configuration file.

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/3bcf9b82-254a-4002-a5f4-ff16dda22f41)


Use a random hash instead of a static client secret for more security.

![image](https://github.com/HungryHowies/grafana-setup/assets/22652276/cdfcc538-5f7f-41d9-b114-fe907a3d9f3c)

### Token Settings

For token Option enable Auth Token Type "JWT" 

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/74be3977-bf93-4da5-b964-2f4cd17904be)



Check the tic box called ```User Info inside ID Token```.



### Edit Grafana config file.

```
vi /etc/grafana/grafana.ini
```
Fill in the settings from the example below.

```
[server]
root_url = https://grafana.hungry-howard.com

[users]
allow_sign_up = false
allow_org_create = true
auto_assign_org = true
auto_assign_org_id = 1
```
Set role for SSO user/s add this line to the Grafana Config file. This would make the user a admin.
```
auto_assign_org_role = admin
```
```
verify_email_enabled = false
login_hint = email or username
password_hint = password
[auth.generic_oauth]
enabled = true
name = zitadel
allow_sign_up = true
```
Paste the ClientID saved from earlier and past it nect to the setting *client_id*.
```
client_id = 259048395543485137@grafana
```

```
scopes = openid email profile offline_access roles 
email_attribute_name = email
login_attribute_path = username
name_attribute_path = fullname
```
Set the end points need for Zitadel URI as shown below.

```
auth_url =  https://zitadel-build.hungry-howard.com/oauth/v2/authorize
token_url = https://zitadel-build.hungry-howard.com/oauth/v2/token
api_url = https://zitadel-build.hungry-howard.com/oidc/v1/userinfo 
use_pkce = true
```

Set role for SSO user/s add this line to the Grafana Config file. This would make the user a admin.

*NOTE:* Need to check Users section settings if it can tdo this also.
```
role_attribute_path = contains('"user-roles[*]"', 'monitoring') && 'Editor' || 'admin'
```


### The Grafana Project settings

Check the tic box called "Assert Roles on Authentication"

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/33a741cd-9c91-4b81-9300-3f421eec7563)

Add the role Admin under Project.

```
Add roles "admin".
```
Grant ORG to Grafana project.

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/f73ace35-4f30-4d17-a8bf-ccebe7720927)


For authorizations select the users needed.

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/a34e5c6d-b2b8-423e-8cc7-0283974907d5)










