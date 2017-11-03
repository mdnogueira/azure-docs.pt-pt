---
title: "Comparação do serviço de aplicações, as máquinas virtuais, Service Fabric e serviços em nuvem do Azure | Microsoft Docs"
description: "Saiba como escolher entre o App Service do Azure, as máquinas virtuais, Service Fabric e serviços em nuvem para alojar aplicações web."
services: app-service\web, virtual-machines, cloud-services
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: 7d346a23-532a-42a9-98a8-23b7286d32a8
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/07/2016
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: edd5099d2804fdb5867b4be5b11a361004db1665
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Comparação do serviço de aplicações, as máquinas virtuais, Service Fabric e serviços em nuvem do Azure
## <a name="overview"></a>Descrição geral
O Azure disponibiliza várias formas de anfitrião web sites: [App Service do Azure][Azure App Service], [máquinas virtuais][Virtual Machines], [Service Fabric][Service Fabric], e [serviços em nuvem][Cloud Services]. Este artigo ajuda-o a compreender as opções e efetue a escolha certa para a sua aplicação web.

App Service do Azure é a melhor opção para a maioria das aplicações web. Implementação e gestão estão integradas com a plataforma, sites podem rapidamente Dimensionar para processar cargas de tráfego elevado e o Gestor de tráfego e balanceamento de carga incorporadas fornecem elevada disponibilidade. Pode mover sites existentes para o Azure App Service facilmente com um [ferramenta de migração online](https://www.migratetoazure.net/), utiliza uma aplicação de open source da Galeria de aplicações Web, ou crie um novo site utilizando as ferramentas da sua preferência e framework. O [WebJobs] [ WebJobs] funcionalidade torna mais fácil adicionar o processamento da tarefa em segundo plano a sua aplicação web do app Service.

Service Fabric é uma boa opção se estiver a criar uma nova aplicação ou volte a escrever uma aplicação existente para utilizar uma arquitetura de microsserviço. As aplicações que são executadas num agrupamento partilhado de máquinas, podem começar por algo pequenas e aumentar de grande escala com centenas ou milhares de máquinas conforme necessário. Serviços com monitorização de estado facilitam a consistentemente e fiável armazenar o estado da aplicação e o Service Fabric gere automaticamente a criação de partições de serviço, dimensionamento e disponibilidade para si.  Serviço de recursos de infraestrutura também suporta end WebAPI com Open Web Interface para .NET (OWIN) e o ASP.NET Core.  Em comparação com serviço de aplicações, o Service Fabric também fornece mais controlo sobre ou acesso direto à infraestrutura subjacente. Pode remoto em servidores ou configurar tarefas de arranque do servidor. Serviços cloud é semelhante ao Service Fabric no grau de controlo versus facilidade de utilização, mas agora é um serviço de legado e Service Fabric é recomendada para um novo desenvolvimento.

Se tiver uma aplicação existente que necessitem de alterações substanciais para execução no serviço de aplicações ou de Service Fabric, pode escolher máquinas virtuais para simplificar a migração para a nuvem. No entanto, corretamente configurar, proteger e VMs manutenção requer muito mais tempo e conhecimentos IT comparado com o App Service do Azure e o Service Fabric. Se estiver a considerar Virtual Machines do Azure, certifique-se de que ter em consideração o esforço manutenções correntes para aplicar o patch, atualizar e gerir o ambiente de VM. Máquinas virtuais do Azure é infraestrutura-como-um-serviço (IaaS), enquanto serviço de aplicações e recursos de infraestrutura de serviço são plataforma-como-um-serviço (Paas). 

## <a name="features"></a>Comparação de funcionalidades
A tabela seguinte compara as capacidades do serviço de aplicações, serviços em nuvem, máquinas virtuais e recursos de infraestrutura de serviço para o ajudar a tornar a melhor opção. Para obter informações atuais sobre o SLA para cada opção, consulte [contratos de nível de serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

| Funcionalidade | Serviço de aplicações (aplicações web) | Serviços cloud (funções da web) | Virtual Machines | Service Fabric | Notas |
| --- | --- | --- | --- | --- | --- |
| Implementação de perto instantâneas |X | | |X |Implementar uma aplicação ou uma atualização da aplicação para um serviço em nuvem ou criar uma VM, demora vários minutos, pelo menos, implementar uma aplicação numa aplicação web demora alguns segundos. |
| Aumentar verticalmente para máquinas maiores sem a implementar |X | | |X | |
| Instâncias de servidor Web partilham conteúdo e a configuração, o que significa que não tem de voltar a implementar ou reconfigurar, à medida que. |X | | |X | |
| Vários ambientes de implementação (produção e teste) |X |X | |X |Recursos de infraestrutura de serviço permite-lhe ter vários ambientes para as suas aplicações ou implementar versões diferentes da sua aplicação do lado do lado a lado. |
| Gestão de atualização automática do SO |X |X | | |Parcialmente através da orquestração Patch aplicação (POA) e totalmente no futuro. |
| Mudar de plataforma totalmente integrada (mover facilmente entre 32 bits e 64 bits) |X |X | | | |
| Implementar código com o GIT, FTP |X | |X | | |
| Implementar código com o Web Deploy |X | |X | |Serviços cloud suporta a utilização do Web Deploy para implementar atualizações para instâncias de função individuais. No entanto, não é possível utilizá-lo para a implementação inicial de uma função e, se utilizar o Web Deploy para uma atualização tem de implementar em separado para cada instância de uma função. Várias instâncias são necessárias para se qualificar para o SLA do serviço de nuvem para ambientes de produção. |
| Suporte do WebMatrix |X | |X | | |
| Acesso aos serviços do Service Bus, armazenamento, base de dados SQL |X |X |X |X | |
| Anfitrião web ou de camada de serviços web de uma arquitetura de várias camada |X |X |X |X | |
| Camada média do anfitrião de uma arquitetura de várias camada |X |X |X |X |Web apps do App Service podem facilmente alojar a camada média de REST API e o [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) funcionalidade pode alojar as tarefas de processamento em segundo plano. Pode executar WebJobs no Web site dedicado para alcançar a escalabilidade independente para a camada. |
| Suporte integrado MySQL-como-um-serviço |X |X |X | |Serviços cloud podem integrar MySQL-como-um-serviço através de ofertas da ClearDB, mas não como parte do fluxo de trabalho do Portal do Azure. |
| Suporte para clássico, ASP.NET, ASP, Node.js, PHP, Python |X |X |X |X |Serviço de recursos de infraestrutura suporta a criação de um web front-end utilizando [ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) ou pode implementar qualquer tipo de aplicação (Node.js, Java, etc.) como um [executável de convidado](../service-fabric/service-fabric-deploy-existing-app.md). |
| Aumentar horizontalmente a várias instâncias sem a implementar |X |X |X |X |Máquinas virtuais pode ampliar a várias instâncias, mas os serviços em execução nos mesmos têm de ser escritos para processar este Escalamento horizontal. Tem de configurar um balanceador de carga para encaminhar pedidos entre as máquinas e criar um grupo de afinidade para impedir que os reinícios simultâneos de todas as instâncias devido a falhas de manutenção ou do hardware. |
| Suporte para SSL |X |X |X |X |Para web apps do App Service, o SSL para nomes de domínio personalizados só é suportada para o modo de base e padrão. Para obter informações sobre como utilizar SSL com as web apps, consulte [configurar um certificado SSL para um Web site Azure](app-service-web-tutorial-custom-ssl.md). |
| Integração do Visual Studio |X |X |X |X | |
| Depuração remota |X |X |X | | |
| Implementar código com o TFS |X |X |X |X | |
| Rede isolamento com [Azure Virtual Network](/azure/virtual-network/) |X |X |X |X |Consulte também [integração de rede de Web sites virtuais do Azure](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) |
| Suporte para [Traffic Manager do Azure](/azure/traffic-manager/) |X |X |X |X | |
| Monitorização de pontos finais integrada |X |X |X | | |
| Acesso de ambiente de trabalho remoto para servidores | |X |X |X | |
| Instalar qualquer MSI personalizado | |X |X |X |Recursos de infraestrutura de serviço permite-lhe alojar qualquer ficheiro executável como uma [executável convidado](../service-fabric/service-fabric-deploy-existing-app.md) ou pode instalar qualquer aplicação nas VMs. |
| Capacidade de definir/executar tarefas de arranque | |X |X |X | |
| Pode escutar Eventos ETW | |X |X |X | |

## <a name="scenarios"></a>Cenários e recomendações
Seguem-se alguns cenários comuns de aplicação com base em recomendações relativamente a qual poderá mais adequada para cada opção de alojamento na web.

* [Preciso de um front-end de publicação na web ' com o backend do processamento e a base de dados em segundo plano para executar aplicações empresariais integradas com recursos no local.](#onprem)
* [Preciso de uma forma fiável para alojar o meu site empresarial que dimensiona bem e alcancem ofertas global.](#corp)
* [Tenho uma aplicação de IIS6 em execução no Windows Server 2003.](#iis6)
* [Sou um proprietário de pequenas empresas e preciso de uma forma económico para alojar o meu site, mas com o crescimento futuro em mente.](#smallbusiness)
* [Sou um web ou um designer gráfico e pretender conceber e criar web sites para os meus clientes.](#designer)
* [Estou a migrar a minha aplicação multicamada com uma web front-end para a nuvem.](#multitier)
* [A minha aplicação depende Windows altamente personalizada ou ambientes de Linux e posso pretende movê-la para a nuvem.](#custom)
* [O meu site utiliza o software de código aberto e pretender alojá-la no Azure.](#oss)
* [Tenho uma aplicação de linha de negócio que necessita para se ligar à rede empresarial.](#lob)
* [Pretende alojar uma REST API ou o serviço web de clientes móveis.](#mobile)

### <a id="onprem"></a>Preciso de um front-end de publicação na web ' com o backend do processamento e a base de dados em segundo plano para executar aplicações empresariais integradas com recursos no local.
App Service do Azure é uma excelente solução para aplicações complexas empresariais. Permite-lhe desenvolver aplicações que Dimensionar automaticamente numa plataforma com balanceamento de carga, são protegidas com o Active Directory e liguem aos seus recursos no local. Permite gerir essas aplicações fácil através de um trabalho portal e APIs e permite-lhe obter conhecimentos aprofundados sobre como os clientes estão a utilizá-los com ferramentas de conhecimentos aprofundados da aplicação. O [Webjobs] [ Webjobs] funcionalidade permite-lhe executar processos em segundo plano e fazer uma cópia de tarefas como parte da sua camada web, enquanto conectividade híbrida e funcionalidades VNET tornam mais fácil ligar aos recursos no local. App Service do Azure fornece três 9 SLA para aplicações web e permite-lhe:

* Execute as suas aplicações fiável numa plataforma de nuvem autorrecuperação, aplicação de patches automática.
* Dimensionar automaticamente através de uma rede global de datacenters.
* Cópia de segurança e restauro para recuperação após desastre.
* Estar ISO, SOC2 e PCI em conformidade.
* Integrar com o Active Directory

### <a id="corp"></a>Preciso de uma forma fiável para alojar o meu site empresarial que dimensiona bem e alcancem ofertas global.
App Service do Azure é uma excelente solução para o alojamento de Web sites empresariais. Permite que as aplicações web de dimensionamento de forma rápida e fácil para satisfazer o pedido através de uma rede global de datacenters. Oferece alcance local, a tolerância a falhas e a gestão de tráfego inteligente. Todos os numa plataforma que fornece ferramentas de gestão de trabalho, permitindo-lhe obter conhecimentos aprofundados sobre o estado de funcionamento do site e o tráfego do site forma rápida e fácil. App Service do Azure fornece três 9 SLA para aplicações web e permite-lhe:

* Execute os Web sites fiável numa plataforma de nuvem autorrecuperação, aplicação de patches automática.
* Dimensionar automaticamente através de uma rede global de datacenters.
* Cópia de segurança e restauro para recuperação após desastre.
* Gerir registos e o tráfego com ferramentas integradas.
* Estar ISO, SOC2 e PCI em conformidade.
* Integrar com o Active Directory

### <a id="iis6"></a>Tenho uma aplicação de IIS6 em execução no Windows Server 2003.
App Service do Azure torna mais fácil evitar os custos de infraestrutura associados migrar aplicações IIS6 mais antigas. Microsoft criou [orientações detalhadas de migração e ferramentas de migração fáceis de utilizar](https://www.movemetowebsites.net/) que lhe permite verificar a compatibilidade e identificar quaisquer alterações que precisam de ser efetuadas. Integração com o Visual Studio, TFS e as ferramentas de CMS comuns torna mais fácil de implementar aplicações de IIS6 diretamente para a nuvem. Depois de implementada, o Portal do Azure fornece ferramentas de gestão robusta que permitem-lhe reduzir verticalmente para gerir os custos e até cumprem exigem conforme necessário. Com a ferramenta de migração, pode:

* Rápida e facilmente migre a sua aplicação de web do Windows Server 2003 legada para a nuvem.
* Optar por deixar anexado SQL da base de dados no local para criar uma aplicação híbrida.
* Mover automaticamente a base de dados do SQL Server juntamente com a aplicação legada.

### <a id="smallbusiness"></a>Sou um proprietário de pequenas empresas e preciso de uma forma económico para alojar o meu site, mas com o crescimento futuro em mente.
App Service do Azure é uma excelente solução para este cenário, porque pode começar a utilizá-la gratuitamente e, em seguida, adicionar capacidades mais quando que precisar. Cada aplicação web livre é fornecido com um domínio fornecido pelo Azure (*your_company*. azurewebsites.net), e a plataforma inclui ferramentas de implementação e gestão integradas, bem como uma galeria de aplicações que tornam mais fácil começar a utilizar. Existem muitos outros serviços e as opções de dimensionamento que permitir que o site de evoluir com o pedido do utilizador de aumento. Com o App Service do Azure, pode:

* Começar com o escalão gratuito e, em seguida, aumentar verticalmente, conforme necessário.
* Utilize a Galeria de aplicações para configurar rapidamente a aplicações web populares, tais como o WordPress.
* Adicione funcionalidades e serviços do Azure adicionais à sua aplicação conforme necessário.
* Proteger a sua aplicação web com HTTPS.

### <a id="designer"></a>Sou um web ou um designer gráfico e pretender conceber e criar os Web sites para os meus clientes
Para programadores de web e designers, App Service do Azure integra-se facilmente com uma variedade de ferramentas e estruturas, inclui suporte de implementação de Git e FTP e oferece uma integração sólida com as ferramentas e serviços como o Visual Studio e a base de dados SQL. Com o Serviço de aplicações, pode:

* Utilize as ferramentas da linha de comandos para [tarefas automatizadas][scripting].
* Trabalhar com idiomas populares, tais como [.Net][dotnet], [PHP][PHP], [Node.js][nodejs], e [Python][Python].
* Selecione as três diferentes níveis de dimensionamento para como aumentar verticalmente as capacidades muito elevadas.
* Integrar com outros serviços do Azure, tal como [base de dados SQL][sqldatabase], [Service Bus] [ servicebus] e [armazenamento][Storage], ou o parceiro de ofertas do [arquivo Azure][azurestore], tais como o MySQL e MongoDB.
* Integre com ferramentas como o Visual Studio, Git, o WebMatrix, WebDeploy, TFS e FTP.

### <a id="multitier"></a>Estou a migrar a minha aplicação multicamada com uma web front-end para a nuvem
Se estiver a executar uma aplicação de várias camadas, como um servidor web que estabelece ligação à base de dados, o App Service do Azure é uma boa opção oferece uma integração sólida com a SQL Database do Azure. E pode utilizar a funcionalidade de WebJobs para processos de back-end em execução.

Escolha o Service Fabric para um ou mais dos seus escalões se precisar de mais controlam sobre o ambiente de servidor, tais como a capacidade de remoto para o servidor ou configurar tarefas de arranque do servidor.

Escolha as máquinas virtuais para um ou mais dos seus camadas se pretender utilizar a sua própria imagem de máquina ou execute o software de servidor ou serviços que não é possível configurar o Service Fabric.

### <a id="custom"></a>A minha aplicação depende Windows altamente personalizada ou ambientes de Linux e posso pretende movê-la para a nuvem.
Se a sua aplicação requer a instalação complexa ou de configuração de software e o sistema operativo, as máquinas virtuais é, provavelmente, a melhor solução. Máquinas virtuais, pode:

* Utilizar a galeria da Máquina Virtual para começar com um sistema operativo, tais como o Windows ou Linux e, em seguida, personalizá-lo para os requisitos da aplicação.
* Criar e carregar uma imagem personalizada de um servidor no local existentes para executar numa máquina virtual no Azure.

### <a id="oss"></a>O meu site utiliza o software de código aberto e pretender alojá-la no Azure
Se a sua arquitetura de código aberto é suportada no App Service, os idiomas e estruturas necessárias pela sua aplicação são automaticamente configuradas para si. Serviço de aplicações permite-lhe:

* Utilizar muitos abra origem idiomas populares, tais como [.NET][dotnet], [PHP][PHP], [Node.js][nodejs], e [Python][Python].
* Configure WordPress, Drupal, Umbraco, DNN e muitas outras aplicações web de terceiros.
* Migrar uma aplicação existente ou crie um novo a partir da Galeria de aplicações.

Se a sua arquitetura de código aberto não é suportada no App Service, pode executá-lo dos outro web do Azure que aloja as opções. Com máquinas virtuais, instalar e configurar o software à imagem de máquina, que pode ser o Windows ou baseado em Linux.

### <a id="lob"></a>Tenho uma aplicação de linha de negócio que necessita para se ligar à rede empresarial
Se pretender criar uma aplicação de linha de negócio, o seu Web site poderá requerem acesso direto aos serviços ou dados na rede empresarial. Isto é possível no serviço de aplicações, o Service Fabric e máquinas virtuais utilizando o [serviço de rede Virtual do Azure](/azure/virtual-network/). No App Service pode utilizar o [funcionalidade de integração de VNET](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), que permite que as aplicações do Azure executar como se estivessem na sua rede empresarial.

### <a id="mobile"></a>Pretender alojar uma REST API ou o serviço web de clientes móveis
Serviços web baseados em HTTP permitem-lhe suportar uma grande variedade de clientes, incluindo clientes móveis. Estruturas, como a API Web do ASP.NET integram com o Visual Studio para o tornar mais fácil de criar e consumir os serviços REST.  Estes serviços são expostos a partir de um ponto final da web, pelo que é possível utilizar qualquer técnica no Azure de alojamento na web para suportar este cenário. No entanto, o serviço de aplicações é uma escolha ideal para o alojamento de REST APIs. Com o Serviço de aplicações, pode:

* Criar rapidamente um [aplicação móvel](../app-service-mobile/app-service-mobile-value-prop.md) ou aplicação API para alojar o serviço web HTTP do Azure globalmente distribuído centros de dados.
* Migrar os serviços existentes ou criar novos.
* Alcançar o SLA de disponibilidade com uma única instância, ou aumentar horizontalmente para várias máquinas dedicadas.
* Utilize o site publicado para fornecer REST APIs para os clientes HTTP, incluindo clientes móveis.

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de inscrever-se para uma conta, aceda a <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, onde, pode criar imediatamente uma aplicação de arranque de curta duração no App Service do Azure gratuitamente. Sem cartões de crédito, sem compromissos.
> 
> 

## <a id="nextsteps"></a>Passos seguintes
Para obter mais informações sobre as três opções de alojamento de web, consulte [introdução ao Azure](../fundamentals-introduction-to-azure.md).

Para começar a utilizar com as opções que escolheu para a sua aplicação, consulte os seguintes recursos:

* [Serviço de Aplicações do Azure](/azure/app-service/)
* [Azure Cloud Services](/azure/cloud-services/)
* [Máquinas virtuais do Azure](/azure/virtual-machines/)
* [Service Fabric](/azure/service-fabric/)

<!-- URL List -->

[Azure App Service]: /azure/app-service/
[Cloud Services]: /azure/cloud-services/
[Virtual Machines]: /azure/virtual-machines/
[Service Fabric]: /azure/service-fabric/
[ClearDB]: http://www.cleardb.com/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: app-service-web-tutorial-custom-ssl.md
[azurestore]: https://azuremarketplace.microsoft.com/en-us/marketplace/apps
[scripting]: https://azure.microsoft.com/documentation/scripts/?services=web-sites
[dotnet]: https://azure.microsoft.com/develop/net/
[nodejs]: https://azure.microsoft.com/develop/nodejs/
[PHP]: https://azure.microsoft.com/develop/php/
[Python]: https://azure.microsoft.com/develop/python/
[servicebus]: /azure/service-bus/
[sqldatabase]: /azure/sql-database/
[Storage]: /azure/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
