---
title: "Guia de operadores de TI do Azure de introdução | Microsoft Docs"
description: "Introdução ao guia de introdução para operadores de TI do Azure"
services: 
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: nepeters
ms.openlocfilehash: 1180001c9fe74aab6b51c5b5969b80a8c7e1302f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="introduction-to-cloud-computing-and-microsoft-azure"></a>Introdução à informática em nuvem e o Microsoft Azure

Este guia apresenta conceitos principais relacionadas com a implementação e gestão de uma infraestrutura do Microsoft Azure. Se não estiver familiarizado com a nuvem de computação ou o Azure, este guia ajuda rapidamente começar com os conceitos, implementação e detalhes de gestão. Muitos secções deste guia abordam uma operação como implementar uma máquina virtual e, em seguida, fornecem uma ligação para aprofundada detalhes técnicos.


## <a name="cloud-computing-overview"></a>Descrição geral de informática em nuvem

Fornece uma alternativa moderna para o Centro de dados tradicionais no local de informática em nuvem. Os fornecedores de nuvem pública fornecem e gerir a infraestrutura de informática todas as e o software de gestão subjacente. Estes fornecedores fornecem uma ampla variedade de serviços em nuvem. Neste caso, um serviço em nuvem pode ser uma máquina virtual, um servidor web ou o motor de base de dados alojado na nuvem. Como um cliente do fornecedor de nuvem, concessão estes serviços em nuvem de forma como necessário. Ao fazê-lo, converta o custo de capital da manutenção de hardware para uma despesas operacionais. Um serviço em nuvem fornece também destas vantagens:

-   Implementação rápida de ambientes de grandes dimensões de computação

-   Desalocação rápida dos sistemas que já não são necessários

-   Como o facilitar a implementação dos sistemas tradicionalmente complexos balanceadores de carga

-   Capacidade para fornecer capacidade de cálculo flexível ou dimensionar quando necessário

-   Ambientes informáticos mais económicos

-   Aceder a partir de qualquer local com um portal baseado na web ou a automatização programática

-   Serviços baseados na nuvem para satisfazer a maioria das necessidades de computação e de aplicação

Com a infraestrutura no local, tem controlo total sobre o hardware e software que é implementado. Historicamente, esta situação deu origem a decisões de aprovisionamento de hardware que foco sobre como aumentar verticalmente. Um exemplo é a compra de um servidor com mais núcleos para satisfazer as necessidades de desempenho das horas de ponta. Infelizmente, esta infraestrutura poderá APARENTAREM ser pouco utilizada fora de uma janela de pedido. Com o Azure, pode implementar apenas a infraestrutura de que precisa e ajustar esta cópia de segurança ou para baixo em qualquer altura. Isto leva a um foco sobre como aumentar horizontalmente através da implementação de nós de computação adicionais para satisfazer a necessidade de desempenho. Ampliar serviços em nuvem é mais rentável do que como aumentar verticalmente através de hardware dispendiosas.

Microsoft tem de implementar vários centros de dados do Azure à volta de todo o mundo, com mais planeada. Além disso, a Microsoft está aumentar nuvens sovereign em regiões como China e na Alemanha. Apenas as empresas global maiores podem implementar centros de dados desta forma, para que utilizar o Azure facilita as empresas de qualquer dimensão para implementar os seus serviços próximo respetivos clientes.

Para pequenas empresas, Azure permite a um ponto de entrada de baixo custo, a capacidade de dimensionar rapidamente a pedido para computação aumenta. Isto impede que um investimento de capital prévio grande na infraestrutura e fornece a flexibilidade para architect e novamente architect sistemas conforme necessário. A utilização de se adequa bem com o modelo de dimensionamento rápido e falhar fast de crescimento de arranque de informática na cloud.

Para obter mais informações sobre regiões do Azure disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-is-classified-into-three-categories-saas-paas-and-iaas"></a>São classificados em três categorias de informática em nuvem: SaaS, IaaS e PaaS,.

#### <a name="saas-software-as-a-service"></a>SaaS: Software como um serviço

SaaS é software que está alojada e gerido centralmente. Normalmente, com base numa arquitetura multi-inquilino — uma única versão da aplicação é utilizada para todos os clientes. Pode ampliar a várias instâncias para garantir o melhor desempenho em todas as localizações. Normalmente, o software de SaaS está licenciado através de uma subscrição mensal ou anual.

Microsoft Office 365 é um bom exemplo de uma oferta de SaaS. Os subscritores pagar uma taxa de subscrição mensais ou anuais e obtêm Microsoft Exchange, Microsoft OneDrive e o resto do conjunto de aplicações Microsoft Office, como um serviço. Os subscritores obter sempre a versão mais recente e o Exchange server for gerido por si. Em comparação com a instalar e atualizar todos os anos o Office, este é menos dispendioso e requer menos esforço.

#### <a name="paas-platform-as-a-service"></a>PaaS: Plataforma como serviço 

Com o PaaS, implementar a aplicação num ambiente que fornece o fornecedor do serviço em nuvem. O fornecedor efetua todas a gestão da infraestrutura, pelo que pode concentrar-se no desenvolvimento de aplicações.

O Azure oferece que vários PaaS computação ofertas, incluindo a funcionalidade Web Apps do App Service do Azure e os Cloud Services do Azure (funções web e de trabalho). Em ambos os casos, os programadores dispõem de várias formas de implementar a sua aplicação sem saber tudo sobre os bolts e nuts que o suportam. Os programadores não tem de criar máquinas virtuais (VMs), utilize o protocolo RDP (Remote Desktop Protocol) para iniciar sessão em cada um deles ou instalar a aplicação. Apenas um botão de acessos (ou feche ao mesmo) e as ferramentas fornecidas pelo Microsoft aprovisionar as VMs e, em seguida, implementam e instalam a aplicação nos mesmos.

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infraestrutura como serviço

