---
title: "Padrões de aplicação do SQL Server em VMs | Microsoft Docs"
description: "Este artigo aborda os padrões de aplicação para o SQL Server em VMs do Azure. Fornece aos programadores e arquitetos de soluções uma base de aplicação boa arquitetura e estrutura."
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: jhubbard
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: ninarn
ms.openlocfilehash: ed2ff59ad33408bef70f332f8a93eb8679bf328f
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Padrões de Aplicação e Estratégias de Desenvolvimento para o SQL Server em Máquinas Virtuais do Azure
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Resumo:
Determinar o padrão de aplicação ou padrões a utilizar para os SQL Server-aplicações baseadas no Azure ambiente é uma decisão de design importantes e requer uma sólida compreensão sobre como cada componente de infraestrutura do Azure e SQL Server funcionam em conjunto. Com o SQL Server nos serviços de infraestrutura do Azure, pode facilmente migrar, manter e monitorizar as aplicações do SQL Server existentes incorporada no Windows Server máquinas virtuais no Azure.

O objetivo deste artigo é fornecer aos programadores e arquitetos de soluções uma base de arquitetura da aplicação boa e estrutura, o que pode seguir quando migrar as aplicações existentes para o Azure, bem como desenvolver novas aplicações no Azure.

Para cada padrão de aplicação, irá encontrar um cenário no local, a respetiva solução ativado para a nuvem e as recomendações técnicas relacionadas. Além disso, o artigo aborda as estratégias de desenvolvimento específicos do Azure para que pode conceber as suas aplicações corretamente. Devido a muitos padrões de aplicação possíveis, recomenda-se que arquitetos e programadores devem escolher o padrão mais adequado para as respetivas aplicações e utilizadores.

**Os contribuintes técnicos:** Luis Carlos Vargas Herring, Madhan Arumugam Ramakrishnan

**Revisores técnicos:** Corey Sanders Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introdução
Pode desenvolver muitos tipos de aplicações de camada de n, separando os componentes das camadas de aplicações diferentes em computadores diferentes, bem como em componentes separados. Por exemplo, pode colocar a aplicação de cliente e componentes de uma máquina, a camada web front-end e componentes de camada de acesso de dados na outra máquina e uma camada de base de dados de back-end na outra máquina as regras de negócio. Este tipo de structuring ajuda a isolar cada camada uns dos outros. Se mudar de onde vêm dados, não precisa de alterar a aplicação de cliente ou da web, mas apenas os componentes de camada de acesso dados.

Típica *n camadas* aplicação inclui a camada de apresentação, a camada de negócio e a camada de dados:

| Escalão | Descrição |
| --- | --- |
| **Apresentação** |O *camada de apresentação* (camada web, de camada de front-end) é a camada no qual os utilizadores interagem com uma aplicação. |
| **Empresas** |O *camada de negócios* (camada média) é a camada de camada de apresentação e a camada de dados utilizam para comunicar entre si e inclui a funcionalidade principal do sistema. |
| **Dados** |O *camada de dados* é basicamente o servidor que armazena dados de uma aplicação (por exemplo, um servidor com o SQL Server). |

