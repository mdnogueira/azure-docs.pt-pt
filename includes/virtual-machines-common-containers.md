As soluções em cloud do Azure são criadas com base em máquinas virtuais (emulação de hardware de computadores físicos), que permitem o ágil empacotamento de implementações e uma melhor consolidação de recursos do que o hardware físico. Os contentores e o ecossistema do [Docker](https://www.docker.com) expandiram, significativamente, as formas como pode desenvolver, enviar e gerir software distribuído. O código das aplicações num contentor é isolado da VM anfitrião e de outros contentores na mesma VM. Este isolamento proporciona-lhe mais agilidade em termos de desenvolvimento e implementação.

O Azure oferece os valores de Docker seguintes:

* [Várias](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) formas [distintas](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) de criar anfitriões do Docker para contentores, de acordo com o seu caso
* O [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/) utiliza orquestradores, como o **marathon** e o **swarm** para criar clusters de anfitriões de contentores
* O [Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md) e os [modelos de grupos de recursos](../articles/resource-group-authoring-templates.md), para simplificar a implementação e a atualização de aplicações distribuídas complexas
* Integração num vasto conjunto de ferramentas de gestão de configuração, proprietárias e de código aberto

E visto que pode criar programaticamente VMs e contentores de Linux no Azure, também pode utilizar ferramentas de *orquestração* de VMs e de contentores para criar grupos de Máquinas Virtuais (VMs) e implementar aplicações dentro de contentores de Linux e, agora, também de [Contentores do Windows](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

Este artigo não só aborda estes conceitos pormenorizadamente, como também contém inúmeras ligações para mais informações, tutoriais e produtos relacionados com a utilização de contentores e clusters no Azure. Se já sabe tudo isto e só quiser as ligações, estão mesmo aqui, em [ferramentas para trabalhar com contentores](#tools-for-working-with-azure-vms-and-containers).

## <a name="the-difference-between-virtual-machines-and-containers"></a>A diferença entre máquinas virtuais e contentores
As máquinas virtuais são executadas dentro de um ambiente de virtualização de hardware isolado, fornecido por um [hipervisor](http://en.wikipedia.org/wiki/Hypervisor). No Azure, o serviço de [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/) trata disto tudo por si. A si cabe-lhe criar Máquinas Virtuais, escolhendo o sistema operativo e configurando-o para ser executado&mdash; ou carregando uma imagem de VM personalizada. As Máquinas Virtuais são uma tecnologia sujeita a teste de tempo e solidificada pela experiência e estão disponíveis muitas ferramentas para gerir o SO e aplicações que contêm.  As aplicações numa VM estão ocultas do anfitrião do SO. Do ponto de vista de uma aplicação ou utilizador numa VM, a VM parece ser um computador físico autónomo.

Os [contentores Linux ](http://en.wikipedia.org/wiki/LXC) e os que foram criados e alojados com ferramentas de docker não utilizam um hipervisor para fornecer isolamento. Com contentores, o anfitrião do contentor utiliza as funcionalidades de isolamento de processos e do sistema de ficheiros do kernel do Linux para expor as respetivas aplicações aos contentores, apenas determinadas funcionalidades do kernel e o seu próprio sistema de ficheiros isolado. Do ponto de vista de uma aplicação em execução num contentor, este parece ser uma instância única do SO. As aplicações contidas não conseguem ver processos nem outros recursos que estejam fora do contentor.

São utilizados muito menos recursos num contentor Docker do que são utilizados numa VM. Os contentores Docker empregam um modelo de execução e isolamento de aplicações que não partilham o kernel do anfitrião Docker. O contentor tem muitos menos requisitos de espaço de disco, uma vez que não inclui o SO completo. O tempo de arranque e o espaço em disco necessários são significativamente inferiores do que numa VM.
Os Contentores do Windows oferecem as mesmas vantagens para aplicações que são executadas no Windows do que os contentores de Linux. Os Contentores do Windows suportam o formato de imagem do Docker e a Docker API, mas também podem ser geridos com o PowerShell. Estão disponíveis dois runtimes de contentor nos Contentores do Windows, nos Contentores do Windows Server e nos Contentores de Hyper-V. Os contentores de Hyper-V alojam cada contentor numa VM superotimizada, proporcionando uma camada de isolamento adicional. Para saber mais sobre os Contentores do Windows, veja [About Windows Containers (Acerca dos Contentores do Windows)](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview). Para começar a utilizar os Contentores do Windows no Azure, saiba como [Implementar um cluster do Azure Container Service](../articles/container-service/dcos-swarm/container-service-deployment.md).

## <a name="what-are-containers-good-for"></a>Para que servem os contentores?

Os contentores podem melhorar:

* A rapidez com que os códigos de aplicações podem ser programados e partilhados amplamente
* A confiança e a velocidade com que uma aplicação pode ser testada
* A confiança e a velocidade com que uma aplicação pode ser implementada

Os contentores são executados num anfitrião de contentor&mdash; um sistema operativo e, no Azure, isto significa uma Máquina Virtual do Azure. Mesmo que já esteja fascinado com a ideia de utilizar contentores, continua a precisar de uma infraestrutura de VM que os aloje, embora a vantagem é que, para os contentores, não é importante a VM onde estão a ser executados (apesar de ser importante, por exemplo, se querem um ambiente de execução do Linux ou do Windows).


## <a name="what-are-containers-good-for"></a>Para que servem os contentores?
São ótimos para muitas coisas, mas promovem&mdash; tal como os [Serviços Cloud do Azure](https://azure.microsoft.com/services/cloud-services/) e o [Azure Service Fabric](../articles/service-fabric/service-fabric-overview.md)&mdash; a criação de aplicações distribuídas de serviço único e orientadas para microsserviços, em que o design das aplicações se baseia em partes mais pequenas e compostas em vez de em componentes maiores e mais interligados.

Isto é especialmente verdade para os ambientes de cloud pública, como o Azure, nos quais as VMs são alugadas quando e onde as quer. Não só obtém o isolamento, a rápida implementação e as ferramentas de orquestração, como também pode tomar decisões relativas à infraestrutura das aplicações mais eficientes.

Por exemplo, pode ter, atualmente, uma implementação que consista em nove VMs do Azure de tamanho grande para uma aplicação distribuída de elevada disponibilidade. Se for possível implementar os componentes desta aplicação em contentores, poderá conseguir utilizar apenas quatro VMs e implementar os componentes da mesma dentro de 20 contentores, para redundância e balanceamento de carga.

Este é apenas um exemplo, claro, mas se puder fazer isto no seu cenário, poderá ajustar-se a picos de utilização com mais contentores em vez de mais VMs do Azure e utilizar a carga de CPU geral restante de forma muito mais eficiente do que antes.

Além disso, existem muitos cenários em que a abordagem de microsserviços não é a mais indicada; cabe-lhe a si determinar se os microsserviços e os contentores o ajudam.

### <a name="container-benefits-for-developers"></a>Vantagens dos contentores para os programadores
De um modo geral, é fácil perceber que a tecnologia de contentores é um passo em frente, mas existem outras vantagens mais concretas. Peguemos no exemplo dos contentores do Docker. Este tópico não vai descrever em pormenor o Docker por enquanto (para isso, leia [What is Docker? [O que é o Docker?]](https://www.docker.com/whatisdocker/) ou veja na [wikipedia](http://wikipedia.org/wiki/Docker_%28software%29)), mas o Docker e o respetivo ecossistema oferecem enormes benefícios, tanto a programadores, como a profissionais de TI.

Os programadores mudam para os contentores do Docker rapidamente, porque, acima de tudo, o Docker faz com que seja fácil utilizar o Linux e o Windows:

* Podem utilizar comandos incrementais simples para criar uma imagem fixa e de fácil implementação e podem automatizar a criação dessas imagens com um dockerfile
* Podem partilhar essas imagens facilmente com comandos push e pull simples e de estilo [git](https://git-scm.com/) para [registos de docker públicos](https://registry.hub.docker.com/) ou [privados](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Podem pensar em componentes de aplicação isolados, em vez de computadores
* Podem utilizar um grande número de ferramentas que compreendem os contentores do Docker e as diferentes imagens de base

### <a name="container-benefits-for-operations-and-it-professionals"></a>Vantagens dos contentores para profissionais de operações e de TI
Os profissionais de operações e de TI também podem tirar partido da combinação de contentores e máquinas virtuais.

* Os serviços contidos estão isolados do ambiente de execução do anfitrião de VM
* O código contido é verificavelmente idêntico
* Os serviços contidos podem ser iniciados, parados e movidos rapidamente entre ambientes de desenvolvimento, teste e produção.

Este tipo de funcionalidades&mdash; e ainda existem outras&mdash; são entusiasmantes para as empresas já estabelecidas, em que as organizações profissionais de Tecnologias de Informação têm a tarefa de ajustar recursos&mdash; incluindo o poder de processamento puro&mdash; às tarefas necessárias não apenas para prosseguir a atividade, mas também para aumentar a satisfação do cliente e o alcance. Pequenas empresas, ISVs e startups têm exatamente as mesmas necessidades, mas podem descrevê-las de forma diferente.

## <a name="what-are-virtual-machines-good-for"></a>Para que servem as máquinas virtuais?
As máquinas virtuais são o alicerce da computação na cloud e isto é algo que não vai mudar. Mesmo que comecem mais devagar, tenham uma pegada de disco maior e não mapeiem diretamente para uma arquitetura de microsserviços, têm vantagens muito importantes:

1. Por predefinição, têm proteções de segurança predefinidas muito mais robustas para o computador anfitrião
2. Suportam configurações dos principais SO e aplicações
3. Têm ecossistemas de ferramentas muito mais duradouros para comando e controlo
4. Fornecem o ambiente de execução aos contentores anfitriões

O último aspeto é importante porque as aplicações contidas continuam a precisar de um sistema operativo específico e de um tipo de CPU, dependendo das chamadas que as aplicações vão fazer. É importante não esquecer que os contentores são instalados em VMs porque contêm as aplicações que quer implementar; os contentores não são substitutos das VMs nem dos sistemas operativos.

## <a name="high-level-feature-comparison-of-vms-and-containers"></a>Comparação pormenorizada de funcionalidades entre VMs e contentores
A tabela seguinte descreve de forma pormenorizada as diferenças nas funcionalidades que&mdash; sem grande esforço extra&mdash; existem entre as VMs e os contentores do Linux. Tenha em conta que algumas funcionalidades podem ser mais ou menos desejáveis, dependendo das necessidades da sua aplicação, e que, tal como sucede com qualquer software, o esforço extra proporciona um maior suporte de funcionalidades, especialmente no campo da segurança.

| Funcionalidade | VMs | Contentores |
|:--- | --- | --- |
| Suporte para segurança “predefinido” |a um nível maior |a um nível ligeiramente inferior |
| Memória no disco necessária |SO completo e aplicações |Requisitos de aplicações apenas |
| Tempo necessário para o arranque |Substancialmente superior: arranque do SO e carregamento das aplicações |Substancialmente inferior: só as aplicações têm de ser iniciadas, porque o kernel já está em execução |
| Portabilidade |Portátil com preparação adequada |Portátil dentro do formato da imagem; tipicamente mais pequena |
| Automatização de Imagem |Varia bastante, dependendo do SO e das aplicações |[Registo Docker](https://registry.hub.docker.com/); outras |

## <a name="creating-and-managing-groups-of-vms-and-containers"></a>Criar e gerir grupos de VMs e contentores
Nesta fase, os arquitetos, programadores ou especialistas em operações de TI devem estar a pensar: “Posso automatizar ISTO tudo; É mesmo Datacenter como Serviço!”.

Têm toda a razão, pode mesmo ser, e existem vários sistemas, que talvez até já estejam a utilizar, que podem gerir grupos de VMs do Azure e injetar código personalizado através de script, muitas com [CustomScriptingExtension para Windows](https://msdn.microsoft.com/library/azure/dn781373.aspx) ou [CustomScriptingExtension para Linux](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/). Pode automatizar&mdash; e se calhar até já o fez&mdash; as suas implementações do Azure com o PowerShell ou os scripts da CLI do Azure.

Muitas vezes, estas capacidades são, depois, migradas para ferramentas como o [Puppet](https://puppetlabs.com/) e o [Chef](https://www.chef.io/), para automatizar a criação e a configuração de VMs em escala. (Seguem-se algumas ligações para [utilizar estas ferramentas com o Azure](#tools-for-working-with-containers).)

### <a name="azure-resource-group-templates"></a>Modelos dos grupos de recursos do Azure
Mais recentemente, o Azure lançou a API REST [Gestão de recursos do Azure](../articles/resource-manager-deployment-model.md) e atualizou as ferramentas do PowerShell e da CLI do Azure para a utilizarem facilmente. Pode utilizar os [modelos do Azure Resource Manager](../articles/resource-group-authoring-templates.md) com a API de gestão de recursos do Azure para implementar, modificar ou reimplementar topologias de aplicação inteiras através:

* do [portal do Azure com modelos](https://github.com/Azure/azure-quickstart-templates)&mdash; sugestão: utilize o botão “DeployToAzure”
* da [CLI do Azure](../articles/virtual-machines/linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* dos [módulos do Azure PowerShell](../articles/virtual-machines/linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="deployment-and-management-of-entire-groups-of-azure-vms-and-containers"></a>Implementação e gestão de grupos inteiros de VMs do Azure e contentores
Existem vários sistemas populares que podem implementar grupos inteiros de VMs e instalar o Docker (ou outros sistemas anfitriões de contentores de Linux) nas mesmas como um grupo automatizável. Para obter ligações diretas, veja a secção [contentores e ferramentas](#containers-and-vm-technologies), abaixo. São muitos os sistemas que fazem este tipo de implementação, a um nível maior ou mais pequeno, e esta lista não é exaustiva. Consoante as suas competências e os seus cenários, podem ser úteis ou não.

O Docker tem um conjunto de ferramentas de criação de VMs próprio ([docker-machine](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) e uma ferramenta de gestão de clusters de contentores do Docker de balanceamento de carga ([swarm](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Além disso, a [Extensão Azure Docker VM](https://github.com/Azure/azure-docker-extension/blob/master/README.md) inclui suporte predefinido para [`docker-compose`](https://docs.docker.com/compose/), que pode implementar contentores de aplicações configurados em vários contentores.

Além disso, pode experimentar o [Data Center Operating System (DCOS) da Mesosphere](http://docs.mesosphere.com/install/azurecluster/). O DCOS baseia-se no “kernel de sistemas distribuídos” [mesos](http://mesos.apache.org/) de código aberto que lhe permite tratar o seu datacenter como se fosse um serviço endereçável. Tem pacotes incorporados para vários sistemas importantes, como o [Spark](http://spark.apache.org/) e o [Kafka](http://kafka.apache.org/) (entre outros), bem como serviços incorporados, como o [Marathon](https://mesosphere.github.io/marathon/) (um sistema de controlo de contentores) e o [Chronos](https://mesos.github.io/chronos/) (um agendador distribuído). O Mesos foi obtido com base em lições aprendidas na Twitter, na AirBnB e noutras empresas à escala da Web. Também pode utilizar o **swarm** como o motor de orquestração.

Além disso, o [kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/) é um sistema de código aberto para gestão de VMs e grupos de contentores, obtido a partir de lições aprendidas na Google. Pode, inclusivamente, utilizar o [kubernetes com o weave para proporcionar suporte de rede](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave).

O [Deis](http://deis.io/overview/) é um “Serviço como Plataforma” (PaaS) de código aberto que facilita a implementação e a gestão de aplicações nos seus próprios servidores. O Deis funciona com base no Docker e o CoreOS para fornecer um PaaS leve com um fluxo de trabalho inspirado no HeroKu. Pode [criar facilmente um grupo de VMs do Azure de três nós e instalar o Deis](../articles/virtual-machines/linux/deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) no Azure e, em seguida, [instalar uma aplicação Hello World Go](../articles/virtual-machines/linux/deis-cluster.md#deploy-and-scale-a-hello-world-application).

O [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html), uma distribuição de Linux com uma pegada otimizada, suporte para Docker e o seu próprio sistema de contentores chamado [rkt](https://github.com/coreos/rkt), também tem uma ferramenta de gestão de grupos de contentores, denominada [fleet](https://coreos.com/using-coreos/clustering/).

O Ubuntu, outra distribuição de Linux muito popular, suporta o Docker sem problemas, mas também suporta [clusters do Linux (estilo LXC)](https://help.ubuntu.com/lts/serverguide/lxc.html).

## <a name="tools-for-working-with-azure-vms-and-containers"></a>Ferramentas para trabalhar com VMs do Azure e contentores
Trabalhar com contentores e VMs do Azure envolve ferramentas. Esta secção disponibiliza uma lista que inclui apenas alguns dos conceitos e ferramentas mais úteis ou importantes sobre contentores e grupos e as ferramentas de configuração e orquestração mais amplas utilizadas com os mesmos.

> [!NOTE]
> Esta é uma área que está em constante evolução e, embora façamos o nosso melhor para manter este tópico e as ligações atualizados, talvez esta possa ser uma tarefa impossível. Faça pesquisas sobre assuntos interessantes para se manter atualizado!
>
>

### <a name="containers-and-vm-technologies"></a>Tecnologias de contentores e VMs
Algumas tecnologias de contentores de Linux:

* [Docker](https://www.docker.com)
* [LXC](https://linuxcontainers.org/)
* [CoreOS e rkt](https://github.com/coreos/rkt)
* [Open Container Project](http://opencontainers.org/)
* [RancherOS](http://rancher.com/rancher-os/)

Ligações para o Contentor do Windows:

* [Windows Containers](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Ligações para o Visual Studio Docker:

* [Visual Studio 2015 RC Tools for Docker - Pré-visualização](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Ferramentas do Docker:

* [Daemon do Docker](https://docs.docker.com/installation/#installation)
* Clientes de Docker
  * [Cliente de Docker do Windows no Chocolatey](https://chocolatey.org/packages/docker)
  * [Instruções de instalação do Docker](https://docs.docker.com/installation/#installation)

Docker no Microsoft Azure:

* [Docker VM Extension for Linux on Azure (Extensão Docker VM para Linux no Azure)](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure Docker VM Extension User Guide (Guia do Utilizador da Extensão Azure Docker VM)](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
* [Utilizar a Extensão Docker VM a partir da Interface de Linha de Comandos do Azure (CLI do Azure)](../articles/virtual-machines/linux/classic/cli-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Using the Docker VM Extension from the Azure portal (Utilizar a Extensão Docker VM a partir do portal do Azure)](../articles/virtual-machines/linux/classic/portal-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [How to use docker-machine on Azure (Como utilizar o docker-machine no Azure)](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [How to use docker with swarm on Azure (Como utilizar o docker com swarm no Azure)](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Get Started with Docker and Compose on Azure (Introdução ao Docker e ao Compose no Azure)](../articles/virtual-machines/linux/docker-compose-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Using an Azure resource group template to create a Docker host on Azure quickly (Utilizar um modelo de grupo de recursos do Azure para criar um anfitrião do Docker no Azure rapidamente)](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
* [O suporte incorporado para `compose`](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys) para aplicações contidas
* [Implementar um registo privado do Docker no Azure](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Distribuições do Linux e exemplos do Azure:

* [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)

Configuração, gestão de clusters e orquestração de contentores:

* [Fleet on CoreOS (Fleet no CoreOS)](https://coreos.com/using-coreos/clustering/)
* Deis

  * [Create a 3-Node Azure VM group, install Deis, and start a Hello World Go application (Criar um grupo de VMs do Azure de três nós, instalar o Deis e iniciar uma aplicação Hello World Go)](../articles/virtual-machines/linux/deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Utilizar o Kubernetes

  * [Complete guide to automated Kubernetes cluster deployment with CoreOS and Weave (Guia completo para implementação de clusters do Kubernetes automatizada com CoreOS e Weave)](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
  * [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Mesos](http://mesos.apache.org/)

  * [Data Center Operating System (DCOS) da Mesosphere](http://beta-docs.mesosphere.com/install/azurecluster/)
* [Jenkins](https://jenkins-ci.org/) e [Hudson](http://hudson-ci.org/)

  * [Blog: Jenkins Slave Plug-in for Azure (Blogue: Plug-in Jenkins Slave para o Azure)](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
  * [GitHub repo: Jenkins Storage Plug-in for Azure (Repositório do GitHub: Plug-in Jenkins Storage para o Azure)](https://github.com/jenkinsci/windows-azure-storage-plugin)
  * [Third Party: Hudson Slave Plug-in for Azure (Terceiros: Plug-in Hudson Slave para o Azure)](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
  * [Third Party: Hudson Storage Plug-in for Azure (Terceiros: Plug-in Hudson Storage para o Azure)](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [Automatização do Azure](https://azure.microsoft.com/services/automation/)

  * [Video: How to Use Azure Automation with Linux VMs (Vídeo: Como utilizar a Automatização do Azure com VMs do Linux)](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* PowerShell DSC para Linux

  * [Blog: How to do Powershell DSC for Linux (Blogue: Como utilizar o PowerShell DSC no Linux)](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
  * [GitHub: Docker Client DSC (GitHub: DSC de Cliente do Docker)](https://github.com/anweiss/DockerClientDSC)

## <a name="next-steps"></a>Passos seguintes
Veja [Docker](https://www.docker.com) e [Windows Containers (Contentores do Windows)](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microservice]: http://martinfowler.com/articles/microservices.html
<!--Image references-->
