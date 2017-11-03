---
title: Azure Service Fabric CLI sfctl arquivo | Microsoft Docs
description: "Descreve os comandos de arquivo de sfctl CLI de recursos de infraestrutura de serviço."
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
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 2af6dff4ffcdf295731f2d61b5f9e35af40615e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-store"></a>arquivo de sfctl
Efetue operações de nível de ficheiro básico no arquivo de imagens de cluster.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
|    eliminar| Elimina existente imagem armazena conteúdo.|
|    informações de raiz| Obtém as informações dos conteúdos na raiz do arquivo de imagens.|
|    STAT  | Obtém as informações de conteúdo de arquivo de imagem.|


## <a name="sfctl-store-delete"></a>eliminação de arquivo sfctl
Elimina existente imagem armazena conteúdo.

Elimina existente que está a ser encontrado a imagem de determinado arquivo de conteúdo da imagem armazena caminho relativo. Este comando pode ser utilizado para eliminar pacotes de aplicações carregado depois de terem sido aprovisionados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -caminho de conteúdo [necessário]| Caminho relativo ao ficheiro ou pasta no arquivo de imagem de raiz.|
| tempo limite – -t          | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug               | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h             | Mostra esta mensagem de ajuda e saída.|
| --o de saída           | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta               | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose             | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-store-stat"></a>sfctl arquivo stat
Obtém as informações de conteúdo de arquivo de imagem.

Devolve as informações sobre o conteúdo da loja de imagem no contentPath especificado relativamente à raiz do arquivo de imagens.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -caminho de conteúdo [necessário]| Caminho relativo ao ficheiro ou pasta no arquivo de imagem de raiz.|
| tempo limite – -t          | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug               | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h             | Mostra esta mensagem de ajuda e saída.|
| --o de saída           | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta               | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose             | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="next-steps"></a>Passos seguintes
- [A configuração](service-fabric-cli.md) os recursos de infraestrutura do serviço CLI.
- Saiba como utilizar a CLI de recursos de infraestrutura de serviço utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).