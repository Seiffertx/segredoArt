# SSTI - SERVER SIDE TEMPLATE INJECTION EXECUTION

## **THE SSTI**

Como o próprio nome já diz, é uma vulnerabilidade que ocorre no lado do servidor, dando a ideia de que o atacante irá induzi-lo a executar alguma ação que teoricamente não deveria, já os **Templates** são basicamente partes dinâmicas do conteúdo **that have the ability to evaluate arbitrary code.**
Se houver uma página web onde esse conteúdo é praticamente o mesmo, mas apenas algumas partes dela mudam, há uma boa chance de que essa página esteja usando templates.


## **THE IMPACT**

Na grande maioria das vezes o impacto é crítico, podendo variar muito do modo em como a aplicação está utilizando o template e também seu modelo. Podendo chegar a um Remote Code Execution (RCE) e obter controle total do servidor. 
É dificil encontrar um SSTI que não tenha um grande impacto, mesmo que inicialmente não pareça, a chance de escalar para outra vulnerabilidade ou obter acesso a leitura de dados confidenciais e acesso a arquivos que não deveriam ser possíveis é grande.


## **THE EXECUTION**

A primeira batalha é detectar, um simples exemplo seria procurar por inputs, url's que nos possibilite enviar dados e procurar um "exception" ao enviar caracteres especiais como `${{<%[%'"}}%\`, muito utilizados para executar ações em templates, podendo indicar um possível SSTI.

![jao](https://user-images.githubusercontent.com/66689576/180695563-12d6c548-63ac-46b0-b184-e7f3e1cd511f.png)

Exception:

![exceptions](https://user-images.githubusercontent.com/66689576/180695573-afa09e2a-3dec-47e5-8dbf-2594ee1955d4.png)

Após detectar que o template que está sendo utilizado é o jinja2, é interessante acessar sua documentação e entender como utiliza-lo para posteriormente começar o ataque.

![100](https://user-images.githubusercontent.com/66689576/180695595-66f01230-8349-4580-a5ce-af518581ac4d.png)

## **THE IDENTIFY**

Pode ser identificado também manualmente ou com brute-force, utilizando listas de payloads relacionados aos diversos templates que existem hoje.

![templates](https://user-images.githubusercontent.com/66689576/180703700-6ff138ae-5581-48df-8449-b1764c51e066.png)


