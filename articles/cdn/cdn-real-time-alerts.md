---
title: Alertas em tempo real de CDN do Azure | Microsoft Docs
description: "Alertas em tempo real na CDN do Microsoft Azure. Os alertas em tempo real fornecem notificações sobre o desempenho dos pontos finais no seu perfil da CDN."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 51dce1680be5f5f4387c2ba02827195bcdbe9b48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Alertas em tempo real na CDN do Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral
Este documento explica alertas em tempo real na CDN do Microsoft Azure. Esta funcionalidade fornece notificações em tempo real sobre o desempenho dos pontos finais no seu perfil da CDN.  Pode configurar por e-mail ou alertas HTTP com base em:

* Largura de Banda
* Códigos de estado
* Estados da cache
* Ligações

## <a name="creating-a-real-time-alert"></a>Criar um alerta em tempo real
1. No [portal do Azure](https://portal.azure.com), navegue para o perfil de CDN.
   
    ![Perfil da CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. No painel de perfil da CDN, clique o **gerir** botão.
   
    ![Botão de gerir do perfil de CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    É aberto o portal de gestão do CDN.
3. Coloque o cursor sobre o **análise** separador, em seguida, coloque o cursor sobre o **estatísticas em tempo real** flyout.  Clique em **alertas em tempo real**.
   
    ![Portal de gestão de CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    É apresentada a lista de configurações de alerta existentes (se aplicável).
4. Clique em de **Adicionar alerta** botão.
   
    ![Alerta botão Adicionar](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    É apresentado um formulário para criar um novo alerta.
   
    ![Novo formulário de alerta](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Se pretender que este alerta ser Active Directory ao clicar em **guardar**, verifique o **alerta ativado** caixa de verificação.
6. Introduza um nome descritivo para o alerta no **nome** campo.
7. No **tipo de suporte** lista pendente, selecione **objeto grande HTTP**.
   
    ![Tipo de suporte com os objetos de grandes dimensões HTTP selecionado](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Tem de selecionar **objeto grande HTTP** como o **tipo de suporte**.  As outras opções não são utilizadas pelo **CDN do Azure da Verizon**.  Falha ao selecionar **objeto grande HTTP** faz com que o alerta nunca sejam acionadas.
   > 
   > 
8. Criar um **expressão** para monitorizar, selecionando um **métrica**, **operador**, e **acionar valor**.
   
   * Para **métrica**, selecione o tipo de condição que pretende monitorizado.  **Mbps de largura de banda** é a quantidade de utilização de largura de banda em megabits por segundo.  **Total de ligações** é o número de ligações simultâneas de HTTP para os nossos servidores edge.  Definições dos vários Estados de cache e códigos de estado, consulte [códigos de estado de Cache do Azure CDN](https://msdn.microsoft.com/library/mt759237.aspx) e [códigos de estado de HTTP de CDN do Azure](https://msdn.microsoft.com/library/mt759238.aspx)
   * **Operador** é o operador matemática que estabelece a relação entre a métrica e o valor de Acionador.
   * **Acionar valor** é o valor de limiar que deve ser cumprido antes de é enviada uma notificação.
     
     No exemplo seguinte, a expressão criada indica que é enviada uma notificação quando o número de códigos de estado 404 é superior ao 25.
     
     ![Exemplo de alertas em tempo real de expressão](./media/cdn-real-time-alerts/cdn-expression.png)
9. Para **intervalo**, introduza a frequência gostaria que a expressão avaliada.
10. No **notificar no** lista pendente, selecione quando pretende ser notificado quando a expressão for verdadeira.
    
    * **Condição de início** indica que é enviada uma notificação quando a condição especificada é detetada pela primeira vez.
    * **Fim de condição** indica que é enviada uma notificação quando a condição especificada já não é detetada. Só pode ser acionada esta notificação depois do nosso sistema de monitorização de rede detetados que ocorreu a condição especificada.
    * **Contínua** indica que é enviada uma notificação sempre que o sistema de monitorização de rede Deteta a condição especificada. Tenha em atenção que o sistema de monitorização de rede verifica-se apenas uma vez por intervalo para a condição especificada.
    * **Condição de início e de fim** indica que é enviada uma notificação na primeira vez que a condição especificada é detetada e novamente quando a condição já não é detetada.
1. Se pretender receber notificações por e-mail, verifique o **notificar por E-Mail** caixa de verificação.  
    
    ![Notificar pelo formulário de E-Mail](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    No **para** campo, introduza o endereço de correio eletrónico, onde pretende que as notificações enviadas. Para **requerente** e **corpo**, pode deixar a predefinição ou pode personalizar a mensagem utilizando o **palavras-chave disponíveis** lista dinamicamente inserir dados de alertas quando a mensagem é enviada.
    
    > [!NOTE]
    > Pode testar a notificação por e-mail clicando a **notificação de teste** botão, mas apenas depois da configuração do alerta foi guardada.
    > 
    > 
12. Se pretender enviar notificações para ser publicado para um servidor web, marque a **notificar por HTTP Post** caixa de verificação.
    
    ![Notificar pelo formulário de HTTP Post](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    No **Url** campo, introduza o URL publicado, onde pretende que a mensagem HTTP. No **cabeçalhos** caixa de texto, introduza os cabeçalhos de HTTP para enviar no pedido.  Para **corpo**, pode personalizar a mensagem utilizando o **palavras-chave disponíveis** lista dinamicamente inserir dados de alertas quando a mensagem é enviada.  **Cabeçalhos** e **corpo** predefinido para um payload XML semelhante ao seguinte exemplo:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Pode testar a notificação de HTTP Post clicando a **notificação de teste** botão, mas apenas depois da configuração do alerta foi guardada.
    > 
    > 
13. Clique em de **guardar** botão para guardar a configuração do alerta.  Se tiver selecionado **alerta ativado** no passo 5, o alerta já está ativo.

## <a name="next-steps"></a>Passos Seguintes
* Analisar [estatísticas em tempo real na CDN do Azure](cdn-real-time-stats.md)
* Dig mais profundo com [avançadas de relatórios HTTP](cdn-advanced-http-reports.md)
* Analisar [padrões de utilização](cdn-analyze-usage-patterns.md)

