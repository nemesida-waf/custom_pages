# Custom Nemesida WAF blocking page

Display a beautiful page when blocking a Nemesida WAF request with additional information instead of a normal <code>403</code> page. To activate the page, follow these steps:

![Nemesida WAF Custom 403 Page](https://user-images.githubusercontent.com/99513957/214073040-3c8f5f96-55c0-4eac-a6ff-653499cde41a.png)

1. Create a file with custom page settings (e.g. <code>/etc/nginx/snippets/custom_pages.conf</code>):
```
## Error pages
error_page 403 =222 /403.html;

## Locations
location /403.html {

        internal;
        root /var/www/custom_pages/;
        proxy_no_cache 1;
        proxy_cache_bypass 1;

        add_header host $host always;
        add_header x-request-id $request_id always;
        add_header x-remote-ip $remote_addr always;
        add_header nemesidawaf-bt $nwaf_block_type always;
        add_header nemesidawaf-cc $nwaf_cc always;

}
```

2. Create a direcory for storing cusom page (e.g. <code>/var/www/custom_pages/</code>) and save <code>403.html</code> there. Set permissions to access for NGINX's user (e.g. <code>chown -R nginx:nginx /var/www/custom_pages</code>).

3. Include a file to NGINX for virtual host (e.g. <code>/etc/nging/conf.d/example.com.conf</code>):
```
server {

    ...
    listen ...;
    server_name ...;
    ...

    include snippets/custom_pages.conf;

    ...
    location / {
        ...
    }
    ...

}
```

4. Check NGINX's status and reload it
```
# nginx -t && service nginx reload
```

5. After that, check that everything works correctly by sending a request with a Nemesida WAF's test signature using a browser:
```
http://example.com/nwaftest
```

In which case, check the NGINX error logs (e.g. <code>/var/www/nginx/error.log</code>).
