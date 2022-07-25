
# SSTI - SERVER SIDE TEMPLATE INJECTION EXECUTION

As the name implies, it's a vulnerability that occurs on the server side, giving the idea that the attacker will induce you to perform some action that theoretically shouldn't, since Templates are basically dynamic parts of the content **that have the ability to evaluate arbitrary code.**

If there's a web page where that content is pretty much the same, but only some parts of it change, there's a good chance that page is using templates.


## **THE IMPACT**

Most of the time, the impact is critical, and it can vary a lot from the way the application is using the template and also its model. Being able to achieve a Remote Code Execution (RCE) and get full control of the server. It is important to note that the vulnerability can exist anywhere, not just on the server side, but wherever there is a template available.

É dificil encontrar um SSTI que não tenha um grande impacto, mesmo que inicialmente não pareça, a chance de escalar para outra vulnerabilidade ou obter acesso a leitura de dados confidenciais e acesso a arquivos que não deveriam ser possíveis é grande.


## **THE IDENTIFY**

A primeira batalha é detectar, um simples exemplo seria procurar por inputs, url's que nos possibilite enviar dados e procurar um "exception" ao enviar caracteres especiais como `${{<%[%'"}}%\`, muito utilizados para executar ações em templates, podendo indicar um possível SSTI.

Pode ser identificado também manualmente ou com brute-force, utilizando listas de payloads relacionados aos diversos templates que existem hoje.

![templates](https://user-images.githubusercontent.com/66689576/180703700-6ff138ae-5581-48df-8449-b1764c51e066.png)

## **THE EXECUTION**

Criei um servidor com uma aplicação em flask e jinja2 vulneravel a SSTI para uma simples demonstração:

![jao](https://user-images.githubusercontent.com/66689576/180695563-12d6c548-63ac-46b0-b184-e7f3e1cd511f.png)

Visualizando o Exception:

![exceptions](https://user-images.githubusercontent.com/66689576/180695573-afa09e2a-3dec-47e5-8dbf-2594ee1955d4.png)

Após detectar que o template que está sendo utilizado é o jinja2, seria interessante acessar sua documentação e entender como utiliza-lo para posteriormente começar um ataque mais solido.

![100](https://user-images.githubusercontent.com/66689576/180695595-66f01230-8349-4580-a5ce-af518581ac4d.png)
## **THE EXPLORE**

Se apenas com a documentação não for suficiente, uma alternativa é explorar o environment e descobrir os objetos que você pode ter acesso.

Esses objetos que estão presentes em templates engines, podem mostrar um caminho dependendo do quanto estão expostos. Com alguns objetos, podemos visualizar as variáveis do ambiente, ler arquivos, executar comandos e tirar vantagem disso, por exemplo no Ruby, para listar diretorios e arquivos, utilizam:

    <%= Dir.entries('/') %>

No Java para listar as variáveis do ambiente, utilizam:

    ${T(java.lang.System).getenv()}

Como podemos observar, existem diversas template engines e cada uma possui uma maneira diferente de se comunicar pois foram desenvolvidas de maneiras e linguagens diferentes uma da outra, o que pode dificultar. Para auxiliar nas buscas, podemos encontrar várias wordlists pela web com payloads variados para diversos tipos de templates.

## **THE PREVENT**
