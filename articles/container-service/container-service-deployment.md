<properties
   pageTitle="Implementar um cluster do Serviço de Contentor do Azure | Microsoft Azure"
   description="Implemente um cluster do Serviço de Contentor do Azure utilizando o Portal do Azure, a CLI do Azure ou o PowerShell."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contentores, Microserviços, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>


# Implementar um cluster do Serviço de Contentor do Azure

O Serviço de Contentor do Azure fornece uma implementação rápida de soluções de orquestração e de clustering populares e de open source do contentor. Ao utilizar o Serviço de Contentor do Azure, pode implementar clusters DC/OS e Docker Swarm com modelos do Azure Resource Manager ou o Portal do Azure. Deve implementar esses clusters com Conjuntos de Dimensionamento das Virtual Machines do Azure. Assim, os clusters tiram partido das ofertas de redes e de armazenamento do Azure. Para aceder ao Serviço de Contentor do Azure, terá de ter uma subscrição do Azure. Se não tiver uma, pode inscrever-se para obter uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Este documento explica como implementar um cluster do Serviço de Contentor do Azure utilizando o [Portal do Azure](#creating-a-service-using-the-azure-portal), a [Interface de linha de comandos (CLI) do Azure](#creating-a-service-using-the-azure-cli) e o [módulo Azure PowerShell](#creating-a-service-using-powershell).  

## Criar um serviço utilizando o Portal do Azure

Inicie sessão no portal do Azure, selecione **Novo** e procure o Azure Marketplace em **Serviço de Contentor do Azure**.

![Criar implementação 1](media/acs-portal1.png)  <br />

Selecione **Serviço de Contentor do Azure** e clique em **Criar**.

![Criar implementação 2](media/acs-portal2.png)  <br />

Introduza as seguintes informações:

- **Nome de utilizador**: este é o nome de utilizador que será utilizado para uma conta em cada uma das máquinas virtuais e nos conjuntos de dimensionamento de máquinas virtuais no cluster do Serviço de Contentor do Azure.
- **Subscrição**: selecione uma subscrição do Azure.
- **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo.
- **Localização**: selecione uma região do Azure para a implementação do Serviço de Contentor do Azure.
- **Chave pública SSH**: adicione a chave pública que será utilizada para autenticação nas máquinas virtuais do Serviço de Contentor do Azure. É muito importante que esta chave não contenha quebras de linha e que inclua o prefixo “ssh-rsa” e o sufixo “nomedeutilizador@domínio”. Deve ser algo semelhante ao seguinte: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. Para obter orientações sobre a criação de chaves Secure Shell (SSH), veja os artigos [Linux]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) e [Windows]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/).

Clique em **OK** quando estiver pronto para continuar.

![Criar implementação 3](media/acs-portal3.png)  <br />

Selecione um tipo de Orquestração. As opções são:

- **DC/OS**: implementa um cluster DC/OS.
- **Swarm**: implementa um cluster Docker Swarm.

Clique em **OK** quando estiver pronto para continuar.

![Criar implementação 4](media/acs-portal4.png)  <br />

Introduza as seguintes informações:

- **Contagem de modelos de estrutura mestres**: o número de modelos de estrutura mestres no cluster.
- **Contagem de agentes**: para o Docker Swarm, este será o número inicial de agentes no conjunto de dimensionamento dos agentes. Para o DC/OS, este será o número inicial de agentes num conjunto de dimensionamento privado. Além disso, é criado um conjunto de dimensionamento público, que contém um número pré-determinado de agentes. O número de agentes neste conjunto de dimensionamento público é determinado pelo número de modelos de estrutura mestres criados no cluster: um agente público para um modelo de estrutura mestre e dois agentes públicos para três ou cinco modelos de estrutura mestres.
- **Tamanho das máquinas virtuais dos agentes**: o tamanho das máquinas virtuais dos agentes.
- **Prefixo DNS**: um nome exclusivo a nível mundial que será utilizado para o prefixo das partes de chave dos nomes de domínio completamente qualificados do serviço.

Clique em **OK** quando estiver pronto para continuar.

![Criar implementação 5](media/acs-portal5.png)  <br />

Clique em **OK** após a conclusão da validação de serviço.

![Criar implementação 6](media/acs-portal6.png)  <br />

Clique em **Criar** para iniciar o processo de implementação.

![Criar implementação 7](media/acs-portal7.png)  <br />

Se optou por afixar a implementação no portal do Azure, o estado da implementação pode ser visto.

![Criar implementação 8](media/acs-portal8.png)  <br />

Quando concluir a implementação, o cluster do Serviço de Contentor do Azure estará pronto para ser utilizado.

## Criar um serviço utilizando a CLI do Azure

