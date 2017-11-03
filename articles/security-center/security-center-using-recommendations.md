---
title: "As recomendações do Centro de segurança do Azure de utilização para melhorar a segurança | Microsoft Docs"
description: " Saiba como utilizar políticas de segurança e as recomendações no Centro de segurança do Azure para o ajudar a mitigar um ataque de segurança. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: terrylan
ms.openlocfilehash: 0616f5e501324bfd821c1455ce234602f1fcf1bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Recomendações do Centro de segurança do Azure de utilização para melhorar a segurança
Pode reduzir as possibilidades de um evento de segurança ao configurar uma política de segurança e, em seguida, implementar as recomendações fornecidas pelo centro de segurança do Azure. Este artigo mostra como utilizar políticas de segurança e as recomendações no Centro de segurança para ajudar a mitigar um ataque de segurança.

> [!NOTE]
> Este artigo baseia-se as funções e os conceitos apresentados no Centro de segurança [guia de operações e planeamento](security-center-planning-and-operations-guide.md). É uma boa ideia rever o guia de planeamento antes de continuar.
>
>

## <a name="managing-security-recommendations"></a>Gerir recomendações de segurança
Uma política de segurança define o conjunto de controlos que são recomendados para recursos dentro da subscrição especificada ou o grupo de recursos. No Centro de segurança, é possível definir políticas de acordo com requisitos de segurança da sua empresa. Para obter mais informações, consulte [definir políticas de segurança no Centro de segurança](security-center-policies.md).

Políticas de segurança para grupos de recursos são herdadas do nível da subscrição.

![Herança da política de segurança][1]

Se precisar de políticas personalizadas em grupos de recurso específico, pode desativar a herança no grupo de recursos. Para desativar, defina herança para exclusivos no painel de política de segurança e personalizar os controlos de centro de segurança mostra recomendações para.

Por exemplo, se tiver cargas de trabalho que não requerem a política de SQL da base de dados transparente dados encriptação (TDE), desative a política ao nível da subscrição e ative-a apenas nos grupos de recursos onde a SQL TDE é necessária.

> [!NOTE]
> Caso exista um conflito entre a política de nível da subscrição e a política de nível do grupo de recursos, a política de nível do grupo de recursos tem prioridade.
>
>

O Centro de Segurança analisa o estado de segurança dos seus recursos do Azure. Quando o Centro de segurança identificar potenciais vulnerabilidades de segurança, cria recomendações com base nos controlos definidos na política de segurança. As recomendações ajudá-lo durante o processo de configurar os controlos de segurança necessário.

Recomendações de política atual no Centro de segurança focarem-se no atualizações do sistema, configuração do SO, grupos de segurança de sub-redes e máquinas virtuais (VMs), auditoria de base de dados SQL, TDE de base de dados do SQL Server, de rede e firewalls de aplicação web. Para a mais atualizadas à sua cobertura de recomendações do Centro de segurança, consulte [gerir recomendações de segurança no Centro de segurança](security-center-recommendations.md).

## <a name="scenario"></a>Cenário
Este cenário mostra como utilizar o Centro de segurança para ajudar a reduzir as possibilidades de um incidente de segurança significativos ao centro de segurança recomendações de monitorização e colocar a ação. O cenário utiliza a empresa fictícia, Contoso e funções apresentadas no Centro de segurança [guia de operações e planeamento](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). As funções representam pessoas e equipas podem utilizar o Centro de segurança para executar tarefas relacionadas com segurança diferentes. As funções são:

![Funções do cenário][2]

Contoso recentemente migrada alguns dos seus recursos no local para o Azure. A Contoso pretende implementar e manter as proteções que reduzem os respetivos vulnerabilidade a um ataque de segurança dos respetivos recursos na nuvem.

## <a name="recommended-solution"></a>Solução recomendada
É uma solução utilizar o Centro de segurança para evitar e detetar vulnerabilidades de segurança. Contoso tem acesso ao centro de segurança através da respetiva subscrição do Azure. O [escalão gratuito](security-center-pricing.md) do Centro de segurança é ativado automaticamente em todas as subscrições do Azure e recolha de dados está ativada em todas as VMs na sua subscrição.

Configura o David, segurança de TI da Contoso, um **política de segurança** utilizando o Centro de segurança. Centro de segurança analisa o estado de segurança de recursos do Azure da Contoso. Quando o Centro de segurança identificar potenciais vulnerabilidades de segurança, cria **recomendações** com base nos controlos definidos na política de segurança.

Jorge, um proprietário da carga de trabalho de nuvem, é responsável por implementar e manter as proteções de acordo com as políticas de segurança da Contoso. Jorge pode monitorizar as recomendações criadas pelo centro de segurança a aplicar a proteção. As recomendações ajudá Jorge durante o processo de configuração de controlos de segurança necessário.

Ordem de Jorge implementar e manter a proteção e eliminar vulnerabilidades de segurança, ele tem de:

- Recomendações de segurança de monitor fornecidas pelo centro de segurança
- Avaliar as recomendações de segurança e decidir se ele deve aplicar ou dispensar
- Aplicar as recomendações de segurança

