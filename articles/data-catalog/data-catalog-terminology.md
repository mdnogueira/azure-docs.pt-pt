---
title: "Terminologia do catálogo de dados do Azure | Microsoft Docs"
description: "Este artigo fornece uma introdução aos conceitos e termos utilizados na documentação do catálogo de dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6fec74d9-4a3c-4b4b-88ba-cad5ad143331
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/01/2017
ms.author: maroche
ms.openlocfilehash: 8cb24357bb24c48dceda714a040427fcc0c0ba4d
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="azure-data-catalog-terminology"></a>Terminologia do catálogo de dados do Azure
## <a name="catalog"></a>Catálogo
O catálogo de dados do Azure é um repositório de metadados baseado na nuvem em que dados ativos origens e dados podem ser registados. O catálogo funciona como uma localização de armazenamento central para metadados estruturais extraídos das origens de dados e para metadados descritivos que adicionou pelos utilizadores.

## <a name="data-source"></a>Origem de dados
Uma origem de dados é um sistema ou o contentor que gere recursos de dados. Os exemplos incluem bases de dados do SQL Server, bases de dados Oracle, bases de dados do SQL Server Analysis Services (tabela ou multidimensionais) e servidores do SQL Server Reporting Services.

## <a name="data-asset"></a>Recurso de dados
Recursos de dados são objetos contidos origens de dados que podem ser registadas com o catálogo. Os exemplos incluem tabelas do SQL Server e vistas, Oracle tabelas e vistas, SQL Server Analysis Services medidas, dimensões e KPIs e relatórios do SQL Server Reporting Services.

## <a name="data-asset-location"></a>Localização do recurso de dados
O catálogo armazena a localização de uma origem de dados ou o recurso de dados, que pode ser utilizado para ligar à origem de utilização de uma aplicação de cliente. Os detalhes da localização e o formato variam com base no tipo de origem de dados. Por exemplo, uma tabela do SQL Server pode ser identificada pelo respetivo nome de quatro parte – nome do servidor, o nome de base de dados, o nome de esquema, o nome de objeto – enquanto um relatório de serviços de relatórios do SQL Server pode ser identificado pelo respetivo URL.

## <a name="structural-metadata"></a>Metadados estruturais
Os metadados estruturais são os metadados extraídos da origem de dados que descreve a estrutura de um recurso de dados. Isto inclui a localização de recursos, o nome do objeto e tipo e características de específicos do tipo adicionais. Por exemplo, os metadados estruturais para tabelas e vistas incluem nomes e tipos de dados para colunas do objeto.

