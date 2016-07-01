<properties
   pageTitle="Introdução ao Centro de Segurança do Azure | Microsoft Azure"
   description="Saiba mais sobre o Centro de Segurança do Azure, as suas capacidades principais e como funciona."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/22/2016"
   ms.author="terrylan"/>

# Introdução ao Centro de Segurança do Azure

Saiba mais sobre o Centro de Segurança do Azure, as suas capacidades principais e como funciona.

> [AZURE.NOTE] As informações neste documento aplicam-se ao pré-lançamento do Centro de Segurança do Azure. Este documento apresenta o serviço ao utilizar um exemplo de implementação.

## O que é o Centro de Segurança do Azure?
 O Centro de Segurança ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

##  Principais capacidades
 O Centro de Segurança proporciona uma prevenção e deteção de ameaças eficiente e de fácil utilização, bem como capacidades de resposta que estão incorporadas no Azure. As principais capacidades são:

| | |
|----- |-----|
| Prevenir | Monitoriza o estado de segurança dos seus recursos do Azure |
| | Define políticas para as subscrições e grupos de recursos do Azure com base nos requisitos de segurança da sua empresa, nos tipos de aplicações que utiliza e na sensibilidade dos seus dados |
| | Utiliza recomendações de segurança orientadas por políticas para orientar os proprietários do serviço no processo de implementação dos controlos necessários |
| | Implementa rapidamente serviços e aplicações de segurança da Microsoft e de parceiros |
| Detetar |Recolhe e analisa automaticamente os dados de segurança a partir dos recursos do Azure, da rede e das soluções de parceiros, como os programas antimalware e as firewalls |
| | Tira partido da análise de informações globais a partir de produtos e serviços da Microsoft, da Unidade de Crimes Digitais (DCU) da Microsoft, do Microsoft Security Response Center (MSRC) e de feeds externos |
| | Aplica análises avançadas, incluindo machine learning e análise comportamental |
| Responder | Faculta alertas/incidentes de segurança prioritários |
| | Disponibiliza informações aprofundadas sobre a origem do ataque e os recursos afetados |
| | Sugere formas de parar o ataque atual e ajuda a evitar ataques futuros |

