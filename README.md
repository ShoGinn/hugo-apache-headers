# Hugo Apache Headers

This helps with creating an .htaccess file for Hugo.
Items included in this .htaccess are:

* Auto 404 file
* Redirects
* Caching
* Security

## Install

1. Edit you hugo config to install the `hugo-apache-headers` Hugo Module so that you have something like:

   ```yaml
   module:
     imports:
       - path: github.com/shoginn/hugo-apache-headers
   ```

## What does this do?

This creates a .htaccess file in your hugo site root directory.
If all you do is add the module, it will create the default configuration below.

## Configuration

This module have very little configuration.

But all of them can be configured in your hugo site config parameters.

### None of these settings are necessary but if you want to change them you can!
If you have a dedicated params file here are the configurable settings:
```yaml
## These are the default settings and are NOT required!

security:
  csp: # Content Security Policy (None By Default)
    policy: ""
    report_only: false
  allow_frame: false # X-Frame-Options: DENY (true =  remove)
  permissions:
    policy: "accelerometer=(), camera=(), geolocation=(), gyroscope=(), magnetometer=(), microphone=(), payment=(), usb=()"
apache:
  error_document: 404.html
  deflate: true
  cache: true
```

### Redirects
Redirects are done a bit differently.

In your sites `data` folder.
Create a file named `redirects.yaml` and put the following in it:

```yaml
redirects:
  - from: /old/url
    to: /new/url
```
Or if you have a page with
```yaml
oldURL: /old/url
```
It will automatically redirect it to the current page.


## Default .htaccess

```apache
# Apache headers
# Automatically generated

RewriteEngine On
ErrorDocument 404 /404.html

<IfModule mod_headers.c>
  Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
  Header set X-XSS-Protection "1; mode=block" 
  Header set X-Content-Type-Options nosniff 
  Header set Referrer-Policy "strict-origin-when-cross-origin"
  Header always set X-Frame-Options: DENY
  Header always set Permissions-Policy "accelerometer=(), camera=(), geolocation=(), gyroscope=(), magnetometer=(), microphone=(), payment=(), usb=()"
</IfModule>

<IfModule mod_deflate.c>

    # Force compression for mangled headers.
    # https://developer.yahoo.com/blogs/ydn/pushing-beyond-gzipping-25601.html
    <IfModule mod_setenvif.c>
        <IfModule mod_headers.c>
            SetEnvIfNoCase ^(Accept-EncodXng|X-cept-Encoding|X{15}|~{15}|-{15})$ ^((gzip|deflate)\s*,?\s*)+|[X~-]{4,13}$ HAVE_Accept-Encoding
            RequestHeader append Accept-Encoding "gzip,deflate" env=HAVE_Accept-Encoding
        </IfModule>
    </IfModule>

    # Compress all output labeled with one of the following MIME-types
    # (for Apache versions below 2.3.7, you don't need to enable `mod_filter`
    #  and can remove the `<IfModule mod_filter.c>` and `</IfModule>` lines
    #  as `AddOutputFilterByType` is still in the core directives).
    <IfModule mod_filter.c>
        AddOutputFilterByType DEFLATE application/atom+xml \
                                      application/javascript \
                                      application/json \
                                      application/rss+xml \
                                      application/vnd.ms-fontobject \
                                      application/x-font-ttf \
                                      application/x-web-app-manifest+json \
                                      application/xhtml+xml \
                                      application/xml \
                                      font/opentype \
                                      image/svg+xml \
                                      image/x-icon \
                                      text/css \
                                      text/html \
                                      text/plain \
                                      text/x-component \
                                      text/xml
    </IfModule>

</IfModule><IfModule mod_expires.c>
    ExpiresActive on
  # Add Types
    AddType		    application/x-font-woff 				      .woff .woff2
    AddType 	    image/svg+xml 							          .svg
    
    ExpiresDefault                                      "access plus 1 month"

  # CSS
    ExpiresByType text/css                              "access plus 1 year"

  # Data interchange
    ExpiresByType application/json                      "access plus 0 seconds"
    ExpiresByType application/xml                       "access plus 0 seconds"
    ExpiresByType text/xml                              "access plus 0 seconds"

  # Favicon (cannot be renamed!)
    ExpiresByType image/x-icon                          "access plus 1 week"

  # HTML components (HTCs)
    ExpiresByType text/x-component                      "access plus 1 month"

  # HTML
    ExpiresByType text/html                             "access plus 0 seconds"

  # JavaScript
    ExpiresByType application/javascript                "access plus 1 year"

  # Manifest files
    ExpiresByType application/x-web-app-manifest+json   "access plus 0 seconds"
    ExpiresByType text/cache-manifest                   "access plus 0 seconds"

  # Media
    ExpiresByType audio/ogg                             "access plus 1 year"
    ExpiresByType image/gif                             "access plus 1 year"
    ExpiresByType image/jpeg                            "access plus 1 year"
    ExpiresByType image/png                             "access plus 1 year"
    ExpiresByType image/webp                            "access plus 1 year"
    ExpiresByType video/mp4                             "access plus 1 month"
    ExpiresByType video/ogg                             "access plus 1 month"
    ExpiresByType video/webm                            "access plus 1 month"

  # Web feeds
    ExpiresByType application/atom+xml                  "access plus 1 hour"
    ExpiresByType application/rss+xml                   "access plus 1 hour"

  # Web fonts
    ExpiresByType application/font-woff2                "access plus 1 year"
    ExpiresByType application/font-woff                 "access plus 1 year"
    ExpiresByType application/x-font-woff               "access plus 1 year"
    ExpiresByType application/vnd.ms-fontobject         "access plus 1 month"
    ExpiresByType application/x-font-ttf                "access plus 1 month"
    ExpiresByType font/opentype                         "access plus 1 month"
    ExpiresByType image/svg+xml                         "access plus 1 year"
</IfModule>
```
