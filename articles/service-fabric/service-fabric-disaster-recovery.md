---
title: "Recuperação de desastres do Service Fabric do Azure | Microsoft Docs"
description: "Azure Service Fabric oferece as capacidades necessárias para lidar com todos os tipos de desastres inesperados. Este artigo descreve os tipos de perante desastres que podem ocorrer e saber como lidar com os mesmos."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5346e331d76149ac3aed7aaf11eb3171e0ac5cfc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperação de desastres no Azure Service Fabric
Uma parte crítica da entrega de elevada disponibilidade é garantir que os serviços podem continuam a vigorar após todos os diferentes tipos de falhas. Isto é especialmente importante para as falhas são não planeadas e fora do controlo. Este artigo descreve alguns modos de falha comuns que pode ser perante desastres se não for modelada e geridos corretamente. Também abordar a mitigações e as ações a tomar nesse caso de um desastre aconteceu mesmo assim. O objetivo é limitar ou eliminar o risco de um período de indisponibilidade ou perda de dados sempre que ocorrerem falhas planeadas ou caso contrário, ocorrer.

## <a name="avoiding-disaster"></a>Evitando após desastre
Principal objetivo Service Fabric é ajudar o seu ambiente e os serviços de forma a que os tipos comuns de falha não são perante desastres de modelo. 

Em geral, existem dois tipos de cenários de desastre/falha:

1. Falhas de hardware ou software
2. Falhas operacionais

### <a name="hardware-and-software-faults"></a>Falhas de hardware e software
Falhas de hardware e software são imprevisíveis. A forma mais fácil continuam a vigorar após falhas está em execução mais cópias do serviço abrangido através de limites de falhas de hardware ou software. Por exemplo, se o seu serviço executar apenas num computador específico, a falha do que uma máquina é um desastre relativamente a esse serviço. A forma simple para evitar este desastre é Certifique-se de que o serviço, na verdade, está em execução várias máquinas. Os testes também são necessários para garantir que a falha de uma máquina não interromper o serviço em execução. Planeamento de capacidade garante que uma instância de substituição pode ser criada noutro local e que a redução de capacidade não os restantes serviços de sobrecarga. O mesmo padrão funciona independentemente da que está a tentar evitar a falha de. Por exemplo. Se estiver preocupados com a falha de uma SAN, executar através de vários SANs. Se estiver preocupados com a perda de um bastidor de servidores, de executar através de vários bastidores. Se estiver preocupados sobre a perda de centros de dados, deve executar o serviço em várias regiões do Azure ou centros de dados. 

Quando em execução neste tipo de modo expandido, que está ainda sujeitos a alguns tipos de falhas em simultâneo, mas único e até mesmo várias falhas de um determinado tipo (ex: uma falha de ligação de rede ou de VM único) são processadas automaticamente (e, por isso, já não é um "desastre"). O Service Fabric fornece vários mecanismos para expandir o cluster e colocar os identificadores falhou nós e os serviços de volta. Recursos de infraestrutura de serviço também permite a executar várias instâncias dos seus serviços para evitar estes tipos de falhas não planeadas de ativar para perante desastres reais.

É possível que existam motivos por que motivo o em execução uma implementação suficientemente grande para span através de falhas não é exequível. Por exemplo, pode demorar mais recursos de hardware que está disposto a pagar relativos a possibilidade de falha. Ao lidar com as aplicações distribuídas, poderia ser saltos de comunicação adicional ou a replicação de estado custos através de latência aceitável do faz com que as distâncias geográfica. Em que esta linha é desenhada é diferente para cada aplicação. Para falhas de software mais especificamente, a falha pode ser no serviço que está a tentar dimensionar. Neste caso mais cópias não impedem desastre, uma vez que a condição de falha é correlacionada em todas as instâncias.

### <a name="operational-faults"></a>Falhas operacionais
Mesmo que o serviço está abrangido em todo o mundo com muitos redundancies, ainda pode deparar-desastroso eventos. Por exemplo, se alguém acidentalmente reconfigura o nome de dns para o serviço, ou elimina imediato. Por exemplo, vamos supor que tinha um serviço do Service Fabric com monitorização de estado e alguém eliminado acidentalmente esse serviço. A menos que exista alguns outra mitigação, que o serviço e todos os Estado tinha é agora removido. Estes tipos de perante desastres operacionais ("UPS") necessitam mitigações diferentes e os passos para recuperação de falhas não planeadas regulares. 

