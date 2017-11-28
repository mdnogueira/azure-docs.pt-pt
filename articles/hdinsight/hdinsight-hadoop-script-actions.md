---
title: "Script de desenvolvimento de ações com o HDInsight - Azure | Microsoft Docs"
description: "Saiba como personalizar clusters do Hadoop com a ação de Script. Ação de script pode ser utilizada para instalar o software adicional em execução num cluster de Hadoop ou para alterar a configuração de aplicações instaladas num cluster."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 836d68a8-8b21-4d69-8b61-281a7fe67f21
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: d0e95014f6ebfc4e0286d3a12999c918f831b489
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Desenvolver scripts de ação de Script para clusters baseados em Windows de HDInsight
Saiba mais sobre como escrever scripts de ação de Script para o HDInsight. Para obter informações sobre como utilizar os scripts de ação de Script, consulte [clusters do HDInsight de personalizar através da ação de Script](hdinsight-hadoop-customize-cluster.md). Para o mesmo artigo escrito para clusters do HDInsight baseado em Linux, consulte [scripts de ação de Script desenvolver para o HDInsight](hdinsight-hadoop-script-actions-linux.md).



> [!IMPORTANT]
> Os passos neste documento só funcionam para os clusters do HDInsight baseados em Windows. HDInsight só está disponível no Windows para as versões inferiores ao HDInsight 3.4. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows). Para obter informações sobre a utilização de ações de script com clusters baseados em Linux, consulte [ação desenvolvimento Script com o HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).
>
>



Ação de script pode ser utilizada para instalar o software adicional em execução num cluster de Hadoop ou para alterar a configuração de aplicações instaladas num cluster. Ações de script são scripts que são executados em nós do cluster quando os clusters do HDInsight são implementados e são executados depois de nós no cluster de concluir a configuração do HDInsight. Uma ação de script é executada com privilégios de conta de administrador de sistema e fornece direitos de acesso total a nós do cluster. Cada cluster pode ser fornecido com uma lista de ações de script a ser executado por ordem na qual são especificados.

> [!NOTE]
> Se ocorrer a seguinte mensagem de erro:
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage: O termo 'Guardar HDIFile' não é reconhecida como o nome do cmdlet, function, ficheiro de script ou programa operacional. Verifique a ortografia do nome, ou se foi incluído um caminho, certifique-se de que o caminho está correto e tente novamente.
> É porque não inclui os métodos de programa auxiliar.  Consulte [métodos de programa auxiliar para scripts personalizados](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).
>
>

## <a name="sample-scripts"></a>Scripts de exemplo
Para criar clusters do HDInsight no sistema operativo Windows, a ação de Script é o script do PowerShell do Azure. O script seguinte é um exemplo para configurar os ficheiros de configuração do site:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

O script utiliza quatro parâmetros, o nome de ficheiro de configuração, a propriedade que pretende modificar, o valor que pretende definir, e uma descrição. Por exemplo:

    hive-site.xml hive.metastore.client.socket.timeout 90

Estes parâmetros define o valor de hive.metastore.client.socket.timeout 90 no ficheiro site.xml do hive.  O valor predefinido é de 60 segundos.

