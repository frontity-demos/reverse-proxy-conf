# Frontity - Reverse Proxy mode 

The Reverse Proxy mode is a way to configure WordPress and Frontity in the same domain, using a reverse proxy (usually NGINX) to route the requests between both servers.

## Configuration 

### WordPress

The only thing that is required is to install the [Frontity Embedded mode](https://github.com/frontity/frontity-embedded-proof-of-concept) plugin.

### Frontity

Nothing special is required.

### NGINX

These are the locations that can be used for this mode: 

```nginx
	# 1. Use WordPress for all the URLs that start with `/wp-`.
	location /wp- {
		try_files $uri $uri/ /index.php$is_args$args;
	}

	# 2. Use WordPress for all the TXT and XML files.
	location ~ \.(txt|xml|xsl)$ {
		try_files $uri $uri/ /index.php$is_args$args;
	}

	# 3. Use Frontity for all the URLs that start with `/static`.
	location /static {
		proxy_pass http://localhost:3000;
	}

	# 4. The rest of the URLs.
	location / {
		# 4.1 Use Frontity if there is no WordPress auth cookie.
		if ($http_cookie !~* "wordpress_logged_in") {
			proxy_pass http://localhost:3000;
		}
		# 4.2 Use WordPress with Embedded mode if there is an auth cookie.
		try_files $uri $uri/ /index.php$is_args$args;
	}
```

## Resources 

### Explanatory video

https://www.youtube.com/watch?v=YpNJb4Lq44E

### Drawing 

![Reverse Proxy mode](https://github.com/frontity-demos/reverse-proxy-conf/blob/master/reverse-proxy.png?raw=true)

All drawings: https://excalidraw.com/#json=5765031278936064,SgwmDgRl-hXTZYtyn-APUQ
