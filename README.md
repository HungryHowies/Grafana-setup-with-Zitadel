# Grafana Setup

This step documentation is a basic installation/configuration for Grafana. It will show the configuration needed for an OIDC connection.

## Prerequisite:
* Ubuntu-22.0.4
* Updates/Upgrades Completed
* Network Configured (Static address and DNS)
* Date/Time is set
* Zitadel Instance
  

```
 apt install -y apt-transport-https software-properties-common wget
```
```
mkdir -p /etc/apt/keyrings/
```
```
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
```
```
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```
Update the repository.

```
apt update
```

Install Grafana.

```
 apt install grafana
```

Reload systemd.

```
sudo systemctl daemon-reload
```

Start Grafan service.

```
sudo systemctl start grafana-server
```

Check the status of Grafana service.

```
sudo systemctl status grafana-server
```

###  Nginx

Install nginx package.

```
apt install nginx
```

Edit nginx default site file.

```
vi /etc/nginx/sites-available/default
```

Configure the default site file as shown below.

```
server {
    listen 80;
    server_name grafana.domain.com;

    location / {
        proxy_pass http://localhost:3000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
Install Certbot for lets encrypt.

```
sudo apt install certbot python3-certbot-nginx
```

Run Certbot and fill in all the requirements needed.

```
sudo certbot --nginx
```

## Zitadel configuration

After the project is completed, these settings will be used for Grafana's configuration file.

Create a project called Grafana. Log into Zitadel and go to Projects then click *Create New Project*.
Name it Grafana and save.

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/3f007267-e6f8-49fa-b15a-bb9041f2be4a)

Create an application and name it Grafana. Next choose the type of Application called WEB OIDC, click continue.

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/08124c77-d59a-414d-8aad-8903799e6389)

Use Recommended which is called PKCE, then click continue.

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/1fc75b30-22b4-4fa8-aff5-a499944e4c9e)

Set the Redirect login.

```
https://grafana.domain.com/login/generic_oauth
```

Set the Post logout.

```
https://grafana.domain.com/logout
```

When completed click the *+* sign on the right of each URI. Click continue, then click create.

Results:

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/8e570e1f-f948-45ee-ae78-341a6cd8eafd)


Copy the  ClientID and save it for Grafana configuration file.

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/dc050703-7e19-4710-8110-d249d1198b50)



Results:

![image](https://github.com/HungryHowies/grafana-setup/assets/22652276/cdfcc538-5f7f-41d9-b114-fe907a3d9f3c)

### Token Settings

Adjust token Option, enable Auth Token Type = "JWT" .

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/74be3977-bf93-4da5-b964-2f4cd17904be)



Check the tic box called ```User Info inside ID Token```.

### The Grafana Project settings

Check the tic box called "Assert Roles on Authentication.”

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/33a741cd-9c91-4b81-9300-3f421eec7563)

Add the role Admin under Project.

```
Add roles "admin".
```
Grant ORG to Grafana project.

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/f73ace35-4f30-4d17-a8bf-ccebe7720927)


For authorizations select the users needed.

![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/a34e5c6d-b2b8-423e-8cc7-0283974907d5)



### Edit Grafana config file.

```
vi /etc/grafana/grafana.ini
```
Fill in the settings from the example below.

```
[server]
root_url = https://grafana.domain.com

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
```

Paste the ClientID saved from earlier and past it next to the setting *client_id*.

```
client_id = 259048395543485137@grafana
```

Email & User attributes.

```
scopes = openid email profile offline_access roles 
email_attribute_name = email
login_attribute_path = username
name_attribute_path = fullname
```

Set the end points needed for Zitadel URI as shown below.

```
auth_url =  https://zitadel-build.domain.com/oauth/v2/authorize
token_url = https://zitadel-build.domain.com/oauth/v2/token
api_url = https://zitadel-build.domain.com/oidc/v1/userinfo 
use_pkce = true
```

Add this to the end of Grafana configuration file. It will make SSO users a *Admin*.

```
role_attribute_path = contains('"user-roles[*]"', 'monitoring') && 'Editor' || 'admin'
```
Close and save file.


Restart Grafana service.
```
sudo systemctl restart grafana-server
```

Check status.

```
sudo systemctl status  grafana-server
```
![image](https://github.com/HungryHowies/Grafana-setup-with-Zitadel/assets/22652276/39bde298-d935-43cd-a649-a8cf9eea6cac)









