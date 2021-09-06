# Começando com Spring Security

[![Silas Candiolli](https://miro.medium.com/fit/c/96/96/1*HOic839bjnQsoqfu5-VrRg.jpeg)](https://medium.com/@candiolli?source=post_page-----86a3caec8c40-----------------------------------) [Silas Candiolli](https://medium.com/@candiolli?source=post_page-----86a3caec8c40-----------------------------------)  -  [Jul 13, 2020](https://medium.com/cwi-software/começando-com-spring-security-86a3caec8c40?source=post_page-----86a3caec8c40-----------------------------------) · 5 min read



![img](https://miro.medium.com/max/800/1*1-13QxXfUE1mdrK_MfqonQ.png)

Figura 1 — Spring Security https://spring.io/projects

OSpring Security, existente desde 2003, é um projeto muito conhecido e bastante utilizado em aplicações com Spring. Espero com esse artigo esclarecer algumas questões e talvez trazer novidades para alguns. Mas meu principal objetivo é responder duas perguntas…

**Do que o Spring Security nos protege? O que ele faz além de proteger?**

# Introdução

De fato, o Spring Security é uma biblioteca que fornece proteção, mas também autenticação, autorização e armazenamento de senhas. Sendo que, para autenticação, ele trabalha com vários protocolos. Para armazenar senhas em um banco de dados, ele tem opção de vários *encoders*. Também é possível utilizá-lo em projetos com Java EE, Spring [Webflux](https://spring.io/reactive), e Kotlin. Além disso, ele protege de ataques mais comuns como CSRF ou XSS.

Verificando atentamente a documentação do projeto Spring Security, é possível constatar que ele fornece um kit completo para proteger uma aplicação, bem como um kit flexível no qual pode-se selecionar e “plugar” os módulos que desejar.

Para facilitar o entendimento, foi criado um [projeto de demonstração](https://github.com/candiolli/spring-security/tree/master/demo-basic-auth), utilizando Basic Auth para autenticação, armazenamento de senhas em banco de dados e autorização.

# 1. Configuração

Para habilitar o Spring Security em um projeto é necessário, além das dependências, criar a classe de configuração. É nessa classe que é definido quais protocolos de autenticação, autorização, proteção e armazanamento. Essas configurações são melhor detalhadas no decorrer do artigo.

Bloco 1 — Classe responsável pela configuração do Spring Security

## **Autenticação**

A autenticação é a porta de entrada da aplicação e essa porta precisa estar protegida, tanto em uma aplicação externa quanto interna. Para isso, o Spring Security disponibiliza alguns mecanismos que podemos utilizar na nossa aplicação. São eles:

- [Username and Password](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#servlet-authentication-unpwd): quando se utiliza usuário e senha para logar. Para esse mecanismos podemos utilizar três formas: *Form login*, *Basic Authentication* e *Digest Authentication*.
- [OAuth2](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#oauth2login): quando se utiliza *OpenID connect,* é possível se autenticar em uma aplicação usando uma conta e servidor externo, como github.
- SAML2: é um padrão que surgiu antes do OAuth e que serve para compartilhamento de informações de login, podendo também atender o padrão *Single Sign On* (SSO).
- [Remember Me](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#servlet-rememberme): mecanismo utilizado para salvar a sessão do usuário logado na aplicação.
- Outros: [*JAAS Authentication*](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#servlet-jaas)*,* [*Central Authentication Server (CAS)*](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#servlet-cas)*,* [*OpenID*](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#servlet-openid)*,* [*Pre-Authentication Scenarios*](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#servlet-preauth) e [*X509 Authentication*](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#servlet-x509)*.*

**No projeto de demonstração desse artigo está sendo utilizado \*Basic Auth. É\* possível ver no trecho de código abaixo que foi extraído da linha 11 da classe** [***SecurityConfig.java\*.**](https://github.com/candiolli/spring-security/blob/master/demo-basic-auth/src/main/java/com/example/springsecurity/config/WebSecurityConfig.java)

```
http 
   .httpBasic()
```

## Autorização

Para autorização, o Spring Security se baseia nas *Authorities* do usuário que se autentica na aplicação. É possível usar credencias registradas i*nMemory (usuário e senha definido no arquivo de propriedades do projeto, sem acesso externo)* para testes ou implementar a estrutura de *UserDetailService (usuários, grupos e permissões salvas na base de dados).* Porém, para cada endpoint, é preciso informar quais ROLEs poderão acessá-lo; do contrário, todos poderão.

As *Authorities* representam as autoridades que foram concedidas ao *principal (*usuário logado na aplicação). Elas são lidas em forma de uma lista de objetos *GrantedAuthority,* que são inseridos no objeto *AuthenticationManager* do *SecurityContext* e são lidos posteriormente pelo *AccessDecisionManager.*

Existem outras funcionalidades legais, como *Pre-invocatio*n ou *After-invocation* para tratar as *Authorities*, ou trabalhar com *Hierarchical Roles*, onde se define uma hierarquia para as roles. Dessa forma, o usuário que tem a role ADMIN acessa também as informações da role USER, sem precisar estar vinculado a ela.

## Proteção

O Spring Security também fornece proteção contra vulnerabilidades comuns em aplicações web, que podem ser exploradas de diferentes formas. São elas: o [*Cross Site Request Forgery* (CSRF)](https://en.wikipedia.org/wiki/Cross-site_request_forgery), [*Security HTTP Response Headers*](https://owasp.org/www-project-secure-headers/), [HTTP](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#http) e [HTTP *Firewall*](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#servlet-httpfirewall).

Não é o objetivo desse artigo entrar em detalhes sobre cada vulnerabilidade, pois é um assunto mais extenso. Porém, foram adicionadas referências para quem tiver interesse em estender a pesquisa.

## Armazenamento de senhas

Outra funcionalidade legal do Spring Security é a [estrutura para armazenamento de senhas](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#appendix-schema). É possível salvar usuários, senhas e grupos no banco, e encriptar as senhas com sistemas de criptografias bem avançados como [bcrypt](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#authentication-password-storage-bcrypt), [PBKDF2](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#authentication-password-storage-pbkdf2), [scrypt](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#authentication-password-storage-scrypt) ou [argon2](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#authentication-password-storage-argon2).

Para isso, é preciso implementar o *UserDetailsService,* que vai fornecer métodos para consultar os usuários na base de dados. Conforme a classe [*MyUserDetailsService.java*](https://github.com/candiolli/spring-security/blob/master/demo-basic-auth-db/src/main/java/com/example/springsecurity/security/MyUserDetailsService.java) abaixo:

Bloco 2 — Classe de serviço que implementa UserDetailsService

Após criar a classe [*MyUserDetailsService.java*](https://github.com/candiolli/spring-security/blob/master/demo-basic-auth-db/src/main/java/com/example/springsecurity/security/MyUserDetailsService.java)*,* é preciso informar na configuração que será utilizado um *userDetailsService,* conforme linha 37 da classe [*SecurityConfig.java*](https://github.com/candiolli/spring-security/blob/master/demo-basic-auth-db/src/main/java/com/example/springsecurity/config/SecurityConfig.java).

```
builder
  .userDetailsService(userDetailsService)
  .passwordEncoder(new BCryptPasswordEncoder())
```

Lembrando que é preciso criar as classes de repositórios e domínios. Porém, a estrutura de banco de dados pode ser criada e alimentada automaticamente, conforme o projeto de demonstração está fazendo. Isso ocorre porque, no arquivo [application.yml](https://github.com/candiolli/spring-security/blob/master/demo-basic-auth-db/src/main/resources/application.yml), linha 12, existe a configuração *ddl-auto: create-drop.*

```
jpa:
  hibernate:
    ddl-auto: create-drop
```

Para o projeto alimentar as tabelas do banco de dados, é necessário criar o arquivo [*/resources/import.sql*](https://github.com/candiolli/spring-security/blob/master/demo-basic-auth-db/src/main/resources/import.sql)*,* e nele por o script necessário.

# 2. Execução

Agora que está entendido o que cada parte do código esta fazendo, é possível executar o projeto e ver o resultado. O projeto de demonstração de encontra no [GitHub](https://github.com/candiolli/spring-security/tree/master/demo-basic-auth-db), e pode ser clonado e executado localmente.

Na raíz do projeto, execute os comandos:

```
$ ./gradlew build
$ ./gradlw bootRun
```

Assim, sua aplicação deve estar disponível na porta 8080, mais ou menos como mostra o log abaixo:

![img](https://miro.medium.com/max/60/1*Bl29M_E-o-IpDHWRw3A4mA.png?q=20)

![img]()

Figura 2— Log exibido ao rodar o projeto

Para testar, execute o seguinte comando em algum terminal bash. Antes, é preciso gerar o *Authorization* baseado no usuário e senha que podem acessar os *endpoints.* Para gerar o *Authorization*, existem opções na internet como o [blitter.se](https://www.blitter.se/utils/basic-authentication-header-generator/) (você pode utilizar usuário `silas`, senha `123`).

```
curl --location --request GET 'localhost:8080/books' \
--header 'Authorization: Basic c2lsYXM6MTIz' \
--header 'Cookie: JSESSIONID=56AD4F472FF75F792FD0F2415ED82A6D'
```

O resultado que você deve receber será como o abaixo:

![img](https://miro.medium.com/max/60/1*4B6od5_awa6FqpaFZqT9sw.png?q=20)

![img]()

Figura 3— Resposta retornada do serviço /books

# 3. Considerações finais

Espero que esse artigo te ajude a entender o *Spring Security,* e dê uma perspectiva de onde ele pode chegar. O objetivo era introduzir o assunto e, assim, ter uma base para testar recursos mais avançados.

Segurança é algo essencial para todas as aplicações, e baseado nos estudos realizados nesse artigo, ficou evidente que o *Spring Security* é uma biblioteca que pode suprir essa necessidade.

# Referências

[Spring Security ReferenceImagine you're designing an application for a pet clinic. There will be two main groups of users of your Spring-based…docs.spring.io](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/#servlet-applications)



Thanks to Pablo Oliveira and Alexandre Silveira. 

- [Java](https://medium.com/cwi-software/tagged/java)
- [Spring Security](https://medium.com/cwi-software/tagged/spring-security)
- [Security](https://medium.com/cwi-software/tagged/security)

