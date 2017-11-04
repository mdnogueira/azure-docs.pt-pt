---
title: Criar cluster do Service Fabric no portal do Azure | Microsoft Docs
description: Este artigo descreve como configurar um cluster do Service Fabric seguro no Azure utilizando o portal do Azure e o Cofre de chaves do Azure.
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/21/2017
ms.author: chackdan
ms.openlocfilehash: 874cf647d4b708bbbc64246ac0dff133639ad86c
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Criar um cluster do Service Fabric no Azure através do portal do Azure
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Este é um guia passo a passo que explica os passos de configuração de um cluster do Service Fabric seguro no Azure utilizando o portal do Azure. Este guia explica os passos seguintes:

* Configure o Cofre de chaves para gerir as chaves de segurança do cluster.
* Crie um cluster protegido no Azure através do portal do Azure.
* Autentica os administradores a utilização de certificados.

> [!NOTE]
> Para opções de segurança mais avançadas, como a autenticação de utilizador no Azure Active Directory e como configurar certificados para a segurança da aplicação, [criar o cluster com o Azure Resource Manager][create-cluster-arm].
> 
> 

Um cluster seguro é um cluster que impede o acesso não autorizado às operações de gestão, que inclui implementar, atualizar e eliminar os dados que contêm, serviços e aplicações. Um cluster não seguro é um cluster que qualquer pessoa pode ligar a qualquer momento e efetuar operações de gestão. Embora seja possível criar um cluster não seguro, é **altamente recomendado para criar um cluster seguro**. Um cluster não seguro **não pode ser protegida mais tarde** -tem de ser criado um novo cluster.

