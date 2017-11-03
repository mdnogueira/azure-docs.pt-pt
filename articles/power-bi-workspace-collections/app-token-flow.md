---
title: "Autenticação e autorização com coleções de área de trabalho do Power BI | Microsoft Docs"
description: "Autenticação e autorização com coleções de área de trabalho do Power BI."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: ae9627c6bb5e7bb099598acaa2eb29375c35593e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Autenticação e autorização com coleções de área de trabalho do Power BI

Coleções de área de trabalho de BI utilização de energia **chaves** e **Tokens de aplicação** para autenticação e autorização, em vez de autenticação de utilizador final explícita. Neste modelo, a sua aplicação gere autenticação e autorização para os utilizadores finais. Quando for necessário, a sua aplicação cria e envia os tokens de aplicação que dizem ao nosso serviço para compor o relatório pedido. Esta estrutura não requer a sua aplicação para utilizar o Azure Active Directory para autenticação de utilizador e a autorização, embora ainda possa.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Duas formas de autenticar

**Chave** -pode utilizar as chaves para todas as chamadas da API de REST do coleções de área de trabalho do Power BI. As chaves podem ser encontradas no **portal do Microsoft Azure** selecionando **todas as definições** e, em seguida, **chaves de acesso**. Trate sempre a sua chave como se se é uma palavra-passe. Estas chaves possuem permissões para efetuar qualquer API de REST chamada de uma coleção de área de trabalho específica.

Para utilizar uma chave de uma chamada REST, adicione o seguinte cabeçalho de autorização:

    Authorization: AppKey {your key}

**Token de aplicação** -tokens de aplicação são utilizados para todos os pedidos embedding. Destinam-se a execução do lado do cliente. O token está restrito a um único relatório e seu melhor prática para definir um prazo de expiração.

Os tokens de aplicação são um JWT (JSON Web Token) que esteja assinado por uma das suas chaves.

O token de aplicação pode conter as seguintes afirmações:

| Afirmação | Descrição |
| --- | --- |
| **ver** |A versão do token da aplicação. 0.2.0 é a versão atual. |
| **aud** |O destinatário do token. Para utilizar as coleções de área de trabalho do Power BI: "https://analysis.windows.net/powerbi/api." |
| **ISS** |Uma cadeia que indica que emitiu o token de aplicação. |
| **tipo** |O tipo de token de aplicação que está a ser criada. O único tipo suportado atual é **incorporar**. |
| **WCN** |O nome da coleção de área de trabalho de token está a ser emitido para. |
| **WID** |O token de ID de área de trabalho está a ser emitido para. |
| **RID** |ID de token está a ser emitido para um relatório. |
| **nome de utilizador** (opcional) |Utilizado com RLS, o nome de utilizador é uma cadeia que pode ajudar a identificar o utilizador ao aplicar regras RLS. |
| **funções** (opcional) |Uma cadeia que contém as funções para selecionar ao aplicar regras de segurança de nível de linha. Se a transmitir mais de uma função, deve ser transmitidas como uma matriz de sting. |
| **SCP** (opcional) |Uma cadeia que contém os âmbitos de permissões. Se a transmitir mais de uma função, deve ser transmitidas como uma matriz de sting. |
| **EXP** (opcional) |Indica a hora em que o token expira. O valor deve ser transmitido como carimbos de Unix. |
| **NBF** (opcional) |Indica a hora em que o token começa a ser válido. O valor deve ser transmitido como carimbos de Unix. |

Um token de aplicação de exemplo que se pareça com:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Quando descodificar, aspeto semelhante ao seguinte:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

Existem métodos disponíveis dentro os SDKs que o facilitar a criação de tokens de aplicação. Por exemplo, para o .NET pode examinar o [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) classe e o [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) métodos.

