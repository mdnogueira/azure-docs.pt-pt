
<properties
   pageTitle="Criar um gateway de aplicação com os modelos Azure Resource Manager | Microsoft Azure"
   description="Esta página fornece instruções para criar um gateway de aplicação do Azure com o modelo Azure Resource Manager"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="gwallace"/>


# Criar um gateway de aplicação com o modelo Azure Resource Manager

O Application Gateway do Azure é um balanceador de carga de 7 camadas. Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diversos servidores, quer estejam na nuvem ou no local. O Aplicação Gateway tem as seguintes funcionalidades de entrega de aplicações: balanceamento de carga HTTP, afinidade de sessão com base em cookies e descarga de SSL (Secure Sockets Layer).

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Modelo Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
- [CLI do Azure](application-gateway-create-gateway-cli.md)

<BR>

Neste artigo, ficará a saber como transferir e modificar um modelo Azure Resource Manager existente a partir do GitHub e como implementar o modelo a partir do GitHub, do PowerShell e da CLI do Azure.

Se estiver a implementar o modelo Azure Resource Manager diretamente do GitHub sem quaisquer alterações, avance para implementar um modelo a partir do GitHub.


## Cenário

Neste cenário, vai:

- Criar um gateway de aplicação com duas instâncias.
- Criar uma rede virtual denominada VirtualNetwork1 com um bloco CIDR reservado de 10.0.0.0/16.
- Criar uma sub-rede denominada Appgatewaysubnet que utiliza 10.0.0.0/28 como bloco CIDR.
- Configurar dois IPs de back-end configurados anteriormente para os servidores Web para os quais pretende balancear o tráfego. Neste exemplo de modelo, os IPs de back-end são 10.0.1.10 e 10.0.1.11.

>[AZURE.NOTE] Estas definições são os parâmetros para este modelo. Para personalizar o modelo, pode alterar as regras, o serviço de escuta e o SSL que abre o azuredeploy.json.



![Cenário](./media/application-gateway-create-gateway-arm-template/scenario-arm.png)



## Transferir e compreender o modelo Azure Resource Manager

Pode transferir o modelo Azure Resource Manager existente para criar uma rede virtual e duas sub-redes a partir do GitHub, efetuar quaisquer alterações que pretenda e reutilizá-lo. Para tal, siga os passos abaixo:

1. Aceda a [Criar Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create).
2. Clique em **azuredeploy.json** e em **RAW**.
3. Guarde o ficheiro numa pasta local do computador.
4. Se estiver familiarizado com os modelos Azure Resource Manager, avance para o passo 7.
5. Abra o ficheiro que guardou e observe o conteúdo em **parâmetros**, na linha 5. Os parâmetros do modelo Azure Resource Manager fornecem um marcador de posição para valores que podem ser preenchidos durante a implementação.

  	| Parâmetro | Descrição |
  	|---|---|
  	| **localização** | Região do Azure onde é criado o gateway de aplicação |
  	| **VirtualNetwork1** | Nome da nova rede virtual |
  	| **addressPrefix** | Espaço de endereços da rede virtual, no formato CIDR |
  	| **ApplicationGatewaysubnet** | Nome da sub-rede do gateway de aplicação |
  	| **subnetPrefix** | Bloco CIDR da sub-rede do gateway de aplicação |
  	| **skuname** | Tamanho da instância SKU |
  	| **capacidade** | Número de instâncias |
  	| **backendaddress1** | Endereço IP do primeiro servidor Web |
  	| **backendaddress2** | Endereço IP do segundo servidor Web |


    >[AZURE.IMPORTANT] Os modelos Azure Resource Manager mantidos no GitHub podem ser alterados ao longo do tempo. Verifique o modelo antes de o utilizar.

6. Verifique o conteúdo em **recursos** e tenha em atenção o seguinte:

    - **tipo**. Tipo de recurso a ser criado pelo modelo. Neste caso, o tipo é **Microsoft.Network/applicationGateways**, o que representa um gateway de aplicação.
    - **nome**. Nome do recurso. Tenha em atenção a utilização de **[parameters("NomeDoGatewayDaAplicação")]**, que significa que o nome é fornecido como uma entrada por si ou por um ficheiro de parâmetros durante a implementação.
    - **propriedades**. Lista de propriedades do recurso. Este modelo utiliza a rede virtual e o endereço IP público durante a criação do gateway de aplicação.

