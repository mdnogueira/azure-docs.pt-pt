---
title: "O que é o Azure Analysis Services | Microsoft Docs"
description: Veja o enquadramento geral do Analysis Services no Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 34726377836d00d484ca01edb098f6c7cbfa9dbf
ms.contentlocale: pt-pt
ms.lasthandoff: 06/17/2017


---
<a id="what-is-azure-analysis-services" class="xliff"></a>

# O que é o Azure Analysis Services?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Baseado no motor analítico comprovado do Microsoft SQL Server Analysis Services, o Azure Analysis Services proporciona modelação de dados de nível empresarial na cloud. 

Veja este vídeo para saber como é que o Azure Analysis Services se integra com as capacidades gerais de BI da Microsoft e como pode tirar partido da migração dos seus modelos de dados para a cloud.


>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>


<a id="built-on-sql-server-analysis-services" class="xliff"></a>

## Baseado no SQL Server Analysis Services
O Azure Analysis Services é compatível com o SQL Server Analysis Services Enterprise Edition com que já está familiarizado. Suporta modelos em tabela nos níveis de compatibilidade 1200 ou seguintes. DirectQuery, partições, segurança ao nível da linha, relações bidirecionais e traduções são todos suportados.

<a id="use-the-tools-you-already-know" class="xliff"></a>

## Utilizar as ferramentas que já conhece
![Ferramentas de BI para programadores](./media/analysis-services-overview/aas-overview-dev-tools.png)

