---
title: Utilize um MSI de VM Linux para aceder ao Cofre de chaves do Azure
description: "Um tutorial que explica o processo de utilizar um Linux VM geridos serviço de identidade (MSI) para aceder ao Gestor de recursos do Azure."
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
ms.openlocfilehash: cd79eea1a23a12aa8a469ea2673c910c1daf1e6b
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Utilize um Linux VM geridos serviço de identidade (MSI) para aceder ao Cofre de chaves do Azure 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como ativar a identidade de serviço geridas (MSI) para uma Máquina Virtual do Linux, em seguida, utilizar essa identidade para aceder ao Cofre de chaves do Azure. A funcionar como um arranque, Cofre de chaves torna possível para a sua aplicação de cliente, em seguida, utilizar o segredo para aceder a recursos não estão protegidos pelo Azure Active Directory (AD). Identidades de serviço geridas são automaticamente geridas pelo Azure e permitem-lhe autenticar para serviços que suportam a autenticação do Azure AD, sem necessidade de introduzir as credenciais para o seu código. 

Saiba como:

> [!div class="checklist"]
> * Ativar MSI numa máquina Virtual Linux 
> * Conceder o acesso VM para um segredo armazenado no Cofre de chaves 
> * Obter um token de acesso utilizando a identidade da VM e utilizá-lo a obter o segredo do Cofre de chaves 
 
## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Criar uma Máquina Virtual Linux num novo grupo de recursos

Para este tutorial, iremos criar uma nova VM do Linux. Também pode ativar MSI numa VM existente.

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**.
3. Introduza as informações da máquina virtual. Para **tipo de autenticação**, selecione **chave pública SSH** ou **palavra-passe**. As credenciais criadas permitem-lhe iniciar sessão VM.

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Escolha um **subscrição** para a máquina virtual na lista pendente.
5. Para selecionar um novo **grupo de recursos** gostaria que a máquina virtual ser criada no, escolha **criar novo**. Quando terminar, clique em **OK**.
6. Selecione o tamanho da VM. Para ver mais tamanhos, selecione **ver todos os** ou alterar o filtro de tipo de disco suportados. Na página Definições, mantenha as predefinições e clique em **OK**.

## <a name="enable-msi-on-your-vm"></a>Ativar o MSI da VM

Um MSI de Máquina Virtual permite-lhe obter os tokens de acesso do Azure AD sem a necessidade de colocar as credenciais para o seu código. Nos bastidores, permitir MSI duas coisas: instala a extensão da VM do MSI da VM e permite MSI da VM.  

1. Selecione o **Máquina Virtual** que pretende ativar o MSI em.
2. Na barra de navegação esquerdo em **configuração**.
3. Verá **identidade de serviço geridas**. Para registar e ativar o MSI, selecione **Sim**, se pretender desativá-la, escolha não.
4. Certifique-se de que clica **guardar** para guardar a configuração.

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se pretender verificar quais as extensões são neste **VM com Linux**, clique em **extensões**. Se o MSI estiver ativado, o **ManagedIdentityExtensionforLinux** aparece na lista.

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Conceder o acesso VM a um segredo armazenado no Cofre de chaves  

Utilizar MSI código pode obter tokens de acesso para autenticar em recursos que suportam a autenticação do Azure Active Directory. No entanto, nem todos os serviços do Azure suportam a autenticação do Azure AD. Para utilizar MSI com esses serviços, armazenar as credenciais de serviço no Cofre de chaves do Azure e utilizar MSI para aceder ao Cofre de chaves para obter as credenciais. 

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
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Obter um token de acesso através da identidade da VM e utilizá-lo a obter o segredo do Cofre de chaves  

Para concluir estes passos, precisa de um cliente SSH.  Se estiver a utilizar o Windows, pode utilizar o cliente SSH o [subsistema Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Se precisar de assistência para configurar as chaves do seu cliente SSH, consulte [como chaves de utilizar o SSH com o Windows no Azure](../virtual-machines/linux/ssh-from-windows.md), ou [como criar e utilizar um par de chaves público e privado SSH para VMs com Linux no Azure](../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. No portal, navegue para a VM com Linux e no **descrição geral**, clique em **Connect**. 
2. **Ligar** para a VM com o cliente SSH à sua escolha. 
3. Na janela de terminal, utilizando CURL, efetue um pedido para o ponto final local de MSI para obter acesso token para o Cofre de chaves do Azure.  
 
    O pedido CURL para o token de acesso é abaixo.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    A resposta inclui o token de acesso que precisa de aceder ao Gestor de recursos. 
    
    Resposta:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Pode utilizar este token de acesso para autenticar para o Cofre de chaves do Azure.  O pedido CURL seguinte mostra como ler um segredo do Cofre de chaves utilizando CURL e a API de REST do Cofre de chaves.  Terá do URL do seu Cofre de chaves, que está a ser o **Essentials** secção o **descrição geral** página do Cofre de chaves.  Também terá do token de acesso que obteve na chamada anterior. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    A resposta irá ter este aspeto: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Assim que tiver obtido o segredo do Cofre de chaves, pode utilizá-lo para se autenticarem num serviço que necessita de um nome e uma palavra-passe.


## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma descrição geral do MSI, consulte [descrição geral de identidade de serviço geridas](../active-directory/msi-overview.md).

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.




