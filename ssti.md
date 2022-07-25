
# SSTI VULNERABILITY - SERVER SIDE TEMPLATE INJECTION EXECUTION

As the name implies, it's a vulnerability that occurs on the server side, giving the idea that the attacker will induce the server to perform some action that theoretically shouldn't, templates are basically dynamic parts of the content **that have the ability to evaluate arbitrary code.**

If there's a web page where the content is pretty much the same, but only some parts of it change, there's a good chance that page is using templates.


## **THE IMPACT**

Most of the time, the impact is critical, and you it's likely to achieve RCE and take full control of the server, although the way in which you achieve that can vary, from how the application is implementing it to which model of template is being used. It is important to note that the vulnerability can exist anywhere, not just on the server side, but wherever there is a template available.

It’s hard to find an SSTI that doesn’t have a big impact, even if initially it doesn’t seem like it, the chance of escalating to another vulnerability or gaining access to sensitive data and access files that shouldn’t be possible is big.


## **THE IDENTIFICATION**

The first battle is to detect, a simple example would be looking for inputs, url's that allow us to send data and looking for an "exception" when sending special characters like `${{<%[%'"}}%\`, often used for perform actions in templates, which may indicate a possible SSTI.

It can also be identified manually or with brute-force, using payload lists related to the various templates that exist today.

![templates](https://user-images.githubusercontent.com/66689576/180703700-6ff138ae-5581-48df-8449-b1764c51e066.png)

## **THE EXECUTION**

I created a server with an application in flask and jinja2 vulnerable to SSTI for a simple demonstration:

![jao](https://user-images.githubusercontent.com/66689576/180695563-12d6c548-63ac-46b0-b184-e7f3e1cd511f.png)

Viewing the Exception:

![exceptions](https://user-images.githubusercontent.com/66689576/180695573-afa09e2a-3dec-47e5-8dbf-2594ee1955d4.png)

After detecting that the template being used is jinja2, it would be interesting to access its documentation and understand how to use it to later start a more solid attack.

![100](https://user-images.githubusercontent.com/66689576/180695595-66f01230-8349-4580-a5ce-af518581ac4d.png)

## **THE EXPLORATION**

If the documentation is not enough, an alternative is to explore the environment and discover the objects that you can access.

These objects that are present in templates engines, can show a path depending on how much they are exposed. With some objects, we can view environment variables, read files, execute commands and take advantage of that, for example in Ruby, to list directories and files, you can use:

    <%= Dir.entries('/') %>

In Java to list environment variables you can use:

    ${T(java.lang.System).getenv()}

As we can see, there are several templates and each one has a different way of communicating because they were developed in different ways and languages, which can be difficult. To help with research, we can find several wordlists on the web with different payloads for different types of templates.

## **THE PREVENTION**

One of the big mistakes that occur is the way the application was developed, as in this example concatenating the user input with the template string, on line 11.

![insecureTRUE](https://user-images.githubusercontent.com/66689576/180862782-e4e1084e-3403-423b-8f85-efce5bbeaccd.png)

We can fix this by passing the variable to the template context, as in line 11.

![secureTRUE](https://user-images.githubusercontent.com/66689576/180862800-7c9e6caa-7fb4-4829-ab6d-64f941e959d4.png)

That's it for now, thank you! =)

References:
>CURL: https://curl.se/docs/manpage.html

>PortSwigger Research: https://portswigger.net/research/server-side-template-injection

>tplmap: https://github.com/epinna/tplmap

>PayloadsAllTheThings(SSTI): https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection

>Payloadbox(SSTI): https://github.com/payloadbox/ssti-payloads