Um fornecedor de nuvem de IaaS é executado e gere todos os recursos de computação física e o software necessário para ativar a virtualização do computador. Um cliente deste serviço implementa máquinas virtuais nos centros de dados alojada. Embora as máquinas virtuais estão localizadas no Centro de dados noutro local, o consumidor do IaaS tem controlo sobre a configuração e gestão dos mesmos.

Azure inclui várias soluções de IaaS, incluindo máquinas virtuais, conjuntos de dimensionamento de máquina virtual e a infraestrutura de rede relacionada. Máquinas virtuais são um populares opção para migrar inicialmente serviços para o Azure porque permitem que um modelo de migração "de comparação de precisão e deslocar". Pode configurar uma VM, como a infraestrutura atualmente em execução os serviços no seu centro de dados e, em seguida, migre o software para a nova VM. Poderá ter de fazer atualizações de configuração, tais como URLs a outros serviços ou o armazenamento, mas é possível migrar muitos aplicações desta forma.

Conjuntos de dimensionamento de máquina virtual são criados por cima de máquinas virtuais do Azure e fornecem uma forma fácil de implementar clusters de VMs idênticas. Conjuntos de dimensionamento de máquina virtual também suportam o dimensionamento automático para que as novas VMs podem ser implementadas automaticamente quando necessário. Isto torna a conjuntos de dimensionamento de máquina virtual numa plataforma ideal para o nível mais elevados microsserviço computação clusters de anfitriões, como o Service Fabric do Azure e o serviço de contentor do Azure.

## <a name="azure-services"></a>Serviços do Azure

O Azure oferece vários serviços na sua plataforma de informática em nuvem. Estes serviços incluem o seguinte:

### <a name="compute-services"></a>Serviços de computação

Serviços para alojar e executar a carga de trabalho da aplicação:

-   Máquinas virtuais do Azure — Linux e Windows

-   Serviços de aplicações (aplicações Web, as aplicações móveis, as Logic Apps, API Apps e aplicações de função)

-   O Azure Batch (para paralelo em grande escala e as tarefas de computação do batch)

-   Azure Service Fabric

-   Azure Container Service

### <a name="data-services"></a>Serviços de dados

Serviços para armazenar e gestão de dados:

-   Armazenamento do Azure (inclui os serviços de ficheiro, tabela, fila e Blob do Azure)

-   Base de Dados SQL do Azure

-   Azure DocumentDB

-   Microsoft Azure StorSimple

-   Cache de Redis do Azure

### <a name="application-services"></a>Serviço de aplicações

Serviços para a construção e funcionamento de aplicações:

-   Azure Active Directory (Azure AD)

-   Service Bus do Azure para ligar sistemas distribuídos

-   O Azure HDInsight para o processamento de macrodados

-   O agendador do Azure

-   Os Media Services do Azure

### <a name="network-services"></a>Serviços de rede

Serviços de rede no Azure e entre centros de dados do Azure e no local:

-   Rede Virtual do Azure

-   Azure ExpressRoute

-   DNS fornecidos pelo Azure

-   Traffic Manager do Azure

-   Rede de entrega de conteúdos do Azure

Para obter documentação detalhada nos serviços do Azure, consulte [documentação de serviço do Azure](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Conceitos chaves do Azure

### <a name="datacenters-and-regions"></a>Os Datacenters e regiões


Azure é uma plataforma de nuvem global que está normalmente disponível em várias regiões em todo o mundo. Quando aprovisiona uma VM no Azure, aplicação ou serviço, é-lhe pedido para selecionar uma região. A região selecionada representa um centro de dados de speciﬁc onde a aplicação for executada. Para obter mais informações, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

Uma das beneﬁts da utilização do Azure é que pode implementar as aplicações em vários centros de dados em torno de todo o mundo. A região que escolher pode aﬀect o desempenho da sua aplicação. É ideal para escolher uma região próximo mais dos seus clientes, para reduzir a latência em pedidos de rede. Também pode selecionar uma região para cumprir os requisitos legais para distribuir a aplicação em determinados países.

### <a name="azure-portal"></a>Portal do Azure


O portal do Azure é uma aplicação baseada na web que pode ser utilizada para criar, gerir e remover recursos do Azure e serviços. O portal do Azure está localizado em https://portal.azure.com. Inclui um dashboard personalizável e ferramentas para gerir recursos do Azure. Também fornece informações de faturação e subscrição. Para obter mais informações, consulte [descrição geral do Microsoft Azure portal](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) e [recursos do Azure de gerir através do portal](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Recursos

Recursos do Azure são computação individuais, redes, dados ou aplicação que aloja os serviços que foram implementados para uma subscrição do Azure. Alguns recursos comuns são as máquinas virtuais, contas de armazenamento ou bases de dados SQL. Serviços do Azure, muitas vezes, consistem de vários recursos relacionados do Azure. Por exemplo, uma máquina virtual do Azure podem incluir uma VM, a conta de armazenamento, o adaptador de rede e o endereço IP público. Estes recursos podem ser criados, geridos e eliminados individualmente ou como um grupo. Recursos do Azure são abordados em mais detalhe posteriormente neste guia.

### <a name="resource-groups"></a>Grupos de recursos

Um grupo de recursos do Azure é um contentor que retém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Grupos de recursos do Azure são abordados em mais detalhe posteriormente neste guia.

### <a name="resource-manager-templates"></a>Modelos do Resource Manager

Um modelo Azure Resource Manager é um ficheiro de JavaScript Object Notation (JSON) que define um ou mais recursos para implementar um grupo de recursos. Também define dependências entre os recursos implementados. Modelos do Resource Manager são abordados em mais detalhe posteriormente neste guia.

### <a name="automation"></a>Automatização


Para além de criar, gerir e eliminar recursos através do portal do Azure, pode automatizar estas atividades utilizando o PowerShell ou a interface de linha de comandos do Azure (CLI).

**Azure PowerShell**

O Azure PowerShell é um conjunto de módulos com cmdlets para gerir o Azure. Pode utilizar os cmdlets para criar, gerir e remover serviços do Azure. Os cmdlets podem ajudar a alcançar a implementações hands-off, repetíveis e consistentes. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-azurerm-ps).

