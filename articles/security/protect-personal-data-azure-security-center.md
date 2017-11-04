---
title: "Proteger os dados pessoais com o Centro de segurança do Azure | Microsoft Docs"
description: "proteger os dados pessoais utilizando o Centro de segurança do Azure"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 3a941389713a4d3dbffbbfe8a717409927d85c6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-from-breaches-and-attacks-azure-security-center"></a>Proteger os dados pessoais contra falhas e ataques: Centro de segurança do Azure

Este artigo irá ajudá-lo a compreender como utilizar o Centro de segurança do Azure para proteger os dados pessoais contra falhas e ataques.

## <a name="scenario"></a>Cenário 

Uma empresa de grande cruise headquartered nos Estados Unidos, está a expandir as suas operações para oferecer itineraries Mediterranean e Baltic seas, bem como o Isles território. Para ajudar os esforços, obteve várias linhas cruise mais pequenas com base em (Itália), na Alemanha, Dinamarca e o U.K.

A empresa utiliza o Microsoft Azure para armazenar dados empresariais na nuvem. Isto inclui informações pessoais, tais como nomes, endereços, números de telefone e informações de cartão de crédito. Também inclui informações de recursos humanos, tais como:

- Endereços
- Números de telefone
- Números de identificação de dedução dos impostos
- Informações médicas

A linha cruise mantém também uma base de dados de grandes dimensões de reward e loyalty membros de programa. Os funcionários empresarias acedam à rede de escritórios remotos da empresa e agentes levar localizados em todo o mundo tem acesso a alguns recursos da empresa.
Dados pessoais circulam na rede entre o Centro de dados da Microsoft e estas localizações.

## <a name="problem-statement"></a>Declaração do problema

A empresa está preocupada com a ameaça de ataques dos seus recursos do Azure. Pretendem evitar a exposição de dados pessoais dos clientes e dos empregados às pessoas não autorizadas. Pretendem obter orientações sobre os prevenção e resposta/remediação, bem como uma forma eficaz para monitorizar a segurança em curso dos recursos na nuvem.
Precisam de uma linha de defesa contra atacantes de hoje sofisticadas e organizados forte.

## <a name="company-goal"></a>Objetivo da empresa

Um dos objetivos da empresa é garantir a privacidade dos dados pessoais dos clientes e dos empregados, protegê-lo contra ameaças. Um dos respetivos objetivos consiste em responder imediatamente a sinais de violação para mitigar o impacto. Requer uma forma de avaliar o estado atual da segurança, identificar configurações vulneráveis e remedeiam-nos.

## <a name="solutions"></a>Soluções

Centro de segurança do Azure (ASC) da Microsoft fornece uma solução de gestão de políticas e monitorização da segurança integrada. Fornece ameaças eficiente e fácil de utilizar as capacidades de prevenção, a deteção e a resposta.

### <a name="prevention"></a>Prevenção

ASC ajuda a evitar falhas, permitindo-lhe definir políticas de segurança, forneça acesso just-in-time e implementar recomendações de segurança.

Uma política de segurança define o conjunto de controlos recomendados para recursos dentro da subscrição especificada. Apenas acesso de tempo pode ser utilizado para bloquear o tráfego de entrada para as VMs do Azure, reduzir a exposição a ataques. Recomendações de segurança são criadas pelo ASC depois de analisar o estado de segurança dos seus recursos Azure.

#### <a name="how-do-i-set-security-policies-in-asc"></a>Como definir a políticas de segurança no ASC

Pode configurar políticas de segurança para cada subscrição. Para modificar uma política de segurança, tem de ser um proprietário ou contribuinte dessa subscrição. No portal do Azure, efetue o seguinte:

1. Selecione **política** no dashboard do ASC.

2. Selecione a subscrição na qual pretende ativar a política.

3. Escolha **política de prevenção** configurar políticas por subscrição. **Recolher dados de máquinas virtuais** deve ser definido como **no.**

4. No **política de prevenção** opções, selecionadas **no** para ativar as recomendações de segurança que são relevantes para a subscrição.

![](media/protect-personal-data-azure-security-center/prevention-policy.png)

Para obter mais instruções e uma explicação de cada uma das recomendações de política que podem ser ativadas, consulte [definir políticas de segurança no Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-policies#set-security-policies).

#### <a name="how-do-i-configure-just-in-time-access-jit"></a>Como posso configurar apenas no tempo de acesso (JIT)?

Quando o JIT está ativado, o Centro de segurança bloqueia baixo tráfego de entrada para as suas VMs do Azure através da criação de uma regra NSG. Selecione as portas na VM para o qual vai ser bloqueado a tráfego de entrada. Para utilizar o acesso JIT, efetue o seguinte:

1. Selecione o **apenas no mosaico de acesso VM de tempo** no painel ASC.

2. Selecione o **recomendado** separador.

3. Em **VMs**, selecione as VMs que pretende ativar. Isto coloca uma marca de verificação junto a uma VM. 
4. Selecione **ativar JIT** em VMs.
5. Selecione **Guardar**.

Em seguida, pode ver as portas predefinidas que ASC recomenda a ser ativada para JIT. Também pode adicionar e configurar uma porta de novo no qual pretende ativar a apenas na solução de tempo. O **apenas acesso de VM de tempo** mosaico no Centro de segurança mostra o número de VMs configuradas para acesso de JIT. Mostra o número de pedidos de acesso aprovados efetuadas na última semana.

![](media/protect-personal-data-azure-security-center/jit-vm.png)

Para obter instruções sobre como fazê-lo, e obter informações adicionais sobre apenas no acesso do tempo, consulte [gerir o acesso de máquina virtual utilizando apenas no tempo.](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

#### <a name="how-do-i-implement-asc-security-recommendations"></a>Como implementar o recomendações de segurança ASC?

Quando o Centro de Segurança identifica potenciais vulnerabilidades de segurança, cria recomendações. As recomendações orientam-no no processo de configuração de controlos necessários. 
1. Selecione o **recomendações** mosaico no dashboard do ASC.

2. Ver as recomendações, que são apresentadas num formato de tabela em que cada linha representa uma recomendação.

3. Para recomendações de filtro, selecione **filtro** e selecione os valores de estado e gravidade que pretende ver.

4. A ignorar uma recomendação que não é aplicável, clique com o botão direito e selecione **dispensar.**

5. Avalie qual recomendação deve ser aplicada pela primeira vez.

6. Aplica as recomendações apresentadas na ordem de prioridade.

Para obter uma lista de recomendações possíveis e passagens sobre como aplicar a cada um, consulte [gerir recomendações de segurança no Centro de segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

### <a name="detection-and-response"></a>Deteção e a resposta

Deteção e a resposta vá em conjunto, como pretende que respondam mais rapidamente possível após uma ameaça é detetada.
A deteção de ameaças ASC funciona através da recolha automática de informações de segurança dos seus recursos do Azure, rede e soluções de parceiros ligadas. ASC pode atualizar rapidamente os respetivos algoritmos de deteção, que os atacantes disponibilizam novas e cada vez mais sofisticadas explorações. Para obter mais informações sobre como funciona a deteção de ameaças do ASC, consulte [as capacidades de deteção do Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities).

#### <a name="how-do-i-manage-and-respond-to-security-alerts"></a>Como gerir e responder a alertas de segurança?

Uma lista de alertas de segurança prioritários é apresentada no Centro de segurança juntamente com as informações necessárias para investigar rapidamente o problema. Centro de segurança também inclui recomendações sobre como remediar um ataque. Para gerir os alertas de segurança, efetue o seguinte:

1. Selecione o **alertas de segurança** mosaico no dashboard do ASC. Isto mostra detalhes de cada alerta.

2. Para filtrar alertas com base na data, estado e gravidade, selecione **filtro** e, em seguida, selecione os valores que pretende ver.

3. Para responder a um alerta, selecione-o e reveja as informações, em seguida, selecione o recurso que foi atacado.

4. No **Descrição** campo, verá mais detalhes, incluindo a remediação recomendada.

![](media/protect-personal-data-azure-security-center/security-alerts.png)

Para obter mais instruções sobre a responder a alertas de segurança, consulte [gerir e responder a alertas de segurança no Centro de segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

Para obter ajuda na investigar alertas de segurança, a empresa pode integrar alertas ASC com a sua própria solução SIEM, utilizando [a integração de registo do Azure](https://aka.ms/AzLog).

#### <a name="how-do-i-manage-security-incidents"></a>Como gerir a incidentes de segurança?

ASC, um incidente de segurança é uma agregação de todos os alertas para um recurso que se alinham com padrões de cadeia de eliminação. Um Incidente apresenta a lista de alertas relacionados, o que lhe permitir obter mais informações sobre cada ocorrência. Os incidentes surgem no mosaico alertas de segurança e painel.

Para consultar e gerir incidentes de segurança, efetue o seguinte procedimento:

1. Selecione o **alertas de segurança** mosaico. Se for detetado um incidente de segurança, este será apresentado no gráfico de alertas de segurança. Pode ter um ícone que é diferente do outros alertas.

2. Seleccione o incidente para ver mais detalhes sobre este incidente de segurança. Detalhes adicionais incluem a respetiva descrição completa, a gravidade, o estado atual, o recurso atacado, os passos de remediação para o incidente e os alertas que foram incluídos neste incidente.

Pode filtrar para ver **apenas incidentes**, **alertas apenas**, ou **ambos**.

#### <a name="how-do-i-access-the-threat-intelligence-report"></a>Como aceder a ameaça Intelligence relatório?

ASC analisa informações de várias origens para identificar ameaças. Para ajudá-lo a resposta a incidentes equipas investigarem e remedeie ameaças, o Centro de segurança inclui um relatório de intelligence de ameaças que contém informações sobre a ameaça que foi detetada.

Centro de segurança tem três tipos de relatórios de ameaças que podem variar por ataque.
Os relatórios disponíveis são:

- Relatório de grupo de atividade: fornece dives profundas para os atacantes, os seus objetivos e táticas de TI.

- Relatório de campanha: centra-se nos detalhes das campanhas de ataque específico.

- Relatório de resumo de ameaças: abrange todos os itens os dois relatórios anteriores.

Este tipo de informações é muito útil durante o processo de resposta a incidentes, onde existe uma investigação em curso para compreender a origem do ataque, motivações o atacante e o que fazer de correção para atenuar este problema daqui para a frente.

1. Para aceder ao relatório de intelligence ameaça, efetue o seguinte:

2. Selecione o **alertas de segurança** mosaico no dashboard do ASC.

3. Selecione o alerta de segurança para o qual pretende obter mais informações.

4. No **relatórios** campo, clique na ligação para o relatório de intelligence de ameaça.

5. Esta ação irá abrir o ficheiro PDF, que pode transferir.

![](media/protect-personal-data-azure-security-center/security-alerts-suspicious-process.png)

Para obter informações adicionais sobre o ASC ameaça intelligence de relatórios, consulte [relatório de Intelligence de ameaças de centro de segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

### <a name="assessment"></a>Avaliação

Para ajudar a testar, avaliação e avaliação da sua postura de segurança, ASC fornece para avaliação da vulnerabilidade integrada com Qualys agentes de nuvem, como parte do respectivo componente de recomendações de máquina virtual.

O agente de Qualys relatórios de dados de vulnerabilidade para a plataforma de gestão Qualys, que, em seguida, envia dados de monitorização de estado de funcionamento e de vulnerabilidade de volta para ASC. A recomendação para adicionar uma solução de avaliação de vulnerabilidade é apresentada no **recomendações** painel no dashboard do ASC.

Quando a solução de avaliação de vulnerabilidades estiver instalada na VM de destino, o Centro de Segurança analisa a VM para detetar e identificar vulnerabilidades do sistema e das aplicações. Os problemas detetados são apresentados na opção **Recomendações de Máquinas Virtuais**.

#### <a name="how-do-i-implement-a-vulnerability-assessment-solution"></a>Como posso implementar uma solução de avaliação de vulnerabilidade? 

Se uma Máquina Virtual não tiver uma solução de avaliação de vulnerabilidade integrada já implementada, o Centro de segurança recomenda que esteja instalado.

1. No dashboard do ASC, no **recomendações** painel, selecione **adicionar uma solução de avaliação de vulnerabilidade.**

2. Selecione as VMs onde pretende instalar a solução de avaliação de vulnerabilidade.

3. Clique em **instalar em VMs [diversas].**

4. Selecione uma solução de parceiros no Azure Marketplace, ou em **utilizar solução existente,** selecione **Qualys.**

5. Pode ativar as definições de atualização automática ou desativar **soluções de parceiros** painel.

Para obter mais instruções sobre como implementar uma solução de avaliação de vulnerabilidade, consulte [avaliação da vulnerabilidade no Centro de segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

## <a name="next-steps"></a>Passos seguintes

- [Guia de introdução do Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-get-started)

- [Introdução ao centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

- [A integração de alertas do Centro de segurança do Azure com a integração de registos do Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration)

- [Aumento do Centro de segurança do Azure com avaliação de vulnerabilidade integrada](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/16/boost-azure-security-center-with-integrated-vulnerability-assessment/)
