## <a name="prepare-for-akv-integration"></a>Preparar a integração AKV
Para utilizar a integração do Cofre de chaves do Azure para configurar a VM do SQL Server, existem vários pré-requisitos: 

1. [Instalar o Azure Powershell](#install-azure-powershell)
2. [Criar um Azure Active Directory](#create-an-azure-active-directory)
3. [Criar um cofre de chaves](#create-a-key-vault)

As secções seguintes descrevem os pré-requisitos e as informações que necessárias para recolher mais tarde executar os cmdlets do PowerShell.

### <a name="install-azure-powershell"></a>Instalar o Azure PowerShell
Certifique-se de que instalou o Azure PowerShell SDK mais recente. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azureps-cmdlets-docs).

### <a name="create-an-azure-active-directory"></a>Criar um Azure Active Directory
Em primeiro lugar, tem de ter um [do Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) na sua subscrição. Entre muitas vantagens, isto permite-lhe conceder permissão ao seu Cofre de chaves para determinados utilizadores e aplicações.

Em seguida, registe uma aplicação com o AAD. Isto irá dar-lhe uma conta do Principal de serviço que tenha acesso ao seu Cofre de chaves que terá da VM. No artigo do Cofre de chaves do Azure, pode encontrar estes passos no [registar uma aplicação no Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register) secção, ou pode ver os passos com capturas de ecrã do **obter uma identidade para a secção da aplicação**  de [esta mensagem de blogue](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Antes de concluir estes passos, tenha em atenção que tem de recolher as informações seguintes durante este registo que é necessária mais tarde, quando ativar a integração do Cofre de chaves do Azure na sua VM do SQL Server.

* Depois da aplicação é adicionada, localizar o **ID de cliente** no **configurar** separador.   ![ID de cliente do Azure Active Directory](./media/virtual-machines-sql-server-akv-prepare/aad-client-id.png)
  
    O ID de cliente é atribuído a mais tarde a **$spName** parâmetro (nome Principal de serviço) no script de PowerShell para ativar a integração do Cofre de chaves do Azure. 
* Além disso, durante estes passos ao criar a chave, copie o segredo para a sua chave conforme é mostrado na captura de ecrã seguinte. Este segredo chave está atribuído a mais tarde a **$spSecret** parâmetro (segredo Principal de serviço) no PowerShell script.  
    ![Segredo do Azure Active Directory](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)
* Tem de autorizar este novo ID de cliente para ter as seguintes permissões de acesso: **encriptar**, **desencriptar**, **wrapKey**, **unwrapKey**, **sessão**, e **verificar**. Isto é feito com o [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx) cmdlet. Para obter mais informações consulte [autorizar a aplicação a utilizar a chave ou segredo](../articles/key-vault/key-vault-get-started.md#authorize).

### <a name="create-a-key-vault"></a>Criar um cofre de chaves
Para utilizar o Cofre de chaves do Azure para armazenar as chaves que irá utilizar para encriptação na sua VM, precisa de acesso para um cofre de chaves. Se já não configurar o seu Cofre de chaves, crie um seguindo os passos a [introdução ao Cofre de chaves do Azure](../articles/key-vault/key-vault-get-started.md) tópico. Antes de concluir estes passos, tenha em atenção que não há algumas informações que necessárias para recolher durante este conjunto de cópias de segurança que é necessária mais tarde, quando ativar a integração do Cofre de chaves do Azure na sua VM do SQL Server.

Quando chegar a criar um passo do Cofre de chaves, tenha em atenção o devolvido **vaultUri** propriedade, o que é o URL do Cofre de chaves. O exemplo fornecido, este passo, abaixo, que o nome do Cofre de chaves é ContosoKeyVault, pelo que o URL do Cofre de chaves seria possível https://contosokeyvault.vault.azure.net/.

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

O URL do Cofre de chaves é atribuído a mais tarde a **$akvURL** parâmetro do script do PowerShell para ativar a integração do Cofre de chaves do Azure.

