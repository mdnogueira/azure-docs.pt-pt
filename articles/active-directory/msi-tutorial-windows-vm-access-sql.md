---
title: Utilize um MSI de VM do Windows para aceder ao SQL do Azure
description: "Um tutorial que explica o processo de utilizar um Windows VM geridos serviço de identidade (MSI) para aceder ao SQL do Azure."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: skwan
ms.openlocfilehash: 1b13255c2bbb0d97c33851b89d071036c47e9cfa
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-sql"></a>Utilizar um Windows VM geridos serviço de identidade (MSI) para aceder ao SQL do Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como utilizar uma identidade de serviço geridas (MSI) para uma máquina virtual (VM) do Windows para aceder a um servidor de SQL do Azure. Identidades de serviço geridas são automaticamente geridas pelo Azure e permitem-lhe autenticar para serviços que suportam a autenticação do Azure AD, sem necessidade de introduzir as credenciais para o seu código. Saiba como:

> [!div class="checklist"]
> * Ativar MSI na VM do Windows 
> * Conceder o acesso VM a um servidor de SQL do Azure
> * Obter um token de acesso utilizando a identidade da VM e utilizá-la para consultar um servidor de SQL do Azure


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Criar uma máquina virtual do Windows num novo grupo de recursos

Para este tutorial, iremos criar uma nova VM do Windows.  Também pode ativar MSI numa VM existente.

