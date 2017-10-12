---
title: "Controlos de Aplicações Adaptativos no Centro de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda-o a utilizar os controlos de aplicações adaptativos no Centro de Segurança do Azure para adicionar as aplicações em execução em VMs do Azure à lista de permissões"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: yurid
ms.openlocfilehash: 9c3a9a7255bbbdab8f4c356eb07022d7f1d242d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="adaptive-application-controls-in-azure-security-center-preview"></a>Controlos de Aplicações Adaptativos no Centro de Segurança do Azure (Pré-visualização)
Saiba como configurar o controlo de aplicações no Centro de Segurança do Azure com estas instruções.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>O que são os controlos de aplicações adaptativos do Centro de Segurança?
Os controlos de aplicações adaptativos ajudam a controlar que aplicações podem ser executadas nas suas VMs localizadas no Azure, o que, entre outros benefícios, permite proteger as VMs contra malware. O Centro de Segurança utiliza machine learning para analisar os processos que estão a ser executados na VM e ajuda-o a aplicar regras de inclusão nas listas de permissões com base nessas informações. Esta capacidade simplifica grandemente o processo de configuração e manutenção de listas de permissões de aplicações, permitindo-lhe:

- Bloquear ou alertar relativamente a tentativas de execução de aplicações maliciosas, incluindo aplicações que possam não ser detetadas pelas soluções de antimalware.
- Estar em conformidade com a política de segurança da sua organização que rege a utilização de software licenciado apenas.
- Evitar a utilização de software não desejado no seu ambiente.
- Evitar a execução de aplicações antigas e não suportadas.
- Impedir ferramentas de software específicas que não são permitidas na sua organização.
- Permitir que a equipa de TI controle o acesso a dados confidenciais através da utilização de aplicações.

> [!NOTE]
> Os controlos de aplicações adaptável estão disponíveis para os clientes do Centro de Segurança do Azure Standard como pré-visualização pública limitada. [Envie-nos](mailto:ASC_appcontrol@microsoft.com) um e-mail com os seus IDs de subscrição para aderir à pré-visualização.

## <a name="how-to-enable-adaptive-application-controls"></a>Como ativar os controlos de aplicações adaptáveis?
Os controlos de aplicações adaptáveis ajudam-no a definir um conjunto de aplicações cuja execução é permitida em grupos de recursos configurados. Esta funcionalidade só está disponível para máquinas Windows (todas as versões, clássica ou Azure Resource Manager). Siga os passos abaixo para configurar a inclusão de aplicações nas listas de permissões no Centro de Segurança:

1.  Abra o dashboard do **Centro de Segurança** e clique em **Descrição Geral**.
2.  Em **Defesa na cloud avançada**, o mosaico **Controlos de aplicações adaptáveis** mostra o número de VMs que têm, atualmente, o controlo implementado em comparação com todas as VMs. Também mostra o número de problemas que foram encontrados na última semana: 

    ![Controlos de aplicações adaptáveis](./media/security-center-adaptive-application\security-center-adaptive-application-fig1.png)

