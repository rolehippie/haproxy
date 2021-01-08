# haproxy

[![Source Code](https://img.shields.io/badge/github-source%20code-blue?logo=github&logoColor=white)](https://github.com/rolehippie/haproxy) [![Build Status](https://img.shields.io/drone/build/rolehippie/haproxy/master?logo=drone)](https://cloud.drone.io/rolehippie/haproxy) [![License: Apache-2.0](https://img.shields.io/github/license/rolehippie/haproxy)](https://github.com/rolehippie/haproxy/blob/master/LICENSE) 

Ansible role to install and configure HAProxy. 

## Sponsor 

[![Proact Deutschland GmbH](https://proact.eu/wp-content/uploads/2020/03/proact-logo.png)](https://proact.eu) 

Building and improving this Ansible role have been sponsored by my employer **Proact Deutschland GmbH**.

## Table of content

* [Default Variables](#default-variables)
  * [haproxy_acls](#haproxy_acls)
  * [haproxy_backends](#haproxy_backends)
  * [haproxy_blocks](#haproxy_blocks)
  * [haproxy_config](#haproxy_config)
  * [haproxy_content_type_options](#haproxy_content_type_options)
  * [haproxy_default_backend](#haproxy_default_backend)
  * [haproxy_enable_maintenance](#haproxy_enable_maintenance)
  * [haproxy_enable_security_headers](#haproxy_enable_security_headers)
  * [haproxy_error_list](#haproxy_error_list)
  * [haproxy_error_pages](#haproxy_error_pages)
  * [haproxy_expect_ct](#haproxy_expect_ct)
  * [haproxy_exporter_binding](#haproxy_exporter_binding)
  * [haproxy_frame_options](#haproxy_frame_options)
  * [haproxy_frontends](#haproxy_frontends)
  * [haproxy_global_maxconn](#haproxy_global_maxconn)
  * [haproxy_http_logging](#haproxy_http_logging)
  * [haproxy_incoming](#haproxy_incoming)
  * [haproxy_incoming_binding](#haproxy_incoming_binding)
  * [haproxy_letsencrypt_backend](#haproxy_letsencrypt_backend)
  * [haproxy_log_address](#haproxy_log_address)
  * [haproxy_log_extra](#haproxy_log_extra)
  * [haproxy_log_facility](#haproxy_log_facility)
  * [haproxy_maintenance_backend](#haproxy_maintenance_backend)
  * [haproxy_maintenance_filter](#haproxy_maintenance_filter)
  * [haproxy_peers](#haproxy_peers)
  * [haproxy_referer_policy](#haproxy_referer_policy)
  * [haproxy_skip_configuration](#haproxy_skip_configuration)
  * [haproxy_ssl_ciphers](#haproxy_ssl_ciphers)
  * [haproxy_ssl_ciphersuites](#haproxy_ssl_ciphersuites)
  * [haproxy_ssl_options](#haproxy_ssl_options)
  * [haproxy_sslredirect_filter](#haproxy_sslredirect_filter)
  * [haproxy_tcp_logging](#haproxy_tcp_logging)
  * [haproxy_userlists](#haproxy_userlists)
  * [haproxy_version](#haproxy_version)
  * [haproxy_xss_protection](#haproxy_xss_protection)
  * [haprxy_http_reuse](#haprxy_http_reuse)
* [Dependencies](#dependencies)
* [License](#license)
* [Author](#author)

---

## Default Variables

### haproxy_acls

List of acl file definitions

#### Default value

```YAML
haproxy_acls:
  - name: private
    whitelist:
      - 10.10.0.0/20
```

#### Example usage

```YAML
haproxy_acls:
  - name: office
    whitelist:
      - 192.168.1.1/24
      - 10.0.0.0/20
  - name: remote
    whitelist:
       - 10.1.0.0/24
  - name: ancient
    state: absent
```

### haproxy_backends

List of backends

#### Default value

```YAML
haproxy_backends: []
```

#### Example usage

```YAML
haproxy_backends:
  - name: backend1
    content: |
      mode tcp
      server backend1 localhost:1337
  - name: backend2
    settings:
      - mode http
    options:
      - tcp-smart-connect
    defaults:
      - tfo
      - check
    smart_connect: True
    retryable_errors: True
    csp: True
    servers:
      - server1:8080
      - server2:8080
      - server3:8080
```

### haproxy_blocks

List of combined frontend and backends

#### Default value

```YAML
haproxy_blocks: []
```

#### Example usage

```YAML
haproxy_blocks:
  - name: foobar
    frontends: |
      bind 0.0.0.0:8080
      log global
      mode tcp
      use_backend foobar
    backends: |
      log global
      mode tcp
      server foobar-01 foobar.example.com:8080 check
  - name: barfoo
    frontends:
      - name: barfoo1
        content: |
          ...
      - name: barfoo2
        content: |
          ...
      - name: barfoo3
        content: |
          ...
    backends:
      - name: backend1
        content: |
          ...
      - name: backend2
        content: |
          ...
      - name: backend3
        content: |
          ...
```

### haproxy_config

Raw configuration for the HAProxy

#### Default value

```YAML
haproxy_config:
```

#### Example usage

```YAML
haproxy_config: |
  global
    log /dev/log local0
    chroot /var/lib/haproxy
    ...


  defaults
    log global
    mode http
    ...


  frontend incoming
    bind 0.0.0.0:80
    bind 0.0.0.0:443 alpn h2,http/1.1,http/1.0 ssl crt /etc/haproxy/ssl/
    ...
```

### haproxy_content_type_options

X-Content-Type-Options header if security headers are enabled

#### Default value

```YAML
haproxy_content_type_options: nosniff
```

### haproxy_default_backend

Default fallback backend

#### Default value

```YAML
haproxy_default_backend: fallback
```

### haproxy_enable_maintenance

Enable or disable the maintenance backend

#### Default value

```YAML
haproxy_enable_maintenance: false
```

### haproxy_enable_security_headers

Enable general security headers

#### Default value

```YAML
haproxy_enable_security_headers: true
```

### haproxy_error_list

List of supported error pages

#### Default value

```YAML
haproxy_error_list:
  - 400
  - 403
  - 405
  - 408
  - 429
  - 500
  - 502
  - 503
  - 504
```

### haproxy_error_pages

Path to error page files

#### Default value

```YAML
haproxy_error_pages: errors
```

### haproxy_expect_ct

Expect-CT header if security headers are enabled

#### Default value

```YAML
haproxy_expect_ct: max-age=0
```

### haproxy_exporter_binding

Address to bind the Prometheus exporter

#### Default value

```YAML
haproxy_exporter_binding: 0.0.0.0
```

### haproxy_frame_options

X-Frame-Options header if security headers are enabled

#### Default value

```YAML
haproxy_frame_options: SAMEORIGIN
```

### haproxy_frontends

List of frontends

#### Default value

```YAML
haproxy_frontends: []
```

#### Example usage

```YAML
haproxy_frontends:
  - name: frontend1
    content: |
      bind 0.0.0.0:1337
      mode tcp
      use_backend backend1
  - name: frontend2
    content: |
      bind 0.0.0.0:1338
      mode tcp
      use_backend backend2
```

### haproxy_global_maxconn

Globally defined max connections

#### Default value

```YAML
haproxy_global_maxconn: 32000
```

### haproxy_http_logging

Logging format for HTTP frontends

#### Default value

```YAML
haproxy_http_logging: '{"host":"%H","ident":"haproxy","pid":%pid,"timestamp":"%Tl","haproxy":{"conn":{"act":%ac,"fe":%fc,"be":%bc,"srv":%sc},"queue":{"backend":%bq,"srv":%sq},"time":{"tq":%Tq,"tw":%Tw,"tc":%Tc,"tr":%Tr,"tt":%Tt},"termination_state":"%tsc","retries":%rc,"network":{"client_ip":"%ci","client_port":%cp,"frontend_ip":"%fi","frontend_port":%fp},"ssl":{"version":"%sslv","ciphers":"%sslc"},"request":{"method":"%HM","uri":"%HP","protocol":"%HV","header":{"host":"%[capture.req.hdr(0),json(utf8s)]","referer":"%[capture.req.hdr(1),json(utf8s)]"}},"name":{"backend":"%b","frontend":"%ft","server":"%s"},"response":{"status_code":%ST,"location":"%[capture.res.hdr(0),json(utf8s)]"},"bytes":{"uploaded":%U,"read":%B}}}'
```

### haproxy_incoming

Configuration for the incoming frontend

#### Default value

```YAML
haproxy_incoming:
```

#### Example usage

```YAML
haproxy_incoming: |
  acl domain1-host req.hdr(host) -m dom -i example.com
  use_backend domain1 if domain1-host
```

### haproxy_incoming_binding

Address to bind the incoming frontend

#### Default value

```YAML
haproxy_incoming_binding: 0.0.0.0
```

### haproxy_letsencrypt_backend

Default letsencrypt backend

#### Default value

```YAML
haproxy_letsencrypt_backend: '{{ haproxy_default_backend }}'
```

### haproxy_log_address

Address to log into

#### Default value

```YAML
haproxy_log_address: /dev/log
```

### haproxy_log_extra

Extra options for global log definiton

#### Default value

```YAML
haproxy_log_extra:
```

### haproxy_log_facility

Facility to log

#### Default value

```YAML
haproxy_log_facility: local0
```

### haproxy_maintenance_backend

Backend used for maintenance mode

#### Default value

```YAML
haproxy_maintenance_backend: maintenance
```

### haproxy_maintenance_filter

Filter to apply skip maintenance mode

#### Default value

```YAML
haproxy_maintenance_filter: '!private-network'
```

### haproxy_peers

List of clustering peers

#### Default value

```YAML
haproxy_peers: []
```

#### Example usage

```YAML
haproxy_peers:
  - name: haproxy-01
    address: 192.168.1.2
  - name: haproxy-02
    address: 192.168.1.3
```

### haproxy_referer_policy

Referrer-Policy header if security headers are enabled

#### Default value

```YAML
haproxy_referer_policy: no-referrer-when-downgrade
```

### haproxy_skip_configuration

Skip the configuration and don't touch it

#### Default value

```YAML
haproxy_skip_configuration: false
```

### haproxy_ssl_ciphers

List of ciphers used for SSL connections

#### Default value

```YAML
haproxy_ssl_ciphers:
  - ECDHE-ECDSA-AES128-GCM-SHA256
  - ECDHE-RSA-AES128-GCM-SHA256
  - ECDHE-ECDSA-AES256-GCM-SHA384
  - ECDHE-RSA-AES256-GCM-SHA384
  - ECDHE-ECDSA-CHACHA20-POLY1305
  - ECDHE-RSA-CHACHA20-POLY1305
  - DHE-RSA-AES128-GCM-SHA256
  - DHE-RSA-AES256-GCM-SHA384
```

### haproxy_ssl_ciphersuites

List of ciphersuites used for SSL connections

#### Default value

```YAML
haproxy_ssl_ciphersuites:
  - TLS_AES_128_GCM_SHA256
  - TLS_AES_256_GCM_SHA384
  - TLS_CHACHA20_POLY1305_SHA256
```

### haproxy_ssl_options

List of options used for SSL connections

#### Default value

```YAML
haproxy_ssl_options:
  - no-sslv3
  - no-tlsv10
  - no-tlsv11
  - no-tls-tickets
```

### haproxy_sslredirect_filter

Filter to apply an redirect to HTTPS

#### Default value

```YAML
haproxy_sslredirect_filter: http !letsencrypt
```

### haproxy_tcp_logging

Logging format for TCP frontends

#### Default value

```YAML
haproxy_tcp_logging: '{"host":"%H","ident":"haproxy","pid":%pid,"timestamp":"%Tl","haproxy":{"conn":{"act":%ac,"fe":%fc,"be":%bc,"srv":%sc},"queue":{"backend":%bq,"srv":%sq},"time":{"tw":%Tw,"tc":%Tc,"tt":%Tt},"termination_state":"%tsc","retries":%rc,"network":{"client_ip":"%ci","client_port":%cp,"frontend_ip":"%fi","frontend_port":%fp},"name":{"backend":"%b","frontend":"%ft","server":"%s"},"bytes":{"uploaded":%U,"read":%B}}}'
```

### haproxy_userlists

List of userlist definitions

#### Default value

```YAML
haproxy_userlists: []
```

#### Example usage

```YAML
haproxy_userlists:
  - name: example
    users:
      - username: username1
        password: $6$To5YrYYRjY/.utPQ$hPTAoVJrf5heTtFBFDJIJz58X15fNQ5MD0qqkt4.MSHjyqyyE8cNoZRqr7qBLh/h/VgdcNQiHCd6VvB8av70t0
      - username: username2
        password: $6$kZ/4XMtsx$2v4XFur04PG3iWnBKSJMm03.TqYachWUrfsMTayVFhtOZ7y8LO7GubBZK4P7.TQhwUjYeZ3UpmC0VO5V8JLfi/
      - username: username3
        password: $6$.1t20Tfh8pocL2$0YX7s.Yrze6Vge1XI.Fs.FvegmqZSkg2sdFXjFi271LNwOC1lRtf.F46PIOIYmsVfjpEu86scRNpY/MqA/tE0/
```

### haproxy_version

Version of the PPA repo to use

#### Default value

```YAML
haproxy_version: 2.0
```

### haproxy_xss_protection

X-XSS-Protection header if security headers are enabled

#### Default value

```YAML
haproxy_xss_protection: 1; mode=block
```

### haprxy_http_reuse

Declare how idle HTTP connections may be shared between requests

#### Default value

```YAML
haprxy_http_reuse: always
```

## Dependencies

* None

## License

Apache-2.0

## Author

[Thomas Boerger](https://github.com/tboerger)
