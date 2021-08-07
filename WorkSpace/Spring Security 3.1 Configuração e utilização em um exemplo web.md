# Spring Security 3.1: Configuração e utilização em um exemplo web

## Veja neste artigo conceitos fundamentais de segurança que são utilizadas no Spring Security. Também veremos as novas funcionalidades trazidas no Spring 3.1 e como baixar e configurar o novo Spring 3.1.



[Artigos](https://www.devmedia.com.br/artigos/)[Java](https://www.devmedia.com.br/artigos/java)Spring Security 3.1: Configuração e utilização em um exemplo web

A segurança para aplicações web é considerada um tópico de extrema criticidade atualmente, principalmente com o surgimento de vírus e formas de exploração de software cada vez mais sofisticada. A segurança é um elemento chave em qualquer projeto.

Neste artigo começaremos a atacar os conceitos chaves e estratégias para segurança com um framework poderoso e muito utilizado no mercado, o Spring Security 3.1. Também veremos como adquirir, instalar, configurar e utilizar o Spring Security numa aplicação web.

### Autenticação

A autenticação é um dos dois conceitos chaves quando estamos desenvolvendo aplicações seguras. O outro conceito é o de autorização que será visto posteriormente. A autenticação basicamente identifica quem está tentando solicitar um recurso.

No dia a dia estamos sempre lidando com a autenticação. Por exemplo, quando acessamos nosso e-mail precisamos fornecer um username e uma senha para acessá-lo. O e-mail, por sua vez, bate as informações conferindo se o username está na base de dados e confere a senha de acordo com o username. Algumas aplicações ao invés de usarem uma base de dados utilizam um servidor de diretório como o Microsoft Active Directory. Outra forma de autenticação que podemos encontrar é quando sacamos dinheiro numa máquina de autoatendimento no banco. Nesse caso precisamos inserir o nosso cartão e depois prover uma senha. Esse tipo de autenticação é bastante similar com a autenticação do e-mail com username e senha, porém o username está codificado no cartão magnético. Outra forma de autenticação é quando ligamos o carro. Apesar de ser diferente dos outros exemplos, essa também é uma forma de autenticação. Existem ainda diversas outras formas de autenticação que podem ser aplicadas a software ou hardware, cada um tem seus prós e contras que devem ser avaliados dependendo do tipo de aplicação que devemos desenvolver.

Um sistema de software pode ser dividido em dois tipos de nível de autenticação: autenticado ou não autenticado, este último também chamado de anônimo.

Em uma aplicação onde o usuário está anônimo, isto é o usuário não possui uma identidade no sistema, normalmente esse usuário não poderá solicitar um log no sistema, o que se torna importante, por exemplo, quando necessitamos saber quando fizemos uso de um serviço ou para descobrir se alguém está se aproveitando de algo que não a pertence. Usuários anônimos também não podem visualizar informações mais sensíveis como nome, endereço, cartões de crédito, ou possuir permissão para funcionalidades que possam manipular o estado ou informações do sistema.

### Autorização

Autorização é o segundo conceito chave que é crucial na implementação e para o entendimento sobre a segurança de aplicações. Basicamente a autorização usa a informação que foi validada durante a autenticação para determinar se o acesso deveria ser concedido para um recurso em particular. Assim, as aplicações definem papéis aos usuários e baseando-se nos papéis desses usuários eles tem acesso a determinados recursos. Também devemos notar que para um usuário ter um papel ele deve estar autenticado, portanto deve passar pela autenticação no sistema.

Dessa forma a autorização envolve dois aspectos separados que devem ser combinados para descrever a acessibilidade do sistema. O primeiro é o mapeamento de um usuário autenticado para uma ou mais autorizações, chamado de papel. Por exemplo, um administrador de uma aplicação tem um nível de autorização, enquanto que um publicador dessa mesma aplicação tem outro nível de autorização. Provavelmente um publicador não conseguirá autorização para acessar um painel de administração. O segundo aspecto é a atribuição de autoridade para os recursos do sistema. Normalmente isso é feito quando o sistema está sendo desenvolvido através de uma declaração explicita no código ou através de parâmetros de configuração.

Os recursos que podem estar seguros são páginas web individuais, porções inteiras de um website ou porções de páginas individuais. Além disso, métodos de negócio também podem ser seguros.

Dizemos que quando um usuário tem permissão de acesso a um determinado recurso ele tem o acesso concedido, caso contrário o acesso é negado (denied).

### Segurança na Base de Dados

Alguns desenvolvedores insistem com velhas práticas perigosas como gravar em arquivos de texto puro as senhas dos usuários. Este tipo de prática facilita o acesso à aplicação por usuários maliciosos.

As aplicações possuem desde dados pessoais até informações financeiras. Usuários não autorizados capaz de acessos a esses dados podem expor a empresa a roubo de identidade ou a adulteração, o que pode provocar danos aos usuários e também sérias complicações à empresa.

Por isso torna-se essencial entendermos a camada de acesso a base de dados do Spring Security para o armazenamento de credenciais. O Spring Security usa conectividade com JDBC.

### SSL

O Spring Security oferece o protocolo SSL aos desenvolvedores. Este garante que a comunicação entre o browser do cliente e o servidor de aplicação está seguro contra muitos tipos de adulteração e espionagem.

### Spring Security 3.1

Spring Security é um framework para Java ou JavaEE que provê autenticação, autorização e diversas outras funcionalidades para aplicações corporativas. Iniciado em 2003 por Ben Alex o Spring Security é distribuído sob a licença Apache Licence. Ele oferece diversos recursos que permitem muitas práticas comuns de segurança serem declaradas ou configuradas de uma forma direta.

Utilizando Spring Security 3.1 podemos aumentar a segurança da nossa aplicação das seguintes formas:



- Segmentar os usuários do sistema em classes de usuário;
- Atribuir níveis de autorização à papéis de usuário;
- Atribuir papéis de usuário à classes de usuário;
- Aplicar regras de autenticação globais para recursos de aplicação;
- Aplicar regras de autorização à todos os níveis da arquitetura da aplicação;
- Previnir tipos de ataques comuns que manipulam ou roubam sessões do usuário.



O Spring Security foi criado com o intuito de preencher uma grande lacuna que existia nas bibliotecas de segurança do Java. As bibliotecas para segurança mais conhecidas para Java, tais como Java Authentication and Authorization Service (JAAS) ou Java EE Security, são excelentes bibliotecas que oferecem funções de autorização e autenticação. No entanto, Spring Security empacota tudo numa única solução. Além disso, o Spring Security pode ser integrado com diferentes soluções corporativas.

### Instalando o Spring Security 3.1 no Eclipse

Primeiramente criaremos um projeto web clicando com o botão direito do mouse em “Project Explorer” e selecionando “New”, “Dynamic Web Project”, conforme ilustra a **Figura 1**.

![img](https://arquivo.devmedia.com.br/artigos/Higor_Medeiros/spring_security/image001.jpg)

**Figura 1**. Criando um projeto web

Na próxima tela definimos um nome para o projeto como “ExemploSpringSecurity”. Deixamos as outras configurações como estão e selecionamos “Next” e posteriormente “Finish”.

Para baixar as bibliotecas do Spring Security podemos utilizar o pom.xml do maven da **Listagem 1.**

**Listagem 1**. Arquivo pom.xml para download do Spring Security 3.1



```
<dependency>
         <groupId>org.springframework.security</groupId>
         <artifactId>spring-security-config</artifactId>
         <version>3.1.0.RELEASE</version>
</dependency>
<dependency>
         <groupId>org.springframework.security</groupId>
         <artifactId>spring-security-core</artifactId>
         <version>3.1.0.RELEASE</version>
</dependency>
<dependency>
         <groupId>org.springframework.security</groupId>
         <artifactId>spring-security-web</artifactId>
         <version>3.1.0.RELEASE</version>
</dependency>
```



Também podemos fazer o download dos jars diretamente no site [projects.spring.io/spring-security.](http://projects.spring.io/spring-security)

É importante ressaltar que todas as versões devem ser compatíveis. Atualmente a versão mais indicada é a versão 3.1, no entanto, para o nosso projeto utilizaremos a versão 3.2. Fica a critério do leitor optar por qual das versões escolher. Como a versão 3.2 recém foi lançada fica como sugestão a utilização da versão 3.1 que está mais estável. Outra ressalva é que utilizando o Maven para baixar o Spring Security ele também fará o download das dependências do Spring Security. Caso contrário, teremos que ir atrás das dependências consultando a documentação do Spring Security ou testando diretamente numa aplicação de teste verificando o que será solicitado.

Para quem tiver interesse em utilizar o Spring Security 3.2 segue o maven na **Listagem 2.**

**Listagem 2**. Arquivo pom.xml para download do Spring Security 3.2



```
<dependencies>
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-web</artifactId>
        <version>3.2.0.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-config</artifactId>
        <version>3.2.0.RELEASE</version>
    </dependency>
</dependencies>
```



### Configuração

Outra configuração necessária é a criação de um arquivo de configuração XML.

O arquivo "security.xml" deve ser criado em "src/main/webapp/WEB-INF/spring/". O conteúdo do arquivo pode ser visto na **Listagem 3.**

**Listagem 3.** Exemplo de um arquivo security.xml



```
<?xml version="1.0" encoding="UTF-8"?>
<bean:beans
   xmlns:bean="http://www.springframework.org/schema/beans"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xmlns="http://www.springframework.org/schema/security"
   xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
   http://www.springframework.org/schema/security
   http://www.springframework.org/schema/security/spring-security-3.1.xsd">

         <http auto-config="true">
                   <intercept-url pattern="/index.jsp" access="ROLE_USER"/>
         </http>
         <authentication-manager>
                   <authentication-provider>
                            <user-service>
                                      <user name="user1@example.com"
                                               password="user1"
                                               authorities="ROLE_USER"/>
                                      </user-service>
                   </authentication-provider>
         </authentication-manager>

</bean:beans>
```



Também devemos configurar nosso arquivo web.xml, conforme a **Listagem 4.**

**Listagem 4.** Exemplo de configuração do arquivo web.xml



```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns="http://java.sun.com/xml/ns/javaee"
xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">

       <display-name>Exemplo Spring Security</display-name>

       <welcome-file-list>
             <welcome-file>/index.jsp</welcome-file>
       </welcome-file-list>

       <!-- Configurações SPRING SECURITY -->
       <context-param>
             <param-name>contextConfigLocation</param-name>
             <param-value>/WEB-INF/spring/security.xml</param-value>
       </context-param>

       <listener>
             <listener-class>
               org.springframework.web.context.ContextLoaderListener
             </listener-class>
       </listener>

       <filter>
             <filter-name>springSecurityFilterChain</filter-name>
             <filter-class>
               org.springframework.web.filter.DelegatingFilterProxy
             </filter-class>
       </filter>
       <filter-mapping>
             <filter-name>springSecurityFilterChain</filter-name>
             <url-pattern>/*</url-pattern>
       </filter-mapping>
       <!-- Fim Configurações SPRING SECURITY -->

</web-app>
```



O listener "ContextLoaderListener" é carregado quando iniciamos e paramos a interface ApplicationContext que é a raiz do Spring. ContextLoaderListener determina quais configurações serão utilizadas, procurando por uma tag <context-param> e por contextConfigLocation. No exemplo acima definimos que o caminho se encontra em "/WEB-INF/spring/security.xml". Também podemos utilizar "/WEB-INF/spring/*.xml". Porém, para termos um maior controle definimos o arquivo especificamente.

Também temos a declaração dos filtros que interceptam todas as requisições. Devemos sempre atentar que o filtro do Spring Security (springSecurityFilterChain) deve ser o primeiro filtro, caso tenhamos mais filtros na aplicação. Isso garante uma maior segurança para a aplicação.

O Security Spring não intercepta forwards, includes, erros ou outros tipos de requests. Porém, caso seja uma necessidade interceptar isto podemos fazer declarativamente como na **Listagem 5.**

**Listagem 5**. Declarando outras interceptações para o Spring Security



```
<filter-mapping>
         <filter-name>springSecurityFilterChain</filter-name>
         <url-pattern>/*</url-pattern>
         <dispatcher>REQUEST</dispatcher>
         <dispatcher>ERROR</dispatcher>
         ...
</filter-mapping>
```



DelegatingFilterProxy é um filtro servlet provido pelo Spring Web que delegará todo trabalho necessário para um bean do Spring que delega para a raiz ApplicationContext que deve implementar javax.servlet.Filter. Por padrão o bean será localizado pelo nome, usando o valor de <filter-name>. Por isso devemos assegurar que estamos usando springSecurityFilterChain como valor de <filter-name>.

Um pseudocódigo para entendermos um pouco sobre DelegatingFilterProxy pode ser visto na **Listagem 6.**

**Listagem 6**. Exemplo de DelegatingFilterProxy



```
public class DelegatingFilterProxy implements Filter {

         void doFilter(request, response, filterChain) {
                   Filter delegate =
                   applicationContext.getBean("springSecurityFilterChain")
                   delegate.doFilter(request,response,filterChain);
         }
}
```



Quando estamos trabalhando em conjunto com Spring Security, o filtro DelegatingFilterProxy delegará para o filtro FilterChainProxy, que foi criado com o arquivo security.xml. O FilterChainProxy permite ao Spring Security aplicar qualquer número de filtros servlets para requisições de servlets.

Um pseudocódigo para FilterChainProxy é mostrado na **Listagem 7.**

**Listagem 7**. Exemplo do FilterChainProxy



```
public class FilterChainProxy implements Filter {
         void doFilter(request, response, filterChain) {

                   // procura todos os filtros para este requisição
                   List<Filter> delegates =
                            lookupDelegates(request,response)

                   // invocada cada filtro a menos que o delegate decida parar
                   for delegate in delegates {
                            if continue processing
                                      delegate.doFilter(request,response,filterChain)
                   }

                   // se todos os filtros decidem que está ok
                   // é permitido que o resto da aplicação execute
                   if continue processing
                            filterChain.doFilter(request,response)
         }
}
```



Ambas as classes DelegatingFilterProxy e FilterChainProxy são a porta da frente do Spring Security, quando usadas em aplicações web. Portanto, essas classes são pontos importantes de debugarmos quando algum problema está ocorrendo e queremos saber o que está acontecendo.

Por fim, também criamos uma JSP simples chamada index.jsp com o código da **Listagem 8.**

**Listagem 8.** Exemplo de uma JSP



```
<%
       String txtInicial = "Página Inicial";
%>
<%=txtInicial%>
```



Nossa aplicação ficará com os mesmos arquivos da **Figura 2.**

![img](https://arquivo.devmedia.com.br/artigos/Higor_Medeiros/spring_security/image002.jpg)

**Figura 2**. Arquivos criados no projeto

As pastas que não foram expandidas não sofreram alterações e nem adições de arquivos ou configurações.

Com isso já podemos acessar a URL http://localhost:8080/ExemploSpringSecurity/ e conferir a que a tela inicial não será a nossa index.jsp e sim uma tela pedindo que o usuário se logue antes de acessar o recurso (**Figura 3**). Isso ocorre porque no arquivo security.xml estamos restringindo o acesso ao recurso índex.jsp que é a tela inicial chamada pela aplicação.

![img](https://arquivo.devmedia.com.br/artigos/Higor_Medeiros/spring_security/image003.jpg)

**Figura 3.** Tela inicial do sistema

Podemos experimentar colocar um user e senha inválidos como ilustrado na tela da **Figura 4.**

![img](https://arquivo.devmedia.com.br/artigos/Higor_Medeiros/spring_security/image004.jpg)

**Figura 4**. Digitando usuário e senha incorretos

Dessa forma, teremos como resposta a tela da **Figura 5.**

![img](https://arquivo.devmedia.com.br/artigos/Higor_Medeiros/spring_security/image005.jpg)

**Figura 5.** Tela exibida quando um usuário e senha estão incorretos

No entanto, se digitarmos o usuário e senha que foram especificados no arquivo security.xml que criamos anteriormente podemos ter acesso a página index.jsp que estávamos tentando acessar.

Na **Figura 6** o usuário e senha corretos:

![img](https://arquivo.devmedia.com.br/artigos/Higor_Medeiros/spring_security/image006.jpg)

**Figura 6**. Digitando usuário e senha corretamente

Como resultado, teremos a página solicitada anteriormente conforme ilustra a **Figura 7**.

![img](https://arquivo.devmedia.com.br/artigos/Higor_Medeiros/spring_security/image007.jpg)

**Figura 7**. Página sendo exibida após login

Podemos verificar que o Spring Security faz tudo que o usuário necessita, inclusive guardando o recurso inicialmente solicitado, funcionalidade que algumas bibliotecas não suportam.

Com simples configurações o Spring Security já ajudou os desenvolvedores a possuírem uma infraestrutura completa para gerenciar a segurança numa aplicação.

Concluindo, é importante entender essas funcionalidades podem ajudar quando estamos resolvendo problemas mais complexos ou para entendermos melhor o funcionamento do framework.

**Bibliografia**

[1] Spring Security, disponível em [projects.spring.io/spring-security](http://projects.spring.io/spring-security)

[2] Robert Winch, Peter Mularien. Spring Security 3.1.. Packt Publishing, 2012.

[3] Mick Knutson. Java EE 6 Cookbook for Securing, Tuning and Extending Enterprise Applications. Packt Publishing, 2012.

Tecnologias:

- [Java](https://www.devmedia.com.br/java/)
- JSP
- Spring Security
- XML