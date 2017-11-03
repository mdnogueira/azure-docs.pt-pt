---
title: "Serviços de entrega contínua para a nuvem com o TFS no Azure | Microsoft Docs"
description: "Saiba como configurar a entrega contínua para aplicações em nuvem do Azure. Exemplos de código para MSBuild as instruções da linha de comandos e scripts do PowerShell."
services: cloud-services
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 4f3c93c6-5c82-4779-9d19-7404a01e82ca
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/12/2017
ms.author: kraigb
ms.openlocfilehash: 0979722b9ec715e91825c7aba74657451df6e83f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-delivery-for-cloud-services-in-azure"></a>Entrega contínua para serviços em nuvem no Azure
O processo descrito neste artigo mostra como configurar a entrega contínua para aplicações em nuvem do Azure. Este processo permite a criação automática de pacotes e a implementação do pacote no Azure após cada entrada de código. O processo de criação de pacote descrito neste artigo é equivalente à **pacote** comando no Visual Studio e os passos de publicação são equivalentes da **publicar** comando no Visual Studio.
O artigo aborda os métodos que pretende utilizar para criar um servidor de compilação com MSBuild as instruções da linha de comandos e scripts do Windows PowerShell e também demonstra como configurar, opcionalmente, o Visual Studio Team Foundation Server - Criar equipa definições para utilizar os comandos de MSBuild e scripts do PowerShell. O processo é personalizável para o seu ambiente de compilação e ambientes de destino do Azure.

Também pode utilizar o Visual Studio Team Services, uma versão do TFS que está alojado no Azure, para fazê-lo mais facilmente. 

Antes de começar, deve publicar a aplicação a partir do Visual Studio.
Isto irá garantir que todos os recursos estão disponíveis e inicializado quando tentar automatizar o processo de publicação.

## <a name="1-configure-the-build-server"></a>1: configurar o servidor de compilação
Antes de poder criar um pacote do Azure através da utilização de MSBuild, tem de instalar o software necessário e ferramentas no servidor de compilação.

Visual Studio não é necessária para ser instalada no servidor de compilação. Se pretender utilizar o serviço de compilação do Team Foundation para gerir o seu servidor de compilação, siga as instruções no [serviço de compilação do Team Foundation] [ Team Foundation Build Service] documentação.

