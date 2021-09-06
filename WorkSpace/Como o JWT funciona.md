# Como o JWT funciona

## JWT, sigla para JSON Web Token, é uma técnica definida na RFC 7519 para autenticação remota entre duas partes. Ele é uma das formas mais utilizadas para autenticar usuários em APIs RESTful.



[Artigos](https://www.devmedia.com.br/artigos/)[JavaScript](https://www.devmedia.com.br/artigos/javascript)Como o JWT funciona

### O que é JSON Web Token?

**JWT (JSON Web Token) é um método RCT 7519 padrão da indústria para realizar autenticação entre duas partes por meio de um token assinado que autentica uma requisição web**. Esse token é um código em Base64 que armazena objetos JSON com os dados que permitem a autenticação da requisição.

No exemplo da **Figura 1** vemos um cliente que enviará uma requisição HTTP ao endpoint de autenticação de uma API. Nela o cliente envia, no corpo da requisição dados como endereço de e-mail e senha.

Saiba mais: [JWT: Web services seguros em Java](https://www.devmedia.com.br/curso/jwt-web-services-seguros-em-java/2152)

![Cliente enviando requisição com dados de autenticação](https://arquivo.devmedia.com.br/artigos/40265/figura_1.png)**Figura 1**. Cliente enviando requisição com dados de autenticação

Uma vez que os dados enviados pelo cliente tenham sido autenticados no servidor, este **criará um token JWT** assinado com um segredo interno da API e enviará este token de volta ao cliente, como mostra a **Figura 2**.

![Servidor envia o token assinado ao cliente](https://arquivo.devmedia.com.br/artigos/40265/figura_2.png)**Figura 2**. Servidor envia o token assinado ao cliente

Provido com o token autenticado, o cliente possui acesso aos endpoints da aplicação que antes lhes eram restritos. Para realizar esse acesso, conforme mostra a **Figura 3** é preciso informar esse token no header Authorization da requisição e, por convenção, após a palavra Bearer.

![Usuário com token autenticado pode acessar endpoints restritos](https://arquivo.devmedia.com.br/artigos/40265/figura_3.png)**Figura 3**. Usuário com token autenticado pode acessar endpoints restritos

### Como é por dentro?

Um JSON Web Token é composto por três componentes básicos: HEADER, PAYLOAD e SIGNATURE. Vamos passar por cada um desses componentes com um exemplo.

#### Header

É o cabeçalho do token e contém dois campos: alg, que informa o algoritmo usado para criar a hash da assinaturas; e typ, que indica que este se trata de um token JWT.

```
{
    "alg": "HS256",
    "typ": "JWT"
}
```

#### Payload

É o componente onde se encontram os dados referentes à própria autenticação.

```
{
    "email": "aylan@boscarino.com",
    "password": "ya0gsqhy4wzvuvb4"
}
```

#### Signature

É a assinatura única de cada token que é gerada a partir de um algoritmo de criptografia e tem seu corpo com base no header, no payload e no segredo definido pela aplicação. No próximo tópico entraremos em mais detalhes.

### Construção de um token

**A assinatura de um JSON Web Token é seu componente mais sensível por tratar justamente da segurança deste token**. Por conta disto existe uma fórmula padrão para que o token seja adequadamente assinado, exigindo que o token seja uma hash em Base64 gerada de um algoritmo de criptografia, por exemplo SHA256 ou SHA512, e essa hash precisa ser feita a partir do header e do payload do token.

A seguir temos um exemplo ilustrando como criar uma signature no padrão JWT utilizando o header e o payload que citamos previamente. O exemplo é feito utilizando Node.js, porém qualquer tecnologia pode ser aplicada para esse fim, contanto que siga o padrão da especificação JWT:

```
const crypto = require('crypto');

const header = JSON.stringify({
    'alg': 'HS256',
    'typ': 'JWT'
});

const payload = JSON.stringify({
    'email': 'aylan@boscarino.com',
    'password': 'ya0gsqhy4wzvuvb4'
});

const base64Header = Buffer.from(header).toString('base64').replace(/=/g, '');
const base64Payload = Buffer.from(payload).toString('base64').replace(/=/g, '');
const secret = 'my-custom-secret';

const data = base64Header + '.' + base64Payload;

const signature = crypto
    .createHmac('sha256', secret)
    .update(data)
    .digest('base64');

const signatureUrl = signature
    .replace(/\+/g, '-')
    .replace(/\//g, '_')
    .replace(/=/g, '')

console.log(signatureUrl);
```

- Linha 1

  : Importamos a biblioteca interna do Node.js

   

  crypo

  , que possui as ferramentas padrão de criptografia da plataforma:

  ```
  const crypto = require('crypto');
  ```

- Linhas 3 a 8

  : Transformamos o objeto JSON do header e do payload em strings simples:

  ```
  const header = JSON.stringify({
      'alg': 'HS256',
      'typ': 'JWT'
  });
  ```

  ```
  const payload = JSON.stringify({
      'email': 'aylan@boscarino.com',
      'password': 'ya0gsqhy4wzvuvb4'
  });
  ```

- Linhas 13 e 14

  : Convertemos o header e o payload para o formato Base64, conforme manda a

   

  especificação do JWT

  . Quando criamos uma string em Base64 é comum ter um ou mais caracteres de

   

  =

   

  em seu final: isso ocorre porque o número de caracteres de uma string Base64 é sempre múltiplo de 4 e quando isso não acontece naturalmente a string é preenchida com iguais

   

  =

  . Para remove-los utilizamos um replace:.

  ```
  const base64Header = Buffer.from(header).toString('base64').replace(/=/g, '');
  const base64Payload = Buffer.from(payload).toString('base64').replace(/=/g, '');
  ```

- Linha 16

  : Salvamos o segredo do nosso exemplo em uma constante. Um segredo pode ser qualquer string, a partir da qual será feito o hashing da nossa assinatura. Quanto mais complexo for o segredo, mais seguro é o nosso JWT:

  ```
  const secret = 'my-custom-secret';
  ```

- Linha 18

  : Para criarmos a signature precisamos primeiro concatenar ambas as strings em Base64 do header e do payload com um ponto, seguindo um formato específico:

   

  header.payload

  .

  ```
  const data = base64Header + '.' + base64Payload;
  ```

- Linha 20

  : Criamos efetivamente a signature utilizando o método

   

  createHmac

   

  da biblioteca crypto. Este método retornará um objeto do tipo

   

  hmac

  . Executaremos seu método update com os dados que queremos assinar e, por fim,

   

  digest

   

  com o formato da hash que o JWT exige: Base64.

  ```
  const signature = crypto
      .createHmac('sha256', secret)
      .update(data)
      .digest('base64');
  ```

- Linha 25

  : O formato Base64 utiliza os caracteres

   

  +

   

  e

   

  /+

   

  por hífen

   

  \-

   

  e as barras

   

  /

   

  por underscores

   

  _

  :

  ```
  const signatureUrl = signature
      .replace(/\+/g, '-')
      .replace(/\//g, '_')
      .replace(/=/g, '')
  ```

- Linha 30

  : Imprimimos nosso token no console para visualizá-lo.

  ```
  console.log(signatureUrl);
  ```

Com a assinatura concluída terminamos o token concatenando seu header, payload e signature, no formato Base64 adequado para URL em uma única string dividida por pontos seguindo a ordem: header.payload.signature.

Seguindo nosso exemplo ficaria:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJlbWFpbCI6ImF5bGFuQGJvc2Nhcmluby5
jb20iLCJwYXNzd29yZCI6InlhMGdzcWh5NHd6dnV2YjQifQ.yN_8-
Mge9mFgsnYHnPEh_ZzNP7YKvSbQ3Alug9HMCsM
```



### Verificação de um token

Com um token construído e seguro, é matematicamente impossível decodificar a assinatura sem ter o segredo-chave da aplicação. Porém, uma vez em posse do segredo, qualquer aplicação pode decodificar a assinatura e verificar se ela é válida. Isso é feito gerando uma signature utilizando o header e o payload fornecidos pelo cliente para então comparamos essa signature gerada com a presente no token enviado pelo cliente. Uma vez que as assinaturas sejam idênticas, podemos permitir o acesso do cliente a uma área restrita da nossa aplicação.

#### Confira também

[![O que são Servlets?](https://arquivo.devmedia.com.br/cursos/imagem/curso_o-que-sao-servlets_2165.png)O que são Servlets?Curso](https://www.devmedia.com.br/curso/curso-servlets/2165)

[![O que é JWT?](https://arquivo.devmedia.com.br/cursos/imagem/curso_o-que-e-jwt_2169.png)O que é JWT?Curso](https://www.devmedia.com.br/curso/o-que-e-jwt/2169)

[![JWT: Web services seguros em Java](https://www.devmedia.com.br/imagens/articles/curso_jwt-web-services-seguros-em-java_2152.png)JWT: Web services seguros em JavaCurso](https://www.devmedia.com.br/curso/jwt-web-services-seguros-em-java/2152)

Tecnologias:

- [JavaScript](https://www.devmedia.com.br/javascript/) 
- JWT