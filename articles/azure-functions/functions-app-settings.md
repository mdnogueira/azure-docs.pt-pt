---
title: "Referência de definições de aplicação para as funções do Azure"
description: "Documentação de referência para as definições de aplicação de funções do Azure ou variáveis de ambiente."
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/26/2017
ms.author: tdykstra
ms.openlocfilehash: ce7bf2cf650b0df7e8998766b2d3f5a37c4a1b72
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="app-settings-reference-for-azure-functions"></a>Referência de definições de aplicação para as funções do Azure

As definições de aplicação na aplicação de função contém opções de configuração globais que afetam todas as funções para essa aplicação de função. Ao executar localmente, estas definições são as variáveis de ambiente. Este artigo apresenta as definições da aplicação que estão disponíveis em aplicações de função.

Existem outras opções de configuração globais no [host.json](functions-host-json.md) ficheiros e no [local.settings.json](functions-run-local.md#local-settings-file) ficheiro.

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

A chave de instrumentação Application Insights, se estiver a utilizar o Application Insights. Consulte [monitorizar as funções do Azure](functions-monitoring.md).

|Chave|Valor da amostra|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Cadeia de ligação de conta de armazenamento opcional para armazenar os registos e apresenta-os no **Monitor** separador no portal. A conta de armazenamento tem de ser um para fins gerais que suporta tabelas, filas e blobs. Consulte [conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e [requisitos de conta de armazenamento](functions-create-function-app-portal.md#storage-account-requirements).

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [a chave]|

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true`significa que desative a predefinição de página que é apresentada para o URL de raiz de uma aplicação de função de destino. Predefinição é `false`.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDisableHomepage|VERDADEIRO|

Quando esta definição de aplicação for omitida ou definida para `false`, é apresentada uma página semelhante ao seguinte exemplo em resposta ao URL `<functionappname>.azurewebsites.net`.

![Página de destino de aplicação de função](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`significa utiliza o modo de libertação ao compilar o código de .NET; `false` significa utiliza o modo de depuração. Predefinição é `true`.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|VERDADEIRO|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Uma lista delimitada por vírgulas das funcionalidades de beta para ativar. Funcionalidades de beta ativadas por estes sinalizadores não são pronta de produção, mas podem ser ativadas para utilização experimental antes de poderem aceder em direto.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsFeatureFlags|feature1, feature2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

O caminho para o diretório de raiz onde o *host.json* função de ficheiros e pastas estão localizadas. Na aplicação de função, a predefinição é `%HOME%\site\wwwroot`.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsScriptRoot|%Home%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Especifica o repositório ou o fornecedor a utilizar para armazenamento de chaves. Atualmente, os repositórios do suportados são blob ("Blob") e o sistema de ficheiros ("desativado"). A predefinição é o sistema de ficheiros ("desativado").

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsSecretStorageType|desativado|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

O tempo de execução das funções do Azure utiliza esta cadeia de ligação da conta de armazenamento para todas as funções, exceto para funções HTTP acionado. A conta de armazenamento tem de ser um para fins gerais que suporta tabelas, filas e blobs. Consulte [conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e [requisitos de conta de armazenamento](functions-create-function-app-portal.md#storage-account-requirements).

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [a chave]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Caminho para o compilador utilizado para TypeScript. Permite-lhe ignorar a predefinição se for necessário.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="functionappeditmode"></a>FUNÇÃO\_APLICAÇÃO\_EDITAR\_MODO

Os valores válidos são "readwrite" e "readonly".

|Chave|Valor da amostra|
|---|------------|
|FUNÇÃO\_APLICAÇÃO\_EDITAR\_MODO|só de leitura|

## <a name="functionsextensionversion"></a>AS FUNÇÕES\_EXTENSÃO\_VERSÃO

A versão do tempo de execução das funções do Azure para utilizar esta aplicação de função. Um til com a versão principal significa utilizar a versão mais recente do que a versão principal (por exemplo, "~ 1"). Quando estiverem disponíveis novas versões para a versão principal, são automaticamente instaladas na aplicação de função. Para afixar a aplicação para uma versão específica, utilize o número de versão completa (por exemplo, "1.0.12345"). A predefinição é "~ 1".

|Chave|Valor da amostra|
|---|------------|
|AS FUNÇÕES\_EXTENSÃO\_VERSÃO|~1|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Para apenas planos de consumo. Cadeia de ligação para a conta de armazenamento onde o código de aplicação de função e configuração são armazenados. Consulte [criar uma aplicação de função](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [a chave]|

## <a name="websitecontentshare"></a>WEBSITE_CONTENTSHARE

Para apenas planos de consumo. O caminho de ficheiro para o código de aplicação de função e a configuração. Utilizado com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Predefinição é uma cadeia exclusiva que comece com o nome de aplicação de função. Consulte [criar uma aplicação de função](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEB SITE\_MÁXIMO\_DINÂMICA\_APLICAÇÃO\_ESCALA\_ENVIADOS

O número máximo de instâncias que a aplicação de função pode aumentar horizontalmente. Predefinição não é nenhum limite.

> [!NOTE]
> Esta definição é para uma funcionalidade de pré-visualização.

|Chave|Valor da amostra|
|---|------------|
|WEB SITE\_MÁXIMO\_DINÂMICA\_APLICAÇÃO\_ESCALA\_ENVIADOS|10|

## <a name="websitenodedefaultversion"></a>WEB SITE\_NÓ\_DEFAULT_VERSION

Predefinição é "6.5.0".

|Chave|Valor da amostra|
|---|------------|
|WEB SITE\_NÓ\_DEFAULT_VERSION|6.5.0|

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como atualizar as definições de aplicação](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

> [!div class="nextstepaction"]
> [Consulte as definições globais no ficheiro host.json](functions-host-json.md)