1.  Clique no botão **Novo** localizado no canto superior esquerdo do portal do Azure.
2.  Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. 
3.  Introduza as informações da máquina virtual. O **Username** e **palavra-passe** criada aqui é as credenciais que utiliza para início de sessão para a máquina virtual.
4.  Escolha o adequado **subscrição** para a máquina virtual na lista pendente.
5.  Para selecionar um novo **grupo de recursos** na qual pretende criar a máquina virtual, escolha **criar novo**. Quando terminar, clique em **OK**.
6.  Selecione o tamanho da VM. Para ver mais tamanhos, selecione **Visualizar todos** ou altere o filtro **Tipo de disco suportado**. Na página Definições, mantenha as predefinições e clique em **OK**.

    ![Texto alternativo da imagem](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Ativar o MSI da VM 

Um MSI da VM permite-lhe obter os tokens de acesso do Azure AD sem a necessidade de colocar as credenciais para o seu código. Ativar MSI diz ao Azure para criar uma identidade de gerido para a VM. Nos bastidores, permitir MSI duas coisas: instala a extensão da VM do MSI da VM e permite MSI no Gestor de recursos do Azure.

1.  Selecione o **Máquina Virtual** que pretende ativar o MSI em.  
2.  Na barra de navegação esquerdo em **configuração**. 
3.  Verá **identidade de serviço geridas**. Para registar e ativar o MSI, selecione **Sim**, se pretender desativá-la, escolha não. 
4.  Certifique-se de que clica **guardar** para guardar a configuração.  
    ![Texto alternativo da imagem](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Se pretende verificar e certifique-se as extensões na VM, clique em **extensões**. Se estiver ativado MSI, em seguida, **ManagedIdentityExtensionforWindows** aparece na lista.

    ![Texto alternativo da imagem](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Conceder o acesso VM para uma base de dados no servidor de SQL do Azure

Agora pode conceder o acesso VM para uma base de dados no servidor de SQL do Azure.  Para este passo, pode utilizar um SQL server existente ou crie um novo.  Para criar um novo servidor e base de dados no portal do Azure, siga este [início rápido do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). Também existem inícios rápidos que utilizam a CLI do Azure e o Azure PowerShell no [documentação do SQL do Azure](https://docs.microsoft.com/azure/sql-database/).

Existem três passos para conceder o acesso VM a uma base de dados:
1.  Crie um grupo no Azure AD e tornar o MSI da VM num membro do grupo.
2.  Ative a autenticação do Azure AD para o SQL server.
3.  Criar um **utilizador contido** na base de dados que representa o grupo do Azure AD.

> [!NOTE]
> Normalmente criaria um utilizador contido diretamente para o MSI da VM.  Atualmente, o Azure SQL não permite o Principal de serviço do Azure AD que representa o MSI da VM deve ser mapeado para um utilizador contido.  Como solução suportada, pode tornar o MSI da VM num membro de um grupo do Azure AD, em seguida, criar um utilizador contido na base de dados que representa o grupo.


### <a name="create-a-group-in-azure-ad-and-make-the-vm-msi-a-member-of-the-group"></a>Crie um grupo no Azure AD e tornar o MSI da VM num membro do grupo

Pode utilizar um grupo do Azure AD existente ou crie uma nova com o Azure AD PowerShell.  

Em primeiro lugar, instalar o [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) módulo. Em seguida, inicie sessão com `Connect-AzureAD`, execute o seguinte comando para criar o grupo e guardá-lo numa variável:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

A saída assemelha-se ao seguinte, o que também examina o valor da variável:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM MSI access to SQL
```

Em seguida, adicione o MSI da VM para o grupo.  É necessário o MSI **ObjectId**, que pode obter com o Azure PowerShell.  Em primeiro lugar, transferir [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Em seguida, inicie sessão com `Login-AzureRmAccount`, e execute os seguintes comandos para:
- Certifique-se do que contexto da sessão está definido para a subscrição do Azure pretendida, se tiver vários.
- Listar os recursos disponíveis na sua subscrição do Azure, verifique na corretos grupo de recursos e nomes VM.
- Obter propriedades da VM MSI, utilizando os valores adequados para `<RESOURCE-GROUP>` e `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

A saída assemelha-se ao seguinte, o que também examina o ID de objeto principal do serviço do MSI da VM:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

Agora, adicione o MSI da VM para o grupo.  Só é possível adicionar um principal de serviço a um grupo com o Azure AD PowerShell.  Execute este comando:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Se também examinar a associação ao grupo posteriormente, a saída de procura da seguinte forma:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Ativar a autenticação do Azure AD para o SQL server

Agora que criou o grupo e adicionar o MSI da VM para a associação, pode [configurar a autenticação do Azure AD para o SQL server](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-azure-sql-server) utilizando os seguintes passos:

1.  No portal do Azure, selecione **servidores SQL** do painel de navegação esquerdo.
2.  Clique no SQL server esteja ativado para a autenticação do Azure AD.
3.  No **definições** secção do painel, clique em **administrador do Active Directory**.
4.  Na barra de comandos, clique em **definir administrador**.
5.  Selecione uma conta de utilizador do Azure AD para tornar um administrador do servidor e clique em **selecionar.**
6.  Na barra de comandos, clique em **guardar.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Criar um utilizador contido na base de dados que representa o grupo do Azure AD

Para este passo seguinte, terá de [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Antes de começar, também pode ser útil rever os seguintes artigos para em segundo plano na integração do Azure AD:

- [Autenticação universal com a base de dados SQL e SQL Data Warehouse (SSMS suporte para a MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication.md)
- [Configurar e gerir a autenticação do Azure Active Directory com a base de dados SQL ou SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure.md)

1.  Inicie o SQL Server Management Studio.
2.  No **ligar ao servidor** caixa de diálogo, introduza o nome do seu SQL server no **nome do servidor** campo.
3.  No **autenticação** campo, selecione **do Active Directory - Universal com suporte MFA**.
4.  No **nome de utilizador** campo, introduza o nome da conta do Azure AD que definir como o administrador do servidor, por exemplo,helen@woodgroveonline.com
5.  Clique em **Opções**.
6.  No **ligar à base de dados** campo, introduza o nome da base de dados não pertencente ao sistema que pretende configurar.
7.  Clique em **Ligar**.  Conclua o processo de início de sessão.
8.  No **Object Explorer**, expanda o **bases de dados** pasta.
9.  Faça duplo clique numa base de dados do utilizador e clique em **nova consulta**.
10.  Na janela da consulta, introduza a linha seguinte e clique em **executar** na barra de ferramentas:
    
     ```
     CREATE USER [VM MSI access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     O comando deve ser concluído com êxito, criar o utilizador contido para o grupo.
11.  Limpar a janela de consulta, introduza a seguinte linha e, em **executar** na barra de ferramentas:
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM MSI access to SQL]
     ```

     O comando deve ser concluído com êxito, conceder ao utilizador contido a capacidade de ler a base de dados.

Código em execução na VM pode obter um token do MSI e utilizar o token para autenticar com o SQL server.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Obter um token de acesso utilizando a identidade da VM e utilizá-la para chamar SQL do Azure 

SQL do Azure nativamente autenticação de suporte do Azure AD, para que possa aceitar tokens de acesso diretamente obtidos através de MSI.  Utilizar o **token de acesso** método de criação de uma ligação ao SQL Server.  Isto faz parte da integração do SQL do Azure com o Azure AD e é diferente de fornecer credenciais na cadeia de ligação.

Eis um exemplo de código de .net de abrir uma ligação ao SQL Server utilizando um token de acesso.  Este código tem de executar na VM para poder aceder ao ponto final MSI da VM.  **.NET framework 4.6** ou superior, é necessário utilizar o método de token de acesso.  Substitua os valores do AZURE-SQL-SERVERNAME e base de dados em conformidade.  Tenha em atenção o ID de recurso para o SQL do Azure "https://database.windows.net/".

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call MSI endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Em alternativa, uma forma rápida para testar a configuração de ponto a ponto sem ter de escrever e implementar uma aplicação na VM é através do PowerShell.

1.  No portal, navegue para **máquinas virtuais** e vá para a máquina virtual do Windows e no **descrição geral**, clique em **Connect**. 
2.  Introduza o **Username** e **palavra-passe** para que adicionou ao criar a VM do Windows. 
3.  Agora que já criou um **ligação ao ambiente de trabalho remoto** com a máquina virtual, abra **PowerShell** na sessão remota. 
4.  Através do PowerShell `Invoke-WebRequest`, efetue um pedido para o ponto final local de MSI para obter acesso token para SQL do Azure.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://database.windows.net/"} -Headers @{Metadata="true"}
    ```
    
    Converter a resposta de um objeto JSON para um objeto do PowerShell. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Extraia o token de acesso da resposta.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5.  Abra uma ligação ao SQL server. Não se esqueça de substituir os valores para o AZURE-SQL-SERVERNAME e base de dados.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Em seguida, criar e enviar uma consulta para o servidor.  Não se esqueça de substituir o valor de tabela.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Analise o valor de `$DataSet.Tables[0]` para ver os resultados da consulta.  Parabéns, tiver consultar a base de dados utilizando um MSI da VM e sem ser necessário fornecer credenciais!

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma descrição geral do MSI, consulte [descrição geral de identidade de serviço geridas](../active-directory/msi-overview.md).
- Saiba mais sobre [suporte de SQL do Azure para a autenticação do Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).
- Saiba mais sobre [como configurar o suporte de SQL do Azure para a autenticação do Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure).
- Saiba mais sobre [acesso no SQL server e autenticação](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.