Camadas de aplicação descrevem os agrupamentos lógicos da funcionalidade e os componentes de uma aplicação; enquanto as camadas descrevem a distribuição da funcionalidade e componentes física em servidores físicos separados, computadores, redes ou localizações remotas. As camadas de uma aplicação podem residir no mesmo computador físico (da mesma camada) ou podem ser distribuídas por computadores separados (n camadas) e os componentes em cada camada comunicam com os componentes de outras camadas através de interfaces bem definidas. Pode considerar a camada de termo como que faça referência a padrões de distribuição físico como duas camadas, três camadas e n camadas. A **padrão de aplicação de camada 2** contém dois escalões de aplicação: servidor de aplicações e o servidor de base de dados. Ocorre a comunicação direta entre o servidor de aplicações e o servidor de base de dados. O servidor de aplicação contém componentes da camada web e de camada de negócio. No **padrão de aplicação de 3 camadas**, existem três camadas da aplicação: servidor de aplicações, que contém a camada de lógica de negócio e/ou os componentes de acesso de dados de camada de negócio e o servidor de base de dados de servidores web. A comunicação entre o servidor web e o servidor de base de dados ocorre através do servidor de aplicações. Para obter informações detalhadas sobre as camadas da aplicação e camadas, consulte [guia de arquitetura de aplicações do Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Antes de começar a ler este artigo, deve ter conhecimento sobre os conceitos fundamentais do SQL Server e do Azure. Para informações, consulte [SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), [do SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) e [Azure.com](https://azure.microsoft.com/).

Este artigo descreve várias padrões de aplicação que podem ser adequados para as suas aplicações simples, bem como as aplicações da empresa altamente complexas. Antes de com detalhes sobre cada padrão, é recomendável que lhe deve familiarizar-se com os serviços de armazenamento de dados disponíveis no Azure, tal como [Storage do Azure](../../../storage/common/storage-introduction.md), [SQL Database do Azure](../../../sql-database/sql-database-technical-overview.md), e [SQL Servidor numa máquina Virtual do Azure](virtual-machines-windows-sql-server-iaas-overview.md). Para tornar as melhores decisões de design para as suas aplicações, compreenda quando deve utilizar o serviço de armazenamento de dados claramente.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Escolha do SQL Server numa máquina Virtual do Azure, quando:
* Terá de controlo no SQL Server e Windows. Por exemplo, isto pode incluir a versão do SQL Server, correções especiais, configuração de desempenho, etc.
* Precisa de uma compatibilidade total com o SQL Server no local e pretender mover as aplicações existentes para o Azure como-é.
* Pretende tirar partido das capacidades do ambiente do Azure, mas SQL Database do Azure não suporta todas as funcionalidades necessárias à aplicação. Isto pode incluir as seguintes áreas:
  
  * **Tamanho de base de dados**: o do momento este artigo foi atualizado, a base de dados SQL suporta uma base de dados de até 1 TB de dados. Se a aplicação necessita de mais de 1 TB de dados e não pretende implementar soluções de fragmentação personalizados, é recomendado que utilize o SQL Server uma Máquina Virtual no Azure. Para informações mais recentes, consulte [dimensionamento fora do Azure bases de dados SQL](https://msdn.microsoft.com/library/azure/dn495641.aspx) e [escalões de serviço de base de dados de SQL do Azure e níveis de desempenho](../../../sql-database/sql-database-service-tiers.md).
  * **Conformidade HIPAA**: cuidados de saúde clientes e os fabricantes independentes de Software (ISV) podem optar por utilizar [do SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) em vez de [SQL Database do Azure](../../../sql-database/sql-database-technical-overview.md) porque o SQL Server Servidor de uma Máquina Virtual no Azure é abrangido por empresas associar contrato (BAA HIPAA). Para obter informações sobre a compatibilidade, consulte [Microsoft Azure Trust Center: conformidade](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Funcionalidades de nível de instância**: neste momento, base de dados do SQL Server não suporta funcionalidades que em direto fora da base de dados (tais como servidores ligados, agente de tarefas, FileStream, Mediador de serviço, etc.). Para obter mais informações, consulte [diretrizes de base de dados do SQL do Azure e limitações](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>camada 1 (simples): única máquina virtual
Neste padrão de aplicação, pode implementar a aplicação do SQL Server e base de dados em máquina virtual autónoma no Azure. A mesma máquina virtual contém a aplicação de cliente/web, componentes comerciais, camada de acesso a dados e o servidor de base de dados. O código de acesso de dados, negócio e apresentação são logicamente separados, mas estão fisicamente localizados na máquina de uma servidor único. A maioria dos clientes começam com este padrão de aplicação e, em seguida, podem aumentar horizontalmente adicionando mais funções da web ou de máquinas virtuais para o seu sistema.

Neste padrão de aplicação é útil quando:

* Pretende efetuar uma migração simple para a plataforma do Azure para avaliar se a plataforma de atende a requisitos da sua aplicação ou não.
* Pretende manter todos os escalões de aplicação alojados na mesma máquina virtual no mesmo centro de dados do Azure para reduzir a latência entre camadas.
* Pretende aprovisionar o desenvolvimento e ambientes de teste para curtos períodos de tempo rapidamente.
* Pretende executar esforço testar para diferentes níveis de carga de trabalho, mas ao mesmo tempo que não pretende proprietários e manter as máquinas físicas muitos sempre.

O diagrama seguinte demonstra uma simples no local cenário e como pode implementar a sua solução de nuvem ativada numa única máquina virtual no Azure.

![padrão de aplicação de camada 1](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Implementar a camada de negócio (lógica de negócio e aceder a componentes de dados) na mesma camada física como a camada de apresentação pode maximizar o desempenho da aplicação, a menos que tem de utilizar uma camada separada devido a problemas de segurança ou escalabilidade.

Uma vez que este é um padrão muito comum para começar, poderão ser úteis seguinte artigo na migração para mover os dados para a VM do SQL Server: [migrar uma base de dados para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>(simples) de 3 camadas: várias máquinas virtuais
Neste padrão de aplicações, implementar uma aplicação de 3 camadas no Azure colocando a cada camada de aplicação numa máquina virtual diferente. Esta opção fornece um ambiente flexível para uma fácil cenários de vertical e horizontal. Quando uma máquina virtual contém a aplicação de cliente/web, outro aloja os componentes de negócio e outro aloja o servidor de base de dados.

Neste padrão de aplicação é útil quando:

* Pretende efetuar uma migração de aplicações de base de dados complexas para máquinas virtuais do Azure.
* Pretende camadas da aplicação diferente para ser alojada em regiões diferentes. Por exemplo, poderá ter partilhado bases de dados que são implementadas em várias regiões para efeitos de relatórios.
* Pretender mover as aplicações empresariais de plataformas no local virtualizado para máquinas virtuais do Azure. Para ver um debate detalhado sobre aplicações da empresa, consulte [o que é uma aplicação empresarial](https://msdn.microsoft.com/library/aa267045.aspx).
* Pretende aprovisionar o desenvolvimento e ambientes de teste para curtos períodos de tempo rapidamente.
* Pretende executar esforço testar para diferentes níveis de carga de trabalho, mas ao mesmo tempo que não pretende proprietários e manter as máquinas físicas muitos sempre.

O diagrama seguinte demonstra como pode colocar uma aplicação de 3 camadas simples no Azure colocando a cada camada de aplicação numa máquina virtual diferente.

![padrão de aplicação de 3 camadas](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

Neste padrão de aplicação, não há apenas uma máquina virtual (VM) em cada camada. Se tiver várias VMs no Azure, recomendamos que configure uma rede virtual. [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) cria um limite de segurança fidedigna e também permite que as VMs comunicar entre si através do endereço IP privado. Além disso, sempre Certifique-se de que todas as ligações de Internet apenas Ir para a camada de apresentação. Quando seguir este padrão de aplicação, gerir as regras de grupo de segurança de rede para controlar o acesso. Para obter mais informações, consulte [permitir o acesso externo à VM através do portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

No diagrama, os protocolos de Internet podem ser TCP, UDP, HTTP ou HTTPS.

> [!NOTE]
> Configurar uma rede virtual no Azure é gratuitamente. No entanto, são-lhe cobrados para o gateway VPN que liga no local. Este encargos baseia-se na quantidade de tempo de ligação aprovisionado e disponível.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>camada de 2 e 3 camadas com a camada de apresentação e escalável
Neste padrão de aplicações, implementar a aplicação de camada 2 ou 3 camadas da base de dados para o Azure Virtual Machines colocando a cada camada de aplicação numa máquina virtual diferente. Além disso, pode ampliar a camada de apresentação devido a volume maior de pedidos de cliente recebidos.

Neste padrão de aplicação é útil quando:

* Pretender mover as aplicações empresariais de plataformas no local virtualizado para máquinas virtuais do Azure.
* Que pretende ampliar a camada de apresentação devido a volume maior de pedidos de cliente recebidos.
* Pretende aprovisionar o desenvolvimento e ambientes de teste para curtos períodos de tempo rapidamente.
* Pretende executar esforço testar para diferentes níveis de carga de trabalho, mas ao mesmo tempo que não pretende proprietários e manter as máquinas físicas muitos sempre.
* Deve possuir um ambiente de infraestrutura que pode ser dimensionados cima e para baixo a pedido.

O diagrama seguinte demonstra como pode colocar os escalões de aplicação em várias máquinas virtuais no Azure ao aumentar horizontalmente a camada de apresentação devido a volume maior de pedidos de cliente recebidos. Como mostrado no diagrama, Balanceador de carga do Azure é responsável pela distribuição de tráfego em várias máquinas virtuais e também determinar qual o servidor web para ligar a. Ter várias instâncias de servidores web por trás de um balanceador de carga garante a disponibilidade elevada de camada de apresentação.

![Padrão de aplicação - de escalamento horizontal de camada de apresentação](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Melhores práticas para padrões de camada 2, 3 camadas ou n camadas com várias VMs numa camada
É recomendado que colocar as máquinas virtuais que pertencem à mesma camada no mesmo serviço de nuvem e na mesma a disponibilidade definido. Por exemplo, coloque um conjunto de servidores web na **CloudService1** e **AvailabilitySet1** e um conjunto de servidores de base de dados no **CloudService2** e  **AvailabilitySet2**. Um conjunto de disponibilidade no Azure permite-lhe colocar os nós de elevada disponibilidade em domínios de falhas separada e domínios de atualização.

Para tirar partido de várias instâncias VM de uma camada, terá de configurar o Azure Balanceador de carga entre camadas da aplicação. Para configurar o Balanceador de carga em cada escalão, crie um ponto final com balanceamento de carga em VMs cada camada separadamente. Para um escalão específico, crie VMs no mesmo serviço em nuvem. Isto garante que têm o mesmo endereço Virtual IP público. Em seguida, crie um ponto final das máquinas virtuais na camada. Em seguida, atribua o ponto final do mesmo a outras máquinas virtuais na camada para balanceamento de carga. Ao criar um conjunto com balanceamento de carga, distribuir o tráfego entre várias máquinas virtuais e também permitir que o Balanceador de carga determinar o nó que liguem quando ocorre uma falha de um nó VM de back-end. Por exemplo, a elevada disponibilidade da camada de apresentação garante ter múltiplas instâncias dos servidores web por trás de um balanceador de carga.

Como melhor prática, sempre Certifique-se de que todas as ligações de internet primeiro ir para a camada de apresentação. A camada de apresentação acede a camada de negócio e, em seguida, a camada de negócio acede a camada de dados. Para obter mais informações sobre como permitir o acesso à camada de apresentação, consulte [permitir o acesso externo à VM através do portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Tenha em atenção que o Balanceador de carga no Azure funciona semelhante ao carregar balanceadores num ambiente no local. Para obter mais informações, consulte [para serviços de infraestrutura do Azure de balanceamento de carga](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Além disso, recomendamos que configure uma rede privada para as máquinas virtuais utilizando a rede Virtual do Azure. Isto permite-lhes comunicar entre si através do endereço IP privado. Para obter mais informações, consulte [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>camada de 2 e 3 camadas com a camada de negócio e escalável
Neste padrão de aplicações, implementar a aplicação de camada 2 ou 3 camadas da base de dados para o Azure Virtual Machines colocando a cada camada de aplicação numa máquina virtual diferente. Além disso, pode querer distribuir os componentes de servidor de aplicações para várias máquinas virtuais devido à complexidade da sua aplicação.

Neste padrão de aplicação é útil quando:

* Pretender mover as aplicações empresariais de plataformas no local virtualizado para máquinas virtuais do Azure.
* Pretende distribuir os componentes de servidor de aplicações para várias máquinas virtuais devido à complexidade da sua aplicação.
* Pretender mover pesado de lógica de negócio aplicações no local LOB (de linha de negócio) para máquinas virtuais do Azure. Aplicações de LOB são um conjunto de aplicações de computador vitais para administrar uma empresa, como gestão de contas, recursos humanos (RH), pagamentos, gestão de cadeia de fornecimento e aplicações de planeamento de recursos.
* Pretende aprovisionar o desenvolvimento e ambientes de teste para curtos períodos de tempo rapidamente.
* Pretende executar esforço testar para diferentes níveis de carga de trabalho, mas ao mesmo tempo que não pretende proprietários e manter as máquinas físicas muitos sempre.
* Deve possuir um ambiente de infraestrutura que pode ser dimensionados cima e para baixo a pedido.

O diagrama seguinte demonstra um cenário no local e a sua solução de nuvem ativada. Neste cenário, é possível colocar as camadas em várias máquinas virtuais no Azure da aplicação por ampliar a camada de negócio, que contém a camada de lógica de negócio e os componentes de acesso de dados. Como mostrado no diagrama, Balanceador de carga do Azure é responsável pela distribuição de tráfego em várias máquinas virtuais e também determinar qual o servidor web para ligar a. Ter várias instâncias de servidores de aplicações por trás de um balanceador de carga garante a disponibilidade elevada de camada de negócio. Para obter mais informações, consulte [melhores práticas para padrões de aplicação de camada 2, 3 camadas ou n camadas com várias máquinas virtuais da camada de um](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Padrão de aplicação com o início de ampliação de camada de negócio](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>camada de 2 e 3 camadas com apresentação e escalável empresariais de camadas e HADR
Neste padrão de aplicações, implementar a aplicação de camada 2 ou 3 camadas da base de dados para máquinas virtuais do Azure por distribuição de camada de apresentação (servidor web) e os componentes de (servidor de aplicações) de camada de negócio para várias máquinas virtuais. Além disso, implementa as soluções de recuperação após desastre e elevada disponibilidade para as bases de dados em máquinas virtuais do Azure.

Neste padrão de aplicação é útil quando:

* Pretender mover as aplicações empresariais virtualizadas plataformas no local para o Azure através da implementação de elevada disponibilidade do SQL Server e funcionalidades de recuperação após desastre.
* Que pretende ampliar a camada de apresentação e a camada de negócio devido a volume maior de pedidos de cliente recebidos e a complexidade da sua aplicação.
* Pretende aprovisionar o desenvolvimento e ambientes de teste para curtos períodos de tempo rapidamente.
* Pretende executar esforço testar para diferentes níveis de carga de trabalho, mas ao mesmo tempo que não pretende proprietários e manter as máquinas físicas muitos sempre.
* Deve possuir um ambiente de infraestrutura que pode ser dimensionados cima e para baixo a pedido.

O diagrama seguinte demonstra um cenário no local e a sua solução de nuvem ativada. Neste cenário, pode ampliar a camada de apresentação e os componentes de camada de negócio de várias máquinas virtuais no Azure. Além disso, implementa técnicas elevadas de recuperação (HADR) de disponibilidade e após desastre para as bases de dados do SQL Server no Azure.

A executar várias cópias de uma aplicação em diferentes VMs Certifique-se de que é o balanceamento de carga pedidos nos mesmos. Quando tiver várias máquinas virtuais, tem de certificar-se de que todas as suas VMs estão acessíveis e em execução num ponto no tempo. Se configurar o balanceamento de carga, o Balanceador de carga do Azure controla o estado de funcionamento das VMs e direciona receber chamadas para os nós em bom estado de VM funcionar corretamente. Para obter informações sobre como configurar o balanceamento de carga das máquinas virtuais, consulte [para serviços de infraestrutura do Azure de balanceamento de carga](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ter várias instâncias de servidores web e da aplicação por trás de um balanceador de carga garante a elevada disponibilidade das camadas de apresentação e o negócio.

![Escalamento horizontal e a elevada disponibilidade](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Melhores práticas para padrões de aplicações que requerem HADR do SQL Server
Quando configurar a elevada disponibilidade do SQL Server e soluções de recuperação de desastre em Azure Virtual Machines, configurar uma rede virtual para as máquinas virtuais com [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) é obrigatório.  Máquinas virtuais dentro de uma rede Virtual tem um endereço IP privado estável, mesmo após um período de indisponibilidade do serviço, deste modo, pode evitar o tempo de atualização necessário para a resolução de nome DNS. Além disso, a rede virtual permite-lhe expandir a sua rede no local para o Azure e cria um limite de segurança fidedigna. Por exemplo, se a aplicação tem restrições de domínio empresarial (tal como, a autenticação do Windows, do Active Directory), configurar [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) é necessário.

A maioria dos clientes, que tenham o código de produção no Azure, é em manter réplicas primárias e secundárias no Azure.

Para obter informações completas e tutoriais sobre técnicas de recuperação de desastre e elevada disponibilidade, consulte [elevada disponibilidade e recuperação após desastre do SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>camada de 2 e 3 camadas utilizando as VMs do Azure e serviços em nuvem
Neste padrão de aplicação, implementar a aplicação de camada 2 ou 3 camadas no Azure utilizando ambos [Cloud Services do Azure](../../../cloud-services/cloud-services-choose-me.md#tellmecs) (funções web e de trabalho - plataforma como serviço (PaaS)) e [Virtual Machines do Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ( Infraestrutura como serviço (IaaS)). Utilizar [Cloud Services do Azure](https://azure.microsoft.com/documentation/services/cloud-services/) para a camada de negócio/camada de apresentação e o SQL Server no [Virtual Machines do Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para os dados camada é vantajoso para a maioria das aplicações em execução no Azure. O motivo é que ter uma instância de computação em execução nos serviços em nuvem fornece um facilita a gestão, a implementação, monitorização e escalável.

Com os serviços em nuvem, Azure mantém a infraestrutura para si, executa a manutenção de rotina, patches os sistemas operativos e tenta recuperar de falhas de hardware e do serviço. Quando a aplicação tem de escalamento horizontal, estão disponíveis para o projeto de serviço cloud automáticas e manuais opções de escalamento horizontal, aumente ou diminua o número de instâncias ou de máquinas virtuais que são utilizadas pela sua aplicação. Além disso, pode utilizar o local no Visual Studio para implementar a sua aplicação para um projeto de serviço em nuvem no Azure.

Em resumo, se não quiser possuir um vasto conjunto de camada de tarefas administrativas para as empresas/apresentação e a aplicação não necessita de qualquer configuração complexa de software ou o sistema operativo, utilize os Cloud Services do Azure. Se a base de dados do Azure SQL não suporta todas as funcionalidades que procura, utilize o SQL Server uma Máquina Virtual no Azure para a camada de dados. Executar uma aplicação nos serviços de nuvem do Azure e armazenar dados na Azure Virtual Machines combinam os benefícios de ambos os serviços. Para ver uma comparação detalhada, consulte a secção neste tópico sobre [comparar estratégias de desenvolvimento no Azure](#comparing-web-development-strategies-in-azure).

Neste padrão de aplicação, a camada de apresentação inclui uma função da web, que é um componente de serviços em nuvem que executem o ambiente de execução do Azure e personalizada para a programação de aplicação web como suportada pelo IIS e ASP.NET. A camada de negócio ou back-end inclui uma função de trabalho, o que é um componente de serviços em nuvem que executem o ambiente de execução do Azure e é útil para desenvolvimento generalizado e pode efetuar o processamento em segundo plano para uma função da web. A camada de base de dados reside numa máquina virtual do SQL Server no Azure. A comunicação entre a camada de apresentação e a camada de base de dados ocorre diretamente ou através de camada de negócio – componentes da função de trabalho.

Neste padrão de aplicação é útil quando:

* Pretender mover as aplicações empresariais virtualizadas plataformas no local para o Azure através da implementação de elevada disponibilidade do SQL Server e funcionalidades de recuperação após desastre.
* Deve possuir um ambiente de infraestrutura que pode ser dimensionados cima e para baixo a pedido.
* Base de dados SQL do Azure não suporta todas as funcionalidades que tem da aplicação ou a base de dados.
* Pretende executar esforço testar para diferentes níveis de carga de trabalho, mas ao mesmo tempo que não pretende proprietários e manter as máquinas físicas muitos sempre.

O diagrama seguinte demonstra um cenário no local e a sua solução de nuvem ativada. Neste cenário, coloque a camada de apresentação em funções da web, a camada de negócio nas funções de trabalho, mas a camada de dados em máquinas virtuais no Azure. A executar várias cópias da camada de apresentação em funções da web diferente garante para a carga de pedidos de equilíbrio entre eles. Ao combinar Cloud Services do Azure com o Azure Virtual Machines, recomendamos que configure [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) bem. Com [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), pode ter estáveis e persistentes endereços IP privados no mesmo serviço na nuvem da nuvem. Depois de definir uma rede virtual para as máquinas virtuais e serviços em nuvem, pode começar a comunicar entre si através do endereço IP privado. Além disso, ter máquinas virtuais e funções da web/trabalho do Azure no mesmo [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) proporciona baixa latência e conectividade mais segura. Para obter mais informações, consulte [o que é um serviço em nuvem](../../../cloud-services/cloud-services-choose-me.md).

Como mostrado no diagrama, o Balanceador de carga do Azure distribui o tráfego entre várias máquinas virtuais e também determina o servidor web ou o servidor de aplicação para ligar a. Ter várias instâncias dos servidores web e da aplicação por trás de um balanceador de carga garante a elevada disponibilidade da camada de apresentação e da camada de negócio. Para obter mais informações, consulte [de melhores práticas para a aplicação padrões que exigem que SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Padrões de aplicação com serviços em nuvem](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Outra abordagem para implementar este padrão de aplicação é utilizar uma função da web consolidado que contém a camada de apresentação e os componentes da camada de negócio conforme mostrado no diagrama seguinte. Neste padrão de aplicação é útil para aplicações que necessitam de estrutura com monitorização de estado. Uma vez que o Azure oferece nós de computação sem monitorização de estado funções web e de trabalho, recomendamos que implemente uma lógica para armazenar o estado da sessão através de uma das seguintes tecnologias: [colocação em cache do Azure](https://azure.microsoft.com/documentation/services/redis-cache/), [Table Storage do Azure](../../../cosmos-db/table-storage-how-to-use-dotnet.md) ou [base de dados SQL do Azure](../../../sql-database/sql-database-technical-overview.md).

![Padrões de aplicação com serviços em nuvem](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Padrão com as VMs do Azure, a base de dados SQL do Azure e o App Service do Azure (aplicações Web)
É o principal objetivo deste padrão de aplicação para lhe mostrar como combinar a infraestrutura do Azure como um componentes de serviço (IaaS) com os componentes de plataforma-como-um-serviço do Azure (PaaS) da sua solução. Este padrão está centrado na SQL Database do Azure para armazenamento de dados relacional. Não inclua do SQL Server numa máquina virtual do Azure, que faz parte da infraestrutura do Azure como uma oferta de serviço.

Neste padrão de aplicação, implementar uma aplicação de base de dados no Azure através da colocação de camadas de apresentação e empresariais na mesma máquina virtual e aceder a uma base de dados em servidores de base de dados de SQL do Azure (base de dados do SQL Server). Pode implementar a camada de apresentação utilizando soluções de web tradicional baseado no IIS. Em alternativa, pode implementar uma apresentação combinada e a camada de negócio através de [Web Apps do Azure](https://azure.microsoft.com/documentation/services/app-service/web/).

Neste padrão de aplicação é útil quando:

* Já tem um servidor de base de dados SQL existente configurado no Azure e pretende testar rapidamente a sua aplicação.
* Pretende testar as capacidades do ambiente do Azure.
* Pretende aprovisionar o desenvolvimento e ambientes de teste para curtos períodos de tempo rapidamente.
* Os componentes de acesso de lógica e os dados empresariais podem ser autónomo dentro de uma aplicação web.

O diagrama seguinte demonstra um cenário no local e a sua solução de nuvem ativada. Neste cenário, coloque as camadas de aplicação numa única máquina virtual nos dados do Azure e o acesso na SQL Database do Azure.

![Padrão de aplicação misto](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Se optar por implementar um combinado web e a camada da aplicação através da utilização de Web Apps do Azure, recomendamos que mantenha o escalão de camada média ou da aplicação como bibliotecas de ligação dinâmica (DLLs) no contexto de uma aplicação web.

Além disso, reveja as recomendações indicadas no [comparar estratégias de desenvolvimento de web no Azure](#comparing-web-development-strategies-in-azure) secção no final deste artigo para obter mais informações sobre técnicas de programação.

## <a name="n-tier-hybrid-application-pattern"></a>Padrão de aplicação híbrida N camadas
Padrão de aplicação híbrida n camadas, implementar a aplicação em vários escalões distribuída entre no local e o Azure. Por conseguinte, criar um sistema flexível e reutilizáveis híbrido, que pode modificar ou adicionar um escalão específico sem alterar os escalões de outros. Para expandir a sua rede empresarial para a nuvem, pode utilizar [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) serviço.

Neste padrão de aplicação híbrida é útil quando:

* Quer criar aplicações que são executadas parcialmente na nuvem e parcialmente no local.
* Pretende migrar alguns ou todos os elementos de uma aplicação no local existentes para a nuvem.
* Pretender mover as aplicações empresariais de plataformas no local virtualizado no Azure.
* Deve possuir um ambiente de infraestrutura que pode ser dimensionados cima e para baixo a pedido.
* Pretende aprovisionar o desenvolvimento e ambientes de teste para curtos períodos de tempo rapidamente.
* Pretende uma forma económica para fazer cópias de segurança para as aplicações de base de dados empresariais.

O diagrama seguinte demonstra um padrão de aplicação híbrida n camadas que abrange no local e o Azure. Como é mostrado no diagrama, no local infraestrutura inclui [serviços de domínio do Active Directory](https://technet.microsoft.com/library/hh831484.aspx) controlador de domínio para suportar a autenticação de utilizador e a autorização. Tenha em atenção que o diagrama demonstra um cenário, onde o algumas partes de camada de dados em direto num centro de dados no local, enquanto algumas partes de camada de dados em direto no Azure. Consoante as necessidades da sua aplicação, pode implementar vários outros cenários híbridos. Por exemplo, poderá manter a camada de apresentação e a camada de negócio no ambiente no local, mas a camada de dados no Azure.

![Padrão de aplicação de camada de N](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

No Azure, pode utilizar o Active Directory como um diretório do autónomo em nuvem para a sua organização ou também pode integrar existente do Active Directory no local com [do Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Como mostrado no diagrama, os componentes da camada de negócio podem aceder a várias origens de dados, tal como para [do SQL Server no Azure](virtual-machines-windows-sql-server-iaas-overview.md) através de um endereço IP interno privado, no local do SQL Server através de [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), ou [base de dados SQL](../../../sql-database/sql-database-technical-overview.md) utilizando as tecnologias de fornecedor de dados .NET Framework. Neste diagrama, SQL Database do Azure é um serviço de armazenamento de dados opcionais.

Padrão de aplicação híbrida n camadas, pode implementar o fluxo de trabalho seguinte na ordem especificada:

1. Identificar aplicações de base de dados da empresa que precisam de ser movidas para cima na nuvem utilizando o [Microsoft Assessment e planeamento (MAP) Toolkit](http://microsoft.com/map). O MAP Toolkit recolhe dados de inventário e de desempenho de computadores que se estiver a considerar para virtualização e fornece recomendações sobre a capacidade e o planeamento de avaliação.
2. Planear os recursos e a configuração necessária na plataforma do Azure, tais como contas de armazenamento e máquinas virtuais.
3. Configurar a conectividade de rede entre a rede empresarial no local e [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Para configurar a ligação entre a rede empresarial no local e uma máquina virtual no Azure, utilize um dos seguintes dois métodos:
   
   1. Estabelece uma ligação entre no local e o Azure através de pontos finais públicos numa máquina virtual no Azure. Este método fornece uma configuração mais fácil e permite-lhe utilizar autenticação do SQL Server na sua máquina virtual. Além disso, configure as regras de grupo de segurança de rede para controlar o tráfego público à VM. Para obter mais informações, consulte [permitir o acesso externo à VM através do portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Estabelece uma ligação entre no local e o Azure através do Azure Virtual privada túnel de rede (VPN). Este método permite-lhe expandir as políticas de domínio a uma máquina virtual no Azure. Além disso, pode configurar regras de firewall e utilizar a autenticação do Windows na sua máquina virtual. Atualmente, o Azure suporta ligações de VPN de ponto a site e VPN site a site segura:
      
      * Com uma ligação site a site segura, pode estabelecer conectividade de rede entre a sua rede no local e a rede virtual no Azure. Recomenda-se para ligar o seu ambiente de centro de dados no local ao Azure.
      * Com uma ligação ponto a site segura, pode estabelecer conectividade de rede entre a rede virtual no Azure e os computadores individuais em execução em qualquer lugar. Recomenda-se principalmente para fins de desenvolvimento e teste.
      
      Para obter informações sobre como ligar ao SQL Server no Azure, consulte [ligar a um SQL Server Máquina Virtual no Azure](virtual-machines-windows-sql-connect.md).
4. Configure tarefas agendadas e alertas de cópia de segurança de dados no local, um disco de máquina virtual no Azure. Para obter mais informações, consulte [cópia de segurança do SQL Server e de restauro com o serviço de armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/jj919148.aspx) e [cópia de segurança e restaurar para o SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).
5. Consoante as necessidades da sua aplicação, pode implementar um dos três seguintes cenários comuns:
   
   1. Pode manter o servidor web, servidor de aplicações e dados sensível e minúsculas num servidor de base de dados no Azure, ao passo que mantém o dados confidenciais no local.
   2. Pode manter o seu servidor web e servidor de aplicações no local, enquanto o servidor de base de dados numa máquina virtual no Azure.
   3. Pode manter o servidor de base de dados, o servidor web e servidor de aplicações no local, enquanto a manter as réplicas de base de dados em máquinas virtuais no Azure. Esta definição permite que os servidores de web no local ou aplicações de relatórios para aceder as réplicas de base de dados no Azure. Por conseguinte, pode conseguir para reduzir a carga de trabalho numa base de dados no local. Recomendamos que implementar neste cenário para pesado cargas de trabalho e fins developmental de leitura. Para informações sobre a criação de réplicas de base de dados no Azure, consulte os grupos de Disponibilidade AlwaysOn no [elevada disponibilidade e recuperação após desastre do SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Comparar as estratégias de desenvolvimento de web no Azure
Para implementar e implementar uma multicamadas aplicações baseados no SQL Server no Azure, pode utilizar um dos seguintes dois métodos programação:

* Configure um servidor de tradicional web (IIS - serviços de informação Internet) no Azure e acesso bases de dados no SQL Server em Azure Virtual Machines.
* Implementar e implementar um serviço em nuvem no Azure. Em seguida, certifique-se de que este serviço em nuvem pode aceder a bases de dados no SQL Server em máquinas virtuais do Azure. Um serviço em nuvem pode incluir várias funções web e de trabalho.

A tabela seguinte fornece uma comparação de desenvolvimento de web tradicional com Cloud Services do Azure e Web Apps no que respeita do SQL Server em Azure Virtual Machines do Azure. A tabela inclui Web Apps do Azure, é possível utilizar o SQL Server na VM do Azure como uma origem de dados para Web Apps do Azure através do respetivo endereço IP virtual público ou o nome DNS.

|  | Desenvolvimento de web tradicional em Azure Virtual Machines | Serviços de nuvem no Azure | Web aloja com aplicações Web do Azure |
| --- | --- | --- | --- |
| **Aplicação de migração no local** |As aplicações existentes como-é. |As aplicações precisam de funções web e de trabalho. |As aplicações existentes como-é mas adequada para as aplicações web autónomo e serviços web que necessitam de escalabilidade rápida. |
| **Desenvolvimento e implementação** |Visual Studio, o WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, Gestor de IIS, o PowerShell. |Visual Studio, o SDK do Azure, TFS, PowerShell. Cada serviço em nuvem tem dois ambientes nos quais pode implementar o pacote de serviço e configuração: teste e produção. Pode implementar um serviço em nuvem para o ambiente de teste, testá-lo antes de promover para produção. |Visual Studio, o WebMatrix, Programador de Visual Web, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web implementar, do PowerShell. |
| **Administração e a configuração** |É responsável por tarefas administrativas na aplicação, dados, as regras de firewall, rede virtual e sistema operativo. |É responsável por tarefas administrativas da aplicação, dados, as regras de firewall e da rede virtual. |É responsável por tarefas administrativas na aplicação e apenas dados. |
| **Elevada disponibilidade e recuperação após desastre (HADR)** |Recomendamos que coloque máquinas virtuais no mesmo conjunto de disponibilidade e o mesmo serviço em nuvem. Manter as suas VMs no mesmo conjunto de disponibilidade permite ao Azure colocar os nós de elevada disponibilidade em domínios de falhas separada e domínios de atualização. Da mesma forma, manter as suas VMs no mesmo serviço em nuvem permite o balanceamento de carga e VMs podem comunicar diretamente entre si através da rede local dentro de um centro de dados do Azure.<br/><br/>É responsável por implementar uma elevada disponibilidade e a solução de recuperação após desastre para o SQL Server em Virtual Machines do Azure para evitar qualquer período de inatividade. Para tecnologias HADR suportadas, consulte [elevada disponibilidade e recuperação após desastre do SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>É responsável pela cópia de segurança os seus próprios dados e de aplicações.<br/><br/>Azure pode mover as máquinas virtuais se o computador anfitrião no Centro de dados falhar devido a problemas de hardware. Além disso, pode haver período de indisponibilidade planeado da sua VM quando a máquina do anfitrião é atualizada para o software de segurança ou atualizações. Por conseguinte, recomendamos que mantenha, pelo menos, duas VMs em cada camada de aplicação para garantir a disponibilidade contínua. Azure fornecem SLA para uma única máquina virtual. Para obter mais informações, consulte [orientações técnica do Azure resiliência](../../../resiliency/resiliency-technical-guidance.md). |Azure gere as falhas resultantes do software de hardware ou de sistema operativo subjacente. Recomendamos que implementar várias instâncias de uma função web ou de trabalho para garantir a elevada disponibilidade da aplicação. Para informações, consulte [serviços em nuvem, as máquinas virtuais e o contrato de nível de serviço de rede Virtual](http://www.microsoft.com/download/details.aspx?id=38427) e [recuperação após desastre e elevada disponibilidade para aplicações do Azure](../../../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)<br/><br/>É responsável pela cópia de segurança os seus próprios dados e de aplicações.<br/><br/>Para bases de dados que residem numa base de dados do SQL Server numa VM do Azure, é responsável por implementar uma solução de recuperação de disponibilidade e após desastre elevada para evitar qualquer período de inatividade. Para tecnologias HDAR suportadas, consulte elevada disponibilidade e recuperação após desastre para o SQL Server em Azure Virtual Machines.<br/><br/>**Espelhamento de base de dados do SQL Server**: utilização com serviços em nuvem do Azure (web/funções de trabalho). VMs de SQL Server e um projeto de serviço em nuvem podem estar na mesma rede Virtual do Azure. Se a VM do SQL Server não está a ser a mesma rede Virtual, terá de criar um Alias de servidor do SQL Server para encaminhar comunicação para a instância do SQL Server. Além disso, o nome de alias deve corresponder ao nome do SQL Server. |Elevada disponibilidade é herdada a partir de funções de trabalho do Azure, do armazenamento de Blobs do Azure e SQL Database do Azure. Por exemplo, o Storage do Azure mantém três réplicas de todos os blob, tabela e fila dados. Num dado momento, SQL Database do Azure mantém três réplicas dos dados em execução – uma réplica primária e duas réplicas secundárias. Para obter mais informações, consulte [Storage do Azure](https://azure.microsoft.com/documentation/services/storage/) e [SQL Database do Azure](../../../sql-database/sql-database-technical-overview.md).<br/><br/>Quando utilizar o SQL Server na VM do Azure como uma origem de dados para Web Apps do Azure, tenha em atenção que o Web Apps do Azure não suporta a rede Virtual do Azure. Por outras palavras, todas as ligações da Web Apps do Azure para VMs de SQL Server no Azure tem de passar por pontos finais públicos de máquinas virtuais. Isto poderá fazer com que algumas limitações para elevada disponibilidade e cenários de recuperação após desastre. Por exemplo, a aplicação de cliente em ligar à VM do SQL Server com o espelhamento da base de dados de Web Apps do Azure não seria capaz de ligar para o novo servidor primário como a base de dados requer que configure a Azure Virtual Network entre as VMs de anfitrião do SQL Server no Azure. Por conseguinte, utilizando **espelhamento da base de dados do SQL Server** com Web Apps do Azure não é suportada atualmente.<br/><br/>**Grupos de Disponibilidade AlwaysOn do SQL Server**: pode configurar grupos de Disponibilidade AlwaysOn ao utilizar Web Apps do Azure com VMs de SQL Server no Azure. Mas, terá de configurar a escuta do grupo de Disponibilidade AlwaysOn para encaminhar a comunicação para a réplica primária através de pontos finais com balanceamento de carga públicos. |
| **Conectividade em vários locais** |Pode utilizar a rede Virtual do Azure para ligar no local. |Pode utilizar a rede Virtual do Azure para ligar no local. |Rede Virtual do Azure é suportada. Para obter mais informações, consulte [integração de rede Virtual do Web Apps](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Escalabilidade** |Dimensionamento está disponível por aumentar os tamanhos de máquina virtual ou adicionar mais discos. Para obter mais informações acerca dos tamanhos da máquina virtual, consulte [tamanhos de Máquina Virtual do Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Para o servidor de base de dados**: Escalamento horizontal está disponível através de técnicas de criação de partições de base de dados e de grupos de Disponibilidade AlwaysOn do SQL Server.<br/><br/>Para cargas de trabalho leitura pesadas, pode utilizar [grupos de Disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) em vários nós secundários, bem como a replicação do SQL Server.<br/><br/>Para cargas de trabalho de cargas pesadas de escrita, pode implementar a data de criação de partições horizontal em vários servidores físicos para fornecer aplicações Escalamento horizontal.<br/><br/>Além disso, pode implementar um Escalamento horizontal utilizando [do SQL Server com o encaminhamento de dados dependentes](https://technet.microsoft.com/library/cc966448.aspx). Com dados dependentes encaminhamento (DDR), tem de implementar o mecanismo de criação de partições da aplicação de cliente, normalmente na camada de camada de negócio, para encaminhar os pedidos de base de dados para vários nós do SQL Server. A camada de negócio contém os mapeamentos para o modo como os dados estão particionados e nó que contém os dados.<br/><br/>Pode dimensionar as aplicações que são máquinas virtuais em execução. Para obter mais informações, consulte [como dimensionar uma aplicação](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Nota importante**: O **dimensionamento automático** funcionalidade do Azure permite-lhe automaticamente aumentar ou diminuir as máquinas virtuais que são utilizados pela sua aplicação. Esta funcionalidade garante que a experiência de utilizador final não é afetada negativamente durante períodos de pico e VMs são encerradas quando a procura é baixa. Recomenda-se que se não tiver definido a opção de dimensionamento automático do serviço em nuvem se inclui VMs de SQL Server. O motivo é que a funcionalidade de dimensionamento automático permite do Azure para ligar uma máquina virtual quando a utilização da CPU nessa VM for superior ao limiar de alguns e, para desativar uma máquina virtual quando a utilização da CPU for inferior a-lo. A funcionalidade de dimensionamento automático é útil para as aplicações sem estado, tais como servidores web, onde qualquer VM pode gerir a carga de trabalho sem quaisquer referências para qualquer estado anterior. No entanto, a funcionalidade de dimensionamento automático não é útil para aplicações com monitorização de estado, como o SQL Server, onde apenas uma instância permite a escrita na base de dados. |Dimensionamento está disponível através da utilização de várias funções web e de trabalho. Para obter mais informações acerca dos tamanhos da máquina virtual para funções da web e funções de trabalho, consulte [configurar tamanhos para serviços em nuvem](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Quando utilizar **serviços em nuvem**, pode definir várias funções de distribuir o processamento e também obter um dimensionamento flexível da sua aplicação. Cada serviço em nuvem inclui um ou mais funções da web e/ou as funções de trabalho, cada um com os seus próprios ficheiros de aplicação e a configuração. Pode vertical um serviço em nuvem aumento do número de instâncias de função (máquinas virtuais) implementado para uma função e escala pendente um serviço em nuvem ao reduzir o número de instâncias de função. Para obter informações detalhadas, consulte [modelos do Azure execução](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Escalamento horizontal está disponível através do suporte de elevada disponibilidade incorporada do Azure através de [serviços em nuvem, as máquinas virtuais e o contrato de nível de serviço de rede Virtual](http://www.microsoft.com/download/details.aspx?id=38427) e Balanceador de carga.<br/><br/>Para uma aplicação multicamada, recomendamos que se ligar a aplicação de funções da web/trabalho servidor VMs através de rede Virtual do Azure na base de dados. Além disso, o Azure oferece balanceamento de carga para as VMs no mesmo serviço de nuvem, propagando-se os pedidos de utilizador em-los. Máquinas virtuais ligadas desta forma pode comunicar diretamente entre si através da rede local dentro de um centro de dados do Azure.<br/><br/>Pode configurar **dimensionamento automático** no portal do Azure, bem como os tempos de agenda. Para obter mais informações, consulte [como configurar automática dimensionamento para um serviço em nuvem no portal do](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Aumentar vertical ou**: pode aumentar/diminuir o tamanho da instância (VM) reservado para o web site.<br/><br/>Aumentar horizontalmente: pode adicionar instâncias mais reservadas (VMs) para o web site.<br/><br/>Pode configurar **dimensionamento automático** no portal, bem como os tempos de agenda. Para obter mais informações, consulte [como as Web Apps do dimensionamento](../../../app-service/web-sites-scale.md). |

Para obter mais informações sobre como escolher entre estes métodos de programação, consulte [Web Apps do Azure, Cloud Services e as VMs: quando utilizar que](../../../app-service/choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre a execução do SQL Server em máquinas virtuais do Azure, consulte [do SQL Server na descrição geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

