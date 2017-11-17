---
title: "PowerShell em funcionalidades de Shell de nuvem do Azure (pré-visualização) | Microsoft Docs"
description: "Descrição geral das funcionalidades do PowerShell na Shell de nuvem do Azure"
services: Azure
documentationcenter: 
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: damaerte
ms.openlocfilehash: e45d66eff4be4918ffd6e7940bbfb5adb4c88c00
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell-preview"></a>Funcionalidades e ferramentas para o PowerShell na Shell de nuvem do Azure (pré-visualização)

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> As funcionalidades e ferramentas para [Bash](features.md) também está disponível.

PowerShell na Shell de nuvem (pré-visualização) é executado no `Windows Server 2016`.

## <a name="features"></a>Funcionalidades

### <a name="secure-automatic-authentication"></a>Autenticação automática segura

PowerShell na Shell de nuvem (pré-visualização) automaticamente e em segurança autentica o acesso de conta para o Azure PowerShell.

### <a name="files-persistence-across-sessions"></a>Ficheiros persistência entre sessões

Para manter os ficheiros entre sessões, Shell da nuvem explica como anexar uma partilha de ficheiros do Azure na primeira execução.
Depois de concluída, Shell da nuvem ligará automaticamente o armazenamento (montado como `$home\clouddrive`) para sessões de todas as futuras.
Uma vez que cada pedido para a nuvem Shell é alocar uma máquina temporária, ficheiros fora do seu `$home\clouddrive` e o estado da máquina não são mantidas entre sessões.

[Saiba mais sobre a ligação de partilhas de ficheiros do Azure para a Shell de nuvem.](persisting-shell-storage-powershell.md)

### <a name="azure-drive-azure"></a>Unidade do Azure (Azure:)

PowerShell na Shell de nuvem (pré-visualização) é iniciado o na unidade do Azure (`Azure:`).
Unidade do Azure permite a deteção de fácil e navegação de recursos do Azure, tais como a computação, rede, etc. armazenamento semelhantes a navegação de sistema de ficheiros.
Pode continuar a utilizar o familiar [cmdlets Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure) gerir estes recursos.
Todas as alterações efetuadas aos recursos do Azure, o efetuadas diretamente no portal do Azure ou através de cmdlets do PowerShell do Azure, de forma instantânea são refletidas na unidade do Azure.

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>Deteção contextual

- **Âmbito do grupo de recursos**: quando dentro do contexto de um caminho de grupo de recursos na unidade do Azure (`Azure:`), o nome do grupo de recursos automaticamente foi transmitido aos cmdlets do PowerShell do Azure.

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand**: Este cmdlet devolve a lista de comandos aplicáveis no contexto da localização na unidade do Azure (`Azure:`). Por exemplo, mostra apenas os comandos relacionados com o armazenamento quando o utilizador está em`Azure:\<subscription name>\StorageAccounts`

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>Editar script de PowerShell avançada

Quando utiliza VIM editar ficheiros do PowerShell (`.ps1,.psm1,.psd1`), obtém automaticamente realce da sintaxe e o suporte IntelliSense.
Suporte IntelliSense é implementado através de um vim-Plug-in que interage com uma instância local do [PowerShell Editor serviços](https://github.com/PowerShell/PowerShellEditorServices).

> [!TIP]
> Utilize `TAB` obter conclusão (IntelliSense) em nomes de comando, os nomes dos parâmetros e valores de parâmetro (quando aplicável).

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>Modelo extensível

Utilizar [PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget), pode instalar (e atualizar) com facilmente módulos personalizados e scripts a partir de [galeria do PowerShell](https://www.powershellgallery.com).
Após a instalação, os módulos automaticamente são mantidos entre sessões do Shell de nuvem.

> [!TIP]
> Módulos instalados pelos utilizadores são guardados no `$Home\CloudDrive\.pscloudshell\WindowsPowerShell` pasta. Uma ligação simbólica para esta pasta é criada na pasta de documentos do utilizador (`$home\Documents\WindowsPowerShell`).

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>Gestão das VMs de convidado

Utilizar dois comandos incorporados - `Enter-AzureRmVM` e `Invoke-AzureRmVMCommand`, pode gerir remotamente as suas VMs do Azure.
Estes comandos assentes comunicação remota do PowerShell e necessitam de conectividade do PowerShell para as VMs do Azure.

## <a name="tools"></a>Ferramentas

|**Categoria**    |**Nome**                                 |
|----------------|-----------------------------------------|
|Ferramentas do Azure     |[O Azure PowerShell (5.0.1)](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-5.0.0)<br> [CLI do Azure (2.0.21)](https://docs.microsoft.com/en-us/cli/azure/overview)|
|Editores de texto    |VIM<br> Nano                             |
|Gestor de pacotes |PowerShellGet<br> PackageManagement<br> npm<br> PIP |
|Controlo de código fonte  |Git                                      |
|Bases de Dados       |[Módulo de SqlServer](https://www.powershellgallery.com/packages/SqlServer)<br> [SQLCMD utilitário](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Ferramentas de teste      |Pester                                   |

## <a name="language-support"></a>Suporte de idiomas

|**Idioma**|**Versão**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |6.10       |
|PowerShell  |5.1 e [6.0 (beta)](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>Passos seguintes

[Início rápido com o PowerShell na Shell de nuvem (pré-visualização)](quickstart-powershell.md)

[Saiba mais sobre o Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
