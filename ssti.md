
# SSTI Vulnerability - Server-Side Template Injection Execution AND Exploration

As the name implies, it's a vulnerability that occurs on the server side, giving the idea that the attacker will induce the server to perform some action that theoretically shouldn't. Templates are basically dynamic parts of the content that **have the ability to evaluate arbitrary code**. If there's a web page where the content is pretty much the same, but only some parts of it change, there's a good chance that page is using templates.


## **THE IMPACT**

Most of the time the impact is critical, and it's likely to achieve RCE and take full control of the server, although the way in which you achieve that can vary from how the application is implementing it to which model of template is being used. It is important to note that the vulnerability can exist anywhere, not just on the server side, but wherever there is a template available.

It's hard to find an SSTI that doesn't have a big impact, even if initially doesn't seem like it, the chance of escalating to another vulnerability or gaining access to sensitive data and access files that the shouldn't be allowed is possible.


## **THE IDENTIFICATION**

The first battle is to detect the template engine, a simple example would be looking for inputs, URLs that allow us to send data and looking for an "exception" when sending special characters like `${{<%[%'"}}%\`, often used for perform actions in templates, which may indicate a possible SSTI.

It can also be identified manually or with brute-force, using payload lists related to the various templates that exist today.

![templates](https://user-images.githubusercontent.com/66689576/180703700-6ff138ae-5581-48df-8449-b1764c51e066.png)

## **THE EXECUTION**

I created a server with an application in flask and jinja2 vulnerable to SSTI for a simple demonstration:

![jao](https://user-images.githubusercontent.com/66689576/180695563-12d6c548-63ac-46b0-b184-e7f3e1cd511f.png)

Viewing the exception after sending the payload with special characters:

![exceptions](https://user-images.githubusercontent.com/66689576/180695573-afa09e2a-3dec-47e5-8dbf-2594ee1955d4.png)

After detecting that the template being used is jinja2, it would be interesting to access its documentation and understand how to use it to later start a more solid attack.

![100](https://user-images.githubusercontent.com/66689576/180695595-66f01230-8349-4580-a5ce-af518581ac4d.png)

## **THE EXPLORATION**

If the documentation is not enough, an alternative is to explore the environment and discover the objects that you can access.

These objects that are present in templates engines, can show a path depending on how much they are exposed. With some objects, we can view environment variables, read files, execute commands and take advantage of that. 
In jinja2 for example, you can use this payload to execute OS commands:

`{{self._TemplateReference__context.cycler.__init__.__globals__.os.popen('id').read()}}`

![image](https://user-images.githubusercontent.com/66689576/185704839-f35e2872-deb1-4f2a-8a0d-1f0ced54e311.png)

Using the same payload, but now to list directories and files:

`{{self._TemplateReference__context.cycler.__init__.__globals__.os.popen('ls -la').read()}}`

![image](https://user-images.githubusercontent.com/66689576/185705090-d7274497-cdfd-4a6e-90fb-10b39f9810d8.png)

Being able to scale to a RCE:

![image](https://user-images.githubusercontent.com/66689576/185705134-e633964b-90b6-479a-9dbe-200ddaaa98c7.png)

And getting it:

![image](https://user-images.githubusercontent.com/66689576/185705156-bc96d9d5-7366-4257-96ef-f1164b9868ab.png)

In Ruby, to list directories and files, you can use:

    <%= Dir.entries('/') %>

In Java to list environment variables you can use:

    ${T(java.lang.System).getenv()}

As we can see, there are several templates and each one has a different way of communicating because they were developed in different ways and languages, which can be difficult. To help with researches, we can find several wordlists on the web with different payloads for different types of templates.

## **THE PREVENTION**

One of the ways to prevent Server Side Template Injection is to not allow any user to submit new templates or modify them, as in this example, concatenating the user input with the template string, on line 11.

![insecureTRUE](https://user-images.githubusercontent.com/66689576/180862782-e4e1084e-3403-423b-8f85-efce5bbeaccd.png)

Returning the result of the calculation, without considering the input as a string:

![image](https://user-images.githubusercontent.com/66689576/185705229-52d30fa2-8248-4989-be7a-01b82743a7e5.png)

We can fix this by passing the variable to the template context, as in line 11.

![secureTRUE](https://user-images.githubusercontent.com/66689576/180862800-7c9e6caa-7fb4-4829-ab6d-64f941e959d4.png)

Returning the input as a string:

![image](https://user-images.githubusercontent.com/66689576/185705346-90356069-ffac-4019-8fa9-477e36208409.png)

Sometimes it's almost inevitable to block all arbitrary code executions, having to deploy your template in a locked container.

## **THE CONCLUSION**

In general, the server-side template injection vulnerability is not complex, making it easy to exploit once detected, but it usually goes unnoticed if you don't try to find it directly. It can also be confused with a Cross-Site Scripting (XSS) vulnerability, due to the possibility of executing code where it shouldn't, but unlike XSS, SSTI directly attacks internal servers, being most of the time, more critical.

That's it for now, thank you! =)

References:
>CURL: https://curl.se/docs/manpage.html

>PortSwigger Research: https://portswigger.net/research/server-side-template-injection

>tplmap: https://github.com/epinna/tplmap

>PayloadsAllTheThings(SSTI): https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection

>Payloadbox(SSTI): https://github.com/payloadbox/ssti-payloads
