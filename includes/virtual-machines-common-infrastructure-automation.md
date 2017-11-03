# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Utilizar as ferramentas de automatização de infraestrutura com máquinas virtuais no Azure
Para criar e gerir máquinas virtuais do Azure (VMs) de uma forma consistente à escala, alguma forma de automatização é normalmente assim o desejar. Existem várias ferramentas e soluções que permitem-lhe automatizar a implementação de toda a infraestrutura do Azure e o ciclo de vida de gestão. Este artigo apresenta algumas das ferramentas de automatização de infraestrutura que pode utilizar no Azure. Estas ferramentas normalmente cabem para uma das abordagens seguintes:

- Automatizar a configuração de VMs
    - As ferramentas incluem [Ansible](#ansible), [Chef](#chef), e [Puppet](#puppet).
    - Ferramentas específicas para personalização de VM incluem [nuvem init](#cloud-init) para VMs com Linux, [PowerShell pretendido Estado Configuration (DSC)](#powershell-dsc)e o [extensão de Script personalizado do Azure](#azure-custom-script-extension) para todos os VMs do Azure.
 
- Automatizar a gestão de infraestrutura
    - As ferramentas incluem [Packer](#packer) para automatizar VM personalizada imagem baseia-se, e [Terraform](#terraform) para automatizar a infraestrutura de criar o processo.
    - [A automatização do Azure](#azure-automation) pode executar ações em toda a infraestrutura Azure e no local.

- Automatizar a implementação de aplicação e a entrega
    - Os exemplos incluem [Visual Studio Team Services](#visual-studio-team-services) e [Jenkins](#jenkins).


## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) é um motor de automatização para gestão de configuração, a criação de VM ou implementação de aplicação. Ansible utiliza um modelo sem agente, normalmente com chaves SSH para autenticar e gerir máquinas de destino. Tarefas de configuração estão definidas em runbooks, com um número de módulos Ansible disponíveis para realizar tarefas específicas. Para obter mais informações, consulte [funciona como Ansible](https://www.ansible.com/how-ansible-works).

Aprenda a:

- [Instalar e configurar Ansible no Linux para utilização com o Azure](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Criar uma VM básica](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Criar um ambiente de VM completado, incluindo a recursos de suporte](../articles/virtual-machines/linux/ansible-create-complete-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) é uma plataforma de automatização que o ajuda a definir como a sua infraestrutura está configurada, implementadas e geridas. Componentes adicionais incluídos Chef Habitat para automatização de ciclo de vida de aplicação em vez da infraestrutura e Chef InSpec que ajuda a automatizar a compatibilidade com requisitos de política e de segurança. Chef clientes são instalados em computadores de destino, com um ou mais central Chef servidores que armazenam e gerir as configurações. Para obter mais informações, consulte [uma descrição geral de Chef](https://docs.chef.io/chef_overview.html).

Aprenda a:

- [Implementar Chef automatizar no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Instalar Chef no Windows e criar as VMs do Azure](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) é uma plataforma de automatização preparada para empresa, que processa o processo de entrega e implementação de aplicação. Os agentes são instalados em computadores de destino para permitir que o mestre de Puppet executar manifestos que definem a configuração pretendida da infraestrutura do Azure e VMs. Puppet pode integrar com outras soluções como Jenkins e GitHub para um fluxo de trabalho do devops melhorada. Para obter mais informações, consulte [funciona como Puppet](https://puppet.com/product/how-puppet-works).

Aprenda a:

- [Implementar Puppet no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2016-1?tab=Overview).


## <a name="cloud-init"></a>init de nuvem
[Nuvem init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux como efetua o arranque pela primeira vez. Pode utilizar a cloud init para instalar pacotes e escrever em ficheiros, ou para configurar utilizadores e de segurança. Como é executado na nuvem init durante o processo de arranque inicial, existem não existem passos adicionais ou agentes necessários para aplicar a configuração.

Nuvem init também funciona em distribuições. Por exemplo, não utilize **apt get instalação** ou **yum instalar** para instalar um pacote. Em vez disso, é possível definir uma lista dos pacotes para instalar. Nuvem init utiliza automaticamente a ferramenta de gestão do pacote nativo para distro que selecionar.

Estamos a trabalhar com os nossos parceiros de obter init cloud incluídos e a funcionar nas imagens que fornecem ao Azure. A tabela seguinte descreve a disponibilidade de nuvem init atual nas imagens de plataforma do Azure:

| Alias | Publicador | Oferta | SKU | Versão |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canónico |UbuntuServer |14.04.5-LTS |mais recente |
| UbuntuLTS |Canónico |UbuntuServer |16.04 LTS |mais recente |
| CoreOS |CoreOS |CoreOS |Estável |mais recente |

Aprenda a:

- [Personalizar uma VM com Linux com a nuvem init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[Configuração de estado pretendido ' (DSC) da PowerShell](https://msdn.microsoft.com/en-us/powershell/dsc/overview) é uma plataforma de gestão para definir a configuração das máquinas de destino. Também pode ser utilizado o DSC no Linux através do [servidor Open Management Infrastructure (OMI)](https://collaboration.opengroup.org/omi/).

Configurações de DSC definem o que instalar num computador e como configurar o anfitrião. Um motor de Gestor de configuração Local (MMC) é executado em cada nó de destino que processa o pedido ações com base nas configurações premidas. Um servidor de solicitação é um serviço web que é executada num anfitrião central para armazenar as configurações de DSC e os recursos associados. O servidor de solicitação comunica com o motor de MMC em cada anfitrião de destino para fornecer as configurações necessárias e relatórios sobre a conformidade.

Aprenda a:

- [Criar uma configuração de DSC básica](https://msdn.microsoft.com/powershell/dsc/quickstart).
- [Configurar um servidor de solicitação do DSC](https://msdn.microsoft.com/powershell/dsc/pullserver).
- [Utilizar o DSC de Linux](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Extensão de Script personalizado do Azure
A extensão de Script personalizado do Azure para [Linux](../articles/virtual-machines/linux/extensions-customscript.md) ou [Windows](../articles/virtual-machines/windows/extensions-customscript.md) transfere e executa os scripts em VMs do Azure. Pode utilizar a extensão quando criar uma VM ou a qualquer altura depois da VM está a ser utilizado. 

Scripts podem ser transferidos do armazenamento do Azure ou de qualquer localização pública, como um repositório do GitHub. Com a extensão de Script personalizado, pode escrever scripts em qualquer idioma que é executado a VM de origem. Estes scripts podem ser utilizados para instalar aplicações ou configurar a VM conforme pretendido. Para proteger as credenciais, as informações confidenciais, tais como palavras-passe podem ser armazenadas numa configuração protegida. Estas credenciais são desencriptadas apenas dentro da VM.

Aprenda a:

- [Criar uma VM com Linux com a CLI do Azure e utilizar a extensão de Script personalizado](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Criar uma VM do Windows com o Azure PowerShell e utilizar a extensão de Script personalizado](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) automatiza o processo de compilação quando criar uma imagem VM personalizada no Azure. Utilize Packer para definir o SO e executar scripts pós-configuração que personalizam a VM para as suas necessidades específicas. Depois de configurar, a VM, em seguida, é capturada como uma imagem de disco gerido. Packer automatiza o processo para criar a origem de VM, rede e recursos de armazenamento, executar scripts de configuração e, em seguida, criar a imagem VM.

Aprenda a:

- [Utilizar Packer para criar uma imagem de VM com Linux no Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Utilizar Packer para criar uma imagem de VM do Windows no Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) é uma ferramenta de automatização que permite-lhe definir e criar uma infraestrutura do Azure completa com um idioma de formato único modelo - o idioma de configuração de HashiCorp (HCL). Com Terraform, é possível definir modelos automatizar o processo de criação de rede, armazenamento e recursos da VM para uma solução de aplicação específica. Pode utilizar os modelos de Terraform existentes para outras plataformas com o Azure para garantir consistência e simplificar a implementação da infraestrutura sem ser necessário converter para um modelo Azure Resource Manager.

Aprenda a:

- [Instalar e configurar Terraform com o Azure](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Criar uma infraestrutura do Azure com Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Automatização do Azure
[A automatização do Azure](https://azure.microsoft.com/services/automation/) utiliza runbooks para processar um conjunto de tarefas em VMs de destino. A automatização do Azure é utilizada para gerir VMs existentes em vez de criar uma infraestrutura. A automatização do Azure pode ser executados através de Linux e VMs do Windows, bem como a máquinas virtuais ou físicos no local com um runbook worker híbrido. Os Runbooks podem ser armazenados num repositório de controlo de origem, como o GitHub. Estes runbooks, em seguida, pode executar manualmente ou com base numa agenda definida.

A automatização do Azure também fornece um serviço de configuração de estado pretendido (DSC) que permite-lhe criar as definições para a forma como deve ser configurado um determinado conjunto de VMs. DSC, em seguida, assegura que a configuração necessária é aplicada a VM permanece consistente. Automation DSC do Azure é executado em computadores Windows e Linux.

Aprenda a:

- [Criar um runbook de PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Utilize o trabalho de Runbook híbrida para gerir recursos no local](../articles/automation/automation-hybrid-runbook-worker.md).
- [Utilizar o DSC de automatização do Azure](../articles/automation/automation-dsc-getting-started.md).


## <a name="visual-studio-team-services"></a>Visual Studio Team Services
[Equipa de serviços](https://www.visualstudio.com/team-services/) é um conjunto de ferramentas que ajudam a partilha e controlar code, utilizar compilações automatizadas e criar uma integração contínua concluída e o pipeline de desenvolvimento (CI/CD). Serviços da equipa integra-se com o Visual Studio e outras editores para simplificar a utilização. Serviços da equipa podem também criar e configurar as VMs do Azure e, em seguida, implementar o código-los.

Aprenda a:

- [Criar um pipeline de integração contínua com serviços da equipa](../articles/virtual-machines/windows/tutorial-vsts-iis-cicd.md).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) é um servidor de integração contínua, que ajuda a implementar e testar aplicações e criar pipelines automatizadas para entrega de código. Existem centenas de plug-ins para expandir a plataforma principal do Jenkins e também pode integrar com muitos outros produtos e soluções através de webhooks. Manualmente, pode instalar Jenkins numa VM do Azure, executar Jenkins de dentro de um contentor de Docker ou utilizar uma imagem do Azure Marketplace pré-criadas.

Aprenda a:

- [Criar uma infraestrutura de desenvolvimento numa VM com Linux no Azure com Jenkins, GitHub e Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Passos seguintes
Existem muitas opções diferentes utilizar ferramentas de automatização de infraestrutura no Azure. Tem de ter a liberdade de utilizar a solução que melhor se adeque às suas necessidades e ambiente. Para começar a utilizar e experimente algumas das ferramentas incorporadas no Azure, consulte como automatizar a personalização de um [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) ou [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) VM.
