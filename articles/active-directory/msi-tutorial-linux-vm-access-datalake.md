---
title: "Como utilizar um Linux VM geridos serviço de identidade (MSI) para aceder ao Azure Data Lake Store"
description: "Um tutorial mostra-lhe como utilizar um Linux VM geridos serviço de identidade (MSI) para aceder ao Azure Data Lake Store."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: 5c738fd8399fb555bcc72aacd278b5966a624cc7
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Utilize um Linux VM geridos serviço de identidade (MSI) para aceder ao Azure Data Lake Store

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como utilizar uma identidade de serviço geridas (MSI) para uma máquina virtual (VM) do Linux para aceder a um Azure Data Lake Store. Identidades de serviço geridas são automaticamente geridas pelo Azure e permitem-lhe autenticar para serviços que suportam a autenticação do Azure AD, sem necessidade de introduzir as credenciais para o seu código. Saiba como:

> [!div class="checklist"]
> * Ativar MSI numa VM com Linux 
> * Conceder o acesso VM a um Azure Data Lake Store
> * Obter um token de acesso utilizando a identidade da VM e utilizá-lo para aceder a um Azure Data Lake Store

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
6. Selecione o tamanho da VM. Para ver mais tamanhos, selecione **ver todos os** ou alterar o filtro de tipo de disco suportados. No painel de definições, mantenha as predefinições e clique em **OK**.

## <a name="enable-msi-on-your-vm"></a>Ativar o MSI da VM

Um MSI de Máquina Virtual permite-lhe obter os tokens de acesso do Azure AD sem a necessidade de colocar as credenciais para o seu código. Nos bastidores, permitir MSI duas coisas: instala a extensão da VM do MSI da VM e permite MSI no Gestor de recursos do Azure.  

1. Selecione o **Máquina Virtual** que pretende ativar o MSI em.
2. Na barra de navegação esquerdo em **configuração**.
3. Verá **identidade de serviço geridas**. Para registar e ativar o MSI, selecione **Sim**, se pretender desativá-la, escolha não.
4. Certifique-se de que clica **guardar** para guardar a configuração.

   ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se pretender verificar quais as extensões são neste **VM com Linux**, clique em **extensões**. Se o MSI estiver ativado, o **ManagedIdentityExtensionforLinux** aparece na lista.

   ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Conceder o acesso VM para o Azure Data Lake Store

