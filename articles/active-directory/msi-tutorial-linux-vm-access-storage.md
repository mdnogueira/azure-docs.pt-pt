---
title: Utilize um MSI de VM Linux para aceder ao armazenamento do Azure
description: "Um tutorial que explica o processo de utilizar um Linux VM geridos serviço de identidade (MSI) para aceder ao armazenamento do Azure."
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
ms.openlocfilehash: 8096ef553f6e425a7e872ce33120ba2f41c6475d
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Utilizar uma identidade de serviço geridas do Linux VM para aceder ao armazenamento do Azure através de chave de acesso

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como ativar a identidade de serviço geridas (MSI) para uma Máquina Virtual de Linux e, em seguida, utilizar essa identidade obter chaves de acesso de conta de armazenamento. Pode utilizar uma chave de acesso de armazenamento como habitualmente quando efetuar operações de armazenamento, por exemplo, quando utilizar o SDK de armazenamento. Para este tutorial, carregar e transferir blobs com a CLI do Azure. Ficará a saber como:

> [!div class="checklist"]
> * Ativar MSI numa máquina Virtual Linux 
> * Conceder o acesso VM para chaves de acesso de conta de armazenamento no Gestor de recursos 
> * Obter um token de acesso através da identidade da VM e utilizá-lo para obter as chaves de acesso de armazenamento do Resource Manager  

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Criar uma máquina virtual Linux num novo grupo de recursos

Para este tutorial, iremos criar uma nova VM do Linux. Também pode ativar MSI numa VM existente.

1. Clique em de **+ /Safari/Chrome criar novo serviço** botão encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Ubuntu Server 16.04 LTS**.
3. Introduza as informações da máquina virtual. Para **tipo de autenticação**, selecione **chave pública SSH** ou **palavra-passe**. As credenciais criadas permitem-lhe iniciar sessão VM.

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Escolha um **subscrição** para a máquina virtual na lista pendente.
5. Para selecionar um novo **grupo de recursos** gostaria que a máquina virtual ser criada no, escolha **criar novo**. Quando terminar, clique em **OK**.
6. Selecione o tamanho da VM. Para ver mais tamanhos, selecione **ver todos os** ou alterar o filtro de tipo de disco suportados. No painel de definições, mantenha as predefinições e clique em **OK**.

## <a name="enable-msi-on-your-vm"></a>Ativar o MSI da VM

Um MSI de Máquina Virtual permite-lhe obter os tokens de acesso do Azure AD sem a necessidade de colocar as credenciais para o seu código. Nos bastidores, permitir MSI duas coisas: instala a extensão da VM do MSI da VM e permite a identidade de serviço geridas para a VM.  

1. Navegue para o grupo de recursos da nova máquina virtual e selecione a máquina virtual que criou no passo anterior.
2. Na VM "Definições" no lado esquerdo, clique em **configuração**.
3. Para registar e ativar o MSI, selecione **Sim**, se pretender desativá-la, escolha não.
4. Certifique-se de que clica **guardar** para guardar a configuração.

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se pretender verificar quais as extensões na VM, clique em **extensões**. Se o MSI estiver ativado, o **ManagedIdentityExtensionforLinux** aparece na lista.

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento 

Se ainda não tiver um, agora, irá criar uma conta de armazenamento.  Também pode ignorar este passo e conceder o acesso do MSI da VM para as chaves de uma conta de armazenamento existente. 

1. Clique em de **+ /Safari/Chrome criar novo serviço** botão encontrado no canto superior esquerdo do portal do Azure.
2. Clique em **armazenamento**, em seguida, **conta de armazenamento**, e irá apresentar um novo painel "Criar a conta de armazenamento".
3. Introduza um **nome** para a conta de armazenamento, o que irá utilizar mais tarde.  
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
7. Por fim, em **selecione** escolha a sua máquina Virtual do Linux na lista pendente, em seguida, clique em **guardar**. 

    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Obter um token de acesso através da identidade da VM e utilizá-la para chamar o Azure Resource Manager

