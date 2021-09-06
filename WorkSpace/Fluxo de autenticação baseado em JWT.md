**[Desenvolvimento](https://www.treinaweb.com.br/blog/categoria/desenvolvimento)**

# Fluxo de autenticação baseado em JWT

Neste artigo iremos aprender de teórica como é o fluxo de autenticação baseado em JWT dentro de uma API REST.

 4 meses atrás

[Artigos](https://www.treinaweb.com.br/blog)Fluxo de autenticação baseado em JWT

Autenticação é um recurso indispensável em quase toda aplicação, pois devemos garantir que as informações contidas nessas aplicações só podem ser acessadas por usuários cadastrados e reconhecidos e durante o desenvolvimento de aplicações web é comum que realizemos a autenticação de usuários através de formulários de login e então guardarmos as informações de autenticação na sessão do navegador.

Mas como podemos realizar a autenticação de usuários quando estamos desenvolvendo uma API REST? Já que em uma API não temos a disposição uma sessão onde possamos guardar as informações de autenticação do usuário. Nesses casos é comum utilizarmos a Token Based Authentication e é justamente sobre esse modo de realizar a autenticação que iremos falar nesse artigo.

Para um pleno entendimento do conteudo exposto neste artigo recomendo que possua uma prévio conhecimento nos seguintes assuntos:

- **JSON:** Caso queira saber mais sobre esse tema eu recomendo a leitura do artigo [O que é JSON?](https://www.treinaweb.com.br/blog/o-que-e-json)
- **APIs:** Caso queira saber mais sobre esse tema eu recomendo a leitura do artigo [O que é uma API?](https://www.treinaweb.com.br/blog/o-que-e-uma-api)
- **Autenticação e Autorização:** Caso queira saber mais sobre esse tema eu recomendo a leitura do artigo [Autenticação x Autorização](https://www.treinaweb.com.br/blog/autenticacao-x-autorizacao)
- **JWT:** Caso queira saber mais sobre esse tema eu recomendo a leitura do artigo [O que é JWT](https://www.treinaweb.com.br/blog/o-que-e-jwt)?



![Spring Framework - Spring Security](https://d2knvm16wkt3ia.cloudfront.net/assets/svg-icon/spring-boot.svg)

##### CursoSpring Framework - Spring Security

[Conhecer o curso](https://www.treinaweb.com.br/curso/spring-framework-spring-security)

## O que é Token Based Authentication?

Token Based Authentication é uma maneira de implementar autenticação em APIs REST, onde é enviado no header Authorization da requisição um token, geralmente um token JWT, que serve como um identificador que garante que o usuário é alguém cadastrado na aplicação e já proveu suas credenciais anteriormente.

## Como é o fluxo de autenticação via JWT?

Basicamente o fluxo de autenticação via JWT é composto por seis etapas.

![img]()

Na imagem acima é possível observar de forma resumida um diagrama que ilustra essas seis etapas do processo de autenticação via JWT.

Agora vamos conhecer melhor cada uma dessas etapas.

## Obtendo o Token JWT

A parte obtenção do token JWT engloba as três primeiras etapas de todo o processo.

Primeiro, para que o usuário seja autenticado o cliente terá que realizar uma requisição HTTP com o verbo POST para a rota de autenticação, que no nosso exemplo será a rota `/users/auth` informando no corpo da requisição as credenciais do usuário, no nosso caso os dados `username` e `password`.

Logo em seguida, a aplicação assim que recebe essa requisição terá que realizar o processo de autenticação que consiste em verificar se existe algum usuário cadastrado com o `username` informado e se o `password` informado bate com o que está salvo.

Caso as credenciais informadas no corpo da requisição estejam corretas a aplicação terá que gerar um token JWT assinado, no payload desse token devem conter as chaves `sub` que possui o identificador desse usuário, no nosso exemplo esse identificador é o `username`, também terá a chave `iat` que contém a data em que o token foi gerado em formato timestamp e por último a chave `exp` que contém a data em que o token irá expirar também em formato timestamp.

E por fim esse token deverá ser retornado para o cliente no corpo da resposta HTTP.

Abaixo temos um exemplo da requisição.

Copiar

```json
POST /users/auth HTTP/1.1
Host: localhost:8080
Content-Type: application/json
Content-Length: 52

{
  "username": "cleyson",
  "password": "senha@123"
}
```

Abaixo temos o exemplo da resposta para a requisição realizada acima.

Copiar

```json
HTTP/1.1 200
Server: Tomcat
Content-Type: application/json
Date: Fri, 22 Jul 2021 11:00 GMT-3

{
  "token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJjbGV5c29uIiwiZXhwIjoxNjI1ODczNTQ5LCJpYXQiOjE2MjU4NzM1MTl9.8eYUZR2AZ0hBkX0p4-eB55CkK-I2xmeCD3udjqdzptOjrFS09zQCcrHRvILMJipjzve2A_KVL6T1UBgh6NmfQQ",
  "type": "Bearer",
  "expiresAt": "2021-07-22T11:30:00.000-03:00",
  "refreshToken": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJjbGV5c29uIiwiZXhwIjoxNjI1ODczNTc5LCJpYXQiOjE2MjU4NzM1MTl9.rWgvsj1lJNfNaMOb30lctT_exBheio-b1K17-Fbpq6T4G8PM4cwuLCl8AerkJVR3Ei1_3YZ5YLppOBQicglfHw"
}
```

Veja que no corpo da resposta HTTP temos um JSON que possui o token que será utilizado pelo cliente para que o mesmo possa se identificar na aplicação, a data em que esse token irá expirar, o tipo do token e o refresh token, mas iremos falar sobre o refresh token mais à frente.



![Flask - Tópicos de segurança]()

##### CursoFlask - Tópicos de segurança

[Conhecer o curso](https://www.treinaweb.com.br/curso/flask-topicos-de-seguranca)

## Usando o Token JWT

Agora que entendemos como é realizado a etapa de obtenção do token JWT veremos como é feito o uso do token, que engloba as etapas finais de todo o processo.

Uma vez que o cliente realizou a autenticação, o mesmo tem em mãos o token JWT que é utilizado por nossa aplicação para reconhecer um usuário autenticado, então o cliente deverá enviar esse token a partir do header Authorization em todas as requisições realizadas para rotas protegidas de nossa API.

Iremos tomar como exemplo que temos a rota `/jobs` que é uma rota que pode ser acessada com o método HTTP POST para realizar o cadastro de uma vaga de emprego e essa rota é uma rota protegida.

Para que o cliente consiga ter acesso a esta rota é necessário que o mesmo ao realizar a requisição HTTP e envie o header Authorization com o token JWT no seguinte formato ``.

Veja abaixo um exemplo dessa requisição.

Copiar

```json
POST /api/v1/jobs HTTP/1.1
Host: localhost:8080
Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJjbGV5c29uIiwiZXhwIjoxNjI1Nzg5MTk3LCJpYXQiOjE2MjU3ODkxNjd9.TtU-faPPJV86iTk3wEfhIIUjuCF3mwS9_4rYJ42cSl_kZaoB7SRQcWxEDPWvx_SEwntP8dmcMqBjN6kIsC9aMA
Content-Type: application/json
Content-Length: 228

{
  "title": "Desenvolvedor Java Sr.",
  "company": "TreinaWeb",
  "email": "contato@treinaweb.com.br",
  "techs": [
    "Java",
    "JPA",
    "Hibernate",
    "Spring Boot",
    "Spring Data JPA",
    "Spring Security"
  ],
  "status": "OPEN"
}
```

No exemplo acima o cliente fez a requisição HTTP com o método POST passando o header Authorization com o token que foi gerado anteriormente e no corpo da requisição os dados da vaga a ser cadastrada.

Agora na nossa API antes de realizar o cadastro dessa vaga a mesma deve capturar o token que foi enviado no header, verificar se esse token está assinado corretamente, verificar se o mesmo não está expirado e por fim verificar se o usuário que é identificado por esse token tem acesso a realizar tal operação.

Caso todas as verificações acima estejam corretas então o fluxo da aplicação deve seguir normalmente, o que nosso exemplo seria cadastrar a vaga e então retornar os dados da vaga cadastrada no corpo da resposta.

Abaixo o exemplo da resposta gerada.

Copiar

```json
HTTP/1.1 201
Server: Tomcat
Content-Type: application/json
Date: Fri, 22 Jul 2021 11:01 GMT-3

{
  "id": 1,
  "title": "Desenvolvedor Java Sr.",
  "description": null,
  "company": "TreinaWeb",
  "email": "contato@treinaweb.com.br",
  "techs": "Java, Hibernate, JPA, Spring Boot, Spring Data JPA, Spring Security",
  "status": "OPEN",
  "createdAt": "2021-07-22T11:01:00.340161",
  "modifiedAt": null
}
```



![Django - Desenvolvimento de APIs REST](https://d2knvm16wkt3ia.cloudfront.net/assets/svg-icon/django.svg)

##### CursoDjango - Desenvolvimento de APIs REST

[Conhecer o curso](https://www.treinaweb.com.br/curso/django-desenvolvimento-de-apis-rest)

## E o Refresh Token?

Uma vez que o token JWT estiver expirado o cliente terá que realizar uma nova requisição para a rota de autenticação enviando as credenciais do usuário, porém isso não traz uma boa usabilidade para o nosso projeto, convenhamos que ninguém gosta de ficar informando seu usuário e senha a todo momento enquanto está utilizando algum sistema.

Então, para evitar que o cliente tenha que novamente realizar uma nova autenticação sempre que o token estiver expirado podemos utilizar o refresh token.

Basicamente o refresh token é um token que tem como proposito atualizar a autenticação sem a necessidade de enviar as credenciais do usuário novamente, o refresh token pode ser implementado de várias maneiras, no nosso exemplo o refresh token também é um token JWT, porém assinado com uma chave diferente e com um tempo de expiração um pouco maior que o tempo de expiração do token de autenticação.

Quando o token de autenticação estiver expirado o cliente pode realizar uma requisição HTTP com o verbo POST para a rota de atualização da autenticação, que no nosso exemplo é a rota `/users/refresh/{refreshToken}` para gerar um novo token de autenticação.

Veja abaixo um exemplo dessa requisição.

Copiar

```json
POST /users/refresh/eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJjbGV5c29uIiwiZXhwIjoxNjI1Nzg5OTA1LCJpYXQiOjE2MjU3ODk4NDV9.CMo2Bk4JPaeKNeNrE5qRwqPCh3Ngxnjz-RyYipOvH1BR83gkxSTZ5V_P6fLEwf_YA86pDEVY-DVIOGOKwYDf9A HTTP/1.1
Host: localhost:8080
Content-Length: 0
```

Uma vez que a nossa API receba essa requisição, a mesma deverá capturar o refresh token, que nesse caso foi enviado a través de uma variável na rota, verificar se o refresh token está assinado corretamente, verificar se o mesmo não está expirado e então gerar um novo token de autenticação e um novo refresh token para o usuário referenciado pelo token.

Abaixo um exemplo da resposta para a requisição de atualização feita acima.

Copiar

```json
HTTP/1.1 200
Server: Tomcat
Content-Type: application/json
Date: Fri, 22 Jul 2021 11:10 GMT-3

{
  "token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJjbGV5c29uIiwiZXhwIjoxNjI1Nzg5ODg2LCJpYXQiOjE2MjU3ODk4NTZ9.O15AcH7AwEeb-O_J0ubpNWsO8l83JCiuDSbWBgQ8GCarvI34BR3SFfP2SF35t0vT2p3S7KKbX-NzHkXu5zOZog",
  "type": "Bearer",
  "expiresAt": "2021-07-22T11:30:00.000-03:00",
  "refreshToken": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJjbGV5c29uIiwiZXhwIjoxNjI1Nzg5OTE2LCJpYXQiOjE2MjU3ODk4NTZ9.cxyTB42lx9jr9UDChEmtrh-jukTX7X42ZIu4KmV9hjuWT2vnW6Fzr8FgwmCxBwHZddkEmdKiroy1R3tsLQgCeQ"
}
```

Dessa maneira o cliente só terá que realizar uma nova requisição para a rota de autenticação enviando as credenciais do usuário caso tanto o token de autenticação quanto o refresh token estejam expirados.



![Laravel - Desenvolvimento de APIs REST](https://d2knvm16wkt3ia.cloudfront.net/assets/svg-icon/laravel.svg)

##### CursoLaravel - Desenvolvimento de APIs REST

[Conhecer o curso](https://www.treinaweb.com.br/curso/laravel-desenvolvimento-de-apis-rest)

## Conclusão

Neste artigo vimos como podemos utilizar o token JWT para realizar o processo de autenticação em nossas APIs e também vimos como se dá esse processo de autenticação, também vimos o que é e como podemos utilizar o refresh token para melhor a usabilidade de nossas APIs.

Fica aqui a minha recomendação para que leiam o artigo [Implementando autenticação baseada em JWT em uma API RESTful JAX-RS](https://www.treinaweb.com.br/blog/implementando-autenticacao-baseada-em-jwt-em-uma-api-restful-jax-rs) onde é mostrado na prática como implementar um autenticação baseada em JWT em uma API desenvolvida com Java JAX-RS.

- [#HTTP](https://www.treinaweb.com.br/blog/tag/http)
- [#JSON](https://www.treinaweb.com.br/blog/tag/json)
- [#Autenticação](https://www.treinaweb.com.br/blog/tag/autenticacao)
- [#API REST](https://www.treinaweb.com.br/blog/tag/api-rest)
- [#JWT](https://www.treinaweb.com.br/blog/tag/jwt)