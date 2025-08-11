# GitLab Security API - Настройки безопасности

## Обзор

GitLab предоставляет множество настроек безопасности, которые можно настроить через файл конфигурации `gitlab.rb`. Ниже приведен полный список настроек безопасности с примерами.

## Основные настройки безопасности

### 1. Аутентификация и авторизация

```ruby
# Настройки LDAP
gitlab_rails['ldap_enabled'] = true
gitlab_rails['ldap_servers'] = {
  'main' => {
    'label' => 'LDAP',
    'host' => 'ldap.example.com',
    'port' => 389,
    'uid' => 'sAMAccountName',
    'method' => 'plain',
    'bind_dn' => 'CN=GitLab,OU=Users,DC=example,DC=com',
    'password' => 'password',
    'active_directory' => true,
    'allow_username_or_email_login' => false,
    'block_auto_created_users' => false,
    'base' => 'OU=Users,DC=example,DC=com',
    'user_filter' => '',
    'attributes' => {
      'username' => ['uid', 'userid', 'sAMAccountName'],
      'email' => ['mail', 'email', 'userPrincipalName'],
      'name' => 'cn',
      'first_name' => 'givenName',
      'last_name' => 'sn'
    }
  }
}

# Настройки SAML
gitlab_rails['omniauth_enabled'] = true
gitlab_rails['omniauth_allow_single_sign_on'] = ['saml']
gitlab_rails['omniauth_block_auto_created_users'] = true
gitlab_rails['omniauth_auto_link_ldap_user'] = false
gitlab_rails['omniauth_providers'] = [
  {
    'name' => 'saml',
    'label' => 'SAML SSO',
    'args' => {
      'assertion_consumer_service_url' => 'https://gitlab.example.com/users/auth/saml/callback',
      'issuer' => 'https://gitlab.example.com',
      'idp_cert_fingerprint' => '43:51:43:a1:b5:fc:8b:b7:0a:3a:a9:b1:0f:66:73:a8',
      'idp_sso_target_url' => 'https://login.example.com/idp',
      'idp_slo_target_url' => 'https://login.example.com/idp/logout',
      'name_identifier_format' => 'urn:oasis:names:tc:SAML:2.0:nameid-format:transient',
      'attribute_statements' => {
        'email' => ['email'],
        'name' => ['name'],
        'first_name' => ['first_name'],
        'last_name' => ['last_name']
      }
    }
  }
]

# Настройки OAuth
gitlab_rails['omniauth_providers'] = [
  {
    'name' => 'google_oauth2',
    'app_id' => 'YOUR_APP_ID',
    'app_secret' => 'YOUR_APP_SECRET',
    'args' => { 'access_type' => 'offline', 'approval_prompt' => '' }
  }
]
```

### 2. Настройки SSL/TLS

```ruby
# Настройки HTTPS
external_url 'https://gitlab.example.com'

# Настройки SSL сертификатов
nginx['ssl_certificate'] = "/etc/gitlab/ssl/gitlab.example.com.crt"
nginx['ssl_certificate_key'] = "/etc/gitlab/ssl/gitlab.example.com.key"
nginx['ssl_ciphers'] = "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256"
nginx['ssl_protocols'] = "TLSv1.2 TLSv1.3"
nginx['ssl_prefer_server_ciphers'] = "on"
nginx['ssl_session_cache'] = "shared:SSL:10m"
nginx['ssl_session_timeout'] = "10m"

# HSTS настройки
nginx['hsts_max_age'] = 31536000
nginx['hsts_include_subdomains'] = true
nginx['hsts_preload'] = true
```

### 3. Настройки безопасности приложений

```ruby
# Настройки безопасности GitLab
gitlab_rails['gitlab_default_can_create_group'] = false
gitlab_rails['gitlab_username_changing_enabled'] = false
gitlab_rails['gitlab_default_projects_features_builds'] = false
gitlab_rails['gitlab_default_projects_features_container_registry'] = false
gitlab_rails['gitlab_default_projects_features_issues'] = true
gitlab_rails['gitlab_default_projects_features_merge_requests'] = true
gitlab_rails['gitlab_default_projects_features_snippets'] = false
gitlab_rails['gitlab_default_projects_features_wiki'] = true
gitlab_rails['gitlab_default_projects_features_pages'] = false
gitlab_rails['gitlab_default_projects_features_analytics'] = false

# Настройки двухфакторной аутентификации
gitlab_rails['gitlab_2fa_grace_period'] = 48

# Настройки сессий
gitlab_rails['session_expire_delay'] = 10080
gitlab_rails['gitlab_session_timeout'] = 10080

# Настройки паролей
gitlab_rails['gitlab_password_authentication_enabled_for_git'] = false
gitlab_rails['gitlab_password_authentication_enabled_for_web'] = true
gitlab_rails['gitlab_password_authentication_enabled_for_api'] = true

# Настройки токенов доступа
gitlab_rails['gitlab_default_can_create_group'] = false
gitlab_rails['gitlab_default_can_create_project'] = false
```

