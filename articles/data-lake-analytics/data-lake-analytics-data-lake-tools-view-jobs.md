---
title: Utilizar o Browser de tarefa e vista de tarefas para tarefas do Azure Data Lake Analytics | Microsoft Docs
description: 'Saiba como utilizar o Browser de tarefa e vista de tarefas para tarefas do Azure Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2017
ms.author: jgao
ms.openlocfilehash: 8f1729f84a4fde2a56427a41b356d6263818519e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics-jobs"></a>Utilizar o Browser de tarefa e vista de tarefas para tarefas do Azure Data lake Analytics
O serviço de Azure Data Lake Analytics arquiva tarefas submetidas um [arquivo de consultas](#query-store). Neste artigo, irá aprender a utilizar o Browser de tarefa e vista de tarefas no Azure Data Lake Tools para Visual Studio para encontrar as informações de histórico da tarefa. 

Por predefinição, o serviço do Data Lake Analytics, arquiva as tarefas durante 30 dias. O período de expiração pode ser configurado do portal do Azure, ao configurar a política de expiração personalizado. Não será capaz de aceder as informações de tarefa após a expiração. 

## <a name="prerequisites"></a>Pré-requisitos
Consulte [ferramentas do Data Lake para pré-requisitos do Visual Studio](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Abra o Browser de tarefa
O Browser de tarefa através de acesso **Explorador de servidores > Azure > do Data Lake Analytics > tarefas** no Visual Studio.  Utilizar o Browser de tarefa, pode aceder o arquivo de consultas de uma conta de Data Lake Analytics. Tarefa Browser mostra o arquivo de consultas no lado esquerdo, as informações básicas de tarefa e as informações da tarefa de detalhado de vista de tarefas no apresentado à direita.

## <a name="job-view"></a>Vista de tarefas
Vista de tarefas mostra as informações detalhadas de uma tarefa. Para abrir uma tarefa, pode faça duplo clique uma tarefa no Browser da tarefa ou abri-lo a partir do menu de Data Lake, clicando em vista de tarefas. Deverá ver uma caixa de diálogo preenchida com o URL de tarefa.

![Data Lake Tools Browser de tarefa do Visual Studio](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Contém a vista de tarefas:

* Resumo da Tarefa
  
    Atualize a vista de tarefa para ver as informações mais recentes de tarefas em execução.
  
  * Estado da tarefa (gráfico):
    
      Estado da tarefa destaca as fases de tarefa:
    
      ![Estado de fases de tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * A preparar: Carregue o script para a nuvem, a compilação e otimizar o script utilizando o serviço de compilação.
    * Em fila: As tarefas são whey em fila estão a aguardar recursos suficientes ou as tarefas de excedem as máximas tarefas simultâneas por limitação de conta. A definição de prioridade determina a sequência de tarefas em fila - a inferior o número, maior prioridade.
    * Em execução: A tarefa, na verdade, está em execução na sua conta do Data Lake Analytics.
    * A finalizar: A tarefa está a ser concluída (por exemplo, a finalizar o ficheiro).
      
      A tarefa pode falhar em cada fase. Por exemplo, erros de compilação na fase preparar, erros de tempo limite na fase de em fila e erros de execução na fase de execução, etc.
  * Informações básicas
    
      Mostram as informações básicas de tarefas na parte inferior do painel de resumo da tarefa.
    
      ![Estado de fases de tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Resultado da tarefa: Foi concluída com êxito ou falha. A tarefa pode falhar em cada fase.
    * Duração total: Padrão de fundo tempo de relógio (duração) entre a submeter o tempo e a hora de fim.
    * O tempo total de computação: A soma de todos os tempo de execução de vértice, pode considerá-lo como o tempo que a tarefa for executada no vértice apenas um. Consulte vértices Total para obter mais informações sobre o vértice.
    * Hora de envio/início/fim: A hora quando o serviço do Data Lake Analytics recebe tarefa submissão/inicia, para executar a tarefa/terminar a tarefa com êxito ou não.
    * Em fila/compilação/execução: Tempo de relógio de lateral gasto durante a fase preparar/em fila/em execução.
    * : A Data Lake Analytics conta utilizada para executar a tarefa.
    * Autor: O utilizador submetido a tarefa pode ser a conta de uma pessoa real ou uma conta de sistema.
    * Prioridade: A prioridade do trabalho. Menor número, maior prioridade. Afeta apenas a sequência de tarefas na fila. Definir uma prioridade mais alta não alta impedem mais tarefas em execução.
    * Paralelismo: O pedido número máximo de simultâneas unidades do Azure Data Lake análise (ADLAUs), aka vértices. Atualmente, um vértice é igual a uma VM com dois núcleos virtual e seis GB de RAM, apesar de Isto pode ser atualizado no futuro do Data Lake Analytics atualizações.
    * Bytes restantes: Total de Bytes que têm de ser processado enquanto a tarefa for concluída.
    * Bytes escritos/leitura: Bytes que foram leitura/escrita desde a tarefa foi iniciada em execução.
    * Total de vértices: A tarefa é dividida cópias de segurança em trabalho de várias partes, cada trabalho de peça é chamado um vértice. Este valor descreve quantos peças de trabalho consiste a tarefa. Pode considerar um vértice como uma unidade de processo básico, também conhecido como Azure Data Lake Analytics unidade (ADLAU), e vértices podem ser executadas em paralelismo. 
    * Concluir/em execução/com falhas: A contagem de vértices concluída/em execução/falhou. Vértices podem falhar devido a ambas as falhas de código e de sistema do utilizador, mas as repetições de sistema falhou vértices automaticamente algumas vezes. Se o vértice ainda é falha depois de repetir, a tarefa Toda irá falhar.
* Gráfico da tarefa
  
    Um script U-SQL representa a lógica de transformar dados de entrada em dados de saída. O script é compilado e otimizado para um plano de execução físico para a fase preparar. Gráfico da tarefa é para mostrar o plano de execução físico.  O diagrama seguinte ilustra o processo:
  
    ![Estado de fases de tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Uma tarefa é dividida cópias de segurança em trabalho de várias partes. Cada trabalho de peça é chamado um vértice. Vértices são agrupados como Super vértice (aka fase) e visualizados como gráfico da tarefa. As fase verde placards no gráfico da tarefa mostram as fases.
  
    Cada vértice numa fase está a fazer o mesmo tipo de trabalho com diferentes partes dos mesmos dados. Por exemplo, se tiver um ficheiro com um de TB de dados, e não existirem centenas de vértices ao ler a partir dos mesmos, cada um deles está a ler um segmento. Esses vértices são agrupadas no mesmo fase e fazer mesmo funciona em diferentes partes do mesmo ficheiro de entrada.
  
  * <a name="state-information"></a>Informações de fase
    
      Numa fase específica, são apresentados algumas números o placard.
    
      ![Fase de gráfico da tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 Extrair: O nome de uma fase, designado por um número e o método de operação.
    * 84 vértices: A contagem total de vértices nesta fase. A figura indica quantos trabalho de peças está dividido nesta fase.
    * s/vértice 12.90: O tempo de execução de vértice médio para esta fase. Desta figura é calculada pela soma (cada tempo de execução de vértice) / (contagem de vértice total). O que significa se foi possível atribuir todos os vértices executados na paralelismo, a fase de toda é concluída no 12.90 s. Isto também significa que se todo o trabalho nesta fase é feito serialmente, o custo seria #vertices * Méd do tempo.
    * 850,895 linhas escritas: Total de contagem de linhas escrita nesta fase.
    * R/w quantidade de dados de leitura/Written nesta fase em bytes.
    * Cores: Cores são utilizadas na fase para indicar o estado de vértice diferentes.
      
      * Verde indica que o vértice é concluída com êxito.
      * Cor de laranja indica que o vértice for repetida. O vertex repetido falhou, mas é repetido com êxito e automaticamente pelo sistema e a fase geral é concluída com êxito. Se o vértice repetida, mas ainda não foi possível, a cor fica vermelho e a tarefa toda falhou.
      * Vermelho indica falha, que significa que um determinado vértice tinha foi repetida algumas vezes pelo sistema, mas ainda não foi possível. Este cenário faz com que a tarefa de toda a falhar.
      * Azul significa que um determinado vértice está em execução.
      * Em branco indica o vértice está à espera. O vértice pode ser a aguardar agendada depois de um ADLAU fica disponível, ou pode ser aguardar a intervenção, desde que os respetivos dados de entrada podem não estar preparados.
      
      Pode encontrar mais detalhes para a fase por posicionado o cursor do rato por um Estado:
      
      ![Detalhes de fase de gráfico da tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Vértices: Descreve os detalhes de vértices, por exemplo, quantas vértices no total, vértices quantas foram concluídas, são que falhou ou continua em execução/em espera, etc.
  * Dados lidos em vários locais/intra pod: ficheiros e os dados são armazenados em várias pods no sistema de ficheiros distribuído. O valor aqui descreve a quantidade de dados foram lidos a mesma pod ou cruzada pod.
  * Total de tempo de computação: A soma de todos os tempo de execução de vértice na fase, pode considerá-lo como o tempo que demoraria se tudo funcionar na fase for executado no vértice apenas um.
  * Linhas de leitura/escrita e de dados: indica quanto dados linhas tem sido leitura/escrita ou tem de ser de leitura.
  * Falhas de leitura de vértice: descreve vértices quantos são falhou ao dados de leitura.
  * Elimina o vértice duplicado: se um vértice executa demasiado lenta, o sistema pode agendar vários vértices para executar o mesmo de trabalho da peça. Vértices reductant serão eliminadas depois de um dos vértices concluída com êxito. Rejeições duplicadas vértice regista o número de vértices são eliminadas como duplications na fase.
  * Revocations vértice: O vértice foi concluída com êxito, mas obter novamente mais tarde devido a algumas razões. Por exemplo, se a jusante vértice perder dados de entrada intermédios, peça o vertex a montante para voltar a executar.
  * Execuções de agenda de vértice: O tempo total que vértices tem sido agendadas.
  * Dados de média/mínimo/máximo vértice lidos: O valor mínimo/médio/máximo de cada vértice ler os dados.
  * : O padrão de fundo relógio tempo de duração Uma fase demora, terá de carregar perfil para ver este valor.
  * Reprodução de Tarefa
    
      Executa tarefas de data Lake Analytics e arquiva vértices com informações de tarefas, tais como quando vértices iniciados, parado, a falha e como são repetidas, etc. Todas as informações é automaticamente registado no arquivo de consultas e armazenadas no seu perfil de tarefa. Pode transferir o perfil de tarefa através de "Carregar perfil" na vista de tarefa e pode ver a reprodução de tarefa depois de transferir o perfil de tarefa.
    
      Reprodução de tarefa é uma visualização de epitome do que aconteceu no cluster. Ajuda a ver o progresso de execução da tarefa e detetar visualmente anomalias de desempenho e congestionamentos de muito curto período de tempo (menor que, normalmente, 30s).
  * Apresentação de mapa térmico de tarefa 
    
      Mapa térmico de tarefa pode ser selecionado através da lista pendente de apresentar no gráfico da tarefa. 
    
      ![Apresentação do mapa de área dinâmica para dados do gráfico de tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Mostra o mapa térmico e/s, hora e débito de uma tarefa, através do qual pode encontrar em que a tarefa despende a maioria das vezes, ou se a sua tarefa é uma tarefa de limites de e/s e assim sucessivamente.
    
      ![Exemplo do mapa de área dinâmica para dados do gráfico de tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Progresso: A execução da tarefa progresso, consulte as informações no [testar informações](#stage-information).
    * Dados de leitura/escrita: O mapa térmico de dados totais de leitura/escrita em cada fase.
    * Tempo de computação: O mapa térmico de SUM (cada tempo de execução de vértice), pode considerar este como período de tempo que demoraria se todo o trabalho na fase é executado com apenas 1 vértice.
    * Tempo de execução média por nó: O mapa térmico de SUM (cada tempo de execução de vértice) / (vértice número). O que significa que se foi possível atribuir todos os vértices executados na paralelismo, a fase de toda será efetuada neste período de tempo.
    * Débito de entrada/saída: O mapa térmico de débito de entrada/saída de cada fase, pode confirmar se a tarefa é uma tarefa está vinculada e/s através deste.
* Operações de metadados
  
    Pode efetuar algumas operações de metadados no script U-SQL, tais como criar uma base de dados, remova uma tabela, etc. Estas operações são apresentadas na operação de metadados após a compilação. Pode encontrar asserções, criar entidades, largar entidades aqui.
  
    ![Operações de metadados de vista de tarefas de análise do Data Lake do Azure](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Histórico de estado
  
    O histórico de estado também é visualizado no resumo da tarefa, mas pode obter mais detalhes aqui. Pode encontrar as informações detalhadas, tais como quando a tarefa está preparada, na fila em execução tiver sido iniciada, terminou. Também pode encontrar o número de vezes a tarefa foi compilada (o CcsAttempts: 1), quando é a tarefa distribuída para o cluster, na verdade, (o detalhe: tarefa ao cluster de emissão), etc.
  
    ![Histórico de estado de vista de tarefas de análise do Data Lake do Azure](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnóstico
  
    A ferramenta diagnoses automaticamente a execução da tarefa. Irá receber alertas quando existirem alguns erros ou problemas de desempenho nas suas tarefas. Tenha em atenção que tem de transferir perfil para obter informações completas aqui. 
  
    ![Diagnóstico de vista de tarefas de análise do Data Lake do Azure](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Avisos: Um alerta aparece aqui com aviso do compilador. Pode clicar em "x comprovativos" ligação para obter mais detalhes assim que o alerta é apresentado.
  * Vértice executar demasiado longo: se qualquer vértice fica sem tempo (diga 5 horas), irão ser encontrados problemas aqui.
  * Utilização de recursos: Se já tiver alocado paralelismo mais ou insuficiente que necessita, irão ser encontrados problemas aqui. Também pode clicar em utilização de recursos para ver mais detalhes e executar investiguem cenários para localizar uma melhor alocação de recursos (para obter mais detalhes, consulte o artigo neste guia).
  * Verificação de memória: se qualquer vértice utiliza mais de 5 GB de memória, irão ser encontrados problemas aqui. Execução da tarefa pode obter terminada pelo sistema, se utiliza mais memória a limitação do sistema.

## <a name="job-detail"></a>Detalhes da tarefa
Detalhes da tarefa mostra as informações detalhadas do trabalho, incluindo Script, recursos e vista de execução de vértice.

![Detalhes da tarefa do Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Script
  
    O script U-SQL da tarefa é armazenado no arquivo de consultas. Pode ver o script U-SQL original e submeter novamente se for necessário.
* Recursos
  
    Pode encontrar as saídas de compilação de tarefa armazenadas no arquivo de consultas através de recursos. Por exemplo, pode encontrar "algebra.xml", que é utilizado para mostrar o gráfico da tarefa, as assemblagens que registou, etc. aqui.
* Vista de execução de vértice
  
    Mostra vértices detalhes de execução. O perfil de tarefa arquiva cada registo de execução de vértice, tais como os dados totais leitura/escrita, tempo de execução, estado, etc. Através desta vista, pode obter mais detalhes sobre a forma como foi executada uma tarefa. Para obter mais informações, consulte [utilize a vista de execução de vértice ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Passos Seguintes
* Para obter informações de diagnóstico de registo, veja [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para utilizar a vista de execução de vértice, consulte [utilize a vista de execução de vértice ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

