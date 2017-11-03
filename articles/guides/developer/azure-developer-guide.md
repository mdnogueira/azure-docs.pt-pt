---
title: "Get guia de introdução para os programadores no Azure | Microsoft Docs"
description: "Este tópico fornece informações essenciais para os programadores de procura para começar a utilizar a plataforma Microsoft Azure para as suas necessidades de desenvolvimento."
services: 
cloud: 
documentationcenter: 
author: ggailey777
manager: erikre
ms.assetid: 
ms.service: na
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: 2de8349c9e727dca48ab8ca3e04851a5ee57d30b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="get-started-guide-for-azure-developers"></a>Guia de introdução para programadores do Azure

## <a name="what-is-azure"></a>O que é o Azure?

Azure é uma plataforma de nuvem concluída que pode alojar as suas aplicações existentes, simplificar o desenvolvimento de novas aplicações e até mesmo melhorar as aplicações no local. Azure integra-se os serviços em nuvem que precisa para desenvolver, testar, implementar e gerir as suas aplicações — ao tirar partido da resulta na eficiência da nuvem de computação.

Ao alojar as aplicações no Azure, pode começar por algo pequeno e dimensionar facilmente a sua aplicação à medida que aumenta a procura dos clientes. Azure também oferece a fiabilidade necessários para elevada disponibilidade aplicações, incluindo mesmo ativação pós-falha entre regiões diferentes. O [portal do Azure](https://portal.azure.com) permite-lhe gerir facilmente todos os seus serviços do Azure. Também pode gerir os serviços através de programação utilizando APIs e modelos específicos do serviço.

**Quem deve ler este artigo**: neste guia é uma introdução à plataforma do Azure para os programadores de aplicações. Fornece orientações e direção que terá de iniciar a criação de novas aplicações no Azure ou migrar as aplicações existentes para o Azure.

## <a name="where-do-i-start"></a>Por onde devo começar?

Com todos os serviços de que o Azure oferece, pode ser tarefas hercúleas para descobrir os serviços necessários para suportar a arquitetura da solução. Esta secção destaca os serviços do Azure que os programadores normalmente a utilizar. Para obter uma lista de todos os serviços do Azure, consulte o [documentação do Azure](../../index.md).

Em primeiro lugar, deve decidir como alojar a sua aplicação no Azure. Precisa de gerir a sua infraestrutura completa como uma máquina virtual (VM). Pode utilizar as instalações de gestão de plataforma que o Azure oferece? Talvez precisa de uma arquitetura sem servidor para apenas a execução do código anfitrião?

A aplicação necessita de armazenamento na nuvem, que o Azure oferece várias opções para. Pode tirar partido da autenticação do Azure. Também existem ferramentas de desenvolvimento baseado na nuvem e de monitorização e a maioria dos serviços de alojamento oferecem integração de DevOps.

Agora, vamos ver alguns dos serviços específicos que recomendamos para as suas aplicações a investigar.

### <a name="application-hosting"></a>Alojamento de aplicações

O Azure oferece que vários baseado na nuvem ofertas para executar a sua aplicação para que não têm de se preocupar com os detalhes de infraestrutura de computação. Pode facilmente aumentar verticalmente ou ampliar os recursos, à medida que aumenta a utilização da aplicação.

O Azure disponibiliza serviços que suportam o desenvolvimento de aplicações e necessidades de alojamento. O Azure oferece infraestrutura-como-um-serviço (IaaS) para lhe dar controlo total sobre o alojamento de aplicações. Ofertas do Azure plataforma-como-um-serviço (PaaS) fornecerem os serviços completamente geridos necessários para ligar as suas aplicações. Não há mesmo verdadeiro sem servidor de alojamento no Azure em que é tudo o que precisa para fazer a escrever o seu código.

![Opções de alojamento de aplicação do Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Serviço de Aplicações do Azure 

Se pretender que o caminho mais rápido para publicar os seus projetos baseada na web, considere o App Service do Azure. Serviço de aplicações torna mais fácil expandir as suas aplicações web para suportar os seus clientes móveis e publicar APIs REST do facilmente foram consumidos. Esta plataforma fornece autenticação utilizando fornecedores de rede sociais, com base no tráfego de dimensionamento automático, teste em produção e implementações contínuas e baseada no contentor.

Pode criar aplicações web, back-ends de aplicações móveis e API apps.

Como todos os três tipos de aplicações partilham o tempo de execução do serviço de aplicações, pode alojar um Web site, suportar clientes móveis e expõe as suas APIs no Azure, tudo a partir do mesmo projecto ou solução. Para saber mais sobre o App Service, consulte [Novidades Web Apps do Azure](../../app-service/app-service-web-overview.md).

Serviço de aplicações tem foram concebido com DevOps em mente. Suporta várias ferramentas para implementações de integração contínua e publicação, incluindo GitHub webhooks, Jenkins, Visual Studio Team Services, TeamCity e outras pessoas.

Pode migrar as suas aplicações existentes para o App Service utilizando a [ferramenta de migração online](https://www.migratetoazure.net/).

>**Quando utilizar**: utilizar o App Service ao qual está a migrar existente web aplicações para o Azure e, quando precisar de uma plataforma de alojamento completamente gerida para as suas aplicações web. Também pode utilizar o serviço de aplicações quando é necessário suportar clientes móveis ou expõe as APIs REST com a sua aplicação.

>**Introdução ao**: serviço de aplicações torna mais fácil criar e implementar o primeiro [aplicação web](../../app-service/app-service-web-get-started-dotnet.md), [aplicação móvel](../../app-service-mobile/app-service-mobile-ios-get-started.md), ou [aplicação API](../../app-service/app-service-web-tutorial-rest-api.md).

>**Experimente agora**: App Service permite-lhe aprovisionar uma aplicação de curta duração para experimentar a plataforma sem ter de inscrever-se numa conta do Azure. Tente a plataforma e [criar a sua aplicação do App Service do Azure](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

Como a fornecedor uma infraestrutura-como-um-serviço (IaaS), o Azure permite-lhe implementar ou migrar a sua aplicação para Windows ou Linux VMs. Em conjunto com a Azure Virtual Network, Virtual Machines do Azure suporta a implementação do Windows ou Linux VMs no Azure. Com as VMs, tem controlo total sobre a configuração da máquina. Ao utilizar VMs, está responsável por todos os servidor software instalação, configuração, manutenção e sistema operativo patches.

Devido ao nível do controlo que tem com as VMs, pode executar uma grande variedade de cargas de trabalho do servidor no Azure que não se ajustem para um modelo PaaS. Estas cargas de trabalho incluem os servidores de base de dados, o Windows Server Active Directory e o Microsoft SharePoint. Para obter mais informações, consulte a documentação de máquinas virtuais para um [Linux](/azure/virtual-machines/linux/) ou [Windows](/azure/virtual-machines/windows/).

>**Quando utilizar**: utilizar máquinas virtuais quando quiser completa controlar através da sua infraestrutura de aplicação ou migrar a cargas de trabalho de aplicações no local para Azure sem ter de efetuar alterações.

>**Introdução ao**: criar um [VM com Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) ou [VM do Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) do portal do Azure.

#### <a name="azure-functions-serverless"></a>Funções do Azure (sem servidor)

Em vez de preocupar com a criação de saída e a gerir toda a aplicação ou a infraestrutura para executar o código. E se foi apenas escrever código e pô-lo a executar em resposta a eventos ou com base numa agenda?  [As funções do Azure](../../azure-functions/functions-overview.md) é uma "sem servidor"-oferta de estilo que permita escrever apenas o código é necessário. Com as funções, a execução do código é acionada por pedidos de HTTP, webhooks, eventos do serviço em nuvem ou com base numa agenda. Pode código no seu idioma de desenvolvimento de escolha, tal como C\#, F\#, Node.js, Python ou PHP. Com faturação baseada no consumo, paga apenas o momento em que executa o código e Azure dimensiona conforme necessário.

>**Quando utilizar**: utilizar as funções do Azure quando tiver o código que é acionado por outros serviços do Azure, por eventos baseada na web, ou com base numa agenda. Também pode utilizar funções quando não precisa da sobrecarga de um projeto alojado concluído ou quando pretender apenas paga a hora em que o seu código é executado. Para obter mais informações, consulte [descrição geral das funções do Azure](../../azure-functions/functions-overview.md).

>**Introdução ao**: Siga o tutorial de início rápido de funções para [criar a sua primeira função](../../azure-functions/functions-create-first-azure-function.md) do portal.

>**Experimente agora**: das funções do Azure permite-lhe executar o código sem ter de inscrever-se numa conta do Azure. Experimente agora em e [criar a sua primeira função do Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita a criar, o pacote, implementar e gerir micro-serviços escaláveis e fiáveis. Também fornece capacidades de gestão de aplicações abrangente para aprovisionamento, implementação, monitorização, aplicação de patches e atualizar e eliminar aplicações implementadas. As aplicações que são executadas num agrupamento partilhado de máquinas, podem começar por algo pequenas e dimensionar centenas ou milhares de máquinas conforme necessário.

Service Fabric suporta end WebAPI com Open Web Interface para .NET (OWIN) e o ASP.NET Core. Fornece SDKs de criação de serviços no Linux em .NET Core e Java. Para obter mais informações sobre recursos de infraestrutura de serviço, consulte o [percurso de aprendizagem do Service Fabric](https://azure.microsoft.com/documentation/learning-paths/service-fabric/).

>**Quando utilizar:** Service Fabric é uma boa opção quando estiver a criar uma aplicação ou conversão de uma aplicação existente para utilizar uma arquitetura de microsserviço. Utilize o Service Fabric quando precisar de mais controlo sobre ou acesso direto à infraestrutura subjacente.

>**Introdução:** [criar a sua primeira aplicação do Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Melhorar as suas aplicações com serviços do Azure

Para além de alojar a aplicação, o Azure oferece ofertas de serviço que podem melhorar a funcionalidade, desenvolvimento e manutenção das suas aplicações, tanto na nuvem e no local.

#### <a name="hosted-storage-and-data-access"></a>Acesso de dados e de armazenamento alojado

A maioria das aplicações tem de armazenar dados, por isso, independentemente de como decidir alojar a aplicação no Azure, considere uma ou mais dos seguintes serviços de dados e armazenamento.

-   **BD do Azure do Cosmos**: um serviço de base de dados globalmente distribuída e múltiplos modelo permite-lhe dimensionar o débito e armazenamento em qualquer número de regiões geográficas com um SLA abrangente. 
    >**Quando utilizar:** quando a aplicação tem de documento, tabela ou bases de dados do gráfico, incluindo bases de dados de MongoDB, com vários modelos de consistência bem definidos. 

    >**Introdução ao**: [compilar uma aplicação web do Azure Cosmos DB](../../cosmos-db/create-documentdb-dotnet.md). Se tiver um programador MongoDB, consulte [compilar uma aplicação web do MongoDB com base de dados do Azure Cosmos](../../cosmos-db/create-mongodb-dotnet.md).

-   **Armazenamento do Azure**: oferece um armazenamento durável e altamente disponível para os blobs, filas, ficheiros e outros tipos de dados nonrelational. Armazenamento fornece a base de armazenamento para as VMs.

    >**Quando utilizar**: quando a aplicação armazena dados nonrelational, tais como pares chave-valor (tabelas), blobs, partilhas de ficheiros ou mensagens (filas).

    >**Introdução ao**: escolher um dos armazenamento destes tipos: [blobs](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabelas](../../cosmos-db/table-storage-how-to-use-dotnet.md), [filas](../../storage/queues/storage-dotnet-how-to-use-queues.md), ou [ficheiros](../../storage/files/storage-dotnet-how-to-use-files.md).

-   **Base de dados SQL do Azure**: baseado no Azure de uma versão do motor Microsoft SQL Server para armazenar dados em tabela relacional na nuvem. Base de dados SQL proporciona um desempenho previsível, escalabilidade sem período de indisponibilidade, continuidade do negócio e proteção de dados.

    >**Quando utilizar**: quando a aplicação necessita de armazenamento de dados com a integridade referencial, suporte de transacional e suporte para consultas TSQL.

    >**Introdução ao**: [criar uma base de dados do SQL Server em minutos, utilizando o portal do Azure](../../sql-database/sql-database-get-started.md).


Pode utilizar [do Azure Data Factory](../../data-factory/introduction.md) mover existente dados no local para o Azure. Se não pronto para mover dados para a nuvem, [ligações híbridas](../../biztalk-services/integration-hybrid-connection-overview.md) permite os BizTalk Services ligar o serviço de aplicações alojada aplicação aos recursos no local. Também pode ligar aos dados do Azure e o armazenamento serviços das suas aplicações no local.

#### <a name="docker-support"></a>Suporte de docker

Contentores de docker, uma forma de Virtualização do SO, permitem-lhe implementar aplicações de forma mais eficiente e previsível. Uma aplicação de funciona em produção a mesma forma que em sistemas de desenvolvimento e teste. Pode gerir contentores utilizando ferramentas padrão de Docker. Pode utilizar as competências existentes e ferramentas de open source populares para implementar e gerir as aplicações baseadas no contentor no Azure.

O Azure oferece várias formas de utilizar contentores nas suas aplicações.

-   **Extensão do Azure do Docker VM**: permite-lhe configurar a VM com ferramentas de Docker para atuar como um anfitrião de Docker.

    >**Quando utilizar**: quando pretende gerar implementações de contentores consistente para as suas aplicações numa VM ou quando pretender utilizar [Docker Compose](https://docs.docker.com/compose/overview/).

    >**Introdução ao**: [criar um ambiente de Docker no Azure utilizando a extensão de VM de Docker](../../virtual-machines/virtual-machines-linux-dockerextension.md).

-   **Serviço de contentor do Azure**: permite-lhe criar, configurar e gerir um cluster de máquinas virtuais que estão pré-configuradas para executar aplicações de. Para saber mais sobre o serviço de contentor, consulte o artigo [introdução do serviço de contentor Azure](../../container-service/container-service-intro.md).

    >**Quando utilizar**: quando precisa de criar ambientes prontos para produção, dimensionáveis, que fornecem ferramentas adicionais de gestão e de agendamento ou quando estiver a implementar um cluster Docker Swarm.

    >**Introdução ao**: [implementar um cluster do serviço de contentor](../../container-service/dcos-swarm/container-service-deployment.md).

-   **Docker máquina**: permite-lhe instalar e gerir um motor de Docker em anfitriões virtuais utilizando comandos de máquina de docker.

    >**Quando utilizar**: quando precisa de protótipo rapidamente uma aplicação através da criação de um único anfitrião de Docker.

-   **Imagem de Docker personalizada para o App Service**: permite-lhe utilizar contentores de Docker a partir do registo de um contentor ou de um contentor de cliente quando implementa uma aplicação web no Linux.

    >**Quando utilizar**: ao implementar uma aplicação web no Linux para uma imagem de Docker.

    >**Introdução ao**: [utilizar uma imagem personalizada do Docker para serviço de aplicações no Linux](../../app-service/containers/quickstart-custom-docker-image.md).

### <a name="authentication"></a>Autenticação

É fundamental saber não só que está a utilizar as suas aplicações, mas também para impedir o acesso não autorizado aos seus recursos. O Azure oferece várias formas de autenticar os clientes de aplicação.

-   **Azure Active Directory (Azure AD)**: O multi-inquilino, baseado na nuvem identidades e acessos serviço de gestão Microsoft. Pode adicionar início de sessão único (SSO) para as suas aplicações através da integração com o Azure AD. Pode aceder a propriedades de diretório utilizando a Azure AD Graph API diretamente ou a Microsoft Graph API. Pode integrar com o suporte do Azure AD para a estrutura de autorização de OAuth2.0 e, em seguida, abrir ID Connect através da utilização de pontos finais HTTP/REST nativos e as bibliotecas de autenticação multiplatform do Azure AD.

    >**Quando utilizar**: quando pretende fornecer uma experiência SSO, trabalhar com dados de gráfico ou autenticar os utilizadores com base no domínio.

    >**Introdução ao**: para obter mais informações, consulte o [guia para programadores do Azure Active Directory](../../active-directory/develop/active-directory-developers-guide.md).

-   **Autenticação do serviço de aplicações**: Quando seleciona o serviço de aplicações para alojar a sua aplicação, também obter suporte de autenticação incorporado para o Azure AD, juntamente com fornecedores de identidade sociais — incluindo Facebook, Google, Microsoft e Twitter.

    >**Quando utilizar**: quando pretender ativar a autenticação numa aplicação do serviço de aplicações ao utilizar o Azure AD, fornecedores de identidade de redes sociais ou ambos.

    >**Introdução ao**: para saber mais sobre a autenticação no serviço de aplicações, consulte [autenticação e autorização no serviço de aplicações do Azure](../../app-service/app-service-authentication-overview.md).

Para saber mais sobre as melhores práticas de segurança no Azure, consulte [padrões e melhores práticas de segurança do Azure](../../security/security-best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitorização

Com a aplicação de cópia de segurança e em execução no Azure, tem de conseguir monitorizar o desempenho, observar problemas e ver como os clientes estão a utilizar a aplicação. O Azure oferece várias opções de monitorização.

-   **Visual Studio Application Insights**: serviço alojado no Azure uma análise extensível que se integra com o Visual Studio para monitorizar as aplicações web em direto. Proporciona os dados que precisa para melhorar continuamente o desempenho e a facilidade de utilização das suas aplicações, se estiver alojados no Azure ou não.

    >**Introdução ao**: Siga a [tutorial do Application Insights](../../application-insights/app-insights-overview.md).

-   **Monitor do Azure**: um serviço que ajuda a visualizar, consultar, encaminhar, arquivar e atuar sobre as métricas e os registos que são gerados pela sua infraestrutura do Azure e recursos. Monitor fornece as vistas de dados que vejam no portal do Azure e é uma única origem para monitorizar os recursos do Azure.
 
    >**Introdução ao**: [introdução ao Azure Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integração DevOps

Se não for aprovisionamento VMs ou publicar as suas aplicações web com a integração contínua, Azure integra-se com a maioria das ferramentas de DevOps populares. Com suporte para ferramentas como Jenkins, GitHub, Puppet, Chef, TeamCity, Ansible, VSTS e outras pessoas, pode trabalhar com as ferramentas que já tiver e maximizar a sua experiência existente.

>**Experimente agora:** [experimentar várias integrações de DevOps](https://azure.microsoft.com/try/devops/).

>**Introdução ao**: para ver as opções de DevOps para uma aplicação do app Service, consulte [a implementação contínua App Service do Azure](../../app-service/app-service-continuous-deployment.md).


## <a name="azure-regions"></a>Regiões do Azure

Azure é uma plataforma de nuvem global que está normalmente disponível em várias regiões em todo o mundo. Quando aprovisiona uma VM no Azure, aplicação ou serviço, é-lhe pedido para selecionar uma região, que representa um centro de dados específico em que a aplicação é executada ou onde os dados são armazenados. Estas regiões correspondem a localizações específicas que são publicadas no [regiões do Azure](https://azure.microsoft.com/regions/) página.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Escolha a região melhor para a sua aplicação e dados

Uma das vantagens da utilização do Azure é que pode implementar as aplicações para vários centros de dados em torno de todo o mundo. A região que escolher pode afetar o desempenho da sua aplicação. Por exemplo, é melhor escolher uma região próximo para a maioria dos seus clientes para reduzir a latência em pedidos de rede. Pode também querer selecione a região para cumprir os requisitos legais para distribuir a aplicação em determinados países. É sempre melhor prática para armazenar dados da aplicação no mesmo centro de dados ou num centro de dados como quase quanto possível para o datacenter que aloja a aplicação.

### <a name="multi-region-apps"></a>Aplicações de multirregião

Embora seja pouco provável, não é a impossibilidade de todo o datacenter fique offline devido a um evento como um desastre natural ou falha de Internet. É uma melhor prática alojam aplicações empresariais vitais no Centro de dados mais do que um para fornecer disponibilidade máxima. Utilizar várias regiões também pode reduzir a latência para utilizadores global e fornecer adicionais oportunidades de flexibilidade ao atualizar as aplicações.

Alguns serviços, tais como Máquina Virtual e os serviços de aplicação, utilizam [Traffic Manager do Azure](../../traffic-manager/traffic-manager-overview.md) para ativar o suporte de multirregião com ativação pós-falha entre regiões para suportar as aplicações empresariais de elevada disponibilidade. Por exemplo, consulte [arquitetura de referência do Azure: aplicação Web com elevada disponibilidade](../../guidance/guidance-web-apps-multi-region.md).

>**Quando utilizar**: Se tiver aplicações empresariais e de elevada disponibilidade que beneficiam da replicação e ativação pós-falha.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Como gerir as minhas aplicações e projetos?

O Azure oferece um conjunto avançado de experiências para criar e gerir os seus recursos do Azure, aplicações e projetos — tanto através de programação e no [portal do Azure](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Interfaces de linha de comandos e o PowerShell

Azure fornece duas formas de gerir as suas aplicações e serviços na linha de comandos utilizando Bash, Terminal, a linha de comandos ou a ferramenta da linha de comandos à escolha. Normalmente, pode efetuar as mesmas tarefas a partir da linha de comandos como no portal do Azure — por exemplo, criar e configurar máquinas virtuais, redes virtuais, as aplicações web e outros serviços.

-   [Interface de linha de comandos do Azure (CLI)](../../xplat-cli-install.md): permite-lhe ligar a uma subscrição do Azure e diversas tarefas relativamente aos recursos do Azure na linha de comandos do programa.

-   [O Azure PowerShell](../../powershell-install-configure.md): Fornece um conjunto de módulos com cmdlets que lhe permite gerir os recursos do Azure através do Windows PowerShell.

### <a name="azure-portal"></a>Portal do Azure

O portal do Azure é uma aplicação baseada na web que pode utilizar para criar, gerir e remover recursos do Azure e serviços. O portal do Azure está localizado em <https://portal.azure.com>. Inclui um dashboard personalizável, ferramentas de gestão de recursos do Azure e aceder às definições de subscrição e informações de faturação. Para obter mais informações, consulte o [descrição geral do portal do Azure](../../azure-portal-overview.md).

### <a name="rest-apis"></a>APIs REST

Azure baseia-se num conjunto de APIs REST que suportam a IU do portal do Azure. Na maioria destas APIs REST também é suportada para permitem-lhe aprovisionar e gerir os seus recursos do Azure e aplicações a partir de qualquer dispositivo de ativados pela Internet programaticamente. Para o conjunto completo de documentação da REST API, consulte o [referência do SDK de REST de Azure](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>APIs

Para além das REST APIs, vários serviços do Azure também permitem-lhe gerir de forma programática recursos das suas aplicações ao utilizar SDKs de Azure específica da plataforma, incluindo SDKs para as seguintes plataformas de desenvolvimento:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](http://azure.github.io/azure-sdk-for-node/)
-   [Java](https://docs.microsoft.com/java/api/)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [python](http://azure-sdk-for-python.readthedocs.io/en/latest/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)

Serviços, tais como [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) e [Media Services do Azure](../../media-services/media-services-dotnet-how-to-use.md) fornecer SDKs do lado do cliente para que os possa aceder aos serviços da web e aplicações de cliente para dispositivos móveis.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Executar a sua aplicação no Azure provável envolve a trabalhar com vários serviços do Azure, sendo todas siga o mesmo ciclo de vida e podem considerar como uma unidade lógica. Por exemplo, uma aplicação web poderá utilizar as aplicações Web, base de dados SQL, armazenamento, a Cache de Redis do Azure e os serviços de rede de entrega de conteúdos do Azure. [O Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) permite trabalhar com os recursos na sua aplicação como um grupo. Pode implementar, atualizar ou eliminar todos os recursos numa operação única e coordenada.

Para além de logicamente agrupar e gerir recursos relacionados, o Azure Resource Manager inclui capacidades de implementação que permitem-lhe personalizar a implementação e configuração de recursos relacionados. Por exemplo, utilizando o Gestor de recursos, pode implementar e configurar uma aplicação que é composta por várias máquinas virtuais, um balanceador de carga e uma base de dados SQL do Azure como uma única unidade.

Desenvolver estas implementações utilizando um modelo Azure Resource Manager, que é um documento formatada em JSON. Modelos permitem-lhe definir uma implementação e gerir as suas aplicações ao utilizar modelos declarativos em vez de scripts. Os modelos podem funcionar para ambientes diferentes, como de teste, de transição e produção. Por exemplo, utilizando os modelos, pode adicionar um botão para um repositório do GitHub que implementa o código no repositório para um conjunto de serviços do Azure com um único clique.

>**Quando utilizar**: Utilize o Gestor de recursos modelos quando pretender que uma implementação baseada no modelo para a sua aplicação que podem ser geridos através de programação utilizando REST APIs, a CLI do Azure e o Azure PowerShell.

>**Introdução ao**: para começar a utilizar modelos, consulte o artigo [modelos Authoring Azure Resource Manager](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Noções sobre contas, subscrições e faturação

Como programadores, iremos como direito aprofundar o código e tente começar a utilizar mais rapidamente possível com tornar as nossas aplicações são executadas. Queremos certamente que começar a trabalhar facilmente possível no Azure. Para ajudar a torná-lo mais fácil, Azure oferece uma [avaliação gratuita](https://azure.microsoft.com/free/). Alguns serviços, mesmo têm uma funcionalidade "Experimente gratuitamente", como [App Service do Azure](https://tryappservice.azure.com/), que não requer o mesmo criar uma conta. Como diversão como está aprofundar a codificação e implementar a aplicação no Azure, também é importante para a compreender como funciona o Azure de um ponto de vista de contas de utilizador, subscrições e faturação demorar algum tempo.

### <a name="what-is-an-azure-account"></a>O que é uma conta do Azure?

Para poder criar ou trabalhar com uma subscrição do Azure, tem de ter uma conta do Azure. Uma conta do Azure é simplesmente uma identidade do utilizador no Azure AD ou num diretório, como uma organização escolar ou profissional, que é considerado fidedigno pelo Azure AD. Se não pertencem essas uma organização, pode criar uma subscrição sempre utilizando o Microsoft Account, que é considerado fidedigno pelo Azure AD. Para saber mais sobre a integração no local Windows Server Active Directory com o Azure AD, consulte [integrar as identidades no local ao Azure Active Directory](../../active-directory/active-directory-aadconnect.md).

Cada subscrição do Azure tem uma relação de confiança com uma instância do Azure AD. Tal significa que confia nesse diretório para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório, mas uma subscrição apenas pode confiar num diretório. Para obter mais informações, consulte [subscrições do Azure como estão associadas ao Azure Active Directory](../../active-directory/active-directory-how-subscriptions-associated-directory.md).

Para além de definir Azure individuais conta identidades, também denominadas *utilizadores*, também pode definir *grupos* no Azure AD. Criar grupos de utilizadores é uma boa forma de gerir o acesso a recursos numa subscrição utilizando o controlo de acesso baseado em funções (RBAC). Para saber como criar grupos, consulte [criar um grupo na pré-visualização do Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md). Também pode criar e gerir grupos por [através do PowerShell](../../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Gerir as suas subscrições

Uma subscrição é uma unidade lógica de serviços do Azure que está ligada a uma conta do Azure. Cada conta associada tem uma função numa subscrição. Faturação para serviços do Azure é efetuada numa base por subscrição. Para obter uma lista de ofertas de subscrição disponível por tipo, consulte [detalhes de oferta do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/).

#### <a name="administrator-roles"></a>Funções de administrador

Uma subscrição do Azure tem várias funções de administrador de conta, que pode atribuir em qualquer altura.

-   **Conta de administrador**: esta função tem controlo total sobre a subscrição e é a conta que é responsável por faturação.

-   **Administrador de serviço**: esta função tem controlo sobre todos os serviços na subscrição. Por predefinição, esta é a mesma conta como o administrador da conta.

-   **Coadministrador**: esta função tem o mesmo acesso como o administrador de serviço, exceto que não é possível alterar a associação da subscrição para um diretório do Azure.

Para saber mais sobre as funções de administrador, consulte [como adicionar ou alterar funções de administrador do Azure](../../billing/billing-add-change-azure-subscription-administrator.md#add-an-admin-for-a-subscription).

#### <a name="resource-groups"></a>Grupos de recursos

Quando aprovisionar novos serviços do Azure, fazê-lo numa determinada subscrição. Serviços do Azure individuais, também designados por recursos, são criados no contexto de um grupo de recursos. Grupos de recursos tornam mais fácil de implementar e gerir os recursos da sua aplicação. Um grupo de recursos deve conter todos os recursos para a sua aplicação que pretende trabalhar com como uma unidade. Pode mover recursos entre grupos de recursos e até mesmo a subscrições diferentes. Para saber mais sobre como mover recursos, consulte [mover recursos para o novo grupo de recursos ou subscrição](../../resource-group-move-resources.md).

O Explorador de recursos do Azure é uma ótima ferramenta para visualizar os recursos que já tiver criado na sua subscrição. Para obter mais informações, consulte [utilizar o Explorador de recursos do Azure para ver e modificar recursos](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Conceder acesso a recursos

Ao permitir o acesso aos recursos do Azure, é sempre melhor prática para fornecer aos utilizadores com o menor privilégio é necessário para efetuar uma tarefa especificada.

-   **O controlo de acesso baseado em funções (RBAC)**: no Azure, pode conceder acesso a contas de utilizador (principais) num âmbito especificado: subscrição, grupo de recursos ou recursos individuais. RBAC permite-lhe implementar um conjunto de recursos para um grupo de recursos e conceder permissões a um grupo ou utilizador específico. Também permitem a limitar o acesso apenas os recursos que pertencem ao grupo de recursos de destino. Também pode conceder acesso a um único recurso, tal como uma máquina virtual ou a rede virtual. Para conceder acesso, atribuir uma função de utilizador, grupo ou principal de serviço. Existem várias funções predefinidas, e também pode definir as suas próprias funções personalizadas.

    >**Quando utilizar**: quando necessitar de gestão de acesso detalhado para utilizadores e grupos.

    >**Introdução ao**: para obter mais informações, consulte [introdução à gestão de acesso no portal do Azure](../../active-directory/role-based-access-control-what-is.md).

-   **Objetos de principais de serviço**: para além de fornecer acesso a principais de utilizador e grupos, pode conceder o acesso ao mesmo a um principal de serviço.

    > **Quando utilizar**: quando estiver a programaticamente gerir recursos do Azure ou conceder acesso para aplicações. Para obter mais informações, consulte [supplication criar do Active Directory e um principal de serviço](../../resource-group-create-service-principal-portal.md).

#### <a name="tags"></a>Etiquetas

O Azure Resource Manager permite-lhe atribuir etiquetas personalizadas para recursos individuais. As etiquetas que são pares chave-valor, podem ser útil quando necessita de organizar recursos de faturação ou de monitorização. Etiquetas fornecem uma forma de controlar recursos em vários grupos de recursos. Pode atribuir etiquetas no portal, o modelo Azure Resource Manager ou através de programação, utilizando a API REST, a CLI do Azure ou o PowerShell. Pode atribuir várias etiquetas para cada recurso. Para obter mais informações, consulte [utilizar etiquetas para organizar os recursos do Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Faturação

A movimentação de computação para serviços alojados na nuvem no local, controlar e estimar a utilização do serviço e os custos relacionados são preocupações significativas. É importante ser capaz de calcular o custo novos recursos para executar mensalmente. Terá também de ser capazes de aspeto de faturação para um determinado mês de com base no gastos atual do projeto.

#### <a name="get-resource-usage-data"></a>Obter dados de utilização de recursos

O Azure oferece um conjunto de APIs REST de faturação, que lhe conceder acesso às consumo de recursos e informações de metadados para as subscrições do Azure. Estas APIs de faturação dão-lhe a capacidade de melhor prever e gerir os custos do Azure. Pode controlar e analisam os gastos com em incrementos de hora a hora, criam alertas de gastos e prever futura faturação com base em tendências de utilização atual.

>**Introdução ao**: para saber mais sobre como utilizar as APIs de faturação, consulte [descrição geral da utilização de faturação do Azure e RateCard APIs](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Prever futuros custos

Embora é difícil para estimar os custos de antecedência, o Azure tem um [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) que pode utilizar quando estimar os custos de implementado recursos. Também pode utilizar o painel de faturação no portal e as APIs de REST de faturação para estimar os custos futuros, com base no consumo atual.

>**Introdução ao**: consulte [descrição geral da utilização de faturação do Azure e RateCard APIs](../../billing-usage-rate-card-overview.md).

#### <a name="set-up-billing-alerts"></a>Set up billing alerts (Configurar alertas de faturação)

Depois de implementar a aplicação ou solução no Azure, pode criar alertas que enviar por e-mail quando abordar os limites de gastos que estão definidos no alerta.

>**Introdução ao**: para obter mais informações, consulte [configurar alertas para as subscrições do Microsoft Azure de faturação](../../billing-set-up-alerts.md).
