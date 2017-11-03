---
title: "Criar artefactos personalizados para a máquina virtual de DevTest Labs | Microsoft Docs"
description: Saiba como criar os seus artefactos para utilizar com o Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 679819618452d65847c6163569e04945ba8a414d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Criar artefactos personalizados para a máquina virtual de DevTest Labs

Veja o seguinte vídeo para uma descrição geral dos passos descritos neste artigo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
> 
> 

## <a name="overview"></a>Descrição geral
Pode utilizar *artefactos* para implementar e configurar a aplicação depois de aprovisionar uma VM. Um artefacto é composta por um ficheiro de definição de artefacto e outros ficheiros de script são armazenados numa pasta num repositório de Git. Ficheiros de definição de artefacto é composto por JSON e expressões que pode utilizar para especificar que pretende instalar numa VM. Por exemplo, pode definir o nome dos parâmetros que estão disponíveis quando o comando é executado, a execução de comando e um artefacto. Pode consultar outros ficheiros de script do ficheiro de definição de artefacto por nome.

## <a name="artifact-definition-file-format"></a>Formato de ficheiro de definição de artefactos
O exemplo seguinte mostra as secções que compõem a estrutura básica de um ficheiro de definição:

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| Nome do elemento | Necessário? | Descrição |
| --- | --- | --- |
| $schema |Não |Localização do ficheiro de esquema JSON. O ficheiro de esquema JSON pode ajudar a testar a validade do ficheiro de definição. |
| Título |Sim |Nome do artefacto apresentado no laboratório. |
| descrição |Sim |Descrição do artefacto apresentado no laboratório. |
| iconUri |Não |URI do ícone apresentado no laboratório. |
| targetOsType |Sim |Sistema operativo da VM onde está instalado o artefacto. As opções suportadas são o Windows e Linux. |
| parâmetros |Não |Valores que são fornecidos quando o comando de instalação do artefacto é executado numa máquina. Isto ajuda a personalizar o artefacto. |
| runCommand |Sim |Comando que é executado numa VM de instalação de artefactos. |

### <a name="artifact-parameters"></a>Parâmetros de artefactos
Na secção de parâmetros do ficheiro de definição, especifique os valores um utilizador pode introduzir a instalação de um artefacto. Pode consultar estes valores no comando de instalação de artefactos.

Para definir parâmetros, utilize a seguinte estrutura:

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| Nome do elemento | Necessário? | Descrição |
| --- | --- | --- |
| tipo |Sim |Tipo de valor de parâmetro. Consulte a lista seguinte para os tipos permitidos. |
| displayName |Sim |Nome do parâmetro que é apresentado a um utilizador no laboratório. | |
| descrição |Sim |Descrição do parâmetro que é apresentado no laboratório. |

Os tipos permitidos são:

* cadeia (qualquer cadeia JSON válida)
* Int (qualquer JSON número inteiro válido)
* booleana (qualquer JSON booleanos válidos)
* matriz (nenhuma matriz JSON válido)

## <a name="artifact-expressions-and-functions"></a>Funções e expressões de artefactos
Pode utilizar expressões e o comando de instalação de funções para construir o artefacto.
As expressões são colocadas entre parênteses Retos ([e]) e são avaliadas quando o artefacto está instalado. As expressões podem aparecer em qualquer local, um valor de cadeia JSON. As expressões sempre devolvem um valor JSON diferente. Se precisar de utilizar uma cadeia literal, que começa com um parêntese ([]), tem de utilizar dois parênteses Retos ([[).
Normalmente, utilizar expressões com as funções para construir um valor. Tal como em JavaScript, chamadas de função estejam formatadas como **functionName(arg1,arg2,arg3)**.

A lista seguinte mostra as funções comuns:

* **Parameters(parameterName)**: devolve um valor de parâmetro que é fornecido quando é executado o comando de artefactos.
* **concat (arg1 arg2, arg3,...)** : Combina vários valores de cadeia. Esta função pode demorar uma variedade de argumentos.

O exemplo seguinte mostra como utilizar expressões e as funções para construir um valor:

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Criar um artefacto personalizado

1. Instale um editor de JSON. Precisa de um editor de JSON para trabalhar com ficheiros de definição de artefactos. Recomendamos que utilize [Visual Studio Code](https://code.visualstudio.com/), que está disponível para Windows, Linux e OS X.
2. Obter um ficheiro de definição de artifactfile.json de exemplo. Veja os artefactos criados pela equipa do DevTest Labs no nosso [repositório do GitHub](https://github.com/Azure/azure-devtestlab). Criámos uma biblioteca avançada de artefactos que podem ajudar a criar os seus artefactos. Transferir um ficheiro de definição de artefacto e alterá-la para criar os seus artefactos.
3. Se utiliza o IntelliSense. Utilize o IntelliSense para ver os elementos válidos que pode utilizar para construir um ficheiro de definição de artefactos. Também pode ver as diferentes opções para os valores de um elemento. Por exemplo, quando editar o **targetOsType** elemento, o IntelliSense mostra-lhe duas opções, para Windows ou Linux.
4. Armazenar artefactos num [repositório de Git](devtest-lab-add-artifact-repo.md).
   
   1. Crie um diretório separado para cada artefactos. O nome do diretório deve ser igual ao nome de artefactos.
   2. Armazene o ficheiro de definição de artefacto (artifactfile.json) no diretório que criou.
   3. Armazene os scripts que são referenciados do comando de instalação de artefactos.
      
      Eis um exemplo de como pode ver uma pasta de artefacto:
      
      ![Exemplo de pasta de artefactos](./media/devtest-lab-artifact-author/git-repo.png)
5. Adicione o repositório de artefactos para o laboratório. Consulte [adicione um repositório de Git para artefactos e modelos](devtest-lab-add-artifact-repo.md).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Como diagnosticar falhas de artefacto no DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Associar uma VM a um domínio do Active Directory existente, utilizando um modelo do Resource Manager no DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Passos seguintes
* Saiba como [adicione um repositório de artefactos de Git para um laboratório](devtest-lab-add-artifact-repo.md).

