---
title: "Descrição geral do Azure a Shell de nuvem (pré-visualização) | Microsoft Docs"
description: "Descrição geral da Shell em nuvem do Azure."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 44d2ac2fd35621ab8cd8d7584744139ee3bab5c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-cloud-shell-preview"></a>Descrição geral da Shell de nuvem do Azure (pré-visualização)
Shell de nuvem do Azure é uma shell interativa e acessíveis para o browser para a gestão de recursos do Azure.
Proporciona a flexibilidade de escolher a experiência de shell que melhor se adapta da forma que trabalha.
Os utilizadores de Linux podem optar por uma experiência de Bash, ao passo que os utilizadores de Windows podem escolher o PowerShell.

![Bash na Shell de nuvem](media/overview/overview-bash-pic.png)

![PowerShell na Shell de nuvem](media/overview/overview-ps-pic.png)

## <a name="features"></a>Funcionalidades
### <a name="browser-based-shell-experience"></a>Experiência de shell baseada no browser
Shell de nuvem permite o acesso a uma experiência da linha de comandos baseada no browser criada com as tarefas de gestão do Azure em mente.
Pode fornecer a Shell de nuvem tire partido untethered a partir de uma máquina local de trabalho de forma apenas a nuvem.

### <a name="choice-of-preferred-shell-experience"></a>Opção de experiência de shell preferencial
O Azure Cloud Shell dá-lhe a flexibilidade de escolher a experiência de shell que mais de adequada ao seu estilo de trabalho.
Os utilizadores de Linux podem optar por uma experiência de Bash, ao passo que os utilizadores de Windows podem escolher o PowerShell.

### <a name="pre-configured-azure-workstation"></a>Pré-configurada estação de trabalho do Azure
Shell de nuvem fica pré-instaladas com ferramentas de linha de comandos populares e suporte de idiomas para que possa trabalhar mais rapidamente.

Ver a lista completa de ferramentas para [Bash experiência](features.md#tools) e [experiência com o PowerShell.](features-powershell.md#tools)

### <a name="automatic-authentication"></a>Autenticação automática
Shell de nuvem segura autentica automaticamente em cada sessão para instantânea acesso aos recursos através de 2.0 do CLI do Azure.

### <a name="connect-your-azure-file-storage"></a>Ligar o File storage do Azure
Nuvem Shell máquinas são temporárias e como consequência necessitam de uma partilha de ficheiros do Azure para possível montá-la como `clouddrive` para manter o seu diretório $Home.
Na primeira execução que nuvem Shell pede-lhe criar um recurso de grupo, a conta de armazenamento e o ficheiro partilham em seu nome. Este é um passo única e será anexado automaticamente todas as sessões. Uma partilha de ficheiros única pode ser mapeada e será utilizada pelo Bash e do PowerShell na Shell de nuvem.

#### <a name="create-new-storage"></a>Criar o novo armazenamento
![](media/overview/basic-storage.png)

Uma conta de armazenamento localmente redundante (LRS) e a partilha de ficheiros do Azure podem ser criados em seu nome. Se optar por utilizar ambas a partilha de ficheiros do Azure será utilizada para ambientes de Bash e PowerShell. Aplicam os custos de armazenamento normal.

Três recursos serão criados em seu nome:
1. Grupo de recursos com o nome:`cloud-shell-storage-<region>`
2. Conta de armazenamento com o nome:`cs<uniqueGuid>`
3. Partilha de ficheiros com o nome:`cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash na Shell de nuvem também cria uma imagem de disco de 5 GB predefinido para manter `$Home`. Todos os ficheiros no seu diretório $Home tal como chaves SSH são mantidos da imagem de disco do utilizador armazenada numa partilha de ficheiro instalado. Aplique as melhores práticas ao guardar os ficheiros no seu diretório $Home e a partilha de ficheiros montada.

#### <a name="use-existing-resources"></a>Utilizar recursos existentes
![](media/overview/advanced-storage.png)

É fornecida uma opção avançada para associar os recursos existentes para a Shell de nuvem.
Na linha de comandos do programa de configuração da armazenamento, clique em "Definições avançadas de mostrar" para mostrar as opções adicionais.
As DropDowns são filtradas para a sua região de nuvem Shell atribuído e contas/global-armazenamento localmente redundante.

[Saiba mais sobre armazenamento de nuvem Shell, ao atualizar as partilhas de ficheiros e a carregar/transferência de ficheiros.](persisting-shell-storage.md)

## <a name="concepts"></a>Conceitos
* Nuvem Shell é executado numa máquina temporária fornecida por-sessão, por utilizador
* Shell de nuvem exceder o tempo limite após 20 minutos sem atividade interativa
* Shell de nuvem só pode ser acedido com uma partilha de ficheiros anexada
* Na nuvem utiliza Shell uma partilha de ficheiros mesmo para Bash e PowerShell
* Shell de nuvem é atribuído uma máquina por conta de utilizador
* As permissões estão definidas como um utilizador de Linux normal (Bash)

Saiba mais sobre as funcionalidades no [Bash na Shell de nuvem](features.md) e [PowerShell na nuvem Shell](features-powershell.md).

## <a name="examples"></a>Exemplos
* Utilize scripts para automatizar tarefas de gestão do Azure
* Gerir recursos do Azure através do portal do Azure e ferramentas de linha de comandos do Azure em simultâneo
* Test-Drive os cmdlets do Azure CLI 2.0 ou o Azure PowerShell

Experimente estes exemplos de inícios rápidos para [Bash na Shell de nuvem](quickstart.md) e [PowerShell na nuvem Shell](quickstart-powershell.md).

## <a name="pricing"></a>Preços
O computador que aloja a Shell de nuvem é gratuito, com um pré-requisito de uma partilha de ficheiros do Azure montada. Aplicam os custos de armazenamento normal.

## <a name="supported-browsers"></a>Browsers suportados
Shell de nuvem é recomendada para Chrome, o limite e o Safari.
Enquanto a Shell de nuvem é suportada para Chrome, Firefox, Safari, IE e Edge, Shell da nuvem está sujeita às definições de browser específico.