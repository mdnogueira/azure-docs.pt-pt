<properties
   pageTitle="Gerir e responder a alertas de segurança no Centro de Segurança do Azure | Microsoft Azure"
   description="Este documento ajuda-o a utilizar as capacidades de Centro de Segurança do Azure para gerir e responder a alertas de segurança."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/22/2016"
   ms.author="yurid"/>

# Gerir e responder a alertas de segurança no Centro de Segurança do Azure
Este documento ajuda-o a utilizar capacidades de Centro de Segurança do Azure para gerir e responder a alertas de segurança.

> [AZURE.NOTE] As informações neste documento aplicam-se ao pré-lançamento do Centro de Segurança do Azure.

## O que é o Centro de Segurança do Azure?
 O Centro de Segurança ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

## O que são alertas de segurança?
O Centro de Segurança recolhe, analisa e integra automaticamente dados de registo a partir dos seus recursos do Azure, da rede e do antimalware integrado e firewalls para detetar ameaças reais e reduzir os falsos positivos. Uma lista de alertas de segurança prioritários, incluindo alertas a partir de soluções de parceiros integradas, é apresentada no Centro de Segurança juntamente com as informações necessárias para investigar rapidamente e recomendações sobre como remediar um ataque.

Os investigadores de segurança da Microsoft estão constantemente a analisar as ameaças emergentes em todo o mundo, incluindo novos padrões e tendências de ataque vistos através dos seus produtos de consumidor e para empresas e serviços online. Como resultado, o Centro de Segurança pode atualizar os algoritmos de deteção à medida que sejam detetadas novas vulnerabilidades e explorações, o que ajuda os clientes a manterem-se a par da evolução das ameaças. Os exemplos de alguns dos tipos de ameaças que o Centro de Segurança pode detetar incluem:

- **Deteção de força bruta sobre dados de rede**: utiliza modelos de machine-learning que compreendem padrões de tráfego de rede típicos para as suas aplicações, ativa uma deteção mais eficiente de tentativas de acesso a serem executadas por pessoas mal-intencionadas em vez de utilizadores legítimos.
- **Deteção de força bruta sobre dados de ponto final**: com base na análise de registos de máquina; permite a diferenciação entre tentativas falhadas e bem-sucedidas.
- **VMs a comunicar com IPs maliciosos**: compara o tráfego de rede para análise de informações sobre ameaças globais da Microsoft, deteta máquinas que estejam comprometidas e a comunicar com os servidores de Comando e Controlo (C & C) e vice-versa.
- **VMs comprometidas**: com base na análise comportamental dos registos de máquina e da correlação com outros sinais, identifica eventos anómalos que provavelmente refletem o facto de a máquina estar comprometida e a ser explorada.

## Gerir alertas de segurança

Pode rever os alertas atuais ao observar o mosaico **Alertas de segurança**. Siga os passos abaixo para ver mais detalhes sobre cada alerta:

1. No dashboard do Centro de Segurança, verá o mosaico **Alertas de segurança**.

    ![O mosaico Alertas de segurança no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-new.png)

2.  Clique no mosaico para abrir o painel **Alertas de segurança** que contém mais detalhes sobre os alertas como é mostrado abaixo.

    ![O painel Alertas de segurança no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-new.png)

Na parte inferior deste painel encontram-se os detalhes de cada alerta. Para ordenar, clique na coluna pela qual pretende ordenar. A definição para cada coluna é indicada abaixo:

- **Alerta**: uma explicação breve do alerta.
- **Contagem**: uma lista de todos os alertas deste tipo específico que foram detetados num dia específico.
- **Detetado por**: o serviço que foi responsável por ter acionado o alerta.
- **Data**: a data em que o evento ocorreu.
- **Estado**: o estado atual para esse alerta. Existem três tipos de estados:
    - **Ativo**: o alerta de segurança foi detetado.
    - **Dispensado**: o alerta de segurança foi dispensado pelo utilizador. Este estado é normalmente utilizado para os alertas que tenham sido investigados, mas que foram mitigados ou considerados como não sendo um ataque real.

- **Gravidade**: o nível de gravidade, que pode ser alta, média ou baixa.

Pode filtrar os alertas com base na data, no estado e na gravidade. A filtragem de alertas pode ser útil para cenários onde necessita de limitar o âmbito dos alertas de segurança mostrados. Por exemplo, pode pretender resolver alertas de segurança que ocorreram nas últimas 24 horas, porque está a investigar uma potencial violação no sistema.

1. Clique em **Filtrar** no painel **Alertas de Segurança**. O painel **Filtro** abre-se e o utilizador seleciona os valores de data, de estado e de gravidade que pretende ver.

    ![Filtrar alertas no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-new.png)

2.  Depois de investigar um alerta de segurança, pode aperceber-se de que se trata de um falso positivo para o seu ambiente ou que indica um comportamento esperado para um recurso específico. Seja como for, se determinar que um alerta de segurança não é aplicável, pode dispensá-lo e, em seguida, filtrá-lo da sua vista. Existem duas formas de dispensar um alerta de segurança. Clique com o botão direito do rato num alerta e selecione **Dispensar** ou coloque o cursor sobre um item, clique nas reticências que aparecem à direita e selecione **Dispensar**. Pode ver os alertas de segurança dispensados ao clicar no **Filtro** e selecionar **Dispensados**.

    ![Filtrar alertas no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-new.png)

### Responder a alertas de segurança
Selecione um alerta de segurança para obter mais informações sobre o(s) evento(s) que acionaram o alerta e quais os passos (se existirem) necessários para remediar um ataque. Os alertas de segurança estão agrupados por tipo e data. Ao clicar num alerta de segurança abre-se um painel que contém uma lista dos alertas agrupados.

![Responder a alertas de segurança no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig7.png)

Neste caso, os alertas que foram acionados referem-se a atividade suspeita do protocolo RDP (Remote Desktop Protocol). A primeira coluna mostra quais os recursos que foram atacados; a segunda mostra a hora em que este ataque foi detetado; a terceira mostra o estado do alerta; e a quarta mostra a gravidade do ataque. Depois de rever estas informações, clique no recurso que foi atacado. Abre-se um novo painel com mais sugestões sobre o que fazer a seguir, conforme é mostrado no exemplo abaixo.

![Sugestões para o que fazer sobre alertas de segurança no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig8-1.png)

No campo **Alerta** deste painel irá encontrar mais detalhes sobre este evento. Estes detalhes adicionais facultam informações aprofundadas sobre o que acionou o alerta de segurança, o recurso de destino, quando aplicável, o endereço IP de origem e as recomendações sobre como remediar.  Em certos casos, o endereço IP de origem estará vazio (não disponível) porque nem todos os registos de eventos de segurança do Windows incluem o endereço IP.

> [AZURE.NOTE] A remediação sugerida pelo Centro de Segurança irá variar de acordo com o alerta de segurança. Em certos casos, poderá ter de utilizar outras capacidades do Azure para implementar a remediação recomendada. Por exemplo, a remediação para este ataque é colocar na lista de proibições o endereço IP que está a gerar este ataque ao utilizar um [ACL de rede](../virtual-network/virtual-networks-acl.md) ou uma regra de [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md).


## Passos seguintes
Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

- [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
- [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
- [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.



<!--HONumber=Jun16_HO2-->


