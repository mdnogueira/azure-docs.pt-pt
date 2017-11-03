---
title: "Técnicas pré-requisitos para criar um serviço de dados para o Marketplace | Microsoft Docs"
description: "Compreender os requisitos para criar um serviço de dados para implementar e propor no Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: aaff609a-1cd1-4146-98f4-d04166b0fce0
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 52827723477677bc292c645e2390c435fbad3ee4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>Oferecem técnicas pré-requisitos para criar um serviço de dados para o Azure Marketplace
> [!IMPORTANT]
> **Neste momento, estamos já não integração qualquer nova editores de serviço de dados. Novo dataservices não irá obter aprovada para listagem.** Se tiver uma aplicação de negócio de SaaS que pretende publicar no AppSource pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se tiver um aplicações IaaS ou serviço do programador que pretende publicar no Azure Marketplace, pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Leia o processo cuidadosamente antes de começar e compreenda o onde e por que motivo é efetuado cada passo. Quanto possível, deve preparar as informações da sua empresa e outros dados, descarregar as ferramentas necessárias, e/ou criar os componentes de técnicos antes de iniciar o processo de criação de oferta.

Deve ter os seguintes itens prontos antes de iniciar o processo:

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>Tomar uma decisão na tecnologia de que será utilizada para publicar a sua oferta de serviço de dados
Um publicador pode decidir entre várias tecnologias quando o serviço de dados de publicação no Azure Marketplace. As principais tecnologias que são suportadas descritas abaixo. Independentemente disso que tecnologia é utilizada para publicar o serviço de dados, o utilizador final consome os dados através de **feed de OData** expostos pelo serviço do Azure Marketplace. Total de informações sobre o serviço OData pode encontrar no [http://www.odata.org/](http://www.odata.org/)

## <a name="sql-azure-database"></a>SQL Server base de dados do Azure
Ter o conjunto de dados pronto no SQL Azure é responsabilidade do publicador. Terá de subscrever para o Azure, aprovisionar um tamanho adequado da base de dados e carregar os dados para a BD SQL do Azure. Publicador é também responsável para manter a sua dados sempre atualizados. Mais informações sobre a subscrição aos serviços do Azure, pode encontrar no [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)

Quando move os dados no SQL Azure, o Azure Marketplace pode expor as tabelas e vistas. O publicador, pode especificar que tabelas/Vistas e colunas são expostas ao utilizador final. Obter o fornecedor de conteúdos também pode especificar as colunas que podem ser consultadas pelo utilizador final e aqueles apenas são devolvidos no payload. Isto proporciona um elevado nível de flexibilidade sobre os quais os dados na base de dados devem ser expostos. As colunas que podem ser consultadas tem de estar associada a um ou mais índices de base de dados.

## <a name="rest-based-web-service"></a>Serviço web REST com base
Suportado protocolo: **apenas HTTPS**

Serviços REST com base existentes podem ser expostos através do Azure Marketplace. Porque o conjunto de dados é exposto sempre para o utilizador final como um feed de OData, as necessidades de serviço do Azure Marketplace para conseguir mapear o serviço para um OData com base em serviço. Para fazê o resto baseada em pontos finais necessário expor todos os parâmetros como parâmetros HTTP.

O payload tem de estar num formato que pode ser mapeado para uma resposta do ATOM. Por conseguinte, a resposta das necessidades de serviços para estar no formato XML e só pode conter um elemento repetido que contém os valores de payload (por exemplo, o conjunto de registos). O serviço do Azure Marketplace irá mapear o nó repetido para o nó de entrada do ATOM e os valores de payload em nós de propriedade no nó de entrada.

Informações de autorização (tais como API chave, autenticação token, etc.) tem de ser fornecido como um parâmetro HTTP ou no cabeçalho de HTTP (par chave-valor) – também é suportada a autenticação básica. Uma chave válida tem de ser fornecido e todos os pedidos através do Azure Marketplace estão a ser efetuados através dessa chave. Utilizador de monitorização e a faturação ocorre na camada do Azure Marketplace.

Erros devolvidos pelo serviço tem de ser mapeada para códigos de estado HTTP. No caso do serviço devolva um XML que contém o erro que estas vão continuar a ser mapeado pelo serviço do Azure Marketplace para códigos de estado HTTP.

## <a name="soap-based-web-services"></a>Serviços web SOAP com base
Protocolo: **apenas HTTPS**

Os requisitos são os mesmos que o resto em secção de serviço. A única diferença é que também podem ser fornecidos parâmetros no corpo XML que está a ser publicado para o serviço do publicador com todos os pedidos efetuados através do Azure Marketplace. Isto significa que os parâmetros HTTP que fornece o utilizador no front-end estão a ser convertidos para elementos XML de um documento XML que está a ser registado com o pedido de serviço web do fornecedor de conteúdo.

## <a name="odata-based-web-services"></a>Serviços web de OData com base
Protocolo: **apenas HTTPS**

Dados podem ser expostos como um serviço OData no Azure Marketplace. O sistema vai passar o serviço através de e substitui a raiz do serviço com a raiz de serviço do Azure Marketplace – para garantir que todas as chamadas subsequentes passam pelo Azure Marketplace.

Serviços OData só não precisam de ir em relação a uma base de dados de back-end. OData suporta qualquer tipo de lógica de armazenamento ou empresarial para o serviço de unidade.

## <a name="next-steps"></a>Passos Seguintes
Agora que revisto os pré-requisitos e concluir as tarefas necessárias, pode avançar com a sua oferta de serviço de dados conforme especificado na criação do [dados serviço de publicação guia](marketplace-publishing-data-service-creation.md).

Ou, se pretender rever o processo geral e os respetivos artigos para cada um das fases de publicação, visite o artigo [introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md).

[link-acct]:marketplace-publishing-accounts-creation-registration.md