Para o resto do tutorial, iremos trabalhar da VM que criou anteriormente.

Para concluir estes passos, terá de um cliente SSH. Se estiver a utilizar o Windows, pode utilizar o cliente SSH o [subsistema Windows para Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Se precisar de assistência para configurar as chaves do seu cliente SSH, consulte [como chaves de utilizar o SSH com o Windows no Azure](../virtual-machines/linux/ssh-from-windows.md), ou [como criar e utilizar um par de chaves público e privado SSH para VMs com Linux no Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal do Azure, navegue para **máquinas virtuais**, vá para a máquina virtual Linux, em seguida, a partir de **descrição geral** página clique **Connect** na parte superior. Copie a cadeia para ligar à VM. 
2. Ligar à VM utilizando o cliente SSH.  
3. Em seguida, será solicitado para introduzir na sua **palavra-passe** adicionado ao criar o **VM com Linux**. Deve, em seguida, ser sessão com êxito.  
4. Utilize o CURL para obter acesso token para o Azure Resource Manager.  

    Abaixo é o CURL pedido e resposta para o token de acesso:
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > O pedido anterior, o valor do parâmetro "recursos" tem de ser uma correspondência exata para que é esperado pelo Azure AD. Ao utilizar o ID de recurso do Azure Resource Manager, tem de incluir a barra no final no URI.
    > Na resposta seguinte, o elemento de access_token conforme foi shortened de uma forma abreviada.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Obter chaves de acesso de conta de armazenamento do Azure Resource Manager para efetuar chamadas de armazenamento  

Agora utilizar CURL para chamar o Resource Manager utilizando o token de acesso que obteve na secção anterior, para obter a chave de acesso de armazenamento. Assim que tivermos a chave de acesso de armazenamento, podemos chamar operações de carregamento/transferência de armazenamento. Não se esqueça de substituir o `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, e `<STORAGE ACCOUNT NAME>` valores de parâmetros com os seus próprios valores. Substitua o `<ACCESS TOKEN>` valor com o token de acesso que obteve anteriormente:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –-request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> O texto no URL anterior é sensível às maiúsculas e minúsculas, por isso, certifique-se se estiver a utilizar superior-minúsculas para os grupos de recursos para refleti-lo em conformidade. Além disso, é importante saber o que se trata de um pedido POST não um pedido GET e certifique-se de que transmita um valor para capturar um limite de comprimento com -d que pode ser nulo.  

A resposta CURL dá-lhe a lista de chaves:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Crie um ficheiro de blob de exemplo para carregar para o contentor de blob storage. Numa VM com Linux para fazer isto com o seguinte comando. 

```bash
echo "This is a test file." > test.txt
```

Em seguida, autenticar com o CLI `az storage` comando utilizando a chave de acesso de armazenamento e carregar o ficheiro para o contentor de blob. Para este passo, terá [instalar a CLI do Azure mais recente](https://docs.microsoft.com/cli/azure/install-azure-cli) na sua VM, se ainda não o fez.
 

```azurecli-interactive
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Resposta: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Além disso, pode transferir o ficheiro utilizando a CLI do Azure e a autenticação com a chave de acesso de armazenamento. 

Pedido: 

```azurecli-interactive
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Resposta: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>Passos seguintes

- Para obter uma descrição geral do MSI, consulte [descrição geral de identidade de serviço geridas](../active-directory/msi-overview.md).
- Para saber como fazê-lo este mesmo tutorial, utilizando um credencial SAS do armazenamento, consulte [utilizar uma identidade de serviço geridas do Linux VM para aceder ao armazenamento do Azure através de uma credencial SAS](msi-tutorial-linux-vm-access-storage-sas.md)
- Para obter mais informações sobre a funcionalidade SAS de conta do Storage do Azure, consulte:
  - [Utilizar assinaturas de acesso partilhado (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Construir um serviço SAS](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.
