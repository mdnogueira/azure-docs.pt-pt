---
title: Utilize um MSI de VM Linux para aceder ao Gestor de recursos do Azure
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
ms.date: 10/30/2017
ms.author: elkuzmen
ms.openlocfilehash: a2ccae76ff548847b331c7993013f22a098525ce
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-resource-manager"></a>Utilize um Linux VM geridos serviço de identidade (MSI) para aceder ao Gestor de recursos do Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como ativar a identidade de serviço geridas (MSI) para uma Máquina Virtual de Linux e, em seguida, utilizar essa identidade para aceder à API do Gestor de recursos do Azure. Identidades de serviço geridas são automaticamente geridas pelo Azure e permitem-lhe autenticar para serviços que suportam a autenticação do Azure AD sem necessidade de introduzir as credenciais para o seu código. Saiba como:

> [!div class="checklist"]
> * Ativar MSI numa máquina Virtual Linux 
> * Conceder o acesso VM a um grupo de recursos no Gestor de recursos do Azure 
> * Obter um token de acesso utilizando a identidade da VM e utilizá-la para chamar o Azure Resource Manager 


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Criar uma Máquina Virtual Linux num novo grupo de recursos

Para este tutorial, iremos criar uma nova VM do Linux. Também pode ativar MSI numa VM existente.

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**.
3. Introduza as informações da máquina virtual. Para **tipo de autenticação**, selecione **chave pública SSH** ou **palavra-passe**. As credenciais criadas irão permitir iniciar sessão para a VM.

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Escolha um **subscrição** para a máquina virtual na lista pendente.
5. Para selecionar um novo **grupo de recursos** gostaria que a máquina virtual ser criada no, escolha **criar novo**. Quando terminar, clique em **OK**.
6. Selecione o tamanho da VM. Para ver mais tamanhos, selecione **ver todos os** ou alterar o filtro de tipo de disco suportados. No painel de definições, mantenha as predefinições e clique em **OK**.

## <a name="enable-msi-on-your-vm"></a>Ativar o MSI da VM

Um MSI de Máquina Virtual permite-lhe obter os tokens de acesso do Azure AD sem a necessidade de colocar as credenciais para o seu código. Nos bastidores, permitir MSI duas coisas: instala a extensão da VM do MSI da VM e permite MSI da VM.  

1. Selecione o **Máquina Virtual** que pretende ativar o MSI em.
2. Na barra de navegação esquerdo em **configuração**.
3. Verá **identidade de serviço geridas**. Para registar e ativar o MSI, selecione **Sim**, se pretender desativá-la, escolha não.
4. Certifique-se de que clica **guardar** para guardar a configuração.

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se pretender verificar quais as extensões são neste **VM com Linux**, clique em **extensões**. Se o MSI estiver ativado, o **ManagedIdentityExtensionforLinux** irão aparecer na lista.

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-azure-resource-manager"></a>Conceder o acesso VM a um grupo de recursos no Gestor de recursos do Azure 

Utilizar MSI código pode obter tokens de acesso para autenticar em recursos que suportam a autenticação do Azure AD. A API do Azure Resource Manager suporta a autenticação do Azure AD. Em primeiro lugar, é necessário conceder acesso de identidade desta VM a um recurso no Gestor de recursos do Azure, neste caso, o grupo de recursos no qual a VM está contida.  

1. Navegue até ao separador para **grupos de recursos**.
2. Selecione o específicos **grupo de recursos** que criou anteriormente.
3. Aceda a **aceder control(IAM)** no painel esquerdo.
4. Clique para **adicionar** uma nova atribuição de função para a VM. Escolha **função** como **leitor**.
5. Na lista pendente seguinte, **atribuir acesso** o recurso **Máquina Virtual**.
6. Em seguida, certifique-se a subscrição correta está listada no **subscrição** pendente. E para **grupo de recursos**, selecione **todos os grupos de recursos**.
7. Por fim, em **selecione** escolha a sua máquina Virtual do Linux na lista pendente e clique em **guardar**.

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-permission-linux.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Obter um token de acesso através da identidade da VM e utilizá-la para chamar o Gestor de recursos 

Para concluir estes passos, terá de um cliente SSH. Se estiver a utilizar o Windows, pode utilizar o cliente SSH o [subsistema Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Se precisar de assistência para configurar as chaves do seu cliente SSH, consulte [como chaves de utilizar o SSH com o Windows no Azure](../virtual-machines/linux/ssh-from-windows.md), ou [como criar e utilizar um par de chaves público e privado SSH para VMs com Linux no Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal, navegue para a VM com Linux e no **descrição geral**, clique em **Connect**.  
2. **Ligar** para a VM com o cliente SSH à sua escolha. 
3. Na janela de terminal, utilizando CURL, efetue um pedido para o ponto final local de MSI para obter acesso token para o Azure Resource Manager.  
 
    O pedido CURL para o token de acesso é abaixo.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true   
    ```
    
    > [!NOTE]
    > O valor do parâmetro "recursos" tem de ser uma correspondência exata para que é esperado pelo Azure AD.  No caso do ID de recurso do Resource Manager, tem de incluir a barra no final no URI. 
    
    A resposta inclui o token de acesso que precisa de aceder ao Gestor de recursos do Azure. 
    
    Resposta:  

    ```bash
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEPfpXZRLwrBNd607AzX0KVmLFrwA1vYJnCV-sSV8bwTh2t6CVEj240t0iyeVWVc2usJ0NY2rxPzKd_UckQ_zzrECG3kS4vuYePKz6GqNJFVzm2w2c61lX0-O1CwvQ9w","refresh_token":"","expires_in":"3599","expires_on":"1504130527","not_before":"1504126627","resource":"https://management.azure.com","token_type":"Bearer"} 
    ```
    
    Pode utilizar este token de acesso para aceder ao Gestor de recursos do Azure, por exemplo lê os detalhes do grupo de recursos aos quais concedeu anteriormente este acesso VM. Substitua os valores de \<ID de subscrição\>, \<grupo de recursos\>, e \<TOKEN de acesso\> com aqueles que criou anteriormente. 
    
    > [!NOTE]
    > O URL é sensível às maiúsculas e minúsculas, por isso, certifique-se se estiver a utilizar as maiúsculas exata que utilizou anteriormente quando denominado o grupo de recursos e, em maiúsculas "G" em "resourceGroup".  
    
    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    A resposta novamente com as informações específicas do grupo de recursos: 
     
    ```bash
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}} 
    ```
     
## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma descrição geral do MSI, consulte [descrição geral de identidade de serviço geridas](../active-directory/msi-overview.md).

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.

