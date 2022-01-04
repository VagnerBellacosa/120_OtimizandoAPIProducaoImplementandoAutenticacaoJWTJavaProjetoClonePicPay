# Tutorial: Como otimizar a indexação com a API de envio por push

O Azure Cognitive Search é compatível com [duas abordagens básicas](https://docs.microsoft.com/pt-br/azure/search/search-what-is-data-import) para importar dados para um índice de pesquisa: *enviar por push* os dados para o índice de maneira programática ou apontar um [indexador do Azure Cognitive Search](https://docs.microsoft.com/pt-br/azure/search/search-indexer-overview) em uma fonte de dados compatível para efetuar o *pull* nos dados.

Este tutorial descreve como indexar dados com eficiência usando o [modelo de push](https://docs.microsoft.com/pt-br/azure/search/search-what-is-data-import#pushing-data-to-an-index) por meio de solicitações de envio em lote e usando uma estratégia de repetição de retirada exponencial. Você pode [baixar e executar o aplicativo de exemplo](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing). Este artigo explica os principais aspectos do aplicativo e os fatores a serem considerados ao indexar dados.

Este tutorial usa o C# e o [SDK do .NET](https://docs.microsoft.com/pt-br/dotnet/api/overview/azure/search) para executar as seguintes tarefas:

- Crie um índice
- Testa vários tamanhos de lote para determinar o tamanho mais eficiente
- Indexar lotes de maneira assíncrona
- Usar vários threads para aumentar as velocidades de indexação
- Usar uma estratégia de repetição de retirada exponencial para repetir documentos com falha

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## Pré-requisitos

Os serviços e as ferramentas a seguir são necessários para este tutorial.

- [Visual Studio](https://visualstudio.microsoft.com/downloads/), qualquer edição. O código de exemplo e as instruções foram testados na edição gratuita da Comunidade.
- [Crie um serviço da Pesquisa Cognitiva do Azure](https://docs.microsoft.com/pt-br/azure/search/search-create-service-portal) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual.



## Baixar arquivos

O código-fonte para este tutorial está na pasta [optimize-data-indexing/v11](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing/v11) no repositório do GitHub [Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## Considerações-chave

Ao enviar dados por push a um índice, há várias considerações importantes que afetam as velocidades de indexação. Você pode saber mais sobre esses fatores no [artigo Indexar conjuntos de dados grandes](https://docs.microsoft.com/pt-br/azure/search/search-howto-large-index).

Seis fatores principais a serem considerados são:

- **A camada de serviço e o número de partições/réplicas** – adicionar partições e aumentar sua camada aumentarão as velocidades de indexação.
- **Esquema de índice** – adicionar campos e adicionar propriedades adicionais a campos (como *searchable*, *facetable* ou *filterable*) reduzem as velocidades de indexação.
- **Tamanho do lote** – o tamanho do lote ideal varia de acordo com o esquema do índice e o conjunto de dados.
- **Número de threads/trabalhadores** – um thread não aproveitará totalmente as velocidades de indexação
- **Estratégia de repetição** – uma estratégia de repetição de retirada exponencial deve ser usada para otimizar a indexação.
- **Velocidades de transferência de dados de rede** – as velocidades de transferência de dados podem ser um fator limitante. Indexe os dados de dentro de seu ambiente do Azure para aumentar as velocidades de transferência de dados.

## 1 – Criar o serviço do Azure Cognitive Search

Para concluir este tutorial, você precisará de um serviço do Azure Cognitive Search, que você pode [criar no portal](https://docs.microsoft.com/pt-br/azure/search/search-create-service-portal). É recomendável usar a mesma camada que você planeja usar na produção para que você possa testar e otimizar com precisão as velocidades de indexação.

### Obter uma URL e uma chave de API de administração para a Pesquisa Cognitiva do Azure

As chamadas à API exigem a URL do serviço e uma chave de acesso. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou a Pesquisa Cognitiva do Azure à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

2. Em **Configurações****Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   ![Get an HTTP endpoint and access key](https://docs.microsoft.com/pt-br/azure/search/media/search-get-started-rest/get-url-key.png)

## 2 – Configurar o ambiente

1. Inicie o Visual Studio e abra **OptimizeDataIndexing.sln**.
2. No Gerenciador de Soluções, abra **appsettings.json** para fornecer informações de conexão.

JSONCopiar

```json
{
  "SearchServiceUri": "https://{service-name}.search.windows.net",
  "SearchServiceAdminApiKey": "",
  "SearchIndexName": "optimize-indexing"
}
```

## 3 – Explorar o código

Depois de atualizar *appsettings.json*, o programa de exemplo em **OptimizeDataIndexing.sln** deverá estar pronto para ser compilado e executado.

Esse código é derivado do [Início rápido do C#](https://docs.microsoft.com/pt-br/azure/search/search-get-started-dotnet). Você pode encontrar informações mais detalhadas sobre as noções básicas de como trabalhar com o SDK do .NET nesse artigo.

Este aplicativo de console C#/.NET simples realiza as seguintes tarefas:

- Cria um índice baseado na estrutura de dados da classe Hotel C# (que também faz referência à classe Address).
- Testa vários tamanhos de lote para determinar o tamanho mais eficiente
- Indexa dados de modo assíncrono
  - Usando vários threads para aumentar as velocidades de indexação
  - Usando uma estratégia de repetição de retirada exponencial para repetir itens com falha

Antes de executar o programa, reserve um minuto para estudar o código e as definições de índice desta amostra. O código relevante está em vários arquivos:

- **Hotel.cs** e **Address.cs** contêm o esquema que define o índice
- **DataGenerator.cs** contém uma classe simples para facilitar a criação de grandes quantidades de dados de Hotel
- **ExponentialBackoff.cs** contém o código para otimizar o processo de indexação conforme descrito abaixo
- **Program.cs** contém funções que criam e excluem o índice do Azure Cognitive Search, indexa lotes de dados e testa diferentes tamanhos de lote

### Como criar o índice

Este programa de exemplo usa o SDK do .NET para definir e criar um índice da Pesquisa Cognitiva do Azure. Ele aproveita a classe `FieldBuilder` para gerar uma estrutura de índice de uma classe de modelo de dados C#.

O modelo de dados é definido pela classe Hotel, que também contém referências à classe Address. O FieldBuilder faz uma busca detalhada em várias definições de classe para gerar uma estrutura de dados complexa para o índice. As marcas de metadados são usadas para definir os atributos de cada campo, como se ele é pesquisável ou classificável.

Os snippets a seguir do arquivo **Hotel.cs** mostram como um único campo e uma referência a outra classe de modelo de dados podem ser especificados.

C#Copiar

```csharp
. . .
[SearchableField(IsSortable = true)]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

No arquivo **Program.cs**, o índice é definido com um nome e uma coleção de campos gerada pelo método e, em seguida, é criado da seguinte maneira:

C#Copiar

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### Como gerar dados

Uma classe simples é implementada no arquivo **DataGenerator.cs** para gerar dados para teste. A única finalidade dessa classe é facilitar a geração de um grande número de documentos com uma ID exclusiva para indexação.

Para obter uma lista de cem mil hotéis com IDs exclusivas, você executaria as seguintes linhas de código:

C#Copiar

```csharp
long numDocuments = 100000;
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(numDocuments, "large");
```

Há dois tamanhos de hotéis disponíveis para teste neste exemplo: **pequenos** e **grandes**.

O esquema do índice pode ter um impacto significativo nas velocidades de indexação. Devido a esse impacto, faz sentido converter essa classe para gerar dados que correspondem ao esquema de índice pretendido após a execução deste tutorial.

## 4 – Tamanhos de lote de teste

O Azure Cognitive Search dá suporte às seguintes APIs para carregar um ou vários documentos em um índice:

- [Adicionar, atualizar ou excluir documentos (API REST)](https://docs.microsoft.com/pt-br/rest/api/searchservice/AddUpdate-or-Delete-Documents)
- [Classe IndexDocumentsAction](https://docs.microsoft.com/pt-br/dotnet/api/azure.search.documents.models.indexdocumentsaction) ou [Classe IndexDocumentsBatch](https://docs.microsoft.com/pt-br/dotnet/api/azure.search.documents.models.indexdocumentsbatch)

A indexação de documentos em lotes aprimorará significativamente o desempenho da indexação. Esses lotes podem ter até mil documentos ou até aproximadamente 16 MB por lote.

Determinar o tamanho de lote ideal para seus dados é um componente-chave de otimização de velocidades de indexação. Os dois fatores principais que influenciam o tamanho de lote ideal são:

- O esquema do índice
- O tamanho dos seus dados

Como o tamanho de lote ideal depende do índice e dos dados, a melhor abordagem é testar diferentes tamanhos de lote para determinar o que resulta nas velocidades de indexação mais rápidas para seu cenário.

A função a seguir demonstra uma abordagem simples para testar tamanhos de lote.

C#Copiar

```csharp
public static async Task TestBatchSizesAsync(SearchClient searchClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocumentsAsync(searchClient, hotels).ConfigureAwait(false);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }

    Console.WriteLine();
}
```

Como nem todos os documentos têm o mesmo tamanho (embora estejam neste exemplo), estimamos o tamanho dos dados que estamos enviando para o serviço de pesquisa. Fazemos isso usando a função abaixo que primeiro converte o objeto em JSON e, em seguida, determina seu tamanho em bytes. Essa técnica nos permite determinar quais tamanhos de lote são mais eficientes em termos de velocidades de indexação de MB/s.

C#Copiar

```csharp
// Returns size of object in MB
public static double EstimateObjectSize(object data)
{
    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    // converting data to json for more accurate sizing
    var json = JsonSerializer.Serialize(data);
    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

A função requer um `SearchClient`, bem como o número de tentativas que você gostaria de testar para cada tamanho de lote. Como pode haver uma variabilidade nas horas de indexação para cada lote, tentamos cada lote três vezes, por padrão, para tornar os resultados mais estatisticamente significativos.

C#Copiar

```csharp
await TestBatchSizesAsync(searchClient, numTries: 3);
```

Ao executar a função, você deverá ver uma saída como abaixo no seu console:

![Output of test batch size function](https://docs.microsoft.com/pt-br/azure/search/media/tutorial-optimize-data-indexing/test-batch-sizes.png)

Identifique qual tamanho de lote é mais eficiente e, em seguida, use esse tamanho de lote na próxima etapa do tutorial. Você pode ver um limite em MB/s em diferentes tamanhos de lote.

## 5 – Dados de índice

Agora que identificamos o tamanho do lote que pretendemos usar, a próxima etapa é começar a indexar os dados. Para indexar dados com eficiência, este exemplo:

- Usa vários threads/trabalhadores.
- Implementa uma estratégia de repetição de retirada exponencial.

### Usa vários threads/trabalhadores

Para aproveitar ao máximo as velocidades de indexação do Azure Cognitive Search, você provavelmente precisará usar vários threads para enviar solicitações de indexação do lote simultaneamente para o serviço.

Várias das principais considerações mencionadas acima afetam o número ideal de threads. Você pode modificar este exemplo e testar com contagens de threads diferentes para determinar a contagem de threads ideal para seu cenário. No entanto, desde que haja vários threads em execução simultânea, você poderá aproveitar a maioria dos ganhos de eficiência.

Conforme você aumenta as solicitações que chegam ao serviço de pesquisa, pode encontrar [códigos de status HTTP](https://docs.microsoft.com/pt-br/rest/api/searchservice/http-status-codes) indicando que a solicitação não foi totalmente concluída com sucesso. Durante a indexação, dois códigos de status HTTP comuns são:

- **503 Serviço Não Disponível** – Esse erro significa que o sistema está sob carga pesada e sua solicitação não pode ser processada no momento.
- **207 Status Múltiplo** – esse erro significa que alguns documentos foram bem-sucedidos, mas pelo menos um deles falhou.

### Implementa uma estratégia de repetição de retirada exponencial

Se ocorrer uma falha, as solicitações deverão ser repetidas usando uma [estratégia de repetição de retirada exponencial](https://docs.microsoft.com/pt-br/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

O SDK do .NET do Azure Cognitive Search tenta automaticamente 503s e outras solicitações com falha, mas você precisará implementar sua lógica para tentar novamente o 207s. Ferramentas de software livre, como [Polly](https://github.com/App-vNext/Polly), também podem ser usadas para implementar uma estratégia de repetição.

Neste exemplo, implementamos nossa própria estratégia de repetição de retirada exponencial. Para implementar essa estratégia, começamos definindo algumas variáveis, incluindo o `maxRetryAttempts` e o `delay` inicial para uma solicitação com falha:

C#Copiar

```csharp
// Create batch of documents for indexing
var batch = IndexDocumentsBatch.Upload(hotels);

// Create an object to hold the result
IndexDocumentsResult result = null;

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

Os resultados da operação de indexação são armazenados na variável `IndexDocumentResult result`. Essa variável é importante porque permite que você verifique se algum documento no lote falhou, conforme mostrado abaixo. Se houver uma falha parcial, um lote será criado com base na ID dos documentos com falha.

As exceções `RequestFailedException` também devem ser detectadas, pois indicam que a solicitação falhou completamente e deve ser repetida.

C#Copiar

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        result = await searchClient.IndexDocumentsAsync(batch).ConfigureAwait(false);

        var failedDocuments = result.Results.Where(r => r.Succeeded != true).ToList();

        // handle partial failure
        if (failedDocuments.Count > 0)
        {
            if (attempts == maxRetryAttempts)
            {
                Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
                break;
            }
            else
            {
                Console.WriteLine("[Batch starting at doc {0} had partial failure]", id);
                Console.WriteLine("[Retrying {0} failed documents] \n", failedDocuments.Count);

                // creating a batch of failed documents to retry
                var failedDocumentKeys = failedDocuments.Select(doc => doc.Key).ToList();
                hotels = hotels.Where(h => failedDocumentKeys.Contains(h.HotelId)).ToList();
                batch = IndexDocumentsBatch.Upload(hotels);

                Task.Delay(delay).Wait();
                delay = delay * 2;
                continue;
            }
        }

        return result;
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("[Batch starting at doc {0} failed]", id);
        Console.WriteLine("[Retrying entire batch] \n");

        if (attempts == maxRetryAttempts)
        {
            Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
            break;
        }

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

Daqui em diante, encapsulamos o código de retirada exponencial em uma função para que ele possa ser chamado facilmente.

Outra função é então criada para gerenciar os threads ativos. Para simplificar, essa função não está incluída aqui, mas pode ser encontrada em [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/v11/OptimizeDataIndexing/ExponentialBackoff.cs). A função pode ser chamada com o seguinte comando quando `hotels` é o dado que queremos carregar, `1000` é o tamanho do lote e `8` é o número de threads concomitantes:

C#Copiar

```csharp
await ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8);
```

Ao executar a função, você deverá ver uma saída como abaixo:

![Output of index data function](https://docs.microsoft.com/pt-br/azure/search/media/tutorial-optimize-data-indexing/index-data-start.png)

Quando um lote de documentos falha, um erro é impresso indicando a falha e o lote está sendo repetido:

Copiar

```
[Batch starting at doc 6000 had partial failure]
[Retrying 560 failed documents]
```

Depois que a função for concluída, você poderá verificar se todos os documentos foram adicionados ao índice.

## 6 – Explorar o índice

Explore o índice de pesquisa preenchido após a execução programática do programa ou usando o [**Gerenciador de pesquisa**](https://docs.microsoft.com/pt-br/azure/search/search-explorer) no portal.

### Programaticamente

Há duas opções principais para verificar o número de documentos em um índice: a [API de Contagem de Documentos](https://docs.microsoft.com/pt-br/rest/api/searchservice/count-documents) e a [API Obter Estatísticas de Índice](https://docs.microsoft.com/pt-br/rest/api/searchservice/get-index-statistics). Ambos os caminhos podem exigir algum tempo adicional para atualizar, portanto, não se assuste se o número de documentos retornados for menor do que o esperado inicialmente.

#### Contar documentos

A operação Contar Documentos recupera uma contagem do número de documentos em um índice de pesquisa:

C#Copiar

```csharp
long indexDocCount = await searchClient.GetDocumentCountAsync();
```

#### Obter estatísticas de índice

A operação Obter Estatísticas do Índice retorna uma contagem de documentos para o índice atual, além do uso do armazenamento. As estatísticas de índice levarão mais tempo do que a contagem de documentos para serem atualizadas.

C#Copiar

```csharp
var indexStats = await indexClient.GetIndexStatisticsAsync(indexName);
```

### Portal do Azure

No portal do Azure, abra a página **Visão Geral** do serviço de pesquisa e localize o índice **optimize-indexing** na lista **Índices**.

![List of Azure Cognitive Search indexes](https://docs.microsoft.com/pt-br/azure/search/media/tutorial-optimize-data-indexing/portal-output.png)

A *Contagem de Documentos* e *Tamanho de Armazenamento* são baseados na [API Obter Estatísticas do Índice](https://docs.microsoft.com/pt-br/rest/api/searchservice/get-index-statistics) e podem levar vários minutos para ser atualizada.

## Redefinir e execute novamente

Nos primeiros estágios experimentais de desenvolvimento, a abordagem mais prática para a iteração de design é excluir os objetos do Azure Cognitive Search e permitir que o código os recompile. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

O código de exemplo deste tutorial verifica se há índices existentes e os exclui, de modo que você possa executar novamente o código.

Você também pode usar o portal para excluir índices.

## Limpar os recursos

Quando você está trabalhando em sua própria assinatura, no final de um projeto, é uma boa ideia remover os recursos que já não são necessários. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

## Próximas etapas

Agora que você está familiarizado com o conceito de ingestão de dados com eficiência, vamos examinar mais de perto a arquitetura de consulta Lucene e como a pesquisa de texto completo funciona no Azure Cognitive Search.

[Como funciona a pesquisa de texto completo no Azure Cognitive Search](https://docs.microsoft.com/pt-br/azure/search/search-lucene-query-architecture)

------

## Conteúdo recomendado

- [Tutorial: criar um analisador personalizado - Azure Cognitive Search](https://docs.microsoft.com/pt-br/azure/search/tutorial-create-custom-analyzer)

  Saiba como criar um analisador personalizado para aprimorar a qualidade dos resultados da pesquisa no Azure Cognitive Search.

- [Tutorial de C# sobre a indexação de várias fontes de dados do Azure - Azure Cognitive Search](https://docs.microsoft.com/pt-br/azure/search/tutorial-multiple-data-sources)

  Saiba como importar dados de várias fontes de dados para um só índice do Azure Cognitive Search usando indexadores. Este tutorial e este código de exemplo estão em C#.

- [Mapeamentos de campo em indexadores - Azure Cognitive Search](https://docs.microsoft.com/pt-br/azure/search/search-indexer-field-mappings)

  Configurar mapeamentos de campo em um indexador para obter as diferenças em nomes de campo e representações de dados.

- [Executar ou reiniciar indexadores - Azure Cognitive Search](https://docs.microsoft.com/pt-br/azure/search/search-howto-run-reset-indexers)

  Execute indexadores por completo ou redefina um indexador, habilidades ou documentos individuais para atualizar todo ou parte de um índice de pesquisa ou do repositório de conhecimento.

Mostrar mais