As formas melhor para evitar estes tipos de falhas operacionais são para
1. restringir o acesso operacional para o ambiente
2. estritamente auditar operações perigosos
3. impõe automatização, impedir manual ou as alterações de banda fora e validar alterações específicas contra o ambiente real antes enacting-las
4. Certifique-se de que as operações de destrutivas são "de forma recuperável". Operações de forma recuperável não entram em vigor imediatamente ou podem ser anuladas algum período de tempo

O Service Fabric fornece algumas mecanismos para evitar falhas operacionais, tais como fornecer [baseada em funções](service-fabric-cluster-security-roles.md) controlo para operações de cluster de acesso. No entanto, a maioria destas falhas operacionais requerem esforços organizacionais e outros sistemas. Service Fabric fornecer algum mecanismo para reiniciadas falhas operacionais, mais notavelmente cópia de segurança e restauro de serviços com monitorização de estado.

## <a name="managing-failures"></a>Gestão de falhas
O objetivo de Service Fabric é quase sempre gestão automática de falhas. No entanto, para lidar com alguns tipos de falhas, serviços tem de ter códigos adicionais. Outros tipos de falhas devem _não_ automaticamente resolvido devido a razões de continuidade de segurança e empresariais. 

### <a name="handling-single-failures"></a>Processamento de falhas único
Podem falhar máquinas únicas para todos os tipos de motivos. Alguns deles são causas de hardware, como fontes de alimentação e falhas de hardware de rede. Outras falhas são no software. Estes incluem falhas do sistema operativo real e o próprio serviço. Service Fabric Deteta automaticamente estes tipos de falhas, incluindo casos onde a máquina fica isolada de outras máquinas devido a problemas de rede.

Independentemente do tipo de serviço, com uma única instância resultados no período de inatividade para que o serviço se esse cópia única do código falhar por algum motivo. 

Para poder processar quaisquer falhas único, a situação mais simples, pode fazê-lo é Certifique-se de que os serviços executados em mais do que um nó por predefinição. Para os serviços sem monitorização de estado, isto pode ser feito ao ter um `InstanceCount` superior a 1. Para os serviços com monitorização de estado, a recomendação mínimo é sempre um `TargetReplicaSetSize` e `MinReplicaSetSize` de, pelo menos, 3. Executar mais cópias da código do serviço assegura que o serviço pode processar qualquer falha único automaticamente. 

### <a name="handling-coordinated-failures"></a>Falhas de processamento coordenado
Falhas de coordenada podem acontecer em cluster devido a uma planeada ou falhas de infraestrutura não planeada e as alterações ou alterações ao software planeada. Service Fabric modelos zonas de infraestrutura que ocorrer falhas coordenadas como domínios de falhas. Áreas que irão ocorrer alterações ao software coordenada são modeladas como domínios de atualização. Obter mais informações sobre domínios de falhas e a atualização estão a ser [neste documento](service-fabric-cluster-resource-manager-cluster-description.md) que descreve a definição e a topologia de cluster.

Por predefinição o Service Fabric considera domínios de falhas e de atualização quando planear onde devem executar os serviços. Por predefinição, o Service Fabric tenta garantir que os seus serviços executam em vários domínios de falhas e a atualização para que o se acontecer alterações planeadas ou imprevistas os seus serviços permanecem disponíveis. 

Por exemplo, vamos supor que o se a falha de uma origem de energia faz com que um bastidor de máquinas para falhem em simultâneo. Com várias cópias do serviço em execução a perda de várias máquinas em falha de domínio de falhas ativa para apenas outro exemplo de falha único para um determinado serviço. Esta é a razão pela qual a gestão de domínios de falhas é fundamental para assegurar elevada disponibilidade dos seus serviços. Ao executar o Service Fabric no Azure, os domínios de falhas são geridos automaticamente. Nos outros ambientes, não podem ser. Se estiver a criar os seus próprios clusters no local, lembre-se de que mapeiam e planear a esquema de domínio de falhas corretamente.