1. No servidor de compilação, instale o [.NET Framework 4.5.2][.NET Framework 4.5.2], que inclui MSBuild.
2. Instalar a versão mais recente [ferramentas de criação do Azure para .NET](https://azure.microsoft.com/develop/net/).
3. Instalar o [bibliotecas do Azure para .NET](http://go.microsoft.com/fwlink/?LinkId=623519).
4. Copie o ficheiro de Microsoft.WebApplication.targets a partir de uma instalação do Visual Studio para o servidor de compilação.

   Num computador com o Visual Studio instalado, este ficheiro está localizado no diretório c:\\programas (x86)\\MSBuild\\Microsoft\\VisualStudio\\v14.0\\WebApplications. Pode copiá-lo para o mesmo diretório no servidor de compilação.
5. Instalar o [ferramentas do Azure para Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx).

## <a name="2-build-a-package-using-msbuild-commands"></a>2: criar um pacote de utilizar comandos de MSBuild
Esta secção descreve como construir um comando de MSBuild baseia-se de um pacote do Azure. Execute este passo no servidor de compilação para verificar que tudo está configurado corretamente e que o comando de MSBuild não que pretende executar. Pode adicionar esta linha de comando de scripts de compilação existentes no servidor de compilação ou pode utilizar a linha de comandos numa definição de compilação do TFS, conforme descrito na secção seguinte. Para obter mais informações sobre parâmetros da linha de comandos e MSBuild, consulte [referência da linha de comandos de MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

1. Se o Visual Studio estar instalado no servidor de compilação, localize e selecione **linha de comandos do Visual Studio** no **Visual Studio Tools** pasta do Windows.

   Se o Visual Studio não está instalado no servidor de compilação, abra uma linha de comandos e certifique-se de que MSBuild.exe está acessível no caminho. MSBuild é instalado com o .NET Framework no caminho de % WINDIR %\\Microsoft.NET\\Framework\\*versão*. Por exemplo, para adicionar MSBuild.exe a variável de ambiente PATH quando tiver instalado o .NET Framework 4, escreva o seguinte comando na linha de comandos:

       set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"
2. Na linha de comandos, navegue para a pasta que contém o ficheiro de projeto do Azure que pretende criar.
3. Execute MSBuild com o /target: publicar a opção como no exemplo seguinte:

       MSBuild /target:Publish

   Esta opção pode ser abreviada como /t: publicar. A opção de /t:Publish do MSBuild não deve ser confundida com os comandos de publicar disponíveis no Visual Studio, quando tiver instalado o SDK do Azure. O /t: publicar opção baseiam-se apenas os pacotes do Azure. Não implementa os pacotes tal como os comandos de publicar no Visual Studio.

   Opcionalmente, pode especificar o nome do projeto como um parâmetro de MSBuild. Se não for especificado, é utilizado o diretório atual. Para obter mais informações sobre as opções de linha de comandos do MSBuild, consulte [referência da linha de comandos de MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).
4. Localize o resultado. Por predefinição, este comando cria um diretório em relação a pasta de raiz para o projeto, tais como *ProjectDir*\\bin\\*configuração*\\app.publish\\. Quando criar um projeto do Azure, gerar dois ficheiros, o próprio ficheiro de pacote e o ficheiro de configuração associada:

   * Project.cspkg
   * ServiceConfiguration. *TargetProfile*. cscfg

   Por predefinição, cada projeto do Azure inclui um ficheiro de configuração de serviço (ficheiro. cscfg) para o locais compilações (depuração) e outra para compilações de nuvem (teste ou de produção), mas pode adicionar ou remover ficheiros de configuração do serviço conforme necessário. Quando criar um pacote no Visual Studio, será pedido que ficheiro de configuração de serviço para incluir juntamente com o pacote.
5. Especifique o ficheiro de configuração do serviço. Quando criar um pacote através da utilização de MSBuild, o ficheiro de configuração do serviço local é incluído por predefinição. Para incluir um ficheiro de configuração de serviço diferente, defina a propriedade TargetProfile o comando de MSBuild, como no exemplo seguinte:

       MSBuild /t:Publish /p:TargetProfile=Cloud
6. Especifique a localização para a saída. Definir o caminho utilizando o /p:PublishDir =*diretório* \\ opção, incluindo o separador de barra invertida à direita, como no exemplo seguinte:

       MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

   Depois de ter criado e testado uma linha de comandos de MSBuild adequada para criar os seus projetos e combine-os para um pacote do Azure, pode adicionar esta linha de comandos para os scripts de compilação. Se o servidor de compilação utiliza scripts personalizados, este processo irá depender as especificações do processo de compilação personalizado. Se estiver a utilizar o TFS como um ambiente de compilação, em seguida, pode seguir as instruções no passo seguinte para adicionar a compilação de pacote do Azure para o processo de compilação.

## <a name="3-build-a-package-using-tfs-team-build"></a>3: criar um pacote com a compilação de equipa do TFS
Se tiver Team Foundation Server (TFS) configurado como um controlador de compilação e o servidor de compilação configurar como uma máquina de compilação do TFS, em seguida, opcionalmente, pode configurar uma compilação automatizada para o pacote do Azure. Para obter informações sobre como configurar e utilizar do Team Foundation server como um sistema de compilação, consulte [aumentar horizontalmente o sistema de compilação][Scale out your build system]. Em particular, o procedimento seguinte parte do princípio de que configurou o servidor de compilação, tal como descrito no [implementar e configurar um servidor de compilação][Deploy and configure a build server], e que criou um projeto de equipa, criar um projeto de serviço em nuvem no projeto de equipa.

Para configurar o TFS para criar pacotes do Azure, execute os seguintes passos:

1. No Visual Studio no seu computador de desenvolvimento, no menu Ver, selecione **equipa Explorer**, ou escolha Ctrl +\\, Ctrl + M. Na janela do Explorador de equipa, expanda o **baseia-se** nós ou escolha a **baseia-se** página e escolha **nova definição de compilação**.

   ![Nova opção de definição de compilação][0]
2. Escolha o **acionador** separador e especificar as condições pretendidas para quando pretender que o pacote de ser criada. Por exemplo, especifique **integração contínua** criar o pacote, sempre que uma origem de controlar dar entrada no ocorre.
3. Escolha o **definições da origem** separador e certifique-se a pasta do projeto está listada no **pasta de origem do controlo** coluna e o estado é **Active Directory**.
4. Escolha o **criar predefinições** separador e no controlador de compilação, verifique o nome do servidor de compilação.  Além disso, escolha a opção **cópia criar saída para a seguinte pasta de largar** e especifique a localização de largar pretendido.
5. Escolha o **processo** separador. No separador processos, escolha o modelo predefinido, em **criar**, escolha o projeto se já não está selecionada e, expanda o **avançadas** secção o **criar** secção da grelha.
6. Escolha **argumentos de MSBuild**e defina os argumentos de linha de comandos de MSBuild adequados, conforme descrito no passo 2 acima. Por exemplo, introduza **/t: publicar /p:PublishDir =\\\\myserver\\ignora\\**  para criar um pacote e copiar os ficheiros do pacote para a localização \\ \\myserver\\ignora\\:

   ![Argumentos de MSBuild][2]

   > [!NOTE]
   > Copiar os ficheiros para uma partilha pública torna mais fácil de implementar manualmente os pacotes do seu computador de desenvolvimento.
7. Testar o êxito da sua passo de compilação, verificando numa alteração ao seu projeto ou uma nova compilação em fila. Para uma nova compilação, no Explorador de equipa, em fila com o botão direito **todas as definições de compilação,** e, em seguida, escolha **fila nova compilação**.

## <a name="4-publish-a-package-using-a-powershell-script"></a>4: publicar um pacote com um Script do PowerShell
Esta secção descreve como construir um script do Windows PowerShell que irá publicar o resultado de pacote de aplicação de nuvem no Azure com parâmetros opcionais. Este script pode ser chamado depois do passo de compilação na automatização da compilação personalizado. Também pode ser chamado de atividades de fluxo de trabalho do modelo de processo no Visual Studio TFS equipa de compilação.

1. Instalar o [cmdlets Azure PowerShell] [ Azure PowerShell cmdlets] (v0.6.1 ou superior).
   Durante a fase de configuração do cmdlet, optar por instalar como um snap-in. Tenha em atenção que esta versão oficialmente suportado substitui a versão mais antiga fornecida via CodePlex, embora as versões anteriores foram numeradas 2.x.x.
2. Inicie o Azure PowerShell utilizando o menu de início ou página de início. Se iniciar desta forma, os cmdlets do PowerShell do Azure serão carregados.
3. Na linha de comandos do PowerShell, certifique-se de que os cmdlets do PowerShell estão carregados, introduzindo o comando parcial `Get-Azure` e, em seguida, premir a tecla de tabulação para a conclusão da instrução.

   Se premir a tecla de tabulação repetidamente, deverá ver vários comandos do Azure PowerShell.
4. Certifique-se de que o se pode ligar à sua subscrição do Azure através da importação de informações da sua subscrição do ficheiro. publishsettings.

   `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

   Em seguida, introduza o comando

   `Get-AzureSubscription`

   Mostra informações sobre a sua subscrição. Certifique-se de que tudo está correto.
5. Guardar o modelo de script fornecido no final deste artigo para a pasta de scripts como c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.
6. Consulte a secção de parâmetros do script. Adiciona ou modifica os valores predefinidos. Estes valores sempre podem ser substituídos mediante a transmissão nos parâmetros explícitos.
7. Certifique-se existir contas de serviço e o armazenamento de nuvem válido são criadas na sua subscrição que pode ser segmentada pelo script de publicar. A conta do storage (armazenamento de BLOBs) será utilizada para carregar e armazenar temporariamente o ficheiro de pacote e a configuração de implementação enquanto está a ser criada a implementação.

   * Para criar um novo serviço em nuvem, pode chamar este script ou utilizar o [portal do Azure](https://portal.azure.com). O nome do serviço em nuvem será utilizado como um prefixo de um nome de domínio completamente qualificado e, por conseguinte, tem de ser exclusivo.

         New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
   * Para criar uma nova conta de armazenamento, pode chamar este script ou utilizar o [portal do Azure](https://portal.azure.com). O nome de conta do storage será utilizado como um prefixo de um nome de domínio completamente qualificado e, por conseguinte, tem de ser exclusivo. Pode tentar utilizando o mesmo nome que o serviço em nuvem.

         New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
8. Chamar o script diretamente a partir do Azure PowerShell ou ligar a cópia de segurança este script para a automatização de compilação do anfitrião para ocorrer após a compilação de pacote.

   > [!IMPORTANT]
   > O script sempre terá de eliminar ou substituir as implementações existentes por predefinição, se estes forem detetados. Isto é necessário para ativar a entrega contínua da automatização em que seja possível sem pedir intervenção do utilizador.
   >
   >

   **Cenário de exemplo 1:** a implementação contínua para o ambiente de teste de um serviço:

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   Isto é, normalmente, seguido de cópia de segurança teste executar verificação e uma alternância de VIP. A alternância de VIP pode ser efetuada através de [portal do Azure](https://portal.azure.com) ou utilizando o cmdlet Move-implementação.

   **Cenário de exemplo 2:** a implementação contínua para o ambiente de produção de um serviço de teste dedicado

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   **Ambiente de trabalho remoto:**

   Se o ambiente de trabalho remoto está ativado no seu projeto do Azure, terá de efetuar passos adicionais de uso individual para garantir que o certificado de serviço em nuvem correto é carregado para todos os serviços de cloud visados por este script.

   Localize os valores de thumbprint do certificado esperados pelas suas funções. Os valores de thumbprint estão visíveis na secção de certificados do ficheiro de configuração de nuvem (ou seja, Serviceconfiguration). É também visível na caixa de diálogo de configuração do ambiente de trabalho remoto no Visual Studio quando a mostra opções e ver o certificado selecionado.

       <Certificates>
             <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
       </Certificates>

   Carregar certificados de ambiente de trabalho remoto como um passo de uma única configuração utilizando o seguinte script do cmdlet:

       Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

   Por exemplo:

       Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

   Em alternativa pode exportar o ficheiro PFX de certificado com chave privada e carregar certificados para cada nuvem de destino serviço utilizando o [portal do Azure](https://portal.azure.com).

   <!---
   Fixing broken links for Azure content migration from ACOM to DOCS. I'm unable to find a replacement links, so I'm commenting out this reference for now. The author can investigate in the future. "Read the following article to learn more: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx.
   -->
   **Atualize implementação vs. Eliminar a implementação -\> nova implementação**

   O script irá efetuar, por predefinição, uma implementação de atualização ($enableDeploymentUpgrade = 1) quando nenhum parâmetro seja transmitido ou o valor 1 é transmitido explicitamente. De instâncias único, este tem a vantagem de demorar menos tempo que uma implementação completa. Para as instâncias que necessitam de elevada disponibilidade que isto também tem a vantagem de abandonar o fileparser algumas instâncias em execução enquanto outros estão atualizadas (walking o seu domínio de atualização), para além do VIP não será eliminado.

   Implementação da atualização pode ser desativada no script ($enableDeploymentUpgrade = 0) ou através da transmissão *- enableDeploymentUpgrade 0* como um parâmetro, que altera o comportamento de script para eliminar primeiro qualquer implementação existente e, em seguida, crie uma nova implementação.

   > [!IMPORTANT]
   > O script sempre terá de eliminar ou substituir as implementações existentes por predefinição, se estes forem detetados. Isto é necessário para ativar a entrega contínua da automatização em que seja possível sem pedir intervenção do utilizador/operador.
   >
   >

## <a name="5-publish-a-package-using-tfs-team-build"></a>5: publicar um pacote com a compilação de equipa do TFS
Este passo opcional liga o TFS equipa de compilação para o script criado no passo 4, que processa a publicação de compilação do pacote para o Azure. Isto envolve modificar o modelo de processo utilizado pela sua definição de compilação para que seja executado uma atividade de publicar no fim do fluxo de trabalho. A atividade de publicar irá executar o comando PowerShell transmitir nos parâmetros de compilação. Resultado do MSBuild destina-se e publicar o script será direcionado para a saída de compilação padrão.

1. Edite a definição de compilação responsável por contínua implementar.
2. Selecione o **processo** separador.
3. Siga [estas instruções](http://msdn.microsoft.com/library/dd647551.aspx) para adicionar um projeto de atividade para o modelo de processo de compilação, transfira o modelo predefinido, adicione-ao projeto e registá-lo. Atribua o modelo de processo de compilação um novo nome, tal como AzureBuildProcessTemplate.
4. Volte à **processo** separador e utilizar **Mostrar detalhes** para mostrar uma lista de modelos de processo de compilação disponíveis. Escolha o **novo...**  botão e navegue para o projeto que acabou de adicionar e verificado. Localize o modelo que acabou de criar e escolha **OK**.
5. Abra o modelo de processo selecionado para edição. Pode abrir diretamente no estruturador de fluxo de trabalho ou no editor de XML para trabalhar com o XAML.
6. Adicione a seguinte lista de argumentos novo como separados de linha de itens no separador argumentos do estruturador de fluxo de trabalho. Todos os argumentos devem ter direção = no e escreva = cadeia. Estes irão ser utilizados para parâmetros de fluxo da definição de compilação no fluxo de trabalho, que, em seguida, obter utilizada para chamar o script de publicar.

       SubscriptionName
       StorageAccountName
       CloudConfigLocation
       PackageLocation
       Environment
       SubscriptionDataFileLocation
       PublishScriptLocation
       ServiceName

   ![Lista de argumentos][3]

   O XAML correspondente tem o seguinte aspeto:

       <Activity  _ />
         <x:Members>
           <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
           <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
           <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
           <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
           <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
           <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
           <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
           <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
           <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
           <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
           <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
           <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
           <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
           <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
           <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
           <x:Property Name="GetVersion" Type="InArgument(x:String)" />
           <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
           <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
           <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
           <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
           <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
           <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
           <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
           <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
           <x:Property Name="Environment" Type="InArgument(x:String)" />
           <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
           <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
           <x:Property Name="ServiceName" Type="InArgument(x:String)" />
         </x:Members>

         <this:Process.MSBuildArguments>
7. Adicione uma nova sequência no final de executar no agente:

   1. Comece por adicionar uma atividade de instrução se verificar a existência de um ficheiro de script válido. Defina a condição para este valor:

          Not String.IsNullOrEmpty(PublishScriptLocation)
   2. O em seguida, caso da instrução se, adicione uma nova atividade de sequência. Definir o nome a apresentar para 'Publish de início'
   3. Com o início publicar sequência ainda selecionada, adicionar a lista seguinte de novas variáveis como separado de linha de itens no separador variáveis do estruturador de fluxo de trabalho. Todas as variáveis devem ter o tipo de variável = cadeia e âmbito = início publicar. Estes irão ser utilizados para parâmetros de fluxo da definição de compilação no fluxo de trabalho, que, em seguida, obter utilizada para chamar o script de publicar.

      * SubscriptionDataFilePath, do tipo cadeia
      * PublishScriptFilePath, do tipo cadeia

        ![Novas variáveis][4]
   4. Se estiver a utilizar o TFS 2012 ou anterior, adicione uma atividade ConvertWorkspaceItem no início da sequência de novo. Se estiver a utilizar o TFS 2013 ou posterior, adicione uma atividade GetLocalPath no início da sequência de novo. Para um ConvertWorkspaceItem, definir as propriedades da seguinte forma: direção = ServerToLocal, DisplayName = 'Converter a publicar o nome de ficheiro de script', entrada = 'PublishScriptLocation' resultado 'PublishScriptFilePath', área de trabalho de = = 'Área de trabalho'. Para uma atividade GetLocalPath, defina a propriedade IncomingPath para 'PublishScriptLocation' e o resultado a 'PublishScriptFilePath'. Esta atividade converte o caminho para o script de publicação do TFS localizações do servidor (se aplicável) para um caminho de disco local padrão.
   5. Se estiver a utilizar o TFS 2012 ou anterior, adicione outra atividade ConvertWorkspaceItem no fim da sequência de novo. Direção = ServerToLocal, DisplayName = 'Converter a subscrição filename', entrada = 'SubscriptionDataFileLocation' resultado 'SubscriptionDataFilePath', área de trabalho de = = 'Área de trabalho'. Se estiver a utilizar o TFS 2013 ou posterior, adicione outro GetLocalPath. IncomingPath = 'SubscriptionDataFileLocation' e o resultado = 'SubscriptionDataFilePath'.
   6. Adicione uma atividade de InvokeProcess no fim da sequência de novo.
      Esta atividade chama o PowerShell.exe com os argumentos transmitidos através da definição de compilação.

      + Argumentos = String.Format ("-""{0}" "- serviceName {1} - storageAccountName {2} - packageLocation""{3}" "- cloudConfigLocation""{4}" "- subscriptionDataFile""{5}" "- selectedSubscription {6} de ficheiros-ambiente""{7}" "", PublishScriptFilePath ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, ambiente)
      + DisplayName = executar script de publicar
      + FileName = "PowerShell" (incluir as aspas)
      + OutputEncoding = System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)
   7. No **lidar com a saída padrão** secção caixa de texto do InvokeProcess, defina o valor de caixa de texto para 'data'. Esta é uma variável para armazenar os dados de saída padrão.
   8. Adicionar uma atividade WriteBuildMessage apenas abaixo o **lidar com a saída padrão** secção. Definir a importância = 'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High' e a mensagem = 'data'. Isto garante que a saída padrão do script irá escrever a saída da compilação.
   9. No **processar saída de erro** secção caixa de texto do InvokeProcess, defina o valor de caixa de texto para 'data'. Esta é uma variável para armazenar os dados de erro padrão.
   10. Adicionar uma atividade WriteBuildError apenas abaixo o **processar saída de erro** secção. Definir a mensagem = 'data'. Isto garante que os erros de padrão do script irá escrever a saída de erro de compilação.
   11. Corrigir os eventuais erros, indicados pelo azuis marcas de exclamação. Coloque o cursor sobre as marcas de exclamação para obter uma sugestão sobre o erro. Guarde o fluxo de trabalho para limpar erros.

   O resultado final das atividades de fluxo de trabalho publicar terá este aspeto no designer:

   ![Atividades de fluxo de trabalho][5]

   O resultado final das atividades de fluxo de trabalho publicar terá este aspeto em XAML:

       <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
           <If.Then>
             <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
               <Sequence.Variables>
                 <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                 <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
               </Sequence.Variables>
               <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
               <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
               <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
                 <mtbwa:InvokeProcess.ErrorDataReceived>
                   <ActivityAction x:TypeArguments="x:String">
                     <ActivityAction.Argument>
                       <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                     </ActivityAction.Argument>
                     <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
                   </ActivityAction>
                 </mtbwa:InvokeProcess.ErrorDataReceived>
                 <mtbwa:InvokeProcess.OutputDataReceived>
                   <ActivityAction x:TypeArguments="x:String">
                     <ActivityAction.Argument>
                       <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                     </ActivityAction.Argument>
                     <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                   </ActivityAction>
                 </mtbwa:InvokeProcess.OutputDataReceived>
               </mtbwa:InvokeProcess>
             </Sequence>
           </If.Then>
         </If>
       </Sequence>
8. Guarde o fluxo de trabalho de modelo de processo de compilação e registar este ficheiro.
9. Edite a definição de compilação (fechá-lo se já estiver aberto) e selecione o **novo** botão se ainda não vir o novo modelo na lista de modelos de processo.
10. Defina o parâmetro de valores de propriedades na secção div da seguinte forma:

    1. CloudConfigLocation = "c:\\ignora\\app.publish\\Serviceconfiguration ' *este valor é derivado do: ($PublishDir)ServiceConfiguration.Cloud.cscfg*
    2. PackageLocation = "c:\\ignora\\app.publish\\ContactManager.Azure.cspkg' *este valor é derivado do: (. cspkg de)($ProjectName) $PublishDir*
    3. PublishScriptLocation = "c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'
    4. ServiceName = 'mycloudservicename' *utilizar aqui o nome do serviço em nuvem correto*
    5. Ambiente = 'Teste'
    6. StorageAccountName = 'mystorageaccountname' *utilizar aqui o nome da conta de armazenamento adequado*
    7. SubscriptionDataFileLocation = "c:\\scripts\\WindowsAzure\\Subscription.xml'
    8. SubscriptionName = 'default'

    ![Valores de propriedade de parâmetros][6]
11. Guarde as alterações à definição de compilação.
12. Uma compilação executar ambas a compilação de pacote e publicar da fila. Se tiver um acionador definido para a integração contínua, será executado este comportamento na entrada cada.

### <a name="publishcloudserviceps1-script-template"></a>Modelo de script PublishCloudService.ps1
```
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )


function Publish()
{
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
    if ($deployment.Name -ne $null)
    {
        switch ($alwaysDeleteExistingDeployments)
        {
            1
            {
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                        DeleteDeployment
                        CreateNewDeployment

                    }
                } # switch ($enableDeploymentUpgrade)
            }
            0
            {
                Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                exit
            }
        } #switch ($alwaysDeleteExistingDeployments)
    } else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
    write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

    $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"

    StartInstances
}

