---
title: "Proteger uma implementação de aplicação multicamada SAP NetWeaver utilizando o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como proteger o SAP NetWeaver implementações de aplicações utilizando o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 5a47acab598e113ef7ed968dd3a6429ac3bc1ec3
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-using-azure-site-recovery"></a>Proteger uma implementação de aplicação multicamada SAP NetWeaver utilizando o Azure Site Recovery

A maioria das implementações de grande e média da SAP têm algum tipo de solução de recuperação após desastre.  A importância das soluções de recuperação após desastre robustas e testable aumentou como processos de negócio mais núcleos são movidos para aplicações como o SAP.  O Azure Site Recovery foi testadas e integradas com aplicações SAP e exceder as capacidades da maioria das soluções de recuperação após desastre no local, um custo inferior total de propriedade (TCO) que soluções concorrentes.
Com o Azure Site Recovery, pode:
* Ative a proteção das aplicações SAP NetWeaver e não NetWeaver Production em execução no local, através da replicação de componentes para o Azure.
* Ative a proteção das aplicações SAP NetWeaver e não NetWeaver Production em execução no Azure, através da replicação de componentes para outro datacenter do Azure.
* Simplificar a migração para a nuvem utilizando a Recuperação de Sites para migrar a implementação do SAP para o Azure.
* Simplifique as atualizações, testes e protótipos de projetos SAP ao criar um clone de produção a pedido para testar aplicações SAP.

Este artigo descreve como proteger o SAP NetWeaver implementações de aplicações utilizando [do Azure Site Recovery](site-recovery-overview.md). Este artigo abrange as melhores práticas para proteger uma implementação de SAP NetWeaver de três camadas no Azure através da replicação para outro datacenter do Azure utilizando o Azure Site Recovery, os cenários suportados e configurações e como executar as ativações pós-falha, ambas as ativações pós-falha (simulações de recuperação após desastre) e as ativações pós-falha real de teste.


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que compreende o seguinte:

