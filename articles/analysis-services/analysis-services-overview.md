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
ms.date: 08/01/2017
ms.author: owend
ms.openlocfilehash: 8b3a538fc0fe34d0b7af8a99510cfb19abd32d55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-analysis-services"></a>O que é o Azure Analysis Services?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

O Azure Analysis Services disponibiliza modelação de dados de nível empresarial na cloud. É uma plataforma como serviço (PaaS) completamente gerida e ligada aos serviços de plataformas de dados do Azure. 

Com o Analysis Services, pode fazer mashup e combinar dados de várias origens, definir métricas e proteger os dados num único modelo de dados semântico fidedigno. O modelo de dados fornece uma forma mais rápida e fácil para os utilizadores procurarem quantidades enormes de dados com aplicações de cliente, como o Power BI, Excel, Reporting Services, aplicações de terceiros e personalizadas.

![Origens de dados](./media/analysis-services-overview/aas-overview-data-sources.png)

Veja [este vídeo](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4) para saber como é que o Azure Analysis Services se integra com as capacidades gerais de BI da Microsoft e como pode tirar partido da migração dos seus modelos de dados para a cloud.

## <a name="built-on-sql-server-analysis-services"></a>Baseado no SQL Server Analysis Services
O Azure Analysis Services é compatível com inúmeras funcionalidades excelentes já existentes no SQL Server Analysis Services Enterprise Edition. O Azure Analysis Services suporta modelos em tabela nos [níveis de compatibilidade](analysis-services-compat-level.md) 1200 e 1400. Partições, segurança ao nível da linha, relações bidirecionais e traduções são todas suportadas. Os modos dentro da memória e DirectQuery significam consultas ultrarrápidas em conjuntos de dados enormes e complexos.

