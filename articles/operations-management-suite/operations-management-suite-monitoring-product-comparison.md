---
title: "Monitorização de comparação de produto do Microsoft | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) é a solução de gestão de IT que ajuda a gerir e proteger no local e a infraestrutura de nuvem de baseada na nuvem da Microsoft.  Este artigo identifica os diferentes serviços incluídos no OMS e fornece ligações para o respetivo conteúdo detalhado."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: a63ca0ad-61f8-425d-a48c-d87ba518c104
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
ms.openlocfilehash: b4201f105a87b0a41059c061eb37fb35d4514e02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-monitoring-product-comparison"></a>Comparação de produto de monitorização Microsoft
Este artigo fornece uma comparação entre o System Center Operations Manager (SCOM) e análise de registos no Operations Management Suite (OMS) em termos da respetiva arquitetura, a lógica de como monitorizam estes recursos e como ele efetuar uma análise de dados que recolher.  Isto é para lhe dar uma compreensão dos seus diferenças e força da codificação relativa fundamental.  

## <a name="basic-architecture"></a>Arquitetura básica
### <a name="system-center-operations-manager"></a>System Center Operations Manager
Todos os componentes SCOM estão instalados no seu centro de dados.  [Os agentes estiverem instalados](http://technet.microsoft.com/library/hh551142.aspx) nas máquinas do Windows e Linux geridos pelo SCOM.  Agentes ligar ao [servidores de gestão](https://technet.microsoft.com/library/hh301922.aspx) que comunicar com o SCOM da base de dados do armazém de dados.  Agentes baseiam-se na autenticação de domínio se ligue aos servidores de gestão.  Aqueles fora de um domínio fidedigno podem efetuar a autenticação de certificado ou ligar a um [servidor de Gateway](https://technet.microsoft.com/library/hh212823.aspx).

SCOM requer duas bases de dados do SQL Server, para dados operacionais e do armazém de dados de outra para suportar a análise de dados e relatórios.  A [servidor de relatórios](https://technet.microsoft.com/library/hh298611.aspx) executa o SQL Server Reporting Services para elaborar relatórios sobre dados do armazém de dados. 

SCOM pode monitorizar os recursos de nuvem através de pacotes de gestão para os produtos, como [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [do Office 365](https://www.microsoft.com/download/details.aspx?id=43708), e [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Estes pacotes de gestão utilizam um ou mais agentes locais como proxies para deteção de recursos de nuvem e fluxos de trabalho em execução para medir o desempenho e a disponibilidade.  Os agentes de proxy também são utilizados para [monitorizar dispositivos de rede](https://technet.microsoft.com/library/hh212935.aspx) e outros recursos externos.

A consola de operações é uma aplicação do Windows que se liga a um dos servidores de gestão e permite ao administrador ver e analisar os dados recolhidos e configurar o ambiente SCOM.  Uma consola baseada na web pode ser alojada em qualquer servidor IIS e fornece uma análise de dados através de um browser.

![Arquitetura do SCOM](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics
A maioria dos componentes do OMS são na nuvem do Azure para que possa implementar e geri-lo com o mínimo custo e esforço administrativo.  Todos os dados recolhidos pela análise de registos são armazenados no repositório de OMS.

Análise de registos pode recolher dados a partir de um dos três origens:

* Máquinas virtuais a executar o Windows e físicas e [Microsoft Monitoring Agent (MMA)](https://technet.microsoft.com/library/mt484108.aspx) ou Linux e o [agente do Operations Management Suite para Linux](https://technet.microsoft.com/library/mt622052.aspx).  Estas máquinas podem ser no local ou de máquinas virtuais no Azure ou noutra nuvem.
* Uma conta de armazenamento do Azure com [diagnósticos do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) dados recolhidos por função de trabalho do Azure, a função da web ou a máquina virtual.
* [Ligação a um grupo de gestão do SCOM](https://technet.microsoft.com/library/mt484104.aspx).  Nesta configuração, os agentes de comunicam com servidores de gestão do SCOM que fornecem os dados na base de dados do SCOM onde, em seguida, é entregue ao arquivo de dados OMS.
  Os administradores de analisam os dados recolhidos e configure a análise de registos com o portal do OMS que está alojado no Azure e pode ser acedido a partir de qualquer browser.  As aplicações móveis para aceder a estes dados estão disponíveis para as plataformas padrão.

![Arquitetura de análise do registo](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Integrar o SCOM e análise de registos
Quando o SCOM é utilizado como uma origem de dados para análise de registos pode tirar partido as funcionalidades de ambos os produtos numa híbrida com o ambiente de monitorização.  Pode configurar agentes SCOM existentes através da consola de operações para serem geridos pelo OMS, além de continuar a executar os pacotes de gestão do SCOM.  
Dados a partir de um grupo de gestão do SCOM ligado é entregue ao Log Analytics utilizando um dos quatro métodos:

* Eventos e os dados de desempenho são recolhidos pelo agente e entregue ao SCOM.  Servidores de gestão no SCOM, em seguida, fornecem os dados para análise de registos.
* Alguns eventos, tais como registos de IIS e eventos de segurança continuam a ser entregues diretamente ao Log Analytics do agente.
* Algumas soluções terá de fornecer o software adicional para o agente ou exigir que o software ser instalado para recolher dados adicionais.  Normalmente, estes dados serão enviados diretamente ao Log Analytics.
* Algumas soluções irão recolher dados diretamente a partir de servidores de gestão do SCOM provenientes do agente.  Por exemplo, o [solução de gestão de alertas](https://technet.microsoft.com/library/mt484092.aspx) recolhe alertas do SCOM, depois de terem sido criadas.

## <a name="monitoring-logic"></a>Lógica de monitorização
SCOM e análise de registos trabalham com dados semelhantes recolhidos a partir de agentes mas tem diferenças fundamentais em como definir e implementar a respetiva lógica para recolha de dados e como analisam estes dados que possam recolher.

### <a name="operations-manager"></a>Operations Manager
Lógica de monitorização para o SCOM estiver implementado no [pacotes de gestão](https://technet.microsoft.com/library/hh457558.aspx) que contém a lógica de deteção de componentes para monitorizar, medir o estado de funcionamento desses componentes e para a recolha de dados para analisar.  Dados de monitorização pode ser tão simples como recolher um contador de desempenho ou eventos ou que poderia utilizar lógica complexa implementada num script.  Pacotes de gestão que incluem a monitorização completa estão disponíveis para uma variedade de [aplicações da Microsoft e de terceiros](http://go.microsoft.com/fwlink/?LinkId=82105) além dos dispositivos de hardware e de rede.  Pode [criar os seus próprios pacotes de gestão](http://aka.ms/mpauthor) para aplicações personalizadas.

Pacotes de gestão contém vários fluxos de trabalho que cada uma desempenha algumas função distinta monitorização, como um contador de desempenho de amostragem, a verificar o estado de um serviço ou executar um script.  Cada fluxo de trabalho é executado independentemente e define o seus próprio resultados, tais como a base de dados que irá escrever e se irá gerar um alerta. 

Pode substituir os detalhes do fluxo de trabalho, tais como a frequência que são executadas, o limiar em que considerar que um erro e a gravidade do alerta que geram.  Também pode fornecer funcionalidades adicionais ao adicionar os seus fluxos de trabalho.

![Substituições](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Os pacotes de gestão instalados na base de dados do Operations Manager e distribuídos automaticamente para os agentes, servidores de gestão.  Cada agente irá transferir pacotes de gestão e carregar os fluxos de trabalho relevantes para as aplicações que são instaladas automaticamente.  Dados recolhidos pelo agente é entregue novamente para o servidor de gestão para inserção no SCOM da base de dados do armazém de dados.  A consola de operações permite-lhe ver e analisar estes dados através de vistas personalizadas, dashboards e relatórios incluídos no pacote de gestão.

A distribuição de pacotes de gestão é ilustrada no diagrama seguinte.

![Fluxo do pacote de gestão](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>Evento e recolha de desempenho
Análise de registos recolhe os eventos e contadores de desempenho de sistemas de agente utilizando origens, como o registo de eventos do Windows, registos de IIS e Syslog.  Pode definir critérios para os quais os dados são recolhidos através do portal da análise de registos e, em seguida, criar consultas de registo para analisar os dados recolhidos.  Um conjunto de critérios padrão é definido quando criar a sua área de trabalho do OMS e pode definir dados adicionais para aplicações específicas. 

![Definir os registos de eventos no registo de análise](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

Enquanto SCOM tem muitos fluxos de trabalho detalhados que, normalmente, definem critérios específicos de dados e a ação que deve ser efetuada em resposta, a análise de registos tem critérios mais gerais para a recolha de dados.  Consultas de registo e soluções fornecem mais visados critérios para analisar e atuar sobre dados específicos na nuvem, após o qual foi recolhido.

#### <a name="solutions"></a>Soluções
Soluções fornecem lógica adicional para recolha de dados e análise.  Pode selecionar soluções para adicionar à sua subscrição do OMS da Galeria a solução.

![Galeria de soluções](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

Soluções principalmente executam na nuvem, fornecendo uma análise de eventos e contadores de desempenho recolhidos no repositório de OMS.  Também pode definem adicionais dados sejam recolhidos que podem ser analisados com consultas de registo ou através da interface de utilizador adicionais fornecida pela solução no dashboard do OMS. 

Por exemplo, o [solução de controlo de alterações](https://technet.microsoft.com/library/mt484099.aspx) Deteta a configuração é alterado em sistemas de agente e escreve os eventos para o repositório do OMS que pode ser analisado com várias vistas gráficas resumem detetou alterações.  Pode desagregar da vista resumida em consultas de registo que apresentam os dados detalhados recolhidos pela solução.

Enquanto pode selecionar que soluções que adicionar à sua subscrição, não tem atualmente a capacidade de criar as suas próprias soluções.  Pode selecionar os eventos e contadores de desempenho para recolher e criar vistas personalizadas com base nas suas próprias consultas de registo.

A lógica de monitorização para análise de registos está resumida no diagrama a seguir.

![Fluxo de solução de análise do registo](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Monitorização do Estado de Funcionamento
### <a name="operations-manager"></a>Operations Manager
SCOM pode os diferentes componentes de uma aplicação de modelo e forneça um Estado de funcionamento em tempo real para cada.  Isto permite-lhe não só ver detectada erros e o desempenho ao longo do tempo, mas também para validar o estado de funcionamento atual do sistema ou uma aplicação e cada um dos respetivos componentes em qualquer momento.  Porque compreende os períodos de tempo que uma aplicação está disponível, o motor de estado de funcionamento no SCOM também suporta o nível de serviço contratos (SLA) que analisar e elaborar relatórios sobre a disponibilidade de uma aplicação ao longo do tempo.

Por exemplo, a vista abaixo mostra o estado de funcionamento em tempo real de motores de base de dados do SQL Server monitorizados pelo SCOM.  O estado de funcionamento de cada uma das bases de dados para um dos motores de base de dados é apresentado na parte inferior metade da vista.

![Vista de estado](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

Com os monitores que são utilizados para determinar o estado de funcionamento geral, o Explorador do Estado de funcionamento para um dos motores de base de dados é mostrado abaixo.  Estes monitores são definidos no pacote de gestão do SQL Server e executar todos os motores de base de dados do SQL Server detetados pelo SCOM.

![Explorador de estado de funcionamento](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Componentes em vários sistemas podem ser combinados para medir o estado de funcionamento de uma aplicação distribuída.  Isto pode ser particularmente útil para a linha de aplicações empresariais que incluem vários componentes distribuídos.  Pode criar um modelo que mede o estado de funcionamento de cada componente dessa rollup na disponibilidade da aplicação.

Active Directory é um exemplo de um pacote de gestão que fornece um modelo para analisar os respetivos componentes distribuídos.  O diagrama de exemplo abaixo mostra o estado de funcionamento do ambiente geral e a relação entre florestas, domínios e controladores de domínio.  Cada um destes componentes inclui subcomponentes e vários monitores de forma semelhantes ao exemplo SQL acima.

![Vista de diagrama do SCOM](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
OMS não incluem um motor de comuns para aplicações do modelo ou medir o respetivo estado de funcionamento em tempo real.  As soluções individuais podem avaliar o estado de funcionamento geral dos serviços de determinado com base nos dados recolhidos e poderá instalarem lógica personalizada no agente para executar uma análise em tempo real.  Porque executam soluções na nuvem com o acesso ao repositório OMS, muitas vezes, podem fornecer mais profundo do Analysis Services que normalmente é efetuado por pacotes de gestão. 

Por exemplo, o [soluções AD avaliação e de avaliação do SQL Server](https://technet.microsoft.com/library/mt484102.aspx) analisar os dados recolhidos e fornecer uma classificação diferente para diferentes aspetos do ambiente.  Inclui recomendações para melhoramentos que podem ser efetuadas para melhorar a disponibilidade e o desempenho do ambiente.

![Solução de avaliação do AD](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Análise de dados
SCOM e análise de registos fornecem funcionalidades diferentes para analisar os dados recolhidos.  SCOM tem vistas e Dashboards na consola de operações para analisar dados recentes numa variedade de formatos e relatórios para apresentar dados no armazém de dados em formato tabular.  Análise de registos fornece uma interface e o idioma de consulta de conclusão de registo para analisar dados no repositório de OMS.  Quando o SCOM é utilizado como uma origem de dados para análise de registos, o repositório inclui dados recolhidos pelo SCOM para que as ferramentas de análise de registos podem ser utilizadas para analisar os dados a partir de ambos os sistemas.

### <a name="operations-manager"></a>Operations Manager
#### <a name="views"></a>Vistas
Vistas na consola de operações permitem-lhe ver os diferentes tipos de dados recolhidos pelo SCOM em diferentes formatos, normalmente, a tabela de eventos, alertas e dados de estado e gráficos para dados de desempenho de linha.  Vistas efetuar análise mínima ou consolidação dos dados, mas permitem-lhe filtrar de acordo com os critérios específicos. 

![Vistas](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Pacotes de gestão, normalmente, irão fornecer várias vistas que suportam a aplicação ou sistema que monitoriza.  Isto pode incluir vistas de estado para os diferentes objetos que o pacote de gestão Deteta, vistas de alertas para os problemas detetados e vistas para contadores de desempenho.

As vistas são particularmente adequadas para analisar o estado atual do ambiente, incluindo alertas abertos e o estado de funcionamento dos objetos e sistemas monitorizados.  Pode desagregar detalhado de eventos ou dados de desempenho que suportam um alerta específico para diagnosticar a causa raiz. Da mesma forma, pode ver o desempenho e estado de funcionamento de diferentes componentes de uma aplicação para avaliar o seu estado de funcionamento atual.

#### <a name="dashboards"></a>Dashboards
Dashboards na consola de operações são principalmente trabalham com os mesmos dados como vistas, mas são mais personalizáveis e podem incluir mais rica visualizações.  Um conjunto de dashboards padrão estão disponíveis que pode personalizar facilmente para os seus fins.  Também pode utilizar um widget de PowerShell que pode apresentar dados devolvidos por uma consulta do PowerShell.

![Dashboard](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Os programadores têm a capacidade de adicionar componentes personalizados para dashboards incluem nos respetivos pacotes de gestão.  Estes podem ser altamente especializadas para uma aplicação específica, como o dashboard no pacote de gestão de SQL abaixo.  Este dashboard também pode ser utilizado como um modelo para as versões personalizadas.

![Dashboard SQL](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Relatórios
Relatórios no SCOM analisam os dados no armazém de dados em formato tabular.  Pode ser impresso e agendadas para entrega automatizada em formatos de ficheiro diferentes, incluindo PDF, o CSV e o Word.  Relatórios de trabalham com dados do armazém de dados para que estejam especialmente adequados para uma análise de tendências de longo prazo.

Pacotes de gestão, normalmente, irão fornecer relatórios personalizados para uma aplicação específica.  Também pode selecionar a partir de uma biblioteca genéricas de relatórios de que pode personalizar para as suas próprias aplicações ou para efetuar análise ad hoc.

Segue-se um relatório de desempenho de exemplo que mostra os dados recolhidos pelo pacote de gestão do Active Directory.

![Relatório](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
Análise de registos tem um [idioma de consulta](https://technet.microsoft.com/library/mt484120.aspx) que pode utilizar para executar uma análise em dados de várias aplicações sem a necessidade de criar uma vista personalizada ou um relatório.  Uma vez OMS está implementado na nuvem, o desempenho das consultas e análise de dados não estão sujeitas à quaisquer limitações de hardware e rapidamente pode analisar incluindo milhões de registos de consultas. 

Consultas de análise de registos também são a base de outras funcionalidades.  Pode guardar uma consulta, exportar os resultados para o Excel ou o tiver executar em intervalos regulares e gerar um alerta se os resultados correspondentes aos critérios de determinado automaticamente.  

![Fluxo de consulta de registo](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Segue-se um exemplo de uma consulta de análise de registos.  Neste exemplo, todos os eventos com "Iniciar" no nome são devolvidos e agrupados pelo evento ID.  O utilizador fornece simplesmente a consulta e análise de registos gera dinamicamente a interface de utilizador para efetuar a análise.  Selecionar qualquer item na lista irá devolver os dados de detalhado de eventos.

![Consulta de registo](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Além de proporcionarem analysis ad hoc, consultas de análise de registos podem ser guardadas para utilização futura e também adicionadas ao seu [OMS dashboard](http://technet.microsoft.com/library/mt484090.aspx) conforme mostrado no exemplo seguinte.

![Dashboard do OMS](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Passos Seguintes
* Implementar [do System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx).
* Inscrever-se [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics).  

