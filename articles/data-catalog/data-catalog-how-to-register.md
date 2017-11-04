---
title: "Registar origens de dados no catálogo de dados do Azure | Microsoft Docs"
description: "Este artigo realça como registar origens de dados no catálogo de dados do Azure, incluindo os campos de metadados extraídos durante o registo."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: maroche
ms.openlocfilehash: 42d7fddd592b9cea8fbfa38bf39ca5def3542d66
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registar origens de dados no catálogo de dados do Azure
## <a name="introduction"></a>Introdução
Catálogo de dados do Azure é um serviço em nuvem completamente gerido que funciona como um sistema de registo e a deteção de origens de dados empresariais. Por outras palavras, pessoas, detetar, compreender e utilizar origens de dados ajuda-o catálogo de dados e ajuda as organizações a obter maior valor dos respetivos dados existentes. O primeiro passo para efetuar uma origem de dados detetável através do catálogo de dados está a registar essa origem de dados.

## <a name="register-data-sources"></a>Registar origens de dados
O registo é o processo de extrair metadados da origem de dados e copiar esses dados para o serviço de catálogo de dados. Os dados permanecem no local onde residem atualmente e continuam sob o controlo dos administradores e das políticas do sistema atual.

Para registar uma origem de dados, efetue o seguinte:
1. No portal do catálogo de dados do Azure, inicie a ferramenta de registo de origem de dados do catálogo de dados. 
2. Inicie sessão com a sua conta profissional ou escolar com as mesmas credenciais do Azure Active Directory que utiliza para iniciar sessão no portal.
3. Selecione a origem de dados que pretende registar.

Para obter mais detalhes passo a passo, consulte o [introdução ao catálogo de dados do Azure](data-catalog-get-started.md) tutorial.

Depois de ter registado a origem de dados, o catálogo controla a respetiva localização e indexa os metadados. Os utilizadores podem pesquisar, procurar e detetar a origem de dados e, em seguida, utilizar a localização para ligar à mesma, utilizando a aplicação ou a ferramenta de sua escolha.

## <a name="supported-data-sources"></a>Origens de dados suportadas
Para obter uma lista de origens de dados atualmente suportados, consulte [DSR do catálogo de dados](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Metadados estruturais
Ao registar uma origem de dados, a ferramenta de registo extrai informações sobre a estrutura dos objetos que selecionar. Esta informação é referida como metadados estruturais.

Para todos os objetos, estes metadados estruturais incluem a localização do objeto, para que os utilizadores que detetar os dados possam utilizar essas informações para ligar para o objeto nas ferramentas de cliente da sua eleição. Outros metadados estruturais incluem o nome do objeto e o tipo e o nome de atributo/coluna e dados de tipo.

## <a name="descriptive-metadata"></a>Metadados descritivos
Para além dos metadados estruturais de núcleos que são extraídos da origem de dados, a ferramenta de registo da origem de dados extrai metadados descritivos. Para SQL Server Analysis Services e o SQL Server Reporting Services, estes metadados são obtidos a partir de propriedades de descrição expostas por estes serviços. Para o SQL Server, valores fornecidos com os ms\_descrição propriedade expandida é extraída. Para a base de dados Oracle, a ferramenta de registo da origem de dados extrai a coluna de comentários do todos os\_SEPARADOR\_vista de comentários.

Para além dos metadados descritivos que são extraídos da origem de dados, os utilizadores podem introduzir metadados descritivos utilizando a ferramenta de registo da origem de dados. Os utilizadores podem adicionar etiquetas e podem identificar especialistas para os objetos que está a ser registados. Estes metadados descritivos é copiado para o serviço de catálogo de dados, juntamente com os metadados estruturais.

## <a name="include-previews"></a>Incluir pré-visualizações
Por predefinição, apenas metadados é extraídos das origens de dados e copiados para o serviço de catálogo de dados, mas não compreender que uma origem de dados é frequentemente facilitada quando pode ver um exemplo dos dados que contém.

Ao utilizar a ferramenta de registo de origem de dados do catálogo de dados, pode incluir uma pré-visualização de instantâneo dos dados em cada tabela e vista que está registada. Se optar por incluir pré-visualizações durante o registo, a ferramenta de registo inclui até 20 registos de cada tabela e vista. Este instantâneo é, em seguida, copiado para o catálogo, juntamente com os metadados estruturais e descritivo.

> [!NOTE]
> Tabelas vasta com um grande número de colunas podem ter menos de 20 registos incluídos no respetivo pré-visualização.
>
>

## <a name="include-data-profiles"></a>Incluem perfis de dados
Tal como pré-visualizações incluindo podem fornecer contexto valioso para os utilizadores que pesquisar origens de dados no catálogo de dados, incluindo um perfil de dados pode tornar mais fácil de compreender as origens de dados detetados.

Ao utilizar a ferramenta de registo de origem de dados do catálogo de dados, pode incluir um perfil de dados para cada tabela e vista que está registada. Se optar por incluir um perfil de dados durante o registo, a ferramenta de registo incluem estatísticas agregadas sobre os dados em cada tabela e vista, incluindo:

* O número de linhas e tamanho dos dados no objeto.
* A data para a atualização mais recente dos dados e o esquema de objeto.
* O número de registos nulo e valores distintos para colunas.
* Os valores mínimo, máximo, média e desvio-padrão para colunas.

Estas estatísticas, em seguida, são copiadas para o catálogo, juntamente com os metadados estruturais e descritivo.

> [!NOTE]
> Colunas de texto e a data não incluem estatísticas média ou desvio-padrão no respetivo perfil de dados.
>
>

## <a name="update-registrations"></a>Atualizar registos
Registar uma origem de dados torna detetável no catálogo de dados quando utiliza os metadados e pré-visualização opcional extraídos durante o registo. Se a origem de dados tem de ser atualizada no catálogo (por exemplo, se tiver alterado o esquema de um objeto, tabelas originalmente excluídas devem ser incluídas ou que pretende atualizar os dados que estão incluídos nas pré-visualizações), a ferramenta de registo da origem de dados pode ser executada novamente.

Volte a registar uma origem de dados já registado efetua uma operação de intercalação de "upsert": objetos existentes são atualizados e são criados novos objetos. Quaisquer metadados fornecidos pelos utilizadores através do portal do catálogo de dados são retidos.

## <a name="summary"></a>Resumo
Porque copia metadados estruturais e descritivo da origem de dados para o serviço de catálogo, registar a origem de dados no catálogo de dados faz com que os dados mais fáceis de detetar e compreender. Depois de ter registado a origem de dados, pode anotar, gerir e deteção utilizando o portal do catálogo de dados.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como registar origens de dados, consulte o [introdução ao catálogo de dados do Azure](data-catalog-get-started.md) tutorial.
