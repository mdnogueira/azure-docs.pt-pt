---
title: "Explore os dados utilizando o Explorador de informações de séries de tempo do Azure | Microsoft Docs"
description: "Este artigo descreve como utilizar o Explorador de informações de séries de tempo do Azure no seu browser para ver rapidamente uma vista global dos seus dados de grandes e validar o seu ambiente de IoT."
services: time-series-insights
ms.service: time-series-insights
author: MarkMcGeeAtAquent
ms.author: kfile
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 35f7bf0504075e8bb0b99f8492b850df2b7ceae1
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="azure-time-series-insights-explorer"></a>Explorador do Azure Insights de séries de tempo
Este artigo explicar as várias funcionalidades e as opções disponíveis na aplicação de web do Explorador de informações de séries de tempo. Utilize o Explorador de informações de séries de tempo no seu browser para criar as visualizações de dados.
 
O Azure Time Series Insights é um serviço de análise, armazenamento e visualização totalmente gerido que simplifica a exploração e a análise de milhões de eventos de IoT em simultâneo. Proporciona uma vista global dos seus dados, que permite-lhe validar rapidamente a sua solução de IoT e evitar períodos de indisponibilidade dispendiosos dispositivos fundamentais. Pode detetar tendências ocultas, spot anomalias e realizar a causa raiz estatísticas em tempo real. O Explorador de informações de séries de tempo está atualmente em pré-visualização pública.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar o Explorador de informações de séries de tempo, tem de:
- Criar um ambiente de informações de séries de tempo
- Fornecer acesso à sua conta no ambiente
- Adicionar uma origem de evento para a ingestão de dados e armazená-la

## <a name="explore-and-query-data"></a>Explorar e consultar dados
Minutos de ligar a sua origem de evento para o seu ambiente de informações de séries de tempo, que possa explorar e consultar os dados de séries de tempo.

