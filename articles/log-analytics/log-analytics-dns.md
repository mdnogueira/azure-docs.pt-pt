---
title: "Solução de análise de DNS no Log Analytics do Azure | Microsoft Docs"
description: "Configurar e utilizar a solução de análise de DNS na análise de registos para recolher informações sobre a infraestrutura de DNS de operações, desempenho e segurança."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.openlocfilehash: 0e8fc0ffb8e0d0bdf00bea46594fe050c00b6c8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Recolher informações sobre a infraestrutura DNS com a solução de pré-visualização de análise de DNS

![Símbolo de análise de DNS](./media/log-analytics-dns/dns-analytics-symbol.png)

Este artigo descreve como configurar e utilizar a solução de análise de DNS do Azure no Log Analytics do Azure para recolher informações sobre a infraestrutura de DNS de operações, desempenho e segurança.

Análise de DNS ajuda-o a:

- Identifica os clientes que tentarem resolver os nomes de domínio malicioso.
- Identifique os registos de recursos obsoletos.
- Identifica os nomes de domínio com frequência consultado e talkative clientes DNS.
- Ver o carregamento de pedido nos servidores DNS.
- Ver falhas de registo DNS dinâmicas.

A solução recolhe, analisa e está correlacionada com DNS do Windows análise e os registos de auditoria e outros dados relacionados dos servidores DNS.

## <a name="connected-sources"></a>Origens ligadas

A tabela seguinte descreve as origens de ligado que são suportadas por esta solução:

| **Origem ligada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](log-analytics-windows-agents.md) | Sim | A solução recolhe informações de DNS de agentes do Windows. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não | A solução não recolhe informações de DNS de agentes de Linux diretos. |
| [Grupo de gestão do System Center Operations Manager](log-analytics-om-agents.md) | Sim | A solução recolhe informações de DNS de agentes num grupo de gestão do Operations Manager ligado. Não é necessária uma ligação direta do agente do Operations Manager para o Operations Management Suite. Dados seja reencaminhados do grupo de gestão para o repositório do Operations Management Suite. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não | Armazenamento do Azure não é utilizado pela solução. |

### <a name="data-collection-details"></a>Detalhes de recolha de dados