3. Clique no mosaico **Controlos de aplicações adaptáveis** para ver mais opções.

    ![controlos](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

4. A secção Grupos de Recursos contém três separadores:
    * **Recomendados**: lista de grupos de recursos para os quais o controlo de aplicações é recomendado. O Centro de Segurança utiliza machine learning para identificar as VMs que são boas candidatas para o controlo de aplicações com base no facto de executarem consistentemente as mesmas aplicações.
    * **Configurados**: lista de grupos de recursos que contém as VMs que foram configuradas com o controlo de aplicações. 
    * **Nenhuma recomendação**: lista de grupos de recursos que contêm as VMs para as quais não existem recomendações de controlo de aplicações. Por exemplo, VMs em que as aplicações estão sempre a ser alteradas e que não atingiram um estado estável.

As secções que se seguem mostram mais pormenorizadamente cada opção e como utilizá-la.

### <a name="configure-a-new-application-control-policy"></a>Configurar uma política de controlo de aplicações nova
Clique no separador **Recomendados** para ver uma lista dos grupos de recursos com recomendações de controlo de aplicações:

![Recomendado](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

A lista inclui:
- **NOME**: o nome do grupo de recursos e da subscrição
- **VMs**: o número de máquinas virtuais no grupo de recursos
- **ESTADO**: o estado das recomendações, que, na maioria dos casos, será aberto
- **GRAVIDADE**: o nível de gravidade das recomendações

Selecione um grupo de recursos para abrir a opção **Criar regras de controlo de aplicações**:

![Regras de controlo de aplicações](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

Em **Selecionar VMs**, reveja a lista de VMs recomendadas e desmarque aquelas às quais não pretende aplicar o controlo de aplicações. Em **Selecionar processos para regras de inclusão em listas de permissões**, reveja a lista de aplicações recomendadas e desmarque aquelas às quais não pretende aplicar. A lista inclui:

- **NOME**: o caminho completo da aplicação
- **PROCESSOS**: quantas aplicações residem em cada caminho
- **COMUNS**: “verdadeiro” indica que estes processos foram executados na maioria das VMs deste grupo de recursos.
- **EXPLORÁVEIS**: um ícone de aviso indica se as aplicações podem ser utilizadas por um atacante para ignorar as listas de permissões de aplicações. É vivamente recomendado que estas aplicações sejam analisadas antes da aprovação. 

Depois de concluir as suas seleções, clique no botão **Criar**. Por predefinição, o Centro de Segurança permite sempre o controlo de aplicações no modo *Auditoria*. Depois de confirmar que a lista de permissões não tem nenhum efeito adverso na sua carga de trabalho, pode alterar para o modo *Impor*.

> [!NOTE]
> Como melhor prática de segurança, o Centro de Segurança tentará sempre criar uma regra de publicador para as aplicações que devem ser adicionadas à lista de permissões e, a menos que as aplicações não tenham informações do publicador (ou seja, não sejam assinadas), é criada uma regra de caminho para o caminho completo do EXE específico.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Editar e monitorizar grupos configurados com o controlo de aplicações

Para editar e monitorizar um grupo configurado com o controlo de aplicações, clique em **CONFIGURADO**, em **Grupos de Recursos**:

![Grupos de recursos](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

A lista inclui:

- **NOME**: o nome do grupo de recursos e da subscrição
- **VMs**: o número de máquinas virtuais no grupo de recursos
- **MODO**: o modo de auditoria vai registar as tentativas de executar aplicações que não estão na lista de permissões; “bloquear” não vai permitir a execução de aplicações não presentes na lista de permissões
- **GRAVIDADE**: o nível de gravidade das recomendações

Selecione um grupo de recursos ao qual fazer alterações na página **Editar política de controlo de aplicações**.

![Proteção](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

No **Modo de Proteção**, tem a possibilidade de selecionar entre as seguintes opções:
- **Auditoria**: neste modo, a solução de controlo de aplicações não vai impor as regras e realiza apenas a auditoria à atividade nas VMs protegidas. Esta opção é recomendada para cenários nos quais pretende primeiro observar o comportamento geral antes de bloquear uma aplicação para ser executada na VM de destino.
- **Impor**: neste modo, a solução de controlo de aplicações vai impor as regras e certificar-se de que as aplicações que não têm autorização para ser executadas são bloqueadas. 

Conforme mencionado anteriormente, por predefinição, é sempre configurada uma política de controlo de aplicações nova no modo *Auditoria*. Em **Extensão da política**, pode adicionar os seus próprios caminhos de aplicações que pretende incluir na lista de permissões. Depois de adicionar estes caminhos, o Centro de Segurança irá criar as regras adequadas para estas aplicações, para além das regras que já estão em vigor. Na secção **Problemas**, são listadas todas as violações atuais.

![Problemas](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

A lista inclui:

- **PROBLEMAS**: todas as violações que foram registadas, que podem incluir o seguinte:
    - **ViolationsBlocked**: quando a solução está ativada no modo Impor e tenta executar uma aplicação que não se encontra na lista de permissões.
    - **ViolationsAudited**: quando a solução está ativada no modo Auditoria e é executada uma aplicação que não está na lista de permissões.
    - **RulesViolatedManually**: quando um utilizador tentou configurar manualmente as regras nas VMs e não através do portal de gestão do ASC.
- **N.º DE VMS**: o número de máquinas virtuais com este tipo de problema.

Se clicar em cada uma destas linhas, será redirecionado para a página [Registo de Atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), onde pode ver informações sobre todas as VMs com este tipo de violação. Se clicar nos três pontos no final de cada linha, poderá eliminar essa entrada específica. A secção **Máquinas virtuais configuradas** mostra as VMs às quais estas regras se aplicam. 

![Máquinas virtuais configuradas](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

**Regras de inclusão na lista de permissões do publicador** mostra as aplicações para as quais uma regra de publicador foi criada com base nas informações de certificado que foram encontradas para cada aplicação. Veja [Understanding Publisher Rules in Applocker](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker) (Compreender as Regras de Publicador no Applocker) para obter mais informações.

![Regras de inclusão em listas de permissões](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

Se clicar nos três pontos no final de cada linha, poderá eliminar essa regra específica. As **Regras de inclusão em listas de permissões de caminho** mostram o caminho completo de aplicação (incluindo o executável) das aplicações que não estão assinadas com um certificado digital, mas ainda são atuais nas regras de inclusão. 

> [!NOTE]
> Por predefinição, como melhor prática de segurança, o Centro de Segurança tentará sempre criar uma regra de publicador para os EXEs que devem ser adicionados à lista de permissões e, a menos que os EXEs não tenham informações do publicador (ou seja, não sejam assinados), é criada uma regra de caminho para o caminho completo do EXE específico.

![Regras de inclusão em listas de permissões de caminho](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

A lista contém:
- **NOME**: o caminho completo do executável
- **EXPLORÁVEIS**: “verdadeiro” indica se as aplicações podem ser utilizadas por um atacante para ignorar as listas de permissões de aplicações.  

Se clicar nos três pontos no final de cada linha, poderá eliminar essa regra específica. Depois de fazer as alterações, pode clicar no botão **Guardar** ou, se optar por não aplicar as alterações, clicar em **Rejeitar**.

### <a name="not-recommended-list"></a>Lista de não recomendadas

O Centro de Segurança só recomendará a inclusão de aplicações em listas de permissões para máquinas virtuais que executem um conjunto de aplicações estável. Não serão criadas recomendações se as aplicações nas VMs associadas forem constantemente alteradas. 

![Recomendação](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

A lista contém:
- **NOME**: o nome do grupo de recursos e da subscrição.
- **VMs**: o número de máquinas virtuais no grupo de recursos.

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a utilizar os controlos de aplicações adaptativos no Centro de Segurança do Azure para adicionar as aplicações em execução em VMs do Azure à lista de permissões. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de Resolução de Problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança. 
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

