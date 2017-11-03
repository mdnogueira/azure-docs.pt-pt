---
title: "Gerir recursos de dados no catálogo de dados do Azure | Microsoft Docs"
description: "O artigo realça como controlar a visibilidade e a propriedade de recursos de dados registados no catálogo de dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 623f5ed4-8da7-48f5-943a-448d0b7cba69
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 8b9159b7bc4f7b2dac12d9012c6c903e75a6ac16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Gerir recursos de dados no catálogo de dados do Azure
## <a name="introduction"></a>Introdução
Catálogo de dados do Azure foi concebido para a deteção de origem de dados, para que possa facilmente detetar e compreender as origens de dados que tem de executar uma análise e tomar decisões. Estas capacidades de deteção fazer com que o impacto maior quando o utilizador e outros utilizadores podem encontrar e compreender o intervalo mais amplas de origens de dados disponíveis. Com estes elementos em mente, o comportamento predefinido do catálogo de dados é para todas as origens de dados registados ser visível à e detetável por todos os utilizadores do catálogo.

Catálogo de dados não dá acesso aos dados propriamente ditos. Acesso de dados é controlado pelo proprietário da origem de dados. Catálogo de dados, pode detetar origens de dados e ver os metadados que está relacionada com as origens registadas no catálogo.

Poderão existir situações, no entanto, onde as origens de dados devem estar visíveis apenas a utilizadores específicos, ou a membros de grupos específicos. Tais cenários, os utilizadores podem assumir a propriedade dos recursos de dados registados no catálogo e, em seguida, controlar a visibilidade dos recursos possuem.

> [!NOTE]
> A funcionalidade descrita neste artigo está disponível apenas a edição Standard do Azure dados de catálogo. A edição gratuita não fornece capacidades de propriedade e restringir visibilidade do recurso de dados.
>
>

## <a name="manage-ownership-of-data-assets"></a>Gerir a propriedade de recursos de dados
Por predefinição, são unowned recursos de dados que são registados no catálogo de dados. Qualquer utilizador com permissão para aceder ao catálogo pode detetar e anotar estes recursos. Os utilizadores podem assumir a propriedade dos recursos de dados unowned e, em seguida, limitar a visibilidade dos recursos possuem.

Quando um recurso de dados no catálogo de dados pertence, apenas os utilizadores estão autorizados pelos proprietários podem detetar o elemento e ver os seus metadados, e apenas os proprietários podem eliminar o elemento do catálogo.

> [!NOTE]
> A propriedade no catálogo de dados afeta apenas os metadados armazenados no catálogo. Propriedade confer não as permissões na origem de dados subjacente.
>
>

### <a name="take-ownership"></a>Assumir a propriedade
Os utilizadores podem assumir a propriedade dos recursos de dados selecionando a **obter propriedade** opção no portal do catálogo de dados. Não existem permissões especiais são necessários para assumir a propriedade de um recurso de dados unowned. Qualquer utilizador pode assumir a propriedade de um recurso de dados unowned.

### <a name="add-owners-and-co-owners"></a>Adicionar os proprietários e os coproprietários
Se um recurso de dados já tem proprietário, outros utilizadores simplesmente não é possível obter a propriedade. Têm de ser adicionados como coproprietários por um proprietário existente. Qualquer proprietário pode adicionar utilizadores adicionais ou grupos de segurança, como os coproprietários.

> [!NOTE]
> É uma melhor prática de ter, pelo menos, duas pessoas como proprietários para qualquer recurso de dados pertencentes à empresa.
>
>

### <a name="remove-owners"></a>Remova os proprietários
Tal como qualquer proprietário do ativo pode adicionar coproprietários, qualquer proprietário do ativo pode remover quaisquer coproprietário.

Um proprietário do recurso que remove-lo ou herself como um proprietário já não pode gerir o elemento. Se o proprietário do ativo remove-lo ou herself como um proprietário e existem não existem outros coproprietários, o elemento é revertido para um Estado unowned.

## <a name="control-visibility"></a>Visibilidade do controlo
Os proprietários de recurso de dados podem controlar a visibilidade dos recursos de dados possuem. Para restringir a visibilidade como predefinição, onde todos os utilizadores do catálogo de dados podem detetar e ver os recursos de dados, o proprietário do ativo pode ativar/desativar a definição de visibilidade de **todas as pessoas** para **proprietários e estes utilizadores** nas propriedades para o elemento. Os proprietários, em seguida, podem adicionar utilizadores e grupos de segurança específicos.

> [!NOTE]
> Sempre que possível, permissões de propriedade e visibilidade do recurso devem ser atribuídas aos grupos de segurança e não a utilizadores individuais.
>
>

## <a name="catalog-administrators"></a>Administradores do catálogo
Os administradores do catálogo de dados são implicitamente co-proprietários de todos os recursos no catálogo. Os proprietários de recurso não é possível remover a visibilidade aos administradores e os administradores podem gerir propriedade e visibilidade para todos os recursos de dados no catálogo.

## <a name="summary"></a>Resumo
O modelo de crowdsourcing de catálogo de dados para a deteção de recurso de metadados e dados permite que todos os utilizadores de catálogo contribuir e detetar. Edição Standard do catálogo de dados foi concebido para a gestão e de propriedade para limitar a visibilidade e a utilização de recursos de dados específica.