Agora pode conceder o acesso VM para ficheiros e pastas um Azure Data Lake Store.  Para este passo, pode utilizar uma versão do Data Lake Store existente ou crie um novo.  Para criar um novo Data Lake Store utilizando o portal do Azure, siga este [início rápido do Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Também existem inícios rápidos que utilizam a CLI do Azure e o Azure PowerShell no [documentação do Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).

O Data Lake Store, criar uma nova pasta e conceder a permissão do MSI da VM para ler, escrever e executar ficheiros nessa pasta:

1. No portal do Azure, clique em **Data Lake Store** no painel de navegação esquerdo.
2. Clique em Data Lake Store que pretende utilizar para este tutorial.
3. Clique em **Explorador de dados** na barra de comando.
4. A pasta raiz do Data Lake Store está selecionada.  Clique em **acesso** na barra de comando.
5. Clique em **Adicionar**.  No **selecione** campo, introduza o nome da sua VM, por exemplo **DevTestVM**.  Clique para selecionar os resultados da pesquisa a VM, em seguida, clique em **selecione**.
6. Clique em **selecionar permissões**.  Selecione **leitura** e **executar**, adicionar a **esta pasta**e adicione como **apenas uma permissão de acesso**.  Clique em **OK**.  A permissão deve ser adicionada com êxito.
7. Fechar o **acesso** painel.
8. Para este tutorial, crie uma nova pasta.  Clique em **nova pasta** na barra de comandos e forneça a nova pasta um nome, por exemplo **TestFolder**.  Clique em **OK**.
9. Clique na pasta que criou, em seguida, clique em **acesso** na barra de comando.
10. Semelhante para o passo 5, clique em **adicionar**, além de **selecione** campo introduza o nome da sua VM, selecione-o e clique em **selecione**.
11. Semelhante ao passo 6, clique em **selecionar permissões**, selecione **leitura**, **escrever**, e **executar**, adicionar a **esta pasta**e adicione como **uma entrada de permissão de acesso e uma entrada de permissão predefinidas**.  Clique em **OK**.  A permissão deve ser adicionada com êxito.

O MSI da VM pode agora efetuar todas as operações em ficheiros na pasta que criou.  Para obter mais informações sobre gerir o acesso ao Data Lake Store, continue a ler este artigo [controlo de acesso no Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Obter um token de acesso utilizando o MSI da VM e utilizá-la para chamar o sistema de ficheiros do Azure Data Lake Store

O Azure Data Lake Store nativamente autenticação de suporte do Azure AD, para que possa aceitar tokens de acesso diretamente obtidos através de MSI.  Para autenticar para o sistema de ficheiros do Data Lake Store envia um token de acesso emitido pelo Azure AD para o Data Lake Store filesystem ponto final, num cabeçalho de autorização no formato "portador \<ACCESS_TOKEN_VALUE\>".  Para saber mais sobre o suporte de Data Lake Store para autenticação do Azure AD, leia [autenticação com o Data Lake Store utilizando o Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)

Neste tutorial, autenticar para o sistema de ficheiros do Data Lake Store pedidos de API de REST utilizando CURL para tornar REST.

> [!NOTE]
> O cliente do sistema de ficheiros do Data Lake Store SDKs ainda não suportam uma identidade de serviço geridas.  Este tutorial será atualizado quando é adicionado suporte para o SDK.

Para concluir estes passos, precisa de um cliente SSH. Se estiver a utilizar o Windows, pode utilizar o cliente SSH o [subsistema Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Se precisar de assistência para configurar as chaves do seu cliente SSH, consulte [como chaves de utilizar o SSH com o Windows no Azure](../virtual-machines/linux/ssh-from-windows.md), ou [como criar e utilizar um par de chaves público e privado SSH para VMs com Linux no Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal, navegue para a VM com Linux e no **descrição geral**, clique em **Connect**.  
2. **Ligar** para a VM com o cliente SSH à sua escolha. 
3. Na janela de terminal, utilizando CURL, efetue um pedido para o ponto final local de MSI para obter acesso token para o sistema de ficheiros do Data Lake Store.  O identificador de recurso para o Data Lake Store é "https://datalake.azure.net/."  É importante incluir a barra no final no identificador de recurso.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Uma resposta com êxito devolve o token de acesso que utilizar para autenticação para o Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Utilizar CURL, efetue um pedido para o Data Lake Store filesystem ponto final do REST para listar as pastas na pasta raiz.  Esta é uma forma simple para verificar que tudo está configurado corretamente.  Copie o valor do token de acesso do passo anterior.  É importante que a cadeia "Portador" no cabeçalho de autorização tem um capital "B".  Pode encontrar o nome do Data Lake Store no **descrição geral** secção do painel no portal do Azure Data Lake Store.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Uma resposta com êxito se pareça com:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Agora pode tentar carregar um ficheiro para o Data Lake Store.  Em primeiro lugar, crie um ficheiro para carregar.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Utilizar CURL, efetue um pedido para o Data Lake Store filesystem ponto final do REST para carregar o ficheiro para a pasta que criou anteriormente.  O carregamento envolve o redirecionamento e CURL segue o redirecionamento automaticamente. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Uma resposta com êxito se pareça com:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Utilizar outro sistema de ficheiros do Data Lake Store APIs pode acrescentar a ficheiros, transferir os ficheiros e muito mais.

Parabéns!  Tiver de autenticar para o sistema de ficheiros do Data Lake Store utilizando um MSI da VM.

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma descrição geral do MSI, consulte [descrição geral de identidade de serviço geridas](../active-directory/msi-overview.md).
- Para a gestão de operações Data Lake Store utiliza o Azure Resource Manager.  Para obter mais informações sobre como utilizar um MSI da VM para autenticar para o Gestor de recursos, leia o artigo [utilizar um Linux VM geridos serviço de identidade (MSI) para aceder ao Gestor de recursos](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm).
- Saiba mais sobre [autenticação com o Data Lake Store utilizando o Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).
- Saiba mais sobre [operações de sistema de ficheiros no Azure Data Lake Store utilizando a REST API](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api) ou [APIs do sistema de ficheiros de WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Saiba mais sobre [controlo de acesso no Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.