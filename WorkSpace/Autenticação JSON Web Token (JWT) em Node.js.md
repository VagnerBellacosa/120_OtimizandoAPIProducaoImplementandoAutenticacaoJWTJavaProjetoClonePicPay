# Autenticação JSON Web Token (JWT) em Node.js

![Luiz Duarte](https://www.gravatar.com/avatar/93a9abccc4d85eefdb90b62bd810342e?d=mm&s=128)

Escrito por [**Luiz Duarte**](https://www.luiztools.com.br/post/author/luiztools/)em 10/11/2020

##### JUNTE-SE A MAIS DE 22 MIL PROFISSIONAIS DE TI

### Entre para minha lista e receba conteúdos exclusivos e com prioridade

Enviar

***Este tutorial tem versão em videoaula no meu [curso de Node.js e MongoDB](https://www.luiztools.com.br/curso-nodejs?utm_source=blog&utm_medium=link&utm_campaign=curso-node&utm_content=jwt)!***

Faz algum tempo que ensino aqui no blog a como fazer APIs em Node.js, uma vez que este é o cenário mais comum de uso com a plataforma.

Já ensinei [como o HTTP funciona](https://www.luiztools.com.br/post/http-para-programadores-node-js/) e a fazer APIs com [MongoDB](https://www.luiztools.com.br/post/como-criar-uma-api-com-nodejs/), [MySQL](https://www.luiztools.com.br/post/como-usar-nodejs-mysql/) e [SQL Server](https://www.luiztools.com.br/post/tutorial-node-js-com-ms-sql-server/). Já ensinei a como estruturar suas [APIs em formato de micro serviços](https://www.luiztools.com.br/post/arquitetura-de-micro-servicos-em-node-js-mongodb/) e como criar um [API Gateway](https://www.luiztools.com.br/post/api-gateway-em-arquitetura-de-microservices-com-node-js/).

No entanto, pouco falei aqui sobre segurança em APIs, então hoje vamos falar de JSON Web Tokens como uma forma de garantir a autenticação e autorização de APIs de maneira bem simples e segura, sendo o JWT um padrão para segurança de APIs RESTful atualmente.

Veremos neste tutorial:

1. [JSON Web Tokens](https://www.luiztools.com.br/post/autenticacao-json-web-token-jwt-em-nodejs/#1)
2. [Estruturando a API](https://www.luiztools.com.br/post/autenticacao-json-web-token-jwt-em-nodejs/#2)
3. [Adicionando o JWT](https://www.luiztools.com.br/post/autenticacao-json-web-token-jwt-em-nodejs/#3)
4. [Autenticação](https://www.luiztools.com.br/post/autenticacao-json-web-token-jwt-em-nodejs/#4)
5. [Autorização](https://www.luiztools.com.br/post/autenticacao-json-web-token-jwt-em-nodejs/#5)

Se preferir, pode assistir ao vídeo abaixo ao invés de ler o artigo.

<iframe loading="lazy" id="_ytid_81797" width="480" height="360" data-origwidth="480" data-origheight="360" src="https://www.youtube.com/embed/D0gpL8-DVrc?enablejsapi=1&amp;autoplay=0&amp;cc_load_policy=0&amp;cc_lang_pref=&amp;iv_load_policy=1&amp;loop=0&amp;modestbranding=0&amp;rel=0&amp;fs=1&amp;playsinline=0&amp;autohide=2&amp;theme=dark&amp;color=red&amp;controls=1&amp;" class="__youtube_prefs__  epyt-is-override  no-lazyload" title="YouTube player" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" data-no-lazy="1" data-skipgform_ajax_framebjll="" style="box-sizing: border-box; margin: 0px; padding: 0px; border: 0px; font: inherit; vertical-align: baseline; outline: 0px; text-decoration: none; position: absolute; left: 0px; top: 0px; width: 700px; height: 406px;"></iframe>

Vamos lá!

[![Curso FullStack](https://www.luiztools.com.br/wp-content/uploads/2021/01/banner-fullstack.jpg)](https://www.luiztools.com.br/curso-fullstack?utm_source=blog&utm_medium=banner&utm_campaign=curso-fullstack&utm_content=jwt)

### #1 – JSON Web Tokens

JWT, resumidamente, é uma string de caracteres que, caso cliente e servidor estejam sob HTTPS, permite que somente o servidor que conhece o ‘segredo’ possa validar o conteúdo do token e assim confirmar a autenticidade do cliente. O token **não é** “criptografado”, mas “assinado”, de forma que só com o secret essa assinatura possa ser comprovada, o que impede que atacantes “criem” tokens por conta própria.

Em termos práticos, quando um usuário se autentica no sistema ou web API (com usuário e senha), o servidor gera um token com data de expiração pra ele. Durante as requisições seguintes do cliente, o JWT é enviado no cabeçalho da requisição e, caso esteja válido, a API irá permitir acesso aos recursos solicitados, sem a necessidade de se autenticar novamente.

O diagrama abaixo mostra este fluxo, passo-a-passo:

![Fluxo JWT](https://www.luiztools.com.br/wp-content/uploads/2018/05/jwt-1024x704.png)

O conteúdo do JWT é um payload JSON que pode conter a informação que você desejar, que lhe permita mais tarde conceder autorização a determinados recursos para determinados usuários. Minimamente ele terá o ID do usuário autenticado ou da sessão (se estiver trabalhando com este conceito), mas pode conter muito mais do que isso, conforme a sua necessidade, embora guardar conteúdos “sensíveis” no seu interior não é uma boa ideia, pois como disse antes, ele **não é** criptografado.

### #2 – Estruturando a API

Antes de começarmos esta API Node.js usando JWT vale ressaltar que o foco aqui é mostrar o funcionamento do JWT e não o funcionamento de uma API real. Caso você já possua uma web API, pule esta seção. Caso contrário, use a API fake abaixo, que vamos mockar os dados retornados pela API e as credenciais de autenticação inicial para ir logo para a geração e posterior verificação dos tokens.

Salve o seguinte código JavaScript em um arquivo /jwt/index.js:

| 1234567891011121314151617181920 | //index.js**const** http = require('http'); **const** express = require('express'); **const** app = express();  **const** bodyParser = require('body-parser');app.**use**(bodyParser.json()); app.get('/', (req, res, next) => {  res.json({message: "Tudo ok por aqui!"});}) app.get('/clientes', (req, res, next) => {   console.log("Retornou todos clientes!");  res.json([{id:1,nome:'luiz'}]);})  **const** server = http.createServer(app); server.listen(3000);console.log("Servidor escutando na porta 3000...") |
| ------------------------------- | ------------------------------------------------------------ |
|                                 |                                                              |

Com esse JS em mãos, vamos instalar algumas dependências na nossa aplicação para fazê-la funcionar:

| 1    | npm i express body-parser |
| ---- | ------------------------- |
|      |                           |

Rode a API com “node index” e ao acessar localhost:3000 deve listar apenas uma mensagem de OK e ao acessar o caminho /clientes no navegador, deve listar um array JSON como abaixo.

![API fake funcionando](https://www.luiztools.com.br/wp-content/uploads/2018/06/api-fake-funcionando.png)

Isso mostra que a API está funcionando em ambas as rotas e sem segurança, afinal, não tivemos de nos autenticar para fazer os GETs que fizemos.

### #3 – Adicionando o JWT

Agora, vamos instalar duas novas dependências para incrementar o nosso projeto, permitindo adicionar autenticação via JWT:

| 1    | **npm** i jsonwebtoken dotenv-safe |
| ---- | ---------------------------------- |
|      |                                    |

A saber:

- **jsonwebtoken**: pacote que implementa o protocolo JSON Web Token;
- **dotenv-safe**: pacote para gerenciar facilmente variáveis de ambiente, não é obrigatório para JWT, mas uma boa prática para configurações em geral;

Vamos começar usando o dotenv-safe, criando dois arquivos. Primeiro, o arquivo .env.example, com o template de variáveis de ambiente que vamos precisar:

| 12   | # .env.example, commit **to** repoSECRET= |
| ---- | ----------------------------------------- |
|      |                                           |

E depois, o arquivo .env, com o valor do segredo à sua escolha:

| 12   | #.env, don't commit **to** repoSECRET=mysecret |
| ---- | ---------------------------------------------- |
|      |                                                |

Este segredo será utilizado pela biblioteca jsonwebtoken para assinar o token de modo que somente o servidor consiga validá-lo, então é de bom tom que seja um segredo forte.

Para que esse arquivo de variáveis de ambiente seja carregado assim que a aplicação iniciar, adicione a seguinte linha logo no início do arquivo index.js da sua API, aproveitando para inserir também as linhas dos novos pacotes que vamos trabalhar:

| 12   | require("dotenv-safe").config();**const** jwt = require('jsonwebtoken'); |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

Isso deixa nossa API minimamente preparada para de fato lidar com a autenticação e autorização.

[![img](https://www.luiztools.com.br/wp-content/uploads/2021/05/banner-curso-bot.jpg)](https://www.luiztools.com.br/curso-beholder?utm_source=blog&utm_medium=link&utm_campaign=auto&utm_content=curso-beholder)

### #4 – Autenticação

Caso você não saiba a diferença, autenticação é você provar que você é você mesmo. Já autorização é você provar que possui permissão para fazer ou ver o que você está tentando.

Antes de gerar o JWT é necessário que o usuário passe por uma autenticação tradicional, geralmente com usuário e senha. Essa informação fornecida é validada junto a uma base de dados e somente caso ela esteja ok é que geramos o JWT para ele.

Assim, vamos criar uma nova rota /login que vai receber um usuário e senha hipotético e, caso esteja ok, retornará um JWT para o cliente:

| 1234567891011121314 | //authenticationapp.post('/login', (req, res, next) => {  //esse teste abaixo deve ser feito no seu banco de dados  **if**(req.body.user === 'luiz' && req.body.password === '123'){   //auth ok   const id = 1; //esse id viria do banco de dados   **const** token = jwt.sign({ id }, process.env.SECRET, {    expiresIn: 300 // expires in 5min   });   **return** res.json({ auth: **true**, token: token });  }    res.status(500).json({message: 'Login inválido!'});}) |
| ------------------- | ------------------------------------------------------------ |
|                     |                                                              |

Aqui temos o seguinte cenário: o cliente posta na URL /login um user e um pwd, que simulo uma ida ao banco meramente verificando se user é igual a luiz e se pwd é igual a 123. Estando ok, o banco me retornaria o ID deste usuário, que simulei com uma constante.

Esse ID está sendo usado como payload do JWT que está sendo assinado, mas poderia ter mais informações conforme a sua necessidade. Além do payload, é passado o SECRET, que está armazenado em uma variável de ambiente como mandam as boas práticas de segurança. Por fim, adicionei uma expiração de 5 minutos para este token, o que quer dizer que o usuário autenticado poderá fazer suas requisições por 5 minutos antes do sistema ou Web API pedir que ele se autentique novamente.

Caso o user e password não coincidam, será devolvido um erro ao usuário.

Vamos aproveitar o embalo e vamos criar uma rota para o logout:

| 123  | app.post('/logout', **function**(req, res) {  res.json({ auth: **false**, token: **null** });}) |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

Aqui apenas dizemos ao requisitante para anular o token, embora esta rota de logout seja completamente opcional uma vez que no próprio client-side é possível destruir o cookie ou localstorage de autenticação e com isso o usuário está automaticamente deslogado. Se nem o client-side ou o server-side destrua o token, ele irá expirar sozinho em 5 minutos.

Se quiser adicionar uma camada a mais de segurança, você pode ter uma blacklist de tokens que fizeram logout, para impedir reuso deles depois do logout realizado. Apenas lembre-se de limpar essa lista depois que eles expirarem ou use um [cache em Redis](https://www.luiztools.com.br/post/como-criar-um-cache-de-dados-com-redis-em-node-js/) com expiração automática.

Mas será que está funcionando?

### #5 – Autorização

Aí que entra a autorização!

Vamos criar uma função de verificação em nosso index.js, com o intuito de, dada uma requisição que está chegando, a gente verifica se ela possui um JWT válido:

| 123456789101112 | **function** verifyJWT(req, res, next){  **const** token = req.headers['x-access-token'];  **if** (!token) **return** res.status(401).json({ auth: **false**, message: 'No token provided.' });    jwt.verify(token, process.env.SECRET, **function**(err, decoded) {   **if** (err) **return** res.status(500).json({ auth: **false**, message: 'Failed to authenticate token.' });      // se tudo estiver ok, salva no request para uso posterior   req.userId = decoded.id;   next();  });} |
| --------------- | ------------------------------------------------------------ |
|                 |                                                              |

Aqui eu obtive o token a partir do cabeçalho x-access-token, que se não existir já gera um erro logo de primeira.

Caso exista, verificamos a autenticidade desse token usando a função verify, usando a variável de ambiente com o SECRET. Caso ele não consiga verificar o token, irá gerar um erro. Em seguida chamamos a função next que passa para o próximo estágio de execução das funções no pipeline do middleware do Express, mas não antes de salvar a informação do id do usuário para a requisição, visando poder ser utilizado pelo próximo estágio.

Ok, entendi que esta função atuará como um middleware, mas como usaremos a mesma?

Basta inserirmos sua referência na chamada GET /clientes que já existia em nossa API:

| 1234 | app.get('/clientes', verifyJWT, (req, res, next) => {   console.log("Retornou todos clientes!");  res.json([{id:1,nome:'luiz'}]);}) |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

Assim, antes de responder os GETs de clientes, a API vai criar essa camada intermediária de autorização baseada em JWT, que obviamente vai bloquear requisições que não estejam autenticada e autorizadas, conforme as suas regras para tal.

O resultado, tentando chamar a rota /clientes sem estar autenticado é esse:

![JWT não informado](https://www.luiztools.com.br/wp-content/uploads/2018/06/jwt-nao-informado.png)

Note que eu não disse para colocar esse middleware de segurança na chamada GET / pois vamos deixar ela pública, sempre acessível mesmo a usuários anônimos.

Mas voltando à nossa rota de clientes, para que seja possível acessá-la o client da API deve primeiro obter um token se autenticando com um usuário válido na rota POST de login. Essa requisição teremos de realizar usando POSTMAN, Insomnia ou similar (cURL,etc):

![Login via POSTMAN](https://www.luiztools.com.br/wp-content/uploads/2018/05/login-via-postman-720x409.png)

Na aplicação que for consumir a sua web API, deverá ser coletado esse token gerado e todas as requisições aos endpoints protegidos devem ser feitas passando o mesmo no header da requisição. Para simular isso, usaremos o POSTMAN novamente, passando o conteúdo do JWT no header x-access-token, como abaixo:

![JWT válido](https://www.luiztools.com.br/wp-content/uploads/2018/06/jwt-valido.png)

E por fim, como este token está programado para expirar 5 minutos após a sua criação, as requisições podem ser feitas usando o mesmo durante este tempo, sem novo login. Mas assim que ele expirar, receberemos como retorno…

![JWT Expirado](https://www.luiztools.com.br/wp-content/uploads/2018/06/jwt-expirado-720x348.png)

Bacana, não?!

E com isso encerro o tutorial de hoje. Note que foquei no uso do JWT, sem entrar em muitos detalhes de como você pode estruturar sua autenticação a partir do banco de dados (tabela/coleção login e senha) e nem como você pode estruturar o seu modelo de autorização (perfis de acesso, por exemplo).

Quem sabe não abordo este tema de maneira mais avançada e completa em outra oportunidade?

Por ora, abordei uma maneira ainda [mais segura de ter seu JWT neste artigo](https://www.luiztools.com.br/post/autenticacao-json-web-token-jwt-em-node-js-2/), caso esteja trabalhando com microservices e compartilhando token entre eles, mas não queira compartilhar secrets.

Se quiser conhecer outras formas de deixar suas Web APIs seguras, lei [este artigo aqui](https://www.luiztools.com.br/post/autenticacao-de-webapi-com-api-key/).

E, para mais uma dica de segurança com Node.js, assista ao vídeo abaixo:

<iframe loading="lazy" id="_ytid_15184" width="480" height="270" data-origwidth="480" data-origheight="270" src="https://www.youtube.com/embed/Kc2yuSdoxnQ?enablejsapi=1&amp;autoplay=0&amp;cc_load_policy=0&amp;cc_lang_pref=&amp;iv_load_policy=1&amp;loop=0&amp;modestbranding=0&amp;rel=0&amp;fs=1&amp;playsinline=0&amp;autohide=2&amp;theme=dark&amp;color=red&amp;controls=1&amp;" class="__youtube_prefs__  epyt-is-override  no-lazyload" title="YouTube player" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" data-no-lazy="1" data-skipgform_ajax_framebjll="" style="box-sizing: border-box; margin: 0px; padding: 0px; border: 0px; font: inherit; vertical-align: baseline; outline: 0px; text-decoration: none; position: absolute; left: 0px; top: 0px; width: 700px; height: 406px;"></iframe>

Um abraço e até mais!

***Curtiu o post? Então clica no banner abaixo e dá uma conferida no meu curso de Node.js!***

[![Curso Node.js e MongoDB](https://www.luiztools.com.br/wp-content/uploads/2020/08/curso-nodejs.png)](https://www.luiztools.com.br/curso-nodejs?utm_source=blog&utm_medium=banner&utm_campaign=curso-node&utm_content=jwt)

TAGS: [nodejs](https://www.luiztools.com.br/tag/nodejs/) [seguranca](https://www.luiztools.com.br/tag/seguranca/) [web](https://www.luiztools.com.br/tag/web/)