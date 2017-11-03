---
title: Cluster HPC Pack para o Excel e SOA | Microsoft Docs
description: "Começar a executar cargas de trabalho em grande escala Excel e SOA num cluster HPC Pack no Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: 63babd94fdab15217cfb0757e4cd6efe458a628d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Introdução ao executar cargas de trabalho do Excel e SOA num cluster HPC Pack no Azure
Este artigo mostra como implementar um cluster do Microsoft HPC Pack 2012 R2 em máquinas virtuais do Azure, utilizando um modelo de início rápido do Azure ou, opcionalmente, um script de implementação do Azure PowerShell. O cluster utiliza as imagens de VM do Azure Marketplace concebidas para ser executado o Microsoft Excel ou cargas de trabalho de arquitetura orientada para serviços (SOA) com o HPC Pack. Pode utilizar o cluster para executar o Excel HPC e serviços SOA a partir de um computador de cliente no local. Os serviços do Excel HPC incluem descarregamento de livros do Excel e funções definidas pelo utilizador do Excel ou UDFs.

> [!IMPORTANT] 
> Este artigo baseia-se nas funcionalidades, modelos e scripts para HPC Pack 2012 R2. Este cenário não é atualmente suportado HPC Pack 2016.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Um nível elevado, o diagrama seguinte mostra o cluster HPC Pack que cria.

![Cluster HPC connosco executar cargas de trabalho do Excel][scenario]

