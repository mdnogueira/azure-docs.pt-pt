---
title: Utilize um MSI de VM do Windows para aceder ao armazenamento do Azure
description: "Um tutorial que explica o processo de utilizar um Windows VM geridos serviço de identidade (MSI) para aceder ao armazenamento do Azure."
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
ms.openlocfilehash: e162134f52ceca9d77735893b847782d06e72cfe
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Utilizar uma identidade de serviço geridas do Windows VM para aceder ao armazenamento do Azure através de chave de acesso

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como ativar a identidade de serviço geridas (MSI) para uma Máquina Virtual do Windows, em seguida, utilizar essa identidade obter chaves de acesso de conta de armazenamento. Pode utilizar as chaves de acesso de armazenamento como habitualmente quando efetuar operações de armazenamento, por exemplo, quando utilizar o SDK de armazenamento. Para este tutorial, carregar e transferir blobs através do PowerShell de armazenamento do Azure. Ficará a saber como:


> [!div class="checklist"]
> * Ativar MSI na máquina Virtual do Windows 
> * Conceder o acesso VM para chaves de acesso de conta de armazenamento no Gestor de recursos 
> * Obter um token de acesso através da identidade da VM e utilizá-lo para obter as chaves de acesso de armazenamento do Resource Manager 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Criar uma máquina virtual do Windows num novo grupo de recursos

Para este tutorial, iremos criar uma nova VM do Windows. Também pode ativar MSI numa VM existente.

