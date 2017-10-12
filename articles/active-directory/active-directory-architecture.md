---
title: Compreender a arquitetura do Azure Active Directory | Microsoft Docs
description: "Explica o que é um inquilino do Azure AD e como gerir o Azure através do Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/02/2017
ms.author: markvi
ms.openlocfilehash: 50dad848cfbdab7f5b1fff0fcec3b5f754e6ae74
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-azure-active-directory-architecture"></a>Compreender a arquitetura do Azure Active Directory
O Azure Active Directory (Azure AD) permite-lhe gerir de forma segura o acesso dos seus utilizadores aos serviços e recursos do Azure. Incluído com o Azure AD está um conjunto completo de capacidades de gestão de identidades. Para obter informações sobre as funcionalidades do Azure AD, veja [What is Azure Active Directory?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis) (O que é o Azure Active Directory?)

Com o Azure AD, pode criar e gerir utilizadores e grupos e ativar permissões para permitir e recusar o acesso a recursos empresariais. Para obter informações sobre a gestão de identidades, veja [The fundamentals of Azure identity management](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity) (Noções básicas da gestão de identidades do Azure).

## <a name="azure-ad-architecture"></a>Arquitetura do Azure AD
A arquitetura geograficamente distribuída do Azure AD combina extensas capacidades de monitorização, redirecionamento automatizado, ativação pós-falha e recuperação, o que nos permite proporcionar aos nossos clientes disponibilidade e desempenho de nível empresarial.

Este artigo cobre os elementos da arquitetura seguintes:
 *  Design da arquitetura do serviço
 *  Escalabilidade 
 *  Disponibilidade contínua
 *  Datacenters

### <a name="service-architecture-design"></a>Design da arquitetura do serviço
A forma mais comum de criar um sistema dimensionável, de elevada disponibilidade e rico em dados é utilizar blocos modulares independentes ou unidades de escala para a camada de dados do Azure AD. As unidades de escala são denominadas *partições*. 

A camada de dados tem vários serviços front-end que proporcionam a capacidade de leitura/escrita. O diagrama abaixo mostra como os componentes de uma partição de diretório único são distribuídos ao longo dos datacenters geograficamente distribuídos. 

  ![Partições de Diretório Único](./media/active-directory-architecture/active-directory-architecture.png)

Os componentes da arquitetura do Azure AD incluem uma réplica primária e réplicas secundárias.

**Réplica primária**

A *réplica primária* recebe todas as *escritas* da partição a que pertence. Qualquer operação de escrita é replicada imediatamente numa réplica secundária noutro datacenter antes de devolver com êxito para o chamador, garantindo, assim, a durabilidade geograficamente redundante das escritas.

**Réplicas secundárias**

Todas as *leituras* do diretório são servidas a partir de *réplicas secundárias*, que estão em datacenters localizados fisicamente em diferentes geografias. Existem muitas réplicas secundárias, uma vez que os dados são replicados de forma assíncrona. As leituras do diretório, tais como pedidos de autenticação, são servidas a partir de datacenters que estão próximos dos nossos clientes. As réplicas secundárias são responsáveis pela escalabilidade das leituras.

### <a name="scalability"></a>Escalabilidade

A escalabilidade é a capacidade de um serviço se expandir para satisfazer o aumento da procura pelo desempenho. A escalabilidade das escritas é obtida ao particionar os dados. A escalabilidade das leituras é obtida ao replicar dados de uma partição para várias réplicas secundárias distribuídas em todo o mundo.

Geralmente, os pedidos de aplicações de diretório são encaminhados para o datacenter do qual estão fisicamente mais perto. As escritas são redirecionadas, de forma transparente, para a réplica primária, de modo a proporcionar consistência de leitura/escrita. As réplicas secundárias ampliam significativamente a escala das partições, porque, regra geral, na maior parte do tempo, os diretórios estão a servir leituras.

As aplicações de diretório ligam-se aos datacenters mais próximos. Isto melhora o desempenho e, consequentemente, possibilita o aumento horizontal. Uma vez que as partições de diretório podem ter muitas réplicas secundárias, estas podem ser colocadas mais perto dos clientes dos diretórios. Apenas os componentes do serviço de diretório internos que sejam de escrita intensiva segmentam diretamente a réplica primária ativa.

### <a name="continuous-availability"></a>Disponibilidade contínua

A disponibilidade (ou tempo de atividade) define a capacidade de um sistema de funcionar sem interrupções. A chave para a elevada disponibilidade do Azure AD é que os nossos serviços podem alternar rapidamente o tráfego entre múltiplos datacenters geograficamente distribuídos. Cada datacenter é independente, o que possibilita os modos de falha de descorrelação.

O design de partições do Azure AD é simplificado quando comparado com o do AD empresarial, o que é fundamental para aumentar verticalmente. Adotámos um design de mestre único que inclui um processo de ativação pós-falha de réplicas primárias determinístico e cuidadosamente orquestrado.

**Tolerância a falhas**

Os sistemas estão mais disponíveis se forem tolerantes a falhas de hardware, rede e software. Para cada partição no diretório, existe uma réplica principal de elevada disponibilidade: a réplica primária. Só são realizadas nesta réplica as escritas para a partição. Esta réplica é monitorizada continuamente e de perto e as escritas podem ser alternadas imediatamente para outra réplica (que se torna na primária nova) caso seja detetada uma falha. Durante a ativação pós-falha, poderá haver uma perda de disponibilidade de escrita de cerca de 1 a 2 minutos, geralmente. A disponibilidade de leitura não é afetada durante este período.

As operações de leitura (que superam em muito as escritas) só vão para as réplicas secundárias. Uma vez que as réplicas secundárias são idempotentes, a perda de qualquer réplica numa determinada partição é facilmente compensada mediante o direcionamento das leituras para outra réplica, normalmente no mesmo datacenter.

**Durabilidade de dados**

As escritas são consolidadas de forma duradoura a, pelo menos, dois datacenters antes de serem reconhecidas. Isto acontece ao consolidar primeiro as escritas no datacenter primário e, depois, replicá-las imediatamente em, pelo menos, num outro. Desta forma, garante-se que uma potencial perda catastrófica de dados no datacenter que aloja a réplica primária não resulta em perda de dados.

O Azure AD mantém um [Objetivo de Tempo de Recuperação (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) de zero para emissão de tokens e leituras de diretório e um RTO de alguns minutos (aproximadamente cinco) para escritas de diretório. Também mantemos um [Objetivo de Ponto de Recuperação (RPO)](https://en.wikipedia.org/wiki/Recovery_point_objective) de zero e não perdemos dados em ativações pós-falha.

### <a name="data-centers"></a>Datacenters

As réplicas do Azure AD são armazenadas nos datacenters localizados em todo o mundo. Para obter mais informações, veja [Datacenters do Azure](https://azure.microsoft.com/en-us/overview/datacenters).

O Azure AD funciona em datacenters que têm as características seguintes:

 * Os serviços de Autenticação, Graph e outros serviços do AD residem por detrás do serviço Gateway. O Gateway gere o balanceamento de carga destes serviços. Se forem detetados servidores em mau estado de funcionamento, através de sondas de estado de funcionamento transacionais, é feita a ativação pós-falha do mesmo automaticamente. Com base nestas sondas de estado de funcionamento, o Gateway encaminha dinamicamente o tráfego para os datacenters em bom estado de funcionamento.
 * Relativamente a *leituras*, o diretório tem réplicas secundárias e serviços front-end correspondentes numa configuração ativo-ativo e em funcionamento em vários datacenters. Em caso de falha de um datacenter inteiro, o tráfego é encaminhado automaticamente para outro datacenter.
 *  Relativamente a *escritas*, o diretório faz a ativação pós-falha da réplica primária (principal) nos datacenters através de procedimentos de ativação pós-falha planeada (a réplica primária nova é sincronizada com a primária antiga) ou de emergência. A durabilidade dos dados é obtida mediante a replicação de uma consolidação com, pelo menos, dois datacenters.

**Consistência de dados**

O modelo de diretório é um modelo de consistência eventual. Um problema comum com os sistemas de replicação assíncrona distribuída está relacionado com o facto de os dados devolvidos por uma “determinada” réplica podem estar desatualizados. 

O Azure AD proporciona consistência de leitura/escrita às aplicações que segmentam réplicas secundárias ao encaminhar as respetivas escritas para a réplica primária e ao enviá-las de forma síncrona novamente para a réplica secundária.

As escritas de aplicações que utilizem a Graph API do Azure AD não mantêm afinidade com réplicas de diretórios para consistência de leitura/escrita. O serviço Azure AD Graph mantém uma sessão lógica, que tem afinidade com uma réplica secundária utilizada para leituras; a afinidade é capturada num “token de réplicas” que o serviço Graph coloca em cache mediante a utilização de uma cache distribuída. Este token é, depois, utilizado para operações subsequentes na mesma sessão lógica. 

 >[!NOTE]
 >As escritas são replicadas imediatamente na réplica secundária para a qual as leituras da sessão lógica foram emitidas.
 >

**Proteção de cópia de segurança**

O diretório implementa eliminações de forma recuperável, em vez de eliminações definitivas, para utilizadores e inquilinos para recuperação fácil em caso de eliminações acidentais por parte de um cliente. Se o administrador do seu inquilino eliminar por acidente utilizadores, pode facilmente anular a ação e restaurar esses utilizadores. 

O Azure AD implementa cópias de segurança diárias de todos os dados, pelo que consegue restaurar com autoridade dados, em caso de eliminações lógicas ou danos nos dados. A nossa camada de dados utiliza códigos de correção de erros, de modo a poder procurar erros e corrigir automaticamente determinados tipos de erros de disco.

**Métricas e monitores**

A execução de um serviço de elevada disponibilidade requer capacidades de métricas e monitorização de topo. O Azure AD analisa e comunica, de forma contínua, as métricas-chave de estado de funcionamento e os critérios de sucesso relativos a cada um dos seus serviços. Desenvolvemos e aperfeiçoamos continuamente as métricas, a monitorização e os alertas para cada cenário, em cada serviço do Azure AD e em todos os serviços.

Se um serviço do Azure AD não estiver a funcionar como o esperado, tomamos medidas imediatamente para restaurar o funcionamento o mais depressa possível. A métrica mais importante que o Azure AD acompanha é a velocidade com que conseguimos detetar e mitigar problemas dos clientes ou de sites ativos. Investimos significativamente na monitorização e nos alertas para minimizar o tempo de deteção (TTD de Destino: <5 minutos) e a prontidão operacional para minimizar o tempo para mitigar (TTM de Destino: <30 minutos).

**Operações seguras**

Empregamos controlos operacionais, como a autenticação multifator (MFA), em qualquer operação, bem como auditoria de todas as operações. Além disso, utilizamos um sistema de elevação just-in-time para conceder o acesso temporário necessário a qualquer tarefa a pedido operacional de forma contínua. Para obter mais informações, veja [A Cloud de Confiança](https://azure.microsoft.com/en-us/support/trust-center).

## <a name="next-steps"></a>Passos seguintes
[Guia para programadores do Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)

