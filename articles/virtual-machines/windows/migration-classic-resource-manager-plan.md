---
title: "Planeamento da migração de recursos IaaS do clássico para o Azure Resource Manager | Microsoft Docs"
description: "Planeamento da migração de recursos IaaS do clássico para o Azure Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: b77000c2cf46b59b65c27c78182193624fd62062
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planeamento da migração de recursos IaaS do clássico para o Azure Resource Manager
Enquanto o Azure Resource Manager oferece muitas funcionalidades incrível, é essencial para planear a saída da sua viagem de migração para fazer coisas sure correm. A dedicar muito tempo sobre o planeamento irá garantir que não ocorrerem problemas ao executar atividades de migração.

> [!NOTE]
> As seguintes orientações descontos elevados foi contribuíram para pela equipa de Consultadora de cliente do Azure e os arquitetos de soluções de nuvem trabalhar com os clientes em ambientes de grandes dimensões de migração. Como esse este documento irá continuar a obter atualizada como novos padrões de sucesso surgir, por isso, verifique novamente a partir do momento tempo para ver se existem quaisquer novas recomendações.

Existem quatro fases geral do journey a migração:<br>

![Fases de migração](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planear

### <a name="technical-considerations-and-tradeoffs"></a>Considerações de técnicas e fala

Consoante o tamanho de requisitos técnicos, localizações geográficas e práticas operacionais, poderá pretender considerar:

1. Por que motivo o Azure Resource Manager for pretendido para a sua organização?  Quais são as razões de negócio para uma migração?
2. Quais são os motivos pelos quais técnicos para o Azure Resource Manager?  O que (se aplicável) serviços Azure adicionais pretende tirar partido?
3. Que aplicações (ou conjuntos de máquinas virtuais) estão incluídos na migração?
4. Os cenários são suportados com a API de migração?  Reveja o [não suportado funcionalidades e configurações](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations).
5. As equipas operacionais agora suportar VMs/aplicações em clássica e do Azure Resource Manager?
6. Como (se todo) do Azure Resource Manager alterar a implementação de VM, gestão, monitorização e relatórios processos?  Os scripts de implementação tem de ser atualizados?
7. O que é que as comunicações planear para o alertar intervenientes (os utilizadores finais, os proprietários da aplicação e proprietários de infraestrutura)?
8. Dependendo da complexidade do ambiente, deve existir um período de manutenção em que a aplicação está disponível para os utilizadores finais e para os proprietários da aplicação?  Se Sim, para o período de tempo?
9. O que é o plano de formação para garantir que intervenientes são knowledgeable e proficient no Gestor de recursos do Azure?
10. O que é a gestão do programa ou o esquema de gestão do projeto para a migração?
11. Quais são as linhas cronológicas para a migração do Gestor de recursos do Azure e outros relacionadas com tecnologia da estrada maps?  São serem executadas de forma ideal alinhados?

### <a name="patterns-of-success"></a>Padrões de sucesso

Os clientes com êxito tem detalhadas planos onde as questões anteriores são abordadas, documentadas e a regida.  Certifique-se de que os planos de migração amplamente são comunicados ao sponsors e intervenientes.  Equipar por si com conhecimentos sobre as opções de migração; é altamente recomendável ler através deste documento migração definido abaixo.

* [Descrição geral da migração de plataforma suportada dos recursos IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Técnica descrição profunda sobre a migração de plataforma suportada do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planear a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilizar o PowerShell para migrar os recursos IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilizar a CLI para migrar os recursos IaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas de Comunidade para prestar assistência com a migração de recursos IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rever as perguntas mais frequentes sobre a migração de IaaS de recursos do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Pitfalls para evitar

- Falha ao plano.  Os passos de tecnologia de migração são comprovados e o resultado é previsível.
- Pressuposto de que a plataforma suportada será uma conta de API para todos os cenários de migração. Leia o [não suportado funcionalidades e configurações](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) para compreender os cenários que são suportados.
- Planear não potencial falha de aplicação para os utilizadores finais.  Planear a memória intermédia suficiente para o avisar adequadamente os utilizadores finais de tempo de aplicação potencialmente indisponível.


## <a name="lab-test"></a>Teste de laboratório

**Replicar o seu ambiente e efetuar uma migração de teste**
  > [!NOTE]
  > É executada replicação exata do seu ambiente existente utilizando uma ferramenta contribuíram de Comunidade que não é oficialmente suportada pelo Support da Microsoft. Por conseguinte, é um **opcional** passo mas é a melhor forma de determinar os problemas sem afetar os ambientes de produção. Se utilizar uma ferramenta contribuíram de Comunidade não é uma opção, em seguida, leia sobre a recomendação de preparar/validar/abortar Dry executar abaixo.
  >

  Realizar um laboratório de teste do seu cenário exato (computação, redes e armazenamento) é a melhor forma de garantir uma migração uniforme. Isto irá ajudar a garantir:

  - Um laboratório detida separado ou num ambiente de não produção existente para testar. Recomendamos um laboratório detida separado que pode ser migrado repetidamente e pode ser modificado destructively.  A recolher/hydrate metadados a partir de subscrições reais de scripts são listados abaixo.
  - É uma boa ideia criar o laboratório numa subscrição separada. O motivo é que o laboratório será desligado repetidamente e ter um separado, subscrição isolada irá reduzir as hipóteses que algo real irá obter sejam eliminado acidentalmente.

  Isto pode ser conseguido utilizando a ferramenta de AsmMetadataParser. [Leia mais sobre esta ferramenta aqui](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Padrões de sucesso

Os seguintes foram problemas detetados em muitos das migrações maior. Não se trata de uma lista exaustiva e deve referir-se ao [não suportado funcionalidades e configurações](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) para obter mais detalhes.  Pode ou não pode encontrar estes problemas técnicos, mas se o fizer a resolver estes antes de tentar efetuar a migração irá garantir uma experiência smoother.

- **Fazer uma execução de Dry preparar/validar/abortar** -talvez é o passo mais importante para garantir clássico para êxito de migração do Gestor de recursos do Azure. A migração API tem três passos principais: validar, preparar e a consolidação. Validar irá ler o estado do seu ambiente clássico e devolver um resultado de todos os problemas. No entanto, porque alguns problemas poderão existir na pilha do Azure Resource Manager, validar será interceta tudo. O próximo passo no processo de migração, preparar ajudará a expor esses problemas. Preparar irá mover os metadados do clássico para o Azure Resource Manager, mas será não consolidar a movimentação e irá não remover ou alterar nada no lado clássico. A execução de dry envolve a preparar a migração, em seguida, abortar (**não são consolidar**) preparar a migração. É o objetivo de validar/preparar/abortar dry executar para ver todos os metadados na pilha do Azure Resource Manager, examiná-lo (*programaticamente ou no Portal*), certifique-se de que tudo migra corretamente e de trabalho através de problemas técnicos.  Esta será também dão-lhe um sentido de duração de migração, para que possam planear em conformidade para período de indisponibilidade.  Um validar/preparar/abortar não irá causar qualquer período de inatividade do utilizador; Por conseguinte, é não acontece a utilização da aplicação.
  - Os itens abaixo, terá de ser resolvidos antes da execução de dry, mas um teste de execução dry também com segurança esvaziar saída estes passos de preparação se estes estão em falta. Durante a migração de enterprise, encontrámos a execução de dry ser uma forma segura e valiosas para garantir a preparação da migração.
  - Quando preparar está em execução, o controlo plane (operações de gestão do Azure) será bloqueada para a rede virtual todo, pelo que não podem ser efetuadas alterações aos metadados VM durante validar/preparar/abortar.  Mas, caso contrário, qualquer função de aplicação (RD, VM utilização, etc.) será afetada.  Os utilizadores das VMs não saberá que a execução de dry está a ser executada.

- **Express circuitos Expressroute e a VPN**. Atualmente, Gateways de rota Express com ligações de autorização não podem ser migrados sem períodos de indisponibilidade. Para a solução, consulte [migrar ExpressRoute circuitos e associadas redes virtuais do clássico para o modelo de implementação Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **Extensões de VM** -extensões de Máquina Virtual, potencialmente, são uma dos roadblocks maiores para migrar VMs em execução. Remediação de extensões de VM pode demorar upwards of 1-2 dias, por isso planeie em conformidade.  É necessário um trabalho do agente do Azure para informar o estado da extensão de VM de VMs em execução. Se o estado volta como incorreto para uma VM em execução, esta será parado a migração. O próprio agente tem de estar pela ordem de trabalho para permitir a migração, mas se existirem extensões na VM, em seguida, tanto um agente de trabalho e saído acesso à internet (com o DNS) serão necessários para a migração para avançar.
  - Se a conectividade a um servidor DNS se perder durante a migração, todas as extensões de VM, exceto BGInfo versão 1. \* tem de primeiro removidos cada VM antes de preparar a migração e subsequentemente novamente adicionado novamente à VM após a migração do Gestor de recursos do Azure.  **Esta é apenas para as VMs que estejam a executar.**  Se as VMs estão paradas desalocada, extensões de VM não precisa de ser removido.

  > [!NOTE]
  > Muitos extensões, tal como será monitorização do Centro de segurança e diagnóstico do Azure reinstalar próprios após a migração, por isso, a remover não é um problema.

  - Além disso, certifique-se grupos de segurança de rede não são restringir o acesso de internet de saída. Isto pode acontecer com algumas configurações de grupos de segurança de rede. Acesso de internet de saída (e DNS) é necessário para as extensões de VM a serem migradas para o Azure Resource Manager.
  - Duas versões da extensão BGInfo existem e são designadas por versões 1 e 2.  

      - Se a VM estiver a utilizar a extensão de versão 1 BGInfo, pode deixar esta extensão como está. A migração API ignora esta extensão. É possível adicionar a extensão BGInfo após a migração.
      - Se a VM estiver a utilizar a extensão de versão 2 BGInfo baseados em JSON, a VM foi criada no portal do Azure. A API de migração inclui esta extensão na migração para o Azure Resource Manager, desde que o agente está a funcionar e se tem acesso de internet de saída (e DNS).

  - **Opção de remediação 1**. Se souber que as suas VMs não terão saída acesso à internet, um serviço DNS de funcionar e, em seguida, os agentes do Azure a trabalhar nas VMs, desinstale todas as extensões VM como parte da migração antes de preparar, em seguida, reinstale as extensões de VM após a migração.
  - **Opção de remediação 2**. Se forem demasiado grandes de um hurdle extensões VM, outra opção consiste em Desalocação de encerramento/todas as VMs antes da migração. Migrar VMs desalocadas e reiniciá-las no lado do Azure Resource Manager. A vantagem aqui é que as extensões de VM irão migrar. O downside é que todos os público com acesso à IPs virtuais serão perdidas (pode ser um não-starter), e, obviamente VMs serão encerrado a causar um muito maior impacto nas aplicações de trabalho.

    > [!NOTE]
    > Se uma política do Centro de segurança do Azure está configurada com as VMs em execução que está a ser migradas, a política de segurança tem de ser parado antes de remover as extensões, caso contrário, a extensão de monitorização de segurança será reinstalada automaticamente na VM depois de ser removido.

- **Conjuntos de disponibilidade** – para uma rede virtual (vNet) a serem migradas para o Azure Resource Manager, a implementação clássica (ou seja, o serviço de nuvem) contidas VMs têm de estar todos num conjunto de disponibilidade ou as VMs todos os não pode ser qualquer conjunto de disponibilidade. Ter mais do que um conjunto de disponibilidade no serviço em nuvem não é compatível com o Azure Resource Manager e será parado a migração.  Além disso, não pode existir algumas VMs num conjunto de disponibilidade e algumas VMs não num conjunto de disponibilidade. Para resolver este problema, será necessário remediar ou reshuffle o seu serviço em nuvem.  Planear em conformidade, como poderá ser demorada.

- **Implementações de função da Web/trabalho** -serviços em nuvem que contém funções web e de trabalho não é possível migrar para o Azure Resource Manager. As funções da web/trabalho primeiro tem de ser removidas da rede virtual antes de iniciar a migração.  É uma solução típica para mover apenas instâncias de função da web/trabalho a uma rede virtual de clássico separada que também está ligada a um circuito ExpressRoute ou para migrar o código para os serviços mais recentes da aplicação de PaaS (este debate está fora do âmbito deste documento). A primeira no Reimplementar a maiúsculas e minúsculas, criar uma nova rede virtual clássica, mover/volte a implementar as funções da web/trabalho para essa nova rede virtual e eliminar as implementações de rede virtual a ser movida. Não existem alterações de código necessárias. A nova [Peering de rede Virtual](../../virtual-network/virtual-network-peering-overview.md) capacidade pode ser utilizada a ponto, em conjunto, a rede virtual clássica que contém as funções da web/trabalho e outras redes virtuais na mesma região do Azure, tais como a rede virtual a ser migrada (**depois de concluída a migração da rede virtual, redes virtuais em modo de peering não podem ser migradas**), por conseguinte, fornecer as mesmas capacidades sem perda de desempenho e não penalidades de latência/largura de banda. Dada a adição de [Peering de rede Virtual](../../virtual-network/virtual-network-peering-overview.md), implementações de função da web/trabalho facilmente agora podem ser mitigadas e não bloqueia a migração para o Azure Resource Manager.

- **Quotas do Gestor de recursos do Azure** -regiões do Azure têm quotas/limites separados para clássica e do Azure Resource Manager. Apesar de um cenário de migração não está a ser consumido novo hardware *(está a troca de VMs existentes do clássico para o Azure Resource Manager)*, quotas do Azure Resource Manager ainda tem de ser implementados com capacidade suficiente antes de iniciar a migração. Listadas abaixo está os principais limites que viu causam problemas.  Abra um pedido de suporte para aumentar os limites de quota.

    > [!NOTE]
    > Estes limites têm de ser gerado na mesma região que o seu ambiente atual a ser migrada.
    >

    - Interfaces de Rede
    - Balanceadores de carga
    - IPs Públicos
    - IPs públicos estáticos
    - Núcleos
    - Grupos de Segurança de Rede
    - Tabelas de Rota

    Pode verificar a sua atual quotas do Azure Resource Manager utilizando os seguintes comandos com a versão mais recente do Azure PowerShell.

    **Computação** *(núcleos, conjuntos de disponibilidade)*

    ```powershell
    Get-AzureRmVMUsage -Location <azure-region>
    ```

    **Rede** *(Interfaces, balanceadores de carga, as tabelas de rotas de rede de redes virtuais, os IPs públicos estáticos, os IPs públicos, grupos de segurança de rede)*

    ```powershell
    Get-AzureRmUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **Armazenamento** *(conta de armazenamento)*

    ```powershell
    Get-AzureRmStorageUsage
    ```

- **API de Gestor de recursos do Azure, os limites** – se tiver um ambiente grande o suficiente (ex. > 400 VMs numa VNET), poderá atingiu a API de predefinição os limites para escritas (atualmente `1200 writes/hour`) no Gestor de recursos do Azure. Antes de iniciar a migração, deverá emitir um pedido de suporte para aumentar este limite da sua subscrição.


- **Excedeu o tempo limite estado de VM de aprovisionamento** - se qualquer VM tem o estado de `provisioning timed out`, este tem de ser resolvida de pré-migração. É a única forma de fazê-lo com o período de indisponibilidade por desaprovisionamento/reprovisioning a VM (eliminar, mantenha o disco e recrie a VM).

- **Estado da VM RoleStateUnknown** - se a migração forem devido a um `role state unknown` mensagem de erro, Inspecione a VM com o portal e certifique-se de que está a ser executado. Este erro normalmente desaparecerá respetivo proprietário (sem remediação necessária) após alguns minutos e é, muitas vezes, um tipo transitório frequentemente visto durante uma Máquina Virtual `start`, `stop`, `restart` operações. **Recomendado prática:** nova tentativa de migração novamente após alguns minutos.

- **Cluster de recursos de infraestrutura não existe** – em alguns casos, determinados de VMs não podem ser migradas por vários motivos ímpares. Um nestes casos conhecidos é se a VM foi recentemente criado (na última semana ou modo) e foram efetuadas para encaminhado para um cluster do Azure que ainda não está equipado para cargas de trabalho do Azure Resource Manager.  Receberá um erro que indica que `fabric cluster does not exist` e não é possível migrar a VM. A aguardar alguns dias, normalmente, irá resolver este problema específico como o cluster em breve obterá ativado o Azure Resource Manager. No entanto, é uma solução imediata `stop-deallocate` a VM, em seguida, reencaminhar continuar com a migração e iniciar a VM cópia de segurança no Azure Resource Manager depois de migrar.

### <a name="pitfalls-to-avoid"></a>Pitfalls para evitar

- Não coloque atalhos e omitir as migrações de validar/preparar/abortar dry executar.
- Mais, caso contrário, os problemas potenciais será superfície durante os passos de validar/preparar/abortar.

## <a name="migration"></a>Migração

### <a name="technical-considerations-and-tradeoffs"></a>Considerações de técnicas e fala

Agora, está pronto porque já trabalhou através de problemas conhecidos com o seu ambiente.

Para as migrações reais, poderá pretender considerar:

1. Planear e agendar a rede virtual (unidade mais pequena de migração) com aumento de prioridade.  Não primeiro as redes virtuais simples e progresso com as redes virtuais mais complicadas.
2. Maioria dos clientes terão de ambientes de não produção e de produção.  Agende produção pela última vez.
3. **(OPCIONAL)**  Agendar um período de indisponibilidade de manutenção com muitos da memória intermédia no caso de surgem problemas inesperados.
4. Comunicar com e alinhar com as equipas de suporte no caso de surgir um.

### <a name="patterns-of-success"></a>Padrões de sucesso

As orientações técnica do _laboratório teste_ secção deve ser considerada e atenuada antes de uma migração real.  Com testes adequados, a migração é, na verdade, um non-evento.  Para ambientes de produção, poderá ser útil ter suporte adicional, tal como um parceiro da Microsoft fidedigno ou serviços do Microsoft Premier.

### <a name="pitfalls-to-avoid"></a>Pitfalls para evitar

A testar não totalmente poderá causar problemas e atraso na migração.  

## <a name="beyond-migration"></a>Para além da migração

### <a name="technical-considerations-and-tradeoffs"></a>Considerações de técnicas e fala

Agora que já está no Gestor de recursos do Azure, maximize a plataforma.  Leia o [descrição geral do Gestor de recursos do Azure](../../azure-resource-manager/resource-group-overview.md) para obter informações sobre outras vantagens.

Aspetos a considerar:

- Agrupamento de migração com outras atividades.  Optar ativamente por participar a maioria dos clientes para uma janela de manutenção da aplicação.  Se Sim, pode querer utilizar este período de indisponibilidade para ativar a outras funcionalidades do Azure Resource Manager, tais como encriptação e a migração para discos geridos.
- Revê as razões técnicas e empresariais para o Azure Resource Manager; Ative os serviços adicionais disponíveis apenas no Azure Resource Manager que se aplicam ao seu ambiente.
- Modernize o seu ambiente com os serviços de PaaS.

### <a name="patterns-of-success"></a>Padrões de sucesso

Ser tem um fim específico nos serviços que pretende ativar no Gestor de recursos do Azure agora.  Muitos clientes localizar o abaixo apelativa para os seus ambientes do Azure:

- [Controlo de acesso baseado em funções](../../azure-resource-manager/resource-group-overview.md#access-control).
- [Modelos Azure Resource Manager para a implementação mais fácil e mais controlada](../../azure-resource-manager/resource-group-overview.md#template-deployment).
- [Etiquetas](../../azure-resource-manager/resource-group-using-tags.md).
- [Controlo de atividade](../../azure-resource-manager/resource-group-audit.md)
- [Políticas do Azure](../../azure-policy/azure-policy-introduction.md)

### <a name="pitfalls-to-avoid"></a>Pitfalls para evitar

Lembre-se de que o motivo pelo qual iniciou esta clássico para journey de migração do Gestor de recursos do Azure.  Quais eram as razões de negócio original? Foi possível alcançar o motivo de negócio?


## <a name="next-steps"></a>Passos seguintes

* [Descrição geral da migração de plataforma suportada dos recursos IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Técnica descrição profunda sobre a migração de plataforma suportada do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilizar o PowerShell para migrar os recursos IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utilizar a CLI para migrar os recursos IaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas de Comunidade para prestar assistência com a migração de recursos IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Consultar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rever as perguntas mais frequentes sobre a migração de IaaS de recursos do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