## <a name="descriptive-metadata"></a>Metadados descritivos
Metadados descritivos são metadados que descreve a finalidade ou intenção de um recurso de dados. Normalmente, os metadados descritivos é adicionado pelos utilizadores do catálogo através do portal do catálogo de dados do Azure, mas também pode ser extraído da origem de dados durante o registo. Por exemplo, a ferramenta de registo do catálogo de dados do Azure que extrai descrições da propriedade descrição no SQL Server Analysis Services e o SQL Server Reporting Services e para o [ms_description propriedade expandida](https://technet.microsoft.com/library/ms190243.aspx) no SQL Server Bases de dados, se estas propriedades foram preenchidas com valores.

## <a name="request-access"></a>Pedir acesso
Metadados descritivos um recurso de dados podem incluir informações sobre como pedir acesso para o recurso de dados ou a origem de dados. Esta informação é apresentada a localização do recurso de dados e pode incluir um ou mais das seguintes opções:

* O endereço de e-mail do utilizador ou equipa responsável por conceder acesso à origem de dados.
* O URL do processo de documentado que os utilizadores têm de seguir para obter acesso à origem de dados.
* O URL de uma identidade e acesso ferramenta de gestão (tais como o Microsoft Identity Manager) que pode ser utilizado para obter acesso à origem de dados.
* Uma entrada de texto livre que descreve a forma como os utilizadores poderem obter acesso à origem de dados.

## <a name="preview"></a>Pré-visualização
Uma versão de pré-visualização no catálogo de dados do Azure é um instantâneo de até 20 registos que podem ser extraídos da origem de dados durante o registo e armazenados no catálogo com metadados do recurso de dados. A pré-visualização pode ajudar os utilizadores que detetar um recurso de dados compreender melhor a sua função e o objetivo. Por outras palavras, a ver dados de exemplo pode ser mais útil ao ver apenas os nomes das colunas e tipos de dados.
Pré-visualizações só são suportadas para tabelas e vistas e tem de ser explicitamente selecionados pelo utilizador durante o registo.

## <a name="data-profile"></a>Perfil de dados
Um perfil de dados no catálogo de dados do Azure é um instantâneo do nível de tabela e ao nível da coluna metadados sobre um recurso de dados registados que pode ser extraído da origem de dados durante o registo e armazenado no catálogo com metadados do recurso de dados. O perfil de dados pode ajudar os utilizadores que detetar um recurso de dados compreender melhor a sua função e o objetivo. Semelhante à pré-visualizações, perfis de dados tem de ser explicitamente selecionados pelo utilizador durante o registo.

> [!NOTE]
> Extrair um perfil de dados pode ser uma operação dispendiosa para tabelas grandes e vistas e pode aumentar significativamente o tempo necessário para registar uma origem de dados.
>
>

## <a name="user-perspective"></a>Perspetiva do utilizador
No catálogo de dados do Azure, qualquer utilizador pode fornecer metadados descritivos para um recurso de dados registados. Cada utilizador tem uma perspetiva distinta dos dados e a sua utilização. Por exemplo, o administrador responsável por um servidor pode fornecer os detalhes do respetivo contrato de nível de serviço (SLA) ou o windows de cópia de segurança; um steward de dados poderá fornecer ligações para documentação para as empresas e processa o suporta de dados; e um analista poderá fornecer uma descrição em termos que são mais relevantes para outros analistas e que pode ser mais valioso para os utilizadores que necessitam para detetar e compreender os dados.

Cada um destes perspetivas são inerentemente importantes e com o catálogo de dados do Azure, cada utilizador pode fornecer as informações que faça sentidas, apesar de todos os utilizadores podem utilizar essas informações para compreender os dados e o objetivo.

## <a name="expert"></a>disponibilizamos
Um especialista é um utilizador que foi identificado como tendo uma perspetiva informada "especialista" para um recurso de dados. Qualquer utilizador pode adicionar si próprios ou outro utilizador como um especialista para um recurso. A ser apresentado como um especialista não transmitir quaisquer privilégios adicionais no catálogo de dados do Azure; permite que os utilizadores localizar facilmente as perspetivas que são mais prováveis ser útil quando rever metadados descritivos um recurso.

## <a name="owner"></a>Proprietário
Um proprietário é um utilizador com privilégios adicionais para gerir um recurso de dados no catálogo de dados do Azure. Os utilizadores podem assumir a propriedade dos recursos de dados registados e os proprietários podem adicionar outros utilizadores como coproprietários. Para obter mais informações consulte [como gerir recursos de dados](data-catalog-how-to-manage.md)  

> [!NOTE]
> Propriedade e gestão estão disponíveis apenas na edição Standard do Azure dados de catálogo.
>
>

## <a name="registration"></a>Registo
O registo é o ato de extrair metadados do recurso de dados de uma origem de dados e copiar para o serviço de catálogo de dados do Azure. Recursos de dados que foram registados, em seguida, podem ser anotados e detetados.

## <a name="see-also"></a>Consultar também
* [O que é o Catálogo de Dados do Azure?](data-catalog-what-is-data-catalog.md) -Este artigo fornece uma descrição geral do serviço catálogo de dados do Azure, o valor fornece e os cenários que suporta.
* [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) -este artigo fornece um tutorial ponto-a-ponto que mostra como utilizar o catálogo de dados do Azure para a deteção de origem de dados.  
