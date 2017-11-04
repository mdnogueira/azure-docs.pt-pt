---
title: "Códigos de erro do Azure API de REST do Machine Learning | Microsoft Docs"
description: "Estes códigos de erro foi devolvidos por uma operação num serviço web Azure Machine Learning."
keywords: 
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/16/2016
ms.author: garye
ms.openlocfilehash: 5cf7d5bb878f323e4e3559822dc745359e43608e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="machine-learning-rest-api-error-codes"></a>Os códigos de erro de API de REST do Machine Learning
 
Os seguintes códigos de erro foi devolvidos por uma operação num serviço web Azure Machine Learning.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (código de estado HTTP 400)
 
Foi fornecido um argumento inválido.
 
Esta classe de erros significa um argumento algures fornecido era inválido. Isto pode ser uma localização de armazenamento do Azure para algo transmitido para o serviço web ou credenciais. Consulte o campo de "código", erro na secção "Detalhes" para diagnosticar o argumento específico era inválido.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| BadParameterValue | O valor de parâmetro fornecido não satisfaz a regra de parâmetro no parâmetro |
| BadSubscriptionId | A subscrição do Id que é utilizado para pontuação não é a presente no recurso |
| BadVersionCall | O parâmetro de versão inválido foi transmitido durante a chamada de API: {0}. Verifique a página de ajuda da API para passar a versão correta e tente novamente. |
| BatchJobInputsNotSpecified | Os seguintes necessários input(s) não foram especificadas com o pedido: {0}. Certifique-se de todos os dados de entrada é especificado e tente novamente. |
| BatchJobInputsTooManySpecified | O pedido especificado mais de entradas que definido no serviço. Lista de input(s) aceites: {0}. Certifique-se de todos os dados de entrada foi especificado corretamente e tente novamente. |
| BlobNameTooLong | Caminho de armazenamento de Blobs do Azure fornecido para resultados de diagnóstico é demasiado longo: {0}. Encurte o caminho e tente novamente. |
| BlobNotFound | Não é possível aceder a fornecido BLOBs do Azure - {0}.  Mensagem de erro do Azure: {1}. |
| ContainerIsEmpty | Não foi fornecido nenhum nome de contentor de armazenamento do Azure. Forneça um nome de contentor válido e tente novamente. |
| ContainerSegmentInvalid | Nome do contentor inválido. Forneça um nome de contentor válido e tente novamente. |
| ContainerValidationFailed | Falhou com o erro de validação de contentor do blob: {0}. |
| DataTypeNotSupported | Foi fornecido um tipo de dados não suportado. Forneça os tipos de dados válido e tente novamente. |
| DuplicateInputInBatchCall | O pedido de batch é inválido. Não é possível especificar a entrada única e múltiplos ao mesmo tempo. Remova um dos itens do pedido e tente novamente. |
| ExpiryTimeInThePast | Hora de expiração fornecidas está no passado: {0}. Forneça uma hora de expiração futura em UTC e tente novamente. Para nunca expirar, definir a hora de expiração para um valor nulo. |
| IncompleteSettings | As definições de diagnóstico estão incompletas. |
| InputBlobRelativeLocationInvalid | Nenhum nome de blob storage do Azure fornecida. Forneça um nome de blob válido e tente novamente. |
| InvalidBlob | Especificação de blob inválido para o blob: {0}. Certifique-se essa cadeia de ligação / caminho relativo ou especificação de token SAS está correta e tente novamente. |
| InvalidBlobConnectionString | A cadeia de ligação especificada para um dos blobs entrada/saída inválido: {0}. Corrija este e tente novamente. |
| InvalidBlobExtension | A referência de blob: {0} tem uma extensão de ficheiro inválido ou em falta. Suportado extensões de ficheiro para este tipo de saída são: "{1}". |
| InvalidInputNames | Nomes especificado no pedido de entrada de serviço inválida: {0}. . Mapear os dados de entrada para as entradas de serviço correto e tente novamente. |
| InvalidOutputOverrideName | Nome de substituição de uma saída inválida: {0}. O serviço não tem um nó de saída com este nome. . Transmita um nome de nó de saída correto para substituir (aplica sensibilidade às maiúsculas e minúsculas). |
| InvalidQueryParameter | Parâmetro de consulta inválida '{0}'. {1} |
| MissingInputBlobInformation | Informações do blob storage do Azure em falta. Forneça uma cadeia de ligação válida e o caminho relativo ou um URI e tente novamente. |
| MissingJobId | Não existem tarefas Id fornecido. Uma tarefa de Id é devolvido quando foi submetida uma tarefa pela primeira vez. Certifique-se de que o Id da tarefa está correto e tente novamente. |
| MissingKeys | Não existem chaves fornecido ou do site primário ou de chave secundária não for fornecido. |
| MissingModelPackage | Nenhum Id do pacote de modelo ou o pacote de modelo fornecidos. Forneça um Id de pacote do modelo válido ou pacote do modelo e tente novamente. |
| MissingOutputOverrideSpecification | O pedido está em falta a especificação de BLOBs para substituição de saída 0}. Especifique uma localização de blob válido com o pedido ou remova a especificação de saída, não se pretender nenhuma substituição da localização. |
| MissingRequestInput | O serviço web espera uma entrada, mas não foi fornecida nenhuma entrada. Certifique-se entradas válidas são fornecidas com base nas portas de entrada publicadas no modelo e tente novamente. |
| MissingRequiredGlobalParameters | Nem todos os parâmetros de serviço de web fornecidos necessários. Verifique se o parâmetro esperado para os módulos está corretos e tente novamente. |
| MissingRequiredOutputOverrides | Ao chamar um ponto final de serviço encriptada é obrigatório para passar na saída substituições para saídas do serviço. Falta substituições neste momento para estes saídas: {0} |
| MissingWebServiceGroupId | Nenhum grupo de serviço web Id fornecido. Forneça um Id de grupo serviço web válido e tente novamente. |
| MissingWebServiceId | Nenhum serviço web Id fornecido. Forneça um Id de serviço web válido e tente novamente. |
| MissingWebServicePackage | Nenhum pacote de serviço web fornecido. Forneça um pacote de serviço web válido e tente novamente. |
| MissingWorkspaceId | Não existem área de trabalho Id fornecido. Forneça uma Id de área de trabalho válida e tente novamente. |
| ModelConfigurationInvalid | Configuração de modelo inválido no pacote do modelo. Certifique-se de que a configuração de modelo contém uma definição de pontos finais de saída, ponto final de erro std, e std o ponto final e tente novamente. |
| ModelPackageIdInvalid | Pacote de modelo inválido ID. Certifique-se de que o Id do pacote do modelo está correto e tente novamente. |
| RequestBodyInvalid | Nenhum corpo do pedido fornecido ou erro ao anular serialização do corpo do pedido. |
| RequestIsEmpty | Nenhum pedido fornecidas. Forneça um pedido válido e tente novamente. |
| UnexpectedParameter | Inesperado parâmetros fornecidos. Certifique-se de que todos os nomes de parâmetro estão escritos corretamente, apenas esperados parâmetros são transmitidos e tente novamente. |
| UnknownError | Erro desconhecido. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Não é possível alterar os requisitos de pedidos simultâneos de {0} do serviço web. |
| WebServiceIdInvalid | Id do serviço web inválido fornecido. Id do serviço Web deve ser um guid válido. |
| WebServiceTooManyConcurrentRequestRequirement | Não é possível definir o requisito de pedido simultâneo em mais do que {0}. |
| WebServiceTypeInvalid | Tipo de serviço web inválido fornecido. Certifique-se de que o tipo de serviço web válido está correto e tente novamente. Tipos de serviço web válido: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (código de estado HTTP 400)
 
