---
title: Como origens de dados de perfil de dados
description: "Artigo procedimentos realce como incluem perfis de dados de nível de tabela e coluna ao registar origens de dados no catálogo de dados do Azure e como utilizar perfis de dados para compreender as origens de dados."
services: data-catalog
documentationcenter: 
author: spelluru
manager: NA
editor: 
tags: 
ms.assetid: 94a8274b-5c9c-4962-a4b1-2fed38a3d919
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: spelluru
ms.openlocfilehash: 848d244a2a6c3420d1a11005e1b02f5671d7912a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="data-profile-data-sources"></a>Origens de dados de perfis de dados
## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço em nuvem completamente gerido que funciona como um sistema de registo e de deteção de origens de dados empresariais. Por outras palavras, **catálogo de dados do Azure** está tudo sobre ajudando as pessoas detetar, compreender e utilizar origens de dados e que ajuda as organizações para rentabilizar o valor a partir dos respetivos dados existentes. Quando uma origem de dados é registada com **catálogo de dados do Azure**, os metadados é copiado e o serviço indexa, mas o bloco não termina não existe.

O **a criação de perfis de dados** funcionalidade do **catálogo de dados do Azure** analisa os dados a partir de origens de dados suportadas no seu catálogo e recolhe estatísticas e as informações sobre os dados. É fácil incluir um perfil dos seus recursos de dados. Ao registar um recurso de dados, escolha **incluir perfil de dados** na ferramenta de registo da origem de dados.

## <a name="what-is-data-profiling"></a>O que é a criação de perfis de dados
A criação de perfis de dados analisa os dados da origem de dados que está a ser registado e recolhe estatísticas e as informações sobre os dados. Durante a deteção de origem de dados, estas estatísticas podem ajudar a determinar a adequabilidade dos dados para resolver seu problema empresarial.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

As seguintes origens de dados suportam a criação de perfis de dados:

* SQL Server (incluindo BD SQL do Azure e Azure SQL Data Warehouse) tabelas e vistas
* Oracle tabelas e vistas
* Teradata tabelas e vistas
* Tabelas do Hive

Perfis de dados, incluindo quando registar recursos de dados ajuda os utilizadores a responder às perguntas sobre origens de dados, incluindo:

* Pode, ser utilizado para resolver o meu problema empresarial?
* Os dados em conformidade com as normas específicas ou padrões?
* Quais são alguns de anomalias da origem de dados?
* O que são possíveis desafios de integrar estes dados na minha aplicação?

> [!NOTE]
> Também pode adicionar documentação para um recurso de descrever como dados podem ser integrados numa aplicação. Consulte [como documentar origens de dados](data-catalog-how-to-documentation.md).
>
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Como incluir um perfil de dados ao registar uma origem de dados
É fácil incluir um perfil da sua origem de dados. Quando registar uma origem de dados no **objetos a registar** painel da ferramenta de registo de origem de dados, escolha **incluir perfil de dados**.

![](media/data-catalog-data-profile/data-catalog-register-profile.png)

Para obter mais informações sobre como registar origens de dados, consulte [como registar origens de dados](data-catalog-how-to-register.md) e [introdução ao catálogo de dados do Azure](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtragem de recursos de dados que incluem perfis de dados
Para detetar recursos de dados que incluem um perfil de dados, pode incluir `has:tableDataProfiles` ou `has:columnsDataProfiles` como um dos seus termos de pesquisa.

> [!NOTE]
> Selecionar **incluir perfil de dados** na origem de dados a ferramenta de registo inclui tabela e as informações do perfil de nível de coluna. No entanto, a API do catálogo de dados permite que os recursos de dados a registar com apenas um conjunto de informações de perfil incluídas.
>
>

## <a name="viewing-data-profile-information"></a>Ver as informações de perfil de dados
Depois de encontrar uma origem de dados adequado com um perfil, pode ver os detalhes do perfil de dados. Para ver o perfil de dados, selecione um recurso de dados e escolha **dados perfil** na janela do portal do catálogo de dados.

![](media/data-catalog-data-profile/data-catalog-view.png)

Um perfil de dados no **catálogo de dados do Azure** mostra tabela e informações de perfil de coluna, incluindo:

### <a name="object-data-profile"></a>Perfil de dados de objeto
* Número de linhas
* Tamanho da tabela
* Quando o objeto foi atualizado pela última vez

### <a name="column-data-profile"></a>Perfil de dados da coluna
* Tipo de dados da coluna
* Número de valores distintos
* Número de linhas com valores nulos
* Mínimo, máximo, média e desvio padrão para valores da coluna

## <a name="summary"></a>Resumo
Data de criação de perfis fornece estatísticas e as informações sobre recursos de dados registados para o ajudar a determinar a adequabilidade dos dados para resolver problemas empresariais. Juntamente com anotar e documentar origens de dados, perfis de dados podem dar aos utilizadores uma compreensão mais aprofundada dos seus dados.

## <a name="see-also"></a>Veja Também
* [Como registar origens de dados](data-catalog-how-to-register.md)
* [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md)