1.  Clique em de **+ /Safari/Chrome criar novo serviço** botão encontrado no canto superior esquerdo do portal do Azure.
2.  Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. 
3.  Introduza as informações da máquina virtual. O **Username** e **palavra-passe** criada aqui é as credenciais que utiliza para início de sessão para a máquina virtual.
4.  Escolha o adequado **subscrição** para a máquina virtual na lista pendente.
5.  Para selecionar um novo **grupo de recursos** gostaria que a máquina virtual para ser criado no, escolha **criar novo**. Quando terminar, clique em **OK**.
6.  Selecione o tamanho da VM. Para ver mais tamanhos, selecione **Visualizar todos** ou altere o filtro **Tipo de disco suportado**. No painel de definições, mantenha as predefinições e clique em **OK**.

    ![Texto alternativo da imagem](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Ativar o MSI da VM

Um MSI de Máquina Virtual permite-lhe obter os tokens de acesso do Azure AD sem a necessidade de colocar as credenciais para o seu código. Nos bastidores, permitir MSI duas coisas: instala a extensão da VM do MSI da VM e permite MSI para a Máquina Virtual.  

1. Navegue para o grupo de recursos da nova máquina virtual e selecione a máquina virtual que criou no passo anterior.
2. Na VM "Definições" no lado esquerdo, clique em **configuração**.
3. Para registar e ativar o MSI, selecione **Sim**, se pretender desativá-la, escolha não.
4. Certifique-se de que clica **guardar** para guardar a configuração.

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se pretender verificar quais as extensões na VM, clique em **extensões**. Se o MSI estiver ativado, o **ManagedIdentityExtensionforWindows** aparece na lista.

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento 

Se ainda não tiver um, agora, irá criar uma conta de armazenamento. Também pode ignorar este passo e conceder o acesso do MSI da VM para as chaves de uma conta de armazenamento existente. 

1. Clique em de **+ /Safari/Chrome criar novo serviço** botão encontrado no canto superior esquerdo do portal do Azure.
2. Clique em **armazenamento**, em seguida, **conta de armazenamento**, e irá apresentar um novo painel "Criar a conta de armazenamento".
3. Introduza um nome para a conta de armazenamento, o que irá utilizar mais tarde.  
4. **Modelo de implementação** e **conta kind** deve ser definido como "Gestor de recursos" e "Objetivo geral", respetivamente. 
5. Certifique-se a **subscrição** e **grupo de recursos** corresponder aqueles que especificou quando criou a VM no passo anterior.
6. Clique em **Criar**.

    ![Criar nova conta de armazenamento](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Criar um contentor de BLOBs na conta de armazenamento

Iremos mais tarde carregar e transferir um ficheiro para a nova conta de armazenamento. Como os ficheiros requerem armazenamento de BLOBs, temos de criar um contentor do blob no qual pretende armazenar o ficheiro.

1. Navegue de volta para a sua conta de armazenamento criados recentemente.
2. Clique em de **contentores** ligação no lado esquerdo, em BLOBs "serviço".
3. Clique em **+ contentor** no topo da página e um contentor"novo" painel slides enviados.
4. Dê um nome de contentor, selecione um nível de acesso, em seguida, clique em **OK**. O nome especificado será utilizado mais tarde no tutorial. 

    ![Criar contentor de armazenamento](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-storage-account-access-keys"></a>Conceder acesso MSI da VM para utilizar chaves de acesso de conta de armazenamento 

Armazenamento do Azure não suporta a autenticação do Azure AD nativamente.  No entanto, pode utilizar um MSI para obter chaves de acesso de conta de armazenamento do Resource Manager, em seguida, utilizar uma chave para aceder ao armazenamento.  Neste passo, pode conceder o acesso do MSI da VM para as chaves para a sua conta de armazenamento.   

1. Navegue de volta para a sua conta de armazenamento criados recentemente.  
2. Clique em de **(IAM) do controlo de acesso** ligação no painel esquerdo.  
3. Clique em **+ adicionar** na parte superior da página para adicionar uma nova atribuição de função para a VM
4. Definir **função** para "Armazenamento conta chave de serviço de função de operador", no lado direito da página. 
5. Na lista pendente seguinte, defina **atribuir acesso** o recurso "Máquina Virtual".  
6. Em seguida, certifique-se a subscrição correta está listada no **subscrição** lista pendente, em seguida, defina **grupo de recursos** a "Todos os grupos de recursos".  
7. Por fim, em **selecione** escolha a sua máquina Virtual do Windows na lista pendente, em seguida, clique em **guardar**. 

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Obter um token de acesso através da identidade da VM e utilizá-la para chamar o Azure Resource Manager 

Para o resto do tutorial, iremos trabalhar da VM que criou anteriormente. 

Terá de utilizar os cmdlets do PowerShell do Azure Resource Manager nesta parte.  Se não o tiver instalado, [transferir a versão mais recente](https://docs.microsoft.com/powershell/azure/overview) antes de continuar.

1. No portal do Azure, navegue para **máquinas virtuais**, aceda a sua máquina virtual do Windows, em seguida, a partir de **descrição geral** página clique **Connect** na parte superior. 
2. Introduza o **Username** e **palavra-passe** para que adicionou ao criar a VM do Windows. 
3. Agora que já criou um **ligação ao ambiente de trabalho remoto** com a máquina virtual, abra o PowerShell na sessão remota.
4. Através Invoke-WebRequest do Powershell, efetue um pedido para o ponto final local de MSI para obter acesso token para o Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > O valor do parâmetro "recursos" tem de ser uma correspondência exata para que é esperado pelo Azure AD. Ao utilizar o ID de recurso do Azure Resource Manager, tem de incluir a barra no final no URI.
    
    Em seguida, a extrair o elemento "Conteúdo", que é armazenado como uma cadeia de JavaScript Object Notation (JSON) formatado no objeto $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Em seguida, extraia o token de acesso da resposta.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Obter chaves de acesso de conta de armazenamento do Azure Resource Manager para efetuar chamadas de armazenamento  

Agora utilizar o PowerShell para chamar o Resource Manager utilizando o token de acesso que obteve na secção anterior, para obter a chave de acesso de armazenamento. Assim que tivermos a chave de acesso de armazenamento, podemos chamar operações de carregamento/transferência de armazenamento.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> O URL é maiúsculas e minúsculas, por isso, certifique-se de utilizar as maiúsculas exata utilizada anteriormente, quando com o nome do grupo de recursos, incluindo letras maiúsculas "G" em "resourceGroups." 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Em seguida, crie um ficheiro chamado "test.txt". Em seguida, utilize a chave de acesso de armazenamento para autenticar com o `New-AzureStorageContent` cmdlet, carregar o ficheiro para o nosso contentor de blob, em seguida, transfira o ficheiro.

```bash
echo "This is a test text file." > test.txt
```

Não se esqueça de instalar os cmdlets de armazenamento do Azure em primeiro lugar, utilizando `Install-Module Azure.Storage`. Em seguida, carregue o blob que acabou de criar, utilizar o `Set-AzureStorageBlobContent` cmdlet do PowerShell:

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Resposta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Também pode transferir o blob que acabou de carregar, utilizando o `Get-AzureStorageBlobContent` cmdlet do PowerShell:

```powershell
Get-AzureStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Resposta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```


## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma descrição geral do MSI, consulte [descrição geral de identidade de serviço geridas](../active-directory/msi-overview.md).
- Para saber como fazê-lo este mesmo tutorial, utilizando um credencial SAS do armazenamento, consulte [utilizar uma identidade de serviço geridas do Windows VM para aceder ao armazenamento do Azure através de uma credencial SAS](msi-tutorial-windows-vm-access-storage-sas.md)
- Para obter mais informações sobre a funcionalidade SAS de conta do Storage do Azure, consulte:
  - [Utilizar assinaturas de acesso partilhado (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Construir um serviço SAS](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo


