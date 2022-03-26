# Log4J / CVE-2021-44228
    Versions: All versions from 2.0-beta9 to 2.16


CVE-2021-44228 or Log4Shell is a vulnerability found in the Java library with the maximum criticality score of CVSS 10/10. The library named Log4j got this rating because besides being very popular, it grants access to Remote Code Execution (RCE).

It is still used by many companies of all types and sizes and even after months of its discovery being made public, a lot of hosts still haven't updated their software. Here in this article, I present a way to run the exploit so that you can understand the vulnerability and prevent it from being exploited on your system and also explain a little about your application.

Your first targets are usually data entry points like username and password logins, http headers like User-Agent, X-Forwarded-For and others. Basically anywhere the user can provide data.

To perform this exploit, I used a vulnerable and easy-to-apply LAB available in this [GIT](https://github.com/leonjza/log4jpwn)
