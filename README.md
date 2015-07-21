# nginx-spam-referral
Blacklist Referer Spam Bots and Referers with NGINX

**See [http://fadeit.dk/blog/post/nginx-referer-spam-blacklist](http://fadeit.dk/blog/post/nginx-referer-spam-blacklist) and [https://github.com/oohnoitz/nginx-blacklist](https://github.com/oohnoitz/nginx-blacklist) for more.**

**blacklist.conf** utilizes the following two nginx modules to achieve the same results as the original **bad-bot-blocker**: `ngx_http_geo_module` and `ngx_http_map_module`. This provides admins with a single configuration file used for blacklisting any spam referers, bots or malicious web crawlers without the need to complicate server blocks.

> Referrer spam (also known as log spam or referrer bombing) is a kind of spamdexing (spamming aimed at search engines). The technique involves making repeated web site requests using a fake referer URL to the site the spammer wishes to advertise. Sites that publish their access logs, including referer statistics, will then inadvertently link back to the spammer's site. These links will be indexed by search engines as they crawl the access logs. - Wikipedia


----

### Install
1. Save `blacklist.conf` somewhere accessible by nginx.
2. Include `blacklist.conf` in your `nginx.conf` before your server blocks.
```
http {
# ...
  include blacklist.conf;
# ...
}
```
3. Add the following in each server block you wish to apply the blacklist to:
```
if ($bad_referer) {
	return 444;
	}
if ($bad_client) {
  return 403;
}
```

### Configure

#### geo $validate_agent block
This contains the list of **IP Addresses** that should be allowed to use any **User-Agent** blocked. By default, it will block all IP addresses using any of the matching IP addresses.

Syntax:
```
  127.0.0.1                      0;
```
Note: `0` = allow and `1` = deny.


#### geo $validate_client block
This contains the list of **IP Addresses** that should be blocked from accessing the site. This is used to check all IP addresses that don't match any of the offending User-Agent listed. By default, it will allow all IP addresses to do not match any current rules to access the site.

Syntax:
```
  127.0.0.1                      1;
```
Note: `0` = allow and `1` = deny.

### Test

```
	# with subdomain
	$ curl --referer http://www.social-buttons.com http://fadeit.dk/en
	curl: (52) Empty reply from server

	# without subdomain
	$ curl --referer http://social-buttons.com http://fadeit.dk/en
	curl: (52) Empty reply from server
```