Vamos seguir passos de Jorge para ver como ele utiliza as recomendações do Centro de segurança para ajudá-lo durante o processo de configuração de controlos para eliminar vulnerabilidades de segurança.

## <a name="how-to-implement-this-solution"></a>Como implementar esta solução
Jorge inicia sessão [portal do Azure](https://azure.microsoft.com/features/azure-portal/) e abre a consola do Centro de segurança. Como parte do seus atividades de monitorização diárias, ele verifica se existem recomendações de segurança, efetuando os seguintes passos:

1. Jorge seleciona o **recomendações** mosaico para abrir **recomendações**.
   ![Selecione o mosaico de recomendações][3]
2. Jorge revê a lista de recomendações. Ele verá que o Centro de segurança forneceu a lista de recomendações por ordem de prioridade, de prioridade mais elevada a prioridade mais baixa. Ele decide uma recomendação de prioridade alta na lista de endereços. Seleciona **instalar o Endpoint Protection** em **recomendações**.
3. **Instalar o Endpoint Protection** abre-apresentar uma lista de VMs sem antimalware ativado. Jorge revê a lista de VMs, seleciona todas as VMs e, em seguida, seleciona **instalar em 3 VMs**.
   ![Install endpoint protection][4] (Instalar a proteção de ponto final)
4. **Selecione o Endpoint Protection** abre-se de fornecer Jorge duas soluções antimalware. Jorge seleciona o **Antimalware da Microsoft** solução.
5. Informações adicionais sobre a solução antimalware são apresentadas. Jorge seleciona **criar**.
   ![Antimalware da Microsoft][5]
6. Jorge introduz as definições de configuração necessárias em **instalar** e seleciona **OK**.

[Microsoft Antimalware](../security/azure-security-antimalware.md) já está ativo nas VMs selecionadas.

Jorge continua a mover através de alta prioridade e recomendações de prioridade média, tomar decisões na implementação. Jorge referencia o [gerir recomendações de segurança](security-center-recommendations.md) artigo para compreender as recomendações e que cada um faz se ele aplica-se.

Jorge aprende que [Microsoft Security Response Center (MSRC)](../security/azure-security-response-center.md) efetua a segurança selecione monitorização de rede do Azure e a infraestrutura e recebe queixas intelligence e abuso de ameaças de terceiros. Se Jorge fornece os detalhes de contacto de segurança para a subscrição do Azure da Contoso, os contactos Microsoft Contoso se o MSRC Deteta dados de cliente que Contoso tiverem sido acedidos por uma entidade ilícita ou não autorizada. Vamos seguir Jorge como ele aplica-se a **fornecer detalhes de contacto de segurança** recomendação (uma recomendação com gravidade média na lista de recomendações acima).

1. Jorge seleciona **fornecer detalhes de contacto de segurança** em **recomendações**, que abre **fornecer detalhes de contacto de segurança**.
2. Jorge seleciona a subscrição do Azure para fornecer informações de contacto. Um segundo **fornecer detalhes de contacto de segurança** abre o painel.
   ![Detalhes de contacto de segurança][6]
3. Em **fornecer detalhes de contacto de segurança**, introduz Jorge:

  - os endereços de correio eletrónico de contacto de segurança separados por vírgulas (não há um limite ao número de endereços de e-mail que ele pode introduzir)
  - segurança contactar o número de telefone

4. Jorge também ativa a opção **enviar-me mensagens de correio eletrónico sobre alertas** para receber e-mails sobre alertas de elevada gravidade.
5. Jorge seleciona **OK** para aplicar as informações de contacto de segurança para a subscrição da Contoso.

Por fim, Jorge revê a recomendação de baixa prioridade **vulnerabilidades do SO remediar** e determina a que esta recomendação não é aplicável. Ele pretende ignorar a recomendação. Jorge seleciona reticências que aparecem à direita e, em seguida, seleciona **dispensar**.
   ![Ignorar a recomendação][7]

## <a name="conclusion"></a>Conclusão
Monitorização recomendações no Centro de segurança pode ajudar a eliminar vulnerabilidades de segurança antes de ocorre um ataque. Pode impedir que um incidente de segurança ao implementar e manter a proteção com as políticas de segurança no Centro de segurança.

## <a name="next-steps"></a>Passos seguintes
Este cenário mostrou como utilizar políticas de segurança e as recomendações no Centro de segurança para ajudar a mitigar um ataque de segurança. Consulte o [cenário de resposta a incidentes](security-center-incident-response.md) para saber como é que uma resposta a incidentes planear no local antes de um ataque ocorre.

Para saber mais acerca do Centro de segurança, consulte:

* [Monitorização de estado de funcionamento de segurança](security-center-monitoring.md) — Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
* [Gerir e responder a alertas de segurança](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança.
* [Monitorização e a processar eventos de segurança](security-center-events-dashboard.md) - Saiba como monitorizar e recolher eventos de segurança do processo ao longo do tempo.
* [Monitorizar soluções de parceiros](security-center-partner-solutions.md) — Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — obtenha as mais recentes notícias de segurança do Azure e as informações.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png