A solução recolhe dados de eventos relacionados com DNS e de inventário DNS dos servidores DNS onde está instalado um agente de análise de registos. Estes dados, em seguida, são carregados para análise de registos e apresentados no dashboard de solução. Dados relacionados com o inventário, como o número de servidores DNS, zonas e registos de recursos, são recolhidos ao executar os cmdlets do PowerShell de DNS. Os dados são atualizados a cada dois dias. Os dados relacionados com o evento são recolhidos quase em tempo real do [analíticas e registos de auditoria](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fornecida pelo avançada registo e diagnósticos DNS no Windows Server 2012 R2.

## <a name="configuration"></a>Configuração

Utilize as seguintes informações para configurar a solução:

- Tem de ter um [Windows](log-analytics-windows-agents.md) ou [do Operations Manager](log-analytics-om-agents.md) agente em cada servidor DNS que pretende monitorizar.
- Pode adicionar a solução de análise de DNS para a sua área de trabalho do Operations Management Suite do [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Também pode utilizar o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).

A solução inicia a recolha de dados sem a necessidade da configuração adicional. No entanto, pode utilizar a seguinte configuração para personalizar a recolha de dados.

### <a name="configure-the-solution"></a>Configurar a solução

No dashboard da solução, clique em **configuração** para abrir a página de configuração de DNS da análise. Existem dois tipos de alterações de configuração que pode efetuar:

- **Os nomes de domínio na lista de permissões**. A solução não processa todas as consultas de pesquisa. Mantém uma lista branca de sufixos de nome de domínio. As consultas de pesquisa de resolver para os nomes de domínio que correspondem ao sufixos de nome de domínio na lista branca deste não são processadas pela solução. Não processar nomes de domínio na lista de permissões ajuda a otimizar os dados enviados para análise de registos. A lista branca predefinida inclui nomes de domínio de público popular, como www.google.com e www.facebook.com. Pode ver a lista completa, deslocamento.

 Pode modificar a lista para adicionar qualquer sufixo de nome de domínio que pretende ver informações de pesquisa para. Pode também remover qualquer sufixo de nome de domínio que não pretende ver informações de pesquisa para.

- **Limiar de cliente talkative**. Os clientes DNS que excedem o limiar para o número de pedidos de pesquisa é realçado no **clientes DNS** painel. O limiar predefinido é 1000. Pode editar o limiar.

    ![Nomes de domínio na lista de permissões](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>Pacotes de gestão

Se estiver a utilizar o Microsoft Monitoring Agent para ligar à sua área de trabalho do Operations Management Suite, é instalado o pacote de gestão seguintes:

- Pacote de análise de Recoletor de dados de DNS da Microsoft (Microsft.IntelligencePacks.Dns)

Se o grupo de gestão do Operations Manager está ligado à sua área de trabalho do Operations Management Suite, os seguintes pacotes de gestão estão instalados no Operations Manager ao adicionar esta solução. Não há nenhuma configuração necessária ou a manutenção destes pacotes de gestão:

- Pacote de análise de Recoletor de dados de DNS da Microsoft (Microsft.IntelligencePacks.Dns)
- Configuração de análise DNS do Microsoft System Center Advisor (Microsoft.IntelligencePack.Dns.Configuration)

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](log-analytics-om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Utilizar a solução de análise de DNS

Esta secção explica o dashboard de funções e como utilizá-los.

Depois de adicionar a solução para a sua área de trabalho, o mosaico de solução na página de descrição geral do Operations Management Suite fornece um resumo rápido da infraestrutura de DNS. Inclui o número de servidores DNS, onde os dados estão a ser recolhidos. Também inclui o número de pedidos efetuados por clientes para resolver domínios maliciosos nas últimas 24 horas. Quando clica no mosaico, abre o dashboard de solução.

![Mosaico de análise de DNS](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>Dashboard de soluções

O dashboard de solução mostra as informações resumidas as várias funcionalidades da solução. Também inclui ligações para a vista detalhada para análise forense e diagnósticos adicionais. Por predefinição, os dados são apresentados nos últimos sete dias. Pode alterar o intervalo de data e hora, utilizando o **controlo de seleção de data / hora**, conforme mostrado na imagem seguinte:

![Controlo de seleção de tempo](./media/log-analytics-dns/dns-time.png)

O dashboard de solução mostra os seguintes painéis:

**Segurança de DNS**. Os clientes DNS que estão a tentar comunicar com domínios maliciosos de relatórios. Ao utilizar feeds do Microsoft ameaça intelligence, análise de DNS pode detetar cliente IPs que está a tentar aceder a domínios maliciosos. Em muitos casos, dispositivos infetados por software maligno "marcar" para o Centro de "comando e controlo" do domínio malicioso ao resolver o nome de domínio de software maligno.

![Painel de segurança de DNS](./media/log-analytics-dns/dns-security-blade.png)

Ao clicar um IP de cliente na lista, registo de pesquisa abre e apresenta os detalhes de pesquisa da respetiva consulta. No exemplo seguinte, a análise de DNS detetou que a comunicação foi efetuada com uma [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot):

![Resultados de pesquisa de registo que mostra ircbot](./media/log-analytics-dns/ircbot.png)

As informações de ajudam-o a identificar o:

- Cliente IP que iniciou a comunicação.
- Nome de domínio que é resolvido para o IP malicioso.
- Endereços IP que o nome de domínio é resolvido para.
- Endereço IP malicioso.
- Gravidade do problema.
- Razão para blacklisting IP malicioso.
- Hora da deteção.

**Domínios consultados**. Fornece os nomes de domínio mais frequentes que está a ser consultados pelos clientes do DNS no seu ambiente. Pode ver a lista de todos os nomes de domínio a ser consultado. Pode também desagregar para os detalhes do pedido de pesquisa de um nome de domínio específico no registo de pesquisa.

![Painel de Queried de domínios](./media/log-analytics-dns/domains-queried-blade.png)

**Os clientes DNS**. Os clientes os relatórios *ser, o limiar* para o número de consultas no período de tempo escolhido. Pode ver a lista de todos os clientes DNS e os detalhes das consultas efetuadas pelo-los no registo de pesquisa.

![Painel de clientes DNS](./media/log-analytics-dns/dns-clients-blade.png)

**Registos DNS dinâmicos**. Relatórios de falhas de registo do nome. Todas as falhas de registo para o endereço [registos de recursos](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (tipo A e AAAA) são realçados juntamente com o cliente IPs efetuados os pedidos de registo. Em seguida, pode utilizar estas informações para determinar a causa da falha do registo de seguindo estes passos:

1. Localize a zona é autoritativa para o nome que o cliente está a tentar atualizar.

2. Utilize a solução para verificar as informações de inventário dessa zona.

3. Certifique-se de que a atualização dinâmica para a zona está ativada.

4. Verifique se a zona está configurada para a atualização dinâmica segura ou não.

    ![Painel de registos de DNS dinâmico](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**Pedidos de registo de nome**. O mosaico superior mostra um trendline de com êxito e pedidos de atualização dinâmica de DNS. O mosaico inferior lista superiores 10 clientes que enviam pedidos de atualização de DNS falhou para os servidores DNS, ordenados pelo número de falhas.

![Painel de pedidos de registo de nome ](./media/log-analytics-dns/name-reg-req-blade.png)

**Exemplo de consultas de análises DDI**. Contém uma lista de consultas de pesquisa mais comuns que obtenha dados de análise em bruto diretamente.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Consultas de exemplo](./media/log-analytics-dns/queries.png)

Pode utilizar estas consultas como um ponto de partida para criar as suas próprias consultas para relatórios personalizados. As consultas de ligação para a página de pesquisa de DNS de registo de análise onde os resultados são apresentados:

- **Lista de servidores DNS**. Mostra uma lista de todos os servidores DNS com os respetivos associado FQDN, nome de domínio, nome da floresta e o servidor IPs.
- **Lista de zonas DNS**. Mostra uma lista de todas as zonas DNS com o nome da zona associados, o estado de atualização dinâmica, a servidores de nomes e o estado da assinatura do DNSSEC.
- **Registos de recursos não utilizados**. Mostra uma lista de todos os registos de recursos não utilizados/obsoleta. Esta lista contém o nome do registo de recursos, tipo de registo de recursos, o servidor DNS associado, hora de geração de registos e nome da zona. Pode utilizar esta lista para identificar os registos de recursos DNS já não estão em utilização. Com base nestas informações, pode, em seguida, remova essas entradas dos servidores DNS.
- **Servidores DNS consultar carga**. Mostra informações de modo a que pode obter uma perspetiva da carga do DNS nos seus servidores DNS. Estas informações podem ajudar a planear a capacidade para os servidores. Pode ir para o **métricas** separador para alterar a vista para uma visualização de gráfica. Esta vista ajuda-o a compreender a forma como a carga DNS é distribuída entre os servidores DNS. Consulta DNS é mostra as tendências de velocidade para cada servidor.

    ![Resultados de pesquisa de registo de consulta de servidores DNS](./media/log-analytics-dns/dns-servers-query-load.png)

- **Zonas DNS consultar carga**. Mostra as estatísticas de zona consulta por segundo de DNS de todas as zonas nos servidores DNS que está a ser geridos pela solução. Clique em de **métricas** separador para alterar a vista de registos detalhados para uma visualização gráfica dos resultados.
- **Eventos de configuração**. Mostra todos os eventos de alteração de configuração de DNS e mensagens associadas. Em seguida, pode filtrar estes eventos com base na hora do servidor DNS de evento, ID de evento, ou categoria da tarefa. Os dados podem ajudar a auditar as alterações efetuadas a servidores específicos de DNS em alturas específicas.
- **Registo de DNS analítico**. Mostra todos os eventos analíticos em todos os servidores DNS geridos pela solução. Em seguida, pode filtrar estes eventos com base na hora do servidor DNS de evento, ID de evento, IP de cliente que efetuou a consulta de pesquisa e categoria de tarefa de tipo de consulta. Eventos de análise de servidor DNS de ativar a atividade de controlo no servidor DNS. Um evento de análise é registado sempre que o servidor envia ou recebe informações de DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Procurar utilizando a pesquisa de DNS de registo de análise

Na página de pesquisa de registo, pode criar uma consulta. Pode filtrar os resultados da pesquisa ao utilizar controlos de aspeto de restrições. Também pode criar consultas avançadas para a transformação, o filtro e o relatório no seu resultados. Comece por utilizar as seguintes consultas:

1. No **caixa da consulta de pesquisa**, tipo `Type=DnsEvents` para ver todos os eventos DNS gerados por servidores DNS geridos pela solução. Os resultados da listam os dados de registo para todos os eventos relacionados com consultas de pesquisa, dinâmicos registos e as alterações de configuração.

    ![Pesquisa de registo DnsEvents](./media/log-analytics-dns/log-search-dnsevents.png)  

    a. Para ver os dados de registo de consultas de pesquisa, selecione **LookUpQuery** como o **subtipo** filtro do controlo de aspeto de restrições à esquerda. É apresentada uma tabela que lista todos os eventos de consulta de pesquisa para o período de tempo selecionado.

    b. Para ver os dados de registo de registos dinâmicos, selecione **DynamicRegistration** como o **subtipo** filtro do controlo de aspeto de restrições à esquerda. É apresentada uma tabela que lista todos os eventos de registo dinâmico para o período de tempo selecionado.

    c. Para ver os dados de registo de alterações de configuração, selecione **ConfigurationChange** como o **subtipo** filtro do controlo de aspeto de restrições à esquerda. É apresentada uma tabela que lista todos os eventos de alteração de configuração para o período de tempo selecionado.

2. No **caixa da consulta de pesquisa**, tipo `Type=DnsInventory` para ver todas as relacionadas com o inventário dados DNS para servidores DNS geridos pela solução. Os resultados da listam os dados de registo de registos de recursos, servidores DNS e zonas DNS.

    ![Pesquisa de registo DnsInventory](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>Comentários

Existem duas formas que pode enviar comentários:

- **UserVoice**. Após a ideias para funcionalidades de análise de DNS trabalhar. Visite o [Operations Management Suite UserVoice página](https://aka.ms/dnsanalyticsuservoice).
- **Associar o nosso coorte**. Iremos interessa sempre com novos clientes aderir ao nosso cohorts para obter acesso atempado a novas funcionalidades e ajudar-na melhorar a análise de DNS. Se estiver interessado na associação a nossa cohorts, preencha [este inquérito Rápido](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>Passos seguintes

[Pesquisar registos](log-analytics-log-searches.md) para ver registos detalhados de DNS.