### 4. Настройки безопасности контейнеров

```ruby
# Настройки Container Registry
registry['enable'] = true
registry['auth_token_realm'] = "https://gitlab.example.com/jwt/auth"
registry['auth_token_service'] = "container_registry"
registry['auth_token_issuer'] = "gitlab-issuer"
registry['rootcertbundle'] = "/opt/gitlab/embedded/ssl/certs/cacert.pem"
registry['health_storagedriver_enabled'] = true
registry['storage_delete_enabled'] = true

# Настройки безопасности контейнеров
registry['storage'] = {
  'cache' => {
    'blobdescriptor' => 'inmemory'
  },
  'delete' => {
    'enabled' => true
  },
  'maintenance' => {
    'readonly' => {
      'enabled' => false
    }
  }
}
```

### 5. Настройки безопасности базы данных

```ruby
# Настройки PostgreSQL
postgresql['listen_address'] = 'localhost'
postgresql['port'] = 5432
postgresql['max_connections'] = 200
postgresql['shared_preload_libraries'] = 'pg_stat_statements'

# Настройки шифрования
postgresql['ssl'] = true
postgresql['ssl_cert_file'] = '/etc/gitlab/ssl/postgresql.crt'
postgresql['ssl_key_file'] = '/etc/gitlab/ssl/postgresql.key'
postgresql['ssl_ca_file'] = '/etc/gitlab/ssl/ca.crt'

# Настройки аутентификации
postgresql['trust_auth_cidr_addresses'] = ['127.0.0.1/32']
postgresql['md5_auth_cidr_addresses'] = []
postgresql['password_auth_cidr_addresses'] = []
```

### 6. Настройки безопасности Redis

```ruby
# Настройки Redis
redis['bind'] = '127.0.0.1'
redis['port'] = 0
redis['password'] = 'your-redis-password'
redis['tcp_keepalive'] = 300
redis['tcp_keepalive_intvl'] = 75
redis['tcp_keepalive_probes'] = 9
redis['tcp_keepalive_time'] = 300

# Настройки SSL для Redis
redis['ssl'] = true
redis['ssl_cert_file'] = '/etc/gitlab/ssl/redis.crt'
redis['ssl_key_file'] = '/etc/gitlab/ssl/redis.key'
redis['ssl_ca_file'] = '/etc/gitlab/ssl/ca.crt'
```

### 7. Настройки безопасности Nginx

```ruby
# Настройки Nginx
nginx['enable'] = true
nginx['client_max_body_size'] = '250m'
nginx['redirect_http_to_https'] = true
nginx['redirect_http_to_https_port'] = 80
nginx['ssl_redirect'] = true

# Настройки заголовков безопасности
nginx['custom_gitlab_server_config'] = "
  add_header X-Frame-Options DENY;
  add_header X-Content-Type-Options nosniff;
  add_header X-XSS-Protection '1; mode=block';
  add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains; preload';
  add_header Content-Security-Policy \"default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self' data:; connect-src 'self' https:; frame-ancestors 'none';\";
"

# Настройки rate limiting
nginx['rate_limit_whitelist'] = ['127.0.0.1', '10.0.0.0/8']
nginx['rate_limit_zone'] = 'gitlab'
nginx['rate_limit_zone_size'] = '10m'
nginx['rate_limit'] = '100r/s'
```

### 8. Настройки безопасности мониторинга

```ruby
# Настройки Prometheus
prometheus['enable'] = true
prometheus['listen_address'] = 'localhost:9090'
prometheus['monitor_kubernetes'] = false

# Настройки Grafana
grafana['enable'] = true
grafana['admin_password'] = 'your-grafana-password'
grafana['security']['admin_user'] = 'admin'
grafana['security']['admin_password'] = 'your-grafana-password'

# Настройки Alertmanager
alertmanager['enable'] = true
alertmanager['listen_address'] = 'localhost:9093'
```