## <a name="prerequisites"></a>Pré-requisitos
* **Computador cliente** -necessita de um computador de cliente baseada em Windows para submeter tarefas de Excel e SOA de exemplo para o cluster. Também precisa de um computador Windows para executar o script de implementação de cluster do Azure PowerShell (se escolher que método de implementação).
* **Subscrição do Azure** -se não tiver uma subscrição do Azure, pode criar um [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.
* **Quota de núcleos** -poderá ter de aumentar a quota de núcleos, especialmente se implementar vários nós do cluster sem especializados tamanhos de VM. Se estiver a utilizar um modelo de início rápido do Azure, a quota de núcleos no Gestor de recursos é por região do Azure. Nesse caso, poderá ter de aumentar a quota numa região específica. Consulte [limites de subscrição do Azure, quotas e restrições](../../azure-subscription-service-limits.md). Para aumentar uma quota [abrir um pedido de suporte ao cliente online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) , sem encargos.
* **Licença do Microsoft Office** - se implementar computação nós utilizando uma imagem de VM do Marketplace HPC Pack 2012 R2 com o Microsoft Excel, está instalada uma versão de avaliação de 30 dias do Microsoft Excel Professional Plus 2013. Após o período de avaliação, tem de fornecer uma licença válida do Microsoft Office para ativar o Excel para continuar a executar cargas de trabalho. Consulte [Excel ativação](#excel-activation) posteriormente neste artigo. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Passo 1. Configurar um cluster HPC Pack no Azure
Vamos mostrar duas opções para configurar o cluster HPC Pack 2012 R2: primeiro, utilizando um modelo de início rápido do Azure e o portal do Azure; e o segundo, utilizando um script de implementação do Azure PowerShell.

### <a name="option-1-use-a-quickstart-template"></a>Opção 1. Utilize um modelo de início rápido
Utilize um modelo de início rápido do Azure para implementar rapidamente um cluster HPC Pack no portal do Azure. Quando abrir o modelo no portal, obtenha uma IU simple em que introduza as definições para o cluster. Eis os passos. 

> [!TIP]
> Se quiser, utilize um [modelo do Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) que cria um cluster semelhante especificamente para cargas de trabalho do Excel. Os passos diferirem ligeiramente seguintes.
> 
> 

1. Visite o [página do modelo criar Cluster HPC no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Se quiser, reveja as informações sobre o modelo e o código de origem.
2. Clique em **implementar no Azure** para iniciar uma implementação com o modelo no portal do Azure.
   
   ![Implementar o modelo para o Azure][github]
3. No portal, siga estes passos para introduzir os parâmetros para o modelo de cluster HPC.
   
   a. No **parâmetros** página, introduza ou modificar os valores para os parâmetros do modelo. (Clique no ícone junto a cada definição para informações de ajuda.) Valores de exemplo são apresentados no ecrã seguinte. Este exemplo cria um cluster com o nome *hpc01* no *hpc.local* nós de computação de domínio constituída por um nó principal e 2. Os nós de computação são criados a partir de uma imagem de VM de pacote HPC inclui o Microsoft Excel.
   
   ![Introduza os parâmetros][parameters-new-portal]
   
   > [!NOTE]
   > O nó principal VM é criada automaticamente a partir de [mais recente imagem do Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) do HPC Pack 2012 R2 no Windows Server 2012 R2. Atualmente, se baseia a imagem no HPC Pack 2012 R2 Update 3.
   > 
   > VMs de nó de computação são criadas a partir da imagem mais recente da família de nó de computação selecionado. Selecione o **ComputeNodeWithExcel** imagem do nó que inclui uma versão de avaliação do Microsoft Excel Professional Plus 2013 de computação de opção para o HPC Pack mais recente. Para implementar um cluster para sessões SOA gerais ou para descarregamento de UDF do Excel, escolha o **ComputeNode** opção (sem Excel instalado).
   > 
   > 
   
   b. Selecione a subscrição.
   
   c. Criar um grupo de recursos para o cluster, tais como *hpc01RG*.
   
   d. Escolha uma localização para o grupo de recursos, tais como EUA Central.
   
   e. No **termos legais** , reveja os termos de licenciamento. Se concordar, clique em **Compra**. Quando tiver terminado de definir os valores para o modelo, clique em **criar**.
4. Quando concluir a implementação (que normalmente demora cerca de 30 minutos), exporte o ficheiro de certificado de cluster do nó principal do cluster. Num passo posterior, pode importa este certificado público no computador cliente para fornecer autenticação do lado do servidor para o enlace de HTTP seguro.
   
   a. No portal do Azure, aceda ao dashboard, selecione o nó principal e clique em **Connect** na parte superior da página para estabelecer ligação utilizando o ambiente de trabalho remoto.
   
    <!-- ![Connect to the head node][connect] -->
   
   b. Utilize os procedimentos padrão no Gestor de certificados para exportar o certificado de nó principal (localizado em Cert: \LocalMachine\My) sem a chave privada. Neste exemplo, exportar *CN = hpc01.eastus.cloudapp.azure.com*.
   
   ![Exportar o certificado][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Opção 2. Utilize o script de implementação de IaaS HPC Pack
O script de implementação do HPC Pack IaaS proporciona outra forma versátil para implementar um cluster HPC Pack. Cria um cluster no modelo de implementação clássica, enquanto que o modelo utiliza o modelo de implementação Azure Resource Manager. Além disso, o script é compatível com uma subscrição no serviço Global do Azure ou do Azure China.

**Pré-requisitos adicionais**

* **O Azure PowerShell** - [instalar e configurar o Azure PowerShell](/powershell/azure/overview) (versão 0.8.10 ou posterior) no computador cliente.
* **Script de implementação de HPC Pack IaaS** - transferir e Descompacte a versão mais recente do script do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Verifique a versão do script executando `New-HPCIaaSCluster.ps1 –Version`. Este artigo baseia-se numa versão 4.5.0 ou posterior do script.

**Criar o ficheiro de configuração**

 O script de implementação do HPC Pack IaaS utiliza um ficheiro de configuração XML, como entrada que descreve a infraestrutura de HPC cluster. Para implementar um cluster constituída por um nó principal e 18 nós de computação criados a partir da imagem do nó de computação que inclui o Microsoft Excel, substitua os valores para o seu ambiente para o ficheiro de configuração de exemplo seguinte. Para mais informações sobre o ficheiro de configuração, consulte o ficheiro de Manual.rtf na pasta de scripts e [criar um cluster HPC com o script de implementação do HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Notas sobre o ficheiro de configuração**

* O **VMName** do nó principal **tem** ser o mesmo que o **ServiceName**, ou tarefas SOA falham para executar.
* Certifique-se de que especifica **EnableWebPortal** para que o certificado de nó principal é gerado e exportado.
* O ficheiro Especifica um script do PowerShell pós-configuração PostConfig.ps1 que é executado no nó principal. O seguinte script de exemplo configura a cadeia de ligação de armazenamento do Azure, remove a função de nó de computação do nó principal e coloca todos os nós online quando estão implementadas. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Execute o script**

1. Abra a consola do PowerShell no computador cliente como um administrador.
2. Altere o diretório para a pasta de script (E:\IaaSClusterScript neste exemplo).
   
   ```
   cd E:\IaaSClusterScript
   ```
3. Para implementar o cluster HPC Pack, execute o seguinte comando. Este exemplo assume que o ficheiro de configuração está localizado numa E:\HPCDemoConfig.xml.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

Executa o script de implementação de pacote HPC durante algum tempo. Um aspeto que o script faz consiste em exportar e transfira o certificado de cluster e guardá-lo na pasta de documentos do utilizador atual no computador cliente. O script gera uma mensagem semelhante ao seguinte. Um passo seguinte, importe o certificado no arquivo de certificados adequados.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Passo 2. Descarregamento de livros do Excel e executar UDFs a partir de um cliente no local
### <a name="excel-activation"></a>Ativação do Excel
Quando utilizar a imagem de ComputeNodeWithExcel VM para cargas de trabalho de produção, tem de fornecer uma chave de licença válida do Microsoft Office para ativar o Excel em nós de computação. Caso contrário, a versão de avaliação do Excel expira após 30 dias e em execução livros do Excel irá falhar com o COMException (0x800AC472). 

Pode rearmamento Excel por mais 30 dias da hora de avaliação: Inicie sessão na nó principal e clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` no Excel todos os nós através do Gestor de Cluster HPC de computação. Pode rearmamento um máximo de duas vezes. Depois disso, tem de fornecer uma chave de licenciamento do Office válida.

O Office Professional Plus 2013 instalado na imagem de VM é uma edição de volume com uma chave de licença do genérico Volume (GVLK). Pode ativá-lo através do serviço de gestão de chaves (KMS) / ativação baseada no Active Directory (AD BA) ou chave de ativação múltipla (MAK). 

    * Para utilizar AD/KMS-BA, utilize um servidor KMS existente ou configure um novo utilizando o pacote de licença de Volume do Microsoft Office 2013. (Se pretender, configurar o servidor no nó principal.) Em seguida, ative a chave de anfitrião KMS através da Internet ou de telefone. Em seguida, clusrun `ospp.vbs` para definir o servidor KMS e a porta e ativar o Office em todos os nós de computação de Excel. 

    * Para utilizar a MAK, primeiro clusrun `ospp.vbs` para introduzir a chave e, em seguida, ativar todos os nós através da Internet ou de telefone de computação de Excel. 

> [!NOTE]
> Não não possível utilizar chaves de produto de revenda para o Office Professional Plus 2013 com esta imagem de VM. Se tiver chaves válidas e de suporte de dados de instalação para o Office ou Excel edições que não sejam nesta edição de volume do Office Professional Plus 2013, pode utilizá-los em vez disso. Primeiro, desinstale esta edição de volume e instalar a edição que tiver. O nó de cálculo do Excel reinstalado pode ser capturado como uma imagem VM personalizada para utilização numa implementação à escala.
> 
> 

### <a name="offload-excel-workbooks"></a>Descarregamento de livros do Excel
Siga estes passos para efetuar a descarga de um livro do Excel para que seja executado no cluster HPC Pack no Azure. Para tal, tem de ter o Excel 2010 ou 2013 já instalado no computador cliente.

1. Utilize uma das opções no passo 1 para implementar um cluster HPC Pack com o Excel a imagem do nó de computação. Obter o ficheiro de certificado de cluster (. cer) e o nome de utilizador do cluster e a palavra-passe.
2. No computador cliente, importe o certificado de cluster em Cert: \CurrentUser\Root.
3. Certifique-se de que o Excel está instalado. Crie um ficheiro de Excel.exe.config com o seguinte conteúdo na mesma pasta que Excel.exe no computador cliente. Este passo garante que o suplemento do HPC Pack 2012 R2 Excel COM é carregado com êxito.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
4. Configure o cliente para submeter tarefas ao cluster HPC Pack. É uma opção para transferir o completo [instalação do HPC Pack 2012 R2 Update 3](http://www.microsoft.com/download/details.aspx?id=49922) e instalar o cliente de HPC Pack. Em alternativa, transfira e instale o [utilitários de cliente do Microsoft HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49923) e o adequado Visual C++ 2010 redistributable para o seu computador ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).
5. Neste exemplo, utilizamos um livro do Excel de exemplo com o nome ConvertiblePricing_Complete.xlsb. Poderá transferi-lo [aqui](https://www.microsoft.com/en-us/download/details.aspx?id=2939).
6. Copie o livro do Excel para uma pasta de trabalho, tais como D:\Excel\Run.
7. Abra o livro do Excel. No **desenvolver** do Friso, clique em **suplementos COM** e confirme que o suplemento do HPC Pack Excel COM carregada com êxito.
   
   ![Excel suplemento para HPC Pack][addin]
8. Edite a macro VBA HPCControlMacros no Excel, alterando as linhas comentadas, conforme mostrado no seguinte script. Substitua os valores apropriados para o seu ambiente.
   
   ![Macro Excel para HPC Pack][macro]
   
   ```
   'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
   Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"
   
   'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
   Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.Initialize ActiveWorkbook
   HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles
   
   'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
   HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
   HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
   ```
9. Copie o livro do Excel para um diretório de carregamento como D:\Excel\Upload. Este diretório está especificado a constante HPC_DependsFiles na macro VBA.
10. Para executar o livro no cluster no Azure, clique em de **Cluster** botão na folha de cálculo.

### <a name="run-excel-udfs"></a>Executar UDFs do Excel
Para executar UDFs do Excel, siga os passos anteriores 1-3 para configurar o computador cliente. Para UDFs do Excel, não precisa de ter a aplicação de Excel instalada em nós de computação. Por isso, quando criar o cluster de nós de computação, pode escolher um normal em vez da imagem do nó de computação com o Excel da imagem do nó de computação.

> [!NOTE]
> Não há um limite de 34 caracteres no Excel 2010 e a caixa de diálogo de conector de cluster de 2013. Utilize esta caixa de diálogo para especificar o cluster que executa os UDFs. Se o nome do cluster completo é maior (por exemplo, hpcexcelhn01.southeastasia.cloudapp.azure.com), não se ajustar na caixa de diálogo. A solução é definido como uma variável de toda a máquina *CCP_IAASHN* com o valor do nome do cluster longo. Em seguida, introduza *% CCP_IAASHN %* na caixa de diálogo como o nome de nó principal do cluster. 
> 
> 

Depois do cluster é implementado com êxito, continue com os seguintes passos para executar um exemplo incorporado UDF do Excel. Para personalizado UDFs do Excel, consulte estes [recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para compilar os XLLs e implementá-las no cluster de IaaS.

1. Abra um novo livro do Excel. No **desenvolver** do Friso, clique em **suplementos**. Em seguida, na caixa de diálogo, clique em **procurar**, navegue para a pasta de %CCP_HOME%Bin\XLL32 e selecione a amostra ClusterUDF32.xll. Se o ClusterUDF32 não existe no computador cliente, copie-a partir da pasta %CCP_HOME%Bin\XLL32 no nó principal.
   
   ![Selecione UDF][udf]
2. Clique em **ficheiro** > **opções** > **avançadas**. Em **fórmulas**, verifique **permitir definido pelo utilizador a funções XLL executar um cluster de cálculo**. Em seguida, clique em **opções** e introduza o nome do cluster completo no **nome de nó principal do Cluster**. (Que foram anotados anteriormente esta caixa de entrada está limitada a 34 carateres, pelo que não pode ajustar um nome de cluster longo. É possível utilizar uma variável de toda a máquina aqui para um nome de cluster longo.)
   
   ![Configurar UDF][options]
3. Para executar o cálculo de UDF num cluster, clique nas células com valor =XllGetComputerNameC() e prima Enter. A função apenas obtém o nome do nó de computação no qual UDF é executada. Para a primeira execução, uma caixa de diálogo de credenciais solicita o nome de utilizador e palavra-passe para ligar ao cluster de IaaS.
   
   ![Executar UDF][run]
   
   Quando existem demasiadas células para calcular, prima Alt-Shift-Ctrl + F9 para executar o cálculo em todas as células.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Passo 3. Executar uma carga de trabalho SOA a partir de um cliente no local
Para executar aplicações de SOA gerais no cluster HPC Pack IaaS, primeiro utilize um dos métodos no passo 1 para implementar o cluster. Especifique um genérico imagem do nó de computação neste caso, porque não é necessário o Excel em nós de computação. Em seguida, siga estes passos.

1. Após a obtenção de certificado de cluster, tem de importá-lo no computador cliente em Cert: \CurrentUser\Root.
2. Instalar o [HPC Pack 2012 R2 Update 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) e [utilitários de cliente do Microsoft HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49923). Estas ferramentas permitem-lhe desenvolver e executar aplicações de cliente SOA.
3. Transferir o HelloWorldR2 [código de exemplo](https://www.microsoft.com/download/details.aspx?id=41633). Abra o HelloWorldR2.sln no Visual Studio 2010 ou 2012. (Este exemplo não é atualmente compatível com versões mais recentes do Visual Studio).
4. Compilar o projeto de EchoService primeiro. Em seguida, implemente o serviço para o cluster de IaaS da mesma forma que implementa para um cluster no local. Para obter passos detalhados, consulte o Readme.doc no HelloWordR2. Modificar e criar o HellWorldR2 e outros projetos, tal como descrito na secção seguinte para gerar as aplicações de cliente SOA que são executados num cluster do IaaS do Azure.

### <a name="use-http-binding-with-azure-storage-queue"></a>Utilizar o enlace de Http com a fila de armazenamento do Azure
Para utilizar o enlace de Http com uma fila de armazenamento do Azure, efetue algumas alterações ao código de exemplo.

* Atualize o nome do cluster.
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* Opcionalmente, utilize a predefinição TransportScheme SessionStartInfo ou definir explicitamente para Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Utilize o enlace predefinido para o BrokerClient.
  
    ```
  // Before
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
  // After
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
  ```
  
    Ou defina explicitamente utilizando o basicHttpBinding.
  
    ```
  BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
  binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
  ```
* Opcionalmente, defina o sinalizador de UseAzureQueue como VERDADEIRO na SessionStartInfo. Se não definido, será definido para verdadeiro por predefinição quando o nome do cluster tem sufixos de domínio do Azure e o TransportScheme é Http.
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>Utilizar o enlace de Http sem fila de armazenamento do Azure
Para utilizar o enlace de Http sem uma fila de armazenamento do Azure, defina explicitamente o sinalizador de UseAzureQueue como falso no SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Utilizar o enlace de NetTcp
Para utilizar o enlace de NetTcp, a configuração é semelhante à ligação a um cluster no local. É necessário abrir alguns pontos finais no nó principal do VM. Se utilizou o script de implementação do HPC Pack IaaS para criar o cluster, por exemplo, defina os pontos finais no portal do Azure da seguinte forma.

1. Pare a VM.
2. Adicione as portas TCP 9090, 9087, 9091, mediador 9094 para a sessão, Mediador de trabalho e os serviços de dados, respetivamente
   
    ![Configurar pontos finais][endpoint-new-portal]
3. Inicie a VM.

A aplicação de cliente SOA não necessita de alterações, exceto a alterar o nome principal para o nome completo do cluster de IaaS.

## <a name="next-steps"></a>Passos seguintes
* Consulte [estes recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para obter mais informações sobre como executar cargas de trabalho do Excel com o HPC Pack.
* Consulte [gerir serviços SOA no Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) para obter mais informações sobre como implementar e gerir serviços SOA com o HPC Pack.

<!--Image references-->
[scenario]: ./media/excel-cluster-hpcpack/scenario.png
[github]: ./media/excel-cluster-hpcpack/github.png
[template]: ./media/excel-cluster-hpcpack/template.png
[parameters]: ./media/excel-cluster-hpcpack/parameters.png
[parameters-new-portal]: ./media/excel-cluster-hpcpack/parameters-new-portal.png
[create]: ./media/excel-cluster-hpcpack/create.png
[connect]: ./media/excel-cluster-hpcpack/connect.png
[cert]: ./media/excel-cluster-hpcpack/cert.png
[addin]: ./media/excel-cluster-hpcpack/addin.png
[macro]: ./media/excel-cluster-hpcpack/macro.png
[options]: ./media/excel-cluster-hpcpack/options.png
[run]: ./media/excel-cluster-hpcpack/run.png
[endpoint]: ./media/excel-cluster-hpcpack/endpoint.png
[endpoint-new-portal]: ./media/excel-cluster-hpcpack/endpoint-new-portal.png
[udf]: ./media/excel-cluster-hpcpack/udf.png
