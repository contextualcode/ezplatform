# Setup GIT remotes
## Platform.sh remote
```bash
$ cd <LOCAL_PROJECTS_ROOT>
$ git clone git@github.com:<USERNAME>/ezplatform.git
$ cd ezplatform
$ git remote add platform <PLATFORMSH_REPO_URI>
$ git remote -v
$ git push platform
```
## ContextualCode remote
```bash
$ git remote add contextualcode git@github.com:contextualcode/web-summer-camp-2018-ezplatform.git
$ git fetch contextualcode --tags
$ git remote -v
```

---------------

# PHP extensions
## Default PHP Extensions
### PHP Info page
```bash
$ git merge tags/phpinfo
$ git push origin master && git push platform master
```
### Enable Xdebug
```bash
$ git merge tags/enable_xdebug
$ git push origin master && git push platform master
```
### Disable Zip:
```bash
$ git merge tags/disable_zip
$ git push origin master && git push platform master
```
### Revert PHP extensions changes:
```bash
$ git merge tags/revert_php_extensions
$ git push origin master && git push platform master
```
## Custom PHP Extensions
1. SSH to Platform.sh instance
```bash
$ platform ssh -e master
```
2. Download latest Xdebug sources
```bash
$ cd /app/var/cache/
$ rm -Rf ./*
$ wget http://pecl.php.net/get/xdebug-2.6.1.tgz
```
3. Extract and compile it
```bash
$ tar -xzf xdebug-2.6.1.tgz
$ cd xdebug-2.6.1
$ phpize
$ ./configure
$ make
$ exit
```
4. Download compiled extension
```bash
$ mkdir tmp
$ platform mount:download -m /var/cache --target ./tmp
$ mkdir ext
$ cp tmp/xdebug-2.6.1/modules/xdebug.so ext/xdebug.so
$ rm -Rf tmp
```
5. Enable and deploy
```bash
$ git merge tags/enable_custom_xdebug
$ git push origin master && git push platform master
```
## Useful links
- [Platform.sh PHP extensions](https://docs.platform.sh/languages/php/extensions.html)

---------------

# Managing environments
## Create new environment
### Using Platform.sh UI
1. Open Platform.sh Web Interface.
2. Create new “staging” environment, it will also create “staging” branch in Platform.sh GIT repository.
3. Push new “staging” branch to main GIT repository:
```bash
$ git fetch platform
$ git checkout staging
$ git push origin staging
```
### From existing GIT branch
1. Create new “development” branch in main GIT repository and push it to Platform.sh GIT repository:
```bash
$ git checkout master
$ git checkout -b development
$ git push origin development
$ git push platform development
```
2. Open Platform.sh Web Interface and check “Show inactive”, you will see new “development” environment.
3. Activate environment using Platform.sh CLI:
```bash
$ platform environment:activate development
```
## Delete environment
```bash
$ platform environment:delete development --delete-branch
$ git checkout master
```
## Useful links
- [Platform.sh Environments](https://docs.platform.sh/administration/web/environments.html)

---------------

# Managing domains
## Get edge_hostname and edge_ip
```bash
$ host $(platform environment:info edge_hostname)
```
## Local domain
1. Update local hosts file
```bash
$ tail -1 /etc/hosts
<edge_ip>   ezplatformcloud.contextualcode.site
```
2. Add domain
```bash
$ platform domain:add ezplatformcloud.contextualcode.site
```
3. Setup routes
```bash
$ git merge tags/route_local_domain
$ git push origin master && git push platform master
```
## Real domain
1. Check that real domain is a CNAME of edge_hostname:
```bash
$ host ezplatformcloud.ccplatform.net | head -1
ezplatformcloud.ccplatform.net is an alias for <edge_hostname>.
```
2. Add domain
```bash
$ platform domain:add ezplatformcloud.ccplatform.net
```
3. Setup routes
```bash
$ git merge tags/route_real_domain
$ git push origin master && git push platform master
```
## Local admin domain
1. Update local hosts file
```bash
$ tail -1 /etc/hosts
<edge_ip>   ezplatformcloud.contextualcode.site admin.ezplatformcloud.contextualcode.site
```
2. Add domain
```bash
$ platform domain:add admin.ezplatformcloud.contextualcode.site
```
3. Setup routes
```bash
$ git merge tags/route_local_admin_domain
```
4. Change site accesses match configuration
```bash
$ git merge tags/host_siteaccess_matcher
$ git push origin master && git push platform master
```
## platformsh-siteaccess-matcher-bundle
1. Merge staging branch
```bash
$ git checkout staging
$ git merge master
$ git push origin staging
$ git push platform staging
```
2. Install platformsh-siteaccess-matcher-bundle
```bash
$ git merge tags/siteaccess_matcher_bundle
$ git push origin staging && git push platform staging
```
## Useful links
- [Using custom domains for Platform.sh non-master environments](https://www.contextualcode.com/Blog/Using-custom-domains-for-Platform.sh-non-master-environments)
- [platformsh-siteaccess-matcher-bundle](https://packagist.org/packages/contextualcode/platformsh-siteaccess-matcher-bundle)
