---
title: Ligar a pilha do Azure | Microsoft Docs
description: Saiba como ligar a pilha do Azure
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
ms.date: 08/22/2017
ms.author: sngun
ms.openlocfilehash: 914f2e5d10aa341cea5eba8c24c7c37610e6b626
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-azure-stack"></a>Ligar ao Azure Stack

Para gerir recursos, tem de ligar para o Kit de desenvolvimento de pilha do Azure. Detalhes deste tópico os passos necessários para estabelecer ligação com o kit de desenvolvimento. Pode utilizar qualquer uma das seguintes opções de ligação:

* [Ambiente de trabalho remoto](#connect-with-remote-desktop): permite que um único utilizador simultâneo rapidamente ligar a partir do kit de desenvolvimento.
* [Rede privada virtual (VPN)](#connect-with-vpn): permite ligam vários utilizadores em simultâneo a partir de clientes fora da infraestrutura de pilha do Azure (necessita de configuração).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Ligar a pilha do Azure com o ambiente de trabalho remoto
Com uma ligação de ambiente de trabalho remoto, um único utilizador simultâneo pode trabalhar com o portal para gerir os recursos.

1. Abrir uma ligação de ambiente de trabalho remoto e estabelecer ligação com o kit de desenvolvimento. Introduza **AzureStack\AzureStackAdmin** como o nome de utilizador e a palavra-passe administrativa que indicou durante a configuração de pilha do Azure.  

2. No computador do kit de desenvolvimento, abra o Gestor de servidor, clique em **servidor Local**, desative a segurança avançada do Internet Explorer e, em seguida, feche o Gestor de servidor.

3. Para abrir o portal, navegue para (https://portal.local.azurestack.external/) e inicie sessão com credenciais de utilizador.


## <a name="connect-to-azure-stack-with-vpn"></a>Ligar a pilha do Azure com VPN

Pode estabelecer um ligação de rede privada Virtual (VPN) para um Kit de desenvolvimento de pilha do Azure de túnel dividido. Através da ligação VPN, pode aceder ao portal de administrador, o portal de utilizador e instalado localmente ferramentas como o Visual Studio e do PowerShell para gerir os recursos de pilha do Azure. Conectividade VPN é suportada em ambos os Directory(AAD) Active Directory do Azure e o Active Directory Federação Services(AD FS) implementações baseadas em. Ligações VPN ativar vários clientes ligar a pilha do Azure ao mesmo tempo. 

> [!NOTE] 
> Esta ligação VPN não fornece conetividade à infraestrutura do Azure pilha VMs. 

### <a name="prerequisites"></a>Pré-requisitos

* Instalar [pilha do Azure compatível Azure PowerShell](azure-stack-powershell-install.md) no seu computador local.  
* Transferir o [ferramentas necessárias para trabalhar com a pilha de Azure](azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Configurar a conectividade da VPN

Para criar uma ligação VPN para o kit de desenvolvimento, abra uma sessão do PowerShell elevada do computador baseado em Windows local e execute o seguinte script (Certifique-se de que atualiza o os valores de endereço e a palavra-passe IP para o seu ambiente):

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Se o conjunto de cópias de segurança é concluída com êxito, deverá ver **azurestack** na sua lista de ligações VPN.

![Ligações de rede](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Ligar ao Azure Stack

Ligue à instância do Azure pilha utilizando um dos seguintes dois métodos:  

* Utilizando o `Connect-AzsVpn ` comando: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quando lhe for pedido, o anfitrião de pilha do Azure de confiança e instalar o certificado do **AzureStackCertificateAuthority** no arquivo de certificados do computador local. (a linha pode aparecer atrás de janela de sessão do PowerShell). 

* Abra o seu computador local **as definições de rede** > **VPN** > clique **azurestack** > **ligar**. Na linha de início de sessão, introduza o nome de utilizador (AzureStack\AzureStackAdmin) e a palavra-passe.

### <a name="test-the-vpn-connectivity"></a>Testar a conectividade VPN

Para testar a ligação de portal, abra um browser da Internet e navegue para o portal de utilizador (https://portal.local.azurestack.external/), inicie sessão e criar recursos.  

## <a name="next-steps"></a>Passos seguintes



