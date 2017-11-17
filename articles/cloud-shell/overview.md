---
title: "Descrição geral de Shell de nuvem do Azure | Microsoft Docs"
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
ms.date: 11/16/2017
ms.author: juluk
ms.openlocfilehash: 08ab3b38e4c1fbeb1fac67c5d1b6f6749f7a0a3e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="overview-of-azure-cloud-shell"></a>Descrição geral da Shell de nuvem do Azure
Shell de nuvem do Azure é uma shell interativa e acessíveis para o browser para a gestão de recursos do Azure.
Proporciona a flexibilidade de escolher a experiência de shell que melhor se adapta da forma que trabalha.
Os utilizadores de Linux podem optar por uma experiência de Bash, ao passo que os utilizadores de Windows podem escolher o PowerShell.

Iniciar através do portal do Azure do ícone Shell de Cloud:

![Início do portal](media/overview/portal-launch-icon.png)

Tirar partido das Bash ou o PowerShell na lista pendente de Seletor de shell:

![Bash na Shell de nuvem](media/overview/overview-bash-pic.png)

![PowerShell na Shell de nuvem (pré-visualização)](media/overview/overview-ps-pic.png)

## <a name="features"></a>Funcionalidades
### <a name="browser-based-shell-experience"></a>Experiência de shell baseada no browser
Shell de nuvem permite o acesso a uma experiência da linha de comandos baseada no browser criada com as tarefas de gestão do Azure em mente.
Pode fornecer a Shell de nuvem tire partido untethered a partir de uma máquina local de trabalho de forma apenas a nuvem.

### <a name="choice-of-preferred-shell-experience"></a>Opção de experiência de shell preferencial
O Azure Cloud Shell dá-lhe a flexibilidade de escolher a experiência de shell que mais de adequada ao seu estilo de trabalho.
Os utilizadores do Linux podem optar ativamente por participar para Bash na Shell de nuvem, enquanto utilizadores do Windows podem optar por PowerShell na Shell de nuvem (pré-visualização).

### <a name="authenticated-and-configured-azure-workstation"></a>Autenticado e configurada estação de trabalho do Azure
Shell de nuvem vem gerida pela Microsoft, de modo está pré-instalado com ferramentas de linha de comandos populares e suporte de idiomas para que possa trabalhar mais rapidamente. Além disso, a Shell de nuvem segura autentica automaticamente para instantânea acesso aos recursos através dos cmdlets do Azure CLI 2.0 ou o Azure PowerShell.

Ver a lista completa de ferramentas para o [Bash experiência](features.md#tools) e [experiência com o PowerShell (pré-visualização).](features-powershell.md#tools)

### <a name="multiple-access-points"></a>Vários pontos de acesso
Para além da Shell de nuvem a ser disponível a partir do portal do Azure, pode também ser acedido de:
* [Documentação de "Experimente" 2.0 da CLI do Azure](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)
* [Aplicação móvel do Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Extensão de código do Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-azure-files-storage"></a>Ligar o armazenamento de ficheiros do Azure
Nuvem Shell máquinas são temporárias e como consequência necessitam de uma partilha de ficheiros do Azure para possível montá-la como `clouddrive` para manter o seu diretório $Home.
Na primeira execução que nuvem Shell pede-lhe criar um recurso de grupo, a conta de armazenamento e o ficheiro partilham em seu nome. Este é um passo única e será anexado automaticamente todas as sessões. Uma partilha de ficheiros única pode ser mapeada e será utilizada pelo Bash e do PowerShell na Shell de nuvem (pré-visualização).

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
* Nuvem Shell é executada num anfitrião temporário fornecido por-sessão, por utilizador
* Shell de nuvem exceder o tempo limite após 20 minutos sem atividade interativa
* Shell de nuvem requer possível montar uma partilha de ficheiros
* Shell de nuvem utiliza a mesma partilha de ficheiros para Bash e PowerShell
* Shell de nuvem é atribuído uma máquina por conta de utilizador
* As permissões estão definidas como um utilizador de Linux normal no Bash

Saiba mais sobre as funcionalidades no [Bash na Shell de nuvem](features.md) e [PowerShell na Shell de nuvem (pré-visualização)](features-powershell.md).

## <a name="examples"></a>Exemplos
* Utilize scripts para automatizar tarefas de gestão do Azure
* Gerir recursos do Azure através do portal do Azure e ferramentas de linha de comandos do Azure em simultâneo
* Test-Drive os cmdlets do Azure CLI 2.0 ou o Azure PowerShell

Experimente estes exemplos de inícios rápidos para [Bash na Shell de nuvem](quickstart.md) e [PowerShell na Shell de nuvem (pré-visualização)](quickstart-powershell.md).

## <a name="pricing"></a>Preços
O computador que aloja a Shell de nuvem é gratuito, com um pré-requisito de uma partilha de ficheiros do Azure montada. Aplicam os custos de armazenamento normal.

## <a name="next-steps"></a>Passos seguintes
[Bash no início rápido da Shell de nuvem](quickstart.md) <br>
[PowerShell no início rápido da Shell de nuvem (pré-visualização)](quickstart-powershell.md)