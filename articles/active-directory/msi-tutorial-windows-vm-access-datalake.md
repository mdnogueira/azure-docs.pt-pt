---
title: "Como utilizar um Windows VM geridos serviço de identidade (MSI) para aceder ao Azure Data Lake Store"
description: "Um tutorial mostra-lhe como utilizar um Windows VM geridos serviço de identidade (MSI) para aceder ao Azure Data Lake Store."
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
ms.date: 10/31/2017
ms.author: skwan
ms.openlocfilehash: 28b3ec7640e1f126d2ef310130e94c84b68ffa75
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Utilizar um Windows VM geridos serviço de identidade (MSI) para aceder ao Azure Data Lake Store

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como utilizar uma identidade de serviço geridas (MSI) para uma máquina virtual (VM) do Windows para aceder a um Azure Data Lake Store. Identidades de serviço geridas são automaticamente geridas pelo Azure e permitem-lhe autenticar para serviços que suportam a autenticação do Azure AD, sem necessidade de introduzir as credenciais para o seu código. Saiba como:

> [!div class="checklist"]
> * Ativar MSI na VM do Windows 
> * Conceder o acesso VM a um Azure Data Lake Store
> * Obter um token de acesso utilizando a identidade da VM e utilizá-lo para aceder a um Azure Data Lake Store

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Criar uma máquina virtual do Windows num novo grupo de recursos

Para este tutorial, iremos criar uma nova VM do Windows.  Também pode ativar MSI numa VM existente.

1. Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. 
3. Introduza as informações da máquina virtual. O **Username** e **palavra-passe** criada aqui é as credenciais que utiliza para início de sessão para a máquina virtual.
4. Escolha o adequado **subscrição** para a máquina virtual na lista pendente.
5. Para selecionar um novo **grupo de recursos** na qual pretende criar a máquina virtual, escolha **criar novo**. Quando terminar, clique em **OK**.
6. Selecione o tamanho da VM. Para ver mais tamanhos, selecione **Visualizar todos** ou altere o filtro **Tipo de disco suportado**. Na página Definições, mantenha as predefinições e clique em **OK**.

   ![Texto alternativo da imagem](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Ativar o MSI da VM 

Um MSI da VM permite-lhe obter os tokens de acesso do Azure AD sem a necessidade de colocar as credenciais para o seu código. Ativar MSI diz ao Azure para criar uma identidade de gerido para a VM. Nos bastidores, permitir MSI duas coisas: instala a extensão da VM do MSI da VM e permite MSI no Gestor de recursos do Azure.

1. Selecione o **Máquina Virtual** que pretende ativar o MSI em.  
2. Na barra de navegação esquerdo em **configuração**. 
3. Verá **identidade de serviço geridas**. Para registar e ativar o MSI, selecione **Sim**, se pretender desativá-la, escolha não. 
4. Certifique-se de que clica **guardar** para guardar a configuração.  
   ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se pretende verificar e certifique-se as extensões na VM, clique em **extensões**. Se estiver ativado MSI, em seguida, **ManagedIdentityExtensionforWindows** aparece na lista.

   ![Texto alternativo da imagem](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

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

O Azure Data Lake Store nativamente autenticação de suporte do Azure AD, para que possa aceitar tokens de acesso diretamente obtidos através de MSI.  Para autenticar para o sistema de ficheiros do Data Lake Store que envia um token de acesso emitido pelo Azure AD para o Data Lake Store filesystem ponto final, num cabeçalho de autorização no formato "Portador < ACCESS_TOKEN_VALUE >".  Para saber mais sobre o suporte de Data Lake Store para autenticação do Azure AD, leia [autenticação com o Data Lake Store utilizando o Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)

> [!NOTE]
> O cliente do sistema de ficheiros do Data Lake Store SDKs ainda não suportam uma identidade de serviço geridas.  Este tutorial será atualizado quando é adicionado suporte para o SDK.

Neste tutorial, autenticar para o sistema de ficheiros do Data Lake Store pedidos de API de REST através do PowerShell para efetuar o resto. Para utilizar o MSI da VM para a autenticação, terá de efetuar os pedidos da VM.

1. No portal, navegue para **máquinas virtuais**, aceda à sua VM do Windows e no **descrição geral** clique **Connect**.
2. Introduza o **Username** e **palavra-passe** para que adicionou ao criar a VM do Windows. 
3. Agora que já criou um **ligação ao ambiente de trabalho remoto** com a máquina virtual, abra **PowerShell** na sessão remota. 
4. Através do PowerShell `Invoke-WebRequest`, efetue um pedido para o ponto final local de MSI para obter acesso token para o Azure Data Lake Store.  O identificador de recurso para o Data Lake Store é "https://datalake.azure.net/".  Data Lake não uma correspondência exata com o identificador do recurso e a barra no final é importante.

   ```powershell
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://datalake.azure.net/"} -Headers @{Metadata="true"}
   ```
    
   Converter a resposta de um objeto JSON para um objeto do PowerShell. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extraia o token de acesso da resposta.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Através 'Invoke-WebRequest' do PowerShell, efetue um pedido para ponto final REST do Data Lake Store para listar as pastas na pasta raiz.  Esta é uma forma simple para verificar que tudo está configurado corretamente.  É importante que a cadeia "Portador" no cabeçalho de autorização tem um capital "B".  Pode encontrar o nome do Data Lake Store no **descrição geral** secção do painel no portal do Azure Data Lake Store.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Uma resposta com êxito se pareça com:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Agora pode tentar carregar um ficheiro para o Data Lake Store.  Em primeiro lugar, crie um ficheiro para carregar.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Através do PowerShell `Invoke-WebRequest`, efetue um pedido para ponto final REST do Data Lake Store para carregar o ficheiro para a pasta que criou anteriormente.  Este pedido demora dois passos.  No primeiro passo, efetue um pedido e obter um redirecionamento para onde o ficheiro deve ser carregado.  No segundo passo, na realidade, carregue o ficheiro.  Lembre-se definir o nome da pasta e ficheiro adequadamente se valores diferentes que que utilizou neste tutorial. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Se a inspecionar o valor de `$HdfsRedirectResponse` deve ter o seguinte aspeto a seguinte resposta:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Conclua o carregamento por enviar um pedido para o ponto final de redirecionamento:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Um resposta com êxito aspeto:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
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