## Instruções introdutórias
 Pode aceder ao Centro de Segurança a partir do [Portal do Azure](https://azure.microsoft.com/features/azure-portal/). [Inicie sessão no portal](https://portal.azure.com), selecione **Procurar** e, em seguida, desloque-se para a opção **Centro de Segurança** ou selecione o mosaico **Centro de Segurança** anteriormente afixado ao dashboard do portal.

![Mosaico Segurança no Portal do Azure][1]

A partir do Centro de Segurança, pode definir políticas de segurança, monitorizar configurações de segurança e visualizar os alertas de segurança.

### Políticas de segurança

Pode definir políticas para as suas subscrições e grupos de recursos do Azure, de acordo com os requisitos de segurança da sua empresa. Também pode personalizá-las em função dos tipos de aplicações que estiver a utilizar ou da sensibilidade dos dados em cada subscrição. Por exemplo, os recursos utilizados para programação ou testes podem ter requisitos de segurança diferentes dos que são utilizados para aplicações de produção. Da mesma forma, as aplicações com dados regulados como PII podem exigir um nível mais elevado de segurança.

> [AZURE.NOTE] Para modificar uma política de segurança ao nível da subscrição ou ao nível do grupo de recursos, tem de ser o Proprietário da subscrição ou um Contribuinte da mesma.

No painel **Centro de Segurança**, selecione o mosaico **Política** para obter uma lista das suas subscrições e grupos de recursos.   

![Painel Centro de Segurança][2]

No painel **Política de Segurança**, selecione uma subscrição para ver os detalhes da política.

![Subscrição do painel de política de segurança][3]

**Recolha de dados** (consultar acima) permite a recolha de dados para uma política de segurança. A ativação possibilita:

- Análise diária de todas as máquinas virtuais suportadas para monitorização e recomendações de segurança.
- Recolha de eventos de segurança para análise e deteção de ameaças.

**Escolher uma conta do Storage por região** (consultar acima) permite-lhe escolher, para cada região na qual tenha máquinas virtuais em execução, a conta do Storage onde os dados recolhidos dessas máquinas virtuais estão armazenados. Se não escolher uma conta do Storage para cada região, será criada por si. Os dados que são recolhidos são logicamente isolados a partir dos dados de outros clientes, por motivos de segurança.

> [AZURE.NOTE] A recolha de dados e a escolha de uma conta do Storage por região é configurada ao nível da subscrição.

**Mostrar recomendações para** (consultar acima) permite-lhe selecionar os controlos de segurança que pretende monitorizar e recomenda com base nas necessidades de segurança dos recursos dentro da subscrição.

Em seguida, selecione um grupo de recursos para ver os detalhes da política.

![Grupo de recursos de painel de política de segurança][4]

**Herança** (consultar acima) permite-lhe definir o grupo de recursos como:

- Herdado (predefinição), que significa que todas as políticas de segurança para este grupo de recursos são herdadas do nível da subscrição.
- Exclusivo, que significa que o grupo de recursos tem uma política de segurança personalizada. Terá de efetuar alterações em **Mostrar recomendações para**.

> [AZURE.NOTE] Caso exista um conflito entre a política de nível da subscrição e a política de nível do grupo de recursos, a política de nível do grupo de recursos tem prioridade.

### Recomendações de segurança

 O Centro de Segurança analisa o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Uma lista de recomendações orienta-o no processo de configuração de controlos necessários. Os exemplos incluem:

- Aprovisionamento de antimalware para ajudar a identificar e remover o software malicioso
- Configuração de grupos de segurança de rede e regras para controlar tráfego para máquinas virtuais
- Aprovisionamento de firewalls de aplicação Web para ajudar a proteger-se contra ataques que visam as suas Web Apps
- Implementação de atualizações do sistema em falta
- Abordagem de configurações de SO que não correspondam às linhas de base recomendadas

Clique no mosaico **Recomendações** para obter uma lista de recomendações. Clique em cada recomendação para visualizar informações adicionais ou para tomar medidas de resolução do problema.

![Recomendações de segurança no Centro de Segurança do Azure][5]

### Estado de funcionamento de recursos

O mosaico **Estado de funcionamento de segurança de recursos** mostra a postura de segurança geral do ambiente por tipo de recurso, incluindo máquinas virtuais, Web Apps e outros recursos.   

Selecione um tipo de recurso no mosaico **Estado de funcionamento de segurança de recursos** para ver mais informações, incluindo uma lista de quaisquer potenciais vulnerabilidades de segurança que tenham sido identificadas. (**Virtual Machines** está selecionada no exemplo abaixo.)

![Mosaico Estado de funcionamento de recursos][6]

### Alertas de segurança

 O Centro de Segurança recolhe, analisa e integra automaticamente os dados de registo a partir dos recursos do Azure, da rede e das soluções de parceiros, como os programas antimalware e as firewalls. Quando são detetadas ameaças, é criado um alerta de segurança. Os exemplos incluem a deteção de:

- Virtual Machines comprometidas a comunicar com endereços IP maliciosos conhecidos
- Software maligno avançado detetado ao utilizar a comunicação de erros do Windows
- Ataques de força bruta contra máquinas virtuais
- Alertas de segurança a partir de programas antimalware e firewalls integrados

Ao clicar no mosaico **Alertas de segurança** é apresentada uma lista de alertas prioritários.

![Alertas de segurança][7]

Selecionar um alerta mostra mais informações sobre o ataque e sugestões sobre como remediá-lo.

![Detalhes do alerta de segurança][8]

### Soluções de parceiros

O mosaico **Soluções de parceiros** permite-lhe monitorizar rapidamente o estado de funcionamento das suas soluções de parceiros integradas na sua subscrição do Azure. O Centro de Segurança apresenta os alertas provenientes as soluções.

Selecione o mosaico **Soluções de parceiros**. Abre-se um painel a apresentar uma lista de todas as soluções de parceiros ligadas.

![Soluções de parceiros][9]

## Introdução
Para começar a utilizar o Centro de Segurança, necessita de uma subscrição do Microsoft Azure. O Centro de Segurança está ativado com a sua subscrição do Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

 Pode aceder ao Centro de Segurança a partir do [Portal do Azure](https://azure.microsoft.com/features/azure-portal/). Consulte a [documentação do portal](https://azure.microsoft.com/documentation/services/azure-portal/) para saber mais.

[Introdução ao Centro de Segurança do Azure](security-center-get-started.md) proporciona uma orientação rápida pelos componentes de monitorização de segurança e de gestão de políticas do Centro de Segurança.

## Passos seguintes
Neste documento, foi-lhe apresentado o Centro de Segurança, as suas principais capacidades e como começar. Para saber mais, consulte o seguinte:

- [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
- [Gerir recomendações de segurança no Centro de Segurança do Azure](security-center-recommendations.md) – Saiba como as recomendações o ajudam a proteger os seus recursos do Azure.
- [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
- [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
- [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Obtenha as notícias e informações mais recentes de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png



<!--HONumber=Jun16_HO2-->


