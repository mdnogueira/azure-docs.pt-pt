---
title: "Configurar o glossário comercial para regida etiquetagem no catálogo de dados do Azure | Microsoft Docs"
description: "Artigo procedimentos, realce o glossário comercial no catálogo de dados do Azure para definir e utilizar um vocabulário de negócio comuns à etiqueta de recursos de dados registados."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: b3d63dbe-1ae7-499f-bc46-42124e950cd6
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/15/2017
ms.author: maroche
ms.openlocfilehash: bcd2ba4f92937cf1e134b2a59d97c7bea7802145
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Configurar o glossário comercial para regida etiquetagem
## <a name="introduction"></a>Introdução
Catálogo de dados do Azure permite a deteção de origem de dados, para que possa detetar e compreender as origens de dados que precisa para executar uma análise e tomar decisões facilmente. Estas capacidades fazer com que o impacto maior quando possa encontrar e compreender o intervalo mais amplas de origens de dados disponíveis.

Marcação de uma funcionalidade do catálogo de dados que promove maior compreensão dos dados de recursos. Ao utilizar a marcação, pode associar as palavras-chave um recurso ou uma coluna, que por sua vez facilita a detetar o elemento através de pesquisa ou a procura. Marcação também ajuda mais facilmente compreender o contexto e a intenção do elemento.

No entanto, a etiquetagem, por vezes, pode causar problemas próprios. Alguns exemplos dos problemas que pode introduzir a etiquetagem são:

* A utilização de abreviaturas em alguns elementos e texto expandido no outras pessoas. Este inconsistência hinders a deteção de recursos, apesar da intenção era marcar os ativos com a mesma etiqueta.
* Possíveis variações nos significado, dependendo do contexto. Por exemplo, uma tag chamado *receitas* no cliente conjunto de dados poderá significar receitas pelo cliente, mas a mesma etiqueta um conjunto de dados de vendas trimestral poderá significar receitas trimestral da empresa.  

Para ajudar a resolver estes e outros desafios semelhantes, o catálogo de dados inclui um glossário comercial.

Ao utilizar o glossário comercial do catálogo de dados, uma organização pode documentar termos empresarial fundamentais e as respetivas definições para criar um vocabulário de negócio comuns. Este governação permite consistência na utilização de dados em toda a organização. Depois de um termo está definido no glossário comercial, podem ser atribuído a um recurso de dados no catálogo. Esta abordagem, *regida etiquetagem*, é a mesma abordagem como a etiquetagem.

## <a name="glossary-availability-and-privileges"></a>Disponibilidade de glossário e privilégios
O glossário comercial está disponível apenas a edição Standard do Azure dados de catálogo. Edição gratuita do catálogo de dados não inclui um glossário e não fornece capacidades para etiquetagem regida.

Pode aceder ao glossário comercial através de **glossário** opção no menu de navegação do portal do catálogo de dados.  

![Aceder ao glossário comercial](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Os administradores do catálogo de dados e os membros da função de administradores do glossário podem criar, editar e eliminar os termos do glossário no glossário comercial. Todos os utilizadores do catálogo de dados podem ver as definições de prazo e recursos de etiqueta com os termos do glossário.

![Adicionar um novo termo glossário](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Criar os termos do glossário
Os administradores do catálogo de dados e os administradores de glossário podem criar os termos do glossário clicando a **novo termo** botão. Cada termo glossário contém os seguintes campos:

* Uma definição de negócio para o termo
* Uma descrição que captura a utilização prevista ou as regras de negócio para o recurso ou a coluna
* Uma lista dos intervenientes que deve saber mais sobre o termo
* O termo principal, que define a hierarquia na qual está organizado o termo

## <a name="glossary-term-hierarchies"></a>Hierarquias de termo glossário
Ao utilizar o glossário comercial do catálogo de dados, uma organização possa descrever o respetivo vocabulário empresas como uma hierarquia de termos e pode criar uma classificação dos termos de licenciamento que melhor represente a taxonomia comercial.

Um termo têm de ser exclusivo de um determinado nível da hierarquia. Não são permitidos nomes duplicados. Não existe nenhum limite para o número de níveis numa hierarquia, mas uma hierarquia, muitas vezes, é compreendida mais facilmente quando existem três níveis ou menos.

A utilização de hierarquias do glossário comercial é opcional. Abandonar o fileparser o elemento principal termo campo vazio para os termos do glossário cria uma lista de plana (não hierárquica) dos termos do glossário.  

## <a name="tagging-assets-with-glossary-terms"></a>Marcação de recursos com os termos do glossário
Depois dos termos do glossário terem sido definidos no catálogo, a experiência de etiquetagem ativos está otimizada para o glossário de pesquisa, como uma etiqueta de tipos de um utilizador. O portal do catálogo de dados apresenta uma lista dos termos de glossário correspondente à sua escolha. Se o utilizador seleciona um termo Glossário de na lista, o termo é adicionado ao elemento de como uma etiqueta (também denominada uma etiqueta do glossário). O utilizador também pode optar por criar uma nova etiqueta, escrevendo um termo que não está a ser o glossário (também denominada uma etiqueta de utilizador).

![Recurso de dados marcados com etiqueta de um utilizador e duas etiquetas glossário](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Etiquetas de utilizador são o único tipo de tag suportado na edição gratuita do catálogo de dados.
>
>

### <a name="hover-behavior-on-tags"></a>Coloque o cursor comportamento nas etiquetas
No portal do catálogo de dados, os dois tipos de etiquetas são comportamentos visualmente distinto e presente Paire diferentes. Quando paira o rato sobre uma etiqueta de utilizador, pode ver o texto da etiqueta e o utilizador ou utilizadores que adicionaram a etiqueta. Quando paira o rato sobre uma etiqueta do glossário, também pode ver a definição de termo o glossário e uma hiperligação para abrir o glossário comercial para ver a definição do período da completa.

### <a name="search-filters-for-tags"></a>Filtros de pesquisa para etiquetas
Etiquetas de glossário etiquetas de utilizador e são pesquisáveis e pode aplicá-las como filtros numa pesquisa.

## <a name="summary"></a>Resumo
Ao utilizar o glossário comercial no catálogo de dados do Azure e a etiquetagem governed permite, pode identificar, gerir e detetar recursos de dados de uma forma consistente. O glossário comercial pode promover learning de vocabulário o negócio por membros da organização. O glossário também suporta a captura de metadados significativo, o que simplifica a deteção de recurso e a compreensão.

## <a name="next-steps"></a>Passos seguintes
* [Documentação da REST API para operações de glossário de negócio](https://msdn.microsoft.com/library/mt708855.aspx)
