# Controlar e gerenciar acesso a uma API REST no API Gateway

[PDF](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/apigateway-dg.pdf#apigateway-control-access-to-api)

[RSS](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/amazon-apigateway-release-notes.rss)



O API Gateway oferece suporte a vários mecanismos de controle e gerenciamento de acesso à sua API.

Os mecanismos a seguir podem ser usados para autenticação e autorização:

- As **políticas de recursos** permitem que você crie políticas baseadas em recursos para permitir ou negar acesso a APIs e métodos de endereços IP de origem ou endpoints da VPC especificados. Para obter mais informações, consulte [Controlar o acesso a uma API com políticas de recursos do API Gateway](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/apigateway-resource-policies.html).
- As **funções e políticas padrão do AWS IAM** oferecem controles de acesso flexíveis e robustos que podem ser aplicados a uma API inteira ou a métodos individuais. As funções e políticas do IAM podem ser usadas para controlar quem pode criar, gerenciar e chamar suas APIs. Para obter mais informações, consulte [Controlar o acesso a uma API com permissões do IAM](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/permissions.html).
- As **tags do IAM** podem ser usadas com as políticas do IAM para controlar o acesso. Para obter mais informações, consulte [Usar tags para controlar o acesso aos seus recursos do API Gateway](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/apigateway-tagging-iam-policy.html).
- As **políticas de endpoint para VPC endpoints de interface** permitem anexar políticas de recurso do IAM a endpoints da VPC de interface a fim de melhorar a segurança das [APIs privadas](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-private-apis.html). Para obter mais informações, consulte [Usar políticas de VPC endpoint para APIs privadas no API Gateway](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/apigateway-vpc-endpoint-policies.html).
- **Autorizadores do Lambda** são funções do Lambda que controlam o acesso aos métodos da API REST usando a autenticação de token de portador, bem como informações descritas pelos cabeçalhos, caminhos, strings de consulta, variáveis de estágio ou parâmetros de solicitação de variáveis de contexto. Autorizadores do Lambda são usados para controlar quem pode chamar métodos da API REST. Para obter mais informações, consulte [Usar os autorizadores do API Gateway Lambda](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html).
- Os **grupos de usuários do Amazon Cognito** permitem criar soluções de autenticação e autorização personalizáveis para suas APIs REST. Os grupos de usuários do Amazon Cognito são usados para controlar quem pode invocar métodos da API REST. Para obter mais informações, consulte [Controlar o acesso a uma API REST usando um grupo de usuários do Amazon Cognito como autorizador](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/apigateway-integrate-with-cognito.html).

É possível usar os mecanismos a seguir para executar outras tarefas relacionadas a controle de acesso:

- O **compartilhamento de recursos entre origens (CORS)** permite que você controle como a API REST responde a solicitações de recursos entre domínios. Para obter mais informações, consulte [Habilitar o CORS para um recurso da API REST](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/how-to-cors.html).
- Os **certificados SSL no lado do cliente** podem ser usados para verificar se as solicitações HTTP para seu sistema backend provêm do API Gateway. Para obter mais informações, consulte [Gerar e configurar um certificado SSL para autenticação de backend](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/getting-started-client-side-ssl-authentication.html).
- O **AWS WAF** pode ser usado para proteger sua API do API Gateway contra explorações comuns da Web. Para obter mais informações, consulte [Usar o AWS WAF para proteger as APIs](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/apigateway-control-access-aws-waf.html).

É possível usar os mecanismos a seguir para rastrear e limitar o acesso que você concedeu a clientes autorizados:

- Os **planos de uso** permitem que você forneça **chaves da API** aos clientes e monitore e restrinja o uso dos estágios e métodos da API para cada chave da API. Para obter mais informações, consulte [Criar e usar planos de uso com chaves de API](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/api-gateway-api-usage-plans.html).