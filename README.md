Apache2
=======

Installation for Apache2

Role Variables
--------------

Available variables are listed below, along with default values (see `defaults/main.yml`):

| Name                                          | Default Value       | Description                    |
|-----------------------------------------------|---------------------|--------------------------------|
| `apache2__show_ansible_management`            | `false`             | Enable to show hint in ansible managed files. |
| `apache2__enable_mod_rewrite`                 | `true`              | Enable apache2 module rewrite. |
| `apache2__enable_mod_headers`                 | `true`              | Enable apache2 module headers. |
| `apache2__enable_mod_ssl`                     | `true`              | Enable apache2 module ssl. |
| `apache2__enable_mod_http2`                   | `false`             | Enable apache2 module http2. |
| `apache2__enable_mod_security2`               | `false`             | Enable apache2 module security2. |
| `apache2__enable_mod_expires`                 | `false`             | Enable apache2 module expires. |
| `apache2__enable_mod_proxy`                   | `false`             | Enable apache2 module proxy. |
| `apache2__enable_mod_proxy_http`              | `false`             | Enable apache2 module proxy http. |
| `apache2__enable_mod_proxy_fcgi`              | `false`             | Enable apache2 module proxy fcgi. |
| `apache2__server_signature`                   | "Off"               | Set the trailing footer line under server-generated documents. (On/Off/Email) |
| `apache2__server_tokens`                      | "Off"               | Set the server response header field which is sent back to clients. (Major/Minor/Min[imal]/Prod[uctOnly]/OS/Full) |
| `apache2__security_config_name`               | additional_security | Specify the additional security configuration settings filename. ({{ apache2__app_path }}/conf-enabled/{{ apache2__security_config_name }}.conf) |
| `apache2__security_custom_parameters`         | []                  | This is a list of additional security parameters |
| `apache2__vhosts_defaults_vhost_root`         | /var/www            | Set root directory default for vhosts. |
| `apache2__vhosts_defaults_username`           | root                | Set default for owner/group for vhosts. |
| `apache2__vhosts_defaults_port80_enforce_ssl` | `false`             | Set default for vhosts ssl enforcement. This enables the redirection for port 80 to port 443. |
| `apache2__vhosts_defaults_enable_port443`     | `false`             | Set default for vhosts to enable ssl on port 443. |
| `apache2__vhosts`                             | []                  | This is a list of vhosts. See `vhost` below for list of parameters. |

### Vhost
| Name                           | Default Value                                                   | Description                    |
|--------------------------------|-----------------------------------------------------------------|--------------------------------|
| `name`                         | ''                                                              | Set Hostname that the server uses to identify itself. |
| `serveralias`                  | []                                                              | Set alternate names for a host used when matching requests to name-virtual hosts. |
| `admin_email`                  | ''                                                              | Set Email address that the server includes in error messages sent to the client. |
| `document_root`                | `{{ apache2__vhosts_defaults_vhost_root }}/{{ name }}/htdocs'`  | Set the directory that forms the main document tree visible from the web. |
| `document_root_allow_override` | None                                                            | Set the types of directives that are allowed in .htaccess files |
| `log_root`                     | `{{ apache2__vhosts_defaults_vhost_root }}/{{ name }}/logs'`    | Set the location path where the server will create log files. |
| `port80_directory`             | []                                                              | Set directives that apply only to the named file-system directory, sub-directories, and their contents. See `port80_directory/port443_directory` below for list of parameters. |
| `port80_alias`                 | []                                                              | Set URL mapping to other locations. See `port80_alias/port443_alias` below for list of parameters. |
| `port80_custom_parameters`     | []                                                              | This is a list of custom parameters to add to the vhost port80 configuration. |
| `port80_enforce_ssl`           | `{{ apache2__vhosts_defaults_port80_enforce_ssl }}`             | Enable ssl enforcement. This enables the redirection for port 80 to port 443. |
| `enable_port443`               | `{{ apache2__vhosts_defaults_enable_port443 }}`                 | Enable ssl on port 443. |
| `port443_directory`            | []                                                              | Set directives that apply only to the named file-system directory, sub-directories, and their contents. See `port80_directory/port443_directory` below for list of parameters. |
| `port443_alias`                | []                                                              | Set URL mapping to other locations. See `port80_alias/port443_alias` below for list of parameters. |
| `port443_custom_parameters`    | []                                                              | This is a list of custom parameters to add to the vhost port443 configuration. |
| `ssl_cert_file`                | []                                                              | Set Server PEM-encoded X.509 certificate data file. |
| `ssl_key_file`                 | []                                                              | Set Server PEM-encoded private key file. |
| `phpfpm_version`               | []                                                              | Set version to enable phpfpm handler in vhost. This defines the handler to forward to unix socket (/var/run/php{{ vhost.phpfpm_version }}-fpm-{{ vhost.name }}.sock) |

