---
title: Azure Service Fabric CLI sfctl rpm | Microsoft Docs
description: "Descreve os comandos de rpm sfctl CLI de recursos de infraestrutura de serviço."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: f032af4714ad458fa6ad6fb0741f689d44f4098b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-rpm"></a>sfctl rpm
Consultar e enviar comandos para o serviço do Gestor de reparação.

## <a name="commands"></a>Comandos
|Comando|Descrição|
| --- | --- |
|    force aprovar| Força a aprovação da tarefa reparar indicado.|
|    eliminar       | Elimina uma tarefa de reparação foi concluída.|
|    lista         | Obtém uma lista de tarefas de reparação que correspondam aos filtros de indicado.|

## <a name="sfctl-rpm-delete"></a>Eliminar sfctl rpm
Elimina uma tarefa de reparação foi concluída.

Esta API suporta a plataforma de Service Fabric; não se destina a ser utilizado diretamente a partir do seu código. 

### <a name="arguments"></a>Argumentos
|Argumento|Descrição|
| --- | --- |
|    – id de tarefa [necessário]| O ID da tarefa de reparação foi concluída a eliminar.|
|    -versão           | O número da versão atual da tarefa de reparação. Se diferente de zero, o pedido apenas terá êxito se este valor corresponde à versão atual real da tarefa de reparação. Se zero, em seguida, não será efetuada nenhuma verificação da versão.|

### <a name="global-arguments"></a>Argumentos global
|Argumento|Descrição|
| --- | --- |
|    -debug             | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
|    -ajudar -h           | Mostra esta mensagem de ajuda e saída.|
|    --o de saída         | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.
|    – consulta             | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
|    -verbose           | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|


## <a name="sfctl-rpm-list"></a>lista de rpm sfctl
Obtém uma lista de tarefas de reparação que correspondam aos filtros de indicado.

Esta API suporta a plataforma de Service Fabric; não se destina a ser utilizado diretamente a partir do seu código. 

### <a name="arguments"></a>Argumentos
|Argumento|Descrição|
| --- | --- |
|    -executor filtro| O nome do executor a reparação cujas tarefas pedidas devem ser incluídas na lista.|
|    -filtro de estado   | Um bit a bit-OR dos seguintes valores, especificando que tarefas Estados deve ser incluída na lista de resultados. -1 - criado - 2 - pedida - 4 - a preparar - 8 - aprovada - 16 - em execução - 32 - restaurar - 64 - foi concluída.|
|    – o filtro de id de tarefa | Corresponder o prefixo de ID de tarefa reparar.|

### <a name="global-arguments"></a>Argumentos global
|Argumento|Descrição|
| --- | --- |
|    -debug          | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
|    -ajudar -h        | Mostra esta mensagem de ajuda e saída.|
|    --o de saída      | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição| JSON.|
|    – consulta          | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
|    -verbose        | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) a CLI de recursos de infraestrutura de serviço.
- Saiba como utilizar a CLI de recursos de infraestrutura de serviço utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).