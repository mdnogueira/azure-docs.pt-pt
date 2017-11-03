---
title: "Expanda a transformação de JSON utilizando o Azure Machine Learning Workbench"
description: "O documento de referência para a transformação 'JSON expanda'"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 57f461f9a39e8069654985d86bfe3ae23dd0855a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="expand-json-transformation"></a>Expanda a transformação de JSON
O **expanda JSON** transformação permite aos utilizadores expandir uma coluna existente que contenha o texto JSON válido em várias colunas.

## <a name="how-to-perform-this-transformation"></a>Como efetuar esta transformação

Siga estes passos para efetuar esta transformação:
1. Selecione a coluna de origem que contém o texto JSON.
2. Selecione **expanda JSON** do **transforma** menu. Em alternativa, clique no cabeçalho da coluna de origem e selecione **expanda JSON** no menu de contexto. 
3. Clique em **OK**. 
 
Junto a coluna de origem, são adicionadas colunas novas. Estas colunas contenham propriedades do próximo nível de hierarquia no texto JSON. Propriedade chave, se estiver presente, é utilizada para criar o nome da coluna correspondente. Propriedades aninhadas são preservadas como texto JSON que o utilizador pode expandir iteratively ou rejeitar, conforme necessário.

## <a name="examples"></a>Exemplos

A origem columnn *cliente* é expandido para duas colunas *Customer.Name* e *Customer.Phone*.

| Cliente                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| {"Name": "Carrie Dodson", "Phone": "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| {"Name": "Leonard Robledo", "Phone": "456-7890-123"} | Leonard Robledo | 456-7890-123   |

