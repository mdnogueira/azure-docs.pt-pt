---
title: Utilizar Scripts do Windows PowerShell para publicar a ambientes de teste e desenvolvimento | Microsoft Docs
description: Saiba como utilizar scripts do Windows PowerShell a partir do Visual Studio para publicar no desenvolvimento e ambientes de teste.
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 5fff1301-5469-4d97-be88-c85c30f837c1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: 4e9409aac836a60e7ea01261840c084ff09e954e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>Utilizar scripts do Windows PowerShell para publicar em ambientes de desenvolvimento e teste

Quando cria uma aplicação web no Visual Studio, pode gerar um script do Windows PowerShell que pode utilizar mais tarde para automatizar a publicação do seu site para o Azure como uma aplicação Web no App Service do Azure ou uma máquina virtual. Pode editar e expandir o script do Windows PowerShell no editor do Visual Studio para se adequarem às suas necessidades ou integrar o script a compilação existente, teste e publicação de scripts.

Utilizar estes scripts, pode aprovisionar versões personalizadas (também conhecido como ambientes de desenvolvimento e teste) do seu site para utilização temporária. Por exemplo, pode configurar uma versão específica do seu site numa máquina virtual do Azure ou o bloco de transição num Web site para executar um conjunto de teste, reproduzir de erros, teste uma correção de erros, versão de avaliação de uma alteração proposta ou configurar um ambiente personalizado para um demonstração ou apresentação. Depois de criar um script que publica o seu projeto, pode recriar ambientes idênticos executando o script conforme necessário ou execute o script com a suas próprias compilação da aplicação web para criar um ambiente personalizado para fins de teste.

## <a name="prerequisites"></a>Pré-requisitos

