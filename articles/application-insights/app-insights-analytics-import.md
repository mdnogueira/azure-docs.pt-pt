---
title: "Importar os seus dados para análise no Azure Application Insights | Microsoft Docs"
description: "Importar dados estáticos para associar a telemetria da aplicação ou importar um fluxo de dados separada para consulta de análise."
services: application-insights
keywords: "Abra o esquema, a importação de dados"
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: mbullwin
ms.openlocfilehash: f124748434be1b8f0c4704fe6ffba70414c47916
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="import-data-into-analytics"></a>Importar dados para análise

Importar dados de tabela para [análise](app-insights-analytics.md), a respetiva com associação [Application Insights](app-insights-overview.md) telemetria da sua aplicação, ou pode analisá-lo como uma sequência separada. A análise é uma linguagem de consulta poderosa ideais para analisar fluxos timestamped de elevado volume de telemetria.

Pode importar dados para análise ao utilizar o seu próprio esquema. Não tem de utilizar os esquemas de Application Insights padrão, tais como a pedido ou rastreio.

Pode importar JSON ou DSV (delimitador de valores separados por - ponto e vírgula, vírgula ou por separador) ficheiros.

Existem três situações em que importar para a análise é útil:

* **Associe-se com a telemetria da aplicação.** Por exemplo, pode importar uma tabela que mapeia URLs a partir do seu site para títulos de página mais legíveis. Análise, pode criar um relatório de gráfico do dashboard que apresenta as páginas mais populares dez no seu Web site. Agora, pode mostrar os títulos de página em vez dos URLs.
* **Correlacionar a telemetria da sua aplicação** com outras origens, como o tráfego de rede, os dados do servidor ou CDN ficheiros de registo.
* **Aplicam-se a análise para um fluxo de dados separada.** Application Insights Analytics é uma ferramenta poderosa, o que funciona bem com dispersa, timestamped fluxos - muito melhores do que o SQL Server em muitos casos. Se tiver essa um fluxo de alguns outra origem, pode analisá-lo com a análise.

Enviar dados para a origem de dados é fácil. 

1. (Uma vez) Defina o esquema dos seus dados numa "origem de dados'.
2. (Periodicamente) Carregar os dados para o armazenamento do Azure e chamar a API REST para notificá-nos que novos dados está a aguardar ingestão. Dentro de alguns minutos, os dados estão disponíveis para consulta na análise.

A frequência do carregamento é definida por si e rápido como pretende que os seus dados fique disponível para consultas. É mais eficiente para carregar dados segmentos maior, mas não superior a 1GB.

