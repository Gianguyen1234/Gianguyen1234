---
title: "Set up mkcert HTTPS on your PHP web server running on WSL (Windows Subsystem for Linux)"
datePublished: Tue Feb 11 2025 08:28:54 GMT+0000 (Coordinated Universal Time)
cuid: cm70808wr002k09lebop31qow
slug: set-up-mkcert-https-on-your-php-web-server-running-on-wsl-windows-subsystem-for-linux
tags: technology

---

To set up **mkcert** HTTPS on your PHP web server running on **WSL** (Windows Subsystem for Linux), follow these steps:

---

### 🚀 **Step-by-Step Guide:**

#### **1\. Install mkcert on WSL**

```sh
sudo apt update && sudo apt install -y libnss3-tools
curl -JLO "https://dl.filippo.io/mkcert/latest?for=linux/amd64"
chmod +x mkcert
sudo mv mkcert /usr/local/bin/
```

Verify installation:

```sh
mkcert --version
```

#### **2\. Install Local Certificate Authority (CA)**

Run:

```sh
mkcert -install
```

This will install a local CA in your system.

#### **3\. Generate SSL Certificates for Your Local Domain**

```sh
mkcert localhost 127.0.0.1 ::1
```

This will create:

* [`localhost`](http://localhost)`.pem` (certificate)
    
* [`localhost`](http://localhost)`-key.pem` (private key)
    

#### **4\. Configure PHP’s Built-in Server to Use HTTPS**

If using PHP’s built-in server, start it with:

```sh
php -S localhost:443 -t /path/to/your/project --cert=localhost.pem --key=localhost-key.pem
```

#### **5\. Configure Apache or Nginx**

If using **Apache**, modify `/etc/apache2/sites-available/default-ssl.conf`:

```apache
<VirtualHost *:443>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html
    ServerName localhost

    SSLEngine on
    SSLCertificateFile /path/to/localhost.pem
    SSLCertificateKeyFile /path/to/localhost-key.pem

    <Directory /var/www/html>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

Enable SSL:

```sh
sudo a2enmod ssl
sudo a2ensite default-ssl
sudo systemctl restart apache2
```

#### **6\. Add Certificate to Windows Trust Store**

To avoid browser warnings, copy the generated certificate to Windows and import it:

```sh
mkcert -install
cp "$(mkcert -CAROOT)/rootCA.pem" /mnt/c/Users/YOUR_WINDOWS_USERNAME/Documents/
```

Then, double-click `rootCA.pem` in Windows and install it as a **Trusted Root Certificate**.

---

### 🔥 **Done!** Open your PHP site with [`https://localhost`](https://localhost) on WSL. 🚀

Would you like help debugging any issues? 😊