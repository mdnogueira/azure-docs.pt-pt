## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Preparar para autenticar pedidos do Azure Resource Manager
Tem de autenticar todas as operações que efetuar nos recursos utilizando o [do Azure Resource Manager] [ lnk-authenticate-arm] com o Azure Active Directory (AD). A forma mais fácil de configurar esta opção é utilizar o PowerShell ou a CLI do Azure.

Instalar o [cmdlets Azure PowerShell] [ lnk-powershell-install] antes de continuar.

Os passos seguintes mostram como configurar a autenticação de palavra-passe para uma aplicação AD utilizando o PowerShell. Pode executar estes comandos numa sessão do PowerShell padrão.

1. Inicie sessão sua subscrição do Azure utilizando o seguinte comando:

    ```powershell
    Login-AzureRmAccount
    ```

1. Se tiver várias subscrições do Azure, o início de sessão Azure concede acesso a todas as subscrições Azure associadas com as suas credenciais. Utilize o seguinte comando para listar as subscrições do Azure disponíveis para que possa utilizar:

    ```powershell
    Get-AzureRMSubscription
    ```

    Utilize o seguinte comando para selecionar a subscrição que pretende utilizar para executar os comandos para gerir o seu IoT hub. Pode utilizar o nome da subscrição ou o ID da saída do comando anterior:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Tome nota do seu **TenantId** e **SubscriptionId**. Poderá precisa deles mais tarde.
3. Crie uma nova aplicação do Azure Active Directory utilizando o seguinte comando, substituindo os proprietários da local:
   
   * **{Nome a apresentar}:** um nome a apresentar para a sua aplicação, tais como **MySampleApp**
   * **{URL da página inicial}:** o URL da home page da sua aplicação, tais como **http://mysampleapp/home**. Este URL não é necessário apontar para uma aplicação real.
   * **{Identificador da aplicação}:** um identificador exclusivo, tais como **http://mysampleapp**. Este URL não é necessário apontar para uma aplicação real.
   * **{Palavra-passe}:** uma palavra-passe que utiliza para autenticar com a sua aplicação.
     
     ```powershell
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
     ```
4. Anote o **ApplicationId** da aplicação que criou. Poderá precisa deste mais tarde.
5. Criar um novo principal de serviço utilizando o seguinte comando, substituindo **{MyApplicationId}** com o **ApplicationId** do passo anterior:
   
    ```powershell
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Configurar uma atribuição de função utilizando o seguinte comando, substituindo **{MyApplicationId}** com seu **ApplicationId**.
   
    ```powershell
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Terminar agora de criar a aplicação do Azure AD permite-lhe autenticar a partir da sua aplicação c# personalizada. Terá dos seguintes valores mais tarde no tutorial:

* TenantId
* SubscriptionId
* ApplicationId
* Palavra-passe

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
