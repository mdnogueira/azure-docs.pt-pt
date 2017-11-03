---
title: "Configurar notificações e modelos de e-mail na API Management do Azure | Microsoft Docs"
description: "Saiba como configurar notificações e modelos de e-mail na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: ee25f26d-4752-433b-af9c-3817db38aed5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 2029405e4fa05c061cdf7b38fcaa05dd38f9c804
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Como configurar notificações e modelos de e-mail na API Management do Azure
API Management fornece a capacidade para configurar notificações para eventos específicos e para configurar os modelos de e-mail que são utilizados para comunicar com os administradores e programadores de uma instância de API Management. Este tópico mostra como configurar notificações para os eventos disponíveis e fornece uma descrição geral de configuração de modelos de e-mail utilizados estes eventos.

## <a name="publisher-notifications"></a>Configurar notificações de publicador
Para configurar notificações, clique em **portal do publicador** no Portal do Azure para o seu serviço de API Management. Isto leva-o para o portal do publicador da API Management.

![Portal do publicador][api-management-management-console]

> [!NOTE] 
> Se ainda não criou uma instância de serviço de Gestão de API, consulte [Criar uma instância de serviço de Gestão de API][Create an API Management service instance] no tutorial [Introdução à Gestão de API do Azure][Get started with Azure API Management].

Clique em **notificações** do **API Management** menu à esquerda para ver as notificações disponíveis.

![Notificações de publicador][api-management-publisher-notifications]

A lista seguinte de eventos pode ser configurada para notificações.

* **Pedidos de subscrição (exigir aprovação)** -os destinatários de e-mail especificado e os utilizadores irão receber notificações por e-mail sobre pedidos de subscrição para exigir a aprovação de produtos de API.
* **Novas subscrições** -os destinatários de e-mail especificado e os utilizadores irão receber notificações por e-mail sobre novas subscrições de produto de API.
* **Pedidos de Galeria de aplicações** -os destinatários de e-mail especificado e os utilizadores irão receber notificações por e-mail quando novas aplicações são enviadas para a Galeria de aplicações.
* **BCC** -os destinatários de e-mail especificado e os utilizadores irão receber o e-mail oculta carbono cópias de todas as mensagens de correio eletrónico enviadas para os programadores.
* **Novo problema ou comentário** - os destinatários de e-mail especificado e os utilizadores irão receber notificações por e-mail quando um novo problema ou comentário é submetido no portal de programador.
* **Mensagem de fecho conta** -os destinatários de e-mail especificado e os utilizadores irão receber notificações por e-mail quando uma conta for fechada.
* **Limite de quota de subscrição aproximado** -os seguintes destinatários de e-mail e os utilizadores irão receber notificações por e-mail quando a utilização de subscrição obtém próximo da quota de utilização.

Para cada evento, pode especificar os destinatários de e-mail utilizando a caixa de texto de endereço de e-mail ou pode selecionar os utilizadores a partir de uma lista.

Para especificar os endereços de e-mail para ser notificado, introduzi-las na caixa de texto de endereço de correio eletrónico. Se tiver vários endereços de e-mail, separá-los com vírgulas.

![Destinatários das notificações][api-management-email-addresses]

Para especificar os utilizadores para ser notificado, clique em **Adicionar destinatário**, selecione a caixa junto as que os utilizadores notificados e clique em **OK**.

> [!NOTE] 
> Apenas os administradores são apresentados na lista.


Depois de configurar os destinatários de notificação, clique em **guardar** para aplicar os destinatários de notificação atualizado.

> [!NOTE] 
> Se navegar away do **publicador notificações** separador portal do publicador alerta-o se existe alterações não guardadas.


## <a name="email-templates"></a>Configurar modelos de e-mail
API Management fornece modelos de e-mail para as mensagens de e-mail que são enviadas MBS administrar e utilizar o serviço. Os modelos de e-mail seguintes são fornecidos.

* Submissão de Galeria de aplicações aprovada
* Letra de farewell de programador
* Limite de quota de programador aproximar-se de notificação
* Convidar utilizadores
* Novo comentário adicionado a um problema
* Novo problema recebido
* Nova subscrição ativada
* Confirmação de subscrição renovada
* Recusar o pedido de subscrição
* Foi recebido um pedido subscrição

Estes modelos podem ser modificados conforme pretendido.

Para ver e configurar os modelos de e-mail para a instância de API Management, clique em **notificações** do **API Management** menu à esquerda e selecione o **modelos de E-Mail** separador.

![Modelos de E-mail][api-management-email-templates]

Para ver ou modificar um modelo específico, selecione-à partir de **modelos** na lista pendente.

![Lista de modelos de e-mail][api-management-email-templates-list]

Cada modelo de correio eletrónico tem um requerente em texto simples e uma definição de corpo em formato HTML. Cada item pode ser personalizado conforme pretendido.

![Editor de modelo de correio eletrónico][api-management-email-template]

O **parâmetros** lista contém uma lista de parâmetros, que quando inseridas no assunto ou do corpo, será substituído o valor designado quando o e-mail é enviado. Para introduzir um parâmetro, coloque o cursor onde pretende que o parâmetro ir e clique na seta à esquerda do nome do parâmetro.

Clique em **pré-visualização** ou **enviar um teste** para ver como o e-mail irá procurar ou enviar um e-mail de teste.

> [!NOTE] 
> Os parâmetros não são substituídos pelos valores reais quando pré-visualização ou enviar um teste.

Para guardar as alterações ao modelo de correio eletrónico, clique em **guardar**, ou para cancelar a alterações, clique em **Cancelar**.
 

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