**Interface de linha de comandos do Azure**

A interface de linha de comandos do Azure é uma ferramenta que pode utilizar para criar, gerir e remover recursos do Azure na linha de comandos. A CLI do Azure está disponível para o Linux, Mac OS X e Windows. Para obter mais informações e os detalhes técnicos, consulte [instalar a CLI do Azure](/cli/azure/install-azure-cli.md).

**RESTO das APIs** Azure baseia-se num conjunto de APIs REST que suportam a IU do portal do Azure. Na maioria destas APIs REST também é suportada para permitem-lhe aprovisionar e gerir recursos do Azure e aplicações a partir de qualquer dispositivo de ativados pela Internet programaticamente. Para obter mais informações, consulte o [referência do SDK de REST de Azure](https://docs.microsoft.com/rest/api/index).


## <a name="azure-subscriptions"></a>Subscrições do Azure


Uma subscrição é um agrupamento lógico de serviços do Azure que está ligado a uma conta do Azure. Conta do Azure único pode conter várias subscrições. Faturação para serviços do Azure é efetuada numa base por subscrição. As subscrições do Azure têm um administrador de conta, que tem controlo total sobre a subscrição e um administrador de serviço, que tem controlo sobre todos os serviços na subscrição. Para além dos administradores, contas individuais podem ser concedidas detalhadas de controlo de recursos do Azure através do RBAC.

### <a name="select-and-enable-an-azure-subscription"></a>Selecionar e ativar uma subscrição do Azure

Antes de pode trabalhar com os serviços do Azure, terá de uma subscrição. Estão disponíveis vários tipos de subscrição.

**Liberte contas**: A ligação para se inscrever para uma conta gratuita está no [Web site Azure](https://azure.microsoft.com/). Isto fornece um crédito ao longo de 30 dias para experimentar qualquer combinação de recursos no Azure. Se exceder a quantidade de crédito, a sua conta está suspenso. No final da versão de avaliação, os seus serviços são descontinuados e deixará de funcionar. Pode atualizar para uma subscrição pay as you go em qualquer altura.

**Subscrições do MSDN**: Se tiver uma subscrição do MSDN, obtém uma quantidade específica em crédito do Azure por mês. Por exemplo, se tiver um Microsoft Visual Studio Enterprise com subscrição do MSDN, obter \$150 por mês em crédito do Azure.

Se exceder a quantidade de crédito, o serviço estão desativadas até que inicia o mês seguinte. Pode desativar o limite de gastos e adicionar um cartão de crédito a ser utilizada para os custos adicionais. Alguns destes custos são subvalorizados para contas do MSDN. Por exemplo, paga o preço do Linux para VMs com o Windows Server e existe sem encargos adicionais para servidores da Microsoft, tais como o Microsoft SQL Server. Isto faz com que as contas de MSDN ideais para cenários de desenvolvimento e teste.

**Contas de BizSpark**: elegíveis do BizSpark Microsoft o programa fornece várias vantagens para startups. Uma desses vantagens é o acesso o todo o software Microsoft para ambientes de desenvolvimento e teste de até cinco contas do MSDN. Obter 150 $ em crédito do Azure para cada uma dessas contas MSDN cinco e paga taxas reduzidas para vários serviços do Azure, como as máquinas virtuais.

**Pay as you go**: com esta subscrição paga a utilizar ao anexar um cartão de crédito ou de débito para a conta. Se uma organização, pode também ser aprovado para faturação.

**Contratos Enterprise**: com um enterprise agreement, consolidar através de um determinado número de serviços no Azure ao longo do ano seguinte, e paga essa quantidade antecedência. Os montantes que efetuar é consumido ao longo do ano. Se exceder a quantidade de compromisso, pode pagar o excedido na arrears. Dependendo da quantidade do compromisso, obter um desconto nos serviços de no Azure.



### <a name="grant-administrative-access-to-an-azure-subscription"></a>Conceda acesso administrativo para uma subscrição do Azure


Várias funções de administrador de conta disponíveis e podem ser alteradas em qualquer altura. Duas funções de chaves são:

-   **Administrador de serviço**: esta função está autorizada a gerir os serviços do Azure. Por predefinição, é concedido acesso para a mesma conta que o administrador da conta.

-   **Coadministrador**: esta função tem o mesmo acesso que o administrador de serviço. No entanto, esta função não é possível alterar a associação de uma subscrição para diretórios do Azure.

Para obter mais informações, consulte [como adicionar ou alterar funções de administrador do Azure](../../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Ver as informações de faturação no portal do Azure


Um componente importante da utilização do Azure é a capacidade para ver as informações de faturação. O portal do Azure fornece informações detalhadas sobre as informações de faturação do Azure.

Para obter mais informações, consulte [como transferir o Azure faturação dados de utilização de uma fatura e diariamente](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Obter as informações de faturação de APIs de faturação


Além de visualizar a faturação no portal, pode aceder as informações de faturação, utilizando um script ou programa através das APIs REST faturação do Azure:

-   Pode utilizar a API de utilização do Azure para obter os dados de utilização. Pode ajustar as informações de utilização de faturação por marcação de recursos do Azure relacionados. Por exemplo, pode etiquetar cada um dos recursos num grupo de recursos com um nome de departamento ou o nome do projeto e, em seguida, controlar os custos especificamente para esse uma tag.

-   Pode utilizar a API de cartão de velocidade do Azure para listar todos os recursos disponíveis no, juntamente com os metadados e preços informações sobre cada um desses recursos.

Para obter mais informações, consulte [obter informações acerca do consumo de recursos do Microsoft Azure](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Custo de previsão com a Calculadora de preços

Os preços para cada serviço no Azure são diferente. Muitos serviços do Azure fornecem escalões básico, Standard e Premium. Normalmente, cada escalão tem vários níveis de preço e desempenho. Utilizando o [Calculadora de preços online](http://azure.microsoft.com/pricing/calculator), pode criar estimativas de preços. A Calculadora inclui flexibilidade para estimar o custo de um único recurso ou um grupo de recursos.

### <a name="set-up-billing-alerts"></a>Set up billing alerts (Configurar alertas de faturação)

Depois de ter implementado a sua aplicação ou solução no Azure, pode criar alertas que enviar por e-mail quando abordagem limites de gastos definido o alerta. Para obter mais informações, consulte [configurar alertas para as subscrições do Microsoft Azure de faturação](../../billing/billing-set-up-alerts.md).

## <a name="azure-resource-manager"></a>Azure Resource Manager

O Azure Resource Manager é um mecanismo de implementação, gestão e organização de recursos do Azure. Utilizando o Gestor de recursos, pode colocar vários recursos individuais em conjunto num grupo de recursos.

Gestor de recursos também inclui capacidades de implementação que permitem a implementação personalizável e a configuração de recursos relacionados. Por exemplo, utilizando o Gestor de recursos, pode implementar uma aplicação que é composta por várias máquinas virtuais, um balanceador de carga e uma base de dados do SQL Server como uma única unidade. Desenvolver estas implementações utilizando um modelo do Resource Manager.

O Resource Manager oferece várias vantagens:

-   Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.

-   Repetidamente pode implementar a solução durante todo o ciclo de vida de desenvolvimento e ter a confiança de que os recursos são implementados num estado consistente.

-   Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.

-   Pode definir as dependências entre os recursos, de modo a que sejam implementados na ordem correta.

-   Pode aplicar o controlo de acesso a todos os serviços no seu grupo de recursos porque o RBAC está integrado de forma nativa na plataforma de gestão.

-   Pode aplicar etiquetas de recursos para organizar logicamente todos os recursos na sua subscrição.

-   Pode clarificar a faturação da sua organização ao visualizar os custos de um grupo de recursos que partilham a mesma etiqueta.

### <a name="tips-for-creating-resource-groups"></a>Sugestões para a criação de grupos de recursos


Quando estiver a tomar decisões sobre os grupos de recursos, considere estas sugestões:

-   Todos os recursos num grupo de recursos devem ter o mesmo ciclo de vida.

-   Pode atribuir um recurso a apenas um grupo de cada vez.

-   Pode adicionar ou remover um recurso de um grupo de recursos em qualquer altura. Cada recurso têm de pertencer a um grupo de recursos. Por isso, se remover um recurso a um grupo, deve adicioná-lo para outro.

-   Pode mover a maioria dos tipos de recursos para um grupo de recursos diferente em qualquer altura.

-   Os recursos num grupo de recursos podem ser em regiões diferentes.

-   Pode utilizar um grupo de recursos para controlar o acesso a recursos na mesma.

### <a name="building-resource-manager-templates"></a>Modelos do Resource Manager edifício

Modelos do Resource Manager forma declarativa de definir os recursos e configurações de recursos que serão implementadas para um grupo de recursos única. Pode utilizar modelos do Resource Manager para orquestrar com implementações complexas sem a necessidade de processamento de scripts em excesso ou configuração manual. Depois de desenvolver um modelo, pode implementá-la várias vezes — sempre com um resultado idêntico.

Um modelo do Resource Manager é constituída por quatro secções:

-   **Os parâmetros**: estes são entradas para a implementação. Os valores de parâmetros podem ser fornecidos por um humanos ou um processo automatizado. Um parâmetro de exemplo pode ser um nome de utilizador de administrador e a palavra-passe para uma VM do Windows. Os valores de parâmetros são utilizados em toda a implementação quando que estão especificados.

-   **Variáveis**: estes são utilizados para conter valores que são utilizados em toda a implementação. Ao contrário de parâmetros, um valor da variável não é fornecido no momento da implementação. Em vez disso, é difícil coded ou gerada dinamicamente.

-   **Recursos**: Esta secção do modelo define os recursos implementados, como máquinas virtuais, contas de armazenamento e redes virtuais.

-   **Saída**: depois de uma implementação estiver concluída, o Gestor de recursos pode devolver dados como cadeias de ligação gerado dinamicamente.

Os seguintes mecanismos estão disponíveis para a automatização de implementação:

-   **As funções**: pode utilizar várias funções nos modelos do Resource Manager. Estes incluem operações como converter uma cadeia para minúsculas, implementação de várias instâncias de um recurso definido e dinamicamente a devolver o grupo de recursos de destino. As funções do Gestor de recursos ajudam a criar implementações dinâmicas.

-   **Dependências de recursos**: quando estiver a implementar vários recursos, alguns recursos não terá uma dependência na outras pessoas. Para facilitar a implementação, pode utilizar uma declaração de dependência para que os recursos dependentes são implementados antes da outras pessoas.

-   **Ligação de modelo**: de um modelo do Resource Manager, pode ligar a outro modelo. Isto permite decomposição de implementação para um conjunto de modelos direcionados e com uma finalidade específica.

Pode criar modelos do Resource Manager qualquer editor de texto. No entanto, o Azure SDK para Visual Studio inclui ferramentas para ajudá-lo. Ao utilizar o Visual Studio, pode adicionar recursos para o modelo através de um assistente, em seguida, implementar e depurar o modelo diretamente no Visual Studio. Para obter mais informações, consulte [modelos Authoring Azure Resource Manager](../../resource-group-authoring-templates.md).

Por fim, pode converter grupos de recursos existente para um modelo reutilizável do portal do Azure. Isto pode ser útil se pretender criar um modelo implementável de um grupo de recursos existente ou apenas pretende examinar o JSON subjacente. Para exportar um grupo de recursos, selecione o **scripts de automatização** botão das definições do grupo de recursos.

## <a name="security-of-azure-resources-rbac"></a>Segurança de recursos do Azure (RBAC)

Pode garantir acesso operacional para contas de utilizador a um âmbito especificado: subscrição, o grupo de recursos ou o recurso individuais. Isto significa que pode implementar um conjunto de recursos para um grupo de recursos, tais como uma máquina virtual e todos os recursos relacionados e conceder permissões a um grupo ou utilizador específico. Esta abordagem limita o acesso a apenas os recursos que pertencem ao grupo de recursos de destino. Também pode conceder acesso a um único recurso, tal como uma máquina virtual ou uma rede virtual.

Para conceder acesso, atribuir uma função de utilizador ou grupo de utilizadores. Existem várias funções predefinidas. Também pode definir as suas próprias funções personalizadas.

Seguem-se algumas funções de exemplo incorporadas no Azure:

-   **Proprietário**: um utilizador com esta função pode gerir tudo, incluindo o acesso.

-   **Leitor**: um utilizador com esta função podem ler os recursos de todos os tipos (exceto os segredos), mas não é possível efetuar alterações.

-   **Contribuinte de máquina virtual**: um utilizador com esta função podem gerir máquinas virtuais, mas não é possível gerir a rede virtual para que estão ligados ou a conta de armazenamento onde reside o ficheiro VHD.

-   **Contribuinte da BD do SQL Server**: um utilizador com esta função pode gerir bases de dados SQL, mas não as respetivas políticas relacionadas com segurança.

-   **Gestor de segurança do SQL Server**: um utilizador com esta função pode gerir as políticas relacionadas com a segurança de bases de dados e servidores SQL.

-   **Contribuinte de conta de armazenamento**: um utilizador com esta função podem gerir contas de armazenamento, mas não é possível gerir o acesso às contas de armazenamento.

Para obter mais informações, consulte [utilize atribuições de funções para gerir o acesso aos recursos da sua subscrição do Azure](../../active-directory/role-based-access-control-configure.md).

## <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

Máquinas virtuais do Azure é um dos serviços de IaaS centrais no Azure. Máquinas virtuais do Azure suporta a implementação do Windows ou Linux máquinas virtuais no Centro de dados do Microsoft Azure. Com máquinas de virtuais do Azure, tem controlo total sobre a configuração de VM e são responsáveis por todos os instalação de software, configuração e manutenção.

Quando estiver a implementar uma VM do Azure, pode selecionar uma imagem do Azure Marketplace, ou pode fornecer a própria imagem generalizada. Esta imagem é utilizada para aplicar o sistema operativo e a configuração inicial. Durante a implementação do Resource Manager irá processar algumas definições de configuração, tais como atribuir o nome do computador, as credenciais administrativas e configuração de rede. Pode utilizar extensões de máquina virtual do Azure para automatizar mais configurações, tais como a instalação de software, configuração de antivírus e soluções de monitorização.

Pode criar máquinas virtuais em vários tamanhos diferentes. O tamanho da máquina virtual estabelece a alocação de recursos, tais como a capacidade de processamento, memória e armazenamento. Em alguns casos, as funcionalidades específicas, tais como placas de rede com capacidade RDMA e discos SSD estão disponíveis apenas com determinadas tamanhos de VM. Para uma lista completa das capacidades e dos tamanhos da VM, consulte "Tamanhos de máquinas virtuais do Azure" para [Windows](../../virtual-machines/windows/sizes.md) e [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Casos de utilização

Porque as máquinas virtuais do Azure oferece controlo total sobre a configuração, estes são ideais para uma vasta gama de cargas de trabalho do servidor que o se ajusta um modelo PaaS. Cargas de trabalho do servidor, tais como servidores de base de dados (SQL Server, Oracle ou MongoDB), Windows Server Active Directory, Microsoft SharePoint e muitos mais tornar-se possíveis ser executada na plataforma Microsoft Azure. Se assim o desejar, pode mover destas cargas de trabalho de um datacenter no local para um ou mais regiões do Azure, sem uma grande quantidade de reconfiguração.

### <a name="deployment-of-virtual-machines"></a>Implementação de máquinas virtuais

Pode implementar máquinas virtuais do Azure utilizando o portal do Azure, através da utilização de automatização com o módulo Azure PowerShell ou através da utilização de automatização com a CLI de várias plataformas.

**Portal**

Implementar uma máquina virtual utilizando o portal do Azure requer apenas uma subscrição do Azure Active Directory e o acesso a um web browser. Pode selecionar várias imagens de sistema operativo diferente com configurações diferentes. Todos os requisitos de rede e armazenamento são configurados durante a implementação. Para obter mais informações, consulte "Criar uma máquina virtual no portal do Azure" para [Windows](../../virtual-machines/windows/quick-create-portal.md) e [Linux](../../virtual-machines/linux/quick-create-portal.md).

Para além de implementar uma máquina virtual do portal do Azure, pode implementar um modelo Azure Resource Manager no portal. Isto irá implementar e configurar todos os recursos, tal como definido no modelo. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).


**PowerShell**

Implementar uma máquina virtual do Azure utilizando o PowerShell permite a automatização da implementação completa de todos os recursos de máquinas de virtuais relacionados, incluindo o armazenamento e rede. Para obter mais informações, consulte [criar uma VM do Windows utilizando o Gestor de recursos e o PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Para além de implementar individualmente recursos de computação do Azure, pode utilizar o módulo Azure PowerShell para implementar um modelo Azure Resource Manager. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Interface de linha de comandos (CLI)**

Tal como acontece com o módulo do PowerShell, a interface de linha de comandos do Azure fornece a automatização da implementação e pode ser utilizada em sistemas Windows, OS X ou Linux. Quando estiver a utilizar a CLI do Azure **criação rápida vm** comando, todos os relacionados com os recursos da máquina virtual (incluindo o armazenamento e redes) e a máquina virtual propriamente dito implementadas. Para obter mais informações, consulte [criar uma VM com Linux no Azure utilizando a CLI](../../virtual-machines/linux/quick-create-cli.md).

Da mesma forma, pode utilizar a CLI do Azure para implementar um modelo Azure Resource Manager. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Acesso e segurança para máquinas virtuais

Aceder a uma máquina virtual a partir da Internet necessita de rede associado à interface ou, se aplicável, ser configurado com um endereço IP público de Balanceador de carga. O endereço IP público inclui um nome DNS que irá resolver-se à máquina virtual ou o Balanceador de carga. Para obter mais informações, consulte [endereços IP no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Gerir o acesso à máquina virtual através do endereço IP público utilizando um recurso de grupo (NSG) de segurança de rede. Um NSG funciona como uma firewall e permite ou nega o tráfego através da interface de rede ou de sub-rede num conjunto de portas definidos. Por exemplo, para criar uma sessão de ambiente de trabalho remoto com uma VM do Azure, terá de configurar o NSG para permitir tráfego de entrada na porta 3389. Para obter mais informações, consulte [a abertura de portas para uma VM no Azure utilizando o portal do Azure](../../virtual-machines/windows/nsg-quickstart-portal.md).

Por fim, tal como acontece com a gestão de qualquer sistema de computador, deve fornecer segurança para uma máquina virtual do Azure, o sistema operativo utilizando as credenciais de segurança e de firewalls de software.

## <a name="azure-storage"></a>Storage do Azure

Storage do Azure é um serviço gerida pela Microsoft que fornece armazenamento durável, dimensionável e redundante. Pode adicionar uma conta de armazenamento do Azure como um recurso para qualquer grupo de recursos com qualquer método de implementação de recursos. Azure inclui quatro tipos de armazenamento: Blob storage, armazenamento de ficheiros, armazenamento de tabelas e o armazenamento de filas. Quando implementar uma conta de armazenamento, dois tipos de conta estiver disponível, para fins gerais e armazenamento de Blobs. Uma conta do storage para fins gerais dá-lhe acesso a todos os quatro tipos de armazenamento. Contas do blob storage são semelhantes às contas para fins gerais, mas contém blobs especializados que incluem camadas de acesso frequente e amovíveis. Para obter mais informações sobre armazenamento de BLOBs, consulte [Blob storage do Azure](../../storage/blobs/storage-blob-storage-tiers.md).

As contas do storage do Azure podem ser configuradas com diferentes níveis de redundância:

-   **Armazenamento localmente redundante** fornece elevada disponibilidade, garantindo que são efetuadas em sincronia três cópias de todos os dados antes de uma operação de escrita é considerada com êxito. Estas cópias são armazenadas num único local e numa única região. As réplicas residirem em domínios de falhas separada e domínios de atualização. Isto significa que os dados estão disponíveis mesmo se um nó de armazenamento que é que contém a falha de dados ou é colocado offline para ser atualizado.

-   **Armazenamento georredundante** faz três cópias síncronas dos dados na região primária para elevada disponibilidade e, em seguida, no modo assíncrono faz com que as três réplicas numa região emparelhada para recuperação após desastre.

-   **Armazenamento georredundante com acesso de leitura** é armazenamento georredundante e a capacidade para ler os dados na região secundária. Esta capacidade torna adequada recuperação de desastres parcial. Se existir um problema com a região primária, pode alterar a sua aplicação para ter acesso só de leitura para a região emparelhada.

### <a name="use-cases"></a>Casos de utilização 

Cada tipo de armazenamento tem um caso de utilização diferentes.

**Armazenamento de blobs** 

A palavra *blob* é um acrónimo para *binário objeto grande*. Os BLOBs são ficheiros não estruturados, como aqueles que armazena no seu computador. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos. Armazenamento de Blobs do Azure também contém discos de dados de máquinas virtuais do Azure.

Storage do Azure suporta três tipos de blobs:

-   **Blobs de blocos** são utilizados para armazenar comum ficheiros 195 GB (4 MB × 50 000 blocos) de tamanho. O principal motivo para blobs de blocos é o armazenamento de ficheiros que são lidos a partir do início ao final, tais como ficheiros de multimédia ou ficheiros de imagem para Web sites. Forem denominados de blobs de blocos porque é necessário carregar ficheiros maiores do que 64 MB como pequenos blocos. Estes blocos são, em seguida, consolidados (ou consolidados) para o final do blob.

-   **Blobs de páginas** são utilizados para armazenar ficheiros de acesso aleatório até 1 TB de tamanho. Os blobs de páginas são utilizados principalmente como o armazenamento de cópia de segurança para os VHDs que fornecem duráveis discos de máquinas virtuais do Azure, os IaaS computação serviço no Azure. Forem denominados de blobs de páginas porque fornecem acesso de leitura/escrita aleatórias às páginas de 512 bytes.

-   **Blobs de acréscimo** consistir em blocos, como os blobs de blocos, mas são otimizados para operações de acréscimo. Estes são frequentemente utilizados para obter informações de registo de uma ou mais origens para o mesmo blob. Por exemplo, poderá escrever todos os seu registo de rastreio para o blob de acréscimo mesmo para uma aplicação que está a executar várias VMs. Um blob de acréscimo única pode ser 195 GB.

Para obter mais informações, consulte [introdução ao Blob storage do Azure através do .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

**Armazenamento de ficheiros**

File storage do Azure é um serviço que oferece partilhas de ficheiros na nuvem utilizando o protocolo Server Message Block (SMB) padrão. O serviço suporta tanto o SMB 2.1 e o SMB 3.0. Com o File storage do Azure, pode migrar as aplicações que se baseiam em partilhas de ficheiros para o Azure rapidamente e sem reescritas dispendiosas. Aplicações em execução nas máquinas virtuais do Azure, nos serviços em nuvem ou no local os clientes podem montar uma partilha de ficheiros na nuvem. Isto é semelhante à forma como uma aplicação de ambiente de trabalho monta uma partilha SMB típica. Em seguida, quaisquer componentes da aplicação podem montar e aceder à partilha do File Storage em simultâneo.

Como uma partilha do File storage é uma partilha de ficheiros SMB padrão, as aplicações em execução no Azure podem aceder a dados na partilha através de APIs de e/s do sistema de ficheiros. Os programadores podem utilizar, por conseguinte, respetivo código existente e competências para migrar as aplicações existentes. Os profissionais de TI podem utilizar os cmdlets do PowerShell para criar, montar e gerir partilhas de File storage como parte da administração de aplicações do Azure.

Para obter mais informações, consulte [introdução ao File storage do Azure no Windows](../../storage/files/storage-how-to-use-files-windows.md) ou [como utilizar o File storage do Azure com o Linux](../../storage/files/storage-how-to-use-files-linux.md).

**Armazenamento de tabelas**

O Table Storage do Azure é um serviço que armazena dados NoSQL estruturados na nuvem. O Table storage é um arquivo de chaves/atributos com um design sem esquema. Porque o Table storage é sem esquema, é fácil adaptar os dados que as necessidades da sua aplicação evoluem. O acesso aos dados é rápido e rentável para todos os tipos de aplicações. Geralmente, o custo do Table Storage é significativamente inferior ao SQL tradicional para volumes de dados semelhantes.

Pode utilizar o Table Storage para armazenar conjuntos de dados flexíveis, tais como os dados do utilizador para Web Apps, livros de endereços, informações do dispositivo e qualquer outro tipo de metadados necessários para o seu serviço. Pode armazenar quantas entidades pretender numa tabela. Uma conta do storage pode conter quantas tabelas pretender, até ao limite de capacidade da conta de armazenamento.

Para obter mais informações, consulte [introdução ao Table storage do Azure](../../cosmos-db/table-storage-how-to-use-dotnet.md).

**Armazenamento de filas**

O armazenamento de Filas do Azure fornece um serviço de mensagens na nuvem entre componentes da aplicação. Ao conceber aplicações para o dimensionamento, os componentes da aplicação são muitas vezes, desacoplados para um dimensionamento independente. O Armazenamento de filas fornece um serviço de mensagens assíncrono para uma comunicação entre os componentes da aplicação, quer estejam a ser executados na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

Para obter mais informações, consulte [introdução ao armazenamento de filas do Azure](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Implementação de uma conta de armazenamento

Existem várias opções para a implementação de uma conta de armazenamento.

**Portal**

A implementação de uma conta de armazenamento utilizando o portal do Azure requer apenas uma subscrição do Azure Active Directory e o acesso a um web browser. Pode implementar uma nova conta de armazenamento para um grupo de recursos novo ou existente. Depois de criar a conta de armazenamento, pode criar uma partilha de ficheiro ou o contentor de BLOBs através do portal. Pode criar a tabela e fila entidades de armazenamento através de programação. Para obter mais informações, consulte [criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

Para além de implementar uma conta de armazenamento do portal do Azure, pode implementar um modelo Azure Resource Manager no portal. Isto irá implementar e configurar todos os recursos, tal como definido no modelo, incluindo as contas de armazenamento. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**PowerShell**

Implementar uma conta de armazenamento do Azure utilizando o PowerShell permite a automatização da implementação concluída da conta de armazenamento. Para obter mais informações, consulte [utilizar o Azure PowerShell com o Storage do Azure](../../storage/common/storage-powershell-guide-full.md).

Para além de implementar recursos do Azure individualmente, pode utilizar o módulo Azure PowerShell para implementar um modelo Azure Resource Manager. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Interface de linha de comandos (CLI)**

Tal como acontece com o módulo do PowerShell, a Interface de linha de comandos do Azure fornece a automatização da implementação e pode ser utilizado em sistemas Windows, OS X ou Linux. Pode utilizar a CLI do Azure **criar conta de armazenamento** comando para criar uma conta de armazenamento. Para obter mais informações, consulte [utilizando a CLI do Azure com o Storage do Azure.](../../storage/common/storage-azure-cli.md)

Da mesma forma, pode utilizar a CLI do Azure para implementar um modelo Azure Resource Manager. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e a CLI do Azure](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Acesso e a segurança para armazenamento do Azure

Armazenamento do Azure é acedido de várias formas, incluindo apesar do portal do Azure, durante a criação da VM e a operação e de bibliotecas de cliente de armazenamento. 

**Discos de máquinas virtuais**

Quando estiver a implementar uma máquina virtual, também terá de criar uma conta de armazenamento para armazenar o disco de sistema operativo da máquina virtual e quaisquer discos de dados adicionais. Pode selecionar uma conta de armazenamento existente ou crie um novo. Porque o tamanho máximo de um blob é 1.024 GB, um único disco da VM tem um tamanho máximo de 1,023 GB. Para configurar um disco de dados maior, pode apresentar vários discos de dados para a máquina virtual e agrupamento-los em conjunto como um único disco lógico. Para obter mais informações, consulte "Gerir discos do Azure" para [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) e [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

**Ferramentas de armazenamento**

As contas do storage do Azure podem ser acedidas através de vários exploradores de armazenamento diferente, por exemplo, o Visual Studio Cloud Explorer. Estas ferramentas permitem-lhe procurar através de contas de armazenamento e os dados. Para obter mais informações e uma lista de exploradores de armazenamento disponíveis, consulte [ferramentas de cliente do Storage do Azure](../../storage/common/storage-explorers.md).

**API do armazenamento**

Recursos de armazenamento podem ser acedida por qualquer idioma que consiga efetuar pedidos HTTP/HTTPS. Além disso, o Storage do Azure oferece bibliotecas de programação para vários idiomas populares. Estas bibliotecas simplificam o trabalho com o Storage do Azure ao processar detalhes como a invocação síncrona e assíncrona, a criação de batches de operações, gestão de exceções e tentativas automáticas. Para obter mais informações, consulte [referência de REST API do serviço de armazenamento do Azure](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

**Chaves de acesso de armazenamento**

Cada conta de armazenamento tem duas chaves de autenticação, um servidor principal e secundária. O valor pode ser utilizado para operações de acesso de armazenamento. Estas chaves de armazenamento são utilizados para ajudar a proteger uma conta de armazenamento e são necessárias para aceder a dados através de programação. Existem duas chaves para permitir ocasional rollover das chaves para melhorar a segurança. É fundamental para manter as chaves segura porque permite que os respetivos posse, juntamente com o nome da conta, acesso ilimitado a todos os dados na conta de armazenamento.

**Assinaturas de acesso partilhado**

Se precisar de permitir que os utilizadores tenham acesso aos seus recursos de armazenamento controlado, pode criar uma assinatura de acesso partilhado. Uma assinatura de acesso partilhado é um token que pode ser anexado a um URL que ativa o acesso delegado a um recurso de armazenamento. Qualquer pessoa que tiver o token pode aceder ao recurso que aponta para com as permissões que especifica, para o período de tempo que as TI 's válida. Para obter mais informações, consulte [utilizar assinaturas de acesso partilhado](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Rede Virtual do Azure


Redes virtuais são necessárias para suportar as comunicações entre máquinas virtuais. Pode definir o endereço IP personalizado, sub-redes, a filtragem de segurança e as definições de DNS e balanceamento de carga. Ao utilizar um gateway de VPN ou de um circuito do ExpressRoute, pode ligar redes virtuais do Azure para as suas redes no local.

### <a name="use-cases"></a>Casos de utilização

Existem casos de utilização diferentes para funcionamento em rede do Azure.

**Redes virtuais apenas na nuvem**

Uma rede virtual do Azure, por predefinição, apenas esteja acessível aos recursos armazenados no Azure. Recursos ligados à mesma rede virtual podem comunicar entre si. Pode associar as interfaces de rede de máquina virtual e um endereço IP público para tornar a máquina virtual acessível através da Internet balanceadores de carga. Pode ajudar a proteger o acesso aos recursos publicamente expostos através da utilização de um grupo de segurança de rede.

**Redes virtuais em vários locais**

Pode ligar uma rede no local a uma rede virtual do Azure utilizando o ExpressRoute ou de uma ligação de VPN de site para site. Nesta configuração, a rede virtual do Azure é essencialmente uma baseado na nuvem extensão da sua rede no local.

Porque a rede virtual do Azure está ligada à sua rede no local, em vários locais redes virtuais têm de utilizar uma parte exclusiva do espaço de endereços que utiliza a sua organização. Da mesma forma que diferentes localizações da empresa são atribuídas uma sub-rede IP específica, Azure torna-se noutra localização como expandir a sua rede.

###<a name="deploying-a-virtual-network"></a>Implementar uma rede virtual

Existem várias opções para implementar uma rede virtual.

**Portal**

A implementação de uma rede virtual do Azure utilizando o portal do Azure requer apenas uma subscrição do Azure Active Directory e o acesso a um web browser. Pode implementar uma nova rede virtual para um grupo de recursos novo ou existente. Quando estiver a criar uma nova máquina virtual a partir do portal, pode selecionar uma rede virtual existente ou crie um novo. Para obter mais informações, consulte [criar uma rede virtual com o portal do Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

Para além de implementar uma Azure virtual network do portal do Azure, pode implementar um modelo Azure Resource Manager no portal. Isto irá implementar e configurar todos os recursos, tal como definido no modelo, incluindo quaisquer recursos de rede virtual. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**PowerShell**

Implementar uma rede virtual do Azure utilizando o PowerShell permite a automatização da implementação concluída da conta de armazenamento. Para obter mais informações, consulte [criar uma rede virtual com o PowerShell](../../virtual-network/virtual-networks-create-vnet-arm-ps.md).

Para além de implementar recursos do Azure individualmente, pode utilizar o módulo Azure PowerShell para implementar um modelo Azure Resource Manager. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Interface de linha de comandos (CLI)**

Tal como acontece com o módulo do PowerShell, a interface de linha de comandos do Azure fornece a automatização da implementação e pode ser utilizada em sistemas Windows, OS X ou Linux. Pode utilizar a CLI do Azure **criar rede vnet** comando para criar uma rede virtual. Para obter mais informações, consulte [criar uma rede virtual, utilizando a CLI do Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md).

Da mesma forma, pode utilizar a CLI do Azure para implementar um modelo Azure Resource Manager. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-networks"></a>Acesso e segurança para as redes virtuais

Pode ajudar a proteger redes virtuais do Azure através da utilização de um grupo de segurança de rede. Os NSGs contêm uma lista de lista (ACL) de regras de controlo de acesso que permitem ou negam o tráfego de rede para as instâncias de VM numa rede virtual. Pode associar NSGs a sub-redes ou instâncias de VM individuais dentro dessa sub-rede. Quando associa um NSG uma sub-rede, as regras da ACL são aplicadas a todas as instâncias VM nessa sub-rede. Além disso, pode restringir mais o tráfego para uma VM individual ao associar um NSG diretamente com essa VM. Para obter mais informações, consulte [filtrar o tráfego de rede com grupos de segurança de rede](../../virtual-network/virtual-networks-nsg.md).

## <a name="next-steps"></a>Passos seguintes

- [Criar uma VM do Windows](/virtual-machines/windows/quick-create-portal.md)
- [Criar uma VM com Linux](../../virtual-machines/linux/quick-create-portal.md)
