---
title: Utilizar bases de dados MySQL como PaaS na pilha do Azure | Microsoft Docs
description: "Saiba como pode implementar o fornecedor de recursos de MySQL e fornecer bases de dados MySQL como um serviço na pilha do Azure"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: fdb4180ce11b29577299e329869144e99ead0f05
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2017
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Utilizar bases de dados MySQL na pilha do Microsoft Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Pode implementar um fornecedor de recursos de MySQL na pilha do Azure. Depois de implementar o fornecedor de recursos, pode criar bases de dados através de modelos de implementação Azure Resource Manager e servidores MySQL e fornecer bases de dados MySQL como um serviço. Bases de dados MySQL, que são comuns em web sites, suportam várias plataformas de Web site. Por exemplo, depois de implementar o fornecedor de recursos, pode criar sites WordPress da plataforma Web Apps do Azure como um suplemento de serviço (PaaS) para a pilha do Azure.

Para implementar o fornecedor de MySQL num sistema que não tem acesso à internet, pode copiar o ficheiro [mysql conector-net 6.9.9.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.9.9.msi) para uma partilha local. Em seguida, fornece esse nome de partilha quando lhe for pedido. Também tem de instalar os módulos do Azure e o Azure PowerShell da pilha.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Arquitetura de adaptador de fornecedor de recursos de servidor MySQL

O fornecedor de recursos é constituído por três componentes:

- **O adaptador de fornecedor de recursos de MySQL VM**, que é uma máquina virtual do Windows em execução os serviços do fornecedor.
- **O fornecedor de recursos próprio**, que processa os pedidos de aprovisionamento e expõe recursos de base de dados.
- **Servidores que alojam o servidor de MySQL**, que fornecem a capacidade para bases de dados, denominados servidores de alojamento.

Esta versão já não cria uma instância de MySQL. Tem de criá-los e/ou fornecer acesso a instâncias externas do SQL Server. Visite o [Galeria de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) para um modelo de exemplo que podem:
- criar um servidor de MySQL por si
- transferir e implementar um servidor de MySQL do Marketplace.

> [!NOTE]
> Servidores instalado uma pilha do Azure com vários nós de alojamento têm de ser criado a partir de uma subscrição de inquilino. Estes não podem ser criados da subscrição de fornecedor predefinido. Por outras palavras, estes devem ser criados no portal inquilino ou a partir de uma sessão do PowerShell com um início de sessão adequado. Todos os servidores de alojamento são cobráveis VMs e tem de ter licenças adequadas. O administrador de serviço pode ser o proprietário dessa subscrição.

### <a name="required-privileges"></a>Privilégios necessários
A conta do sistema tem de ter os seguintes privilégios:

1.  Base de dados: Criar, remover
2.  Início de sessão: Criar, definir, remover, conceder, revogar

## <a name="deploy-the-resource-provider"></a>Implementar o fornecedor de recursos

1. Se ainda não o tiver feito, registe o kit de desenvolvimento e transferir a imagem do Windows Server 2016 Datacenter Core transferível através da gestão do Marketplace. Tem de utilizar uma imagem do Windows Server 2016 Core. Também pode utilizar um script para criar um [imagem do Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) -Certifique-se selecionar a opção de núcleos. O tempo de execução de .NET 3.5 já não é necessário.


2. A iniciar sessão para um anfitrião que pode aceder a VM de ponto final com privilégios.

    a. Nas instalações do Kit de desenvolvimento de pilha do Azure (ASDK), inicie sessão no anfitrião físico.

    b. Em sistemas com vários nós, o anfitrião tem de ser um sistema que pode aceder ao ponto de final com privilégios.

