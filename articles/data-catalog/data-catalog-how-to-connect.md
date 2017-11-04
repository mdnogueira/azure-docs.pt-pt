---
title: Como ligar a origens de dados | Microsoft Docs
description: "Artigo procedimentos realce como ligar a origens de dados detetadas com o catálogo de dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 4e6b27a5-cf75-4012-b88c-333c1fe638e8
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: maroche
ms.openlocfilehash: 8176a952107a630d42d557e568a230f1cdc840aa
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="how-to-connect-to-data-sources"></a>Como ligar a origens de dados
## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço em nuvem completamente gerido que funciona como um sistema de registo e de deteção de origens de dados empresariais. Por outras palavras, **catálogo de dados do Azure** está tudo sobre ajudando as pessoas detetar, compreender e utilizar origens de dados e que ajuda as organizações para rentabilizar o valor a partir dos respetivos dados existentes. Um aspeto chave deste cenário é utilizar os dados – assim que um utilizador Deteta uma origem de dados e compreende o seu objetivo, o passo seguinte é estabelecer ligação à origem de dados para colocar os dados a utilizar.

## <a name="data-source-locations"></a>Localizações de origem de dados
Durante o registo da origem de dados, **catálogo de dados do Azure** recebe os metadados sobre a origem de dados. Estes metadados incluem os motivos da localização da origem de dados. Os detalhes da localização irão variar a partir da origem de dados à origem de dados, mas irão conter sempre as informações necessárias para estabelecer ligação. Por exemplo, a localização de uma tabela do SQL Server inclui o nome do servidor, nome de base de dados, nome de esquema e nome de tabela, enquanto a localização para um relatório do SQL Server Reporting Services inclui o nome do servidor e o caminho para o relatório. Outros tipos de origens de dados terão localizações refletem a estrutura e capacidades do sistema de origem.

## <a name="integrated-client-tools"></a>Ferramentas de cliente integrada
A forma mais simples para ligar a uma origem de dados está a utilizar o "Abrir em..." menu no **catálogo de dados do Azure** portal. Este menu apresenta uma lista de opções para ligar ao recurso de dados selecionada.
Quando utilizar a vista de mosaico predefinida, este menu está disponível em cada mosaico.

 ![Abrir uma tabela do SQL Server no Excel a partir do mosaico de recurso de dados](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Quando utilizar a vista de lista, no menu está disponível na barra de pesquisa na parte superior da janela do portal.

 ![Abrir um relatório do SQL Server Reporting Services no Gestor de relatórios na barra de pesquisa](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Aplicações de cliente suportados
Ao utilizar o "Abrir em..." menu para origens de dados no portal do catálogo de dados do Azure, a aplicação correta de cliente tem de estar instalado no computador cliente.

| Abrir numa aplicação | Extensão de ficheiro / protocolo | Versões de aplicações suportados |
| --- | --- | --- |
| Excel |. odc |Excel 2010 ou posterior |
| Excel (primeiros 1000) |. odc |Excel 2010 ou posterior |
| Power Query |ou |Excel 2016 ou o Excel 2010 ou o Excel 2013 com o Power Query para o suplemento do Excel instalado |
| Ambiente de trabalho do Power BI |. pbix |Power BI Desktop Julho de 2016 ou posterior |
| SQL Server Data Tools |vsweb: / / |Visual Studio 2013 atualização 4 ou posterior com ferramentas do SQL Server instalada |
| Gestor de relatórios |http:// |Consulte [requisitos de browser do SQL Server Reporting Services](https://technet.microsoft.com/en-us/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Os dados, as ferramentas
As opções disponíveis no menu dependerão do tipo de recurso de dados atualmente selecionado. Obviamente, nem todas as ferramentas de possíveis serão incluídas a "Abrir em..." menu, mas é ainda mais fácil ligar à origem de dados utilizando qualquer ferramenta de cliente. Quando um recurso de dados está selecionado no **catálogo de dados do Azure** portal, a localização de conclua é apresentada no painel Propriedades.

 ![Informações de ligação para uma tabela do SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Os detalhes de informações de ligação serão diferentes do tipo de origem de dados para o tipo de origem de dados, mas as informações incluídas no portal do irão dar-lhe tudo o que precisa para ligar à origem de dados em qualquer ferramenta de cliente. Os utilizadores podem copiar os detalhes de ligação para as origens de dados que são detetados utilizando **catálogo de dados do Azure**, ativá-las trabalhar com os dados na ferramenta de sua escolha.

## <a name="connecting-and-data-source-permissions"></a>Permissões de origem de dados e de ligação
Embora **catálogo de dados do Azure** faz com que as origens de dados detetável, acesso aos dados propriamente dito permanece sob o controlo do proprietário da origem de dados ou administrador. Deteção de uma origem de dados de **catálogo de dados do Azure** não atribua um utilizador permissões para aceder à origem de dados própria.

Para tornar mais fácil para os utilizadores que detetar uma origem de dados, mas não tem permissão para aceder aos respetivos dados, os utilizadores podem fornecer informações na propriedade pedir acesso ao anotar uma origem de dados. Informações fornecidas aqui – incluindo ligações para o processo ou o ponto de contacto para obter acesso à origem de dados – são apresentadas em conjunto com as informações de localização de origem de dados no portal.

 ![Informações de ligação com instruções de acesso de pedido fornecidas](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Resumo
Registar uma origem de dados com **catálogo de dados do Azure** faz com que os dados detetável por copiar os metadados estruturais e descritivo da origem de dados para o serviço de catálogo. Depois de uma origem de dados tiver sido registada e detetada, os utilizadores podem ligar à origem de dados do **catálogo de dados do Azure** portal "Abrir em..." " menu ou utilizar as ferramentas de dados escolhidas.

## <a name="see-also"></a>Consultar também
* [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) tutorial para obter detalhes passo a passo sobre como ligar a origens de dados.
