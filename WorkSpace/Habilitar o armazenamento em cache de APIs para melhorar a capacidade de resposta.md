# Habilitar o armazenamento em cache de APIs para melhorar a capacidade de resposta

[PDF](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/apigateway-dg.pdf#api-gateway-caching)

[RSS](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/amazon-apigateway-release-notes.rss)



Você pode habilitar o armazenamento em cache de APIs no Amazon API Gateway para armazenar em cache as respostas do seu endpoint. Com o armazenamento em cache, você pode reduzir o número de chamadas feitas para o endpoint e também melhorar a latência de solicitações para a sua API.

Quando o armazenamento em cache é habilitado para um estágio, o API Gateway armazena em cache as respostas do seu endpoint por um período Time-to-live (TTL – vida útil) especificado, em segundos. Depois, o API Gateway responde à solicitação examinando a resposta do endpoint no cache em vez de fazer uma solicitação ao seu endpoint. O valor de TTL padrão para o armazenamento em cache de APIs é de 300 segundos. O valor de TTL máximo é de 3600 segundos. TTL=0 significa que o armazenamento em cache está desabilitado.

O tamanho máximo de uma resposta que pode ser armazenada em cache é 1.048.576 bytes. A criptografia de dados de cache pode aumentar o tamanho da resposta quando está sendo armazenada em cache.

Este é um serviço qualificado da HIPAA. Para obter mais informações sobre a AWS, a Lei de Portabilidade e Responsabilidade de Seguro de Saúde de 1996 dos EUA (HIPAA) e o uso dos serviços da AWS para processar, armazenar e transmitir informações de saúde protegidas (PHI), consulte [Visão geral da HIPAA](http://aws.amazon.com/compliance/hipaa-compliance/).

**Importante**

Ao habilitar o armazenamento em cache para um estágio, somente métodos `GET` têm o armazenamento em cache habilitado por padrão. Isso ajuda a garantir a segurança e a disponibilidade da sua API. Você pode habilitar o armazenamento em cache para outros métodos, [substituindo as configurações do método](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/api-gateway-caching.html#override-api-gateway-stage-cache-for-method-cache).

**Importante**

O armazenamento em cache é cobrado por hora e não está qualificado para o nível gratuito da AWS.

## Habilitar o armazenamento em cache do Amazon API Gateway

No API Gateway, é possível habilitar o armazenamento em cache um estágio especificado.

Ao habilitar o armazenamento em cache, você deve escolher uma capacidade de cache. Em geral, uma capacidade maior proporciona melhor desempenho, mas também custa mais.

O API Gateway habilita o armazenamento em cache por meio da criação de uma instância de cache dedicada. Esse processo pode demorar até 4 minutos.

O API Gateway altera a capacidade de armazenamento em cache removendo a instância de cache existente e criando uma nova com capacidade modificada. Todos os dados armazenados em cache existentes são excluídos.

**nota**

A capacidade do cache afeta a CPU, a memória e a largura de banda da rede da instância de cache. Como resultado, a capacidade do cache pode afetar a performance do cache.

O API Gateway recomenda que você execute um teste de carga de 10 minutos para verificar se a capacidade do cache é apropriada para sua carga de trabalho. Certifique-se de que o tráfego durante o teste de carga corresponde ao tráfego da produção. Por exemplo, inclua padrões de tráfego crescente, constante e em picos. O teste de carga deve incluir respostas que podem ser servidas a partir do cache, bem como respostas exclusivas que adicionam itens ao cache. Monitore as métricas de latência, 4xx, 5xx, acertos de cache e erros de cache durante o teste de carga. Ajuste a capacidade do cache conforme necessário com base nessas métricas.

No console do API Gateway, configure o armazenamento em cache na guia **Settings (Configurações)** de um **Stage Editor (Editor de estágio)** denominado.

**Para configurar o armazenamento em cache de API para um determinado estágio:**

1. Acesse o console do API Gateway.
2. Selecione a API.
3. Escolha **Stages (Estágios)**.
4. Na lista **Stages (Estágios)** da API, escolha o estágio.
5. Escolha a guia **Configurações**.
6. Escolha **Enable API cache (Habilitar cache da API)**.
7. Aguarde a conclusão da criação do cache.

**nota**

A criação ou exclusão de um cache leva cerca de 4 minutos para ser concluída pelo API Gateway. Quando um cache é criado, o valor de **Cache status (Status de cache)** é alterado de `CREATE_IN_PROGRESS` para `AVAILABLE`. Quando a exclusão do cache é concluída, o valor de **Cache status (Status de cache)** muda de `DELETE_IN_PROGRESS` para uma string vazia.

Ao habilitar o armazenamento em cache dentro das **Cache Settings (Configurações de cache)** de um estágio, somente métodos `GET` são armazenados em cache. Para garantir a segurança e a disponibilidade da sua API, recomendamos não alterar essa configuração. No entanto, você pode habilitar o armazenamento em cache para outros métodos, [substituindo as configurações do método](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/api-gateway-caching.html#override-api-gateway-stage-cache-for-method-cache).

Se quiser verificar se o armazenamento em cache está funcionando como esperado, você tem duas opções gerais:

- Inspecionar as métricas do CloudWatch de **CacheHitCount** e **CacheMissCount** para a sua API e estágio.
- Colocar um carimbo de data/hora na resposta.

**nota**

Você não deve usar o cabeçalho `X-Cache` da resposta do CloudFront para determinar se a sua API está sendo atendida pela instância de cache do API Gateway.

## Substituir o armazenamento em cache de nível de estágios do API Gateway para armazenamento em cache de métodos

Você pode substituir as configurações de cache em nível de estágio habilitando ou desabilitando o armazenamento em cache para um método específico. Aumentando ou diminuindo seu período TTL ou ativando ou desativando a criptografia para respostas armazenadas em cache.

Se você antecipar que um determinado método armazenado em cache receberá dados confidenciais em suas respostas, em **Cache Settings (Configurações de cache)**, escolha **Encrypt cache data (Criptografar dados de cache)**.

**Para configurar o armazenamento em cache de API para métodos individuais usando o console:**

1. Inicie uma sessão no console do API Gateway em https://console.aws.amazon.com/apigateway.
2. Acesse o console do API Gateway.
3. Selecione a API.
4. Escolha **Stages (Estágios)**.
5. Na lista **Stages (Estágios)** da API, expanda o estágio e escolha um método na API.
6. Escolha **Override for this method (Sobreposição para esse método)** em **Settings (Configurações)**.
7. Na área **Cache Settings (Configurações de cache)**, desmarque **Enable Method Cache (Habilitar cache de método)** ou personalize as opções desejadas. (Esta seção é mostrada somente se o [armazenamento em cache em nível de estágio](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/api-gateway-caching.html#enable-api-gateway-caching) estiver ativado).

## Usar parâmetros de método ou integração como chaves de cache para indexar respostas em cache

Quando um método ou uma integração em cache tem parâmetros, que podem assumir a forma de cabeçalhos personalizados, caminhos de URL ou strings de consulta, você pode usar alguns ou todos os parâmetros para formar chaves de cache. O API Gateway pode armazenar as respostas do método em cache, dependendo dos valores de parâmetros usados.

**nota**

As chaves de cache são necessárias ao configurar o armazenamento em cache em um recurso.

Por exemplo, suponha que você tenha uma solicitação no seguinte formato:

```
GET /users?type=... HTTP/1.1
host: example.com
...
        
```

Nessa solicitação, `type` pode ter um valor de `admin` ou `regular`. Se você incluir o parâmetro `type` como parte da chave do cache, as respostas de `GET /users?type=admin` serão armazenadas em cache separadamente daquelas de `GET /users?type=regular`.

Quando uma solicitação de método ou integração usa mais de um parâmetro, você pode optar por incluir alguns ou todos os parâmetros para criar a chave de cache. Por exemplo, você pode incluir apenas o parâmetro `type` na chave de cache para a seguinte solicitação, feita na ordem listada dentro de um período de TTL:

```
GET /users?type=admin&department=A HTTP/1.1
host: example.com
...
        
```

A resposta dessa solicitação é armazenada em cache e usada para atender à seguinte solicitação:

```
GET /users?type=admin&department=B HTTP/1.1
host: example.com
...
        
```

Para incluir um parâmetro de solicitação de método ou integração como parte de uma chave de cache no console do API Gateway, selecione **Caching (Armazenamento em cache)** depois de adicionar o parâmetro.



![                 Incluir parâmetros de método ou integração como chaves de cache para indexar a resposta em cache             ](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/api-caching-including-parameter-as-cache-key.png)

## Liberar o cache de estágio de APIs no API Gateway

Quando o armazenamento em cache de APIs está habilitado, você pode descarregar o cache inteiro do estágio de API para garantir que os clientes da sua API obtenham as respostas mais recentes dos seus endpoints de integração.

Para liberar o cache do estágio de APIs, é possível escolher o botão **Flush entire cache (Liberar todo o cache)** na seção **Cache Settings (Configurações de cache)** na guia **Settings (Configurações)** em um editor de estágio do console do API Gateway. A operação de descarga do cache demora alguns minutos, depois dos quais o status de cache será `AVAILABLE` imediatamente após a liberação.

**nota**

Após o cache ser enviado, as respostas serão atendidas no endpoint de integração até que o cache seja criado novamente. Durante esse período, o número de solicitações enviadas ao endpoint de integração poderá aumentar. Isso pode aumentar temporariamente a latência geral da sua API.

## Invalidar uma entrada de cache do API Gateway

Um cliente da sua API pode invalidar uma entrada de cache existente e recarregá-la no endpoint de integração para solicitações individuais. O cliente deve enviar uma solicitação que contenha o cabeçalho `Cache-Control: max-age=0`. O cliente recebe a resposta diretamente do endpoint de integração em vez do cache, desde que o cliente esteja autorizado a fazer isso. Isso substitui a entrada de cache existente pela nova resposta, que é obtida do endpoint de integração.

Para conceder permissão a um cliente, anexe uma política com o formato a seguir a uma função de execução do IAM para o usuário.

**nota**

Não há suporte à invalidação de cache entre contas.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "execute-api:InvalidateCache"
      ],
      "Resource": [
        "arn:aws:execute-api:region:account-id:api-id/stage-name/GET/resource-path-specifier"
      ]
    }
  ]
}            
        
```

Essa política permite que o serviço de execução do API Gateway invalide o cache para solicitações referentes aos recursos especificados. Para especificar um grupo de recursos direcionados, use um caractere curinga (*) para `account-id`, `api-id` e outras entradas no valor de ARN de `Resource`. Para obter mais informações sobre como definir permissões para o serviço de execução do API Gateway, consulte [Controlar o acesso a uma API com permissões do IAM](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/permissions.html).

Se você não impuser uma política `InvalidateCache` (ou marcar a caixa de seleção **Require authorization (Exigir autorização)** no console), qualquer cliente poderá invalidar o cache da API. Se a maioria ou todos os clientes invalidarem o cache de API, isso poderá aumentar significativamente a latência da sua API.

Quando a política está em vigor, o armazenamento em cache está habilitado e a autorização é necessária. É possível controlar como as solicitações não autorizadas são tratadas escolhendo uma opção em **Handle unauthorized requests (Lidar com solicitações não autorizadas)** no console do API Gateway.



![                 Configurar a invalidação do cache             ](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/apig-cache-invalidation.png)

As três opções resultam nos seguintes comportamentos:

- **Fail the request with 403 status code (Falha na solicitação com código de status 403)**: retorna uma resposta não autorizada 403.

  Para definir essa opção usando a API, use `FAIL_WITH_403`.

- **Ignore cache control header; Add a warning in response header (Ignorar o cabeçalho de controle de cache; adicionar um aviso no cabeçalho de resposta)**: processa a solicitação e inclui um cabeçalho de aviso na resposta.

  Para definir essa opção usando a API, use `SUCCEED_WITH_RESPONSE_HEADER`.

- **Ignore cache control header (Ignorar o cabeçalho de controle do cache)**: processa a solicitação e não inclui um cabeçalho de aviso na resposta.

  Para definir essa opção usando a API, use `SUCCEED_WITHOUT_RESPONSE_HEADER`.





**Esta página foi útil?**

Sim

Não

[Fornecer feedback](https://docs.aws.amazon.com/forms/aws-doc-feedback?hidden_service_name=API Gateway&topic_url=http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/api-gateway-caching.html)

**Tópico anterior:** [Otimizar](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/rest-api-optimize.html)

**Próximo tópico:** [Codificação de conteúdo](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/api-gateway-gzip-compression-decompression.html)

**Precisa de ajuda?**

- [Experimente os fóruns ](http://forums.aws.amazon.com/forum.jspa?forumID=199)
- [Entre em contato com um especialista do AWS IQ ](https://iq.aws.amazon.com/get-started/?utm=docs&service=Amazon API Gateway)