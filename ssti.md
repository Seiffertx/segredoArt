
# SSTI - SERVER SIDE TEMPLATE INJECTION EXECUTION

As the name implies, it's a vulnerability that occurs on the server side, giving the idea that the attacker will induce you to perform some action that theoretically shouldn't, since Templates are basically dynamic parts of the content **that have the ability to evaluate arbitrary code.**

If there's a web page where that content is pretty much the same, but only some parts of it change, there's a good chance that page is using templates.


## **THE IMPACT**

Most of the time, the impact is critical, and it can vary a lot from the way the application is using the template and also its model. Being able to achieve a Remote Code Execution (RCE) and get full control of the server. It is important to note that the vulnerability can exist anywhere, not just on the server side, but wherever there is a template available.

It's hard to find an SSTI that doesn't have a big impact, even if initially it doesn't seem like the chance of it escalating to another vulnerability or gaining access to read sensitive data and access files that shouldn't be possible is great.


## **THE IDENTIFY**

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
## **THE EXPLORE**

If the documentation is not enough, an alternative is to explore the environment and discover the objects that you can access.

These objects that are present in templates engines, can show a path depending on how much they are exposed. With some objects, we can view environment variables, read files, execute commands and take advantage of that, for example in Ruby, to list directories and files, you can use:

    <%= Dir.entries('/') %>

In Java to list environment variables you can use:

    ${T(java.lang.System).getenv()}

As we can see, there are several templates and each one has a different way of communicating because they were developed in different ways and languages, which can be difficult. To help with research, we can find several wordlists on the web with different payloads for different types of templates.

## **THE PREVENT**