### 9. Настройки безопасности логирования

```ruby
# Настройки логирования
gitlab_rails['log_level'] = 'INFO'
gitlab_rails['log_format'] = 'json'
gitlab_rails['log_group'] = 'git'

# Настройки аудита
gitlab_rails['audit_events'] = true
gitlab_rails['audit_events_enabled'] = true

# Настройки логирования безопасности
gitlab_rails['security_log_enabled'] = true
gitlab_rails['security_log_level'] = 'INFO'
```

### 10. Настройки безопасности API

```ruby
# Настройки API
gitlab_rails['gitlab_api_rate_limit'] = 1000
gitlab_rails['gitlab_api_rate_limit_per_ip'] = 100
gitlab_rails['gitlab_api_rate_limit_authenticated'] = 1000
gitlab_rails['gitlab_api_rate_limit_unauthenticated'] = 100

# Настройки токенов API
gitlab_rails['gitlab_api_access_token_expires_in'] = 7200
gitlab_rails['gitlab_api_personal_access_token_expires_in'] = 365

# Настройки CORS
gitlab_rails['gitlab_cors_origins'] = ['https://your-frontend-domain.com']
gitlab_rails['gitlab_cors_allow_credentials'] = true
```

### 11. Настройки безопасности файлов

```ruby
# Настройки загрузки файлов
gitlab_rails['max_attachment_size'] = 10.megabytes
gitlab_rails['max_import_size'] = 50.megabytes
gitlab_rails['max_artifacts_size'] = 100.megabytes

# Настройки безопасности файлов
gitlab_rails['gitlab_workhorse_upload_timeout'] = 300
gitlab_rails['gitlab_workhorse_upload_max_size'] = 100.megabytes
```

### 12. Настройки безопасности почты

```ruby
# Настройки SMTP
gitlab_rails['gitlab_email_enabled'] = true
gitlab_rails['gitlab_email_from'] = 'gitlab@example.com'
gitlab_rails['gitlab_email_display_name'] = 'GitLab'
gitlab_rails['gitlab_email_reply_to'] = 'noreply@example.com'

# Настройки SMTP сервера
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.example.com"
gitlab_rails['smtp_port'] = 587
gitlab_rails['smtp_user_name'] = "gitlab@example.com"
gitlab_rails['smtp_password'] = "your-smtp-password"
gitlab_rails['smtp_domain'] = "example.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = false
gitlab_rails['smtp_openssl_verify_mode'] = 'peer'
```

## Полный пример файла gitlab.rb

```ruby
# GitLab Security Configuration
# Полный пример файла gitlab.rb с настройками безопасности

# Основные настройки
external_url 'https://gitlab.example.com'

# Настройки SSL/TLS
nginx['ssl_certificate'] = "/etc/gitlab/ssl/gitlab.example.com.crt"
nginx['ssl_certificate_key'] = "/etc/gitlab/ssl/gitlab.example.com.key"
nginx['ssl_ciphers'] = "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256"
nginx['ssl_protocols'] = "TLSv1.2 TLSv1.3"
nginx['redirect_http_to_https'] = true

# Настройки безопасности GitLab
gitlab_rails['gitlab_default_can_create_group'] = false
gitlab_rails['gitlab_username_changing_enabled'] = false
gitlab_rails['gitlab_2fa_grace_period'] = 48
gitlab_rails['session_expire_delay'] = 10080

# Настройки LDAP
gitlab_rails['ldap_enabled'] = true
gitlab_rails['ldap_servers'] = {
  'main' => {
    'label' => 'LDAP',
    'host' => 'ldap.example.com',
    'port' => 389,
    'uid' => 'sAMAccountName',
    'method' => 'plain',
    'bind_dn' => 'CN=GitLab,OU=Users,DC=example,DC=com',
    'password' => 'password',
    'active_directory' => true,
    'base' => 'OU=Users,DC=example,DC=com',
    'attributes' => {
      'username' => ['uid', 'userid', 'sAMAccountName'],
      'email' => ['mail', 'email', 'userPrincipalName'],
      'name' => 'cn',
      'first_name' => 'givenName',
      'last_name' => 'sn'
    }
  }
}

# Настройки базы данных
postgresql['listen_address'] = 'localhost'
postgresql['ssl'] = true
postgresql['ssl_cert_file'] = '/etc/gitlab/ssl/postgresql.crt'
postgresql['ssl_key_file'] = '/etc/gitlab/ssl/postgresql.key'

# Настройки Redis
redis['bind'] = '127.0.0.1'
redis['password'] = 'your-redis-password'
redis['ssl'] = true

# Настройки Container Registry
registry['enable'] = true
registry['auth_token_realm'] = "https://gitlab.example.com/jwt/auth"
registry['auth_token_service'] = "container_registry"
registry['auth_token_issuer'] = "gitlab-issuer"

# Настройки API
gitlab_rails['gitlab_api_rate_limit'] = 1000
gitlab_rails['gitlab_api_rate_limit_per_ip'] = 100

# Настройки логирования
gitlab_rails['log_level'] = 'INFO'
gitlab_rails['audit_events'] = true
gitlab_rails['security_log_enabled'] = true

# Настройки почты
gitlab_rails['gitlab_email_enabled'] = true
gitlab_rails['gitlab_email_from'] = 'gitlab@example.com'
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.example.com"
gitlab_rails['smtp_port'] = 587
gitlab_rails['smtp_user_name'] = "gitlab@example.com"
gitlab_rails['smtp_password'] = "your-smtp-password"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
```