Os conceitos são os mesmos para criação de clusters seguros, se os clusters são clusters do Linux ou clusters do Windows. Para mais informações e do programa auxiliar de scripts para a criação de clusters seguros do Linux, consulte [criação de clusters seguros no Linux](service-fabric-cluster-creation-via-arm.md#secure-linux-clusters). Os parâmetros obtidos pelo script de programa auxiliar fornecido podem ser introduzidos diretamente no portal, conforme descrito na secção [criar um cluster no portal do Azure](#create-cluster-portal).

## <a name="configure-key-vault"></a>Configurar o Cofre de chaves 
### <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Este guia utiliza [Azure PowerShell][azure-powershell]. Quando a partir de uma nova sessão do PowerShell, inicie sessão na sua conta do Azure e selecionar a sua subscrição antes de executar os comandos do Azure.

Inicie sessão sua conta do azure:

```powershell
Login-AzureRmAccount
```

Selecione a sua subscrição:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="set-up-key-vault"></a>Configurar o Cofre de Chaves
Esta parte do guia explica como criar um cofre de chave para um cluster do Service Fabric no Azure e para aplicações de Service Fabric. Para obter um guia completo no Cofre de chaves, consulte o [Cofre de chaves guia de introdução][key-vault-get-started].

O Service Fabric utiliza certificados x. 509 para proteger um cluster. O Cofre de chaves do Azure é utilizado para gerir os certificados para clusters de Service Fabric no Azure. Quando um cluster é implementado no Azure, o fornecedor de recursos do Azure responsável pela criação de clusters de Service Fabric obtém certificados a partir do Cofre de chaves e instala-los em VMs do cluster.

O diagrama seguinte ilustra a relação entre o Cofre de chaves, um cluster do Service Fabric e o fornecedor de recursos do Azure que utiliza certificados armazenados no Cofre de chaves quando cria um cluster:

![Instalação do certificado][cluster-security-cert-installation]

#### <a name="create-a-resource-group"></a>Criar um Grupo de Recursos
O primeiro passo é criar um novo grupo de recursos especificamente para o Cofre de chaves. É recomendado colocar o Cofre de chaves ao seu próprio grupo de recursos, de modo a que possa remover grupos de recursos de computação e armazenamento - como o grupo de recursos que tem o cluster do Service Fabric - sem perder as chaves e segredos. O grupo de recursos que tenha o seu Cofre de chaves deve estar na mesma região que o cluster que está a ser utilizado.

```powershell

    PS C:\Users\vturecek> New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet will be modified in a future release.

    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

#### <a name="create-key-vault"></a>Criar Cofre de chaves
Crie um cofre de chaves no novo grupo de recursos. O Cofre de chaves **tem de estar ativado para a implementação** para permitir que o fornecedor de recursos do Service Fabric obter certificados a partir do mesmo e a instalar em nós de cluster:

```powershell

    PS C:\Users\vturecek> New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment


    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```

Se tiver um cofre de chaves existente, pode ativá-la para implementação utilizando a CLI do Azure:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```


### <a name="add-certificates-to-key-vault"></a>Adicione certificados à Cofre de chaves
Os certificados são utilizados no Service Fabric para fornecer autenticação e encriptação para proteger diferentes aspetos de um cluster e as respetivas aplicações. Para obter mais informações sobre como os certificados são utilizados no Service Fabric, consulte [cenários de segurança de cluster do Service Fabric][service-fabric-cluster-security].

#### <a name="cluster-and-server-certificate-required"></a>Cluster e servidor de certificado (obrigatório)
Este certificado é necessário para proteger um cluster e impedir o acesso não autorizado ao mesmo. Fornece segurança de cluster de algumas formas:

* **Autenticação do cluster:** autentica o nó de nó de comunicação para a Federação de cluster. Apenas nós que podem provar a sua identidade com este certificado podem aderirem ao cluster.
* **Autenticação de servidor:** autentica pontos finais de gestão de cluster para um cliente de gestão, para que o cliente de gestão sabe o que é falar com o cluster real. Este certificado fornece também SSL para a API de gestão HTTPS e para o Service Fabric Explorer através de HTTPS.

Para efetuar estes fins, o certificado tem de cumprir os seguintes requisitos:

* O certificado tem de conter uma chave privada.
* O certificado tem de ser criado para a troca de chaves, exportável para um ficheiro Personal Information Exchange (. pfx).
* Nome do requerente do certificado deve corresponder ao domínio utilizado para aceder ao cluster do Service Fabric. Isto é necessário para fornecer o SSL para o cluster pontos finais de gestão HTTPS e Service Fabric Explorer. Não é possível obter um certificado SSL de uma autoridade de certificação (AC) para o `.cloudapp.azure.com` domínio. Adquirir um nome de domínio personalizado para o cluster. Quando solicitar um certificado a partir de uma AC nome do requerente do certificado tem de corresponder ao nome de domínio personalizado utilizado para o cluster.

#### <a name="client-authentication-certificates"></a>Certificados de autenticação de cliente
Certificados de cliente adicionais autenticar os administradores para tarefas de gestão do cluster. Serviço de recursos de infraestrutura tem dois níveis de acesso: **admin** e **utilizador só de leitura**. No mínimo, um único certificado para acesso administrativo deve ser utilizado. Para acesso de nível de utilizador adicionais, tem de ser fornecido um certificado diferente. Para obter mais informações sobre funções de acesso, consulte [controlo de acesso baseado em funções para clientes de Service Fabric][service-fabric-cluster-security-roles].

Não é necessário carregar certificados de autenticação de cliente para o Cofre de chaves para trabalhar com o Service Fabric. Estes certificados só tem de ser fornecida para os utilizadores que têm autorização para gestão de clusters. 

> [!NOTE]
> Azure Active Directory é a forma recomendada para autenticar clientes para operações de gestão do cluster. Para utilizar o Azure Active Directory, terá [criar um cluster com o Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certificados de aplicação (opcionais)
Qualquer número de certificados adicionais pode ser instalado num cluster por motivos de segurança da aplicação. Antes de criar o cluster, considere os cenários de segurança de aplicações que necessitam de um certificado para ser instalada em nós, tais como:

* Encriptação e desencriptação de valores de configuração de aplicação
* Encriptação dos dados em nós durante a replicação 

Certificados de aplicação não podem ser configurados quando criar um cluster através do portal do Azure. Para configurar certificados de aplicação no momento da configuração de cluster, terá [criar um cluster com o Azure Resource Manager][create-cluster-arm]. Também pode adicionar certificados de aplicação para o cluster, depois de terem sido criadas.

#### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formatação certificados para utilização do fornecedor de recursos do Azure
Ficheiros de chave privados (. pfx) podem ser adicionados e podem ser utilizados diretamente através do Cofre de chaves. No entanto, o fornecedor de recursos do Azure requer que sejam armazenadas num formato JSON especial que inclui o PFX como uma base-64 chaves codificado cadeia e a palavra-passe da chave privada. Para acomodar estes requisitos, chaves tem de ser colocadas numa cadeia JSON e, em seguida, armazenadas como *segredos* no Cofre de chaves.

Para facilitar este processo, um módulo do PowerShell está [está disponível no GitHub][service-fabric-rp-helpers]. Siga estes passos para utilizar o módulo:

1. Transferir todo o conteúdo do repositório para um diretório local. 
2. Importe o módulo na janela do PowerShell:

```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

O `Invoke-AddCertToKeyVault` comando neste módulo PowerShell automaticamente formatos uma chave privada do certificado numa cadeia JSON e carrega-o para o Cofre de chaves. Utilizá-lo para adicionar o certificado de cluster e quaisquer certificados de aplicação adicional ao Cofre de chaves. Repita este passo para todos os certificados adicionais que pretende instalar no seu cluster.

```powershell
PS C:\Users\vturecek> Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet will be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault


Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

Estes são todos os pré-requisitos de Cofre de chaves para configurar um modelo de Gestor de recursos de cluster de Service Fabric que instala os certificados para autenticação de nó, a segurança ponto a ponto de gestão e a autenticação e quaisquer funcionalidades de segurança de aplicações adicionais que utilizam certificados x. 509. Neste momento, agora, deve ter a seguinte configuração no Azure:

* Grupo de recursos do Cofre de chaves
  * Cofre de Chaves
    * Certificado de autenticação de servidor de cluster

< /a "Criar-cluster-portal" ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Criar cluster no portal do Azure
### <a name="search-for-the-service-fabric-cluster-resource"></a>Pesquisa para o recurso de cluster do Service Fabric
![pesquisa para o modelo de cluster do Service Fabric no portal do Azure.][SearchforServiceFabricClusterTemplate]

1. Inicie sessão no [Portal do Azure][azure-portal].
2. Clique em **novo** para adicionar um novo modelo de recursos. Pesquisa para o modelo de Cluster do Service Fabric no **Marketplace** em **tudo**.
3. Selecione **Cluster do Service Fabric** da lista.
4. Navegue para o **Cluster do Service Fabric** painel, clique em **criar**,
5. O **cluster do Service Fabric criar** painel tem os seguintes quatro passos.

#### <a name="1-basics"></a>1. Noções básicas
![Captura de ecrã da criação de um novo grupo de recursos.][CreateRG]

No painel Noções básicas tem de fornecer os detalhes básicos para o cluster.

1. Introduza o nome do cluster.
2. Introduza um **nome de utilizador** e **palavra-passe** para ambiente de trabalho remoto para as VMs.
3. Certifique-se de que seleciona o **subscrição** que pretende que o cluster ser implementado, especialmente se tiver várias subscrições.
4. Criar um **novo grupo de recursos**. É melhor atribuir-lhe o mesmo nome que o cluster, uma vez que o ajuda a localizar mais tarde, especialmente quando estiver a tentar efetuar alterações à sua implementação ou eliminar o cluster.
   
   > [!NOTE]
   > Embora pode optar por utilizar um grupo de recursos existente, é recomendável criar um novo grupo de recursos. Isto torna mais fácil eliminar clusters que não é necessário.
   > 
   > 
5. Selecione o **região** no qual pretende criar o cluster. Tem de utilizar a mesma região que consta do seu Cofre de chaves.

#### <a name="2-cluster-configuration"></a>2. Configuração de cluster
![Criar um tipo de nó][CreateNodeType]

Configure os nós do cluster. Tipos de nó definem os tamanhos VM, o número de VMs e as respetivas propriedades. O cluster pode ter mais do que um tipo de nó, mas o tipo de nó principal (aquele primeiro por si no portal) tem de ter, pelo menos, cinco VMs, que é o tipo de nó em que os serviços de sistema do Service Fabric são colocados. Não configure **as propriedades de colocação** porque uma propriedade de posicionamento predefinido de "NodeTypeName" é adicionada automaticamente.

> [!NOTE]
> Um cenário comum para vários tipos de nó é uma aplicação que contém um serviço front-end e um serviço de back-end. Pretende colocar o serviço de front-end em VMs mais pequenas (tamanhos de VM, como D2) com portas abertas à Internet, mas quiser colocar o serviço de back-end em VMs maior (com tamanhos de VM, como D4, D6, D15 e assim sucessivamente) com não abrir as portas para a Internet.
> 
> 

1. Escolha um nome para o seu tipo de nó (1 a 12 carateres apenas letras e números).
2. O mínimo **tamanho** de VMs para o nó principal tipo é conduzido pelos **durabilidade** camada que escolher para o cluster. A predefinição para o escalão de durabilidade é bronze. Para obter mais informações sobre a durabilidade, consulte [como escolher a durabilidade e fiabilidade de cluster do Service Fabric][service-fabric-cluster-capacity].
3. Selecione o tamanho da VM e o escalão de preço. VMs de série D tem unidades SSD e são altamente recomendadas para aplicações com monitorização de estado. Não utilizar qualquer SKU de VM que tenha núcleos parciais ou de ter menos de 7 GB de capacidade de disco disponível. 
4. O mínimo **número** de VMs para o nó principal, tipo é suscitada pelo departamento pelo **fiabilidade** camada que escolher. A predefinição para o escalão de fiabilidade é Silver. Para obter mais informações sobre a fiabilidade, consulte [como escolher a durabilidade e fiabilidade de cluster do Service Fabric][service-fabric-cluster-capacity].
5. Escolha o número de VMs para o tipo de nó. Pode aumentar ou reduzir verticalmente o número de VMs num tipo de nó mais tarde, mas o tipo de nó principal, o mínimo é conduzido pelo nível de fiabilidade que escolheu. Outros tipos de nó podem ter um mínimo de 1 VM.
6. Configure pontos finais personalizados. Este campo permite-lhe introduzir uma lista separada por vírgulas de portas que pretende expor através do Balanceador de carga do Azure para a Internet pública para as suas aplicações. Por exemplo, se planeia implementar uma aplicação web para o cluster, introduza "80" aqui para permitir tráfego na porta 80 para o cluster. Para obter mais informações sobre os pontos finais, consulte [comunicar com aplicações][service-fabric-connect-and-communicate-with-services]
7. Configurar o cluster **diagnóstico**. Por predefinição, o diagnóstico está ativado no seu cluster para ajudar na resolução de problemas. Se pretender desativar a alteração de diagnóstico a **estado** alternar para **desativar**. Desativar diagnostics é **não** recomendado.
8. Selecione o modo de atualização de recursos de infraestrutura que quiser como o seu cluster. Selecione **automática**, se pretender que o sistema para automaticamente processará a versão mais recente e tente atualizar o seu cluster ao mesmo. Definir o modo como **Manual**, se pretende escolher uma versão suportada.

> [!NOTE]
> Suportamos apenas os clusters que executam versões suportadas do serviço de recursos de infraestrutura. Ao selecionar o **Manual** modo, que está a efetuar da responsabilidade para atualizar o cluster para uma versão suportada. Para obter mais detalhes sobre os recursos de infraestrutura atualizar consulte de modo a [documento de serviço--cluster-atualização do fabric.][service-fabric-cluster-upgrade]
> 
> 

#### <a name="3-security"></a>3. Segurança
![Captura de ecrã das configurações de segurança no portal do Azure.][SecurityConfigs]

O último passo consiste em fornecer as informações do certificado para proteger o cluster com o Cofre de chaves e certificados informações criada anteriormente.

* Preencha os campos de certificado principal com a saída obtida de carregar o **certificado de cluster** ao Cofre de chaves utilizando o `Invoke-AddCertToKeyVault` comando do PowerShell.

```powershell
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47
```

* Verifique o **configurar as definições avançada** caixa para introduzir os certificados de cliente para **admin cliente** e **clientes apenas de leitura**. Nestes campos, introduza o thumbprint do certificado de cliente a administração e o thumbprint do certificado de cliente do utilizador só de leitura, se aplicável. Quando os administradores tentam ligar ao cluster, são concedidos acesso apenas se tiverem um certificado com um thumbprint que correspondem aos valores thumbprint introduzido aqui.  

#### <a name="4-summary"></a>4. Resumo

Para concluir a criação do cluster, clique em **resumo** para ver as configurações que lhe forneceu ou transferir o modelo Azure Resource Manager que que utilizados para implementar o cluster. Depois de ter fornecido as definições obrigatórias, o **OK** botão fica verde e pode iniciar o processo de criação do cluster clicando nela.

Pode ver o progresso da criação nas notificações. (Clique no ícone de "Sino", junto a barra de estado no canto superior direito do ecrã.) Se clicou em **afixar ao Startboard** ao criar o cluster, verá **implementação de Cluster do Service Fabric** afixado o **iniciar** quadro.

### <a name="view-your-cluster-status"></a>Ver o estado do cluster
![Captura de ecrã dos detalhes de cluster no dashboard.][ClusterDashboard]

Depois do cluster foi criado, pode inspecionar o seu cluster no portal:

1. Aceda a **procurar** e clique em **Clusters de recursos de infraestrutura de serviço**.
2. Localize o seu cluster e clique no mesmo.
3. Agora pode ver os detalhes do seu cluster no dashboard, incluindo o ponto final público do cluster e uma ligação para o Service Fabric Explorer.

O **Monitor nó** secção no painel de dashboard do cluster indica o número de VMs que fazem o bom estado de funcionamento e não em bom estado. Pode encontrar mais detalhes sobre o estado de funcionamento do cluster em [introdução de modelo de estado de funcionamento do Service Fabric][service-fabric-health-introduction].

> [!NOTE]
> Clusters de Service Fabric necessitam de um determinado número de nós se sempre manter a disponibilidade e preservar o estado - referido como "manutenção quórum". Por conseguinte, normalmente, não é seguro encerrar todas as máquinas do cluster, exceto se tiver efetuado primeiro um [cópia de segurança completa do seu estado][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Remoto ligar a uma instância de conjunto de dimensionamento da Máquina Virtual ou um nó de cluster
Cada um dos NodeTypes especificou nos resultados de cluster num conjunto de dimensionamento de Máquina Virtual ao obter a configuração. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Passos seguintes
Neste momento, tiver um cluster seguro que utilizar certificados para autenticação de gestão. Em seguida, [ligar ao cluster](service-fabric-connect-to-secure-cluster.md) e saber como [gerir segredos de aplicação](service-fabric-application-secret-management.md).  Além disso, saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
<!--[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node -->
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
