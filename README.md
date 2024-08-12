# Magento
Magento is an open-source CRM software that offers robust tools for managing customer relationships, sales, marketing, and support. It is highly customizable and scalable, providing cost-effective solutions for businesses to enhance customer engagement and streamline operations.




<p align="center">
<a href="https://www.codetriage.com/magento/magento2"><img src="https://www.codetriage.com/magento/magento2/badges/users.svg" alt="Open Source Helpers" /></a>
<a href="https://gitter.im/magento/magento2?utm_source=badge&amp;utm_medium=badge&amp;utm_campaign=pr-badge"><img src="https://badges.gitter.im/Join%20Chat.svg" alt="Gitter" /></a> <a href="https://crowdin.com/project/magento-2"><img src="https://d322cqt584bo4o.cloudfront.net/magento-2/localized.svg" alt="Crowdin" /></a><br/>
<a href="https://magento.com/products/magento-open-source">
<img alt="Adobe logo" height="50px" src="https://www.adobe.com/content/dam/cc/icons/Adobe_Corporate_Horizontal_Red_HEX.svg"/>
</a>
</p>

<h1 align="center">Magento Open Source</h1>

Welcome to the Magento Open Source project! [Magento Open Source](https://magento.com/products/magento-open-source) software delivers basic eCommerce capabilities to build a unique online store from the ground up.

However, for those who need a full-featured eCommerce solution, we recommend [Adobe Commerce](https://magento.com/products/magento-commerce), which includes our optimized cloud architecture and hosting as well as AI-powered merchandising and analytics.

![](https://www.consnet.co.za/wp-content/uploads/2022/04/Magento-eCommerce.png)


## Get started

- [Quick start install](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/composer.html)
- [System requirements](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html)
- [Prerequisites](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/overview.html)
- [More installation options](https://www.thecoachsmb.com/install-magento-2-4-5-on-ubuntu-22-04-complete-guide/)

To install Magento 2 with Nginx, MySQL, and an SSL certificate on Ubuntu 22.04, follow these steps:

### 1. **Prepare Your System**

1. **Update System Packages:**
   ```bash
   sudo apt update
   sudo apt upgrade
   ```

2. **Install Required Packages:**
   ```bash
   sudo apt install -y curl gnupg unzip
   ```

### 2. **Install and Configure MySQL**

1. **Install MySQL Server:**
   ```bash
   sudo apt install -y mysql-server
   ```

2. **Secure MySQL Installation:**
   ```bash
   sudo mysql_secure_installation
   ```

3. **Create a Database and User for Magento:**
   ```bash
   sudo mysql -u root -p
   ```

   ```sql
   CREATE DATABASE magento2 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
   CREATE USER 'magento2user'@'localhost' IDENTIFIED BY 'your_password';
   GRANT ALL PRIVILEGES ON magento2.* TO 'magento2user'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```

### 3. **Install PHP and Extensions**

1. **Add PHP Repository:**
   ```bash
   sudo add-apt-repository ppa:ondrej/php
   sudo apt update
   ```

2. **Install PHP and Required Extensions:**
   ```bash
   sudo apt install -y php8.1 php8.1-fpm php8.1-mysql php8.1-xml php8.1-mbstring php8.1-curl php8.1-zip php8.1-intl php8.1-bcmath
   ```

### 4. **Install Composer**

1. **Download and Install Composer:**
   ```bash
   curl -sS https://getcomposer.org/installer | php
   sudo mv composer.phar /usr/local/bin/composer
   ```

### 5. **Install Magento 2**

1. **Navigate to the Directory Where You Want to Install Magento:**
   ```bash
   cd /var/www/html
   ```

2. **Download Magento 2 Using Composer:**
   ```bash
   composer create-project --repository=https://repo.magento.com/ magento/magento2 magento2
   ```

3. **Set Correct Permissions:**
   ```bash
   sudo chown -R www-data:www-data /var/www/html/magento2
   sudo find /var/www/html/magento2 -type d -exec chmod 755 {} \;
   sudo find /var/www/html/magento2 -type f -exec chmod 644 {} \;
   ```

4. **Install Magento Using the Command Line:**
   ```bash
   cd /var/www/html/magento2
   bin/magento setup:install --base-url=http://your_domain_or_ip/ \
   --db-host=localhost --db-name=magento2 --db-user=magento2user --db-password=your_password \
   --admin-firstname=Admin --admin-lastname=User --admin-email=admin@example.com \
   --admin-user=admin --admin-password=admin_password \
   --language=en_US --currency=USD --timezone=America/Chicago --use-rewrites=1
   ```

### 6. **Install and Configure Nginx**

1. **Install Nginx:**
   ```bash
   sudo apt install -y nginx
   ```

2. **Create Nginx Configuration File for Magento:**
   ```bash
   sudo nano /etc/nginx/sites-available/magento2
   ```

   **Add the following configuration:**
   ```nginx
   server {
       listen 80;
       server_name your_domain_or_ip;
       root /var/www/html/magento2/pub;

       index index.php index.html index.htm;

       location / {
           try_files $uri $uri/ /index.php?$args;
       }

       location ~ ^/index\.php {
           fastcgi_split_path_info ^(.+\.php)(/.+)$;
           fastcgi_pass unix:/run/php/php8.1-fpm.sock;
           fastcgi_index index.php;
           include fastcgi_params;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           fastcgi_param PATH_INFO $fastcgi_path_info;
           fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
       }

       location ~* \.(jpg|jpeg|gif|css|png|js|ico|xml)$ {
           try_files $uri $uri/ =404;
           expires max;
           log_not_found off;
       }
   }
   ```

3. **Enable the Nginx Configuration and Restart Nginx:**
   ```bash
   sudo ln -s /etc/nginx/sites-available/magento2 /etc/nginx/sites-enabled/
   sudo nginx -t
   sudo systemctl restart nginx
   ```

### 7. **Install and Configure SSL Certificate**

1. **Install Certbot and Nginx Plugin:**
   ```bash
   sudo apt install -y certbot python3-certbot-nginx
   ```

2. **Obtain and Install SSL Certificate:**
   ```bash
   sudo certbot --nginx -d your_domain
   ```

   Follow the prompts to complete the SSL setup.

3. **Verify SSL Certificate Renewal:**
   ```bash
   sudo certbot renew --dry-run
   ```

### 8. **Complete Magento Setup**

1. **Update Magento Indexes and Set Up Permissions:**
   ```bash
   bin/magento indexer:reindex
   bin/magento cache:clean
   bin/magento cache:flush
   ```

2. **Access Your Magento Store:**
   Open your web browser and navigate to `https://your_domain`.

### Optional: **Secure Your Magento Installation**

1. **Enable Maintenance Mode (if needed):**
   ```bash
   bin/magento maintenance:enable
   ```

2. **Disable Maintenance Mode:**
   ```bash
   bin/magento maintenance:disable
   ```

This guide sets up Magento 2 with Nginx, MySQL, and an SSL certificate. Adjust configurations based on your specific needs and ensure that your DNS settings and domain point to your server.

## Get help

- [Help center](https://support.magento.com/hc/en-us)

## Contribute

Our [Community](https://opensource.magento.com/) is large and diverse, and our project is enormous. As a contributor, you have countless opportunities to impact product development and delivery by introducing new features or improving existing ones, enhancing test coverage, updating documentation for [developers](https://developer.adobe.com/commerce/docs/) and [end-users](https://experienceleague.adobe.com/docs/commerce-admin/user-guides/home.html), catching and fixing code bugs, suggesting points for optimization, and sharing your great ideas.

- [Contribute to the code](https://developer.adobe.com/commerce/contributor/guides/code-contributions/)
- [Report an issue](https://developer.adobe.com/commerce/contributor/guides/code-contributions/#report)
- [Improve the developer documentation](https://github.com/magento/devdocs)
- [Improve the end-user documentation](https://github.com/magento/merchdocs)
- [Shape the future of Magento Open Source](https://developer.adobe.com/open/magento)

### Maintainers

We encourage experts from the Community to help us with GitHub routines such as accepting, merging, or rejecting pull requests and reviewing issues. Adobe has granted the Community Maintainers permission to accept, merge, and reject pull requests, as well as review issues. Thanks to invaluable input from the Community Maintainers team, we can significantly improve contribution quality and accelerate the time to deliver your updates to production.

- [Learn more about the Maintainer role](https://developer.adobe.com/commerce/contributor/guides/maintainers/)
- [Maintainer's Handbook](https://developer.adobe.com/commerce/contributor/guides/maintainers/handbook/)

[![](https://raw.githubusercontent.com/wiki/magento/magento2/images/maintainers.png)](https://magento.com/magento-contributors#maintainers)

### Leaders

Adobe highly appreciates contributions that help us to improve the code, clarify the documentation, and increase test coverage. Check out our Community leaders, superstars, and superheroes on the [leaderboard](https://magento.biterg.io/app/kibana#/dashboard/41dc0c60-fa06-11eb-bbaa-dd6ca6f8fda8?_g=()).

[![](https://raw.githubusercontent.com/wiki/magento/magento2/images/contributors.png)](https://magento.com/magento-contributors)

### Labeling

We use labels in the GitHub issues and pull requests to help the participants retrieve additional information such as progress, component assignments, or release lines.

- [Labels applied by the Community Engineering team](https://developer.adobe.com/commerce/contributor/guides/code-contributions/#labels)

## Security

[Security](https://developer.adobe.com/commerce/php/architecture/basics/security/) is one of the highest priorities at Adobe. To learn more about reporting security concerns, visit the [Adobe Bug Bounty Program](https://hackerone.com/adobe).

Stay up-to-date on the latest security news and patches by signing up for [Security Alert Notifications](https://magento.com/security/sign-up).

## Licensing

Each Magento source file included in this distribution is licensed under OSL 3.0 or the terms and conditions of the applicable ordering document between Licensee/Customer and Adobe (or Magento).

[Open Software License (OSL 3.0)](https://opensource.org/licenses/osl-3.0.php) – Please see [LICENSE.txt](LICENSE.txt) for the full text of the OSL 3.0 license.

Subject to Licensee's/Customer's payment of fees and compliance with the terms and conditions of the applicable ordering document between Licensee/Customer and Adobe (or Magento), the terms and conditions of the applicable ordering between Licensee/Customer and Adobe (or Magento) supersede the OSL 3.0 license for each source file.

## Communications

We are dedicated to our Community and encourage your contributions and welcome feedback through [events](https://www.adobe.io/open/magento/calendar), our [DevBlog](https://community.magento.com/t5/Magento-DevBlog/bg-p/devblog), Twitter and YouTube channels, and [other Community resources](https://developer.adobe.com/commerce/contributor/community/).

To connect with people from the Community and Adobe engineering, [join us in Slack](https://magentocommeng.slack.com). We have a channel for every project. To join a particular channel, send us a request at [engcom@adobe.com](mailto:engcom@adobe.com), or [sign up](https://opensource.magento.com/slack).

- [Popular Slack channels](https://www.adobe.io/open/magento/slack)

If you are a new Community member, check out the following channels:

- [general](https://magentocommeng.slack.com/archives/C4YS78WE6) is an open chat for introductions and Magento 2 questions
- [github](https://magentocommeng.slack.com/archives/C7KB93M32) is a support channel for GitHub issues, pull requests, and processes
- [public-backlog](https://magentocommeng.slack.com/archives/CCV3J3RV5) for discussions of the backlog
