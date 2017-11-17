---
title: "Idiomas suportados das funções do Azure"
description: "Conheça os idiomas suportados (GA) e que são experimental ou em pré-visualização."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: tdykstra
ms.openlocfilehash: 5786a206b258cfe7c48f52ead9b5a4cceb64cd5f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="supported-languages-in-azure-functions"></a>Idiomas suportados das funções do Azure

Este artigo explica que os níveis de suporte é fornecido para idiomas que pode utilizar com as funções do Azure.

## <a name="levels-of-support"></a>Níveis de suporte

Existem três níveis de suporte:

* **Geralmente disponível (GA)** - totalmente suportadas e aprovadas para utilização em produção.
* **Pré-visualização** - ainda não suportado, mas é esperado para alcançar o estado de GA no futuro.
* **Experimental** - não suportada e pode ser abandonada no futuro; nenhuma garantia de pré-visualização eventual ou estado GA.

## <a name="languages-in-runtime-1x-and-2x"></a>Idiomas no tempo de execução 1. x e 2. x

[Duas versões do tempo de execução das funções do Azure](functions-versions.md) estão disponíveis. O tempo de execução de 1. x é depois da disponibilidade geral É o tempo de execução apenas for aprovado para aplicações de produção. O tempo de execução de 2. x está atualmente em pré-visualização, pelo que os idiomas que suporta estão em pré-visualização. A tabela seguinte mostra os idiomas são suportados em cada versão do tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Idiomas experimental

Os idiomas experimental em 1. x não escala bem e não suportam a todos os enlaces. Por exemplo, Python é lenta porque o tempo de execução de funções é executado *python.exe* com cada invocação de função. E embora Python suporte Enlaces de HTTP, não é possível aceder ao objeto de pedido.

Experimental suporte para o PowerShell está limitado a versão 4.0, uma vez que é o que é instalado em VMs que executam aplicações de função no. Se pretender executar scripts do PowerShell, considere [da automatização do Azure](https://azure.microsoft.com/services/automation/).

O tempo de execução de 2. x não suporta idiomas experimental. 2. x, iremos irá adicionar suporte para um idioma apenas quando se dimensiona bem e suporta avançadas de acionadores.

Se pretender utilizar um dos idiomas que só estão disponíveis em 1. x, permaneça o tempo de execução de 1. x. Mas não utilizar idiomas experimental para tudo o que pode depender, como não são suportadas para os mesmos oficial. Pode pedir ajuda ao [criar problemas de GitHub](https://github.com/Azure/azure-webjobs-sdk-script/issues), mas não devem ser abertos casos de suporte para problemas com idiomas experimental. 

### <a name="language-extensibility"></a>Extensibilidade de idioma

O tempo de execução de 2. x foi concebido para oferecer [extensibilidade de idioma](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Entre os idiomas primeiro a ser baseada neste extensibilidade modelo é Java, que se encontra na pré-visualização em 2. x.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre como utilizar um dos idiomas GA ou preview das funções do Azure, consulte os seguintes recursos:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)