Argumento de um utilizador inválido fornecido.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| InputMismatchError | Dados de entrada não corresponde ao esquema de porta de entrada. |
| InputParseError | Análise de vetor entrada falhou.  Certifique-se de que o vetor de entrada tem o número correto de colunas e tipos de dados.  Detalhes adicionais: {0}. |
| MissingRequiredGlobalParameters | Parâmetro (s) esperado pelo serviço web está em falta. Certifique-se de que todos os parâmetros necessários esperados pelo serviço web estão corretos e tente novamente. |
| UnexpectedParameter | Certifique-se de que apenas os parâmetros necessários esperados pelo serviço web são passados e tente novamente. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (código de estado HTTP 400)
 
O pedido é inválido no contexto atual.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| CannotStartJob | Não é possível iniciar a tarefa porque se encontra no estado {0}. |
| IncompatibleModel | O modelo é incompatível com a versão do pedido. A versão de pedido só suporta modelos de saída do datatable único. |
| MultipleInputsNotAllowed | O modelo não permite várias entradas. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (código de estado HTTP 400)
 
Execução do módulo encontrou um erro de biblioteca interna.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (código de estado HTTP 400)
 
A execução do módulo encontrou um erro.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (código de estado HTTP 400)
 
Pacote de serviço web inválido. Certifique-se de que o pacote de serviço web fornecido está correto e tente novamente.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| FormatError | O pacote de serviço web tem um formato incorreto. Detalhes: {0} |
| RuntimesError | O gráfico de pacote de serviço web é inválido. Detalhes: {0} |
| ValidationError | O gráfico de pacote de serviço web é inválido. Detalhes: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Não autorizado (código de estado HTTP 401)
 
