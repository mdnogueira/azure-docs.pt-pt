---
title: "Resolução de problemas de Shell de nuvem do Azure | Microsoft Docs"
description: "Resolução de problemas de Shell de nuvem do Azure"
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: damaerte
ms.openlocfilehash: 59aec1cb6433e90caf683b4bce98c92e9f79fd18
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="troubleshooting-azure-cloud-shell"></a>Resolução de problemas de Shell de nuvem do Azure

Conhecidos resoluções para problemas na Shell de nuvem do Azure incluem:

## <a name="general-resolutions"></a>Resoluções gerais

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Diálogo de armazenamento - erro: RequestDisallowedByPolicy 403
- **Detalhes**: ao criar uma conta de armazenamento através da Shell de nuvem, é sem êxito devido a uma política do Azure pelo seu administrador. Mensagem de erro irá incluir:`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Resolução**: contacte o administrador para remover ou atualizar a política do Azure negar a criação de armazenamento do Azure.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Diálogo de armazenamento - erro: DisallowedOperation 400
 - **Detalhes**: ao utilizar uma subscrição do Azure Active Directory, não é possível criar o armazenamento.
 - **Resolução**: utilizar uma subscrição do Azure com capacidade de criação de recursos de armazenamento. Subscrições do Azure AD não são possível criar recursos do Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal de saída - erro: Falha ao ligar o terminal: não é possível estabelecer o websocket. Prima `Enter` para restabelecer a ligação.
 - **Detalhes**: nuvem Shell requer a capacidade para estabelecer uma ligação de websocket a infraestrutura de nuvem Shell.
 - **Resolução**: Verifique que configurou as definições de rede para permitir o envio pedidos de https e pedidos de websocket para domínios em *. console.azure.com.

## <a name="bash-resolutions"></a>Bash resoluções

### <a name="cannot-run-az-login"></a>Não é possível executar o início de sessão az

- **Detalhes**: executar `az login` não irá funcionar conforme já estão autenticados sob a conta utilizada para iniciar sessão no portal do Azure ou do Shell de nuvem.
- **Resolução**: utilizar a conta utilizada para iniciar ou terminar sessão e voltar com a sua conta do Azure que se destinam.

### <a name="cannot-run-the-docker-daemon"></a>Não é possível executar o daemon de docker

- **Detalhes**: um contentor para alojar o seu ambiente de shell utiliza a Shell de nuvem, como resultado a executar o daemon não é permitida.
- **Resolução**: utilizar [docker máquina](https://docs.docker.com/machine/overview/), que é instalada por predefinição, para gerir os contentores de docker de um anfitrião remoto do Docker.

## <a name="powershell-resolutions"></a>Resoluções de PowerShell

### <a name="no-home-directory-persistence"></a>No $Home persistência de diretório

- **Detalhes**: quaisquer dados essa aplicação (tais como: git, vim e outros) escreve `$Home` não é continuada entre sessões do PowerShell.
- **Resolução**: no seu perfil do PowerShell, criar uma ligação simbólica para a pasta específicos de aplicações no `clouddrive` para $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>CTRL + C não sair fora de uma linha de comandos do Cmdlet

- **Detalhes**: ao tentar sair de uma linha de comandos do Cmdlet, `Ctrl+C` não saia da linha de comandos.
- **Resolução**: para sair da linha de comandos, prima `Ctrl+C` , em seguida, `Enter`.

### <a name="gui-applications-are-not-supported"></a>Não são suportadas aplicações de GUI

- **Detalhes**: se um utilizador inicia uma aplicação de GUI, a linha não devolve. Por exemplo, quando um utilizador clones um repositório do GitHub privado que é autenticação de dois fatores ativada, é apresentada uma caixa de diálogo para concluir a autenticação de dois fatores.
- **Resolução**: `Ctrl+C` para sair do comando.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help - online não abrir a página de ajuda

- **Detalhes**: se a tipos de utilizador `Get-Help Find-Module -online`, um vê uma mensagem de erro, tais como:`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **Resolução**: Copie o url e abri-lo manualmente no seu browser.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Resolução de problemas de gestão remota de VMs do Azure

- **Detalhes**: devido às predefinições de Firewall do Windows para o WinRM, o utilizador pode ver o seguinte erro:`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Resolução**: Certifique-se a VM está em execução. Pode executar `Get-AzureRmVM -Status` para saber o estado de VM.  Em seguida, adicione uma nova regra de firewall na VM remota para permitir ligações de WinRM a partir de qualquer sub-rede, por exemplo,

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 Pode utilizar [extensão de script personalizado do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript) para evitar o início de sessão para a VM remoto para adicionar a nova regra de firewall.
 Pode guardar o script anterior para um ficheiro, diga `addfirerule.ps1`e carregá-la para o contentor de armazenamento do Azure.
 Em seguida, tente o seguinte comando:

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir`coloca em cache o resultado na unidade do Azure

- **Detalhes**: O resultado da `dir` é colocado em cache na unidade do Azure.
- **Resolução**: depois de criar ou remover um recurso na vista de unidade do Azure, execute `dir -force` para atualizar.
