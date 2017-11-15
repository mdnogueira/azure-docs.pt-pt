---
title: "Configurar a autenticação do Azure Active Directory - SQL | Microsoft Docs"
description: "Saiba como ligar à base de dados SQL e SQL Data Warehouse, utilizando a autenticação do Active Directory do Azure."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 7e2508a1-347e-4f15-b060-d46602c5ce7e
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 07/10/2017
ms.author: rickbyh
ms.openlocfilehash: f0c9578217beff22b4a322b363c7499943311d88
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql-database-or-sql-data-warehouse"></a>Configurar e gerir a autenticação do Azure Active Directory com a base de dados SQL ou SQL Data Warehouse

Este artigo mostra como criar e preencher do Azure AD e, em seguida, utilizar o Azure AD com o SQL Database do Azure e o SQL Data Warehouse. Para obter uma descrição geral, consulte [autenticação do Active Directory do Azure](sql-database-aad-authentication.md).

>  [!NOTE]  
>  A ligar ao SQL Server em execução numa VM do Azure não é suportada através de uma conta do Azure Active Directory. Em alternativa, utilize um domínio de conta do Active Directory.

## <a name="create-and-populate-an-azure-ad"></a>Criar e preencher um Azure AD
Criar um Azure AD e preenchê-lo com os utilizadores e grupos. Azure AD pode ser o Azure AD inicial domínio gerido. Azure AD também pode ser um local no Active Directory Domain Services que está federada com o Azure AD.

Para obter mais informações, veja [Integrating your on-premises identities with Azure Active Directory (Integrar as identidades no local ao Azure Active Directory)](../active-directory/active-directory-aadconnect.md), [Add your own domain name to Azure AD (Adicionar o seu próprio nome de domínio ao Azure AD)](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure now supports federation with Windows Server Active Directory (O Microsoft Azure suporta agora a federação com o Windows Server Active Directory)](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administering your Azure AD directory (Administrar o seu diretório do Azure AD)](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Manage Azure AD using Windows PowerShell (Gerir o Azure AD com o Windows PowerShell)](/powershell/azure/overview?view=azureadps-2.0) e [Hybrid Identity Required Ports and Protocols (Portas e Protocolos Necessários para a Identidade Híbrida)](../active-directory/active-directory-aadconnect-ports.md).

