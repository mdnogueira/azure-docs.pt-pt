<properties
   pageTitle="Introdução ao Azure Batch PowerShell | Microsoft Azure"
   description="Obtenha uma introdução rápida aos cmdlets do Azure PowerShell que pode utilizar para gerir o serviço Azure Batch"
   services="batch"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="07/28/2016"
   ms.author="danlep"/>

# Introdução aos cmdlets do Azure Batch PowerShell
Com os cmdlets do Azure Batch PowerShell, pode efetuar e encriptar muitas das mesmas tarefas que desempenha com as APIs do lote, o portal do Azure e a Interface de Linha de Comandos do Azure (CLI). Isto é uma introdução rápida aos cmdlets que pode utilizar para gerir as contas do Batch e trabalhar com os recursos do Batch, como conjuntos e tarefas. Este artigo baseia-se nos cmdlets do Azure PowerShell versão 1.6.0.

Para obter uma lista completa de cmdlets do Batch e a sintaxe detalhada dos cmdlets, veja a [Referência de cmdlets do Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx). 


## Pré-requisitos

* **Azure PowerShell** - Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter instruções para transferir e instalar o Azure PowerShell. 
   
    * Uma vez que os cmdlets do Azure Batch são enviados no módulo Azure Resource Manager, terá de executar o cmdlet **Login-AzureRmAccount** para ligar à sua subscrição. 
    
    * Recomendamos que atualize frequentemente o Azure PowerShell para tirar partido das atualizações e melhoramentos do serviço. 
    
* **Registar no espaço de nomes do fornecedor do Batch (operação única)** - Antes de trabalhar com as suas contas do Batch, tem de registar no espaço de nomes do fornecedor do Batch. Esta operação só tem de ser efetuada uma vez por subscrição. Execute o seguinte cmdlet:

        Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch


## Gerir contas e chaves do Batch

### Criar uma conta do Batch

**New-AzureRmBatchAccount** cria uma nova conta do Batch num grupo de recursos especificado. Caso ainda não tenha um grupo de recursos, crie um executando o cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx), especificando uma das regiões do Azure no parâmetro **Localização**, por exemplo, "E.U.A. Central". Por exemplo:


    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"


Em seguida, crie uma nova conta do Batch no grupo de recursos, especificando um nome de conta em <*nome_conta*> e a localização e o nome do seu grupo de recursos. Criar a conta do Batch pode demorar algum tempo a concluir. Por exemplo:


    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup

> [AZURE.NOTE] O nome da conta do Batch tem de ser exclusivo para a região do Azure do grupo de recursos, conter entre 3 e 24 carateres e utilizar apenas letras minúsculas e números.

### Obter chaves de acesso à conta
**Get-AzureRmBatchAccountKeys** mostra as chaves de acesso associadas a uma conta do Azure Batch. Por exemplo, execute o seguinte para obter as chaves primária e secundária da conta que criou.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <accountname>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey


### Gerar uma nova chave de acesso
**New-AzureRmBatchAccountKey** gera uma nova chave de conta primária ou secundária para uma conta do Azure Batch. Por exemplo, para gerar uma nova chave primária para a conta do Batch, escreva:


    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary


> [AZURE.NOTE] Para gerar uma nova chave secundária, especifique "Secondary" para o parâmetro **KeyType**. Tem de regenerar as chaves primária e secundária em separado.

### Eliminar uma conta do Batch
**Remove-AzureRmBatchAccount** elimina uma conta do Batch. Por exemplo:


    Remove-AzureRmBatchAccount -AccountName <account_name>

Quando lhe for perguntado, confirme que pretende remover a conta. A remoção da conta pode demorar algum tempo a concluir.

## Criar um objeto BatchAccountContext

Para efetuar a autenticação utilizando os cmdlets do Batch PowerShell quando criar e gerir agrupamentos, tarefas e outros recursos do Batch, primeiro crie um objeto BatchAccountContext para armazenar o nome e as chaves da conta:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

O objeto BatchAccountContext é transmitido aos cmdlets que utilizam o parâmetro **BatchContext**.

> [AZURE.NOTE] Por predefinição, a chave primária da conta é utilizada na autenticação, mas pode selecionar explicitamente a chave a utilizar ao alterar a propriedade **KeyInUse** do objeto BatchAccountContext: `$context.KeyInUse = "Secondary"`.



