**[Java](https://www.treinaweb.com.br/blog/categoria/java)**

# O que é o Spring Security?

Neste artigo veremos o que é o Spring Security, quais suas funcionalidades e como ele pode aumentar a segurança de nossas aplicações Java.

 7 meses atrás

[Artigos](https://www.treinaweb.com.br/blog)O que é o Spring Security?

Um dos pontos cruciais durante o desenvolvimento de uma aplicação web é a sua segurança, a todo momento existem novas técnicas de invasão e formas da segurança de sua aplicação ser quebrada.

Manter-se atualizado sobre todas essas novas técnicas e formas de mitigá-las é algo bem difícil, pois existem muitos conceitos que envolvem o processo de manter uma aplicação web segura.

Por razão disso é bem comum terceirizarmos as implementações de segurança de nossas aplicações para bibliotecas ou frameworks que foram desenvolvidas por especialistas na área de segurança e por conta disso que neste artigo iremos falar sobre o Spring Security.



## O que é o Spring Security?

Primeiramente o Spring Security é um framework do [projeto Spring](https://www.treinaweb.com.br/blog/o-que-e-o-spring/) que possui um sistema de [autenticação e autorização](https://www.treinaweb.com.br/blog/autenticacao-x-autorizacao/) de alto nível e altamente customizável para aplicações Java.

A framework inclusive é a solução oficial para implementação de recursos de segurança em aplicações [Spring Boot](https://www.treinaweb.com.br/blog/o-que-e-o-spring-boot/), porém ele também pode ser utilizado em conjunto com outras frameworks.

## Funcionalidades do Spring Security

Ainda que o foco do Spring Security seja o sistema de autenticação e autorização, ele possui outras funcionalidades que aumentam a segurança de nossas aplicações Java, aqui vai uma lista de algumas de suas principais funcionalidades:

- Sistema de autenticação
- Sistema de autorização
- Proteção contra ataques como session fixation, clickjacking e [cross site request forgery](https://www.treinaweb.com.br/blog/cross-site-request-forgery-csrf-e-abordagens-para-mitiga-lo/)
- Integração com a Servlet API
- Integração opcional com o Spring Web MVC
- Fácil instalação através de um starter Spring Boot

## Autenticação

Basicamente podemos dizer que a autenticação é o login na nossa aplicação. Trata-se da etapa de verificação se um determinado usuário possui credenciais (geralmente, combinação de login e senha) válidas para acessar a nossa aplicação.

Especificamente o sistema de autenticação do Spring Security pode ser configurado para que utilize diferentes estratégias de autenticação, pois o mesmo trabalha com o conceito de providers de autenticação.

Os providers de autenticação são as estruturas responsáveis por efetivar as informações sobre os usuários que acessam a aplicação. Dessa maneira, você pode ter uma série de providers diferentes para utilizar nas aplicações.

O próprio Spring Security já expõe alguns providers para serem prontamente utilizados, como os providers baseados na JPA, providers baseados no JDBC e até mesmo providers baseados no padrão LDAP, permitindo que as aplicações executem o fluxo de autenticação e autorização através de servidores Active Directory, por exemplo.

## Autorização

Já a autorização é um processo que acontece depois da autenticação. É o momento onde a aplicação verifica se o usuário atualmente autenticado tem permissão de acesso a um determinado recurso.

O sistema de autorização do Spring Security também é bastante flexível, pois nos permite definir com facilidade quais são os possíveis tipos de usuários da nossa aplicação, como o sistema relaciona cada usuário com o seu determinado tipo e quais rotas de nossa aplicação cada tipo de usuário terá acesso.

Toda essa flexibilidade do Spring Security é possível por que o mesmo nos disponibiliza algumas interfaces que devem ser implementadas e assim informamos qual será a regra de negócio de nossa aplicação para a realização dos processos de autenticação e autorização.

## Armazenamento de Senha

Além dos sistemas de autenticação, autorização e proteção contra diferentes tipos de vulnerabilidades de aplicações web, o Spring Security também disponibiliza algoritmos de criptografias que evitam que sua aplicação guarde as senhas de seus usuários em texto puro no banco de dados.

Os algoritmos disponibilizados no Spring Security são o [bcrypt](https://docs.spring.io/spring-security/site/docs/5.4.6/reference/html5/#authentication-password-storage-bcrypt), [PBKDF2](https://docs.spring.io/spring-security/site/docs/5.4.6/reference/html5/#authentication-password-storage-pbkdf2), [scrypt](https://docs.spring.io/spring-security/site/docs/5.4.6/reference/html5/#authentication-password-storage-scrypt) e [argon2](https://docs.spring.io/spring-security/site/docs/5.4.6/reference/html5/#authentication-password-storage-argon2). Sendo o bcrypt o mais utilizado pela comunidade.

## Instalação do Spring Security

O processo de instalação do Spring Security dentro de uma aplicação Spring Boot é muito simples, já que o próprio Spring Boot possui um starter para instalar e pré-configurar o Spring Security.

No caso de uma aplicação Spring Boot que utilize o Maven basta adicionar o código abaixo dentro da tag de dependências no arquivo `pom.xml` do seu projeto.

Copiar

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Com isso o Spring Security já estará disponível na sua aplicação e basta apenas configura-lo da maneira que deseja.



![Spring Framework - Fundamentos](https://d2knvm16wkt3ia.cloudfront.net/assets/svg-icon/spring-boot.svg)

##### CursoSpring Framework - Fundamentos

[Conhecer o curso](https://www.treinaweb.com.br/curso/spring-framework-fundamentos)

## Conclusão

O Spring Security é um ótimo framework, principalmente se você já desenvolve aplicações web com o Spring Boot, pois com o Spring Security conseguimos uma grande melhoria na segurança de nossas aplicações sem a necessidade de termos que gastar horas implementando soluções para problemas recorrentes em aplicações web, como por exemplo o sistema de autenticação.

Recomendo fortemente que visitem a [documentação oficial](https://spring.io/projects/spring-security) do framework, ela mostra em detalhes como se dá o funcionamento do Spring Security e como você pode adiciona-lo em seu projeto.

- [#Java](https://www.treinaweb.com.br/blog/tag/java)
- [#segurança](https://www.treinaweb.com.br/blog/tag/seguranca)
- [#Spring](https://www.treinaweb.com.br/blog/tag/spring)
- [#spring security](https://www.treinaweb.com.br/blog/tag/spring-security)