function UpgradeDeployment()
{
    write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
    $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

    write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
    $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

    write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"

}

function StartInstances()
{
    write-progress -id 4 -activity "Starting Instances" -status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running')
    {
        $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $oldStatusStr = @("") * $deployment.RoleInstanceList.Count

    while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
    {
        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
            $i = $i + 1
        }

        sleep -Seconds 1

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    }

    $i = 1
    foreach ($roleInstance in $deployment.RoleInstanceList)
    {
        $instanceName = $roleInstance.InstanceName
        $instanceStatus = $roleInstance.InstanceStatus

        if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
        {
            $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
            Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
        }

        $i = $i + 1
    }

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $opstat = $deployment.Status

    write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
    foreach ($roleInstance in $roleInstanceList)
    {
        if ($roleInstance.InstanceStatus -ne "ReadyRole")
        {
            return $false
        }
    }

    return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
```

## <a name="next-steps"></a>Passos seguintes
Para ativar a depuração remota quando utilizar o fornecimento contínuo, consulte [ativar a depuração remota quando utilizar o fornecimento contínuo para publicar no Azure](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md).

[Team Foundation Build Service]: https://msdn.microsoft.com/library/ee259687.aspx
[.NET Framework 4]: https://www.microsoft.com/download/details.aspx?id=17851
[.NET Framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653
[.NET Framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643
[Scale out your build system]: https://msdn.microsoft.com/library/dd793166.aspx
[Deploy and configure a build server]: https://msdn.microsoft.com/library/ms181712.aspx
[Azure PowerShell cmdlets]: /powershell/azureps-cmdlets-docs
[the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
[0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png
[2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
[3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
[4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
[5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
[6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
