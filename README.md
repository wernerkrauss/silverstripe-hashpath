#Hash Path

Hash Path provides a function in SilverStripe templates which, given a path to an asset, returns a modified path with a file hash appended. In combination with a web server rewrite rule, browser caching can be completely mitigated as the file URL sent to the browser changes whenever the file does.

```php
// Template:
$HashPath(css/style.css)

// Rendered:                          ↙ File hash
/themes/my-theme/css/style.vpOQ8F6ybteKQQYND5dzZQ.css
```

For a SilverStripe `2.4` compatible version see branch `1.1`.

##License

Hash Path is licensed under an [MIT license](http://heyday.mit-license.org/)

##Installation

###Composer

Installing from composer is easy, 

Create or edit a `composer.json` file in the root of your SilverStripe project, and make sure the following is present.

```json
{
    "require": {
        "heyday/silverstripe-hashpath": "1.1.*"
    }
}
```

After completing this step, navigate in Terminal or similar to the SilverStripe root directory and run `composer install` or `composer update` depending on whether or not you have composer already in use.

### Web server config

As Hash Path returns paths that don't exist on disk, a rewrite rule needs to be added to your web server in order to return the file that was originally given to Hash Path. The URL format is `.v[hash]` inserted before the file extension, so you end up with `.v[hash].[extension]`.

#### Apache

The following is required in your `.htaccess` file or virtual host config.

```
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /

    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^(.+)\.(v[A-Za-z0-9]+)\.(js|css|png|jpg|gif)$ $1.$3 [L]
</IfModule>
```

#### Nginx

```
# Hashpath module
location /themes {
	rewrite "^(.+)\.(?:v\w{10,32})\.(js|css|png|jpg|gif)$" $1.$2 last;
	try_files $uri =404;
}
```

##How to use

Provided the correct theme is set, you can simply call `$HashPath` with the asset location relative to the current theme as the first argument.

For example, for a file located at `themes/my-theme/js/general.js` and with `my-theme` current, using:

```html
<script src="$HashPath(js/general.js)"></script>
```

will result in:

```html
<script src="/themes/my-theme/js/general.v54473acf909c645bb14f011d86a47733.js"></script>
```

If you are wanting to use an asset that is not relative to the current theme, use:

```html
<script src="$HashPath(/my-module/js/general.js, 0)"></script>
```

##Unit Testing

If you have `phpunit` installed you can run `silverstripe-hashpath`'s unit tests to see if everything is functioning correctly.

###Running the unit tests

From the command line:
    
    ./sake dev/tests/module/silverstripe-hashpath


From your browser:

    http://localhost/dev/tests/module/silverstripe-hashpath


##Contributing

###Code guidelines

This project follows the standards defined in:

* [PSR-1](https://github.com/pmjones/fig-standards/blob/psr-1-style-guide/proposed/PSR-1-basic.md)
* [PSR-2](https://github.com/pmjones/fig-standards/blob/psr-1-style-guide/proposed/PSR-2-advanced.md)
