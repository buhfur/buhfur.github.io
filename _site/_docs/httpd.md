
# Httpd configuration for linux 

main config file is in /etc/httpd/conf/http.conf

**DocumentRoot**

Field in config file determines where apache should find the web servers contents.By default this is set to /var/www/html.

**ServerRoot** 

This field determines where apache should look for it's config files , by default this is set to /etc/httpd


---

Using alternative directories for apache config files is useful for applications to install drop-in files to be included by the apache server.

The magic file is used by the browser to interpret how the contents of the web server should be displayed on diff browsers.

/etc/httpd/conf.d contains files included in the main config file. This dir can be used by RPM to include Apache drop-in files. This also allows you add config files that define diff web pages without changing the main /etc/httpd/conf/httpd.conf


**Adding new modules**

You can add new modules by placing them in the /etc/httpd/conf.modules.d


**Virtual hosts**

virtual hosts use separate configuration files for unique hostnames 

To know which requests should go to an individual host, Apache reads the http header of requests to know which virtual host to request data from. 

Apache then reads the virtual host config to find the document root for the virtual host.

Then the request is forwarded to the contents file in the document root for the virtual host.


**default virtual host**

If you're server is configured to use virtual hosts , you may need to setup an entry to direct requests that don't specify a virtual host. To do this , you can create a virtual host for _default_:80 

If you don't packets that arrive through DNS are sent to the virtual host that the apache config finds first 

Virtual hosts use different hostnames but use the same IP of the apache server

IP-based virtual hosts are required if the name of the web server must be resolved to a unique IP address. These hosts do require several IP addresses. These configurations are common with apache servers that are using TLS.

**Guide to creating virtual hosts**

Open up /etc/hosts and input the IP's and hostnames of your virtual hosts

Then , create the virtual host configs in /etc/httpd/conf.d


```
<VirtualHost *:80>
    ServerAdmin webmaster@account.example.com
    DocumentRoot /www/docs/account.example.com
    ServerName account.example.com
    ErrorLog logs/account.example.com-error_log
    CustomLog logs/account.example.com-access_log common
</VirtualHost>
```

---

Then , create the directories corresponding to the DocumentRoot

