---
title: Utilize um MSI de VM do Windows para aceder ao Cofre de chaves do Azure
description: "Um tutorial que explica o processo de utilizar um Windows VM geridos serviço de identidade (MSI) para aceder ao Cofre de chaves do Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: bryanla
ms.openlocfilehash: f1f51f59a86c7815b5a18aa84dfb315345debcd9
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Utilizar um Windows VM geridos serviço de identidade (MSI) para aceder ao Cofre de chaves do Azure 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como ativar a identidade de serviço geridas (MSI) para uma Máquina Virtual do Windows, em seguida, utilizar essa identidade para aceder ao Cofre de chaves do Azure. A funcionar como um arranque, Cofre de chaves torna possível para a sua aplicação de cliente, em seguida, utilizar o segredo para aceder a recursos não estão protegidos pelo Azure Active Directory (AD). Identidades de serviço geridas são automaticamente geridas pelo Azure e permitem-lhe autenticar para serviços que suportam a autenticação do Azure AD, sem necessidade de introduzir as credenciais para o seu código. 

Saiba como:


> [!div class="checklist"]
> * Ativar a identidade de serviço geridas na máquina Virtual do Windows 
> * Conceder o acesso VM para um segredo armazenado no Cofre de chaves 
> * Obter um token de acesso utilizando a identidade da VM e utilizá-lo a obter o segredo do Cofre de chaves 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Criar uma máquina virtual do Windows num novo grupo de recursos

Para este tutorial, iremos criar uma nova VM do Windows. Também pode ativar MSI numa VM existente.

1.  Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.
2.  Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. 
3.  Introduza as informações da máquina virtual. O **Username** e **palavra-passe** criada aqui é as credenciais que utiliza para início de sessão para a máquina virtual.
4.  Escolha o adequado **subscrição** para a máquina virtual na lista pendente.
5.  Para selecionar um novo **grupo de recursos** gostaria que a máquina virtual para ser criado no, escolha **criar novo**. Quando terminar, clique em **OK**.
6.  Selecione o tamanho da VM. Para ver mais tamanhos, selecione **Visualizar todos** ou altere o filtro **Tipo de disco suportado**. No painel de definições, mantenha as predefinições e clique em **OK**.

    ![Texto alternativo da imagem](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Ativar o MSI da VM 

Um MSI de Máquina Virtual permite-lhe obter os tokens de acesso do Azure AD sem a necessidade de colocar as credenciais para o seu código. Ativar MSI diz ao Azure para criar uma identidade de gerido para a Máquina Virtual. Nos bastidores, permitir MSI duas coisas: instala a extensão da VM do MSI da VM e permite MSI no Gestor de recursos do Azure.

1.  Selecione o **Máquina Virtual** que pretende ativar o MSI em.  
2.  Na barra de navegação esquerdo em **configuração**. 
3.  Verá **identidade de serviço geridas**. Para registar e ativar o MSI, selecione **Sim**, se pretender desativá-la, escolha não. 
4.  Certifique-se de que clica **guardar** para guardar a configuração.  

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se pretende verificar e certifique-se as extensões na VM, clique em **extensões**. Se estiver ativado MSI, em seguida, **ManagedIdentityExtensionforWindows** aparece na lista.

    ![Texto alternativo da imagem](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Conceder o acesso VM a um segredo armazenado no Cofre de chaves 
 
Utilizar MSI código pode obter tokens de acesso para autenticar em recursos que suportam a autenticação do Azure AD.  No entanto, nem todos os serviços do Azure suportam a autenticação do Azure AD. Para utilizar MSI com esses serviços, armazenar as credenciais de serviço no Cofre de chaves do Azure e utilizar MSI para aceder ao Cofre de chaves para obter as credenciais. 

Em primeiro lugar, temos de criar um cofre de chaves e conceder acesso de identidade do nosso VM para o Cofre de chaves.   

1. Na parte superior da barra de navegação esquerdo, selecione **+ novo** , em seguida, **segurança + identidade** , em seguida, **Cofre de chaves**.  
2. Forneça um **nome** para o novo cofre de chaves. 
3. Localize o Cofre de chaves do mesmo grupo de subscrição e dos recursos da VM que criou anteriormente. 
4. Selecione **políticas de acesso** e clique em **adicionar novo**. 
5. Configurar a partir do modelo, selecionar **segredo gestão**. 
6. Escolha **selecione Principal**e o campo de pesquisa, introduza o nome da VM que criou anteriormente.  Selecione a VM na lista de resultados e clique em **selecione**. 
7. Clique em **OK** para concluir a adicionar a nova política de acesso, e **OK** para concluir a seleção da política de acesso. 
8. Clique em **criar** para concluir a criação do Cofre de chaves. 

    ![Texto alternativo da imagem](media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Em seguida, adicione um segredo ao Cofre de chaves, para que mais tarde possa obter o segredo do código em execução numa VM a utilizar: 

1. Selecione **todos os recursos**e localize e selecione o Cofre de chaves que criou. 
2. Selecione **segredos**e clique em **adicionar**. 
3. Selecione **Manual**, de **carregar opções**. 
4. Introduza um nome e valor para o segredo.  O valor pode ser qualquer coisa que pretende. 
5. Deixe a data de ativação e a data de expiração clara e deixe **ativado** como **Sim**. 
6. Clique em **criar** para criar o segredo. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Obter um token de acesso utilizando a identidade da VM e utilizá-lo a obter o segredo do Cofre de chaves  

Se não tiver PowerShell 4.3.1 ou posterior instalado, terá [transfira e instale a versão mais recente](https://docs.microsoft.com/powershell/azure/overview).

Em primeiro lugar, utilizamos MSI da VM para obter um token de acesso para autenticar para o Cofre de chaves:
 
1. No portal, navegue para **máquinas virtuais** e vá para a máquina virtual do Windows e no **descrição geral**, clique em **Connect**.
2. Introduza o **Username** e **palavra-passe** para que adicionou ao criar o **VM do Windows**.  
3. Agora que já criou um **ligação ao ambiente de trabalho remoto** com a máquina virtual, abra o PowerShell na sessão remota.  
4. No PowerShell, invoque o pedido web no inquilino ao obter o token para o anfitrião local na porta específica para a VM.  

    O pedido de PowerShell:
    
    ```powershell
    PS C:\> $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://vault.azure.net"} -Headers @{Metadata="true"} 
    ```
    
    Em seguida, a extrair a resposta completa, que é armazenada como uma cadeia de JavaScript Object Notation (JSON) formatado no objeto $response.  
    
    ```powershell
    PS C:\> $content = $response.Content | ConvertFrom-Json 
    ```
    
    Em seguida, extraia o token de acesso da resposta.  
    
    ```powershell
    PS C:\> $KeyVaultToken = $content.access_token 
    ```
    
    Por fim, utilize o comando de Invoke-WebRequest do PowerShell para obter o segredo que criou anteriormente no Cofre de chaves, transmitir o token de acesso no cabeçalho de autorização.  Terá do URL do seu Cofre de chaves, que está a ser o **Essentials** secção o **descrição geral** página do Cofre de chaves.  
    
    ```powershell
    PS C:\> (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    A resposta irá ter este aspeto: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Assim que tiver obtido o segredo do Cofre de chaves, pode utilizá-lo para se autenticarem num serviço que necessita de um nome e uma palavra-passe. 

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma descrição geral do MSI, consulte [descrição geral de identidade de serviço geridas](../active-directory/msi-overview.md).

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.