Pedido não está autorizado a recursos de acesso.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| AdminRequestUnauthorized | Não autorizado |
| ManagementRequestUnauthorized | Não autorizado |
| ScoreRequestUnauthorized | Foram fornecidas credenciais inválidas. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (código de estado HTTP 404)
 
Recurso não encontrado.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| ModelPackageNotFound | Pacote do modelo não foi encontrado. Certifique-se de que o Id do pacote do modelo está correto e tente novamente. |
| WebServiceIdNotFoundInWorkspace | Serviço Web nesta área de trabalho não foi encontrado. Não há um erro de correspondência entre o webServiceId e o workspaceId. Certifique-se de que o serviço web fornecido faz parte da área de trabalho e tente novamente. |
| WebServiceNotFound | Serviço Web não foi encontrado. Certifique-se de que o Id de serviço web está correto e tente novamente. |
| WorkspaceNotFound | Área de trabalho não foi encontrada. Certifique-se de que a área de trabalho Id está correta e tente novamente. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (código de estado HTTP 408)
 
Não foi possível concluir a operação no tempo permitido.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| RequestCanceled | Pedido foi cancelado pelo cliente. |
| ScoreRequestTimeout | Pedido de execução excedeu o tempo limite. |
 
## <a name="conflict-http-status-code-409"></a>Conflito (código de estado HTTP 409)
 
Já existe um recurso.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Nome do parâmetro de saída inválida. Tente utilizar o módulo do editor de metadados para mudar o nome de colunas e tente novamente. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (código de estado HTTP 413)
 
O modelo tinha excedeu a quota de memória atribuída.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| OutOfMemoryLimit | O modelo consumido mais memória do que foi appropriated para o mesmo. Memória máxima permitida para o modelo é {0} MB. Verifique o seu modelo para problemas. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (código de estado HTTP 500)
 
A execução encontrou um erro interno.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | O processo de contentor falhado com o erro de sistema |
| ContainerProcessTerminatedWithUnknownError | O processo de contentor falhado com um erro desconhecido |
| ContainerValidationFailed | Falhou com o erro de validação de contentor do blob: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Não existem argumentos fornecidos. Certifique-se de que os argumentos válidos são passados e tente novamente. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Id da porta = {0} tem um tipo de dados não suportado: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Falha na geração do swagger, detalhes: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Código de estado de tarefa desconhecido {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, detalhes: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (código de estado HTTP 500)
 
A execução encontrou um erro interno. Sistema de memória insuficiente. Tente novamente.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (código de estado HTTP 500)
 
Pacote do modelo inválido. Certifique-se de que o pacote do modelo fornecido está correto e tente novamente.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (código de estado HTTP 500)
 
Pacote de serviço web inválido. Certifique-se de que o pacote do web fornecido está correto e tente novamente.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| ModuleError | O gráfico de pacote de serviço web é inválido. Detalhes: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (código de estado HTTP 503)
 
O pedido não é possível executar como os contentores estão a ser inicializados.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (código de estado HTTP 503)
 
Serviço está temporariamente indisponível.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| NoMoreResources | Não existem recursos disponíveis para o pedido. |
| RequestThrottled | Pedido foi limitado para o ponto final de {0}. A simultaneidade máxima para o ponto final é {1}. |
| TooManyConcurrentRequests | Demasiados pedidos simultâneos enviados. |
| TooManyHostsBeingInitialized | Demasiados anfitriões que está a ser inicializados em simultâneo. Considere limitação / repetir a operação. |
| TooManyHostsBeingInitializedPerModel | Demasiados anfitriões que está a ser inicializados em simultâneo. Considere limitação / repetir a operação. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (código de estado HTTP 504)
 
Não foi possível concluir a operação no tempo permitido.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| BackendInitializationTimeout | Não foi possível concluir a inicialização do serviço web no tempo permitido. |
| BackendScoreTimeout | Não foi possível concluir a execução do pedido de serviço web no tempo permitido. |
 
