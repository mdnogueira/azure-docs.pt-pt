---
title: Ligar a pilha do Azure | Microsoft Docs
description: Saiba como ligar a pilha do Azure.
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: ba2359739b1d9cd265879227a499c94f93d8e4c6
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="connect-to-azure-stack"></a>Ligar ao Azure Stack

*Aplica-se a: Azure da pilha Kit de desenvolvimento*

Para gerir recursos, tem de ligar pela primeira vez para o Kit de desenvolvimento de pilha do Azure. Neste artigo, vamos descrever os passos que efetuar para estabelecer ligação com o kit de desenvolvimento. Pode utilizar uma das seguintes opções de ligação:

* [Ligação de ambiente de trabalho remota](#connect-with-remote-desktop). Quando ligar utilizando a ligação de ambiente de trabalho remoto, um único utilizador pode ligar-se rapidamente do kit de desenvolvimento.
* [Rede privada virtual (VPN)](#connect-with-vpn). Quando se liga através da utilização de uma VPN, vários utilizadores em simultâneo podem ligar a partir de clientes fora da infraestrutura de pilha do Azure (requer configuração).

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Ligar a pilha do Azure utilizando a ligação ao ambiente de trabalho remoto
Um único utilizador simultâneo pode gerir os recursos no portal do operador ou o portal de utilizador através da ligação de ambiente de trabalho remoto.

1. Abrir as ligações de ambiente de trabalho remoto e estabelecer ligação com o kit de desenvolvimento. Para o nome de utilizador, introduza **AzureStack\AzureStackAdmin**. Utilize a palavra-passe de operador que especificou quando configurar a pilha do Azure.  

2. No computador do kit de desenvolvimento, abra o Gestor de servidor. Selecione **servidor Local**, desmarque a **segurança avançada do Internet Explorer** caixa de verificação e, em seguida, feche o Gestor de servidor.

3. Para abrir o [portal de utilizador](azure-stack-key-features.md#portal), aceda a https://portal.local.azurestack.external/. Inicie sessão com as credenciais do utilizador. Para abrir a pilha de Azure [portal operador](azure-stack-key-features.md#portal), aceda a https://adminportal.local.azurestack.external/. Inicie sessão com as credenciais do Azure Active Directory (Azure AD) que especificou durante a instalação.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Ligar a pilha do Azure através da VPN

Pode estabelecer um túnel de divisão ligação VPN para um Kit de desenvolvimento de pilha do Azure. Pode utilizar uma ligação VPN para aceder ao portal de operador de pilha do Azure, o portal de utilizador e as ferramentas instaladas localmente, como o Visual Studio e do PowerShell para gerir os recursos de pilha do Azure. Conectividade VPN é suportada no Azure AD e em implementações de serviços de Federação do Active Directory (AD FS). Ligações VPN possibilitam para múltiplos clientes ligar a pilha do Azure ao mesmo tempo. 

> [!NOTE] 
> Uma ligação VPN não fornece conetividade à infraestrutura do Azure pilha VMs. 

### <a name="prerequisites"></a>Pré-requisitos

1. Instalar [Azure pilha compatível com o Azure PowerShell](azure-stack-powershell-install.md) no seu computador local.  
2. Transferir o [ferramentas necessárias para trabalhar com a pilha de Azure](azure-stack-powershell-download.md). 

### <a name="set-up-vpn-connectivity"></a>Configurar a conectividade VPN

Para criar uma ligação VPN para o kit de desenvolvimento, abra o Windows PowerShell como administrador no computador local baseados em Windows. Em seguida, execute o seguinte script (o endereço IP e a palavra-passe os valores para o seu ambiente de atualização):

```PowerShell 
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Se o programa de configuração for bem sucedida, **azurestack** aparece na lista de ligações VPN.

![Ligações de rede](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Ligar ao Azure Stack

Ligar à instância de pilha do Azure, utilizando um dos seguintes métodos:  

* Utilize o `Connect-AzsVpn ` comando: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quando lhe for pedido, o anfitrião de pilha do Azure de confiança e instalar o certificado do **AzureStackCertificateAuthority** no arquivo de certificados do computador local. (A linha de comandos. o poderá estar ocultada pela janela do PowerShell.). 

* No computador local, selecione **as definições de rede** > **VPN** > **azurestack** > **ligar**. Na linha de início de sessão, introduza o nome de utilizador (**AzureStack\AzureStackAdmin**) e a palavra-passe.

### <a name="test-vpn-connectivity"></a>Testar a conectividade VPN

Para testar a ligação de portal, abra um browser e, em seguida, avance para o portal de utilizador (https://portal.local.azurestack.external/) ou o portal de operador (https://adminportal.local.azurestack.external/). Inicie sessão e criar recursos.  

## <a name="next-steps"></a>Passos seguintes

[Máquinas virtuais disponibilizar aos utilizadores de pilha do Azure](azure-stack-tutorial-tenant-vm.md)

