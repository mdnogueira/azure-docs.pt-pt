---
title: "Criar uma definição de OpenAPI para uma função | Microsoft Docs"
description: "Crie uma definição de OpenAPI que permite a outras aplicações e serviços para chamar a função no Azure."
services: functions
keywords: "OpenAPI, Swagger, aplicações em nuvem, serviços em nuvem,"
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/25/2017
ms.author: mblythe; glenga
ms.custom: mvc
ms.openlocfilehash: a196df5b4ab47b234b48594da45cd4d72f604086
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-openapi-definition-for-a-function"></a>Criar uma definição de OpenAPI para uma função
REST APIs, muitas vezes, são descritas com uma definição de OpenAPI (anteriormente conhecido como um [Swagger](http://swagger.io/) ficheiro). Esta definição contém informações sobre as operações que estão disponíveis numa API e a forma como os dados de pedido e resposta para a API devem ser estruturados.

Neste tutorial, vai criar uma função que determina se uma reparação de emergência num turbine vento económica. Em seguida, crie uma definição de OpenAPI para a aplicação de função para que a função pode ser chamada a partir de outras aplicações e serviços.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma função no Azure
> * Gerar uma definição de OpenAPI utilizando ferramentas OpenAPI
> * Modificar a definição de metadados adicionais
> * A definição de teste ao chamar a função

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. Permite de aplicação de função, grupo de funções como uma unidade lógica para facilitar a gestão de implementação, dimensionamento e partilha de recursos. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]


## <a name="create-the-function"></a>Criar a função

Este tutorial utiliza uma função de acionada de HTTP que utiliza dois parâmetros: o tempo estimado para tornar um turbine reparar (em horas) e a capacidade de turbine (em quilowatts). A função, em seguida, calcula quanto a reparação será custo, e quantidade receitas de turbine pode tornar num período de 24 horas.

1. Expanda a sua aplicação de função, clique o  **+**  junto a **funções**, clique em de **HTTPTrigger** modelo. Introduza `TurbineRepair` para a função **nome** e clique em **criar**.

    ![Painel de aplicações de função, as funções +](media/functions-openapi-definition/add-function.png)

1. Substitua o conteúdo do ficheiro run.csx com o código seguinte, em seguida, clique em **guardar**:

    ```c#
    using System.Net;

    const double revenuePerkW = 0.12; 
    const double technicianCost = 250; 
    const double turbineCost = 100;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {   

        //Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();
        int hours = data.hours;
        int capacity = data.capacity;

        //Formulas to calculate revenue and cost
        double revenueOpportunity = capacity * revenuePerkW * 24;  
        double costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;

        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        }

        return req.CreateResponse(HttpStatusCode.OK, new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix         
        }); 
    }
    ```
    Este código de função devolve uma mensagem de `Yes` ou `No` para indicar se uma reparação de emergência é económica, bem como a oportunidade de receitas que representa o turbine e o custo para corrigir o turbine. 

1. Para testar a função, clique em **testar** no extremo direito expandir o separador de teste. Introduza o seguinte valor para o **corpo do pedido**e, em seguida, clique em **executar**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Testar a função no portal do Azure](media/functions-openapi-definition/test-function.png)

    É devolvido o seguinte valor no corpo da resposta.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Agora, tem uma função que determina a eficácia de custos de emergência reparações. Em seguida, pode gera e modificar uma definição de OpenAPI para a aplicação de função.

## <a name="generate-the-openapi-definition"></a>Gerar a definição de OpenAPI