Este script de exemplo também pode ser encontrado em [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

O HDInsight fornece vários scripts para instalar componentes adicionais nos clusters do HDInsight:

| Nome | Script |
| --- | --- |
| **Instalar o Spark** |https://hdiconfigactions.blob.Core.Windows.NET/sparkconfigactionv03/spark-Installer-v03.ps1. Consulte [instalar e utilizar clusters do Spark no HDInsight][hdinsight-install-spark]. |
| **Instalar o R** |https://hdiconfigactions.blob.Core.Windows.NET/rconfigactionv02/r-Installer-v02.ps1. Consulte [instalar e utilizar R nos clusters do HDInsight][hdinsight-r-scripts]. |
| **Instalar Solr** |https://hdiconfigactions.blob.Core.Windows.NET/solrconfigactionv01/solr-Installer-v01.ps1. Consulte [instalar e utilizar Solr no HDInsight clusters](hdinsight-hadoop-solr-install.md). |
| - **Instalar Giraph** |https://hdiconfigactions.blob.Core.Windows.NET/giraphconfigactionv01/giraph-Installer-v01.ps1. Consulte [instalar e utilizar Giraph no HDInsight clusters](hdinsight-hadoop-giraph-install.md). |

Ação de script pode ser implementada no portal do Azure, Azure PowerShell ou utilizando o SDK .NET do HDInsight.  Para obter mais informações, consulte [clusters do HDInsight de personalizar através da ação de Script][hdinsight-cluster-customize].

> [!NOTE]
> Os scripts de exemplo funcionam apenas com clusters do HDInsight versão 3.1 ou posterior. Para obter mais informações sobre as versões de cluster do HDInsight, consulte [as versões de cluster do HDInsight](hdinsight-component-versioning.md).
>
>

## <a name="helper-methods-for-custom-scripts"></a>Métodos de programa auxiliar para scripts personalizados
Métodos de programa auxiliar de ação de script são utilitários que pode utilizar ao escrever scripts personalizados. Estes métodos são definidos no [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)e pode ser incluído na sua scripts com o exemplo seguinte:

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Seguem-se os métodos de programa auxiliar que são fornecidos por este script:

| Método de programa auxiliar | Descrição |
| --- | --- |
| **Guardar HDIFile** |Transferir um ficheiro da especificado Uniform Resource Identifier (URI) para uma localização no disco local que está associado com o nó de VM do Azure atribuído ao cluster. |
| **HDIZippedFile expanda** |Descomprimir um ficheiro zipped. |
| **HDICmdScript invocar** |Execute um script de cmd.exe. |
| **Escrita HDILog** |Escreva a saída do script personalizado utilizado para uma ação de script. |
| **Get-Services** |Obter uma lista de serviços em execução na máquina em que executa o script. |
| **Get-Service** |Com o nome de serviço específica como entrada, obter informações detalhadas sobre um serviço específico (nome de serviço, processar o ID, estado, etc.) na máquina em que executa o script. |
| **Get-HDIServices** |Obter uma lista de serviços do HDInsight em execução no computador em que executa o script. |
| **Get-HDIService** |Com o nome de serviço HDInsight específico como entrada, obter informações detalhadas sobre um serviço específico (nome de serviço, processar o ID, estado, etc.) na máquina em que executa o script. |
| **Get-ServicesRunning** |Obter uma lista de serviços em execução no computador em que executa o script. |
| **Get-ServiceRunning** |Verifique se um serviço específico (por nome) está em execução no computador em que executa o script. |
| **Get-HDIServicesRunning** |Obter uma lista de serviços do HDInsight em execução no computador em que executa o script. |
| **Get-HDIServiceRunning** |Verifique se um serviço de HDInsight específico (por nome) está em execução no computador em que executa o script. |
| **Get-HDIHadoopVersion** |Obter a versão do Hadoop instalado no computador em que executa o script. |
| **Teste IsHDIHeadNode** |Verifique se o computador em que executa o script de um nó principal. |
| **Teste IsActiveHDIHeadNode** |Verifique se o computador em que executa o script de um nó principal Active Directory. |
| **Teste IsHDIDataNode** |Verifique se o computador em que executa o script de um nó de dados. |
| **Editar HDIConfigFile** |Edite a ficheiros de configuração do ramo de registo-site.xml, Core-site.XML, hdfs site.xml, mapred site.xml ou yarn site.xml. |

## <a name="best-practices-for-script-development"></a>Melhores práticas para o desenvolvimento do script
Quando desenvolver um script personalizado para um cluster do HDInsight, existem vários procedimentos recomendados para tenha em atenção:

* Verificação da versão de Hadoop

    Apenas HDInsight versão 3.1 (Hadoop 2.4) e acima suporte através da ação de Script para instalar componentes personalizados num cluster. O script personalizado, tem de utilizar o **Get-HDIHadoopVersion** método de programa auxiliar para verificar a versão de Hadoop antes de prosseguir com a efetuar outras tarefas no script.
* Fornecer estáveis ligações para recursos de script

    Os utilizadores Certifique-se de que todos os scripts e outros artefactos utilizados na personalização de um cluster permanecem disponíveis ao longo da duração do cluster e que as versões destes ficheiros não mudar durante a duração. Estes recursos são necessários se o reprocessamento de imagem de nós do cluster é necessário. A melhor prática é transferir e arquivar tudo numa conta de armazenamento que o utilizador controla. Isto pode ser a conta do Storage predefinida ou qualquer uma das contas de armazenamento adicionais especificadas no momento da implementação para um cluster personalizado.
    O Spark e R personalizado amostras de cluster fornecidos na documentação, por exemplo, vamos efetuou uma cópia local dos recursos nesta conta de armazenamento: https://hdiconfigactions.blob.core.windows.net/.
* Certifique-se de que o script de personalização do cluster idempotent

    Tem de esperar que os nós do cluster do HDInsight é recriada durante a duração do cluster. O script de personalização do cluster é executado sempre que um cluster é recriado. Este script tem de ser concebido para ser idempotent na medida em que após de reprocessamento de imagem, o script deve certificar-se que o cluster é devolvido para o mesmo personalizadas de estado que estava depois do script é executado pela primeira vez que o cluster foi inicialmente criado. Por exemplo, se um script personalizado instalada uma aplicação em D:\AppLocation na sua primeira execução, em seguida, em cada execução subsequente, após de reprocessamento de imagem, o script deve verificar se a aplicação existe na localização D:\AppLocation antes de continuar com outros passos seguintes no script.
* Instalar componentes personalizados na localização ideal

    Quando nós de cluster são recriados, a unidade de recursos C:\ e a unidade de sistema D:\ podem reformatadas, resultando na perda de dados e aplicações que tinham sido instaladas nessas unidades. Isto também pode acontecer se um nó de máquina virtual do Azure (VM) que faz parte do cluster de fica inativo e é substituído por um novo nó. Pode instalar os componentes na unidade D:\ ou na localização C:\apps no cluster. Todas as outras localizações na unidade C:\ estão reservadas. Especifique a localização onde são aplicações ou bibliotecas para ser instalada no script de personalização de cluster.
* Certifique-se de elevada disponibilidade da arquitetura do cluster

    HDInsight tem uma arquitetura de ativo-passivo para elevada disponibilidade, em que um nó principal está no modo de Active Directory (em que os serviços do HDInsight estão em execução) e o outro nó principal está em modo de reserva dinâmica (no qual HDInsight serviços não estão em execução). Os nós mudar de modos de ativos e passivos se os serviços do HDInsight são interrompidos. Se uma ação de script é utilizada para instalar os serviços em ambos os nós principais para elevada disponibilidade, tenha em atenção que o mecanismo de ativação pós-falha de HDInsight não é automaticamente pós-falha estes serviços instalada por utilizador. Instalado pelo utilizador serviços no HDInsight nós principais que são esperados para elevada disponibilidade tem de ser ter os seus próprios mecanismo de ativação pós-falha se no modo ativo-passivo ou no modo ativo-ativo.

    Um comando de ação de Script do HDInsight é executada em ambos os nós principais quando a função de nó principal é especificada como um valor de *ClusterRoleCollection* parâmetro. Por isso, ao conceber um script personalizado, certifique-se de que o script em consideração esta configuração. Não deve depare com problemas em que os serviços do mesmos são instalados e iniciados em ambos os nós principais e de acaba por ficar competir entre si. Além disso, tenha em atenção de que os dados são perdidos durante o reprocessamento de imagem, pelo que o software instalado através de ação de Script tem de ser resiliente para esses eventos. As aplicações devem ser concebidas para trabalhar com dados altamente disponíveis, que são distribuídos por vários nós. Tenha em atenção que o máximo de 1/5 de nós num cluster podem ser recriados ao mesmo tempo.
* Configurar as componentes personalizadas para utilizar o Blob storage do Azure

    Os componentes personalizados que instalar em nós do cluster podem ter uma configuração predefinida para utilizar o armazenamento distribuído ficheiro sistema Hadoop (HDFS). A configuração para utilizar o Blob storage do Azure em vez disso, deve ser alterado. Recriação de imagem do cluster, o sistema de ficheiros do HDFS obtém a formatados em seria perder quaisquer dados armazenados não existe. Em vez disso, utilizar o Blob storage do Azure garante que os dados são mantidos.

## <a name="common-usage-patterns"></a>Padrões de utilização comuns
Esta secção fornece orientação na implementação de algumas das comuns padrões de utilização que possam ser executadas ao escrever o seu próprio script personalizado.

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente
Muitas vezes, no desenvolvimento de ações de script, pode ter de definir variáveis de ambiente. Por exemplo, um cenário mais provável é quando transferir um binário a partir de um site externo, instalá-lo no cluster e adicione a localização onde está instalado para a variável de ambiente 'PATH'. O fragmento seguinte mostra como definir variáveis de ambiente de script personalizado.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Esta instrução define a variável de ambiente **MDS_RUNNER_CUSTOM_CLUSTER** para o valor 'true' e também define o âmbito desta variável para ser globais. Por vezes é importante que as variáveis de ambiente são definidas no âmbito adequado – computador ou utilizador. Consulte [aqui] [ 1] para obter mais informações sobre a definição de variáveis de ambiente.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acesso a localizações onde estão armazenados os scripts personalizados
Scripts utilizados para personalizar um cluster tem de ser quer na conta de armazenamento predefinido para o cluster ou num contentor só de leitura público em qualquer outra conta de armazenamento. Se o script acede a recursos localizados noutro local estes têm de ser um acessível publicamente (pelo menos público só de leitura). Para a instância poderá aceder a um ficheiro e guardá-lo utilizando o comando SaveFile HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

Neste exemplo, tem de se certificar de que o contentor somecontainer na conta de armazenamento 'somestorageaccount' está acessível publicamente. Caso contrário, o script emite uma exceção 'Não foi encontrado' e falhar.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Transmita os parâmetros para o cmdlet Add-AzureRmHDInsightScriptAction
Para passar os vários parâmetros para o cmdlet Add-AzureRmHDInsightScriptAction, tem de formatar o valor de cadeia que contém todos os parâmetros para o script. Por exemplo:

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

ou

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Acionar excepção para a implementação de cluster com falhas
Se pretender ser notificado com exatidão do facto de personalização do cluster não foi bem sucedida conforme esperado, é importante acionar uma excepção e efetuar a criação do cluster. Por exemplo, pode querer processar um ficheiro caso exista e processar o caso de erro onde o ficheiro não existe. Isto seria Certifique-se de que o script sair transições e o estado do cluster corretamente é conhecido. O fragmento seguinte fornece um exemplo de como alcançar isto:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

Este fragmento, se o ficheiro não existia,-seria levar para um Estado em que o script, na verdade, sai corretamente após a impressão através da mensagem de erro e o cluster atinja o estado de execução, partindo do princípio de que "com êxito" concluir o processo de personalização do cluster. Se pretender com precisão notificado do facto de que o cluster, essencialmente, a personalização não foi bem sucedida conforme esperado devido a um ficheiro em falta, é mais adequado acionar uma excepção e falhar o passo de personalização do cluster. Para atingir esse objetivo tem de utilizar o seguinte fragmento de código de exemplo em vez disso.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Lista de verificação para implementar uma ação de script
Eis os passos que pegámos quando preparar para implementar estes scripts:

1. Colocar os ficheiros que contêm os scripts personalizados num local que esteja acessível a nós do cluster durante a implementação. Isto pode ser qualquer um dos predefinido ou mais contas de armazenamento especificadas no momento da implementação de cluster ou outro contentor de armazenamento acessível publicamente.
2. Adicione verificações em scripts para se certificar de que são executados idempotently, para que o script pode ser executado múltiplas vezes no mesmo nó.
3. Utilize o **Write-Output** cmdlet do PowerShell do Azure para imprimir para o STDOUT e STDERR. Não utilize **escrita anfitrião**.
4. Utilizar uma pasta de ficheiro temporário, tais como $env: TEMP, para manter o ficheiro transferido utilizado pelos scripts e, em seguida, limpe-los depois de tem executado scripts.
5. Instale software personalizado apenas em D:\ ou C:\apps. Outras localizações na unidade c: não devem ser utilizadas como estão reservados. Tenha em atenção que a instalação de ficheiros na unidade c: fora da pasta C:\apps poderá resultar em falhas de configuração durante reimages do nó.
6. No caso de foram alterados as definições de nível de SO ou ficheiros de configuração de serviço do Hadoop, poderá pretender reiniciar os serviços do HDInsight para que pode escolher se as definições de nível de SO, tais como as variáveis de ambiente definidas nos scripts.

## <a name="debug-custom-scripts"></a>Depurar scripts personalizados
Os registos de erros de script são armazenados, juntamente com outra saída, na conta do Storage predefinida que especificou para o cluster na respetiva criação. Os registos são armazenados numa tabela com o nome *u < \cluster-name-fragment >< \time-stamp > setuplog*. Estes são registos agregados com registos de todos os nós (nó principal e nós de trabalho) em que o script é executado no cluster.

Uma forma fácil para verificar os registos está a utilizar as ferramentas do HDInsight para Visual Studio. Para instalar as ferramentas, consulte [começar a utilizar as ferramentas Hadoop do Visual Studio para o HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-and-upgrade-data-lake-tools-for-visual-studio)

**Para verificar o registo com o Visual Studio**

1. Abra o Visual Studio.
2. Clique em **vista**e, em seguida, clique em **Explorador de servidores**.
3. Clique com botão direito "Azure", clique em ligar ao **subscrições do Microsoft Azure**e, em seguida, introduza as suas credenciais.
4. Expanda **armazenamento**, expanda a conta de armazenamento do Azure utilizada como o sistema de ficheiros, expanda **tabelas**e, em seguida, faça duplo clique o nome da tabela.

Pode também remoto para os nós de cluster para ver STDOUT e STDERR para scripts personalizados. Os registos em cada nó são específicos apenas para esse nó e são registados numa **C:\HDInsightLogs\DeploymentAgent.log**. Estes ficheiros de registo registam todas as saídas do script personalizado. Um fragmento de registo de exemplo para uma ação de script do Spark tem o seguinte aspeto:

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


Este registo, é claro que a ação de script do Spark foi executada na VM com o nome HEADNODE0 e que nenhum excepções foram iniciadas durante a execução.

No caso de ocorre uma falha de execução, o resultado que descrevem o é também contido neste ficheiro de registo. As informações fornecidas nestes registos devem ser útil depurar problemas de script que possam surgir.

## <a name="see-also"></a>Consultar também
* [Personalizar clusters do HDInsight através da ação de Script][hdinsight-cluster-customize]
* [Instalar e utilizar o Spark nos clusters do HDInsight][hdinsight-install-spark]
* [Instalar e utilizar o R nos clusters do HDInsight][hdinsight-r-scripts]
* [Instalar e utilizar Solr nos clusters do HDInsight](hdinsight-hadoop-solr-install.md).
* [Instalar e utilizar Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