1. Para iniciar, abra o [Explorador Insights de séries de tempo](https://insights.timeseries.azure.com/) no web browser e selecione um ambiente no lado esquerdo da janela. Todos os ambientes que tem acesso a são listados por ordem alfabética.

2. Depois de selecionar um ambiente, utilizar o **FROM** e **para** configurações na parte superior, ou clique e arraste ao longo do intervalo de tempo pretendido.  Clique a Lupa na parte superior direita, ou durante o período de tempo selecionado com o botão direito e selecione **pesquisa**.  

3. Também pode atualizar disponibilidade automaticamente cada minuto, selecionando o **automática no** botão.

4. Tenha em atenção de que o ícone de nuvem do Azure leva-o para o seu ambiente no portal do Azure.

   ![Ambiente de informações de séries de tempo](media/time-series-insights-explorer/explorer1.png)

5. Em seguida, verá um gráfico que mostra uma contagem de todos os eventos durante o período de tempo selecionado.  Aqui, tem um número de controlos:

    **Painel do Editor de termos**: O espaço do termo é onde consultar o seu ambiente.  É possível encontrá-lo no lado esquerdo do ecrã, ativa 
      - **Medidas**: Isto pendente mostra todas as colunas numéricas (Doubles)
      - **Divisão pela**: esta lista pendente mostra colunas categórico (cadeias)
      - Pode ativar passo interpolação, Mostrar mínima e máxima e ajustar o eixo y do painel de controlo seguinte para medir.  Além disso, pode ajustar se os dados apresentados são uma contagem, média ou soma dos dados.
      - Pode adicionar até cinco termos para ver no mesmo eixo x.  Utilize o **cópia pendente** botão para adicionar um termo adicional ou clique em de **adicionar** botão para adicionar um termo de raiz.
     
        ![Painel do Editor de termos](media/time-series-insights-explorer/explorer2.png)

      - **Predicado**: O predicado permite-lhe filtrar rapidamente os eventos utilizando o conjunto de operandos listados abaixo. Se realiza uma pesquisa clicando selecionar /, o predicado será automaticamente atualização com base nessa procura.      Os tipos de operando suportados incluem:

         |Operação  |Tipos suportados  |Notas  |
         |---------|---------|---------|
         |<, >, <=, >=     |  TimeSpan duplo, DateTime,       |         |
         |=, !=, <>     | String, booleano, Double, DateTime, TimeSpan, nulo        |         |
         |EM     | String, booleano, Double, DateTime, TimeSpan, nulo        |  Todos os operandos devem ser do mesmo tipo ou ser constante NULL.        |
         |TEM     | Cadeia        |  Literais de cadeia constante só são permitidas no lado direito. Uma cadeia vazia e NULL não são permitidos.       |

      - **Exemplos de consultas**
      
         ![Consultas de exemplo](media/time-series-insights-explorer/explorer9.png)

6. O **tamanho do intervalo** ferramenta de controlo de deslize permite-lhe aplicar zoom e para intervalos através do mesmo timespan.  Esta opção fornece um controlo mais preciso de movimento entre grande frações de tempo que mostram as tendências uniforme para baixo para setores tão pequena como milissegundo, permitindo-lhe ver cuts granulares e de alta resolução dos seus dados. Ponto de partida de predefinição do controlo de deslize está definida como a vista ideal dos dados da sua seleção; balanceamento de resolução, velocidade de consulta e granularidade.

7. O **tempo Pincel** ferramenta facilita a partir de um intervalo de tempo para outro, colocando frente UX intuitiva e do Centro de movimento totalmente integrado entre os intervalos de tempo.

8. O **guardar** comando permite-lhe guardar a consulta atual e ativá-la para a partilha com outros utilizadores do ambiente. Utilizar **abra**, pode ver todas as consultas guardadas e quaisquer consultas partilhadas de outros utilizadores em ambientes que tem acesso. 

   ![Consultas](media/time-series-insights-explorer/explorer3.png)

9. O **perspetiva vista** ferramenta fornece uma vista de até quatro consultas exclusivas em simultâneo. Pode encontrar o botão de vista de perspetiva no canto superior direito do gráfico.  

   ![Vista de perspetiva](media/time-series-insights-explorer/explorer4.png)

10. O **gráfico** permite-lhe explorar visualmente os dados. Ferramentas do gráfico incluem:

   - Selecione/clique, que permite uma seleção de um timespan específico ou de uma série de dados individual.  
   - Dentro de um tempo span seleção, pode ampliar ou explorar eventos.  
   - Dentro de uma série de dados, pode dividir as séries por outra coluna, adicionar a série como um novo termo, mostrar apenas a série selecionada, excluir seleccionadas séries, enviar um ping a série ou explorar eventos da série de selecionado.
   - Na área de filtro para a esquerda do gráfico, pode ver todas as séries de dados apresentados e reordenar por nome ou valor, ver todas as séries de dados ou série especificamente afixado ou alojado.  Pode também selecionar uma série de dados individual e dividir as séries por outra coluna, adicionar a série como um novo termo, mostrar apenas a série selecionada, excluir seleccionadas séries, afixar a série ou explorar eventos da série de selecionado.
   - Ao visualizar várias termos em simultâneo, pode da pilha, unstack, veja dados adicionais sobre uma série de dados e utilizar o mesmo eixo y em todos os termos com os botões no canto superior direito do gráfico.
 
   ![Ferramentas do gráfico](media/time-series-insights-explorer/explorer5.png) 

11. O **heatmap** pode ser utilizado para detetar rapidamente série de dados exclusivo ou anómalos numa consulta especificada. Termo de pesquisa de uma só pode ser visualizado como um heatmap.    

   ![Heatmap](media/time-series-insights-explorer/explorer6.png)

12. **Eventos**: Quando escolhe explore eventos ao selecionar ou clicar acima, o painel de eventos é disponibilizado.  Aqui, pode ver todos os eventos não processados e exportar os eventos como JSON ou ficheiros CSV. Tenha em atenção que Insights de séries de tempo armazena todos os dados não processados.

   ![Eventos](media/time-series-insights-explorer/explorer7.png)

13. Clique em de **estatísticas** separador após a explorar os eventos para expor os padrões e estatísticas da coluna.  

   - **Padrões**: esta funcionalidade proativamente analisa os padrões mais estatisticamente significativos numa região de dados selecionada. Isto tal não invalida possa tem de ter que observar milhares de eventos para compreender os padrões garantir mais tempo e energia. Além disso, o Insights de séries de tempo permite-lhe ir diretamente para estes padrões estatisticamente significativos para continuar a realizar uma análise. Esta funcionalidade também é útil para as investigações de post mortem em dados históricos. 

   - **Estatísticas de coluna**: estatísticas de coluna fornecem charting e tabelas que dividir dados a partir de cada coluna de série de dados selecionada ao longo do intervalo de tempo selecionado.  
 
      ![ESTATÍSTICAS](media/time-series-insights-explorer/explorer8.png) 

Agora constatou as várias funcionalidades e as opções disponíveis na aplicação de web do Explorador de informações de séries de tempo. 

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
>[Diagnosticar e resolver problemas no seu ambiente de informações de séries de tempo](time-series-insights-diagnose-and-solve-problems.md)