Agora está pronto para gerar a definição de OpenAPI. Esta definição pode ser utilizada por outras tecnologias da Microsoft, como API Apps, [PowerApps](functions-powerapps-scenario.md) e [Microsoft Flow](../azure-functions/app-service-export-api-to-powerapps-and-flow.md), como o terceira, bem como ferramentas de Programador de terceiros, como [Postman](https://www.getpostman.com/docs/importing_swagger) e [muitos mais pacotes](http://swagger.io/tools/).

1. Selecione apenas a *verbos* que a sua API suporta (neste POST maiúsculas). Isto faz com que a definição da API gerada limpeza.

    1. No **integrar** separador da sua nova função de Acionador de HTTP, alteração **métodos de HTTP permitido** para **selecionado métodos**

    1. No **métodos de HTTP selecionado**, desmarque todas as opções, exceto **POST**.

        ![Métodos de HTTP selecionados](media/functions-openapi-definition/selected-http-methods.png)
        
1. Clique no nome de aplicação de função (como **demonstração de função energia**) > **funcionalidades da plataforma** > **definição da API**.

    ![Definição da API](media/functions-openapi-definition/api-definition.png)

1. No **definição da API** separador, clique em **função**.

    ![Origem de definição de API](media/functions-openapi-definition/api-definition-source.png)

    Este passo ativa um conjunto de opções de OpenAPI para a sua aplicação de função, incluindo um ponto final para alojar um ficheiro de OpenAPI do domínio da sua aplicação de função, uma cópia inline a [OpenAPI Editor](http://editor.swagger.io)e um gerador de modelo de definição de API.

1. Clique em **modelo de definição de API gerar** > **guardar**.

    ![Gerar o modelo de definição de API](media/functions-openapi-definition/generate-template.png)

    Azure analisa a sua aplicação de função para funções de Acionador de HTTP e utiliza as informações no functions.json para gerar uma definição de OpenAPI. Segue-se a definição de que é gerada:

    ```yaml
    swagger: '2.0'
    info:
    title: function-demo-energy.azurewebsites.net
    version: 1.0.0
    host: function-demo-energy.azurewebsites.net
    basePath: /
    schemes:
    - https
    - http
    paths:
    /api/TurbineRepair:
        post:
        operationId: /api/TurbineRepair/post
        produces: []
        consumes: []
        parameters: []
        description: >-
            Replace with Operation Object
            #http://swagger.io/specification/#operationObject
        responses:
            '200':
            description: Success operation
        security:
            - apikeyQuery: []
    definitions: {}
    securityDefinitions:
    apikeyQuery:
        type: apiKey
        name: code
        in: query
    ```

    Esta definição será descrita como um _modelo_ porque requer mais metadados para ser uma definição de OpenAPI completa. Terá de modificar a definição no próximo passo.

## <a name="modify-the-openapi-definition"></a>Modificar a definição de OpenAPI
Agora que tem uma definição de modelo, modifique-los para fornecer metadados adicionais sobre estruturas de dados e de operações da API. No **definição da API**, elimine a definição gerada do `post` na parte inferior da definição, cole o conteúdo abaixo e clique em **guardar**.

```yaml
    post:
      operationId: CalculateCosts
      description: Determines if a technician should be sent for repair
      summary: Calculates costs
      x-ms-summary: Calculates costs
      x-ms-visibility: important
      produces:
        - application/json
      consumes:
        - application/json
      parameters:
        - name: body
          in: body
          description: Hours and capacity used to calculate costs
          x-ms-summary: Hours and capacity
          x-ms-visibility: important
          required: true
          schema:
            type: object
            properties:
              hours:
                description: The amount of effort in hours required to conduct repair
                type: number
                x-ms-summary: Hours
                x-ms-visibility: important
              capacity:
                description: The max output of a turbine in kilowatts
                type: number
                x-ms-summary: Capacity
                x-ms-visibility: important
      responses:
        200:
          description: Message with cost and revenue numbers
          x-ms-summary: Message
          schema:
           type: object
           properties:
            message:
              type: string
              description: Returns Yes or No depending on calculations
              x-ms-summary: Message 
            revenueOpportunity:
              type: string
              description: The revenue opportunity cost
              x-ms-summary: RevenueOpportunity 
            costToFix:
              type: string
              description: The cost in $ to fix the turbine
              x-ms-summary: CostToFix
      security:
        - apikeyQuery: []
definitions: {}
securityDefinitions:
  apikeyQuery:
    type: apiKey
    name: code
    in: query
```

Neste caso, apenas pode colar nos metadados atualizado, mas é importante compreender os tipos de modificações que Tornamos para o modelo predefinido:

+ Especificar que a API produz e consome dados num formato JSON.

+ Especificar os parâmetros necessários, com os respetivos nomes e tipos dados.

+ Especificar os valores devolvidos por uma resposta com êxito, com os respetivos tipos de dados e de nomes.

+ Foi fornecido resumos amigáveis e descrições de API e operações e parâmetros. Isto é importante para as pessoas que irão utilizar esta função.

+ Adicionar x-ms-resumo e x-ms-visibilidade, que são utilizadas na IU para Microsoft Flow e Logic Apps. Para obter mais informações, consulte [OpenAPI extensões para APIs personalizadas no Microsoft Flow](https://preview.flow.microsoft.com/documentation/customapi-how-to-swagger/).

> [!NOTE]
> Iremos deixar a definição de segurança com o método de autenticação predefinido da chave de API. Deverá alterar esta secção da definição se tiver utilizado um tipo diferente de autenticação.

Para obter mais informações sobre como definir operações de API, consulte o [especificação de Open API](https://swagger.io/specification/#operationObject).

## <a name="test-the-openapi-definition"></a>A definição de OpenAPI de teste
Antes de utilizar a definição da API, é boa ideia testá-lo na IU de funções do Azure.

1. No **gerir** separador da sua função, em **chaves de anfitrião**, copiar o **predefinido** chave.

    ![Copie a chave de API](media/functions-openapi-definition/copy-api-key.png)

    > [!NOTE]
    >Utilize esta chave para fins de teste e também utilizá-la ao chamar a API de uma aplicação ou serviço.

1. Regresse à definição de API: **demonstração de função energia** > **funcionalidades da plataforma** > **definição da API**.

1. No painel direito, clique em **alterar autenticação**, introduza a chave de API que copiou e clique em **autenticar**.

    ![Autenticar com a chave de API](media/functions-openapi-definition/authenticate-api-key.png)

1. Desloque para baixo e clique em **Repita esta operação**.

    ![Repita esta operação](media/functions-openapi-definition/try-operation.png)

1. Introduza os valores para **horas** e **capacidade**.

    ![Introduza os parâmetros](media/functions-openapi-definition/parameters.png)

    Repare como a IU utiliza as descrições da definição de API.

1. Clique em **enviar um pedido de**, em seguida, clique o **Pretty** separador para ver o resultado.

    ![Enviar um pedido](media/functions-openapi-definition/send-request.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma função no Azure
> * Gerar uma definição de OpenAPI utilizando ferramentas OpenAPI
> * Modificar a definição de metadados adicionais
> * A definição de teste ao chamar a função

Avançar para o seguinte tópico para saber como criar uma aplicação de PowerApps que utiliza a definição de OpenAPI que criou.
> [!div class="nextstepaction"]
> [Chamar uma função a partir do PowerApps](functions-powerapps-scenario.md)
