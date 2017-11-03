---
title: "Replicar uma aplicação do SharePoint de várias camadas utilizando o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como replicar uma aplicação do SharePoint de várias camadas utilizando as capacidades do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: sutalasi
ms.openlocfilehash: 55323df68715c80d5e8535199cd739921a3baad9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Replicar uma aplicação do SharePoint de várias camadas para a recuperação de desastre utilizando o Azure Site Recovery

Este artigo descreve em detalhe como proteger uma aplicação do SharePoint utilizar [do Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Descrição geral

Microsoft SharePoint é uma aplicação de elevado desempenho que pode ajudar a um grupo ou o departamento de organizar, colaborar e partilhar informações. SharePoint pode fornecer portais de intranet, gestão de ficheiros e documentos, colaboração, redes sociais, extranets, Web sites, pesquisa de enterprise e business intelligence. Tem também a integração de sistema, integração de processo e capacidades de automatização do fluxo de trabalho. Normalmente, as organizações consideram como uma aplicação de camada 1 sensível à perda de dados e o período de indisponibilidade.

Hoje em dia, Microsoft SharePoint não fornece quaisquer funcionalidades de recuperação de desastre out of box. Independentemente do tipo e dimensionamento de um desastre, a recuperação envolve a utilização de um centro de dados espera que pode recuperar o farm. Os centros de dados em modo de espera são necessários para cenários em que sistemas redundantes locais e as cópias de segurança não é possível recuperar a interrupção no Centro de dados principal.

Uma solução de recuperação após desastre boa deve permitir a modelação de planos de recuperação em torno de arquiteturas de aplicações complexas, tais como o SharePoint. Também deve ter a capacidade de adicionar passos personalizados para processar os mapeamentos de aplicação entre várias camadas e, por conseguinte, fornecendo uma ativação pós-falha de clique único com um RTO inferior em caso de desastre.

Este artigo descreve em detalhe como proteger uma aplicação do SharePoint utilizar [do Azure Site Recovery](site-recovery-overview.md). Este artigo abordará as melhores práticas para replicar uma três camada de aplicação do SharePoint para o Azure, como pode fazer um exercício de recuperação após desastre e como pode de ativação pós-falha, a aplicação no Azure.

Pode ver as vídeo sobre como recuperar uma aplicação de várias camadas para o Azure abaixo.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende o seguinte:

1. [Uma máquina virtual a replicar para o Azure](site-recovery-vmware-to-azure.md)
2. Como [estruturar uma rede de recuperação](site-recovery-network-design.md)
3. [Efetuar uma ativação pós-falha de teste para o Azure](site-recovery-test-failover-to-azure.md)
4. [Efetuar uma ativação pós-falha para o Azure](site-recovery-failover.md)
5. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
6. Como [replicar do SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Arquitetura do SharePoint

SharePoint pode ser implementado num ou mais servidores utilizando topologias em camadas e funções de servidor para implementar uma estrutura do farm que cumpre os objetivos e objetivos específicos. Um típico grande, a pedido alta server farm do SharePoint que suporta um número elevado de utilizadores em simultâneo e um grande número de itens de conteúdo utilizar agrupamento do serviço como parte da sua estratégia de escalabilidade. Esta abordagem envolve a executar os serviços em servidores dedicados, agrupamento estes serviços em conjunto e, em seguida, aumentar horizontalmente os servidores como um grupo. A topologia seguinte ilustra o serviço e o servidor de agrupamento para uma camada de três farm de servidores do SharePoint. Consulte a documentação do SharePoint e arquiteturas de linha de produto para obter orientações detalhadas sobre diferentes topologias do SharePoint. Pode encontrar mais detalhes sobre a implementação do SharePoint 2013 no [neste documento](https://technet.microsoft.com/en-us/library/cc303422.aspx).



![Implementação padrão 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Suporte de recuperação de site

Para criar este artigo, máquinas virtuais VMware com o Windows Server 2012 R2 Enterprise foram utilizadas. SharePoint 2013 Enterprise edition e a edição Enterprise do SQL server 2014 foram utilizados. Como a replicação do Site Recovery desconhece de aplicação, as recomendações fornecidas aqui espera espere para cenários seguintes bem.

### <a name="source-and-target"></a>A origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim

### <a name="sharepoint-versions"></a>Versões do SharePoint
São suportadas as seguintes versões de servidor do SharePoint.

* Servidor do SharePoint 2013 padrão
* SharePoint server 2013 Enterprise
* Servidor do SharePoint 2016 padrão
* SharePoint server 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>Coisas a lembrar

Se estiver a utilizar um cluster partilhado com base em disco como qualquer camada na sua aplicação, em seguida, não poderá utilizar replicação de recuperação de sites para replicar as máquinas virtuais. Pode utilizar replicação nativa fornecida pela aplicação e, em seguida, utilizar um [plano de recuperação](site-recovery-create-recovery-plans.md) a ativação pós-falha todas as camadas.

## <a name="replicating-virtual-machines"></a>Replicar máquinas virtuais

Siga [esta orientação](site-recovery-vmware-to-azure.md) iniciar a replicação da máquina virtual para o Azure.

* Assim que a replicação estiver concluída, certifique-se de que vá para cada máquina virtual de cada camada e selecione o mesma conjunto de disponibilidade no ' item replicados > Definições > propriedades > computação e rede '. Por exemplo, se a camada web tiver 3 VMs, certifique-se a todos os 3 VMs estão configuradas para fazer parte do mesma conjunto de disponibilidade no Azure.

    ![Conjunto de disponibilidade de conjunto](./media/site-recovery-sharepoint/select-av-set.png)

* Para obter orientações sobre a proteger o Active Directory e DNS, consulte [proteger o Active Directory e DNS](site-recovery-active-directory.md) documento.

* Para obter orientações sobre a proteção de camada de base de dados em execução no SQL server, consulte [proteger o SQL Server](site-recovery-active-directory.md) documento.

## <a name="networking-configuration"></a>Configuração de redes

### <a name="network-properties"></a>Propriedades da rede

* Para a aplicação e as VMs de camada Web, configure as definições de rede no portal do Azure para que as VMs obterem ligadas à rede de DR direita após a ativação pós-falha.

    ![Selecione a rede](./media/site-recovery-sharepoint/select-network.png)


* Se estiver a utilizar um IP estático, em seguida, especifique o IP que pretende que a máquina virtual para efetuar para a **IP de destino** campo

    ![Definir o IP estático](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS e o encaminhamento de tráfego

Para a internet com sites, [criar um perfil do Traffic Manager do tipo 'Priority'](../traffic-manager/traffic-manager-create-profile.md) na subscrição do Azure. E, em seguida, configure o seu perfil do Traffic Manager e o DNS da seguinte forma.


| **Onde** | **Origem** | **Destino**|
| --- | --- | --- |
| DNS público | DNS público para sites do SharePoint <br/><br/> Ex: sharepoint.contoso.com | Gestor de Tráfego <br/><br/> contososharepoint.trafficmanager.NET |
| DNS no local | sharepointonprem.contoso.com | IP público no farm de no local |


No perfil do Gestor de tráfego, [criar os pontos finais primário e de recuperação](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Utilize o ponto final externo para o ponto final no local e o IP público para o ponto final do Azure. Certifique-se de que a prioridade é definida superior para o ponto final no local.

Aloje uma página de teste numa porta específica (por exemplo, 800) na camada web SharePoint por ordem para o Gestor de tráfego para detetar automaticamente a ativação pós-falha de post de disponibilidade. Esta é uma solução no caso de não é possível ativar a autenticação anónima em qualquer um dos seus sites do SharePoint.

[Configurar o perfil do Traffic Manager](../traffic-manager/traffic-manager-configure-priority-routing-method.md) com o abaixo as definições.

* Método de encaminhamento - 'Priority'
* Tempo DNS TTL (TTL) - ' 30 segundos'
* Definições de monitorização do ponto final - se de que pode ativar a autenticação anónima, pode dar um ponto final do Web site específico. Em alternativa, pode utilizar uma página de teste numa porta específica (por exemplo, 800).

## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação

Um plano de recuperação permite a ativação pós-falha de várias camadas numa aplicação de várias camada, por conseguinte, manter a consistência da aplicação da sequência. Siga o procedimento abaixo ao criar um plano de recuperação para uma aplicação web de várias camadas. [Saiba mais sobre como criar um plano de recuperação](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>A adicionar máquinas virtuais para grupos de ativação pós-falha

1. Crie um plano de recuperação ao adicionar a aplicação e as VMs de camada Web.
2. Clique em Personalizar para agrupar as VMs. Por predefinição, todas as VMs fazem parte do 'Grupo 1'.

    ![Personalizar RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Criar outro grupo (grupo 2) e mova a camada Web VMs para o grupo de novo. O escalão de aplicação VMs deve fazer parte do 'Grupo 1' e a camada Web VMs deve fazer parte do 'Grupo 2'. Isto é, para garantir que o arranque de VMs de camada de aplicação de cópia de segurança, seguido pela primeira vez camada Web VMs.


### <a name="adding-scripts-to-the-recovery-plan"></a>A adição de scripts para o plano de recuperação

Pode implementar os scripts de Azure Site Recovery frequentemente utilizados na sua conta de automatização clicando no botão 'Implementar no Azure' abaixo. Quando estiver a utilizar qualquer script publicado, certifique-se de que siga as orientações no script.

[![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione um script de pré-ação para 'Grupo 1' ao grupo de disponibilidade de SQL de ativação pós-falha. Utilize o script de 'ASR-SQL-FailoverAG' publicado nos scripts de exemplo. Certifique-se de que siga as orientações no script e efetue as alterações necessárias no script adequadamente.

    ![Adicionar-AG-Script-passo-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Adicionar-AG-Script-passo-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Adicionar um script de ação de post para anexar um balanceador de carga a falha na ativação pós-falha de máquinas virtuais da camada Web (grupo 2). Utilize o script de 'ASR AddSingleLoadBalancer' publicado nos scripts de exemplo. Certifique-se de que siga as orientações no script e efetue as alterações necessárias no script adequadamente.

    ![Adicionar-LB-Script-passo-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Adicionar-LB-Script-passo-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Adicione um passo manual para atualizar os registos DNS para apontar para o novo farm no Azure.

    * Para a internet com sites, não existem atualizações DNS são ativação pós-falha de post necessária. Siga os passos descritos na secção 'Funcionamento em rede orientações' para configurar o Gestor de tráfego. Se o perfil do Gestor de tráfego tenha sido configurado, tal como descrito na secção anterior, adicione um script para abrir a porta fictício (800 no exemplo) na VM do Azure.

    * Para os sites com acesso internos, adicione um passo manual para atualizar o registo DNS para apontar para o IP de Balanceador de carga novo Web camada da VM.

4. Adicione um passo manual para restaurar a aplicação de pesquisa de uma cópia de segurança ou de iniciar um novo serviço de pesquisa.

5. Para restaurar a aplicação de serviço de pesquisa de uma cópia de segurança, siga os passos abaixo.

    * Este método parte do princípio de que uma cópia de segurança da aplicação de serviço de pesquisa foi efetuada antes do evento catastrófica e que a cópia de segurança está disponível no site de DR.
    * Isto pode facilmente alcançado agendar a cópia de segurança (por exemplo, uma vez diariamente) e utilizando um procedimento de cópia para colocar a cópia de segurança no site de DR. Procedimentos de cópia pode incluir programas de script como AzCopy (cópia do Azure) ou como configurar o DFSR (Serviços de replicação de ficheiros distribuído).
    * Agora que o farm do SharePoint estiver em execução, navegue até a Administração Central, 'Cópia de segurança e restaurar' e selecione restaurar. O restauro interrogates a localização de cópia de segurança especificada (poderá ter de atualizar o valor). Selecione a cópia de segurança de aplicação de serviço de pesquisa que pretende restaurar.
    * Pesquisa é restaurada. Tenha em atenção que o restauro espera encontrar o mesmo topologia (mesmo número de servidores) e as mesmas rígido letras de unidade atribuídas a esses servidores. Para obter mais informações, consulte ['Aplicação de serviço de pesquisa de restauro no SharePoint 2013'](https://technet.microsoft.com/library/ee748654.aspx) documento.


6. Para começar com uma nova aplicação de serviço de pesquisa, siga os passos abaixo.

    * Este método parte do princípio de que está disponível uma cópia de segurança da base de dados "Administração de pesquisa" no site de DR.
    * Uma vez que outra aplicação de serviço de pesquisa bases de dados não são replicadas, têm de ser recriadas. Para tal, navegue para a Administração Central e eliminar a aplicação de serviço de pesquisa. Em todos os servidores que alojam o índice de pesquisa, elimine os ficheiros de índice.
    * Voltar a criar a aplicação de serviço de pesquisa e isto recria as bases de dados. É recomendado ter um script preparado que cria novamente esta aplicação de serviço, uma vez que não é possível efetuar todas as ações através da GUI. Por exemplo, a localização da unidade de índice de definição e configurar a topologia de pesquisa são possíveis apenas utilizando cmdlets do PowerShell do SharePoint. Utilize o cmdlet do Windows PowerShell SPEnterpriseSearchServiceApplication de restauro e especifique o encontram enviadas para registo e replicado administração de pesquisa da base de dados, Search_Service__DB. Este cmdlet fornece a configuração de procura, esquema, propriedades geridas, regras e origens e cria um conjunto predefinido de outros componentes.
    * Assim que a aplicação de serviço de pesquisa tem de ser recriada, tem de iniciar uma pesquisa completa para cada origem de conteúdo restaurar o serviço de pesquisa. Perder algumas informações de análise do farm no local, tais como recomendações de pesquisa.

7. Depois de concluídos todos os passos, guardar o plano de recuperação e o plano de recuperação final terá um aspeto semelhante.

    ![RP guardado](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Efetuar uma ativação pós-falha de teste
Siga [esta orientação](site-recovery-test-failover-to-azure.md) para efetuar uma ativação pós-falha de teste.

1.  Aceda ao portal do Azure e selecione o Cofre de serviço de recuperação.
2.  Clique no plano de recuperação criado para a aplicação do SharePoint.
3.  Clique em "Ativação pós-falha de teste".
4.  Selecione o ponto de recuperação e de rede virtual do Azure para iniciar o processo de ativação pós-falha de teste.
5.  Assim que o ambiente secundário está ativo, pode realizar as validações.
6.  Depois de validações estiverem concluídas, pode clicar em "Limpeza ativação pós-falha de teste" no plano de recuperação e o ambiente de ativação pós-falha de teste é limpa.

Para obter orientações sobre a fazer a ativação pós-falha de teste do AD e DNS, consulte [considerações de ativação pós-falha do AD e DNS](site-recovery-active-directory.md#test-failover-considerations) documento.

Para obter orientações sobre a efetuar ativação pós-falha de teste para o SQL Server sempre em grupos de disponibilidade, consulte [fazer testar a ativação pós-falha para o SQL Server Always On](site-recovery-sql.md#steps-to-do-a-test-failover) documento.

## <a name="doing-a-failover"></a>Efetuar uma ativação pós-falha
Siga [esta orientação](site-recovery-failover.md) para efetuar uma ativação pós-falha.

1.  Aceda ao portal do Azure e selecione o Cofre dos serviços de recuperação.
2.  Clique no plano de recuperação criado para a aplicação do SharePoint.
3.  Clique em 'Failover'.
4.  Selecione o ponto de recuperação para iniciar o processo de ativação pós-falha.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre [replicar outras aplicações](site-recovery-workload.md) utilizando a recuperação de sites.
