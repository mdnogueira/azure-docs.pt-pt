---
title: "Implementação contínua do DSC de automatização do Azure com Chocolatey | Microsoft Docs"
description: "Implementação contínua de DevOps com Automation DSC do Azure e o Gestor de pacote Chocolatey.  Exemplo com o modelo JSON ARM completo e de origem do PowerShell."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: c0baa411-eb76-4f91-8d14-68f68b4805b6
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/29/2016
ms.author: golive
ms.openlocfilehash: f23d7374a8954a0a95853fa9e00b54a8d9c468c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-dsc-and-chocolatey"></a>Exemplo de utilização: A implementação contínua para máquinas virtuais utilizando o DSC de automatização e Chocolatey
Um universo de DevOps existem diversas ferramentas para ajudá-lo com vários pontos no pipeline de integração contínua.  Configuração de estado de Desired de automatização do Azure (DSC) é uma adição de nova boas-vindas para as opções que podem utilizar agrupamentos de DevOps.  Este artigo demonstra definição cópia contínua implementação (CD) para um computador Windows.  Pode expandir facilmente técnica para incluir tantos computadores Windows, conforme necessário na função (um web site, por exemplo) e a partir daí, bem como funções adicionais.

![Implementação contínua para as VMs do IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Um nível elevado
Não existe bastantes bits acontecimentos aqui, mas Felizmente pode ser dividido em dois processos principais: 

* Escrever código e testá-lo, em seguida, criar e publicar pacotes de instalação para as versões principais e secundários do sistema. 
* Criar e gerir VMs que irão instalar e executar o código dos pacotes.  

Assim que estes processos core são implementados, é um passo abreviado para atualizar automaticamente o pacote em execução em qualquer VM específica como novas versões são criadas e implementadas.

## <a name="component-overview"></a>Descrição geral do componente
O pacote, tais como gestores [apt get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) são pretty bem conhecidos no mundo de Linux, mas não for grande parte do mundo Windows.  [Chocolatey](https://chocolatey.org/) faz essas coisa e de autoria de Scott Hanselman [blogue](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) no tópico é uma excelente introdução.  Um nutshell, Chocolatey permite-lhe instalar pacotes do repositório central de pacotes num sistema Windows utilizando a linha de comandos.  Pode criar e gerir o seus próprios repositório e Chocolatey pode instalar pacotes a partir de qualquer número de repositórios que designar.

Pretender configuração de estado (DSC) ([descrição geral](https://technet.microsoft.com/library/dn249912.aspx)) é uma ferramenta do PowerShell que permite-lhe declarar a configuração que pretende para uma máquina.  Por exemplo, pode dizer, "quero Chocolatey instalado, pretendo que o IIS instalado, pretendo que abrir a porta 80, pretendo versão 1.0.0 do meu site instalado."  O DSC Local Configuration Manager (o MMC) implementa que a configuração. Um servidor de solicitação do DSC contém um repositório de configurações para as máquinas. O MMC em cada máquina verifica periodicamente para ver se a configuração da sua corresponde a configuração armazenada. -Pode comunicar Estado ou tente recuperar a máquina em alinhamento com a configuração armazenada. Pode editar a configuração armazenada no servidor de solicitação para fazer com que um computador ou um conjunto de máquinas que entrem em alinhamento com a configuração foi alterada.

A automatização do Azure é um serviço gerido no Microsoft Azure que permite-lhe automatizar várias tarefas utilizando runbooks, nós, as credenciais, recursos e recursos, tais como as agendas e variáveis globais. Automation DSC do Azure expande esta capacidade de automatização para incluir ferramentas de DSC do PowerShell.  Eis um ótimo [descrição geral](automation-dsc-overview.md).

Um recurso de DSC é um módulo de código que tem capacidades específicas, como a gestão de rede do Active Directory ou o SQL Server.  O recurso de DSC Chocolatey sabe como aceder a um servidor NuGet (entre outras), pacotes de transferir, instalar pacotes e assim sucessivamente.  Existem muitos outros recursos de DSC no [galeria do PowerShell](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).  Estes módulos são instalados no seu servidor de solicitação do DSC do Azure Automation (por si), para poderem ser utilizados pelas configurações.

Modelos do Resource Manager fornecem uma forma declarativa de gerar a sua infraestrutura - coisas como redes, sub-redes, segurança de rede e encaminhamento, carregar balanceadores, NICs, VMs e assim sucessivamente.  Eis um [artigo](../azure-resource-manager/resource-manager-deployment-model.md) que compara o modelo de implementação Resource Manager (declarativo) com a gestão de serviço do Azure (ASM ou clássico) implementação modelo (imperativo) e descreve os principais fornecedores de recursos, computação, armazenamento e rede.

Uma funcionalidade-chave de um modelo do Resource Manager é a capacidade de instalar uma extensão VM para a VM, que está aprovisionado.  Uma extensão VM tem capacidades específicas, tais como executar um script personalizado, instalar o software de antivírus ou executar um script de configuração de DSC.  Existem muitos outros tipos de extensões VM.

## <a name="quick-trip-around-the-diagram"></a>Viagem rápida em torno do diagrama
Iniciar na parte superior, escrever o seu código, criar e testar, em seguida, criar um pacote de instalação.  Chocolatey pode processar vários tipos de pacotes de instalação, tais como ZIP MSI, MSU.  E tiver o poder do PowerShell para efetuar a instalação real se capacidades nativas do Chocolatey não são bastante até-lo.  Colocar o pacote no avisou acessível – um repositório de pacote.  Este exemplo de utilização utiliza uma pasta pública numa conta de armazenamento de Blobs do Azure, mas pode ser qualquer local.  Chocolatey nativamente funciona com servidores do NuGet e alguns outros utilizadores para a gestão dos metadados de pacote.  [Este artigo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descreve as opções.  Este exemplo de utilização utiliza NuGet.  Um Nuspec é metadados sobre os pacotes.  O Nuspec é "compilar" do NuPkg e armazenado num servidor NuGet.  Quando a configuração solicita um pacote de por nome e faz referência a um servidor do NuGet, os recursos de DSC Chocolatey (agora na VM) grabs o pacote e instala-o por si.  Também pode solicitar uma versão específica de um pacote.

Na parte inferior esquerda de imagem, há um modelo Azure Resource Manager (ARM).  Neste exemplo de utilização, a extensão da VM regista a VM com o servidor de solicitação de DSC na automatização do Azure (ou seja, um servidor de solicitação) como um nó.  A configuração é armazenada no servidor de solicitação.  Na realidade, são armazenada duas vezes: uma vez como texto simples e depois compilada como um ficheiro MOF (para aqueles que sabe mais sobre estas ações.)  No portal, o MOF é uma "configuração de nó" (por oposição a simplesmente "configuração de").  É o artefacto que está associada a um nó para que o nó saberá a respetiva configuração.  Os detalhes abaixo mostram como atribuir a configuração do nó para o nó.

Provavelmente já estiver a fazer o bits na parte superior, ou a maior parte do mesmo.  Criar o nuspec, compilar e armazenar num servidor NuGet são uma pequena coisa.  E já estiver a gerir as VMs.  Colocar o passo seguinte para a implementação contínua necessita de configurar o servidor de solicitação (uma vez), registar os nós com a mesma (uma vez), criação e armazenar a configuração não existe (inicialmente).  Como os pacotes são atualizados e implementados para o repositório, atualize a configuração e a configuração do nó no servidor de solicitação (Repita conforme necessário).

Se não estiver a começar com um modelo ARM, que também é OK.  Existem cmdlets do PowerShell concebidos para ajudar a registar as suas VMs com o servidor de solicitação e todas as restantes. Para obter mais detalhes, consulte este artigo: [máquinas de integração de gestão do Automation DSC do Azure](automation-dsc-onboarding.md)

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Passo 1: Configurar a conta de servidor e a automatização de solicitação
Numa linha de comandos do PowerShell (Add-AzureRmAccount) autenticada: (pode demorar alguns minutos enquanto o servidor de solicitação é configurado)

    New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

Pode colocar a sua conta de automatização em qualquer uma das regiões do seguintes (também conhecido como localização): EUA Leste 2, Sul Central dos EUA, Virginia us dos EUA, Europa Ocidental, Sudeste asiático, leste do Japão, Índia Central e Sudeste da Austrália, Canadá Central, Europa do Norte.

## <a name="step-2-vm-extension-tweaks-to-the-arm-template"></a>Passo 2: Tweaks de extensão VM para o modelo ARM
Detalhes do registo de VM (utilizando a extensão da VM do PowerShell DSC) do fornecidos neste [modelo de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).  Este passo regista a nova VM com o servidor de solicitação na lista de nós de DSC.  Parte deste registo é especificar a configuração do nó ser aplicados ao nó.  Esta configuração de nó não é necessário ainda existe no servidor de solicitação, pelo que é OK passo 4 é onde este procedimento é efetuado pela primeira vez.  Mas aqui no passo 2 é necessário decidir o nome do nó e o nome da configuração.  Neste exemplo de utilização, o nó é 'isvbox' e a configuração estiver 'ISVBoxConfig'.  Por isso, o nome de configuração do nó (para ser especificado no DeploymentTemplate.json) é 'ISVBoxConfig.isvbox'.  

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Passo 3: Adicionar os recursos de DSC necessários para o servidor de solicitação
A galeria do PowerShell está instrumentada instalar recursos de DSC na sua conta de automatização do Azure.  Navegue para o recurso que pretende e clique no botão "Implementar da automatização do Azure".

![Exemplo de galeria do PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Outra técnica recentemente adicionada ao Portal do Azure permite-lhe obter novos módulos ou atualizar módulos existentes. Clicar no recurso de conta de automatização, o mosaico de recursos e, finalmente, o mosaico de módulos.  O ícone de procurar galeria permite-lhe ver a lista de módulos na galeria, desagregar para obter detalhes e, em última análise importar para a sua conta de automatização. Esta é uma excelente forma de manter os módulos atualizados ocasionalmente. Além disso, a funcionalidade de importação verifica dependências com outros módulos para garantir a que nada obtém fora de sincronização.

Ou, existe a abordagem manual.  A estrutura de pastas de um módulo de integração do PowerShell para um computador com o Windows é ligeiramente diferente da estrutura da pasta esperada pela automatização do Azure.  Isto requer um pequeno ajuste da sua parte.  Mas não é difícil e este procedimento é efetuado apenas uma vez por recurso (a menos que pretenda atualizá-la no futuro.)  Para obter mais informações sobre a criação de módulos de integração do PowerShell, consulte este artigo: [criação de módulos de integração para a automatização do Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

* Instale o módulo que precisa na sua estação de trabalho, da seguinte forma:
  * Instalar [Windows Management Framework, versão 5](http://aka.ms/wmf5latest) (não é necessária para o Windows 10)
  * `Install-Module –Name MODULE-NAME`< — grabs o módulo a partir da galeria do PowerShell 
* Copie a pasta do módulo de `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` para uma pasta temporária 
* Eliminar exemplos e documentação da pasta principal 
* Zip a pasta principal, o ficheiro ZIP de nomenclatura exatamente o mesmo que a pasta 
* Coloque o ficheiro ZIP para uma localização acessível do HTTP, tal como o armazenamento de BLOBs numa conta de armazenamento do Azure.
* Execute do PowerShell:
  
      New-AzureRmAutomationModule `
          -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
          -Name MODULE-NAME –ContentLink "https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip"

O exemplo incluído efetua estes passos para cChoco e xNetworking. Consulte o [notas](#notes) para processamento especial para cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Passo 4: Adicionar a configuração do nó para o servidor de solicitação
Não há nada especiais sobre a primeira vez, que importar a configuração para o servidor de solicitação e compilação.  Todos os subsequentes importação/compila da mesma configuração exatamente têm o mesmo aspeto.  Sempre que o pacote de atualização e precisar de enviá-lo a para produção efetuar este passo depois de se certificar que o ficheiro de configuração está correto – incluindo a nova versão do pacote.  Segue-se o ficheiro de configuração e o PowerShell:

ISVBoxConfig.ps1:

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking

        Node "isvbox" {   

            cChocoInstaller installChoco 
            { 
                InstallDir = "C:\choco" 
            }

            WindowsFeature installIIS 
            { 
                Ensure="Present" 
                Name="Web-Server" 
            }

            xFirewall WebFirewallRule 
            { 
                Direction = "Inbound" 
                Name = "Web-Server-TCP-In" 
                DisplayName = "Web Server (TCP-In)" 
                Description = "IIS allow incoming web site traffic." 
                DisplayGroup = "IIS Incoming Traffic" 
                State = "Enabled" 
                Access = "Allow" 
                Protocol = "TCP" 
                LocalPort = "80" 
                Ensure = "Present" 
            }

            cChocoPackageInstaller trivialWeb 
            {            
                Name = "trivialweb" 
                Version = "1.0.0" 
                Source = “MY-NUGET-V2-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

Novo-ConfigurationScript.ps1:

    Import-AzureRmAutomationDscConfiguration ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 ` 
        -Published –Force

    $jobData = Start-AzureRmAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -ConfigurationName ISVBoxConfig 

    $compilationJobId = $jobData.Id

    Get-AzureRmAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -Id $compilationJobId

Resultado estes passos uma nova configuração de nó com o nome "ISVBoxConfig.isvbox", que está a ser colocada no servidor de solicitação.  O nome de configuração do nó foi concebido como "configurationName.nodeName".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Passo 5: Criar e manter os metadados de pacote
Para cada pacote que colocar no repositório de pacote, é necessário um nuspec que a descreve.  Esse nuspec tem de ser compilado e armazenado no seu servidor NuGet. Este processo é descrito [aqui](http://docs.nuget.org/create/creating-and-publishing-a-package).  Pode utilizar MyGet.org como um servidor do NuGet.  Propor este serviço, mas tem uma arranque de SKU é gratuito.  Em NuGet.org encontrará instruções sobre como instalar o próprio servidor NuGet para os pacotes de privada.

## <a name="step-6-tying-it-all-together"></a>Passo 6: Escrever todos os em conjunto
Sempre que uma versão transmite pergunta e resposta e for aprovada para implementação, o pacote é criado, nuspec e nupkg atualizado e implementados no servidor do NuGet.  Além disso, a configuração (passo 4 acima) têm de ser atualizada para concordar com o novo número de versão.  Tem de ser enviada para o servidor de solicitação e compilada.  A partir desse ponto no, é cópias de segurança para as VMs que dependem de que a configuração para solicitar a atualização e instalá-la.  Cada uma destas atualizações são simples - apenas uma linha ou dois do PowerShell.  No caso do Visual Studio Team Services, alguns deles são encapsulados em tarefas de compilação que podem ser ligadas em cadeia na compilação.  Isto [artigo](https://www.visualstudio.com/en-us/docs/alm-devops-feature-index#continuous-delivery) fornece mais detalhes.  Isto [repositório do GitHub](https://github.com/Microsoft/vso-agent-tasks) fornece detalhes sobre as várias tarefas de compilação disponíveis.

## <a name="notes"></a>Notas
Neste exemplo de utilização começa com uma VM a partir de uma imagem do Windows Server 2012 R2 genérica na galeria do Azure.  Pode iniciar a partir de qualquer imagem armazenada e, em seguida, otimizar a partir daí, com a configuração de DSC.  No entanto, a alteração da configuração que é integrada com uma imagem é muito mais difícil que atualizar dinamicamente a configuração através do DSC.

Não tem de utilizar um modelo ARM e a extensão VM para utilizar esta técnica com as suas VMs.  E as suas VMs não têm de ser no Azure para estar sob gestão do CD.  Tudo o que é necessário é que Chocolatey seja instalado e o MMC configurado na VM para que confie em que é o servidor de solicitação.  

Obviamente, quando atualizar um pacote de uma VM que estiver em produção, terá de efetuar essa VM fora de rotação enquanto a atualização é instalada.  Como fazê-lo varia amplamente.  Por exemplo, com uma VM por trás de um balanceador de carga do Azure, pode adicionar uma sonda personalizada.  Ao atualizar a VM, tem o ponto final de pesquisa devolver um 400.  Otimizar necessária fazer com que esta alteração pode estar no interior da configuração, como pode otimizar para mudá-lo novamente para devolver um 200 assim que a atualização estiver concluída.

Origem completo para este exemplo de utilização está a ser [este projeto do Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) no GitHub.

## <a name="related-articles"></a>Artigos relacionados
* [Descrição geral do DSC da automatização do Azure](automation-dsc-overview.md)
* [Cmdlets do DSC da automatização do Azure](https://msdn.microsoft.com/library/mt244122.aspx)
* [Máquinas de integração de gestão do Automation DSC do Azure](automation-dsc-onboarding.md)