3. [Transfira o ficheiro de binários do fornecedor de recursos MySQL](https://aka.ms/azurestackmysqlrp) e executar o Self-extractor para extrair os conteúdos num diretório temporário.

    > [!NOTE]
    > Se em execução numa pilha de Azure criar 20170928.3 ou anterior, [transferir esta versão](https://aka.ms/azurestackmysqlrp1709).

4.  O certificado de raiz de pilha do Azure é obtido a partir do ponto final com privilégios. Para ASDK, é criado um certificado autoassinado como parte deste processo. Em vários nós, tem de fornecer um certificado adequado.

    Se tiver de fornecer o seu próprio certificado, terá do certificado seguinte:

    Um certificado de caráter universal para \*.dbadapter.\< região\>.\< fqdn externo\>. Este certificado tem de ser fidedigno, tal como faria ser emitido por uma autoridade de certificação. Ou seja, a cadeia de confiança tem de existir sem exigir certificados intermédios. Um certificado de site único pode ser utilizado com o nome VM explícito [mysqladapter] utilizado durante a instalação.


5. Abra um **novo** elevada consola do PowerShell (administrativa) e alteração para o diretório onde extraiu os ficheiros. Utilize uma nova janela para evitar problemas que podem surgir na incorretos módulos do PowerShell já carregados no sistema.

6. [Instalar o Azure PowerShell versão 1.2.11](azure-stack-powershell-install.md).

7. Execute o script de DeploySqlProvider.ps1.

O script efetua estes passos:

* Transfira o binário de conector MySQL (Isto pode ser fornecido offline).
* Carregar os certificados e outros artefactos para uma conta de armazenamento na pilha do Azure.
* Publica pacotes de galeria que pode implementar as bases de dados do SQL Server através da galeria.
* Publicar um pacote de galeria para implementar servidores de alojamento
* Implementar uma VM com a imagem do Windows Server 2016 criada no passo 1 e instale o fornecedor de recursos.
* Registe um registo DNS local, que mapeia para o fornecedor de recursos VM.
* Registe o fornecedor de recursos com o local do Azure Resource Manager (inquilino e administrador).


Pode:
- Especifique, pelo menos, os parâmetros necessários na linha de comandos
- em alternativa, se executar sem quaisquer parâmetros, introduza-las quando lhe for pedido.

Eis um exemplo que pode executar a partir do PowerShell solicitar (mas alterar as informações de conta e as palavras-passe, conforme necessário):


```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privleged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files
# Find the ERCS01 IP address first and make sure the certificate
# file is in the specified directory
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploysqlproviderps1-parameters"></a>Parâmetros de DeploySqlProvider.ps1
Pode especificar estes parâmetros na linha de comandos. Se não o fizer, ou qualquer parâmetro validação falhar, são-lhe pedido fornecer as necessárias.

| Nome do Parâmetro | Descrição | Comentário ou o valor predefinido |
| --- | --- | --- |
| **CloudAdminCredential** | A credencial para o administrador da nuvem, necessária para aceder ao ponto final Privleged. | _necessário_ |
| **AzCredential** | Forneça as credenciais da conta de administrador de serviço de pilha do Azure. Utilize as mesmas credenciais que utilizou para a implementação de pilha do Azure). | _necessário_ |
| **VMLocalCredential** | Defina as credenciais para a conta de administrador local do fornecedor de recursos de MySQL VM. | _necessário_ |
| **PrivilegedEndpoint** | Forneça o endereço IP ou nome de DNS do ponto final com privilégios. |  _necessário_ |
| **DependencyFilesLocalPath** | Caminho para uma partilha local que contém [mysql conector-net 6.9.9.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.9.9.msi). Se fornecer um, o ficheiro de certificado tem de ser colocado bem neste diretório. | _opcional_ (_obrigatório_ para vários nós) |
| **DefaultSSLCertificatePassword** | A palavra-passe para o certificado. pfx | _necessário_ |
| **MaxRetryCount** | Defina o número de vezes que pretende repetir a cada operação, se existir uma falha.| 2 |
| **RetryDuration** | Defina o limite de tempo entre tentativas, em segundos. | 120 |
| **Desinstalar** | Remover o fornecedor de recursos e recursos de todos os associados (ver notas abaixo) | Não |
| **DebugMode** | Impede a limpeza automática em caso de falha | Não |
| **AcceptLicense** | Ignora o pedido para aceitar a licença GPL (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


Consoante as velocidades de desempenho e a transferência de sistema, instalação pode demorar tão pequeno como 20 minutos ou longa como várias horas. Se o painel MySQLAdapter não estiver disponível, atualize o portal de administração.

> [!NOTE]
> Se a instalação demora mais de 90 minutos, poderá falhar e verá uma mensagem de falha no ecrã e no ficheiro de registo. A implementação é repetida do passo falhar. Sistemas que não cumprem as especificações de núcleo e de memória recomendadas poderão não conseguir implementar o MySQL RP.



## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verificar a implementação através do portal de pilha do Azure

> [!NOTE]
>  Quando o script de instalação estiver concluída, terá de atualizar o portal para ver o painel de administração.


1. Inicie sessão no portal de administração como o administrador de serviço.

2. Certifique-se de que a implementação concluída com êxito. Procurar **grupos de recursos** &gt;, clique em de **system.\< localização\>.mysqladapter** recursos de grupo e certifique-se de que todas as implementações de quatro teve êxito.

      ![Verificar a implementação de RP o MySQL](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Fornecem a capacidade de ligar a um servidor de alojamento MySQL

1. Inicie sessão no portal do Azure pilha como um administrador de serviço.

2. Navegue até à **recursos administrativos** &gt; **MySQL servidores de alojamento** &gt; **+ adicionar**.

    O **servidores de alojamento MySQL** painel é onde pode ligar o fornecedor de recursos do servidor de MySQL para instâncias reais do servidor de MySQL que servem de back-end do fornecedor de recursos.

    ![Servidores de alojamento](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Preencha o formulário com os detalhes de ligação da sua instância do servidor de MySQL. Forneça o nome de domínio completamente qualificado (FQDN) ou um endereço IPv4 válido e não o nome abreviado de VM. Esta instalação já não fornece uma instância de MySQL predefinida. O tamanho fornecido ajuda-o o gerir a capacidade de base de dados do fornecedor de recursos. Deve estar perto a capacidade do servidor da base de dados física.

    > [!NOTE]
    > A instância de MySQL pode ser acedida pelo inquilino e administrador do Azure Resource Manager, desde que pode ser colocado sob o controlo do fornecedor de recursos. A instância de MySQL __tem__ atribuída exclusivamente para o RP.

4. Como adicionar servidores, tem de atribuir-lhes um SKU de novo ou existente para permitir a diferenciação de ofertas de serviço.
  Por exemplo, pode ter uma instância de enterprise que fornecer:
    - capacidade de base de dados
    - Cópia de segurança automática
    - servidores de elevado desempenho para departamentos individuais de reserva
 

O nome do SKU deve refletir as propriedades, para que os inquilinos podem colocar as bases de dados adequadamente. Todos os servidores de alojamento de um SKU devem ter as mesmas capacidades.

![Criar um SKU de MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> SKUs podem demorar até uma hora ser visível no portal. Não é possível criar uma base de dados até que seja criada a SKU.


## <a name="to-test-your-deployment-create-your-first-mysql-database"></a>Para testar a implementação, crie a sua primeira base de dados MySQL


1. Inicie sessão no portal do Azure pilha como administrador de serviço.

2. Clique em de **+ novo** botão &gt; **dados + armazenamento** &gt; **base de dados MySQL**.

3. Preencha o formulário com os detalhes da base de dados.

    ![Criar um teste de base de dados MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Selecione um SKU.

    ![Selecione um SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Crie uma definição de início de sessão. A definição de início de sessão possa ser reutilizada ou criar um novo. Esta definição contém o nome de utilizador e palavra-passe para a base de dados.

    ![Criar um novo início de sessão de base de dados](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    A cadeia de ligações inclui o nome do servidor de base de dados real. Copie-a partir do portal.

    ![Obter a cadeia de ligação para a base de dados MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> O comprimento dos nomes de utilizador não pode exceder 32 carateres com MySQL 5.7 ou 16 carateres de edições anteriores.


## <a name="add-capacity"></a>Adicionar capacidade

Adicione capacidade adicionando servidores adicionais do MySQL no portal do Azure pilha. Servidores adicionais podem ser adicionados a um SKU de novo ou existente. Certifique-se que as características de servidor são os mesmos.


## <a name="make-mysql-databases-available-to-tenants"></a>Disponibilizar bases de dados MySQL aos inquilinos
Crie planos e as ofertas para disponibilizar as bases de dados MySQL para os inquilinos. Adicione o serviço de Microsoft.MySqlAdapter, adicione uma quota, etc.

![Criar planos e as ofertas para incluir as bases de dados](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>Atualizar a palavra-passe administrativa
Pode modificar a palavra-passe pelo primeiro alterá-lo na instância de servidor MySQL. Navegue até à **recursos administrativo** &gt; **servidores de alojamento MySQL** &gt; e clique no servidor de alojamento. No painel de definições, clique na palavra-passe.

![Atualizar a palavra-passe de administrador](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="remove-the-mysql-resource-provider-adapter"></a>Remova o adaptador de fornecedor de recursos de MySQL

Para remover o fornecedor de recursos, é essencial primeiro de remover as dependências.

1. Certifique-se de que tem o pacote de implementação original que transferiu para esta versão do fornecedor de recursos.

2. Devem ser apagadas todas as bases de dados do inquilino do fornecedor de recursos (não elimina os dados). Isto deve ser efetuado por si próprios inquilinos.

3. Os inquilinos tem de anular o registo do espaço de nomes.

4. Administrador tem de eliminar os servidores de alojamento da placa de MySQL

5. Administrador tem de eliminar quaisquer planos que referenciam o adaptador de MySQL.

6. Administrador tem de eliminar quaisquer quotas associadas à placa de MySQL.

7. Volte a executar o script de implementação-desinstalar parâmetro, pontos finais do Azure Resource Manager, DirectoryTenantID e as credenciais da conta de administrador de serviço.




## <a name="next-steps"></a>Passos seguintes

Tente outro [PaaS serviços](azure-stack-tools-paas-services.md) como o [fornecedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md) e [fornecedor de recursos de serviços aplicacionais](azure-stack-app-service-overview.md).