Os modelos em tabela proporcionam desenvolvimento rápido e são altamente personalizáveis. Para os programadores, estes modelos incluem o Model de Objeto em Tabela (TOM) para descrever os objetos de modelos. O TOM é exposto em JSON, através da [linguagem TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) ,e da linguagem de definição de dados AMO, através do espaço de nomes [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

## <a name="better-with-azure"></a>Melhor com o Azure
O Azure Analysis Services integra-se em muitos serviços do Azure, o que lhe permite criar soluções de análise sofisticadas. A integração com o [Azure Active Directory](../active-directory/active-directory-whatis.md) fornece acesso seguro e baseado em funções aos seus dados críticos. Faça a integração com pipelines do [Azure Data Factory](../data-factory/introduction.md) ao incluir uma atividade que carrega dados para o modelo. A [Automatização do Azure](../automation/automation-intro.md) e as [Funções do Azure](../azure-functions/functions-overview.md) podem ser utilizadas para orquestrações leves de modelos através de código personalizado.

## <a name="get-up-and-running-quickly"></a>Comece a trabalhar rapidamente
No portal do Azure, pode [criar um servidor](analysis-services-create-server.md) numa questão de minutos. Além disso, com os [modelos](../azure-resource-manager/resource-manager-create-first-template.md) do Azure Resource Manager e o PowerShell, pode utilizar modelos declarativos para aprovisionar servidores. Com um só modelo, pode implementar múltiplos serviços juntamente com outros componentes do Azure, como contas de armazenamento e Funções do Azure. 

Assim que tiver um servidor criado, pode criar um modelo em tabela no portal do Azure. Com a nova funcionalidade (pré-visualização) de [estruturador Web](analysis-services-create-model-portal.md), pode ligar à Base de Dados SQL do Azure, a uma origem de dados do Azure SQL Data Warehouse ou importar um ficheiro .pbix do Power BI Desktop. As relações entre as tabelas são criadas automaticamente e pode criar medidas ou editar o ficheiro model.bim no formato json diretamente a partir do browser.

## <a name="scale-to-your-needs"></a>Dimensionar para as suas necessidades
O Azure Analysis Services está disponível nos escalões Programador, Básico e Standard. Em cada camada, os custos dos planos variam consoante o poder de processamento, o QPUs e o tamanho da memória. Quando criar um servidor, selecione um plano de dentro de uma camada. Pode mudar para escalões superiores ou inferiores na mesma camada ou atualizar para uma camada superior, mas não pode passar de uma camada superior para uma mais baixa.

Aumente ou reduza verticalmente o seu servidor ou interrompa-o. Utilize o portal do Azure ou utilize o PowerShell para ter controlo num instante. Só paga o que utilizar. Para saber mais sobre os diferentes planos e escalões e para utilizar a calculadora de preços para determinar qual é o melhor plano para si, veja [Preços de Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="keep-your-data-close"></a>Manter os seus dados próximos
O servidores do Azure Analysis Services podem ser criados nas [regiões do Azure](https://azure.microsoft.com/regions/) seguintes:

| Américas | Europa | Ásia-Pacífico |
|----------|--------|--------------|
|  Sul do Brasil<br> Canadá Central<br> EUA Leste 2<br> EUA Centro-Norte<br> EUA Centro-Sul<br> EUA Centro-Oeste<br> EUA Oeste | Europa do Norte<br> Reino Unido Sul<br> Europa Ocidental |   Sudeste da Austrália<br> Leste do Japão<br> Sudeste Asiático<br> Índia Ocidental  |

São adicionadas regiões novas constantemente, pelo que esta lista pode estar incompleta. A localização é escolhida quando cria o seu servidor no portal do Azure ou com os modelos do Azure Resource Manager. Para obter o melhor desempenho, escolha a localização mais perto da sua base de utilizadores. Implemente os seus modelos em servidores redundantes em várias regiões para garantir a [elevada disponibilidade](analysis-services-bcdr.md).

## <a name="migrate-your-existing-tabular-models"></a>Migrar os modelos em tabela existentes
Se já tiver soluções de modelos no local do SQL Server Analysis Services, pode migrar para o Azure Analysis Services sem que sejam necessárias grandes alterações. Para migrar, pode utilizar o SSDT para implementar o seu modelo no servidor. Em alternativa, no SSMS, pode utilizar a cópia de segurança e restauro ou o TMSL.

Se tiver de origens de dados no local, tem de instalar e configurar um [gateway de dados no local](analysis-services-gateway.md). Se tiver funções e membros de funções já configurados, as funções são migradas, mas os membros das mesmas têm de ser adicionados novamente com o SSM ou o PowerShell.

## <a name="connect-to-popular-data-sources"></a>Ligar a origens de dados populares
O Azure Analysis Services suporta a [ligação a origens de dados](analysis-services-datasource.md) no local na sua organização e na cloud. Combine dados das origens de dados no local e na cloud e obtenha uma solução híbrida. 

Os novos modelos 1400 em tabela utilizam a funcionalidade Obter Dados moderna no SSDT, com base na linguagem de consultas de fórmula M. Com Obter Dados, beneficia de mais funcionalidades de transformação e mashup de dados, bem como da capacidade de criar e editar as suas próprias consultas avançadas de linguagem de fórmula M. Por exemplo, nos modelos 1400 em tabela, pode fazer modelações em ficheiros de dados no Armazenamento de Blobs do Azure.

## <a name="use-the-tools-you-already-know"></a>Utilizar as ferramentas que já conhece

![Ferramentas de BI para programadores](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>SQL Server Data Tools (SSDT) para Visual Studio
Desenvolva e implemente modelos com o [SQL Server Data Tools (SSDT) para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) gratuito. O SSDT inclui modelos de projeto do Analysis Services que lhe permitem começar rapidamente. O SSDT inclui, agora, a funcionalidade de mashup e consultas de origens de dados Obter Dados para modelos 1400 em tabela. Se estiver familiarizado com Obter Dados no Power BI Desktop e no Excel 2016, já sabe como é fácil criar consultas de origens de dados altamente personalizadas.

#### <a name="sql-server-management-studio"></a>Sql Server Management Studio
Faça a gestão dos seus servidores e das suas bases de dados de modelos com o [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Ligue aos seus servidores na cloud. Execute scripts TMSL diretamente na janela de consultas XMLA e utilize-os para automatizar tarefas. As funcionalidades e funções novas são disponibilizadas rapidamente - o SSMS é atualizado todos os meses.

#### <a name="powershell"></a>PowerShell
As tarefas de gestão de recursos de servidores, como a criação de servidores, a suspensão ou retoma de operações de servidores ou a alteração do nível de serviço (escalão), utilizam os cmdlets do Azure Resource Manager (AzureRM). Outras tarefas para gerir bases de dados, como adicionar ou remover membros de funções, processar ou executar scripts TMSL, utilizam os cmdlets do módulo SqlServer. Os módulos AzureRM e SQLServer estão disponíveis na [galeria do PowerShell](https://www.powershellgallery.com/).


## <a name="your-data-is-secure"></a>Os dados estão seguros
![Visualizações de dados](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>Autenticação
O [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) lida com a autenticação dos utilizadores para o Azure Analysis Services. Quando tentam iniciar sessão numa base de dados do Azure Analysis Services, os utilizadores utilizam uma identidade de conta de organização com acesso à base de dados que pretendem utilizar. Estas identidades de utilizador têm de ser membros do Azure Active Directory predefinido da subscrição em que o servidor do Azure Analysis Services reside. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador).

#### <a name="data-security"></a>Segurança de dados
O Azure Analysis Services utiliza o Armazenamento de blobs do Azure para persistir o armazenamento e os metadados das bases de dados do Analysis Services. Os ficheiros de dados no Blob são encriptados com a Encriptação do Lado do Servidor (SSE) do Blob do Azure. Ao utilizar o modo DirectQuery, só são armazenados os metadados. Os dados propriamente ditos são acedidos a partir da origem de dados no momento da consulta.

#### <a name="on-premises-data-sources"></a>Origens de dados no local
Pode instalar e configurar um [Gateway de dados no local](analysis-services-gateway.md) para proteger o acesso aos dados que residem no local na sua organização. Os gateways fornecem acesso aos dados, tanto para o modo DirectQuery, e para o modo dentro da memória. Quando um modelo do Azure Analysis Services se liga a uma origem de dados no local, é criada uma consulta, juntamente com as credenciais encriptadas dessa origem de dados. O serviço cloud do gateway analisa a consulta e envia o pedido para um barramento do Azure Service Bus. O gateway no local sonda o barramento do Azure Service Bus à procura de pedidos pendentes. Em seguida, o gateway obtém a consulta, desencripta as credenciais e liga-se à origem de dados para execução. Os resultados são, depois, reenviados da origem de dados para o gateway e daí para a base de dados do Azure Analysis Services.

O Azure Analysis Services é regido pelos [Termos do Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e pela [Declaração de Privacidade do Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Para saber mais sobre a Segurança do Azure, veja o [Centro de Fidedignidade do Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="supports-the-latest-client-tools"></a>Suporta as ferramentas de cliente mais recentes
![Visualizações de dados](./media/analysis-services-overview/aas-overview-clients.png)

As ferramentas modernas de exploração e visualização de dados, como o Power BI, o Excel e ferramentas de terceiros, proporcionam aos utilizadores informações altamente interativas e visualmente ricas relativamente aos dados do seu modelo.

Os clientes utilizam as [bibliotecas de cliente](analysis-services-data-providers.md) MSOLAP, AMO ou ADOMD para se ligarem aos servidores do Analysis Services. As aplicações de cliente da Microsoft, como o Power BI Desktop e o Excel, instalam as três bibliotecas de cliente. No entanto, tenha em conta que, consoante a versão ou a frequência das atualizações, as bibliotecas poderão não corresponder às versões mais recentes de que o Azure Analysis Services precisa. O mesmo se aplica a aplicações personalizadas ou a outras interfaces, como sCmd, TOM ou ADOMD.NET. Geralmente, estas aplicações requerem a instalação manual das bibliotecas como parte de um pacote.


## <a name="get-help"></a>Obter ajuda

#### <a name="documentation"></a>Documentação
O Azure Analysis Services é fácil de configurar e gerir. Pode encontrar aqui todas as informações necessárias para criar e gerir os seus serviços de servidores. A criação de um modelo de dados para implementação no seu servidor é praticamente igual à criação de um modelo de dados que é implementado num servidor no local. Em [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services), está disponível uma biblioteca abrangente de artigos de conceitos, procedimentos, tutoriais e referência.

#### <a name="videos"></a>Vídeos
Veja vídeos úteis sobre o [Azure Analysis Services no Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

#### <a name="blogs"></a>Blogues
As coisas evoluem rapidamente. Pode sempre ver as últimas informações no [blogue da equipa do Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/) e no [blogue do Azure](https://azure.microsoft.com/blog/).

#### <a name="community"></a>Comunidade
O Analysis Services tem uma vibrante comunidade de utilizadores. Junte-se à conversa no [fórum do Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Comentários
Tem sugestões ou pedidos de funcionalidades? Não se esqueça de deixar os seus comentários nos [Comentários ao Azure Analysis Services](https://aka.ms/azureanalysisservicesfeedback).

Tem sugestões relativamente à documentação? Pode adicionar comentários com o Livefyre na parte inferior de cada artigo.

## <a name="next-steps"></a>Passos seguintes
Agora que já sabe mais sobre o Azure Analysis Services, é altura de começar a utilizá-lo. Saiba como [criar um servidor](analysis-services-create-server.md) no Azure. Quando o servidor estiver pronto, veja o [tutorial da Adventure Works](tutorials/aas-adventure-works-tutorial.md) para saber como criar um modelo em tabela totalmente funcional e implementá-lo no seu servidor.
