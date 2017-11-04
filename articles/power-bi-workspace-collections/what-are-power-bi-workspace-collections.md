---
title: "Quais são as coleções de área de trabalho do Power BI?"
description: "Power BI Embedded permite-lhe integrar relatórios do Power BI na sua web ou de aplicações móveis, por isso não terá de criar soluções personalizadas."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 7df172895bb926f1715370b941964e2c29ab393d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-power-bi-workspace-collections"></a>Quais são as coleções de área de trabalho do Power BI?

Com **coleções de área de trabalho do Power BI**, pode integrar os relatórios do Power BI diretamente no seu web ou de aplicações móveis.

![Diagrama de aplicação](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

As coleções de área de trabalho do Power BI são um **serviço do Azure** que permite que os ISVs e os programadores de aplicações para experiências de dados do Power BI superfície dentro das suas aplicações. Como um programador, criou aplicações e as aplicações têm os seus próprios utilizadores e um conjunto diferente de funcionalidades. Essas aplicações também podem acontecer ter alguns elementos de dados incorporada, como gráficos e relatórios que podem ser desligados agora pelas coleções de área de trabalho do Microsoft Power BI. Não precisa de uma conta do Power BI para utilizar a aplicação. Pode continuar a iniciar sessão na sua aplicação, tal como anteriormente e ver e interagir com o Power BI reporting experiência sem necessidade de licenciamento adicionais.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Licenciamento do Microsoft Power BI área de trabalho coleções

No **coleções de área de trabalho do Microsoft Power BI** modelo de utilização, licenciamento para o Power BI não é da responsabilidade do utilizador final.  Em vez disso, **sessões** são adquiridas pelo programador da aplicação que está a consumir visuais e são cobradas para a subscrição que detém esses recursos. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Modelo concetual de coleções de área de trabalho do Microsoft Power BI

![Fluxo de aplicações com coleções de área de trabalho](media/what-are-power-bi-workspace-collections/model.png)

Como qualquer outro serviço no Azure, os recursos para coleções de área de trabalho do Power BI são aprovisionados através do [APIs do Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). Neste caso, é o recurso que é aprovisionar um **coleção de área de trabalho do Power BI**.

## <a name="workspace-collection"></a>Coleção de área de trabalho

A **coleção de área de trabalho** é o nível superior contentor do Azure para os recursos que contém 0 ou mais **áreas de trabalho**.  A **área de trabalho** **coleção** tem todas as propriedades do Azure standard, bem como o seguinte:

* **Chaves de acesso** – as chaves utilizadas durante a chamada de forma segura as APIs do Power BI (descrito na secção posterior).
* **Os utilizadores** – os utilizadores do Azure Active Directory (AAD) que tenha direitos de administrador para gerir a coleção de área de trabalho do Power BI através do portal do Azure ou a API do Azure Resource Manager.
* **Região** – como parte de aprovisionamento de um **coleção de área de trabalho**, pode selecionar uma região a ser aprovisionado no. Para obter mais informações, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Área de trabalho

A **área de trabalho** é um contentor do Power BI conteúdo, que pode incluir os conjuntos de dados e relatórios. A **área de trabalho** está vazio quando é criado pela primeira vez. Irá criar conteúdo utilizando o ambiente de trabalho do Power BI e irá implemente o PBIX através de programação na área de trabalho utilizando o [API de importação do Power BI](https://msdn.microsoft.com/library/mt711504.aspx). Pode também programaticamente criar o conjunto de dados e, em seguida, criar relatórios na sua aplicação em vez de utilizar o Power BI Desktop.

## <a name="using-workspace-collections-and-workspaces"></a>Utilizar coleções de área de trabalho e áreas de trabalho

**Coleções de área de trabalho** e **áreas de trabalho** são contentores de conteúdo que são utilizados e organizados de qualquer forma que melhor se adequa a estrutura da aplicação que está a criar. Será muitas formas diferentes, que foi dispor o conteúdo dentro delas. Pode optar por colocar todo o conteúdo dentro de uma área de trabalho e, em seguida, utilize tokens de aplicação mais tarde para obter mais subdividir o conteúdo dos seus clientes. Também pode optar por colocar todos os seus clientes em áreas de trabalho separadas para que não há alguns separação entre eles. Em alternativa, pode optar por organizar utilizadores por região, em vez de pelo cliente. Esta estrutura flexível permite-lhe escolher a melhor forma de organizar o conteúdo.

## <a name="cached-datasets"></a>Conjuntos de dados em cache

Conjuntos de dados em cache podem ser utilizados.  No entanto, não é possível atualizar dados em cache assim que tiver sido carregada **coleções de área de trabalho do Microsoft Power BI**. Um conjunto de dados em cache significa que importou os dados para o ambiente de trabalho do Power BI em vez de utilizar DirectQuery.

## <a name="authentication-and-authorization-with-app-tokens"></a>Autenticação e autorização com tokens de aplicação

**Coleções de área de trabalho do Microsoft Power BI** defers à sua aplicação para efetuar a autenticação de utilizador necessário e autorização. Não há nenhum requisito explícito de que os utilizadores finais ser clientes do Azure Active Directory (Azure AD).  Em vez disso, a aplicação expresse precisa para **coleções de área de trabalho do Microsoft Power BI** autorização para compor o relatório do Power BI utilizando **Tokens de autenticação da aplicação (Tokens de aplicação)**.  Estes **Tokens de aplicação** são criados, conforme necessário quando a aplicação que pretende um relatório de composição.

![Diagrama de utilização de tokens de aplicação](media/what-are-power-bi-workspace-collections/app-tokens.png)

**Os Tokens de autenticação de aplicação (Tokens de aplicação)** são utilizados para autenticar face **coleções de área de trabalho do Microsoft Power BI**.  Existem três tipos de **Tokens de aplicação**:

1. Aprovisionamento Tokens - utilizados quando aprovisionar um novo **área de trabalho** para um **coleção de área de trabalho**
2. Tokens de desenvolvimento - utilizados ao efetuar chamadas diretamente para o **APIs REST do Power BI**
3. Ao incorporar Tokens - utilizados ao efetuar chamadas para compor um relatório numa iframe incorporados

Estes tokens são utilizados para as fases das suas interações com vários **coleções de área de trabalho do Microsoft Power BI**.  Os tokens foram concebidos para que pode delegar as permissões da sua aplicação para o Power BI. Para obter mais informações, consulte [fluxo de Token de aplicação](app-token-flow.md).

## <a name="create-or-edit-reports-within-your-application"></a>Cria ou edita relatórios na sua aplicação

Agora pode editar os relatórios existentes ou criar novos relatórios diretamente na sua aplicação sem ter de utilizar o Power BI Desktop. Isto requer que um conjunto de dados existe dentro da sua área de trabalho.

## <a name="see-also"></a>Consultar também

[Cenários comuns de coleções de área de trabalho do Microsoft Power BI](scenarios.md)  
[Começar a utilizar coleções de área de trabalho do Microsoft Power BI](get-started.md)  
[Introdução com exemplo](get-started-sample.md)  
[Incorporar um relatório](embed-report.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Repositório de Git do PowerBI CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repositório de Git do nó de PowerBI](https://github.com/Microsoft/PowerBI-Node)  

Mais perguntas? [Tente a Comunidade do Power BI](http://community.powerbi.com/)