1. [Uma máquina virtual a replicar para o Azure](azure-to-azure-walkthrough-enable-replication.md)
2. Como [estruturar uma rede de recuperação](site-recovery-azure-to-azure-networking-guidance.md)
3. [Efetuar uma ativação pós-falha de teste para o Azure](azure-to-azure-walkthrough-test-failover.md)
4. [Efetuar uma ativação pós-falha para o Azure](site-recovery-failover.md)
5. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
6. Como [replicar do SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Cenários suportados
Com o Azure Site Recovery pode implementar uma solução de recuperação após desastre para os seguintes cenários:
* Sistemas SAP com um replicar o datacenter do Azure para outro datacenter do Azure (DR do Azure para o Azure), como criado [aqui](https://aka.ms/asr-a2a-architecture).
* Site de sistemas SAP em execução no VMWare (ou físico) servidores no local a replicar para um DR num datacenter do Azure (VMware para o Azure DR), que requer alguns componentes adicionais conforme criado [aqui](https://aka.ms/asr-v2a-architecture).
* Site de sistemas SAP em execução no Hyper-V no local a replicar para um DR num datacenter do Azure (Hyper-V para Azure DR), que requer alguns componentes adicionais conforme criado [aqui](https://aka.ms/asr-h2a-architecture).

Este documento utiliza o primeiro caso - DR do Azure para o Azure - para demonstrar funcionalidades de recuperação do Azure Site Recovery SAP desastre. Como a replicação do Azure Site Recovery desconhece de aplicação, o processo descrito deverá conter para obter outros cenários, bem como.

### <a name="required-foundation-services"></a>Serviços necessários foundation
Neste cenário de documentação todos os foi implementada com os seguintes serviços foundation implementados:
* ExpressRoute ou de Site para Site rede privada Virtual (VPN)
* Servidor de pelo menos um controlador de domínio do Active Directory e DNS em execução no Azure

Recomenda-se que a infraestrutura acima é estabelecida antes de implementar o Azure Site Recovery.


## <a name="typical-sap-application-deployment"></a>Implementação de aplicação de SAP típica
Normalmente, implementar clientes SAP grande entre as aplicações de SAP individuais 6 a 20.  A maioria destas aplicações baseiam-se nos motores do SAP NetWeaver ABAP ou Java.  Suportar estas aplicações de NetWeaver core é muitas mais pequenos motores de autónoma de SAP não NetWeaver específico e, normalmente, algumas aplicações não SAP.  

É fundamental para inventário de todas as aplicações SAP em execução numa horizontal e para determinar o modo de implementação (camada 2 ou 3 camadas), versões, patches, tamanhos, churn taxas e requisitos de persistência de disco.

![Implementação padrão](./media/site-recovery-sap/sap-typical-deployment.png)

A camada de persistência de base de dados SAP deve ser protegida através de ferramentas nativas de DBMS como AlwaysOn do SQL Server, Oracle DataGuard ou HANA replicação do sistema. A camada de cliente não é também protegida pelo Azure Site Recovery, mas é importante a ter em consideração os tópicos que afetam esta camada, tais como o atraso de propagação de DNS, segurança e acesso remoto para o Centro de dados de DR.

O Azure Site Recovery é a solução recomendada para a camada de aplicação, incluindo (A) SCS. Form de outras aplicações, tais como aplicações SAP não NetWeaver e aplicações SAP não faz parte do ambiente geral da implementação SAP e também deve ser protegida com o Azure Site Recovery.

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais
Siga [esta orientação](azure-to-azure-walkthrough-enable-replication.md) iniciar a replicação de todas as SAP aplicação máquinas virtuais para o Centro de dados de DR do Azure.

Se estiver a utilizar um IP estático, pode especificar o IP que pretende que a máquina virtual para efetuar para a secção de cartões de interface de rede nas definições de rede e computação.

![IP de destino](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação permite a ativação pós-falha de várias camadas numa aplicação de várias camada, por conseguinte, manter a consistência da aplicação da sequência. Siga os passos descritos [aqui](site-recovery-create-recovery-plans.md) ao criar um plano de recuperação para uma aplicação web de várias camadas.

### <a name="adding-scripts-to-the-recovery-plan"></a>A adição de scripts para o plano de recuperação
Terá de fazer algumas operações em máquinas virtuais do Azure após ativação pós-falha de teste ativação pós-falha para as suas aplicações funcionar corretamente. Pode automatizar a operação de ativação pós-falha de post como atualizar a entrada DNS e alterar os vínculos e ligações, adicionando scripts correspondentes no plano de recuperação, conforme descrito em [neste artigo](site-recovery-create-recovery-plans.md#add-scripts).

### <a name="dns-update"></a>Atualização de DNS
Se o DNS está configurado para a atualização dinâmica de DNS, em seguida, as máquinas virtuais, normalmente, atualize o DNS com as novas IP assim que for iniciado. Se pretender adicionar um passo explícito para atualizar o DNS com os IPs novo das máquinas virtuais, em seguida, adicionar este [script para atualizar o IP no DNS](https://aka.ms/asr-dns-update) como uma ação de post em grupos de plano de recuperação.  

## <a name="example-azure-to-azure-deployment"></a>Exemplo de implementação do Azure para o Azure
No diagrama abaixo a DR do Azure para o Azure do Azure Site Recovery cenário é descrito:
* O Centro de dados primária está num Singapura (Azure Sul-Oriental) e o Centro de dados de DR RAE de Hong Kong (Azure Oriental).  Neste cenário, o local de elevada disponibilidade é fornecida por ter duas VMs AlwaysOn do SQL Server em execução no modo síncrono no Singapura.
* ASCS de partilha de ficheiros pode ser utilizado para fornecer HA para os pontos únicos de SAP da falha. ASCS de partilha de ficheiros não necessita de um disco partilhado de cluster e aplicações, tais como SIOS não são necessárias.
* Proteção DR para a camada DBMS é conseguida utilizando a replicação assíncrona.
* Este cenário mostra "simétrico DR" – um termo utilizado para descrever uma solução de DR que tenha uma réplica exata de produção, por conseguinte, a solução de DR SQL Server tem elevada disponibilidade. A utilização de DR simétrico não é obrigatória para a camada de base de dados e a flexibilidade de implementações de nuvem para criar um nó de disponibilidade elevada local rapidamente após um evento de DR tirar partido de muitos clientes.
* O diagrama ilustra a camada de servidor SAP NetWeaver ASCS e aplicação replicada pelo Azure Site Recovery.

![Cenário de replicação](./media/site-recovery-sap/sap-replication-scenario.png)

## <a name="doing-a-test-failover"></a>Efetuar uma ativação pós-falha de teste
Siga [esta orientação](azure-to-azure-walkthrough-test-failover.md) para efetuar uma ativação pós-falha de teste.

1.  Aceda ao portal do Azure e selecione o Cofre dos serviços de recuperação.
2.  Clique no plano de recuperação criado para aplicações SAP (s).
3.  Clique em "Ativação pós-falha de teste".
4.  Selecione o ponto de recuperação e de rede virtual do Azure para iniciar o processo de ativação pós-falha de teste.
5.  Assim que o ambiente secundário está ativo, pode realizar as validações.
6.  Depois de validações estiverem concluídas, clique em "Limpeza ativação pós-falha de teste" e para limpar o ambiente de ativação pós-falha.

## <a name="doing-a-failover"></a>Efetuar uma ativação pós-falha
Siga [esta orientação](site-recovery-failover.md) quando estão a fazer uma ativação pós-falha.

1.  Aceda ao portal do Azure e selecione o Cofre dos serviços de recuperação.
2.  Clique no plano de recuperação criado para a aplicação (ões) de SAP.
3.  Clique em 'Failover'.
4.  Selecione o ponto de recuperação para iniciar o processo de ativação pós-falha.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como criar uma solução de recuperação após desastre para as implementações do SAP NetWeaver utilizando o Azure Site Recovery no [deste documento técnico](http://aka.ms/asr-sap). O documento técnico também descreve as recomendações para diferentes arquiteturas SAP, apresenta uma lista de aplicações suportadas e tipos VM para SAP no Azure e descreve possíveis planos de teste para a sua solução de recuperação após desastre.

Saiba mais sobre [replicar outras cargas de trabalho](site-recovery-workload.md) utilizando a recuperação de sites.