> [!NOTE]
> *Obteve inúmeras origens de dados para analisar?* [*Considere a utilização de* logstash *para enviar os dados no Application Insights.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>Antes de começar

É necessário:

1. Um recurso do Application Insights no Microsoft Azure.

 * Se pretender analisar os dados separadamente a partir de qualquer outra telemetria [criar um novo recurso do Application Insights](app-insights-create-new-resource.md).
 * Se estiver a adesão ou comparar os seus dados com a telemetria a partir de uma aplicação que já está definida com o Application Insights, em seguida, pode utilizar o recurso para essa aplicação.
 * Acesso de contribuinte ou proprietário para esse recurso.
 
2. Armazenamento do Azure. Carregar para o armazenamento do Azure e análise obtém os dados a partir daí. 

 * Recomendamos que crie uma conta de armazenamento dedicado para os blobs. Se os blobs são partilhados com outros processos, demora mais para a nossa processos ler os blobs.


## <a name="define-your-schema"></a>Definir o esquema

Antes de importar dados, tem de definir um *origem de dados,* que especifica o esquema dos seus dados.
Pode ter até 50 origens de dados de um único recurso do Application Insights

1. Inicie o Assistente de origem de dados. Utilize o botão "Adicionar nova origem de dados". Em alternativa - clique no botão de definições no canto superior direito e selecione "Origens de dados" no menu pendente.

    ![Adicionar nova origem de dados](./media/app-insights-analytics-import/add-new-data-source.png)

    Forneça um nome para a nova origem de dados.

2. Defina o formato dos ficheiros que irá carregar.

    Pode definir o formato manualmente ou carregar um ficheiro de exemplo.

    Se os dados estão num formato CSV, a primeira linha do exemplo pode ser cabeçalhos de coluna. Pode alterar os nomes de campo no próximo passo.

    O exemplo deve incluir pelo menos 10 linhas ou registos de dados.

    Os nomes de coluna ou o campo devem ter nomes alfanuméricos (sem espaços ou pontuação).

    ![Carregar um ficheiro de exemplo](./media/app-insights-analytics-import/sample-data-file.png)


3. Reveja o esquema que foi obtido o assistente. Se este inferir os tipos de uma amostra, poderá ter de ajustar os tipos de inferido das colunas.

    ![Reveja o esquema inferido](./media/app-insights-analytics-import/data-source-review-schema.png)

 * (Opcional.) Carregue uma definição de esquema. Consulte o formato abaixo.

 * Selecione um carimbo. Todos os dados na análise tem de ter um campo de carimbo. Tem de ter tipo `datetime`, mas não tem de ter o nome 'timestamp'. Se os seus dados têm uma coluna que contém uma data e hora no formato ISO, escolha tal como a coluna de carimbo. Caso contrário, escolha "como dados chegou" e o processo de importação irá adicionar um campo de carimbo.

5. Crie a origem de dados.

### <a name="schema-definition-file-format"></a>Formato de ficheiro de definição de esquema

Em vez de editar o esquema na IU, pode carregar a definição de esquema a partir de um ficheiro. O formato de definição de esquema é o seguinte: 

Formato delimitado 
```
[ 
    {"location": "0", "name": "RequestName", "type": "string"}, 
    {"location": "1", "name": "timestamp", "type": "datetime"}, 
    {"location": "2", "name": "IPAddress", "type": "string"} 
] 
```

Formato JSON 
```
[ 
    {"location": "$.name", "name": "name", "type": "string"}, 
    {"location": "$.alias", "name": "alias", "type": "string"}, 
    {"location": "$.room", "name": "room", "type": "long"} 
]
```
 
Cada coluna é identificada pela localização, o nome e o tipo. 

* Localização – para o formato de ficheiro delimitado é a posição do valor mapeada. Para o formato JSON, é jpath da chave mapeada.
* Nome – o nome apresentado da coluna.
* Tipo de – o tipo de dados dessa coluna.
 
No caso de dados de exemplo foi utilizados e é delimitado formato de ficheiro, a definição de esquema tem de mapear todas as colunas e adicione novas colunas no final. 

JSON permite que um mapeamento parcial dos dados, por isso a definição de esquema no formato JSON não é necessário mapear cada chave que não foi encontrado nos dados de exemplo. Também é possível mapear colunas que não façam parte de dados de exemplo. 

## <a name="import-data"></a>Importar dados

Para importar dados, carregue-o no armazenamento do Azure, criar uma chave de acesso para o mesmo e, em seguida, efetuar uma chamada de REST API.

![Adicionar nova origem de dados](./media/app-insights-analytics-import/analytics-upload-process.png)

Pode efetuar o seguinte processo manualmente ou configurar um sistema automatizado para fazê-lo em intervalos regulares. Tem de seguir estes passos para cada bloco de dados que pretende importar.

1. Carregar os dados para [blob storage do Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). 

 * Os BLOBs podem ser qualquer um tamanho até 1GB descomprimido. Os blobs grande de centenas de MB são ideais de uma perspetiva de desempenho.
 * Pode comprimi-los com Gzip para melhorar o tempo de carregamento e latência de dados estar disponível para consulta. Utilize o `.gz` extensão de nome de ficheiro.
 * É melhor utilizar uma conta de armazenamento separada para esta finalidade, para evitar chamadas provenientes de diferentes serviços abrandamento desempenho.
 * Quando o envio de dados de alta frequência, cada alguns segundos, recomenda-se para utilizar mais do que uma conta de armazenamento, por motivos de desempenho.

 
2. [Criar uma chave de assinatura de acesso partilhado do blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). A chave deve ter um período de expiração de um dia e fornecer acesso de leitura.
3. Efetuar uma chamada REST para notificar o Application Insights que está a aguardar a dados.

 * Ponto final:`https://dc.services.visualstudio.com/v2/track`
 * Método HTTP: POST
 * Payload:

```JSON

    {
       "data": {
            "baseType":"OpenSchemaData",
            "baseData":{
               "ver":"2",
               "blobSasUri":"<Blob URI with Shared Access Key>",
               "sourceName":"<Schema ID>",
               "sourceVersion":"1.0"
             }
       },
       "ver":1,
       "name":"Microsoft.ApplicationInsights.OpenSchema",
       "time":"<DateTime>",
       "iKey":"<instrumentation key>"
    }
```

Os marcadores de posição são:

* `Blob URI with Shared Access Key`: Pode obter esta do procedimento para criar uma chave. É específico para o blob.
* `Schema ID`: O ID de esquema gerado para o esquema definido. Os dados no blob deste devem estar em conformidade com o esquema.
* `DateTime`: A hora em que o pedido ser submetido, UTC. Podemos aceitar estes formatos: ISO8601 (como "2016-01-01 13:45:01"); Rfc822 ("Qua, 14 16 de Dec 14:57:01 + 0000"); RFC850 ("Quarta-feira, 14-Dec-16 UTC de 57:14:00"); RFC1123 ("Qua, 14 de Dec de 2016 57:14:00 + 0000").
* `Instrumentation key`de recurso do Application Insights.

Os dados estão disponíveis no Analytics após alguns minutos.

## <a name="error-responses"></a>Respostas de erro

* **pedido incorreto 400**: indica que o payload de pedido é inválido. Verifique:
 * Chave de instrumentação correto.
 * Valor de hora válido. Deve ser o tempo, agora em UTC.
 * Está em conformidade com o esquema JSON do evento.
* **403 Proibido**: O blob que enviou não está acessível. Certifique-se de que a chave de acesso partilhado é válida e não expirou.
* **404 não encontrado**:
 * O blob não existe.
 * O sourceId está errada.

Estão disponíveis na mensagem de erro de resposta informações mais detalhadas.


## <a name="sample-code"></a>Código de exemplo

Este código utiliza o [newtonsoft](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) pacote NuGet.

### <a name="classes"></a>Classes

```C#
namespace IngestionClient 
{ 
    using System; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceIngestionRequest 
    { 
        #region Members 
        private const string BaseDataRequiredVersion = "2"; 
        private const string RequestName = "Microsoft.ApplicationInsights.OpenSchema"; 
        #endregion Members 

        public AnalyticsDataSourceIngestionRequest(string ikey, string schemaId, string blobSasUri, int version = 1) 
        { 
            Ver = version; 
            IKey = ikey; 
            Data = new Data 
            { 
                BaseData = new BaseData 
                { 
                    Ver = BaseDataRequiredVersion, 
                    BlobSasUri = blobSasUri, 
                    SourceName = schemaId, 
                    SourceVersion = version.ToString() 
                } 
            }; 
        } 


        [JsonProperty("data")] 
        public Data Data { get; set; } 

        [JsonProperty("ver")] 
        public int Ver { get; set; } 

        [JsonProperty("name")] 
        public string Name { get { return RequestName; } } 

        [JsonProperty("time")] 
        public DateTime Time { get { return DateTime.UtcNow; } } 

        [JsonProperty("iKey")] 
        public string IKey { get; set; } 
    } 

    #region Internal Classes 

    public class Data 
    { 
        private const string DataBaseType = "OpenSchemaData"; 

        [JsonProperty("baseType")] 
        public string BaseType 
        { 
            get { return DataBaseType; } 
        } 

        [JsonProperty("baseData")] 
        public BaseData BaseData { get; set; } 
    } 


    public class BaseData 
    { 
        [JsonProperty("ver")] 
        public string Ver { get; set; } 

        [JsonProperty("blobSasUri")] 
        public string BlobSasUri { get; set; } 

        [JsonProperty("sourceName")] 
        public string SourceName { get; set; } 

        [JsonProperty("sourceVersion")] 
        public string SourceVersion { get; set; } 
    } 

    #endregion Internal Classes 
} 


namespace IngestionClient 
{ 
    using System; 
    using System.IO; 
    using System.Net; 
    using System.Text; 
    using System.Threading.Tasks; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceClient 
    { 
        #region Members 
        private readonly Uri endpoint = new Uri("https://dc.services.visualstudio.com/v2/track"); 
        private const string RequestContentType = "application/json; charset=UTF-8"; 
        private const string RequestAccess = "application/json"; 
        #endregion Members 

        #region Public 

        public async Task<bool> RequestBlobIngestion(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            HttpWebRequest request = WebRequest.CreateHttp(endpoint); 
            request.Method = WebRequestMethods.Http.Post; 
            request.ContentType = RequestContentType; 
            request.Accept = RequestAccess; 

            string notificationJson = Serialize(ingestionRequest); 
            byte[] notificationBytes = GetContentBytes(notificationJson); 
            request.ContentLength = notificationBytes.Length; 

            Stream requestStream = request.GetRequestStream(); 
            requestStream.Write(notificationBytes, 0, notificationBytes.Length); 
            requestStream.Close(); 

            try 
            { 
                using (var response = (HttpWebResponse)await request.GetResponseAsync())
                {
                    return response.StatusCode == HttpStatusCode.OK;
                }
            } 
            catch (WebException e) 
            { 
                HttpWebResponse httpResponse = e.Response as HttpWebResponse; 
                if (httpResponse != null) 
                { 
                    Console.WriteLine( 
                        "Ingestion request failed with status code: {0}. Error: {1}", 
                        httpResponse.StatusCode, 
                        httpResponse.StatusDescription); 
                    return false; 
                }
                throw; 
            } 
        } 
        #endregion Public 

        #region Private 
        private byte[] GetContentBytes(string content) 
        { 
            return Encoding.UTF8.GetBytes(content);
        } 


        private string Serialize(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            return JsonConvert.SerializeObject(ingestionRequest); 
        } 
        #endregion Private 
    } 
} 
```

### <a name="ingest-data"></a>Ingerir dados

Utilize este código para cada blob. 

```C#
   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);
```

## <a name="next-steps"></a>Passos seguintes

* [Apresentação do idioma de consulta de análise de registos](app-insights-analytics-tour.md)
* Se estiver a utilizar Logstash, utilize o [Plug-in do Logstash para enviar dados para o Application Insights](https://github.com/Microsoft/logstash-output-application-insights)
