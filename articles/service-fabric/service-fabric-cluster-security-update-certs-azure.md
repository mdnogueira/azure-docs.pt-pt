---
title: Gerir certificados de um cluster do Service Fabric do Azure | Microsoft Docs
description: Descreve como adicionar novos certificados, o certificado de rollover e remover o certificado de ou para um cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2017
ms.author: chackdan
ms.openlocfilehash: c433e8683755e454f9561f094269c3daccf78a62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Adicionar ou remover certificados para um cluster do Service Fabric no Azure
É recomendado que familiarizar-se com a forma como o Service Fabric utiliza certificados x. 509 e deve estar familiarizado com o [cenários de segurança do Cluster](service-fabric-cluster-security.md). Tem de compreender que um certificado de cluster e o que é utilizado para, antes de prosseguir.

Recursos de infraestrutura de serviço permite-lhe especificar os dois certificados de cluster, um servidor principal e secundária, quando configurar a segurança de certificado durante a criação do cluster, além de certificados de cliente. Consulte [criação de um cluster do azure através do portal](service-fabric-cluster-creation-via-portal.md) ou [criação de um cluster do azure através do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para obter detalhes sobre configurá-los em tempo de criação. Se especificar apenas um certificado de cluster em tempo de criação, em seguida, que é utilizado como o certificado principal. Após a criação do cluster, pode adicionar um novo certificado como uma secundária.

> [!NOTE]
> Para um cluster seguro, sempre precisa de, pelo menos, um cluster (não revogado e não expirado) válido certificado (primário ou secundário) implementada (caso contrário, a paragem do cluster a funcionar). 90 dias antes de todos os certificados válidos alcancem expiração, o sistema gera um rastreio de aviso e também um evento de estado de funcionamento de aviso no nó. Não há atualmente nenhum e-mail ou qualquer notificação que recursos de infraestrutura de serviço envia a sobre este tópico. 
> 
> 

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Adicionar um certificado de cluster secundária através do portal

Não é possível adicionar o certificado de cluster secundária através do portal do Azure. Tem de utilizar o Azure powershell para esse. O processo é descrito posteriormente neste documento.

## <a name="swap-the-cluster-certificates-using-the-portal"></a>Os certificados de cluster utilizando o portal de comutação

Depois de ter implementado com êxito um certificado de cluster secundária, se pretender que o servidor primário e secundário de comutação, navegue para o painel de segurança e selecione a opção 'Troca com primário' no menu de contexto ao trocar o certificado com o certificado principal secundário.

![Certificado de troca][Delete_Swap_Cert]

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Remover um certificado de cluster através do portal

Para um cluster seguro, é sempre necessário pelo menos um válido (não revogado e não expirado) certificado (primário ou secundário) implementado caso contrário, o cluster deixa de funcionar.

Para remover um certificado secundário de que está a ser utilizada para segurança do cluster, navegue para o painel de segurança e selecione a opção 'Eliminar', no menu de contexto no certificado secundário.

Se a intenção é ao remover o certificado que está marcado como principal, em seguida, terá de trocar primeiro com secundário e, em seguida, eliminar secundário após a atualização foi concluída.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Adicionar um certificado secundário com o Resource Manager Powershell

Estes passos partem do princípio de que está familiarizado com o funcionamento do Gestor de recursos e implementou a, pelo menos, um dos recursos de infraestrutura do serviço cluster com um modelo do Resource Manager e ter o modelo que utilizou para configurar o cluster à mão. Também é suposto que está familiarizado com JSON.

> [!NOTE]
> Se estiver à procura de um modelo de exemplo e os parâmetros que pode utilizar a seguir ao longo ou como um ponto de partida, em seguida, transfira-deste [repositório do git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Editar o modelo do Resource Manager

Para facilitar a seguinte ao longo, o exemplo 5-VM-1-NodeTypes-Secure_Step2.JSON contém todas as edições que irá ser efetuar. o exemplo está disponível em [repositório do git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Certifique-se que siga todos os passos**

**Passo 1:** abra o modelo do Resource Manager que utilizou para implementar o Cluster. (Se tiver transferido o exemplo do repositório acima, em seguida, utilize 5-VM-1-NodeTypes-Secure_Step1.JSON para implementar um cluster seguro e, em seguida, abrir esse modelo).

**Passo 2:** adicionar **dois parâmetros novos** "secCertificateThumbprint" e "secCertificateUrlValue" do tipo "cadeia" para a secção de parâmetro do seu modelo. Pode copiar o seguinte fragmento de código e adicione-o para o modelo. Consoante a origem do seu modelo, pode já ter o estes definidas, por isso, mover para o passo seguinte. 
 
```JSON
   "secCertificateThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Thumbprint"
      }
    },
    "secCertificateUrlValue": {
      "type": "string",
      "metadata": {
        "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
      }
    },

```

**Passo 3:** efetuar alterações para o **Microsoft.ServiceFabric/clusters** recurso - localize a definição do recurso "Microsoft.ServiceFabric/clusters" no modelo. Em Propriedades de que a definição, encontrará "Certificate" JSON etiqueta, que deve ter um aspeto semelhante o fragmento JSON seguinte:

   
```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
     }
``` 

Adicionar uma nova etiqueta "thumbprintSecondary" e atribua-lhe um valor "[parameters('secCertificateThumbprint')]".  

Sim agora a definição do recurso deve ter um aspeto semelhante ao seguinte (consoante a origem do modelo, não poderá exatamente como o fragmento abaixo). 

```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
     }
``` 

Se pretender **rollover cert**, em seguida, especifique o novo certificado como primário e mover o principal atual como elemento secundário. Isto resulta no rollover do seu certificado primário atual para o novo certificado num único passo de implementação.

```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('secCertificateThumbprint')]",
          "thumbprintSecondary": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
     }
``` 


**Passo 4:** efetuar alterações aos **todos os** o **Microsoft.Compute/virtualMachineScaleSets** definições de recursos - localizar o recurso de Microsoft.Compute/virtualMachineScaleSets definição. Desloque-se para "publisher": "Microsoft.Azure.ServiceFabric", em "virtualMachineProfile".

As definições de publicador de recursos de infraestrutura de serviço, deverá ver algo semelhante ao seguinte.

![Json_Pub_Setting1][Json_Pub_Setting1]

Adicionar as novas entradas de certificado

```JSON
               "certificateSecondary": {
                    "thumbprint": "[parameters('secCertificateThumbprint')]",
                    "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },

```

As propriedades devem agora ter este aspeto

![Json_Pub_Setting2][Json_Pub_Setting2]

Se pretender **rollover cert**, em seguida, especifique o novo certificado como primário e mover o principal atual como elemento secundário. Isto resulta no rollover do certificado atual para o novo certificado num único passo de implementação. 


```JSON
               "certificate": {
                   "thumbprint": "[parameters('secCertificateThumbprint')]",
                   "x509StoreName": "[parameters('certificateStoreValue')]"
                     },
               "certificateSecondary": {
                    "thumbprint": "[parameters('certificateThumbprint')]",
                    "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },

```
As propriedades devem agora ter este aspeto

![Json_Pub_Setting3][Json_Pub_Setting3]


**Passo 5:** efetuar alterações aos **todos os** o **Microsoft.Compute/virtualMachineScaleSets** definições de recursos - localizar o recurso de Microsoft.Compute/virtualMachineScaleSets definição. Desloque-se para "vaultCertificates":, em "OSProfile". deve ser algo semelhante.


![Json_Pub_Setting4][Json_Pub_Setting4]

Adicione o secCertificateUrlValue ao mesmo. Utilize o seguinte fragmento:

```Json
                  {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('secCertificateUrlValue')]"
                  }

```
Agora o Json resultante deverá ter um aspeto semelhante ao seguinte.
![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Certifique-se de que tem repetido os passos 4 e 5 para todas as definições do recurso de Nodetypes/Microsoft.Compute/virtualMachineScaleSets no modelo. Se perder um deles, o certificado será não obter instalado em que VMSS e terão resultados imprevisíveis no seu cluster, incluindo o cluster vai para baixo (Se acaba por ficar com nenhuma certificados válidos que pode utilizar o cluster para segurança. Por isso, verifique duplo, antes de prosseguir.
> 
> 


### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Edite o ficheiro de modelo para refletir os parâmetros novos que adicionou acima
Se estiver a utilizar o exemplo a [repositório do git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) para acompanhar, pode começar a efetuar alterações no exemplo 5-VM-1-NodeTypes-Secure.paramters_Step2.JSON 

Editar o ficheiro de parâmetro de modelo do Resource Manager, adicione os dois parâmetros novos para secCertificateThumbprint e secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Implementar o modelo para o Azure

- Agora está pronto para implementar o modelo para o Azure. Abra uma linha de comandos de versão 1 + Azure PS.
- Inicie sessão na sua conta do Azure e selecione a subscrição do azure específica. Este é um passo importante para utilizadores que tenham acesso a mais do que uma subscrição do azure.

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

Teste o modelo antes de implementar a política. Utilize o mesmo grupo de recursos que o cluster está atualmente implementado.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Implemente o modelo para o grupo de recursos. Utilize o mesmo grupo de recursos que o cluster está atualmente implementado. Execute o comando New-AzureRmResourceGroupDeployment. Não é necessário especificar o modo, uma vez que o valor predefinido é **incremental**.

> [!NOTE]
> Se definir o modo como concluída, pode eliminar inadvertidamente recursos que não estão no seu modelo. Por isso, não utilizá-lo neste cenário.
> 
> 

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Eis um exemplo de manchas saída do powershell do mesmo.

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

Assim que a implementação estiver concluída, ligar ao cluster com o novo certificado e executar algumas consultas. Se estiver a efetuar. Em seguida, pode eliminar o certificado antigo. 

Se estiver a utilizar um certificado autoassinado, não se esqueça para importá-los para o arquivo de certificados TrustedPeople local.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Para referência rápida, eis o comando para ligar a um cluster seguro 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Para referência rápida, eis o comando para obter o estado de funcionamento do cluster

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-application-certificates-to-the-cluster"></a>Implementação de certificados de aplicações em cluster.

Pode utilizar os mesmos passos conforme descritos em 5 passos acima para ter certificados implementados a partir de um keyvault a nós. apenas tem de definir e utilizar parâmetros diferentes.


## <a name="adding-or-removing-client-certificates"></a>Adicionar ou remover certificados de cliente

Para além dos certificados de cluster, pode adicionar certificados de cliente para executar operações de gestão no cluster do service fabric.

Pode adicionar dois tipos de certificados de cliente - Admin ou só de leitura. Em seguida, estes podem ser utilizados para controlar o acesso às operações de admin e operações de consulta no cluster. Por predefinição, os certificados de cluster são adicionados à lista de certificados permitidos do administrador.

Pode especificar qualquer número de certificados de cliente. Cada adições/eliminações resulta numa configuração atualização para o cluster do service fabric


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>A adição de certificados de cliente - Admin ou só de leitura através do portal

1. Navegue para o painel de segurança e selecione o '+ de autenticação' botão na parte superior do painel de segurança.
2. No painel de 'Adicionar autenticação', escolha o "tipo de autenticação' - 'Client só de leitura' ou 'Admin client'
3. Agora, escolha o método de autorização. Esta definição indica ao Service Fabric se deve ser se este certificado utilizando o nome do requerente ou o thumbprint. Em geral, não é uma boa prática de segurança para utilizar o método de autorização do nome do requerente. 

![Adicionar o certificado de cliente][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Eliminação de certificados de cliente - Admin ou só de leitura com o portal

Para remover um certificado secundário de que está a ser utilizada para segurança do cluster, navegue para o painel de segurança e selecione a opção 'Eliminar', no menu de contexto no certificado específico.



## <a name="next-steps"></a>Passos seguintes
Estes artigos para obter mais informações sobre a gestão de clusters de leitura:

* [Processo de atualização de Cluster do Service Fabric e as expectativas do utilizador](service-fabric-cluster-upgrade.md)
* [Configurar o acesso baseado em funções para clientes](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Delete_Swap_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_09.PNG
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