Para o SDK .NET, pode fazer referência a [âmbitos](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>âmbitos

Ao utilizar incorporação tokens, poderá querer restringir a utilização dos recursos de que conceder acesso. Por este motivo, pode gerar um token com permissões com âmbito definido.

Seguem-se os âmbitos disponíveis para coleções de área de trabalho do Power BI.

|Âmbito|Descrição|
|---|---|
|Dataset.Read|Fornece a permissão para ler o conjunto de dados especificado.|
|Dataset.Write|Fornece a permissão de escrita para o conjunto de dados especificado.|
|Dataset.ReadWrite|Fornece a permissão para ler e escrever para o conjunto de dados especificado.|
|Report.Read|Fornece a permissão para ver o relatório especificado.|
|Report.ReadWrite|Fornece a permissão para ver e editar o relatório especificado.|
|Workspace.Report.Create|Fornece a permissão para criar um novo relatório na área de trabalho especificado.|
|Workspace.Report.Copy|Fornece a permissão para clonar um relatório existente na área de trabalho especificado.|

Pode fornecer vários âmbitos, utilizando um espaço entre os âmbitos como o seguinte.

```
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Afirmações necessárias - âmbitos**

SCP: {scopesClaim} scopesClaim pode ser uma cadeia ou matriz de cadeias, salientar as permissões permitidas aos recursos da área de trabalho (relatório, conjunto de dados, etc.)

Um token descodificado, com definida, os âmbitos seria ter um aspeto semelhante a:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>Operações e âmbitos

|Operação|Recurso de destino|Permissões de token|
|---|---|---|
|Crie um novo relatório com base num conjunto de dados de (na memória).|Conjunto de dados|Dataset.Read|
|Criar um novo relatório com base num conjunto de dados de (na memória) e guarde o relatório.|Conjunto de dados|* Dataset.Read<br>* Workspace.Report.Create|
|Visualize e explore/Editar (na memória) um relatório existente. Report.Read implica Dataset.Read. Report.Read não permite a guardar as edições.|Relatório|Report.Read|
|Editar e guardar um relatório existente.|Relatório|Report.ReadWrite|
|Guarde uma cópia de um relatório (Guardar como).|Relatório|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Eis como funciona o fluxo
1. Copie as chaves de API à sua aplicação. Pode obter as chaves na **portal do Azure**.
   
    ![Onde encontrar as chaves de API no portal do Azure](media/get-started-sample/azure-portal.png)
1. Token de asserções uma afirmação e tem um período de tempo de expiração.
   
    ![Fluxo do token da aplicação - asserções de token de afirmação](media/get-started-sample/token-2.png)
1. Token obtém assinado com chaves de acesso da API.
   
    ![Aplicação fluxo do token - token obtém assinado](media/get-started-sample/token-3.png)
1. Pedidos de utilizador para ver um relatório.
   
    ![Fluxo do token da aplicação - pedidos de utilizador para ver um relatório](media/get-started-sample/token-4.png)
1. Token é validado com um chaves de acesso da API.
   
   ![Aplicação fluxo do token - token é validado](media/get-started-sample/token-5.png)
1. Coleções de área de trabalho do Power BI envia um relatório ao utilizador.
   
   ![Fluxo do token da aplicação - serviço Enviar relatório para utilizador](media/get-started-sample/token-6.png)

Depois de **coleções de área de trabalho do Power BI** envia um relatório para o utilizador, o utilizador pode ver o relatório na sua aplicação personalizada. Por exemplo, se importou o [exemplo analisar PBIX de dados de vendas](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), a aplicação web de exemplo seria ter o seguinte aspeto:

![Exemplo de relatório incorporado na aplicação](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Veja Também

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Introdução ao exemplo de coleções de área de trabalho do Microsoft Power BI](get-started-sample.md)  
[Cenários comuns de coleções de área de trabalho do Microsoft Power BI](scenarios.md)  
[Começar a utilizar coleções de área de trabalho do Microsoft Power BI](get-started.md)  
[Repositório de Git do PowerBI CSharp](https://github.com/Microsoft/PowerBI-CSharp)

Mais perguntas? [Tente a Comunidade do Power BI](http://community.powerbi.com/)