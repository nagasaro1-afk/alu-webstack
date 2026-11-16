# https_ssl

Project covering DNS setup, SSL termination on HAProxy, and HTTP to HTTPS redirection.

## Servers

| Name | IP | Role |
|------|----|------|
| 7234-web-01 | 3.8.119.106 | Web server |
| 7234-web-02 | 18.175.146.19 | Web server |
| 7234-lb-01 | 52.56.209.234 | Load balancer (HAProxy) |

## Files

| File | Description |
|------|-------------|
| `0-world_wide_web` | Bash script that audits subdomains (`www`, `lb-01`, `web-01`, `web-02`) using `dig` and `awk` |
| `1-haproxy_ssl_termination` | HAProxy config that terminates SSL on port 443 for `www` |
| `2-redirect_http_to_https` | HAProxy config that also redirects HTTP (port 80) to HTTPS with a 301 |

## Usage

Audit all subdomains:

    ./0-world_wide_web yourdomain.tech

Audit a single subdomain:

    ./0-world_wide_web yourdomain.tech web-02

Example output:

    The subdomain web-02 is a A record and points to 18.175.146.19

## HAProxy setup (on lb-01)

1. Install `haproxy` and `certbot`
2. Get a certificate: `sudo certbot certonly --standalone -d www.yourdomain.tech`
3. Combine the cert and key into `/etc/haproxy/certs/www.yourdomain.tech.pem`
4. Copy `2-redirect_http_to_https` to `/etc/haproxy/haproxy.cfg`
5. Validate and start: `sudo haproxy -c -f /etc/haproxy/haproxy.cfg && sudo systemctl start haproxy`

## Testing

    curl -sI https://www.yourdomain.tech
    curl -sIL http://www.yourdomain.tech

## Author

Agasaro Nicia Greta
