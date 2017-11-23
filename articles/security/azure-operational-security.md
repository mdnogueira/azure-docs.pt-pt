---
title: "Segurança operacional do Azure | Microsoft Docs"
description: "Saiba mais sobre o Microsoft Operations Management Suite (OMS), os respetivos serviços e como funciona."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 8528eeb4eac8397beaa3d0dca37ce9eb33167b32
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-operational-security"></a>Segurança operacional do Azure
## <a name="introduction"></a>Introdução

### <a name="overview"></a>Descrição geral
Sabemos que a segurança está tarefa um na nuvem e como importante é que encontrará exatas e atempadas informações sobre a segurança do Azure. Uma das razões melhor a utilizar o Azure para as suas aplicações e serviços é tirar partido da vasta gama de ferramentas de segurança e funcionalidades disponíveis. Estas ferramentas e capacidades ajudam possibilitam a criar soluções seguras na plataforma do Azure segura. O Microsoft Azure tem de fornecer confidencialidade, integridade e disponibilidade dos dados de cliente, permitindo também accountability transparente.

Para ajudar os clientes a compreender melhor a matriz de controlos de segurança implementado no Microsoft Azure a partir de ambos do cliente e Microsoft operacionais das perspetivas, este documento técnico, "Azure operacional" segurança, é escrito na que fornece um abrangente da segurança operacional disponível com o Windows Azure.

### <a name="azure-platform"></a>Plataforma do Azure
Azure é uma plataforma de serviço de nuvem pública que suporta uma seleção abrangente dos sistemas operativos, programação idiomas, estruturas, ferramentas, bases de dados e dispositivos. Pode ser executado contentores de Linux com a integração do Docker; criar aplicações com JavaScript, Python, .NET, PHP, Java e Node.js; compilação back-ends para iOS, Android e Windows dispositivos. Serviço Cloud do Azure suporta as mesmas tecnologias milhões de programadores e profissionais de TI já dependem e de confiança.

Quando criar ou migrar os recursos IT à, um fornecedor de serviços de nuvem pública são depender capacidades da organização para proteger as suas aplicações e dados com os serviços e os controlos fornecem para gerir a segurança dos seus recursos baseados na nuvem.

Infraestrutura do Azure foi concebida de instalação de aplicações para o alojamento de milhões de clientes em simultâneo, e fornece uma base de confiança no qual as empresas podem cumprir os seus requisitos de segurança. Além disso, Azure disponibiliza uma vasta gama de opções de segurança configuráveis e a capacidade de controlá-las, de modo a que possa personalizar a segurança para satisfazer os requisitos exclusivos de implementações da sua organização. Este documento será ajuda-o a compreender a segurança do Azure como capacidades podem ajudar a satisfazer estes requisitos.

### <a name="abstract"></a>Abstrato
Segurança operacionais do Azure refere-se os serviços, controlos e funcionalidades disponíveis para os utilizadores para proteger os seus dados, aplicações e outros recursos no Microsoft Azure. Segurança operacionais do Azure é construída numa estrutura que incorpora o conhecimento adquirido através de várias funcionalidades que são exclusivas para a Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Center e a deteção profunda de ameaças atuais.

