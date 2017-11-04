---
title: "Resolução de problemas e a monitorização de SAP HANA no Azure (instâncias de grande) | Microsoft Docs"
description: "Resolver problemas e monitorizar SAP HANA num (instâncias de grande) do Azure."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5583f3d1949614dbba4d2f91d72e4ac6b4d03d1c
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="how-to-troubleshoot-and-monitor-sap-hana-large-instances-on-azure"></a>Como resolver problemas e monitorizar SAP HANA (instâncias de grande) no Azure


## <a name="monitoring-in-sap-hana-on-azure-large-instances"></a>Monitorização de SAP HANA no Azure (instâncias de grandes)

SAP HANA no Azure (instâncias de grande) é igual a partir de qualquer outra implementação de IaaS — precisar de monitorizar a que o SO e a aplicação está a fazer e como estes consumam os seguintes recursos:

- CPU
- Memória
- Largura de banda de rede
- Espaço em disco

Com máquinas virtuais do Azure que precisar descobrir se as classes de recurso denominadas acima são suficientes ou se estes obterem esgotados. Eis mais detalhes em cada um dos diferentes classes:

**Consumo de recursos de CPU:** o rácio entre o SAP definida para algumas cargas de trabalho contra HANA é imposto para se certificar de que deve ser suficiente recursos de CPU disponíveis para completar os dados que são armazenados na memória. Contudo, poderão existir casos onde HANA consome muita das consultas em execução de CPU devido a índices em falta ou problemas semelhantes. Isto significa que, deve monitorizar o consumo de recursos de CPU de unidade de instância grande HANA, bem como os recursos de CPU consumidos pelos serviços de HANA específicos.

**Consumo de memória:** é importante para a monitorização a partir de dentro de HANA, bem como fora HANA na unidade. Dentro do HANA, monitorize a forma como os dados está a consumir HANA atribuída memória para permanecer nas diretrizes de dimensionamento necessárias do SAP. Também querer monitorizar o consumo de memória no nível da instância grande para se certificar de que software adicional instalado não-HANA não consumir demasiada memória e, por conseguinte, com HANA competem para ter memória.

**Largura de banda de rede:** gateway a VNet do Azure é limitado em largura de banda de dados mover para a VNet do Azure, pelo que é útil monitorizar os dados recebidos por todas as VMs do Azure numa VNet para descobrir como fechar são os limites do gateway do Azure SKU que selecionou. Na unidade instância grande HANA,-fazer sentido para monitorizar a entrada e saída tráfego de rede bem e para controlar os volumes que são processados ao longo do tempo.

**Espaço em disco:** consumo de espaço em disco é normalmente aumenta ao longo do tempo. Existem muitos motivos para tal, mas a maioria de todos os são: o volume de dados aumenta, execução de transação registo cópias de segurança, armazenar os ficheiros de rastreio e efetuar instantâneos de armazenamento. Por conseguinte, é importante monitorizar a utilização de espaço em disco e gerir o espaço em disco associado à unidade de instância grande HANA.