## Рекомендации по безопасности

1. **Всегда используйте HTTPS** - настройте SSL/TLS сертификаты
2. **Включите двухфакторную аутентификацию** - обязательно для всех пользователей
3. **Настройте LDAP/SAML** - для централизованной аутентификации
4. **Ограничьте создание групп и проектов** - только администраторам
5. **Настройте rate limiting** - для защиты от DDoS атак
6. **Включите аудит** - для отслеживания действий пользователей
7. **Регулярно обновляйте GitLab** - для получения последних исправлений безопасности
8. **Настройте резервное копирование** - с шифрованием
9. **Используйте сильные пароли** - для всех сервисов
10. **Мониторинг безопасности** - настройте оповещения о подозрительной активности

## Команды для применения настроек

```bash
# Применить конфигурацию
sudo gitlab-ctl reconfigure

# Проверить статус сервисов
sudo gitlab-ctl status

# Перезапустить GitLab
sudo gitlab-ctl restart

# Проверить логи
sudo gitlab-ctl tail

# Создать резервную копию
sudo gitlab-backup create
```

## Дополнительные настройки безопасности

### Настройки безопасности для Kubernetes

```ruby
# Настройки Kubernetes
gitlab_rails['gitlab_default_projects_features_container_registry'] = false
gitlab_rails['gitlab_default_projects_features_kubernetes'] = false

# Настройки безопасности для CI/CD
gitlab_rails['gitlab_default_projects_features_builds'] = false
gitlab_rails['gitlab_default_projects_features_pages'] = false
```

### Настройки безопасности для внешних интеграций

```ruby
# Настройки Webhooks
gitlab_rails['webhook_timeout'] = 90
gitlab_rails['webhook_max_retries'] = 3

# Настройки внешних сервисов
gitlab_rails['gitlab_default_projects_features_external_wiki'] = false
gitlab_rails['gitlab_default_projects_features_external_issue_tracker'] = false
```

### Настройки безопасности для администраторов

```ruby
# Настройки административных функций
gitlab_rails['gitlab_default_can_create_group'] = false
gitlab_rails['gitlab_default_can_create_project'] = false
gitlab_rails['gitlab_default_projects_features_issues'] = true
gitlab_rails['gitlab_default_projects_features_merge_requests'] = true
gitlab_rails['gitlab_default_projects_features_wiki'] = true
gitlab_rails['gitlab_default_projects_features_snippets'] = false
gitlab_rails['gitlab_default_projects_features_builds'] = false
gitlab_rails['gitlab_default_projects_features_container_registry'] = false
gitlab_rails['gitlab_default_projects_features_pages'] = false
gitlab_rails['gitlab_default_projects_features_analytics'] = false
```

Эта документация содержит полный список настроек безопасности GitLab API с примерами конфигурации в файле `gitlab.rb`. Все настройки можно адаптировать под ваши конкретные требования безопасности. 