<a name="tellmevm"></a>

## O que são as máquinas virtuais
As Virtual Machines do Azure permitem-lhe criar e utilizar máquinas virtuais na nuvem. Fornecendo a chamada *Infraestrutura como Serviço (IaaS)*, a tecnologia de máquinas virtuais pode ser utilizada de vários modos. Alguns exemplos incluem:

* **Máquinas virtuais (VMs) de desenvolvimento e teste.** Os grupos de desenvolvimento utilizam frequentemente VMs porque oferecem uma forma rápida e fácil de criar um computador com as configurações específicas necessárias para codificar e testar uma aplicação. As Virtual Machines do Azure proporcionam um modo simples e económico de criar estas VMs, utilizá-las e eliminá-las quando já não forem necessárias.
* **Executar aplicações na nuvem.** Faz sentido em termos económicos executar algumas aplicações na nuvem pública. Um exemplo é uma aplicação que tenha grandes picos de procura. Embora possa equipar o seu próprio datacenter com hardware suficiente para aguentar picos de procura, esse hardware poderá estar subutilizado grande parte do tempo. Executar essa aplicação no Azure permite-lhe pagar VMs extras apenas quando precisar delas e encerrá-las quando não precisar. Ou, suponha que é uma start-up que precisa de recursos de computação a pedido rapidamente e sem compromissos. Mais uma vez, o Azure pode ser a escolha certa.
* **Expandir o datacenter para a nuvem pública.** Ao utilizar a Virtual Network do Azure, a sua organização pode criar uma rede virtual (VNET) que é uma extensão da sua própria rede no local e adicionar VMs a essa VNET. Esta abordagem permite a execução de aplicações como o [SharePoint](../articles/virtual-machines/virtual-machines-windows-sharepoint-farm.md), o [SQL Server](../articles/virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md) e outras numa VM do Azure. Esta abordagem poderá ser mais fácil de implementar ou menos dispendiosa do que executar as aplicações em VMs no seu próprio datacenter.   
* **Recuperação após desastre.** Em vez de pagar continuamente por um datacenter de cópias de segurança que raramente utiliza, a recuperação após desastre com base em IaaS permite-lhe pagar os recursos de computação de que necessita apenas quando precisar realmente deles.  Por exemplo, se o seu datacenter primário ficar inativo, pode criar VMs em execução no Azure para executar aplicações essenciais e encerrá-las quando já não forem necessárias.

Como qualquer máquina virtual, uma VM no Azure tem um sistema operativo, capacidades de armazenamento e de rede e pode executar uma grande variedade de aplicações. Pode utilizar uma imagem fornecida pelo Azure ou por um dos seus parceiros ou utilizar a sua. Os exemplos incluem várias versões, edições e configurações de:

* Servidores Linux como Suse, Ubuntu e CentOS
* Windows Server 
* SQL Server
* BizTalk Server 
* SharePoint Server

As máquinas virtuais utilizam discos rígidos virtuais (VHDs) para armazenar o respetivo sistema operativo (SO) e dados. Os VHDs também são utilizados para as imagens que pode escolher para instalar um SO. A figura seguinte mostra esse caso, bem como duas das ferramentas para criar e gerir as VMs.

<a name="fig_createvms"></a>
![vm_diagram](./media/virtual-machines-choose-me-content/diagram.png)

**Figura: As Virtual Machines do Azure fornecem a Infraestrutura como um Serviço.**

As VMs podem ser geridas com um portal baseado num browser, ferramentas de linha de comandos com suporte para processamento de scripts ou diretamente através da API REST. Os parceiros da Microsoft, como RightScale e ScaleXtreme, fornecem também serviços de gestão que dependem da API REST. 

Juntamente com o SO, as outras opções de configuração que tem com as VMs incluem:

* O tamanho, que determina os fatores, como quantos discos pode anexar e a capacidade de processamento. O Azure disponibiliza uma vasta variedade de tamanhos para suportar muitos tipos de utilizações. Para obter mais detalhes, veja [Tamanhos das Virtual Machines](../articles/virtual-machines/virtual-machines-linux-sizes.md).  
* A região do Azure onde será alojada a sua nova VM, como EUA, Europa ou Ásia. 
* As extensões das VMs, que proporcionam capacidades adicionais à máquina virtual, como executar software antivírus ou utilizar a funcionalidade Configuração de Estado Pretendido do Windows PowerShell.

Outros benefícios a ter em conta ao utilizar VMs:

**Pay as you go** – o Azure cobra um preço por hora com base no tamanho da VM e do sistema operativo. Para horas parciais, o Azure cobra apenas os minutos de utilização. O armazenamento tem um preço à parte e é cobrado separadamente. Para obter mais detalhes, veja [Preços das Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/).

**Resiliência** – o Azure monitoriza o hardware físico que aloja cada VM em execução. Se um servidor físico a executar uma VM falhar, o Azure deteta essa falha, muda a VM para novo hardware e reinicia a VM. Este processo é por vezes denominado autorrecuperação do serviço. O Azure protege também os dados de uma máquina virtual, mantendo cópias redundantes dos VHDs no armazenamento de blobs. 

<!--HONumber=Sep16_HO3-->