Para o **tipo II SKUs** das instâncias HANA grande, o servidor ficar com as ferramentas de diagnóstico do sistema pré-carregada. Pode utilizar estas ferramentas de diagnóstico para efetuar a verificação de estado de funcionamento do sistema. Execute o seguinte comando para gerar o ficheiro de registo de verificação do Estado de funcionamento em /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Quando trabalha com a equipa de Support da Microsoft para resolver um problema, pode também ser pedido para fornecer os ficheiros de registo utilizando estes ferramenta de diagnóstico. Pode zip o ficheiro utilizando o seguinte comando.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```


## <a name="monitoring-and-troubleshooting-from-hana-side"></a>Monitorização e resolução de problemas do lado HANA

Para analisar eficazmente os problemas relacionados com o SAP HANA no Azure (instâncias de grande), é útil restringir a causa de raiz de um problema. SAP publicou uma grande quantidade de documentação para ajudá-lo.

Perguntas mais frequentes aplicáveis relacionados com o desempenho de SAP HANA podem ser encontrados nas seguintes notas de SAP:

- [A nota #2222200 – FAQ: SAP HANA rede](https://launchpad.support.sap.com/#/notes/2222200)
- [A nota #2100040 – FAQ: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [A nota #199997 – FAQ: SAP HANA memória](https://launchpad.support.sap.com/#/notes/2177064)
- [Nota SAP #200000 – FAQ: Otimização do desempenho SAP HANA](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP nota #199930 – FAQ: Análise de e/s SAP HANA](https://launchpad.support.sap.com/#/notes/1999930)
- [A nota #2177064 – FAQ: SAP HANA serviço reiniciar e falhas](https://launchpad.support.sap.com/#/notes/2177064)

**SAP HANA alertas**

Como primeiro passo, consulte os registos de alerta de SAP HANA atuais. SAP HANA Studio, aceda a **consola de administração: alertas: Mostrar: todos os alertas**. Este separador mostra todos os alertas de SAP HANA por valores específicos (memória física livre, a utilização da CPU, etc.) que se inserem fora dos limiares de mínimo e máximo do conjunto. Por predefinição, as verificações de são atualizados de automática a cada 15 minutos.

![No Studio do SAP HANA, aceda à consola de administração: alertas: Mostrar: todos os alertas](./media/troubleshooting-monitoring/image1-show-alerts.png)

**CPU**

Um alerta acionado devido à definição de limiar incorrecto, uma resolução é reposto para o valor predefinido ou um valor de limiar mais razoável.

![Repor o valor predefinido ou um valor de limiar razoável mais](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Os seguintes alertas podem indicar problemas de recursos de CPU:

- Utilização da CPU do anfitrião (5 alertas)
- Operação de ponto de reposição mais recente (28 alerta)
- Duração de ponto de reposição (54 em alertas)

Poderá reparar elevado consumo de CPU na base de dados SAP HANA a partir de um dos seguintes procedimentos:

- 5 (utilização da CPU do anfitrião) do alerta é gerado para utilização de CPU atual ou passada
- A utilização da CPU apresentada no ecrã descrição geral

![Apresentar a utilização da CPU no ecrã descrição geral](./media/troubleshooting-monitoring/image3-cpu-usage.png)

O gráfico de carga poderá mostrar elevado consumo de CPU ou de elevado consumo no passado:

![O gráfico de carga poderá mostrar elevado consumo de CPU ou de elevado consumo no passado](./media/troubleshooting-monitoring/image4-load-graph.png)

Um alerta acionado devido a elevada utilização da CPU pode ser causado por vários motivos, incluindo, mas não se limitando: execução de determinados transações, carregamento de dados, pendente de tarefas, execução longa instruções SQL e o desempenho de consulta inválido (por exemplo, com BW em cubos HANA).

Consulte o [resolução de problemas de SAP HANA: CPU relacionados faz com que e soluções](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) para os passos de resolução de problemas detalhada do site.

**Sistema operativo**

Uma das verificações mais importantes para SAP HANA no Linux é Certifique-se de que as páginas enorme transparente estão desativadas, consulte [2131662 de n. º de nota SAP – transparente enorme páginas (THP) nos servidores do SAP HANA](https://launchpad.support.sap.com/#/notes/2131662).

- Pode verificar se as páginas enorme transparente estão ativadas através do seguinte comando do Linux: **cat /sys/kernel/mm/transparent\_hugepage/ativa**
- Se _sempre_ é colocado entre parênteses Retos, tal como indicado abaixo, significa que as páginas enorme transparente estão ativadas: [sempre] madvise nunca; se _nunca_ é colocado entre parênteses Retos, tal como indicado abaixo, significa que as páginas enorme transparente estão desativadas: sempre madvise [nunca]

O seguinte comando do Linux deverá devolver nada: **rpm - pergunta e resposta | grep ulimit.** Se parecer que _ulimit_ é instalado, desinstale-a imediatamente.

**Memória**

Poderá reparar que a quantidade de memória atribuída pela base de dados SAP HANA é superior ao esperado. Os seguintes alertas indicam problemas com utilização elevada da memória:

- Utilização de memória física do anfitrião (1 alerta)
- Utilização da memória de servidor de nomes (12 alerta)
- Utilização da memória total das tabelas de arquivo de coluna (40 alerta)
- Utilização de memória dos serviços (43 alerta)
- Utilização de memória de armazenamento principal de tabelas de arquivo de coluna (45 alerta)
- Ficheiros de informação de tempo de execução (46 alerta)

Consulte o [resolução de problemas de SAP HANA: problemas de memória](http://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) para os passos de resolução de problemas detalhada do site.

**Rede**

Consulte [2081065 de n. º de nota SAP – resolução de problemas de rede do SAP HANA](https://launchpad.support.sap.com/#/notes/2081065) e efetuar a passos este a nota de resolução de problemas de rede.

1. Analisar reportadas round-trip tempo entre o servidor e cliente.
  A. Execute o script SQL [ _HANA\_rede\_clientes_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analise a comunicação internós.
  A. Executar script de SQL [ _HANA\_rede\_serviços_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Execute o comando Linux **ifconfig** (o resultado mostra se quaisquer perdas de pacotes estão a ocorrer).
4. Execute o comando Linux **tcpdump**.

Além disso, utilize de open source [IPERF](https://iperf.fr/) ferramenta (ou semelhante) para medir o desempenho da rede real da aplicação.

Consulte o [resolução de problemas de SAP HANA: desempenho de rede e problemas de conectividade](http://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) para os passos de resolução de problemas detalhada do site.

**Armazenamento**

De uma perspetiva do utilizador final, uma aplicação (ou o sistema como um todo) é executado sluggishly, não responde ou mesmo pode parecer fique suspensa se existem problemas de desempenho de e/s. No **Volumes** separador SAP HANA Studio, pode ver os volumes anexados, e os volumes são utilizados por cada serviço.

![No separador de Volumes no SAP HANA Studio, pode ver os volumes anexados, e os volumes são utilizados por cada serviço](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Ligado volumes na parte inferior do ecrã, pode ver os detalhes dos volumes, tais como ficheiros e estatísticas de e/s.

![Ligado volumes na parte inferior do ecrã, pode ver os detalhes dos volumes, tais como ficheiros e estatísticas de e/s](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Consulte o [resolução de problemas de SAP HANA: e/s relacionados causas e soluções](http://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) e [resolução de problemas de SAP HANA: disco relacionados causas e soluções](http://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) para os passos de resolução de problemas detalhada do site.

**Ferramentas de diagnóstico**

Executar uma verificação de estado de funcionamento do SAP HANA através de HANA\_configuração\_Minichecks. Esta ferramenta devolve potencialmente críticos problemas técnicos que devem já ter sido gerados como alertas no Studio do SAP HANA.

Consulte [1969700 de n. º de nota SAP – coleção de instrução de SQL para SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) e transfira o ficheiro de SQL Statements.zip ligado para que tenha em atenção. Armazene este ficheiro. zip no disco rígido local.

No SAP HANA Studio, no **informações do sistema** separador, clique com o botão direito no **nome** coluna e selecione **instruções SQL de importação**.

![No SAP HANA Studio, no separador de informações do sistema, faça duplo clique na coluna de nome e selecione as instruções de SQL de importação](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Selecione o ficheiro de SQL Statements.zip armazenado localmente, e uma pasta com as instruções de SQL correspondentes será importada. Neste momento, as verificações de diagnóstico diferentes muitos podem ser executadas com estas instruções SQL.

Por exemplo, para testar os requisitos de largura de banda de SAP HANA sistema replicação, faça duplo clique o **largura de banda** instrução em **replicação: largura de banda** e selecione **abra** na consola do SQL Server.

A instrução SQL completa abre-se permitir que os parâmetros de entrada (secção de modificação) para ser alterada e, em seguida, executar.

![A instrução SQL completa abre-se permitir que os parâmetros de entrada (secção de modificação) para ser alterada e, em seguida, executar](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Outro exemplo é right-clicking nas instruções em **replicação: Descrição geral**. Selecione **executar** no menu de contexto:

![Outro exemplo é right-clicking nas declarações de replicação: Descrição geral. Selecione executar no menu de contexto](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Isto resulta nas informações que ajuda a resolver o problema:

![Este procedimento resultará num informações que o irão ajudar na resolução de problemas](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Faça o mesmo para HANA\_configuração\_Minichecks e verificação para qualquer _X_ marca no _C_ coluna (crítico).

Saídas de exemplo:

**HANA\_configuração\_MiniChecks\_Rev102.01 + 1** para verifica geral SAP HANA.

![HANA\_configuração\_MiniChecks\_Rev102.01 + 1 para verificações de SAP HANA gerais](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_serviços\_descrição geral** para uma descrição geral de que os serviços de SAP HANA estão atualmente em execução.

![HANA\_serviços\_descrição geral para uma descrição geral de que os serviços de SAP HANA estão atualmente em execução](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_serviços\_estatísticas** para SAP HANA service informações (CPU, memória, etc.).

![HANA\_serviços\_as estatísticas de SAP HANA informações de serviço ](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_configuração\_descrição geral\_Rev110 +** para obter informações gerais sobre a instância de SAP HANA.

![HANA\_configuração\_descrição geral\_Rev110 + para obter informações gerais sobre a instância de SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_configuração\_parâmetros\_Rev70 +** para verificar os parâmetros de SAP HANA.

![HANA\_configuração\_parâmetros\_Rev70 + para verificar os parâmetros de SAP HANA](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