Para criar uma instância do Serviço de Contentor do Azure utilizando a linha de comandos, necessita de uma subscrição do Azure. Se não tiver uma, pode inscrever-se para obter uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). Terá também de ter [instalado](../xplat-cli-install.md) e [configurado](../xplat-cli-connect.md) a CLI do Azure.

Para implementar um cluster Docker Swarm ou DC/OS, selecione um dos modelos seguintes no GitHub. Tenha em atenção que estes modelos são os mesmos, com exceção da seleção do orquestrador predefinido.

* [Modelo do DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modelo do Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Em seguida, certifique-se de que a CLI do Azure foi ligada a uma subscrição do Azure. Pode fazê-lo utilizando o comando seguinte:

```bash
azure account show
```
Se não for devolvida uma conta do Azure, utilize o comando seguinte para iniciar sessão na CLI do Azure.

```bash
azure login -u user@domain.com
```

Em seguida, configure as ferramentas da CLI do Azure para utilizar o Azure Resource Manager.

```bash
azure config mode arm
```

Crie um cluster do grupo de recursos e do Serviço de Contentor do Azure com o comando seguinte, em que:

- **RESOURCE_GROUP** é o nome do grupo de recursos que pretende utilizar para este serviço.
- **LOCATION** é a região do Azure onde será criada a implementação do grupo de recursos e do Serviço de Contentor do Azure.
- **TEMPLATE_URI** é a localização do ficheiro de implementação. Tenha em atenção que este tem de ser o ficheiro Raw, não um ponteiro para a IU do GitHub. Para localizar este URL, selecione o ficheiro azuredeploy.json no GitHub e clique no botão **Raw**.

> [AZURE.NOTE] Quando executa este comando, a shell irá solicitar-lhe os valores dos parâmetros de implementação.

```bash
azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### Fornecer os parâmetros do modelo

Esta versão do comando requer que defina os parâmetros de forma interativa. Se pretender fornecer parâmetros, tal como uma cadeia formatada em JSON, pode fazê-lo utilizando o comutador `-p`. Por exemplo:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

Em alternativa, pode fornecer um ficheiro de parâmetros formatados em JSON utilizando o comutador `-e`:

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

Para ver um ficheiro de parâmetros de exemplo denominado `azuredeploy.parameters.json`, procure-o com os modelos do Serviço de Contentor do Azure no GitHub.

## Criar um serviço utilizando o PowerShell

Também é possível implementar um cluster do Serviço de Contentor do Azure com o PowerShell. Este documento baseia-se na versão 1.0 do [módulo do Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

Para implementar um cluster DC/OS ou Docker Swarm, selecione um dos seguintes modelos. Tenha em atenção que estes modelos são os mesmos, com exceção da seleção do orquestrador predefinido.

* [Modelo do DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modelo do Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Antes de criar um cluster na subscrição do Azure, certifique-se de que a sua sessão do PowerShell iniciou sessão no Azure. Pode fazer isto com o comando `Get-AzureRMSubscription`:

```powershell
Get-AzureRmSubscription
```

Se precisar de iniciar sessão no Azure, utilize o comando `Login-AzureRMAccount`:

```powershell
Login-AzureRmAccount
```

Se estiver a implementar num novo grupo de recursos, tem primeiro de criar o grupo de recursos. Para criar um novo grupo de recursos, utilize o comando `New-AzureRmResourceGroup` e especifique o nome e a região de destino do grupo de recursos:

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Depois de criar um grupo de recursos, pode criar o cluster com o comando seguinte. O URI do modelo pretendido será especificado para o parâmetro `-TemplateUri`. Quando executa este comando, o PowerShell irá solicitar-lhe os valores dos parâmetros de implementação.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
```

### Fornecer os parâmetros do modelo

Se estiver familiarizado com o PowerShell, sabe que pode percorrer os parâmetros disponíveis de um cmdlet escrevendo um sinal de subtração (-) e, em seguida, premir a tecla de tabulação. Esta mesma funcionalidade também funciona com os parâmetros que define no modelo. Assim que escrever o nome do modelo, o cmdlet obtém o modelo, analisa os parâmetros e adiciona os parâmetros do modelo ao comando de forma dinâmica. Isto torna muito mais fácil especificar os valores dos parâmetros do modelo. E, caso se esqueça de um valor de parâmetro necessário, o PowerShell pede-lhe o valor.

Veja abaixo o comando completo, com parâmetros incluídos. Pode fornecer os seus próprios valores para os nomes dos recursos.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## Passos seguintes

Agora que tem um cluster a funcionar, veja estes documentos para obter os detalhes de ligação e de gestão:

- [Ligar a um cluster do Serviço de Contentor Azure](container-service-connect.md)
- [Trabalhar com o Serviço de Contentor do Azure e o DC/OS](container-service-mesos-marathon-rest.md)
- [Trabalhar com o Serviço de Contentor do Azure e o Docker Swarm](container-service-docker-swarm.md)



<!--HONumber=Sep16_HO3-->


