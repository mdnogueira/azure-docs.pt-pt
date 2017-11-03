---
title: "Alteração feed recursos HL7 FHIR - base de dados do Azure Cosmos | Microsoft Docs"
description: "Saiba como configurar as notificações de alteração para HL7 FHIR patient saúde os registos com Azure Logic Apps, a base de dados do Azure Cosmos e o Service Bus."
keywords: HL7 fhir
services: cosmos-db
author: hedidin
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 0d25c11f-9197-419a-aa19-4614c6ab2d06
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: b-hoedid
ms.openlocfilehash: d2b50c0b6864af41fb9cfa051721c432772b228d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Notificar patients das alterações de registo de saúde HL7 FHIR com Logic Apps e a base de dados do Azure Cosmos

Azure Edidin de Howard MVP recentemente foi contactado por uma organização de cuidados de saúde que pretendia adicionar novas funcionalidades para os respetivos patient portal. São necessárias para enviar notificações para patients quando o respetivo registo de estado de funcionamento foi atualizado e necessárias patients para poder subscrever estas atualizações. 

Este artigo explica a alteração de feed de solução de notificação criada para esta organização de cuidados de saúde utilizando a BD do Cosmos do Azure, as Logic Apps e o Service Bus. 

## <a name="project-requirements"></a>Requisitos do projeto
- Fornecedores de enviam a arquitetura de documento consolidado Clinical HL7 (C-CDA) documentos no formato XML. Documentos C CDA abranger praticamente cada tipo de documento clinical, incluindo documentos clinical como histories famílias e registos de immunization, bem como administrativos, fluxo de trabalho e os documentos financeiros. 
- Documentos C CDA são convertidos em [HL7 FHIR recursos](http://hl7.org/fhir/2017Jan/resourcelist.html) no formato JSON.
- Documentos de recurso FHIR modificados são enviados por correio eletrónico no formato JSON.

## <a name="solution-workflow"></a>Fluxo de trabalho da solução 

Um nível elevado, o projeto necessários os seguintes passos de fluxo de trabalho: 
1. Converta os documentos de C CDA recursos FHIR.
2. Execute o acionador recorrente de consulta para obter recursos FHIR modificados. 
2. Chame uma aplicação personalizada, FhirNotificationApi, para ligar à base de dados do Azure Cosmos e a consulta para documentos novos ou modificados.
3. Guarde a resposta para o barramento de serviço fila.
4. Consulta para novas mensagens na fila de barramento de serviço.
5. Envie notificações por e-mail para patients.

## <a name="solution-architecture"></a>Arquitetura de solução
Esta solução requer três Logic Apps cumprir os requisitos acima e concluir o fluxo de trabalho da solução. As três as logic apps são:
1. **Aplicação de mapeamento de FHIR HL7**: recebe o documento HL7 C-CDA, transforma-a para o recurso de FHIR e guarda-o no Azure Cosmos DB.
2. **Aplicação EHR**: consulta o repositório do Azure Cosmos DB FHIR e guarda a resposta a uma fila do Service Bus. Esta aplicação lógica utiliza um [aplicação API](#api-app) obter documentos novos e alterados.
3. **Aplicação de notificação de processo**: envia uma notificação por e-mail com os documentos de recurso FHIR no corpo.

![Logic Apps três utilizada nesta solução de cuidados de saúde HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>Serviços do Azure utilizados na solução

#### <a name="azure-cosmos-db-documentdb-api"></a>DocumentDB do Azure Cosmos DB API
BD do Azure do Cosmos consiste no repositório para os recursos FHIR conforme mostrado na figura seguinte.

![A conta de base de dados do Azure Cosmos utilizada neste tutorial de cuidados de saúde HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Aplicações Lógicas
As Logic Apps lidar com o processo de fluxo de trabalho. As capturas de ecrã seguintes mostram as Logic apps criadas para esta solução. 


1. **Aplicação de mapeamento de FHIR HL7**: recebe o documento HL7 C-CDA e transformá-los para um recurso FHIR utilizando o pacote de integração do Enterprise para aplicações lógicas. O pacote de integração do Enterprise processa o mapeamento de C-CDA para recursos FHIR.

    ![A aplicação lógica utilizada para receber os registos de cuidados de saúde HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **Aplicação EHR**: consultar o repositório do Azure Cosmos DB FHIR e guardar a resposta a uma fila do Service Bus. O código da aplicação GetNewOrModifiedFHIRDocuments é abaixo.

    ![A aplicação lógica utilizado para consultar a base de dados do Azure Cosmos](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Aplicação de notificação de processo**: enviar uma notificação por e-mail com os documentos de recurso FHIR no corpo.

    ![A aplicação lógica que envia correio eletrónico patient com o recurso de HL7 FHIR no corpo do](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
A figura seguinte mostra os patients fila. O valor de propriedade da etiqueta é utilizado para o assunto do e-mail.

![A fila de barramento de serviço utilizado neste tutorial HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>Aplicação API
Uma aplicação API estabelece ligação à base de dados do Azure Cosmos e consultas para novos ou modificados documentos FHIR por tipo de recurso. Esta aplicação tem um controlador, **FhirNotificationApi** com uma única operação **GetNewOrModifiedFhirDocuments**, consulte [origem para a aplicação API](#api-app-source).

Estamos a utilizar o [ `CreateDocumentChangeFeedQuery` ](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) classe do DocumentDB .NET API do Azure Cosmos DB. Para obter mais informações, consulte o [alteração feed artigo](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>Operação GetNewOrModifiedFhirDocuments

**Entradas**
- DatabaseId
- CollectionId
- Nome de tipo de recurso de FHIR HL7
- Valor booleano: Iniciar a partir do início
- INT: Número de documentos devolvidos

**Saídas**
- Êxito: Código de estado: resposta 200,: lista de documentos (matriz JSON)
- Falha: Código de estado: respostas 404,: "não existem documentos encontrado para '*nome de recurso '* tipo de recurso"

<a id="api-app-source"></a>

**Origem para a aplicação API**

```C#

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///     or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modifed FHIR Documents By Resource Type " +
                "from Last Run Date or Begining of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modifed Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();  
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>Testar o FhirNotificationApi 

A imagem seguinte demonstra como swagger foi utilizado para testar o [FhirNotificationApi](#api-app-source).

![O ficheiro Swagger utilizado para testar a aplicação API](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Dashboard do portal do Azure

A imagem seguinte mostra todos os serviços do Azure para esta solução em execução no portal do Azure.

![O portal do Azure que mostra todos os serviços utilizados neste tutorial HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Resumo

- Aprendeu a que o Azure Cosmos DB tem suppport nativo para as notificações para novas ou documentos contidos e como é fácil utilizar. 
- Ao tirar partido das Logic Apps, pode criar fluxos de trabalho sem escrever qualquer código.
- Utilizar filas do Service Bus do Azure para processar a distribuição para os documentos HL7 FHIR.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a BD do Cosmos do Azure, consulte o [home page da base de dados do Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/). Para obter mais informaiton sobre Logic Apps, consulte [Logic Apps](https://azure.microsoft.com/services/logic-apps/).