Domínios de atualização são úteis para a modelação áreas onde software vai ser atualizado ao mesmo tempo. Por este motivo, os domínios de atualização também, muitas vezes, defina os limites de onde o software é desativado durante atualizações planeadas. Atualizações do Service Fabric e os serviços siga o mesmo modelo. Para mais informações sobre a atualização, domínios de atualização e o modelo de estado de funcionamento de recursos de infraestrutura de serviço que ajuda a impedir que as alterações não intencional afetar o cluster e o seu serviço, consulte estes documentos:

 - [Atualização da aplicação](service-fabric-application-upgrade.md)
 - [Tutorial de atualização de aplicações](service-fabric-application-upgrade-tutorial.md)
 - [Modelo de serviço de estado de funcionamento de recursos de infraestrutura](service-fabric-health-introduction.md)

Pode visualizar o esquema do cluster utilizando o mapa de cluster fornecido no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>
![Nós distribuídos por domínios de falhas no Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modelação áreas de falha, a atualizações, executar várias instâncias do seu código do serviço e o estado, regras de colocação para garantir que os serviços de executam em vários domínios de falhas e a atualização e monitorização de estado de funcionamento incorporado são apenas **algumas** do funcionalidades que o Service Fabric fornece para manter os problemas operacionais normais e falhas de ativar para perante desastres. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Processamento de falhas de hardware ou software simultâneas
Acima abordámos falhas único. Como pode ver, são fáceis de utilizar para serviços sem monitorização de estado e com monitorização de estado mantendo mais cópias da código (e o estado) em execução em falhas e domínios de atualização. Também podem acontecer várias falhas aleatórias em simultâneo. Estas são mais prováveis levar a um desastre real.


### <a name="random-failures-leading-to-service-failures"></a>Falhas aleatórias, resultando em falhas de serviço
Digamos que o serviço tinha um `InstanceCount` de 5 e vários nós que executa essas instâncias de todos os falhou ao mesmo tempo. Service Fabric responde criando automaticamente instâncias de substituição nos outros nós. Irá continuar a criar substituições até que o serviço seja novamente à respetiva contagem de instâncias pretendido. Outro exemplo, vamos supor que ocorreu um serviço sem monitorização de estado com um `InstanceCount`de -1, que significa que é executada em todos os nós de cluster válidos. Imaginemos que algumas das instâncias estavam a falhar. Neste caso, o Service Fabric avisos que o serviço não se encontra no estado pretendido e tenta criar as instâncias em nós em que estão em falta. 

Para serviços com monitorização de estado a situação depende se o serviço manteve Estado ou não. Além de depender como muitas réplicas tinha o serviço e quantos falhou. Determinar se um desastre ocorreu para um serviço com estado e geri-lo segue três fases:

1. Determinar se tiver ocorrido perda de quórum ou não
 - A perda de quórum é sempre que a maioria das réplicas de um serviço com monitorização de estado estão inativos, ao mesmo tempo, incluindo o site primário.
2. Determinar se a perda de quórum está ou não permanente
 - Na maioria das vezes, as falhas são transitórias. Os processos são reiniciados, nós são reiniciados, VMs são relaunched, heal de partições de rede. Por vezes, falhas são permanentes. 
    - Para serviços persistentes sem estado, uma falha de um quórum, ou mais dos resultados de réplicas _imediatamente_ na perda de quórum permanente. Quando o Service Fabric Deteta a perda de quórum num serviço com monitorização de estado não persistente, imediatamente prossegue para o passo 3 por declarar dataloss (potenciais). Avançar para o dataloss faz sentido porque Service Fabric sabe que não há nenhum ponto de aguardar que as réplicas voltar atrás, porque o mesmo foram recuperados deverá estar vazios.
    - Para os serviços com monitorização de estado persistentes, uma falha de um quórum, ou mais das réplicas faz com que o Service Fabric iniciar a aguardar que as réplicas voltar atrás e restaurar o quórum. Como resultado, uma interrupção do serviço para qualquer _escreve_ para a partição afetada (ou o "conjunto de réplicas") do serviço. No entanto, ainda podem ser possíveis com garantias de consistência reduzida leituras. O período de tempo que o Service Fabric aguarda quórum ser restauradas predefinido é infinito, uma vez que prosseguir é um evento de dataloss (potenciais) e acarreta riscos de outros. Substituir a predefinição `QuorumLossWaitDuration` valor for possível, mas não é recomendada. Em vez disso, neste momento, todos os esforços devem ser efetuados para restaurar as réplicas baixo. Isto requer que levam os nós que estão indisponíveis de cópia de segurança e garantir que estes podem remontar as unidades que armazenar o estado persistente local. Se a perda de quórum é causada por falhas do processo, o Service Fabric automaticamente tenta recriar os processos e reiniciar as réplicas dentro deles. Se isto falhar, Service Fabric relatórios de erros de estado de funcionamento. Se estes podem ser resolvidos, em seguida, as réplicas normalmente voltar atrás. Por vezes, no entanto, as réplicas não podem ser recuperadas. Por exemplo, as unidades de todas as ter falhado ou as máquinas fisicamente destruído preso de algum modo. Nestes casos, temos agora um evento de perda de quórum permanente. Para saber se o Service Fabric para parar a aguardar que as réplicas para baixo voltar atrás, um administrador de cluster tem de determinar quais as partições que serviços são afetados e chame o `Repair-ServiceFabricPartition -PartitionId` ou ` System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API.  Esta API permite especificar o ID da partição mover fora QuorumLoss e para dataloss potenciais.

> [!NOTE]
> É _nunca_ seguro utilizar esta API diferente de uma forma visada contra partições específicas. 
>

3. Determinar se foi perda de dados real e o restauro de cópias de segurança
  - Quando o Service Fabric chama o `OnDataLossAsync` método é sempre porque _suspeito_ dataloss. Service Fabric garante que esta chamada é entregue ao _melhor_ réplica restantes. Este é qualquer réplica foi tornada o progresso da maioria dos. O motivo pelo qual podemos sempre diga _suspeito_ dataloss é que é possível que a réplica restantes, na verdade, tem mesmo de estado todas as principais foram quando entrou para baixo. No entanto, sem esse Estado de comparação, não há nenhum boa forma de Service Fabric ou operadores saber por se. Neste momento, o Service Fabric também conheça a que outras réplicas não são regressam. Que estava a decisão tomado quando foi parado a aguardar a perda de quórum seja resolvido automaticamente. O melhor método de ação para o serviço é, normalmente parar e a aguardar a intervenção administrativa específica. Por isso, o que faz uma implementação típica do `OnDataLossAsync` método fazer?
  - Primeiro, inicie a sessão que `OnDataLossAsync` foi acionada e desativar quaisquer alertas administrativas necessárias.
   - Normalmente, neste momento, para colocar em pausa e aguardar decisões adicionais e ações manuais ser executada. Isto acontece porque, mesmo se as cópias de segurança estão disponíveis poderão ter de ser preparado. Por exemplo, se dois serviços diferentes coordenam informações, as cópias de segurança poderão ter de ser modificados para garantir que depois do restauro acontece que as informações cuidado esses dois serviços sobre é consistente. 
  - Muitas vezes, há também alguns outra telemetria ou a esgotar os do serviço. Estes metadados podem ser contidos nos outros serviços ou nos registos. Estas informações podem ser utilizadas necessárias para determinar se existiam quaisquer chamadas recebidas e processadas no principal que não estavam presentes na cópia de segurança ou replicadas para esta réplica específica. Estes poderão ter de ser reproduzido ou adicionada à cópia de segurança antes do restauro é exequível.  
   - Comparações do Estado da réplica restantes para contidos em qualquer cópias de segurança que estão disponíveis. Se utilizar as coleções fiáveis de Service Fabric, em seguida, existem ferramentas e processos disponível para fazê-lo, descrita [neste artigo](service-fabric-reliable-services-backup-restore.md). O objetivo é para ver se o estado dentro da réplica é suficiente, ou também que a cópia de segurança pode estar em falta.
  - Depois da comparação é feita, e se for necessário o restauro foi concluído, o código do serviço deve devolver VERDADEIRO se foram efetuadas alterações de estado. Se a réplica determinado que estava a melhor cópia do Estado e efetuadas sem alterações, devolve false. VERDADEIRO indica que qualquer _outros_ restantes réplicas podem agora ser inconsistente com este. Irá ser removidos e reconstruídos a partir desta réplica. FALSO indica que foram efetuadas sem alterações de estado, pelo que as outras réplicas podem manter a tem. 

É extremamente importante que os autores de serviço praticar dataloss potenciais cenários e falhas antes de serviços nunca estão implementados na produção. Para proteger contra a possibilidade de dataloss, é importante periodicamente [cópia de segurança do estado](service-fabric-reliable-services-backup-restore.md) de qualquer um dos seus serviços com monitorização de estado para um armazenamento georredundante. Tem também Certifique-se de que tem a capacidade de restaurá-lo. Uma vez que são efetuadas cópias de segurança de vários serviços diferentes em momentos diferentes, tem de garantir que, após um restauro, os serviços têm uma vista consistente de si. Por exemplo, considere uma situação em que um serviço de gera um número e armazena-os, em seguida, envia-a para outro serviço que armazena-os também. Após um restauro, poderá detetar que o serviço de segundo tem o número, mas o primeiro não, porque se trata de cópia de segurança não incluem essa operação.

Se lhe saber que as réplicas restantes são suficientes continuar a partir de um cenário de dataloss e não é possível reconstrua o estado do serviço de telemetria ou a esgotar os, a frequência das cópias de segurança de determina o melhor objetivo de ponto de recuperação (RPO). O Service Fabric fornece diversas ferramentas para vários cenários de falha, incluindo quórum permanente e dataloss necessidade de restauro de uma cópia de segurança de teste. Estes cenários são incluídos como parte das ferramentas de teste de Service Fabric, geridas pelo serviço de análise de falhas. Obter mais informações sobre os padrões e ferramentas estão disponível [aqui](service-fabric-testability-overview.md). 

> [!NOTE]
> Serviços do sistema podem também ser afetado e perda de quórum, com o impacto que está a ser específicos do serviço em questão. Por exemplo, perda de quórum no serviço de nomenclatura afeta a resolução de nomes, enquanto que a perda de quórum no serviço do Gestor de ativação pós-falha bloqueia a criação do novo serviço e as ativações pós-falha. Enquanto os serviços de sistema do Service Fabric siga o mesmo padrão, como os serviços de gestão do Estado, não se recomenda que deve tentar movê-los fora de perda de quórum e para dataloss potenciais. A recomendação é em vez disso, [procurar o ressarcimento de suporte](service-fabric-support.md) para determinar uma solução que se destina à sua situação específica.  Normalmente, é preferível aguardar simplesmente até as réplicas baixo devolvem.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilidade do cluster Service Fabric
Um modo geral, o próprio cluster do Service Fabric é um ambiente altamente distribuído com sem pontos únicos de falha. Uma falha de um nó não irá causar problemas de fiabilidade para o cluster, ou disponibilidade principalmente porque os serviços de sistema do Service Fabric seguem as diretrizes mesmas fornecidas anteriormente: estes são sempre executadas com três ou mais réplicas por predefinição e essas sistema serviços que estão sem monitorização de estado são executados em todos os nós. Os subjacentes Service Fabric falha e redes de deteção camadas totalmente são distribuídas. A maioria dos serviços do sistema podem ser reconstruídos de metadados no cluster ou sabem como ressincronizar o respetivo estado de outros locais. A disponibilidade do cluster pode ficar comprometida se obter serviços do sistema no quórum perda situações, como as descritas acima. Nestes casos, poderá não conseguir efetuar determinadas operações no cluster como iniciar uma atualização ou implementação de novos serviços, mas o próprio cluster é ainda uma cópia de segurança. Serviços em já em execução continuará em execução nestas condições, a menos que necessitam de operações de escrita para os serviços do sistema para continuar a funcionar. Por exemplo, se o Gestor de ativação pós-falha em perda de quórum todos os serviços continuará a ser executado, mas quaisquer serviços que não cumpram não será possível reiniciar automaticamente, uma vez que isto requer o envolvimento do Gestor de ativação pós-falha. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Falhas de um centro de dados ou a região do Azure
Em casos raros, um centro de dados físicos pode tornar-se temporariamente indisponível devido a perda de conectividade de rede ou de energia. Nestes casos, os clusters de Service Fabric e serviços que Centro de dados ou a região do Azure estará indisponíveis. No entanto, _os dados são preservados_. Para clusters em execução no Azure, pode ver as atualizações de falhas no [página de estado do Azure][azure-status-dashboard]. O evento muito pouco provável que o Centro de dados física é total ou parcialmente destruído, quaisquer clusters de recursos de infraestrutura do serviço alojados não existe ou os serviços dentro deles poderão ser perdidos. Isto inclui qualquer Estado não um cópia de segurança fora desse datacenter ou a região.

Há dois estratégias diferentes para reiniciadas falha permanente ou constante de um único centro de dados ou uma região. 

1. Executar clusters separados de Service Fabric em várias essas regiões e utilizar algum mecanismo de ativação pós-falha e falhas de retorno entre estes ambientes. Este tipo de modelo de ativo-ativo ou ativo-passivo do cluster multi requer códigos adicionais de gestão e operações. Este procedimento também requer a coordenação de cópias de segurança dos serviços de um centro de dados ou região, de modo a que estão disponíveis outros centros de dados ou regiões quando um falha. 
2. Execute um único cluster de Service Fabric que abranja vários centros de dados ou regiões. A configuração mínima suportada para esta situação é três centros de dados ou regiões. O número recomendado de regiões ou centros de dados for igual a cinco. Isto requer uma topologia de cluster mais complexa. No entanto, a vantagem deste modelo é que a falha de um centro de dados ou a região é convertida a partir de um desastre uma falha normal. Estas falhas podem ser processadas pelo mecanismos que funcionam para clusters numa única região. Domínios de falhas, regras de posicionamento de Service Fabric e domínios de atualização, certifique-se de que as cargas de trabalho são distribuídas para que estes toleram falhas normais. Para obter mais informações sobre políticas que podem ajudá-lo a funcionar serviços neste tipo de cluster, ler sobre [políticas de colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Falhas aleatórias, resultando em falhas de cluster
Service Fabric tem o conceito de nós Seed. Estes são nós que mantêm a disponibilidade do cluster subjacente. Estes nós ajudam a garantir que o cluster permanecer cópias de segurança pelo estabelecimento concessões com outros nós e que serve como tiebreakers durante determinados tipos de falhas de rede. Se falhas aleatórias remover uma maioria de nós seed do cluster e não são recuperados, o cluster automaticamente encerrado. No Azure, são automaticamente geridas nós Seed: que são distribuídas através de falhas disponível e de domínios de atualização e, se um nó de seed único é removido do cluster outro será criado no seu lugar. 

Nos clusters de Service Fabric autónomos e Azure, o "tipo de nó primário" é o que é executado os seeds. Ao definir um tipo de nó principal, Service Fabric será automaticamente tirar partido do número de nós fornecida através da criação até 9 seed nós e 9 réplicas de cada um dos serviços de sistema. Se um conjunto de falhas aleatórias demora enviados a maioria desses réplicas de serviço do sistema em simultâneo, os serviços do sistema entrará em perda de quórum, conforme é descrito acima. Se uma maioria de nós seed é perdida, o cluster será encerrado logo após.

## <a name="next-steps"></a>Passos seguintes
- Saiba como simular várias falhas utilizando o [framework de teste](service-fabric-testability-overview.md)
- Ler outros recursos de recuperação após desastre e elevada disponibilidade. Microsoft ter publicado uma grande quantidade de documentação de orientação estes tópicos. Embora alguns destes documentos se refiram ao técnicas específicas para utilização em outros produtos, que contêm muitos gerais melhores práticas que pode aplicar no contexto de Service Fabric:
  - [Lista de verificação de disponibilidade](../best-practices-availability-checklist.md)
  - [Efetuar um exercício de recuperação após desastre](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Recuperação após desastre e elevada disponibilidade para aplicações do Azure][dr-ha-guide]
- Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
