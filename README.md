# grafana-setup
Notes on installation and SSO connection

sudo apt-get install -y apt-transport-https software-properties-common wget

sudo mkdir -p /etc/apt/keyrings/

wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null

echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

(optional) If need be  add Beta release
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com beta main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

apt update

sudo apt-get install grafana
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl status grafana-server

apt install nginx

/etc/grafana/grafana.ini 
```
root@grafana:/etc/grafana# grep -Ev ^'(;|#|$)' grafana.ini
[paths]
[server]
protocol = https
http_port = 3000
domain = hungry-howard.com
enforce_domain = false
root_url = https://grafana.hungry-howard.com:3000
cert_file = /etc/grafana/fullchain.pem
cert_key = /etc/grafana/privkey.pem
[server.custom_response_headers]
[database]
[datasources]
[remote_cache]
[dataproxy]
[analytics]
[security]
[security.encryption]
[snapshots]
[dashboards]
[users]
[secretscan]
[service_accounts]
[auth]
[auth.anonymous]
[auth.github]
[auth.gitlab]
[auth.google]
[auth.grafana_com]
[auth.azuread]
[auth.okta]
[auth.generic_oauth]
[auth.basic]
[auth.proxy]
[auth.jwt]
[auth.ldap]
[aws]
[azure]
[rbac]
[smtp]
[smtp.static_headers]
[emails]
[log]
[log.console]
[log.file]
[log.syslog]
[log.frontend]
[quota]
[unified_alerting]
[unified_alerting.reserved_labels]
[unified_alerting.state_history]
[unified_alerting.state_history.external_labels]
[unified_alerting.upgrade]
[alerting]
[annotations]
[annotations.dashboard]
[annotations.api]
[explore]
[help]
[profile]
[news]
[query]
[query_history]
[metrics]
[metrics.environment_info]
[metrics.graphite]
[grafana_com]
[tracing.jaeger]
[tracing.opentelemetry]
[tracing.opentelemetry.jaeger]
[tracing.opentelemetry.otlp]
[external_image_storage]
[external_image_storage.s3]
[external_image_storage.webdav]
[external_image_storage.gcs]
[external_image_storage.azure_blob]
[external_image_storage.local]
[rendering]
[panels]
[plugins]
[live]
[plugin.grafana-image-renderer]
[support_bundles]
[enterprise]
[feature_toggles]
[date_formats]
[expressions]
[geomap]
[navigation.app_sections]
[navigation.app_standalone_pages]
[secure_socks_datasource_proxy]
[feature_management]
[public_dashboards]
```


sudo chown grafana:grafana fullchain.pem privkey.pem