## Criar e modificar recursos do Batch
Utilize cmdlets como **New-AzureBatchPool**, **New-AzureBatchJob** e **New-AzureBatchTask** para criar recursos numa conta do Batch. Existem cmdlets **Get-** e **Set-** correspondentes para atualizar as propriedades dos recursos existentes e cmdlets **Remove-** para remover recursos numa conta do Batch. 

Quando utiliza muitos destes cmdlets, além de transmitir um objeto de BatchContext, terá de criar ou passar objetos que contêm definições detalhadas de recursos, conforme mostrado no exemplo seguinte. Consulte a ajuda detalhada de cada cmdlet para obter exemplos adicionais.

### Criar um conjunto do Batch

Quando criar ou atualizar um conjunto de Batch, selecione uma configuração de serviço na nuvem ou uma configuração de máquina virtual para o sistema operativo nos nós de computação (veja [Descrição geral da funcionalidade do Batch](batch-api-basics.md#pool)). A sua escolha determina se os nós de computação são instalados com uma das [versões do SO Convidado do Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases) ou com uma das imagens suportadas do Linux ou VM do Windows no Azure Marketplace. 

Quando executa o **New-AzureBatchPool**, introduza as definições do sistema operativo num objeto PSCloudServiceConfiguration ou PSVirtualMachineConfiguration. Por exemplo, o seguinte cmdlet cria um novo conjunto do Batch com nós de computação pequenos na configuração de serviço na nuvem, instalados com a versão mais recente do sistema operativo da família 3 (Windows Server 2012). Neste caso, o parâmetro **CloudServiceConfiguration** especifica a variável *$configuration* como o objeto PSCloudServiceConfiguration. O parâmetro **BatchContext** especifica uma variável *$context* definida anteriormente como objeto BatchAccountContext.


    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(3,"*")
    
    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

O número de destino dos nós de computação do novo conjunto é determinado por uma fórmula de dimensionamento automático. Neste caso, a fórmula é simplesmente **$TargetDedicated=4**, que indica que o número de nós de computação no conjunto é, no máximo, 4. 

## Consulta para conjuntos, tarefas e outros detalhes

Utilize cmdlets, como **Get-AzureBatchPool**, **Get-AzureBatchJob** e **Get-AzureBatchTask** para consultar entidades criadas numa conta do Batch.


### Consulta de dados

Por exemplo, utilize **Get-AzureBatchPools** para localizar os seus agrupamentos. Por predefinição, isto consulta todos os agrupamentos na sua conta, assumindo que já armazenou o objeto BatchAccountContext em *$context*:


    Get-AzureBatchPool -BatchContext $context

### Utilizar um filtro OData

Pode fornecer um filtro OData utilizando o parâmetro **Filter** para localizar apenas os objetos que lhe interessam. Por exemplo, pode localizar todos os agrupamentos com ids começados por "myPool":


    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context


Este método não é tão flexível como se utilizasse "Where-Object" num pipeline local. No entanto, a consulta é enviada diretamente para o serviço Batch para que todas as filtragens ocorram no lado do servidor, poupando a largura de banda da Internet.

### Utilizar o parâmetro Id

Uma alternativa a um filtro OData é a utilização do parâmetro **Id**. Para consultar um agrupamento específico com o id "myPool":


    Get-AzureBatchPool -Id "myPool" -BatchContext $context


O parâmetro **Id** suporta apenas a pesquisa de ids completos e não carateres universais ou filtros de estilo OData.



### Utilizar o parâmetro MaxCount

Por predefinição, cada cmdlet devolve um máximo de 1000 objetos. Se atingir este limite, refine o filtro para obter menos objetos ou defina explicitamente um limite máximo utilizando o parâmetro **MaxCount**. Por exemplo:


    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Para remover o limite superior, defina **MaxCount** como 0 ou menos.

### Utilizar o pipeline

Os cmdlets do Batch podem tirar partido do pipeline do PowerShell para enviar dados entre cmdlets. Isto tem o mesmo efeito que especificar um parâmetro, mas facilita a listagem de várias entidades. Por exemplo, o seguinte localiza todas as tarefas na sua conta:


    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context


## Passos seguintes
* Para obter exemplos e a sintaxe detalhada dos cmdlets, veja a [Referência de cmdlets do Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx).

* Veja [Consultar o serviço Batch de forma eficiente](batch-efficient-list-queries.md) para obter mais informações sobre a redução do número de itens e o tipo de informação devolvido para as consultas ao Batch. 



<!--HONumber=Aug16_HO1-->