Este documento técnico descreve a abordagem da Microsoft para segurança operacionais do Azure dentro da plataforma de nuvem do Microsoft Azure e inclui os seguintes serviços:
1.  [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite

Microsoft Operations Management Suite (OMS) é a solução de gestão de IT para a nuvem híbrida. Utilizado individualmente ou para expandir a implementação existente do System Center, OMS dá-lhe a flexibilidade máxima e o controlo de gestão baseado na nuvem da sua infraestrutura.

![Microsoft Operations Management Suite](./media/azure-operational-security/azure-operational-security-fig1.png)

Com o OMS, pode gerir qualquer instância em nenhuma nuvem, incluindo no local, o Azure, AWS, Windows Server, o Linux, VMware e OpenStack, um custo mais baixo que soluções competitivos. Incorporado para o mundo primeiro de nuvem, o OMS oferece uma nova abordagem para gerir a sua empresa que é a forma mais rápida e mais económica para cumprir os novos desafios de negócio e acomodar novas cargas de trabalho, aplicações e ambientes de nuvem.

### <a name="oms-services"></a>Serviços do OMS

A funcionalidade principal do OMS é disponibilizada por um conjunto de serviços que são executados no Azure. Cada serviço proporciona uma função de gestão específica e pode combinar serviços para alcançar cenários de gestão diferentes.

| Serviço  | Descrição|
| :------------- | :-------------|
| Log Analytics | Monitorizar e analisar a disponibilidade e o desempenho de diferentes recursos, incluindo computadores e máquinas virtuais. |
|Automatização | Automatizar processos manuais e impor configurações para computadores e máquinas virtuais. |
| Cópia de segurança | Cópia de segurança e restaurar dados críticos. |
| Site Recovery | Providenciar elevada disponibilidade para aplicações críticas. |

### <a name="log-analytics"></a>Log Analytics

O [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) oferece serviços de monitorização para o OMS, ao recolher dados de recursos geridos num repositório central. Estes dados podem incluir dados de eventos ou de desempenho ou dados personalizados fornecidos através da API. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação.


Este método permite-lhe consolidar os dados de várias origens, pelo que pode combinar dados a partir do seu serviços do Azure com existentes no local ambiente. Também separa claramente a recolha dos dados das ações tomadas em relação aos mesmos, para que todas as ações estejam disponíveis para todos os tipos de dados.


![Log Analytics](./media/azure-operational-security/azure-operational-security-fig2.png)

O serviço de análise de registos gere os dados baseados na nuvem em segurança através dos seguintes métodos:
-   segregação de dados
-   retenção de dados
-   segurança física
-   Gestão de incidentes
-   Conformidade
-   certificações de normas de segurança

### <a name="azure-backup"></a>Azure Backup

[Cópia de segurança do Azure](http://azure.microsoft.com/documentation/services/backup) fornece dados de cópia de segurança e restaurar os serviços e faz parte do suite OMS dos produtos e serviços.
Protege os dados de aplicação e mantém-nos durante anos sem qualquer investimento de capital e com um mínimo de custos operacionais. Pode criar cópias de segurança dados de servidores físicos e virtuais do Windows para além das cargas de trabalho de aplicação, tais como o SQL Server e o SharePoint. Também pode ser utilizado por [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) replicar os dados protegidos no Azure para redundância e armazenamento de longa duração.


Os dados protegidos no Azure Backup são armazenados num cofre de cópias de segurança localizado numa região geográfica específica. Os dados são replicados na mesma região e, dependendo do tipo de cofre, também podem ser replicados noutra região para resiliência adicional.

### <a name="management-solutions"></a>Soluções de Gestão
[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) é baseado na nuvem IT solução de gestão da Microsoft que o ajuda a gerir e proteger no local e a infraestrutura de nuvem.


[As soluções de gestão](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) prepackaged conjuntos de logics que implementam um cenário de gestão específico utilizando um ou mais serviços do OMS. Estão disponíveis diferentes soluções da Microsoft e de parceiros, que podem ser adicionados facilmente à sua subscrição do Azure para aumentar o valor acrescentado do investimento feito no OMS. Como parceiro, pode criar as suas próprias soluções para suportar as suas aplicações e serviços e fornecem aos utilizadores através do Azure Marketplace ou modelos de início rápido.


![Soluções de Gestão](./media/azure-operational-security/azure-operational-security-fig4.png)

É um bom exemplo de uma solução que utiliza vários serviços para fornecer funcionalidades adicionais do [solução de gestão de atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management). Esta solução usa o [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) agente para o Windows e Linux recolher informações sobre atualizações necessárias em cada agente. Escreve estes dados no repositório do Log Analytics, onde pode analisá-los com um dashboard incluído.

Quando cria uma implementação, os runbooks do [da automatização do Azure](https://docs.microsoft.com/azure/automation/automation-intro) são utilizados para instalar as atualizações necessárias. Todo este processo é gerido no portal e não tem de se preocupar com os detalhes subjacentes.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

Centro de segurança do Azure ajuda a proteger os seus recursos do Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure. No âmbito do serviço, que é possível definir as políticas não apenas em relação a suas subscrições do Azure, mas também contra [grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups), pelo que pode ser mais granular.

### <a name="security-policies-and-recommendations"></a>Recomendações e políticas de segurança

Uma política de segurança define o conjunto de controlos que são recomendados para os recursos na subscrição ou grupo de recursos especificados.

No Centro de Segurança, é possível definir políticas de acordo com os requisitos de segurança da sua empresa e com o tipo de aplicações ou sensibilidade dos dados.

![Recomendações e políticas de segurança](./media/azure-operational-security/azure-operational-security-fig5.png)


As políticas que são ativadas ao nível da subscrição automaticamente propagam a todos os grupos de recursos dentro da subscrição, conforme mostrado no diagrama no lado direito:


### <a name="data-collection"></a>Recolha de dados

O Centro de Segurança recolhe dados das suas máquinas virtuais (VMs) para avaliar o respetivo estado de segurança, fornecer recomendações de segurança e alertá-lo sobre ameaças. Quando o acesso do primeiro centro de segurança, a recolha de dados está ativado em todas as VMs na sua subscrição. A recolha de dados é recomendada, mas pode optar ativamente por não participar, desativando a recolha de dados na política do Centro de Segurança.

### <a name="data-sources"></a>Origens de dados

- O Centro de Segurança do Azure analisa dados das origens seguintes para proporcionar visibilidade para o estado da segurança, identificar vulnerabilidades e recomendar mitigações e detetar ameaças ativas:

-   Serviços do Azure: utiliza informações sobre a configuração dos serviços do Azure que implementou através da comunicação com o fornecedor de recursos desses serviços.

- Tráfego de Rede: utiliza metadados de tráfego de rede de amostragem da infraestrutura da Microsoft, tais como porta/IP de origem/destino, tamanho do pacote e protocolo de rede.

-   Soluções de Parceiros: utiliza alertas de segurança das soluções de parceiros integradas, tais como firewalls e soluções antimalware.

-   As suas Máquinas Virtuais: utiliza informações de configuração e informações sobre eventos de segurança, tais como eventos do Windows e registos de auditoria, registos do IIS, mensagens do syslog e ficheiros de informação de falha de sistema das suas máquinas virtuais.

### <a name="data-protection"></a>Proteção de dados

Para ajudar os clientes a evitar, detetar e responder a ameaças, o Centro de Segurança do Azure recolhe e processa dados relacionados com segurança, incluindo informações de configuração, metadados, registos de eventos, ficheiros de informação de falha de sistema e mais. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço.

-   **Segregação de dados**: os dados são mantidos separados de forma lógica em cada componente em todo o serviço. Todos os dados são etiquetados por organização. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço.

-   **Acesso a dados**: para fornecer recomendações de segurança e investigar potenciais ameaças de segurança, técnico da Microsoft pode aceder a informações recolhidas ou analisados pelo serviços do Azure, incluindo ficheiros de informação de falha, processar eventos de criação, instantâneos de disco da VM e artefactos, que podem incluir, involuntariamente, dados de cliente ou dados pessoais das suas máquinas virtuais. Respeitamos a [declaração de privacidade e termos de licenciamento Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), o estado de que a Microsoft não é utiliza dados de cliente ou derivará informações dos mesmos para quaisquer fins comerciais fim de publicidade ou semelhantes.

-   **Utilização de dados**: a Microsoft utiliza os padrões e as informações sobre ameaças presentes em vários inquilinos para melhorar as nossas capacidades de prevenção e deteção. Fazemo-lo em conformidade com os compromissos de privacidade descritos na nossa [Declaração de Privacidade](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).

### <a name="data-location"></a>Localização dos dados

O Centro de Segurança do Azure recolhe cópias efémeras dos ficheiros de informação de falha de sistema e analisa-as para encontrar sinais de tentativas de exploração e casos em que a segurança tenha sido comprometida. O Centro de Segurança do Azure faz esta análise na mesma Geografia que a área de trabalho e elimina as cópias efémeras quando a análise estiver concluída. Os artefactos das máquinas são armazenados centralmente na mesma região que as VMs.

-   **As contas do Storage**: é especificada uma conta de armazenamento para cada região onde as máquinas virtuais estão em execução. Isto permite-lhe armazenar dados na mesma região que a máquina virtual a partir da qual os dados são recolhidos.

-   **Armazenamento do Centro de Segurança do Azure**: as informações sobre alertas de segurança, incluindo alertas de parceiros, recomendações e estado de funcionamento da segurança, são armazenadas de forma central, atualmente nos Estados Unidos. Estas informações podem incluir informações de configuração relacionadas e eventos de segurança recolhidos das suas máquinas virtuais, conforme necessário para lhe fornecer o alerta de segurança, recomendação ou estado de funcionamento de segurança.


## <a name="azure-monitor"></a>Azure Monitor

O [segurança do OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) e solução permite que as TI ativamente monitorizar todos os recursos, podem ajudar a minimizar o impacto de incidentes de segurança de auditoria. Auditoria e segurança do OMS tem domínios de segurança que podem ser utilizados para monitorizar os recursos. O domínio de segurança fornece acesso rápido às opções, para a monitorização de segurança seguintes domínios são abordados em obter mais detalhes:

-   Avaliação de malware
-   Avaliação de atualização
-   Identidade e acesso.

Monitor do Azure fornece indicações para informações sobre tipos específicos de recursos. Oferece visualização, consulta, encaminhamento, alertas, escala automática e automatização nos dados da infraestrutura do Azure (registo da atividade) e cada individuais (registos de diagnóstico) de recursos do Azure.

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


Aplicações em nuvem são complexas com várias partes mover. A monitorização fornece dados para se certificar de que a aplicação permanece cópias de segurança e em execução em bom estado. Também o ajuda a stave desativar potenciais problemas ou resolver passado aqueles.

Além disso, pode utilizar dados de monitorização para obter conhecimentos aprofundados sobre a sua aplicação. Conhecimentos podem ajudar a melhorar o desempenho da aplicação ou maintainability ou automatizar ações que caso contrário necessitem intervenção manual.

### <a name="azure-activity-log"></a>Registo de atividade do Azure


É um registo que fornece informações sobre as operações que foram executadas no recursos na sua subscrição. O registo de atividade era anteriormente conhecido como "Registos de auditoria" ou "Registos operacionais," uma vez que os relatórios de eventos de controlo plane para as suas subscrições.

![Registo de atividade do Azure](./media/azure-operational-security/azure-operational-security-fig7.png)

Utilizar o registo de atividade, poderá determinar o ' que, quem e quando ' para quaisquer operações (PUT, POST, DELETE) efetuadas nos recursos na sua subscrição de escrita. Também pode compreender o estado da operação e outras propriedades relevantes. O registo de atividade inclui as operações de leitura (GET) ou as operações para recursos que utilizam o modelo clássico.

### <a name="azure-diagnostic-logs"></a>Registos de diagnóstico do Azure

Estes registos são emitidos por um recurso e fornecerem dados avançados, frequentes sobre o funcionamento desse recurso. O conteúdo estes registos varia consoante o tipo de recurso.

Por exemplo, registos de sistema de eventos do Windows são uma categoria de registo de diagnóstico para VMs e blob, tabela e fila registos são categorias de registos de diagnóstico para contas de armazenamento.

Registos de diagnóstico é diferente do [registo de atividade (anteriormente conhecido como registo de auditoria ou registo operacional)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). O registo de atividade fornece informações sobre as operações que foram executadas no recursos na sua subscrição. Registos de diagnóstico fornecem informações aprofundadas operations que o seu recurso efetuadas em si.

### <a name="metrics"></a>Métricas

Monitor do Azure permite-lhe consumir telemetria ganhar visibilidade sobre o desempenho e estado de funcionamento das suas cargas de trabalho no Azure. O tipo de dados de telemetria do Azure mais importante é as métricas (também denominadas de contadores de desempenho) emitidas pelo Azure mais recursos. Monitor do Azure fornece várias formas de configurar e consumir estes [métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) para monitorização e resolução de problemas. As métricas são uma origem de telemetria importante e permitem-lhe efetuar as seguintes tarefas:

-   **Monitorizar o desempenho** do seu recurso (por exemplo, uma aplicação VM, o Web site ou lógica) ao representar a métricas num gráfico portal e que o gráfico a um dashboard de afixação.

-   **Seja notificado de um problema** que tem impacto no desempenho do seu recurso quando uma métrica atravesse um determinado limiar.

-   **Configurar as ações automatizadas**, tais como automática dimensionamento um recurso ou acionando um runbook quando uma métrica atravesse um determinado limiar.

-   **Efetuar análises avançadas** ou relatórios sobre tendências de desempenho ou a utilização do seu recurso.

-   **Arquivo** o histórico de desempenho ou o estado de funcionamento dos seus recursos para compatibilidade ou a fins de auditoria.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

É a capacidade no Azure que permite a recolha de dados de diagnóstico sobre uma aplicação implementada. Pode utilizar a extensão de diagnóstico de várias origens diferentes. São atualmente suportados [Web de serviço de nuvem do Azure e funções de trabalho](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [Virtual Machines do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/overview) com o Microsoft Windows, e [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Outros serviços do Azure têm os seus próprios diagnóstico separado.

## <a name="azure-network-watcher"></a>Observador de rede do Azure

Auditoria de segurança de rede é vital para detetar vulnerabilidades de rede e garantir a conformidade com a segurança de TI e o modelo de regulamentação de governação. Vista de grupo de segurança, pode obter o grupo de segurança de rede configurado e regras de segurança e as regras de segurança eficaz. Com a lista de regras aplicadas, poderá determinar as portas que estão abertos e avaliar a vulnerabilidade de rede.

[Observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de rede no, a e do Azure. Diagnóstico de rede e ferramentas de visualização disponíveis com o observador de rede ajudam-na compreender, diagnosticar e obter informações sobre a sua rede no Azure. Este serviço inclui a captura de pacotes, o salto seguinte, o fluxo IP verificar, a vista de grupo de segurança, os registos de fluxo NSG. A monitorização ao nível do cenário fornece uma vista de ponto a ponto dos recursos de rede, ao contrário de monitorização de recursos de rede individuais.

![Observador de rede do Azure](./media/azure-operational-security/azure-operational-security-fig8.png)

Observador de rede atualmente tem as seguintes capacidades:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Registos de auditoria</a>**-operações efetuadas como parte da configuração de redes são registados. Estes registos podem ser visualizados no portal do Azure ou obtidos através de ferramentas da Microsoft, tais como o Power BI ou ferramentas de terceiros. Os registos de auditoria estão disponíveis através do portal, o PowerShell, a CLI e a Rest API. Para obter mais informações sobre os registos de auditoria, consulte operações de auditoria com o Resource Manager. Os registos de auditoria estão disponíveis para operações efetuadas em todos os recursos de rede.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">Fluxo IP verifica </a>**  -verifica se a um pacote é permitido ou negado com base nos parâmetros de pacotes de 5 cadeias de identificação do fluxo informações (IP de destino, IP de origem, porta de destino, porta de origem e protocolo). Se o pacote é negado por um grupo de segurança de rede, a regra e o grupo de segurança de rede que negado o pacote é devolvido.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Próximo salto</a>**  -determina o salto seguinte para pacotes a ser encaminhados na infraestrutura de rede do Azure, permitindo-lhe diagnosticar qualquer configurada incorretamente rotas definidas pelo utilizador.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Vista do grupo de segurança</a>**  -obtém as regras de segurança eficaz e aplicados que são aplicadas numa VM.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">O registo de fluxo de NSG</a>**  -registos de fluxo para grupos de segurança de rede permitem-lhe capturar registos relacionados com o tráfego que são permitidas ou negadas pelas regras de segurança no grupo. O fluxo é definido por uma informação de 5 cadeias de identificação – IP de origem, IP de destino, porta de origem, porta de destino e protocolo.

## <a name="azure-storage-analytics"></a>Análise do Armazenamento do Azure

[Análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) pode armazenar métricas que incluem agregadas de transações estatísticas e dados de capacidade sobre pedidos para um serviço de armazenamento. As transações são reportadas ao ambos o nível de operação de API e o nível de serviço de armazenamento e a capacidade é comunicada a nível de serviço de armazenamento. Dados de métricas, podem ser utilizados para analisar a utilização do serviço de armazenamento, diagnosticar problemas com pedidos efetuados contra o serviço de armazenamento e para melhorar o desempenho das aplicações que utilizam um serviço.

[Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) efetua o registo e fornece dados de métricas para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas relacionados com a sua conta de armazenamento. Registo de análise de armazenamento está disponível para o [serviços tabela, fila e Blob](https://docs.microsoft.com/azure/storage/storage-introduction). Análise de armazenamento regista informações detalhadas sobre pedidos com êxito e a um serviço de armazenamento.

Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas com um serviço de armazenamento. Pedidos são registados numa base de melhor esforço. Entradas de registo são criadas apenas se não existirem pedidos efetuados relativamente ao ponto final de serviço. Para o exemplo se uma conta de armazenamento tem de atividade no respetivo ponto final do Blob, mas não no respetivos pontos finais de tabelas ou filas, apenas os registos relacionados com o serviço Blob é criado.

Para utilizar a análise de armazenamento, terá de a ativar individualmente para cada serviço que pretende monitorizar. Pode ativá-la no [portal do Azure](https://portal.azure.com/); para obter mais informações, consulte [monitorizar uma conta de armazenamento no portal do Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Também pode ativar a análise de armazenamento através de programação através da API REST ou biblioteca de clientes. Utilize a operação definir propriedades de serviço para ativar a análise de armazenamento individualmente para cada serviço.

Os dados agregados são armazenados num blob bem conhecido (para o registo) e em tabelas bem conhecidas (para métricas), que podem ser acedidas através do serviço Blob e APIs de serviço tabela.

Análise de armazenamento tem um limite de 20 TB, a quantidade de dados armazenados, que é independentes do limite total para a sua conta de armazenamento. Todos os registos são armazenados no [blobs de blocos](https://docs.microsoft.com/azure/storage/storage-analytics) num contentor com o nome $logs, que são criados automaticamente quando a análise de armazenamento está ativada para uma conta de armazenamento.

As seguintes ações executadas pelo armazenamento Analytics estão sujeito a faturação:

-   Pedidos para criar os blobs para registo
-   Pedidos para criar as entidades da tabela de métricas.

> [!Note]
> Para obter mais informações sobre as políticas de retenção de dados e de faturação, consulte [faturação e de análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Para um desempenho ideal, pretende limitar o número de elevada disponibilidade sobreutilizados discos ligados à máquina virtual para evitar possíveis limitação. Se todos os discos não estão a ser altamente utilizados ao mesmo tempo, a conta de armazenamento pode suportar um maior disco número.

> [!Note]
> Para obter mais informações sobre limites de conta de armazenamento, consulte [metas de desempenho e escalabilidade do Storage do Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).


Os seguintes tipos de pedidos autenticados e anónimos são registados.

| Autenticados  | Anónima|
| :------------- | :-------------|
| Pedidos com êxito | Pedidos com êxito |
|Falha de pedidos, incluindo o tempo limite, limitação, rede, autorização e outros erros | Pedidos utilizando um acesso assinatura partilhado (SAS), incluindo pedidos falhados e bem-sucedidas |
| Pedidos utilizando um acesso assinatura partilhado (SAS), incluindo pedidos falhados e bem-sucedidas |Erros de tempo limite para o cliente e servidor |
|   Pedidos de dados de análise |    Pedidos falhados de GET com o código de erro 304 (não modificados) |
| Pedidos efetuados pelo armazenamento Analytics ela própria, tal como criação de registo ou eliminação, não são registados. Uma lista completa dos dados com sessão iniciada está documentada no [operações de registadas análise de armazenamento e as mensagens de estado](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [formato de registo de análise do armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) tópicos. | Todos os outros pedidos anónimos falhados não são registados. Uma lista completa dos dados com sessão iniciada está documentada no [operações de registadas análise de armazenamento e as mensagens de estado](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [formato de registo de análise do armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |
## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD também inclui um conjunto completo de capacidades de gestão de identidade, incluindo autenticação multifator, registo de dispositivos, gestão de palavra-passe self-service, gestão de grupos self-service, gestão de contas com privilégios, controlo de acesso baseado em funções, monitorização de utilização de aplicações, avançado auditoria e monitorização e alertas de segurança.

-   Melhore a segurança de aplicação com a autenticação multifator do Azure AD e o acesso condicional.

-   Monitorizar a utilização da aplicação e proteger a sua empresa contra ameaças avançadas com relatórios e monitorização de segurança.

O Azure Active Directory (Azure AD) inclui relatórios de segurança, atividade e auditoria para o diretório. [O relatório de auditoria de diretório Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) ajuda os clientes para identificar ações privilegiadas ocorridas no seu Azure Active Directory. Ações privilegiadas incluem alterações de elevação (por exemplo, a criação da função ou a reposição de palavra-passe), alterar as configurações da política (por exemplo políticas de palavra-passe) ou as alterações à configuração de diretório (por exemplo, alterações às definições de Federação do domínio).

Os relatórios fornecem o registo de auditoria para o nome de evento, o ator que efetuou a ação, o recurso de destino afetado por alteração e a data e hora (em UTC). Os clientes conseguem obter a lista de eventos de auditoria para o seu Azure Active Directory através do [portal do Azure](https://portal.azure.com/), conforme descrito nas [ver os registos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Aqui está uma lista dos relatórios incluídos:

| Relatórios de segurança  | Relatórios de atividade| Relatórios de auditoria |
| :------------- | :-------------| :-------------|
|Inícios de sessão de fontes desconhecidas | Utilização da aplicação: resumo | Relatório de auditoria de diretório |
|Inícios de sessão após várias falhas | Utilização da aplicação: detalhado |   |
|Inícios de sessão de várias localizações geográficas | Dashboard de aplicações |  |
|Inícios de sessão de endereços IP com atividade suspeita |Erros de aprovisionamento de contas |  |
|Atividades irregulares de início de sessão |Dispositivos de utilizadores individuais |  |
|Inícios de sessão de dispositivos possivelmente infetados |Atividade de utilizadores individuais |   |
|Utilizadores com atividade anómala de início de sessão |Relatório de atividade de grupos |   |
| |Relatório de atividade de registo de reposição de palavra-passe |   |
| |Atividade de reposição de palavra-passe |   | |



Os dados destes relatórios podem ser úteis para as suas aplicações, como sistemas SIEM, auditoria e ferramentas de business intelligence. Os relatórios do Azure AD [APIs](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) fornecer acesso programático para os dados através de um conjunto de APIs baseado em REST. Pode chamar estas APIs a partir de vários idiomas e as ferramentas de programação.

Eventos de relatório de auditoria do Azure AD são retidos durante 180 dias.

> [!Note]
> Para obter mais informações sobre a retenção em relatórios, consulte [políticas de retenção de relatório do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Para os clientes interessados em armazenar as respetivas [eventos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) para períodos de retenção mais longos, a API de relatórios podem ser utilizado regularmente extraia eventos de auditoria para um arquivo de dados separada.

## <a name="summary"></a>Resumo

Este resumos de artigo proteger a sua privacidade e proteger os seus dados, durante a distribuição de software e serviços que o ajudam a gerir a infraestrutura de TI da sua organização. Microsoft reconhece que, quando estes entrust os seus dados para outras pessoas, essa confiança requer a segurança rigorosas. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço. Proteger e a proteção de dados é uma prioridade superior na Microsoft.

Este artigo explica

-   Como dados são recolhidos, processados e protegidos no Operations Management Suite (OMS).

-   Analise rapidamente eventos em várias origens de dados. Identifique riscos de segurança e compreenda o âmbito e o impacto das ameaças e ataques para reduzir os danos provocados por falhas na segurança.

-   Identifique padrões de ataque ao ver o tráfego IP malicioso de saída e os tipos de ameaças maliciosas. Compreenda a postura de segurança de todo o seu ambiente, independentemente da plataforma.

-   Capture todos os dados de registo e evento necessários para uma auditoria de segurança ou a conformidade. A hora e os recursos necessários para fornecer uma auditoria de segurança com um registo completo, pesquisável e exportável e o conjunto de dados de eventos de barra.

<ul>
<li>Recolha eventos relacionados com segurança, auditoria e análise de violação para manter um olho fechar os elementos:</li>
<ul>
<li>Postura de segurança</li>
<li>Problema relevantes</li>
<li>Ameaças resumos</li>
</ul>
</ul>

## <a name="next-steps"></a>Passos Seguintes

- [Conceção e segurança operacional](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft designs os respetivos serviços e software com segurança em mente para ajudar a garantir que a respetiva infraestrutura de nuvem é resiliente e defended contra ataques.

- [Operations Management Suite | Segurança e conformidade](https://www.microsoft.com/cloud-platform/security-and-compliance)

Utilize dados de segurança da Microsoft e de análise para efetuar mais a deteção de ameaças inteligente e eficaz.

- [Operações e planeamento do Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) um conjunto de passos e tarefas que pode seguir para otimizar a utilização do Centro de segurança com base nos requisitos de segurança da sua organização e o modelo de gestão de nuvem.

