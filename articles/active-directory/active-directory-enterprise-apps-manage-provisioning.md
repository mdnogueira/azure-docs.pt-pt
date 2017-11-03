---
title: "Gestão de aplicações da empresa no Azure Active Directory de aprovisionamento de utilizadores | Microsoft Docs"
description: "Saiba como gerir o aprovisionamento da conta de utilizador para aplicações da empresa utilizando o Azure Active Directory"
services: active-directory
documentationcenter: 
author: asmalser
manager: femila
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 44476bbf0e878e347d176077e08706d1afd44479
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gerir a conta de utilizador de aprovisionamento de aplicações da empresa no portal do Azure
Este artigo descreve como utilizar o [portal do Azure](https://portal.azure.com) para gerir o aprovisionamento da conta de utilizador automáticas e anular o aprovisionamento de aplicações que suportem-lo, particularmente as que foram adicionados a categoria do "emdestaque"[ Galeria de aplicações do Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Para saber mais sobre o aprovisionamento da conta de utilizador automáticas e como funciona, consulte [automatizar utilizador aprovisionamento e desaprovisionamento para aplicações de SaaS no Azure Active Directory](active-directory-saas-app-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Localizar as suas aplicações no portal
Todas as aplicações que estão configuradas para início de sessão num diretório, por um administrador do diretório a utilizar o [Galeria de aplicações do Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery), podem ser vistos e geridos no [portal do Azure](https://portal.azure.com). As aplicações podem ser encontradas no **mais serviços** &gt; **aplicações empresariais** secção do portal. Aplicações da empresa são aplicações que estão implementadas e utilizadas dentro da sua organização.

![Painel de aplicações da empresa][0]

Selecionar o **todas as aplicações** ligação no lado esquerdo mostra uma lista de todas as aplicações que tenham sido configurados, incluindo aplicações que tinham sido adicionadas a partir da galeria. Selecionar uma aplicação carrega o painel de recursos para essa aplicação, onde podem ser visualizados relatórios para essa aplicação e uma variedade de definições pode ser gerida.

Definições de aprovisionamento da conta de utilizador pode ser gerida selecionando **aprovisionamento** à esquerda.

![Painel de recursos de aplicação][1]

## <a name="provisioning-modes"></a>Modos de aprovisionamento
O **aprovisionamento** painel começa com um **modo** menu, que mostra os modos de aprovisionamento são suportados para uma aplicação empresarial e permite-lhes para ser configurado. As opções disponíveis incluem:

* **Automática** -esta opção é apresentada se suporta o Azure AD baseado na API aprovisionamento automático e/ou anular o aprovisionamento de contas de utilizador para esta aplicação. Selecionar este modo apresenta uma interface que o orienta administradores através de configuração do Azure AD para ligar a API de gestão de utilizador da aplicação, a criação de mapeamentos de contas e fluxos de trabalho que definem a forma como devem do fluxo de dados de conta de utilizador entre o Azure AD e o a aplicação e gerir o Azure AD que o serviço de fornecimento.
* **Manual** -esta opção é apresentada se do Azure AD não suporta o aprovisionamento automático de contas de utilizador para esta aplicação. Esta opção significa que os registos de conta de utilizador armazenados na aplicação têm de ser geridos através de um processo externo, com base nas capacidades de gestão e o aprovisionamento de utilizador fornecidas por essa aplicação (o que pode incluir o aprovisionamento de SAML just).

## <a name="configuring-automatic-user-account-provisioning"></a>Configurar o aprovisionamento da conta de utilizador automáticas
Selecionar o **automática** opção apresenta um ecrã que é dividido em quatro secções:

### <a name="admin-credentials"></a>Credenciais de administrador
Este é onde as credenciais necessárias para o Azure AD ligar a gestão de utilizadores da aplicação API são introduzidos. A entrada necessária varia dependendo da aplicação. Para saber mais sobre os tipos de credenciais e os requisitos para aplicações específicas, consulte o [tutorial de configuração para essa aplicação específica](active-directory-saas-app-provisioning.md).

Selecionar o **Testar ligação** botão permite-lhe testar as credenciais, fazendo com que o Azure AD tentar estabelecer ligação com a aplicação do aprovisionamento de aplicação utilizando as credenciais fornecidas.

### <a name="mappings"></a>Mapeamentos de
Este é onde os administradores podem ver e editar o fluxo de atributos de utilizador entre o Azure AD e a aplicação de destino, quando as contas de utilizador são aprovisionadas ou atualizadas.

Não há um conjunto de mapeamentos entre objetos de utilizador do Azure AD e objetos de utilizador de cada aplicação SaaS pré-configurada. Algumas aplicações, gerir outros tipos de objetos, tais como grupos ou contactos. Selecionar uma destes mapeamentos na tabela mostra o editor de mapeamento para a direita, onde podem ser visualizadas e personalizados.

![Painel de recursos de aplicação][2]

Personalizações suportadas incluem:

* Ativar e desativar mapeamentos para objetos específicos, tais como o objeto de utilizador do Azure AD para o objeto de utilizador da aplicação SaaS.
* Editar fluxo de quais os atributos do objeto de utilizador do Azure AD para o objeto de utilizador da aplicação. Para obter mais informações sobre o mapeamento de atributos, consulte [compreender os tipos de mapeamento de atributos](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).
* Filtre as ações de aprovisionamento do Azure AD efetua na aplicação de destino. Em vez de ter o Azure AD totalmente-sincronizar os objetos, pode limitar as ações executadas. Por exemplo, selecionando apenas **atualização**, do Azure AD apenas as atualizações de utilizador existente contas numa aplicação e não criar novos. Ao selecionar apenas **criar**, Azure apenas cria novas contas de utilizador, mas não atualiza já existentes. Esta funcionalidade permite aos administradores para criar mapeamentos diferentes para a criação de conta e fluxos de trabalho de atualização.

### <a name="settings"></a>Definições
Esta secção permite aos administradores para iniciar e parar o Azure AD aprovisionamento do serviço para a aplicação selecionada, bem como opcionalmente limpar a cache de aprovisionamento e reinicie o serviço.

Se o aprovisionamento estiver a ser ativado pela primeira vez para uma aplicação, ative o serviço alterando o **estado de aprovisionamento** para **no**. Isto faz com que o Azure AD aprovisionamento do serviço efetuar uma sincronização inicial, onde os utilizadores atribuídos no é lida a **utilizadores e grupos** secção, consulta a aplicação de destino para os mesmos e, em seguida, executa as ações de aprovisionamento definidas no Azure AD **mapeamentos** secção. Durante este processo, o serviço de aprovisionamento armazena em cache dados sobre as contas de utilizador que está a gerir, para que contas não gerida dentro de aplicações de destino que foram nunca no âmbito de atribuição não são afetadas por operações de aprovisionamento automatizados. Após a sincronização inicial, o serviço de aprovisionamento sincroniza automaticamente objetos de grupo e utilizador num intervalo de dez minutos.

Alterar o **estado de aprovisionamento** para **desativar** simplesmente interrompe o serviço de aprovisionamento. Neste estado, Azure não criar, atualizar ou remover quaisquer objetos de grupo ou utilizador na aplicação. Alteração do Estado para on faz com que o serviço escolher onde parou.

Selecionar o **limpar o estado atual e reiniciar a sincronização** caixa de verificação e guardar para o serviço de aprovisionamento, capturas os dados em cache sobre as contas do Azure AD está a gerir, reinicia os serviços e efetua inicial sincronização de novo. Esta opção permite aos administradores para iniciar o processo de implementação aprovisionamento através de novo.

### <a name="synchronization-details"></a>Detalhes de sincronização
Esta secção fornece detalhes adicionais sobre o funcionamento do serviço do aprovisionamento, incluindo as primeira e últimas vezes que o serviço de aprovisionamento executou a aplicação e objetos de grupo e utilizador quantas estão a ser geridos.

São fornecidas hiperligações para a **relatório de atividade de aprovisionamento**, que fornece um registo de todos os utilizadores e grupos criados, removido entre e atualizado do Azure AD e a aplicação de destino e o **derelatóriodeerrosdeaprovisionamento** que fornece mais mensagens de erro para o utilizador e objetos de grupo que falharam ao ser lidas, criado, atualizado ou removido. 

##<a name="feedback"></a>Comentários

Esperamos, como a sua experiência do Azure AD. Manter os comentários provenientes! Publique os seus comentários e ideias para melhoria no **Portal de administração** secção do nosso [fórum de comentários](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Iremos estiver entusiasmados sobre como criar conteúdo novo frio diariamente e utilize a orientações sobre a forma e definir o que devemos criar a seguir.


[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