* Azure SDK 2.3 ou posterior. Consulte [transferências do Visual Studio](http://go.microsoft.com/fwlink/?LinkID=624384). (Não precisa do Azure SDK para gerar os scripts para projetos web. Esta funcionalidade é para projetos web, não funções da web nos serviços em nuvem.)
* O Azure PowerShell 0.7.4 ou posterior. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
* [Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) ou posterior.

## <a name="additional-tools"></a>Ferramentas adicionais

Ferramentas adicionais e recursos para trabalhar com o PowerShell no Visual Studio para desenvolvimento do Azure estão disponíveis. Consulte [PowerShell Tools para Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>Gerar os scripts de publicar

Pode gerar os scripts de publicação para uma máquina virtual que aloja o seu Web site quando criar um novo projeto seguindo [estas instruções](virtual-machines/windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Também pode [gerar publicar scripts para as web apps no App Service do Azure](app-service/app-service-web-get-started-dotnet.md).

## <a name="scripts-that-visual-studio-generates"></a>Scripts que gera o Visual Studio

Visual Studio gera uma pasta de nível de solução denominada **PublishScripts** que contém dois ficheiros do Windows PowerShell, um script de publicar para a máquina virtual ou Web site e um módulo que contém as funções que pode utilizar os scripts. Visual Studio também gera um ficheiro no formato JSON que especifica os detalhes do projeto que está a implementar.

### <a name="windows-powershell-publish-script"></a>Script de publicar do Windows PowerShell

O script de publicar contém passos de publicar específicas para implementar um Web site ou a máquina virtual. O Visual Studio fornece sintaxe coloração para desenvolvimento do Windows PowerShell. Ajuda para as funções está disponível e livremente pode editar as funções no script para se adequarem às suas necessidades em constante mudança.

### <a name="windows-powershell-module"></a>Módulo do Windows PowerShell

O módulo do Windows PowerShell que gera o Visual Studio contém funções que utiliza o script de publicar. Estas funções do Azure PowerShell não se destinam a ser modificado. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a name="json-configuration-file"></a>Ficheiro de configuração JSON
O ficheiro JSON é criado no **configurações** pasta e contém dados de configuração que especifica exatamente quais os recursos para implementar no Azure. O nome do ficheiro que gera o Visual Studio é projeto-nome-WAWS-dev.json se tiver criado um Web site ou o nome de projeto-VM-dev.json se tiver criado uma máquina virtual. Eis um exemplo de um ficheiro de configuração JSON que é gerado quando criar um Web site. A maioria dos valores são facilmente compreensíveis. O nome do site é gerado pelo Azure, pelo que não poderá corresponder ao nome do seu projeto.

```json
{
    "environmentSettings": {
        "webSite": {
            "name": "WebApplication26632",
            "location": "West US"
        },
        "databases": [{
            "connectionStringName": "DefaultConnection",
            "databaseName": "WebApplication26632_db",
            "serverName": "YourDatabaseServerName",
            "user": "sqluser2",
            "password": "",
            "edition": "",
            "size": "",
            "collation": "",
            "location": "West US"
        }]
    }
}
```

Quando cria uma máquina virtual, o ficheiro de configuração JSON semelhante ao seguinte. Um serviço em nuvem é criado como um contentor para a máquina virtual. A máquina virtual contém os pontos finais habituais para o acesso web através de HTTP e HTTPS, bem como pontos finais para o Web Deploy, que lhe permite publicar para o Web site a partir do seu computador local, o ambiente de trabalho remoto e o Windows PowerShell.

```json
{
    "environmentSettings": {
        "cloudService": {
            "name": "myusernamevm1",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myusernamevm1",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [{
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [{
            "connectionStringName": "",
            "databaseName": "",
            "serverName": "",
            "user": "",
            "password": ""
        }],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Pode editar a configuração de JSON para alterar o que acontece quando executa os scripts de publicar. O `cloudService` e `virtualMachine` secções são necessárias, mas pode eliminar o `databases` secção se não o tiver. As propriedades que estão vazios no ficheiro de configuração predefinidas que gera o Visual Studio são opcionais; essas propriedades que têm valores no ficheiro de configuração predefinida são necessárias.

Se tiver um site que tenha vários ambientes de implementação (conhecidos como ranhuras) em vez de um site de produção único no Azure, pode incluir o nome de ranhura nome o Web site no ficheiro de configuração JSON. Por exemplo, se tiver um site com o nome **mysite** e uma ranhura para o mesmo nome **testar** , em seguida, o URI é `mysite-test.cloudapp.net`, mas o nome correto a utilizar no ficheiro de configuração é mysite(test). Apenas pode efetuar este se o Web site e ranhuras já existem na sua subscrição. Se não existirem, criar o Web site ao executar o script sem especificar a ranhura de, em seguida, criar a ranhura no [portal do Azure](https://portal.azure.com/), e depois disso, execute o script com o nome do Web site modificadas. Para obter mais informações sobre as ranhuras de implementação para aplicações web, consulte [configurar ambientes para web apps no App Service do Azure de teste](app-service/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>Como executar os scripts de publicar

Se nunca tiver executado um script do Windows PowerShell antes, primeiro tem de definir a política de execução para ativar a execução de scripts. A política é uma funcionalidade de segurança para impedir que os utilizadores executem scripts do Windows PowerShell se não estiverem vulnerável a software maligno ou vírus que envolvam a execução de scripts.

### <a name="run-the-script"></a>Execute o script

1. Crie o pacote do Web Deploy para o seu projeto. Um pacote do Web Deploy é um arquivo comprimido (ficheiro. zip) que contêm os ficheiros que pretende copiar para o Web site ou a máquina virtual. Pode criar pacotes Web Deploy no Visual Studio para qualquer aplicação web.

![Criar Web implementar o pacote](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

Para obter mais informações, consulte [como: criar um pacote de implementação Web no Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). Também pode automatizar a criação do pacote do Web Deploy, conforme descrito em [Customizing e expandir o scripts[(#customizing-and-extending-publish-scripts) publicar]

1. No **Explorador de soluções**, abra o menu de contexto para o script e, em seguida, escolha **abra o ISE do PowerShell**.
2. Se executar scripts do Windows PowerShell neste computador pela primeira vez, abra uma janela de linha de comandos com privilégios de administrador e escreva o seguinte comando:

    ```powershell
    Set-ExecutionPolicy RemoteSigned
    ```

3. Inicie sessão no Azure utilizando o seguinte comando.

    ```powershell
    Add-AzureAccount
    ```

    Quando lhe for pedido, forneça o nome de utilizador e palavra-passe.

    Tenha em atenção que, ao automatizar o script, este método de fornecer credenciais do Azure não funciona. Em vez disso, deve utilizar o `.publishsettings` ficheiro para fornecer credenciais. Uma vez, utiliza apenas o comando **Get-AzurePublishSettingsFile** para transferir o ficheiro a partir do Azure e utilizar após essa data **importação AzurePublishSettingsFile** para importar o ficheiro. Para obter informações detalhadas, veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

4. (Opcional) Se pretender criar recursos do Azure, tais como a máquina virtual, a base de dados e o Web site sem a publicação da sua aplicação web, utilizam o **publicar WebApplication.ps1** comando com o **-configuração** argumento definido para o ficheiro de configuração JSON. Esta linha de comandos utiliza o ficheiro de configuração JSON para determinar quais os recursos que criar. Porque utiliza as predefinições para outros argumentos da linha de comandos, cria os recursos, mas não publicar a sua aplicação web. – Verboso opção dá-lhe obter mais informações sobre o que acontece.

    ```powershell
    Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json
    ```

5. Utilize o **publicar WebApplication.ps1** comando conforme mostrado dos seguintes exemplos para invocar o script e publicar a aplicação web. Se precisar de substituir as predefinições para qualquer um dos outros argumentos, tais como o nome da subscrição, publicar o nome do pacote, credenciais de máquina virtual ou as credenciais do servidor de base de dados, pode especificar os parâmetros. Utilize o **– verboso** opção para obter mais informações sobre o progresso do processo de publicação.

    ```powershell
    Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
    –SubscriptionName Contoso `
    -WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
    -DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
    -Verbose
    ```

    Se estiver a criar uma máquina virtual, o comando o seguinte aspeto. Este exemplo mostra também como especificar as credenciais para várias bases de dados. Para as máquinas virtuais que criar estes scripts, o certificado SSL não é de uma autoridade de raiz fidedigna. Por conseguinte, tem de utilizar o **– AllowUntrusted** opção.

    ```powershell
    Publish-WebApplication.ps1 `
    -Configuration C:\Path\ADVM-VM-test.json `
    -SubscriptionName Contoso `
    -WebDeployPackage C:\Path\ADVM.zip `
    -AllowUntrusted `
    -VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
    -DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
    -Verbose
    ```

    O script pode criar bases de dados, mas não criar servidores de base de dados. Se pretender criar um servidor de base de dados, pode utilizar o **New-AzureSqlDatabaseServer** função no módulo do Azure.

## <a name="customizing-and-extending-the-publish-scripts"></a>Personalizar e expandir os scripts de publicar
Pode personalizar o script de publicar e o ficheiro de configuração JSON. As funções no módulo do Windows PowerShell **AzureWebAppPublishModule.psm1** não se destina a ser modificado. Se pretender especificar uma base de dados diferente ou alterar algumas das propriedades da máquina virtual, edite o ficheiro de configuração de JSON. Se pretender expandir a funcionalidade do script para automatizar a criar e testar o projeto, pode implementar os stubs de função no **publicar WebApplication.ps1**.

Para automatizar a criação do seu projeto, adicione o código que chama MSBuild para `New-WebDeployPackage` conforme mostrado neste exemplo de código. O caminho para o comando de MSBuild é diferente consoante a versão do Visual Studio que instalou. Para obter o caminho correto, pode utilizar a função **Get-MSBuildCmd**, conforme mostrado neste exemplo.

### <a name="to-automate-building-your-project"></a>Para automatizar a criação do seu projeto
1. Adicionar o `$ProjectFile` parâmetro na secção global param.

    ```powershell
    [Parameter(Mandatory = $false)]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [String]
    $ProjectFile,
    ```

2. Copiar a função `Get-MSBuildCmd` no seu ficheiro de script.

    ```powershell
    function Get-MSBuildCmd
    {
            process
    {

                $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                    Sort-Object {[double]$_.PSChildName} -Descending |
                                    Select-Object -First 1 |
                                    Get-ItemProperty -Name MSBuildToolsPath |
                                    Select -ExpandProperty MSBuildToolsPath

                $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

            return Get-Item $path
        }
    }
    ```

3. Substitua `New-WebDeployPackage` com o seguinte código e substitua os marcadores de posição no construir a linha `$msbuildCmd`. Este código é para Visual Studio 2017. Se estiver a utilizar o Visual Studio 2015, altere o **VisualStudioVersion** propriedade `14.0` (`12.0` para Visual Studio 2013).

    ```powershell
    function New-WebDeployPackage
    {
        #Write a function to build and package your web application
    ```

    Para criar a sua aplicação web, use MsBuild.exe. Para obter ajuda, consulte a referência da linha de comandos MSBuild: [http://go.microsoft.com/fwlink/?LinkId=391339](http://go.microsoft.com/fwlink/?LinkId=391339)

    ```powershell
    Write-VerboseWithTime 'Build-WebDeployPackage: Start'

    $msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=15.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory

    Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
    ```

### <a name="start-execution-of-the-build-command"></a>Iniciar a execução do comando de compilação

```powershell
$job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru

if ($job.ExitCode -ne 0) {
    throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
}

#Obtain the project name
$projectName = (Get-Item $ProjectFile).BaseName

#Construct the path to web deploy zip package
$DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName

#Get the full path for the web deploy zip package. This is required for MSDeploy to work
$WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir

Write-VerboseWithTime 'Build-WebDeployPackage: End'

return $WebDeployPackage
}
```

1. Chamar o `New-WebDeployPackage` função antes desta linha: `$Config = Read-ConfigFile $Configuration` para aplicações web ou `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` para máquinas virtuais.

    ```powershell
    if($ProjectFile)
    {
    $WebDeployPackage = New-WebDeployPackage
    }
    ```

2. Invocar o script personalizado na linha de comandos utilizando a transmissão de `$Project` argumento, como no exemplo seguinte:

    ```powershell
    .\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
    -ProjectFile ..\WebApplication5\WebApplication5.csproj `
    -VMPassword @{Name="VMUser";Password="Test.123"} `
    -AllowUntrusted `
    -Verbose
    ```

    Para automatizar a testar da sua aplicação, adicione o código para `Test-WebApplication`. Lembre-se de que as linhas de anule os comentários **publicar WebApplication.ps1** onde estas funções são denominadas. Se não fornecer uma implementação, pode criar manualmente o seu projeto com o Visual Studio e, em seguida, execute o script de publicar para publicar no Azure.

## <a name="publishing-function-summary"></a>Função publicação resumo
Para obter ajuda para funções que pode utilizar a linha de comandos do Windows PowerShell, utilize o comando `Get-Help function-name`. A ajuda inclui ajuda de parâmetro e exemplos. O texto de ajuda mesmo é também nos ficheiros de origem do script **AzureWebAppPublishModule.psm1** e **publicar WebApplication.ps1**. O script e de ajuda está localizada no seu idioma de Visual Studio.

**AzureWebAppPublishModule**

| Nome da função | Descrição |
| --- | --- |
| Adicionar-AzureSQLDatabase |Cria uma nova base de dados SQL do Azure. |
| AzureSQLDatabases adicionar |Cria bases de dados SQL do Azure a partir dos valores no ficheiro de configuração JSON que gera o Visual Studio. |
| Adicionar-AzureVM |Cria uma máquina virtual do Azure e devolve o URL da VM implementada. A função configura os pré-requisitos e, em seguida, chama o **New-AzureVM** função (módulo do Azure) para criar uma nova máquina virtual. |
| AzureVMEndpoints adicionar |Adiciona pontos finais de entrada novo a uma máquina virtual e devolve a máquina virtual com o novo ponto final. |
| AzureVMStorage adicionar |Cria uma nova conta de armazenamento do Azure na subscrição atual. O nome da conta começa com "devtest" seguido de uma cadeia alfanumérica exclusiva. A função devolve o nome da nova conta de armazenamento. Especifique uma localização ou um grupo de afinidade para a nova conta de armazenamento. |
| AzureWebsite adicionar |Cria um Web site com o nome especificado e a localização. Esta função chama o **New-AzureWebsite** função no módulo do Azure. Se a subscrição já não inclui um Web site com o nome especificado, esta função cria o Web site e devolve um objeto de Web site. Caso contrário, devolve `$null`. |
| Subscrição de cópia de segurança |Guarda a subscrição do Azure atual no `$Script:originalSubscription` variável no âmbito de script. Esta função guarda a subscrição do Azure (tal como obtidas pelo `Get-AzureSubscription -Current`) e a conta de armazenamento e a subscrição que seja alterada por este script (armazenado na variável `$UserSpecifiedSubscription`) e a respetiva conta de armazenamento, no âmbito de script. Ao guardar os valores, pode utilizar uma função, tal como `Restore-Subscription`, para restaurar a conta de armazenamento e de subscrição atual original para o estado atual, se tiver alterado o estado atual. |
| Localizar-AzureVM |Obtém a máquina virtual do Azure especificada. |
| Formato DevTestMessageWithTime |Prepends a data e hora para uma mensagem. Esta função foi concebida para mensagens escritas para os fluxos de erro e Verbose. |
| Get-AzureSQLDatabaseConnectionString |Monta uma cadeia de ligação para ligar a uma base de dados SQL do Azure. |
| Get-AzureVMStorage |Devolve o nome da primeira conta de armazenamento com o padrão de nome "devtest*" (sensível) na localização especificada ou o grupo de afinidade. Se o "devtest*" conta de armazenamento não corresponde a localização ou o grupo de afinidade, a função ignora-lo. Especifique uma localização ou um grupo de afinidade. |
| Get-MSDeployCmd |Devolve um comando para executar a ferramenta de MsDeploy.exe. |
| Novo AzureVMEnvironment |Localiza ou cria uma máquina virtual na subscrição que correspondem aos valores no ficheiro de configuração JSON. |
| WebPackage publicar |Uma web e utiliza MsDeploy.exe publicou o pacote. Ficheiro zip para implementar os recursos para um Web site. Esta função não gera qualquer saída. Se a chamada para MSDeploy.exe falhar, a função emite uma exceção. Para obter uma saída mais detalhada, utilize o **-Verbose** opção. |
| WebPackageToVM publicar |Verifica os valores de parâmetros e, em seguida, chama o **publicar WebPackage** função. |
| Leitura ConfigFile |Valida o ficheiro de configuração JSON e devolve uma tabela hash de valores selecionados. |
| Subscrição de restauro |Repõe a subscrição atual para a subscrição original. |
| Teste AzureModule |Devolve `$true` se a versão do módulo do Azure instalado for 0.7.4 ou posterior. Devolve `$false` se o módulo não está instalado ou uma versão anterior. Esta função não tem parâmetros. |
| Teste AzureModuleVersion |Devolve `$true` se a versão do módulo do Azure é 0.7.4 ou posterior. Devolve `$false` se o módulo não está instalado ou uma versão anterior. Esta função não tem parâmetros. |
| Teste HttpsUrl |Converte o URL de entrada para um objeto de URI. Devolve `$True` se o URL absoluto e a esquema é https. Devolve `$false` se o URL é relativo, a esquema não é HTTPS ou cadeia de entrada não pode ser convertida para um URL. |
| Membro de teste |Devolve `$true` se uma propriedade ou método é um membro do objeto. Caso contrário, devolve `$false`. |
| Escrita ErrorWithTime |Escreve uma mensagem de erro prefixo com a hora atual. Esta função chama o **formato DevTestMessageWithTime** função preceder o tempo antes de escrever a mensagem para a sequência de erro. |
| Escrita HostWithTime |Escreve uma mensagem para o programa de anfitrião (**escrita anfitrião**) prefixo com a hora atual. O efeito de escrita para o programa anfitrião varia. A maioria dos programas nesse anfitrião do Windows PowerShell escrever estas mensagens de saída padrão. |
| Escrita VerboseWithTime |Escreve uma mensagem verbosa prefixo com a hora atual. Uma vez que chama **Write-Verbose**, a mensagem apresentada apenas quando o script é executado com o **verboso** parâmetro ou quando o **VerbosePreference** preferência estiver definida como **continuar**. |

**Publicar-WebApplication**

| Nome da função | Descrição |
| --- | --- |
| Novo AzureWebApplicationEnvironment |Cria recursos do Azure, tais como um Web site ou a máquina virtual. |
| Novo WebDeployPackage |Esta função não está implementada. Pode adicionar os comandos nesta função para compilar o projeto. |
| AzureWebApplication publicar |Publica uma aplicação web no Azure. |
| Publicar-WebApplication |Cria e implementa as aplicações Web, as máquinas virtuais, bases de dados SQL e contas de armazenamento para um projeto web do Visual Studio. |
| Teste-WebApplication |Esta função não está implementada. Pode adicionar os comandos nesta função para testar a aplicação. |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre os scripts do PowerShell através da leitura [Scripting com o Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx) e ver outros scripts do PowerShell do Azure no [Centro de scripts](https://azure.microsoft.com/documentation/scripts/).