7. Navegue de volta para [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create).
8. Clique em **azuredeploy-paremeters.json** e em **RAW**.
9. Guarde o ficheiro numa pasta local do computador.
10. Abra o ficheiro que guardou e edite os valores dos parâmetros. Utilize os seguintes valores para implementar o gateway de aplicação descrito no nosso cenário.

        {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        {
        "location" : {
        "value" : "West US"
        },
        "addressPrefix": {
        "value": "10.0.0.0/16"
        },
        "subnetPrefix": {
        "value": "10.0.0.0/24"
        },
        "skuName": {
        "value": "Standard_Small"
        },
        "capacity": {
        "value": 2
        },
        "backendIpAddress1": {
        "value": "10.0.1.10"
        },
        "backendIpAddress2": {
        "value": "10.0.1.11"
        }
        }

11. Guarde o ficheiro. Pode testar o modelo JSON e o modelo de parâmetros com as ferramentas de validação JSON online como [JSlint.com](http://www.jslint.com/).

## Implementar o modelo Azure Resource Manager com o PowerShell

Se nunca tiver utilizado o Azure PowerShell, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](../powershell-install-configure.md) e siga as instruções para iniciar sessão no Azure e selecionar a sua subscrição.

### Passo 1

    Login-AzureRmAccount



### Passo 2

Verifique as subscrições da conta.

    Get-AzureRmSubscription

Ser-lhe-á solicitado a autenticação com as suas credenciais.<BR>

### Passo 3

Escolha qual das subscrições do Azure utilizar. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Passo 4


Se necessário, crie um grupo de recursos com o cmdlet **New-AzureResourceGroup**. No exemplo abaixo, irá criar um novo grupo de recursos denominado AppgatewayRG na localização EUA Leste.

    New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"

        ResourceGroupName : AppgatewayRG
        Location          : eastus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                     Actions  NotActions
                     =======  ==========
                      *

        ResourceId        : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/AppgatewayRG

Execute o cmdlet **New-AzureRmResourceGroupDeployment** para implementar a nova rede virtual com o modelo anterior e os ficheiros de parâmetros transferidos e alterados.

    New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
        -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

A linha de comandos gera o seguinte:

    DeploymentName    : testappgatewaydeployment
    ResourceGroupName : appgatewayRG
    ProvisioningState : Succeeded
    Timestamp         : 9/19/2015 1:49:41 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                location         String                     East US
                addressPrefix    String                     10.0.0.0/16
                subnetPrefix     String                     10.0.0.0/24
                skuName          String                     Standard_Small
                capacity         Int                        2
                backendIpAddress1  String                     10.0.1.10
                backendIpAddress2  String                     10.0.1.11

    Outputs           :


## Implementar o modelo Azure Resource Manager com a CLI do Azure

Para implementar o modelo Azure Resource Manager que transferiu com a CLI do Azure, siga os passos abaixo:

### Passo 1

Se nunca tiver utilizado a CLI do Azure, veja [Install and configure the Azure CLI (Instalar e configurar a CLI do Azure)](../xplat-cli-install.md) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
### Passo 2

Execute o comando **azure config mode** para mudar para o modo Resource Manager, como mostrado abaixo.

    azure config mode arm

O resultado esperado para o comando acima é o seguinte:

    info:   New mode is arm

### Passo 3

Se necessário, execute o comando **azure group create** para criar um novo grupo de recursos, como mostrado abaixo. Repare no resultado do comando. A lista apresentada depois do resultado explica os parâmetros utilizados.  Para obter mais informações sobre os grupos de recursos, veja [Descrição geral do Azure Resource Manager](../resource-group-overview.md).

    azure group create -n appgatewayRG -l eastus

**-n (ou --name)**. Nome do novo grupo de recursos. Para o nosso cenário, o nome é *appgatewayRG*.

**-l (ou --location)**. Região do Azure onde é criado o novo grupo de recursos. Para o nosso cenário, a região é *eastus*.

### Passo 4

Execute o cmdlet **azure group deployment create** para implementar a nova rede virtual com o modelo e os ficheiros de parâmetros que transferiu e alterou acima. A lista apresentada depois do resultado explica os parâmetros utilizados.

    azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

O resultado esperado para o comando acima é o seguinte:

    azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "TestAppgatewayDeployment"
    + Waiting for deployment to complete
    data:    DeploymentName     : TestAppgatewayDeployment
    data:    ResourceGroupName  : appgatewayRG
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-09-21T20:50:27.5129912Z
    data:    Mode               : Incremental
    data:    Name               Type    Value
    data:    -----------------  ------  --------------
    data:    location           String  East US
    data:    addressPrefix      String  10.0.0.0/16
    data:    subnetPrefix       String  10.0.0.0/24
    data:    skuName            String  Standard_Small
    data:    capacity           Int     2
    data:    backendIpAddress1  String  10.0.1.10
    data:    backendIpAddress2  String  10.0.1.11
    info:    group deployment create command OK

**-g (ou --resource-group)**. Nome do grupo de recursos onde é criada a nova rede virtual.

**-f (ou --template-file)**. Caminho do ficheiro de modelo Azure Resource Manager.

**-e (ou --parameters-file)**. Caminho do ficheiro de parâmetros do Azure Resource Manager.

## Implementar o modelo Azure Resource Manager com a função clicar para implementar

A função clicar para implementar é outra forma de utilizar os modelos Azure Resource Manager. É uma forma fácil de utilizar modelos com o Portal do Azure.


### Passo 1
Veja [Create an application gateway with public IP (Criar um gateway de aplicação com IP público)](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/).


### Passo 2

Clique em **Implementar no Azure**.

![Implementar no Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### Passo 3

Preencha os parâmetros do modelo de implementação no portal e clique em **OK**.

![Parâmetros](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### Passo 4

Selecione **Termos legais** e clique em **Comprar**.

### Passo 5

No painel Implementação personalizada, clique em **Criar**.



## Passos seguintes

Se pretender configurar a descarga de SSL, veja [Configure an application gateway for SSL offload (Configurar um gateway de aplicação para a descarga de SSL)](application-gateway-ssl.md).

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interno, veja [Create an application gateway with an internal load balancer (ILB) (Criar um gateway de aplicação com um balanceador de carga interno (ILB))](application-gateway-ilb.md).

Se pretender obter mais informações sobre as opções de balanceamento de carga em geral, veja:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Traffic Manager do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=ago16_HO5-->


