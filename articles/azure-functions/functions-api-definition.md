---
title: "Metadados de OpenAPI nas funções do Azure | Microsoft Docs"
description: "Descrição geral do suporte de OpenAPI nas funções do Azure"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: b6aacc536e589a2036aba5a0784a4ba71641a59e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Suporte de metadados de OpenAPI 2.0 nas funções do Azure (pré-visualização)
OpenAPI 2.0 (anteriormente Swagger) suporte de metadados nas funções do Azure é uma funcionalidade de pré-visualização que pode utilizar para escrever uma definição de OpenAPI 2.0 no interior de uma aplicação de função. Em seguida, pode alojar esse ficheiro utilizando a aplicação de função.

[Metadados OpenAPI](http://swagger.io/) permite que uma função que está a alojar uma API de REST a ser consumidos por uma grande variedade de outro software. Este software inclui ofertas da Microsoft, como PowerApps e [funcionalidade de API Apps do App Service do Azure](../app-service/app-service-web-overview.md), ferramentas de Programador de terceiros, como [Postman](https://www.getpostman.com/docs/importing_swagger), e [muitos mais pacotes](http://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Recomendamos que comece com o [tutorial de introdução](./functions-api-definition-getting-started.md) e, em seguida, regressar a este documento para obter mais informações sobre funcionalidades específicas.

## <a name="enable"></a>Ativar o suporte de definição de OpenAPI
Pode configurar todas as definições de OpenAPI no **definição da API** página na sua aplicação de função **funcionalidades da plataforma**.

Para ativar a geração de uma definição de OpenAPI alojada e uma definição de início rápido, defina **origem de definição de API** para **função (pré-visualização)**. **URL externo** permite que a função utilizar uma definição de OpenAPI tem alojado noutro local.

## <a name="generate-definition"></a>Gerar uma estrutura de Swagger de metadados da função
Um modelo pode ajudar a começar a escrever a definição de OpenAPI primeiro. A funcionalidade de modelo de definição cria uma definição de OpenAPI dispersa utilizando todos os metadados no ficheiro function.json para cada uma das suas funções de Acionador HTTP. Terá de preencher para obter mais informações sobre a API do [OpenAPI especificação](http://swagger.io/specification/), tais como modelos de pedido e resposta.

Para obter instruções passo a passo, consulte o [tutorial de introdução](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Modelos disponíveis

|Nome| Descrição |
|:-----|:-----|
|Definição gerada|Uma definição de OpenAPI com a quantidade máxima de informações que podem ser inferidas a partir de metadados da função de existente.|

### <a name="quickstart-details"></a>Metadados incluído na definição do gerado

A tabela seguinte representa as definições do portal do Azure e dados correspondentes no function.json conforme está mapeada para a estrutura de Swagger gerada.

|Swagger.JSON|IU do portal|Function.JSON|
|:----|:-----|:-----|
|[Anfitrião](http://swagger.io/specification/#fixed-fields-15)|**As definições de aplicação de função** > **definições do serviço de aplicações** > **descrição geral** > **URL**|*Não está presente*
|[Caminhos](http://swagger.io/specification/#paths-object-29)|**Integrar** > **métodos de HTTP selecionado**|Enlaces: rota
|[Item do caminho](http://swagger.io/specification/#path-item-object-32)|**Integrar** > **modelo de rota**|Enlaces: métodos
|[Segurança](http://swagger.io/specification/#security-scheme-object-112)|**Chaves**|*Não está presente*|
|operationID *|**Rota + verbos permitidos**|Rota + verbos permitidos|

\*O ID da operação é necessário apenas para integrar com PowerApps e fluxo.
> [!NOTE]
> A extensão x-ms-resumo fornece um nome a apresentar no Logic Apps, PowerApps e fluxo.
>
> Para obter mais informações, consulte [personalizar a definição de Swagger para PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Utilize o CI/CD para definir uma definição de API

 Tem de ativar a definição da API no portal de alojamento antes de ativar o controlo de origem modificar a definição da API de controlo de origem. Siga estas instruções:

1. Navegue até à **definição da API (pré-visualização)** nas definições da aplicação de função.
  1. Definir **origem de definição de API** para **função**.
  1. Clique em **modelo de definição de API gerar** e, em seguida, **guardar** para criar uma definição de modelo para modificar mais tarde.
  1. Tenha em atenção que o URL de definição de API e a chave.
1. [Configurar a implementação de integração contínua/contínua (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. Modificar swagger.json no controlo de origem em \site\wwwroot\.azurefunctions\swagger\swagger.json.

Agora, as alterações à swagger.json no seu repositório estão alojadas pela sua aplicação de função na API do URL de definição e a chave que anotou no passo 1.c.

## <a name="next-steps"></a>Passos seguintes
* [Tutorial de introdução](functions-api-definition-getting-started.md). Experimente o nosso explicação passo a passo para ver uma definição de OpenAPI em ação.
* [Repositório do GitHub de funções do Azure](https://github.com/Azure/Azure-Functions/). Veja o repositório de funções para lhe dar-nos comentários sobre a pré-visualização de suporte de definição de API. Se um problema no GitHub para tudo o que pretende ver atualizado.
* [Referência de programadores das funções do Azure](functions-reference.md). Saiba mais sobre as funções de codificação e definição de acionadores e enlaces.