## <a name="optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>Opcional: Associar ou altere o active directory que está atualmente associado a sua subscrição do Azure
Para associar a base de dados com o diretório do Azure AD para a sua organização, se o diretório de um diretório fidedigno para a subscrição do Azure que aloja a base de dados. Para obter mais informações, consulte [Como associar as subscrições do Azure ao Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Informações adicionais:** subscrição do Azure cada tem uma relação de confiança com uma instância do Azure AD. Tal significa que confia nesse diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas uma subscrição apenas pode confiar num diretório. Pode ver o diretório de confiança subscrição sob o **definições** separador em [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Esta relação de confiança entre uma subscrição e um diretório é diferente da relação de uma subscrição com todos os outros recursos no Azure (sites, bases de dados, e assim sucessivamente), que são mais como recursos subordinados de uma subscrição. Em caso de expiração de uma subscrição, será interrompido o acesso a esses outros recursos associados à subscrição também. No entanto, o diretório permanece no Azure e pode associar outra subscrição a esse diretório e continuar a gerir os utilizadores do diretório. Para obter mais informações sobre os recursos, consulte [compreender o acesso a recursos no Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Os procedimentos seguintes mostram como alterar o diretório associado para uma determinada subscrição.
1. Ligar ao seu [Portal clássico do Azure](https://manage.windowsazure.com/) através da utilização de um administrador de subscrição do Azure.
2. Na faixa da esquerda, selecione **definições**.
3. As subscrições são apresentadas no ecrã de definições. Se não for apresentada a subscrição pretendida, clique em **subscrições** na parte superior, pendente de **filtro por DIRETÓRIO** e selecione o diretório que contém as suas subscrições e, em seguida, clique em **aplicar**.
   
    ![Selecione a subscrição][4]
4. No **definições** área, clique a sua subscrição e, em seguida, clique em **editar DIRETÓRIO** na parte inferior da página.
   
    ![portal de definições do AD][5]
5. No **editar DIRETÓRIO** caixa, selecione o Azure Active Directory que estão associados com o SQL Server ou SQL Data Warehouse e, em seguida, clique na seta para a seguinte.
   
    ![Selecione de diretório de edição][6]
6. No **confirmar** diretório caixa de diálogo mapeamento, confirme que "**todos os coadministradores serão removidos.**"
   
    ![Editar diretório confirmar][7]
7. Clique em verificação recarregar o portal.

   > [!NOTE]
   > Quando alterar o diretório, o acesso a todos os coadministradores, utilizadores do Azure AD e grupos e os utilizadores dos recursos de diretório de segurança são removidos e já não têm acesso a esta subscrição ou os respetivos recursos. Apenas, como um administrador de serviço, pode configurar o acesso de principais com base no novo diretório. Esta alteração poderá demorar uma quantidade substancial de tempo para propagar a todos os recursos. Alterar o diretório, também altera o administrador do Azure AD para a base de dados SQL e do armazém de dados do SQL Server e não permitir acesso de base de dados para os utilizadores do Azure AD existentes. O administrador do Azure AD tem de ser reposição (conforme descrito abaixo) e Azure novo utilizadores do AD tem de ser criados.
   >  

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Criar um administrador do Azure AD para o Azure SQL server
Cada servidor de SQL do Azure (que aloja uma base de dados SQL ou SQL Data Warehouse) começa com uma conta de administrador de servidor único que é o administrador do servidor SQL do Azure completo. Um administrador do SQL Server segundo tem de ser criado, que é uma conta do Azure AD. Este principal é criado como um utilizador de base de dados contida na base de dados mestra. Como administradores, as contas de administrador de servidor são membros de **db_owner** função em todos os utilizadores da base de dados e introduza cada base de dados de utilizador como o **dbo** utilizador. Para obter mais informações sobre as contas de administrador de servidor, consulte [gerir bases de dados e inícios de sessão na base de dados do Azure SQL](sql-database-manage-logins.md).

Ao utilizar o Azure Active Directory com georreplicação, o administrador do Azure Active Directory tem de ser configurado para o principal e servidores secundários. Se um servidor tiver um administrador do Azure Active Directory, em seguida, inícios de sessão do Azure Active Directory e os utilizadores recebem um "não é possível ligar" para erro de servidor.

> [!NOTE]
> Os utilizadores que não se baseiam numa conta do Azure AD (incluindo a conta de administrador do servidor SQL do Azure), não é possível criar utilizadores do Azure baseada no AD, porque não têm permissão para validar os utilizadores de base de dados proposto com o Azure AD.
> 

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server"></a>Aprovisionar um administrador do Azure Active Directory para o servidor de SQL do Azure

Os dois procedimentos seguintes mostram-lhe como aprovisionar um administrador do Azure Active Directory para o servidor de SQL do Azure no portal do Azure e utilizando o PowerShell.

### <a name="azure-portal"></a>Portal do Azure
1. No [portal do Azure](https://portal.azure.com/), no canto superior direito, clique na ligação para uma lista de diretórios de Active Directory possíveis pendente. Escolha o correto do Active Directory como a predefinição do Azure AD. Este passo liga a associação de subscrição com o Active Directory com o servidor SQL do Azure, certificando-se de que a mesma subscrição é utilizada para o Azure AD e o SQL Server. (O servidor de SQL do Azure pode alojar a base de dados SQL do Azure ou do Azure SQL Data Warehouse.)   
    ![ad escolha][8]   
    
2. Na faixa da esquerda, selecione **servidores SQL**, selecione o **do SQL Server**e, em seguida, no **do SQL Server** painel, clique em **administrador do Active Directory**.   
3. No **administrador do Active Directory** painel, clique em **definir administrador**.   
    ![Selecione active directory](./media/sql-database-aad-authentication/select-active-directory.png)  
    
4. No **Adicionar administrador** painel, procure um utilizador, selecione o utilizador ou grupo para ser um administrador e, em seguida, clique em **selecione**. (O painel de administração do Active Directory mostra todos os membros e grupos do Active Directory. Não não possível selecionar utilizadores ou grupos que estão a cinzento porque não são suportados como administradores do Azure AD. (Ver a lista de administradores suportados no **funcionalidades do Azure AD e limitações** secção [utilizar Azure Active Directory Authentication para a autenticação com base de dados SQL ou SQL Data Warehouse](sql-database-aad-authentication.md).) O controlo de acesso baseado em funções (RBAC) aplica-se apenas para o portal e não é propagado para SQL Server.   
    ![Selecionar admin](./media/sql-database-aad-authentication/select-admin.png)  
    
5. Na parte superior do **administrador do Active Directory** painel, clique em **guardar**.   
    ![Guardar admin](./media/sql-database-aad-authentication/save-admin.png)   

O processo de alterar o administrador pode demorar alguns minutos. Em seguida, o novo administrador aparece no **administrador do Active Directory** caixa.

   > [!NOTE]
   > Quando configurar o administrador do Azure AD, o novo nome de administrador (utilizador ou grupo) não pode já estar presente na base de dados mestra virtual como um utilizador de autenticação do SQL Server. Se estiver presente, a configuração de administração do Azure AD irá falhar; a reverter a respetiva criação e que indica que este tipo de administrador (nome) já existe. Uma vez que tal um utilizador de autenticação do SQL Server não faz parte do Azure AD, falha qualquer esforço para ligar ao servidor utilizando a autenticação do Azure AD.
   > 


Mais tarde remover um administrador, na parte superior do **administrador do Active Directory** painel, clique em **remover administrador**e, em seguida, clique em **guardar**.

### <a name="powershell"></a>PowerShell
Para executar os cmdlets do PowerShell, tem de ter o Azure PowerShell instalado e em execução. Para informações detalhadas, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

Para aprovisionar um administrador do Azure AD, execute os seguintes comandos do Azure PowerShell:

* Add-AzureRmAccount
* SELECT-AzureRmSubscription

Os cmdlets utilizados para aprovisionar e gerir o administrador do Azure AD:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Conjunto AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/set-azurermsqlserveractivedirectoryadministrator) |Aprovisiona um administrador do Azure Active Directory para o servidor SQL do Azure ou do Azure SQL Data Warehouse. (Tem de ser da subscrição atual.) |
| [Remover AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/remove-azurermsqlserveractivedirectoryadministrator) |Remove o administrador do Azure Active Directory para o servidor SQL do Azure ou do Azure SQL Data Warehouse. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator) |Devolve informações sobre um administrador do Azure Active Directory atualmente configurada para o servidor SQL do Azure ou o Azure SQL Data Warehouse. |

Utilize o comando get-ajuda do PowerShell para ver mais detalhes para cada um destes comandos, por exemplo ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

O script seguinte aprovisiona um grupo de administrador do Azure AD com o nome **DBA_Group** (id de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para o **demo_server** servidor num grupo de recursos com o nome **grupo-23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

O **DisplayName** parâmetro de entrada aceita o nome a apresentar do Azure AD ou o nome Principal de utilizador. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Para grupos do Azure AD apenas o Azure AD o nome de apresentação é suportado.

> [!NOTE]
> O comando do PowerShell do Azure ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` não impedem que aprovisionamento admins do Azure AD para os utilizadores não suportados. Um utilizador não suportado pode ser aprovisionado, mas não é possível ligar à base de dados. 
> 

O exemplo seguinte utiliza o opcional **ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> O Azure AD **ObjectID** é necessária quando a **DisplayName** não é exclusivo. Para obter o **ObjectID** e **DisplayName** valores, utilize a secção do Active Directory do Portal clássico do Azure e ver as propriedades de um utilizador ou grupo.
> 

O exemplo seguinte devolve informações sobre o atual administração do Azure AD para o Azure SQL server:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

O exemplo a seguir remove o administrador do Azure AD:

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Também pode aprovisionar um administrador de diretório do Azure Active Directory, utilizando as APIs REST. Para obter mais informações, consulte [referência de API de REST de gestão de serviço e operações para operações de bases de dados SQL do Azure para bases de dados do Azure SQL](https://msdn.microsoft.com/library/azure/dn505719.aspx)

### <a name="cli"></a>CLI  
Também pode aprovisionar um administrador do Azure AD ao chamar os seguintes comandos da CLI:
| Comando | Descrição |
| --- | --- |
|[Criar ad-administrador do servidor de sql AZ](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) |Aprovisiona um administrador do Azure Active Directory para o servidor SQL do Azure ou do Azure SQL Data Warehouse. (Tem de ser da subscrição atual.) |
|[eliminação de administração do ad do AZ sql server](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) |Remove o administrador do Azure Active Directory para o servidor SQL do Azure ou do Azure SQL Data Warehouse. |
|[lista de administração de ad AZ sql server](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) |Devolve informações sobre um administrador do Azure Active Directory atualmente configurada para o servidor SQL do Azure ou o Azure SQL Data Warehouse. |
|[atualização de administração do ad do AZ SQL Server](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) |Atualiza o administrador do Active Directory para um servidor SQL do Azure ou o Azure SQL Data Warehouse. |

Para mais informações sobre comandos da CLI, consulte [SQL - az sql](https://docs.microsoft.com/cli/azure/sql/server).  


## <a name="configure-your-client-computers"></a>Configurar os computadores cliente
Em todas as máquinas de cliente, de que as aplicações ou utilizadores à SQL Database do Azure ou utilizar identidades do Azure AD, o Azure SQL Data Warehouse tem de instalar o seguinte software:

* .NET framework 4.6 ou posterior a partir do [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
* Biblioteca de autenticação do Azure Active Directory para o SQL Server (**ADALSQL. DLL**) está disponível em vários idiomas (tanto x86 como amd64) do Centro de transferências em [Microsoft Active Directory Authentication Library para Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

Pode satisfazer estes requisitos por:

* Instalar o [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ou [SQL Server Data Tools para Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) cumpre os requisitos do .NET Framework 4.6.
* Versão do SSMS instala x86 de **ADALSQL. DLL**.
* SSDT instala a versão amd64 **ADALSQL. DLL**.
* O Visual Studio mais recente do [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) cumpre os requisitos do .NET Framework 4.6, mas não instala a versão necessária amd64 **ADALSQL. DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Criar utilizadores de base de dados contida na base de dados mapeado para as identidades do Azure AD

Autenticação do Azure Active Directory exige que os utilizadores da base de dados ser criado como utilizadores de base de dados contida. Um utilizador de base de dados contida, com base na identidade do Azure AD, é um utilizador de base de dados que não tem um início de sessão na base de dados mestra, e que mapeia para uma identidade no diretório do Azure AD que estão associado com a base de dados. A identidade do Azure AD pode ser uma conta de utilizador individual ou um grupo. Para obter mais informações sobre os utilizadores de base de dados contida, consulte [efetuar contidos utilizadores de base de dados da base de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Não não possível criar utilizadores de base de dados (com exceção dos administradores) através do portal. Não são propagadas para funções do RBAC para o SQL Server, base de dados SQL ou SQL Data Warehouse. Funções do RBAC do Azure são utilizadas para gerir recursos do Azure e não se aplicam às permissões de base de dados. Por exemplo, o **contribuinte de servidor de SQL** função não concede acesso ao ligar à base de dados SQL ou SQL Data Warehouse. A permissão de acesso deve ser concedida diretamente na base de dados através de instruções Transact-SQL.
>

Para criar um Azure AD-utilizador com base no contidos da base de dados (que não seja o administrador do servidor que é proprietário da base de dados), estabeleça ligação à base de dados com uma identidade do Azure AD, como um utilizador com, pelo menos, o **ALTER qualquer utilizador** permissão. Em seguida, utilize a seguinte sintaxe de Transact-SQL:

```
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* pode ser o nome principal de utilizador de um utilizador do Azure AD ou o nome a apresentar para um grupo do Azure AD.

**Exemplos:** para criar uma base de dados contida utilizador que representa um Azure AD federados ou geridos utilizador de domínio:
```
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Para criar um utilizador de base de dados contida, que representa um Azure AD ou federadas o grupo de domínio, forneça o nome de apresentação de um grupo de segurança:
```
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Para criar um utilizador de base de dados contida, que representa uma aplicação que estabelece ligação utilizar um token do Azure AD:

```
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

>  [!TIP]
>  Diretamente não é possível criar um utilizador a partir de um Azure Active Directory que não seja o Azure Active Directory que está associado a sua subscrição do Azure. No entanto, os membros de outros diretórios do Active Directory que são os utilizadores importados do Active Directory associada (conhecido como utilizadores externos) podem ser adicionados a um grupo do Active Directory no inquilino do Active Directory. Ao criar um utilizador de base de dados contida para esse grupo do AD, os utilizadores do Active Directory externo poderem obter acesso à base de dados do SQL Server.   

Para obter mais informações sobre como criar contidos base de dados de utilizadores com base em identidades do Azure Active Directory, consulte [criar utilizador (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Remover o administrador do Azure Active Directory para o Azure SQL server impede que qualquer utilizador de autenticação do Azure AD a ligar ao servidor. Se necessário, inutilizável utilizadores do Azure AD podem ser removidos manualmente por um administrador da base de dados SQL.   

>  [!NOTE]
>  Se receber um **ligação o tempo limite expirou**, poderá ter de definir o `TransparentNetworkIPResolution` parâmetro da cadeia de ligação como false. Para obter mais informações, consulte [problema de tempo limite de ligação com o .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).   

   
Quando criar um utilizador de base de dados, que o utilizador recebe o **CONNECT** permissão e pode ligar a essa base de dados como um membro do **pública** função. Inicialmente as permissões apenas disponíveis para o utilizador são as permissões concedidas para o **pública** função ou as permissões concedidas a quaisquer grupos do Azure AD que são membros do. Depois de aprovisionar um utilizador de base de dados contidas baseada em AD do Azure, pode conceder ao utilizador permissões adicionais da mesma forma como pode conceder permissão para qualquer outro tipo de utilizador. Normalmente, conceder permissões para funções de base de dados e adicionar utilizadores a funções. Para obter mais informações, consulte [Noções básicas de permissão de motor de base de dados](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obter mais informações sobre as funções de base de dados SQL especiais, consulte [gerir bases de dados e inícios de sessão na base de dados do Azure SQL](sql-database-manage-logins.md).
Uma conta de utilizador de domínio federado que é importada para um domínio gerido como um utilizador externo, tem de utilizar a identidade do domínio gerido.

> [!NOTE]
> Os utilizadores do AD do Azure são marcados nos metadados da base de dados com o tipo E (EXTERNAL_USER) e para os grupos com tipo X (EXTERNAL_GROUPS). Para obter mais informações, consulte [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx). 
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Ligar ao armazém de dados ou base de dados de utilizador com o SSMS ou o SSDT  
Para confirmar que o administrador do Azure AD está corretamente configurado, ligue para o **mestre** da base de dados com a conta de administrador do Azure AD.
Para aprovisionar do Azure AD-utilizador com base no contidos da base de dados (que não seja o administrador do servidor que é proprietário da base de dados), ligar à base de dados com uma identidade do Azure AD que tenha acesso à base de dados.

> [!IMPORTANT]
> Suporte para a autenticação do Azure Active Directory está disponível com [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) no Visual Studio 2015. A versão de Agosto de 2016 do SSMS também inclui suporte para autenticação do Active Directory Universal, que permite que os administradores necessitam de utilizar uma chamada telefónica, mensagem de texto, os smart cards com pin ou a notificação da aplicação móvel do multi-factor Authentication.
 
## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Utilizar uma identidade do Azure AD para estabelecer ligação com o SSMS ou o SSDT  

Os procedimentos seguintes mostram como ligar a uma base de dados do SQL Server com uma identidade do Azure AD utilizando o SQL Server Management Studio ou ferramentas de base de dados do SQL Server.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Utilize este método se tiver sessão iniciada Windows utilizando as credenciais do Azure Active Directory a partir de um domínio federado.

1. Iniciar Management Studio ou as ferramentas do Data e a **ligar ao servidor** (ou **ligar ao motor de base de dados**) caixa de diálogo a **autenticação** caixa, selecione  **Integradas no Active Directory -**. Nenhuma palavra-passe é necessária ou pode ser introduzida porque as suas credenciais existentes serão apresentadas para a ligação.   

    ![Selecionar a autenticação integrada do AD][11]
2. Clique no **opções** botão e no **propriedades de ligação** na página a **ligar à base de dados** caixa, escreva o nome da base de dados de utilizador que pretende ligar. (O **ID de nome ou o inquilino de domínio do AD**"opção só é suportada para **Universal com uma ligação de MFA** opções, caso contrário, se estiver a cinzento.)  

    ![Selecione o nome de base de dados][13]

## <a name="active-directory-password-authentication"></a>Autenticação de palavra-passe do Active Directory

Utilize este método quando a ligação com um nome do principal do Azure AD utilizando o Azure AD gerido domínio. Também pode utilizá-lo para uma conta federada sem acesso ao domínio, por exemplo, quando trabalhar remotamente.

Utilize este método se tiver sessão iniciada Windows utilizando credenciais de um domínio que não está Federado com o Azure, ou quando utilizar a autenticação do Azure AD com o Azure AD com base no inicial ou o domínio de cliente.

1. Iniciar Management Studio ou as ferramentas do Data e a **ligar ao servidor** (ou **ligar ao motor de base de dados**) caixa de diálogo a **autenticação** caixa, selecione  **Active Directory – palavra-passe**.
2. No **nome de utilizador** caixa, escreva o nome de utilizador do Azure Active Directory no formato  **username@domain.com** . Tem de ser uma conta do Azure Active Directory ou uma conta de domínio federar com o Azure Active Directory.
3. No **palavra-passe** caixa, escreva a palavra-passe de utilizador para a conta do Azure Active Directory ou federadas conta de domínio.

    ![Selecionar a autenticação de palavra-passe do AD][12]
4. Clique no **opções** botão e no **propriedades de ligação** na página a **ligar à base de dados** caixa, escreva o nome da base de dados de utilizador que pretende ligar. (Consulte o gráfico na opção anterior).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Utilizar uma identidade do Azure AD para ligar a partir de uma aplicação cliente

Os procedimentos seguintes mostram como ligar a uma base de dados do SQL Server com uma identidade do Azure AD a partir de uma aplicação de cliente.

###  <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Para utilizar a autenticação integrada do Windows, Active Directory do seu domínio tem de ser federada com o Azure Active Directory. A aplicação de cliente (ou um serviço) ao ligar à base de dados tem de executar num computador associado a um domínio sob credenciais de domínio de um utilizador.

Para ligar a uma base de dados através da autenticação integrada e uma identidade do Azure AD, a palavra-chave de autenticação na cadeia de ligação de base de dados tem de ser definida para o Active Directory Integrated. O seguinte exemplo de código do c# utiliza ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

A palavra-chave da cadeia de ligação ``Integrated Security=True`` não é suportada para ligar à SQL Database do Azure. Quando efetuar uma ligação de ODBC, terá de remover os espaços e defina autenticação 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticação de palavra-passe do Active Directory

Para ligar a uma base de dados através da autenticação integrada e uma identidade do Azure AD, a palavra-chave de autenticação deve ser definida como palavra-passe de diretório Active Directory. A cadeia de ligação tem de conter ID/UID de utilizador e palavras-chave de palavra-passe/PWD e valores. O seguinte exemplo de código do c# utiliza ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Saiba mais sobre os métodos de autenticação do Azure AD utilizando os exemplos de código de demonstração disponíveis em [demonstração de GitHub de autenticação do Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token do Azure AD
Este método de autenticação permite que os serviços de camada média ligar à SQL Database do Azure ou do Azure SQL Data Warehouse por obter um token do Azure Active Directory (AAD). Permite cenários sofisticados, incluindo a autenticação baseada em certificado. Tem de concluir quatro passos básicos para utilizar a autenticação de token do Azure AD:

1. Registar a sua aplicação no Azure Active Directory e obter o id de cliente para o seu código. 
2. Crie um utilizador de base de dados que representa a aplicação. (Concluir anteriormente no passo 6).
3. Crie um certificado na computador de cliente é executada a aplicação.
4. Adicione o certificado como uma chave para a sua aplicação.

Cadeia de ligação de exemplo:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Para obter mais informações, consulte [blogue de segurança do SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Para obter informações sobre como adicionar um certificado, consulte [introdução à autenticação baseada em certificado no Azure Active Directory](../active-directory/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

As seguintes declarações, ligar utilizando a versão 13.1 de sqlcmd, que está disponível a partir de [Centro de transferências](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Passos seguintes
- Para obter uma descrição geral do acesso e controlo na Base de Dados SQL, veja [Acesso e controlo da Base de Dados SQL](sql-database-control-access.md).
- Para obter uma descrição geral de inícios de sessão, utilizadores e funções de base de dados da Base de Dados SQL, veja [Inícios de sessão, utilizadores e funções de base de dados](sql-database-manage-logins.md).
- Para obter mais informações sobre os principais de bases de dados, veja [Principals (Principais)](https://msdn.microsoft.com/library/ms181127.aspx).
- Para obter mais informações sobre as funções de base de dados, veja [Database roles (Funções de base de dados)](https://msdn.microsoft.com/library/ms189121.aspx).
- Para obter mais informações sobre as regras de firewall na Base de Dados SQL, veja [Regras de firewall da Base de Dados SQL](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png

