# Introdução ao Spring Security

## Veja nesse artigo como usar o Spring Security como alternativa para criação de uma camada de segurança robusta e de simples gerenciamento.

- 

[Artigos](https://www.devmedia.com.br/artigos/)[Java](https://www.devmedia.com.br/artigos/java)Introdução ao Spring Security

Com as exigências dos clientes e os requisitos ficando cada vez mais complexos, temos que de alguma forma centrar todas nossas forças em apenas resolver o problema do cliente e tentar abstrair ao máximo outras funcionalidades importantes mas não essenciais ao cliente, os requisitos não funcionais.

Neste artigo vamos tratar de um requisito não funcional: A Segurança. Para um cliente que solicita a construção de um software, é óbvio e evidente que a segurança é importante, ele não precisa dizer isso, pois é tão óbvio que você como profissional já deve saber desde o inicio. É como comprar um carro e dizer que você quer que seu carro tenha freios, isso é implícito.

Então o principal objetivo é não perder muito tempo desenvolvendo a segurança de uma aplicação, para isso recorremos a Frameworks que nos fornecem tal funcionalidade, o Spring Security. Há outros como o JAAS, jGuard (que funciona com o JAAS) e assim por diante, mas se você já utiliza o Spring, seria uma boa ideia aproveitá-lo para gerenciar a sua camada de segurança. A questão é, se você usa Spring apenas para injeção de dependência talvez não seja uma boa ideia, pois você tem uma ferramenta muito poderosa cheia de recursos e não está utilizando, é como comprar uma fábrica de automóveis apenas para ter um carro, totalmente desnecessário.

### Instalando e Usando o Spring Security

Em nossos exemplos vamos utilizar o Maven para gerenciar nossas dependências. Alguns podem pensar: Para que tantos Frameworks ? Isso não torna nossa aplicação pesada e lenta ? A resposta é: A quantidade de Frameworks que utilizamos é para tentar abstrair ao máximo a tecnologia e focar nas regras de negócio, afinal o cliente não quer saber como você faz, apenas que você o faça. Uma aplicação bem gerenciada e com os frameworks e configurações necessárias raramente terá problemas frequentes de lentidão.

Após muita introdução e explicações do porque usar o Spring Security, vamos enfim utilizá-lo. Vamos partir do principio que você já utiliza o Spring Framework, assim apenas adicionaremos as 3 bibliotecas necessárias ao Spring Secutiry, são elas: spring-security-core.jar, spring-security-web.jar e spring-security-cofing.jar. Vamos adicioná-las no nosso pom.xml, assim como na listagem 1.

**Listagem 1**: Adicionando bibliotecas necessárias

```
<properties>
		<spring.version>3.0.5.RELEASE</spring.version>
	</properties>

	<dependencies>

		<!-- Spring 3 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<!-- Spring Security -->
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-core</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-web</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-config</artifactId>
			<version>${spring.version}</version>
		</dependency>

	</dependencies>

</project>
```

O Maven se encarregará de realizar o download e gerencia das bibliotecas necessárias, descritas no pom.xml.

Agora iremos criar um arquivo chamado **spring-security.xml** que ficará na mesma pasta do **web.xml**. Neste arquivo diremos o que cada usuário vai acessar com qual regra.

**Listagem 2**: Definindo o spring-security.xml

```
<beans:beans xmlns="http://www.springframework.org/schema/security"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	<http://www.springframework.org/schema/beans/spring-beans-3.0.xsd>
	<http://www.springframework.org/schema/security>
	<http://www.springframework.org/schema/security/spring-security-3.0.3.xsd>">

	<http auto-config="true">
		<intercept-url pattern="/admin*" access="ADMIN_USER" />
	</http>

	<authentication-manager>
	  <authentication-provider>
	    <user-service>
		<user name="admin" password="123456" authorities="ADMIN_USER" />
	    </user-service>
	  </authentication-provider>
	</authentication-manager>

</beans:beans>
```

No arquivo XML acima definimos um usuário chamado 'admin' com senha '123456' que faz parte de um grupo chamado “ADMIN_USER”. Poderíamos aqui fazer uma mixagem de regras de acordo com cada grupo. O objetivo aqui é definir regras para os grupos e não para os usuários, pois estes farão parte dos grupos, assim poderemos criar quantos usuários quisermos sem ter que ficar mudando regras de grupos.

O próximo é habilitar na nossa aplicação o filtro do Spring Security, assim ele poderá “capturar” todas as páginas e realizar uma filtragem própria para garantir o acesso restrito por usuário e senha, fazemos isso através do web.xml.

**Listagem 3**: Configurando Spring Security no web.xml

```
<!-- Spring Security -->
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
```

Ainda no web.xml você configura o XML que será carregado para configuração do spring, o applicationContext.xml, então é nessa mesma tag que você deverá definir o spring-security.xml, assim como na listagem 4.

**Listagem 4**: Definindo o spring-security.xml para ser carregado na aplicação [web.xml]

```
<!-- Configuracao do Spring -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/spring-security.xml, /WEB-INF/applicationContext.xml</param-value>
	</context-param>
```

Provavelmente você pode encontrar alguns problemas ao tentar inicializar o Spring Secutiry, então vamos tentar preveni-los antes que eles aconteçam e tragam uma enorme dor de cabeça.

- Verifique se o trecho de código “${spring.version}” está de acordo com a sua versão do spring, pois esta também poder variar para “${org.springframework.version}”. De qualquer forma, agora você já sabe que há ainda uma outra possibilidade.
- Para facilitar a visualização do Spring Security funcionando vamos apontar a configuração abaixa para levar ao usuário a uma tela de login bem simples apenas para você ver o sistema funcionando. Para isso deixar seu spring-security.xml como na listagem abaixo.

**Listagem 5**: Autenticação HTTP Básica - Login Simples

```
<beans:beans xmlns="http://www.springframework.org/schema/security"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	<http://www.springframework.org/schema/beans/spring-beans-3.0.xsd>
	<http://www.springframework.org/schema/security>
	<http://www.springframework.org/schema/security/spring-security-3.0.3.xsd>">

	<http auto-config="true">
		<intercept-url pattern="/admin*" access="ADMIN_USER" />
		<http-basic />
	</http>

	<authentication-manager>
	  <authentication-provider>
	    <user-service>
		<user name="admin" password="123456" authorities="ADMIN_USER" />
	    </user-service>
	  </authentication-provider>
	</authentication-manager>

</beans:beans>
```

A linha “<http-basic />” dispensa a criação de uma tela de login própria, assim poupará você o trabalho de criação desta tela. O problema é que com esta login você apenas poderá realizar o login, sem nenhuma opção para recuperação de senhas. Ideal para sistemas onde não terá possibilidade de recuperação de senhas através de uma opção simples como: “Esqueci minha senha”.

Caso você queira capturar usuários e regras (grupos) via database, você pode substituir o bloco de código 'authentication-manager' pelo mostrado na listagem 6.

**Listagem 6**: Configurando usuários e ROLES via database

```
<authentication-manager>
	   <authentication-provider>
		<jdbc-user-service data-source-ref="dataSource"

		   users-by-username-query="
		      select username,password, enabled
		      from users where username=?"

		   authorities-by-username-query="
		      select u.username, ur.authority from users u, user_roles ur
		      where u.user_id = ur.user_id and u.username =?  "

		/>
	   </authentication-provider>
	</authentication-manager>
```

Esta foi uma introdução ao Spring Security como alternativa para criação de uma camada de segurança robusta e de simples gerenciamento, sem a necessidade de dias ou semanas trabalhando em um módulo próprio para aumentar a segurança do sistema.

Tecnologias:

- [Java](https://www.devmedia.com.br/java/)
- Maven
- Spring Securit
- XML