Ao criar modelos de dados para o Azure Analysis Services, vai utilizar as mesmas ferramentas do SQL Server Analysis Services. Utilize o [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) para criar e implementar modelos. Faça a gestão dos seus servidores e das suas bases de dados de modelos com o [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Automatize tarefas com o [PowerShell](analysis-services-powershell.md) e os modelos do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

<a id="supports-the-latest-features" class="xliff"></a>

## Suporta as funcionalidades mais recentes
O Azure Analysis Services suporta modelos em tabela nos níveis de compatibilidade 1200 e 1400 (Pré-visualização).

**Tabular 1200** - incluída pela primeira vez no SQL Server 2016 Analysis Services, a 1200 introduziu o Modelo de Objeto em Tabela (TOM) para descrever objetos de modelos como tabelas, colunas e relações. O Modelo de Objeto em Tabela é exposto em JSON, através da [linguagem TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference), e da linguagem de definição de dados AMO, através do espaço de nomes [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

**Tabular 1400 (Pré-visualização)** - introduz o suporte para Linhas Detalhadas, Segurança ao nível do objeto, hierarquia desalinhada, uma experiência de Obtenção de Dados moderna para conectividade de dados e muitas outras melhorias. Para tirar partido de todas as últimas funcionalidades, tem de utilizar o mais recente [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx). Uma vez que Tabular 1400 ainda está em pré-visualização, as funcionalidades mudam a um ritmo rápido. Para ver as últimas novidades, veja a nossa [mensagem no blogue](https://azure.microsoft.com/blog/1400-models-in-azure-as/).

<a id="data-sources" class="xliff"></a>

## Origens de dados
Os modelos de dados implementados nos servidores do Azure suportam ligação a origens de dados no local na organização ou na cloud. Combine dados das origens de dados no local e na cloud e obtenha uma solução de BI híbrida.

Uma vez que o servidor está na cloud, a ligação às origens de dados na cloud é contínua. Quando se ligar a origens de dados no local, o [Gateway de dados no local](analysis-services-gateway.md) garante ligações rápidas e seguras ao servidor na cloud. Para saber mais sobre que origens de dados no local são suportadas, veja [Data sources supported in Azure Analysis Service](analysis-services-datasource.md) (Origens de dados suportadas no Azure Analysis Services).


<a id="explore-your-data-from-anywhere" class="xliff"></a>

## Explorar os dados em qualquer local
Ligue-se e obtenha dados dos seus servidores onde quer que esteja. O Azure Analysis Services suporta ligações ao Power BI Desktop, ao Excel, a aplicações personalizadas e a ferramentas baseadas no browser.

![Visualizações de dados](./media/analysis-services-overview/aas-overview-visualization.png)


<a id="secure" class="xliff"></a>

## Proteger
<a id="user-authentication" class="xliff"></a>

#### Autenticação de utilizador
O [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) lida com a autenticação dos utilizadores para o Azure Analysis Services. Quando tentam iniciar sessão numa base de dados do Azure Analysis Services, os utilizadores utilizam uma identidade de conta de organização com acesso à base de dados que pretendem utilizar. Estas identidades de utilizador têm de ser membros do Azure Active Directory predefinido da subscrição em que o servidor do Azure Analysis Services reside. A [integração de diretórios](https://technet.microsoft.com/library/jj573653.aspx) entre o AAD e o Active Directory no local é uma excelente forma de fazer com que os seus utilizadores acedam à base de dados do Azure Analysis Services, mas não é necessária em todos os cenários.

Os utilizadores iniciam sessão com o nome principal de utilizador (UPN) das contas deles e respetivas palavras-passe. Se for sincronizado com um Active Directory no local, o UPN dos utilizadores é, em muitos casos, o endereço de e-mail da organização.

As permissões para gerir o recurso do servidor do Azure Analysis Services são tratadas mediante a atribuição de utilizadores a funções na sua subscrição do Azure. Por predefinição, os administradores das subscrições têm permissões de proprietário no recurso do servidor no Azure. Podem ser adicionados outros utilizadores com o Azure Resource Manager.

<a id="data-security" class="xliff"></a>

#### Segurança de dados
O Azure Analysis Services utiliza o Armazenamento de blobs do Azure para persistir o armazenamento e os metadados das bases de dados do Analysis Services. Os ficheiros de dados no Blob são encriptados com a Encriptação do Lado do Servidor (SSE) do Blob do Azure. Ao utilizar o modo DirectQuery, só são armazenados os metadados. Os dados propriamente ditos são acedidos a partir da origem de dados no momento da consulta.

<a id="on-premises-data-sources" class="xliff"></a>

#### Origens de dados no local
Pode instalar e configurar um [Gateway de dados no local](analysis-services-gateway.md) para proteger o acesso aos dados que residem no local na sua organização. Os gateways fornecem acesso aos dados, tanto para o modo DirectQuery, e para o modo dentro da memória. Quando um modelo do Azure Analysis Services se liga a uma origem de dados no local, é criada uma consulta, juntamente com as credenciais encriptadas dessa origem de dados. O serviço cloud do gateway analisa a consulta e envia o pedido para um barramento do Azure Service Bus. O gateway no local sonda o barramento do Azure Service Bus à procura de pedidos pendentes. Em seguida, o gateway obtém a consulta, desencripta as credenciais e liga-se à origem de dados para execução. Os resultados são, depois, reenviados da origem de dados para o gateway e daí para a base de dados do Azure Analysis Services.

O Azure Analysis Services é regido pelos [Termos do Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e pela [Declaração de Privacidade do Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Para saber mais sobre a Segurança do Azure, veja o [Centro de Fidedignidade do Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

<a id="get-help" class="xliff"></a>

## Obter ajuda

<a id="documentation" class="xliff"></a>

### Documentação
O Azure Analysis Services é fácil de configurar e gerir. Pode encontrar aqui todas as informações necessárias para criar e gerir servidores. A criação de um modelo de dados para implementação no seu servidor é praticamente igual à criação de um modelo de dados que é implementado num servidor no local. Em [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services), está disponível uma biblioteca abrangente de artigos de conceitos, procedimentos, tutoriais e referência.

<a id="videos" class="xliff"></a>

### Vídeos
Veja vídeos úteis sobre o [Azure Analysis Services no Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

<a id="blogs" class="xliff"></a>

### Blogues
As coisas evoluem rapidamente. Pode sempre ver as últimas informações no [blogue da equipa do Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/) e no [blogue do Azure](https://azure.microsoft.com/blog/).

<a id="community" class="xliff"></a>

### Comunidade
O Analysis Services tem uma vibrante comunidade de utilizadores. Junte-se à conversa no [fórum do Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

<a id="feedback" class="xliff"></a>

## Comentários
Tem sugestões ou pedidos de funcionalidades? Não se esqueça de deixar os seus comentários nos [Comentários ao Azure Analysis Services](https://aka.ms/azureanalysisservicesfeedback).

Tem sugestões relativamente à documentação? Pode adicionar comentários com o Livefyre na parte inferior de cada artigo.

<a id="next-steps" class="xliff"></a>

## Passos seguintes
Agora que já sabe mais sobre o Azure Analysis Services, é altura de começar a utilizá-lo. Saiba como [criar um servidor](analysis-services-create-server.md) no Azure e [implementar um modelo em tabela](analysis-services-deploy.md) no mesmo.