#### port80_directory/port443_directory
| Name               | Default Value                        | Description                    |
|--------------------|--------------------------------------|--------------------------------|
| `path`             | ''                                   | Set directory path. |
| `provider`         | directory                            | Set directory provider. (directory|location|files) |
| `options`          | []                                   | Set what features are available in a particular directory. (for Example: ExecCGI, FollowSymLinks, ...) https://httpd.apache.org/docs/2.4/en/mod/core.html#Options |
| `allow_override`   | `'None' if provider == 'Directory'`  | Set the types of directives that are allowed in .htaccess files |
| `require`          | []                                   | Set tests whether an authenticated user is authorized by an authorization provider. |
| `rewrite`          | []                                   | Set parameters for the rule-based rewriting engine to rewrite requested URLs on the fly.  See `rewrite` below for list of parameters. |
| `rewrite_base`     | ''                                   | Set the base URL for per-directory rewrites. |
| `custom_fragment`  | []                                   | Set directory provider. (directory|location|files) |

##### rewrite
| Name               | Default Value    | Description                    |
|--------------------|------------------|--------------------------------|
| `comment`          | ''               | Set comment for rewrite rule.  |
| `rewrite_base`     | ''               | Set the base URL for per-directory rewrites. |
| `rewrite_cond`     | []               | Set a condition under which rewriting will take place. |
| `rewrite_rule`     | []               | Set rules for the rewriting engine. |


##### Default
```
<Directory {{ vhost.document_root }}>
        Options -Indexes +FollowSymLinks +MultiViews
        AllowOverride {{ vhost.document_root_allow_override }}
</Directory>
```

#### port80_alias/port443_alias
| Name               | Default Value                        | Description                    |
|--------------------|--------------------------------------|--------------------------------|
| `path`             | ''                                   | Set destination path. |
| `alias`            | ''                                   | Set to use simple prefix matching. |
| `aliasmatch`       | ''                                   | Set to use prefix matching using regular expressions. |
| `scriptalias`      | ''                                   | Set to use simple prefix matching for a script or handler. |
| `scriptaliasmatch` | ''                                   | Set to use prefix matching using regular expressions for a script or handler. |

### Templates
| Name                                  | Default Value                    | Description                    |
|---------------------------------------|----------------------------------|--------------------------------|
| `apache2__security_config_template`   | conf/additional_security_conf.j2 | Jinja2 Template for Apache additional security configuration ({{ apache2__app_path }}/conf-enabled/{{ apache2__security_config_name }}.conf) |



Dependencies
------------

None.


Example Playbook
----------------

    - hosts: apache2
      vars_files:
        - vars/main.yml
      roles:
        - { role: apache2 }

## With Vhost

    - hosts: apache2
      roles:
        - role: apache2
          vars:
            apache2__enable_mod_http2: True
            apache2__enable_mod_security2: True
            apache2__enable_mod_expires: True
            apache2__enable_mod_proxy_http: True
            apache2__vhosts:
              - name: www.test.local
                serveralias:
                  - www2.test.local
                port80_enforce_ssl: True
                enable_port443: True
                admin_email: admin@test.local
                ssl_cert_file: /etc/ssl/certs/ssl-cert-snakeoil.pem
                ssl_key_file: /etc/ssl/private/ssl-cert-snakeoil.key
                port443_directory:
                  - path: /opt/netbox/netbox/static
                    provider: directory
                    options: ['FollowSymLinks', 'MultiViews']
                    require: ['all granted']
                port443_alias:
                  - alias: /static
                    path: /opt/netbox/netbox/static
                port443_custom_parameters:
                  - 'ProxyPreserveHost On'
                  - 'RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}'
                  - 'ProxyPass / http://127.0.0.1:8001/'
                  - 'ProxyPassReverse / http://127.0.0.1:8001/'


License
-------

MIT


Author Information
------------------

This role was created by Noles
