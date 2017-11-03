---
title: Ativar estado da empresa em Roaming no Azure Active Directory | Microsoft Docs
description: "Perguntas mais frequentes sobre as definições de Roaming de estado empresarial em dispositivos Windows. Roaming de estado empresarial fornece aos utilizadores uma experiência unificada entre os respetivos dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo."
services: active-directory
keywords: roaming, nuvem do windows, como ativar o roaming de estado empresarial de estado empresarial
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: markvi
ms.openlocfilehash: 71212d11452d5f263b8621e1f0c13f9edd744618
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Ativar o Roaming de Estado Empresarial no Azure Active Directory
Roaming de estado empresarial está disponível para qualquer organização com um Azure AD Premium ou Enterprise Mobility + licença de segurança (EMS). Para obter mais informações sobre como obter uma subscrição do Azure AD, consulte o [página de produto do Azure AD](https://azure.microsoft.com/services/active-directory).

Quando ativar Roaming de estado empresarial, a organização é concedida automaticamente uma licença gratuita de utilização limitada para o Azure Rights Management. Esta subscrição gratuita está limitada a encriptação e desencriptação de definições da empresa e dados da aplicação sincronizados por Roaming de estado empresarial. Tem de ter [uma subscrição paga](https://azure.microsoft.com/pricing/details/active-directory/) para utilizar as funcionalidades completas do Azure Rights Management.

## <a name="to-enable-enterprise-state-roaming"></a>Para ativar o Roaming de estado empresarial

1. Inicie sessão no [Centro de administração do Azure AD](https://aad.portal.azure.com/).

2. Selecione **do Azure Active Directory** &gt; **dispositivos** &gt; **definições do dispositivo**.

3. Selecione **os utilizadores poderão sincronizar as definições e dados da aplicação em todos os dispositivos**. Para obter mais informações, consulte [como configurar as definições do dispositivo](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
  ![imagem da definição de dispositivo com a etiqueta dos utilizadores poderão sincronizar as definições e dados da aplicação em todos os dispositivos](./media/active-directory-windows-enterprise-state-roaming-enable/device-settings.png)
  
Para um dispositivo Windows 10 utilizar o serviço de Roaming de estado empresarial, o dispositivo tem de ser autenticado através de uma identidade do Azure AD. Para dispositivos que estão associados ao Azure AD, a identidade do utilizador primário início de sessão é a identidade do Azure AD, pelo que é necessária nenhuma configuração adicional. Para dispositivos que utilizam o Active Directory no local, o administrador de TI tem [ligar os dispositivos associados a um domínio ao Azure AD para experiências do Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## <a name="data-storage"></a>Armazenamento de dados
Roaming de estado empresarial dados estão alojados numa ou várias [regiões do Azure](https://azure.microsoft.com/regions/) que melhores alinham com o valor de país/região, definido na instância do Azure Active Directory. Dados de Roaming de estado empresarial estão particionados com base nas três regiões geográficas principais: América do Norte, EMEA e APAC. Dados de Roaming de estado empresarial para o inquilino encontra localmente com a região geográfica e não são replicados em regiões.  Por exemplo:
Valor de país/região | os seus dados tiverem alojados no
---------------------|-------------------------
Um país EMEA como "França" ou "Zambia" | uma ou das regiões do Azure dentro da Europa 
Um país Norte American como "Dos Estados Unidos" ou "Canadá" | uma ou mais das regiões do Azure dentro EUA
Um país APAC como "Austrália" ou "Nova Zelândia" | uma ou mais das regiões do Azure na Ásia
Sul americano e Antarctica regiões | um ou mais regiões do Azure EUA

O valor de país/região é definido como parte do processo de criação de diretórios do Azure AD e não pode ser modificado posteriormente. Se precisar de mais detalhes sobre a localização de armazenamento de dados, um ticket [suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Ver o estado de sincronização do dispositivo por utilizador
Siga estes passos para ver um relatório de estado de sincronização de dispositivos por utilizador.

1. Inicie sessão no [Centro de administração do Azure AD](https://aad.portal.azure.com/).

2. Selecione **do Azure Active Directory** &gt; **utilizadores e grupos** &gt; **todos os utilizadores**.

3. Selecione o utilizador e, em seguida, selecione **dispositivos**.

4. Em **mostrar**, selecione **dispositivos a sincronizar as definições e dados da aplicação** para mostrar o estado da sincronização.
  
  ![imagem da definição de dados de sincronização do dispositivo](./media/active-directory-windows-enterprise-state-roaming-enable/sync-status.png)
  
5. Se existirem dispositivos de sincronização para este utilizador, pode ver os dispositivos, conforme mostrado aqui.
  
  ![imagem de dados de columnar de sincronização do dispositivo](./media/active-directory-windows-enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Retenção de dados
Os dados sincronizados para o Azure utilizando o Roaming de estado empresarial são mantidos até serem eliminada manualmente ou até que os dados em questão são determinados para ser obsoletos. 

### <a name="explicit-deletion"></a>Eliminação explícita
Eliminação explícita é quando um administrador do Azure elimina um utilizador ou um diretório ou caso contrário, os pedidos explicitamente que os dados são eliminados.

* **A eliminação do utilizador**: quando um utilizador for eliminado no Azure AD, a conta de utilizador roaming de dados é eliminada após 90 para 180 dias. 
* **Eliminação de diretório**: eliminar um diretório todo no Azure AD é uma operação de imediato. Todos os dados de definições associados com que o diretório é eliminado após 90 para 180 dias. 
* **Na eliminação do pedido**: Se pretender que o administrador do Azure AD eliminar manualmente os dados ou dados de definições de um utilizador específico, o administrador pode um ticket [suporte do Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Eliminação de dados obsoleto
Dados não tem sido acedidos durante um ano ("o período de retenção") serão tratados como obsoleto e podem ser eliminados do Azure. O período de retenção está sujeita a alterações, mas não poderá ser inferior a 90 dias. Os dados obsoletos podem ser um conjunto específico de definições de aplicação do Windows ou todas as definições para um utilizador. Por exemplo:

* Se não há dispositivos que aceder a uma coleção de definições (por exemplo, uma aplicação é removida do dispositivo, ou um grupo de definições, tais como "Tema" está desativado para todos os dispositivos de um utilizador), em seguida, essa coleção torna-se obsoleta após o período de retenção e podem ser eliminada . 
* Se um utilizador tiver desativado a sincronização de definições em todos os dispositivos sua, em seguida, nenhum dos dados de definições que serão acedidas e todos os dados de definições para que o utilizador serão ficou obsoletos e podem ser eliminados após o período de retenção. 
* Se o administrador de diretório do Azure AD desligar Roaming de estado empresarial para o diretório de todo, em seguida, todos os utilizadores desse diretório irá parar a sincronização de definições e todos os dados de definições para todos os utilizadores serão ficou obsoletos e podem ser eliminados após o período de retenção. 

### <a name="deleted-data-recovery"></a>Recuperação de dados eliminadas
A política de retenção de dados não é configurável. Depois dos dados são eliminados permanentemente, não é recuperável. No entanto, os dados de definições são eliminados apenas a partir do Azure, não a partir do dispositivo do utilizador final. Se qualquer dispositivo mais tarde volta a ligar ao serviço Roaming de estado empresarial, as definições são novamente sincronizadas com êxito e armazenadas no Azure.

## <a name="related-topics"></a>Tópicos relacionados
* [Descrição geral do Roaming de estado empresarial](active-directory-windows-enterprise-state-roaming-overview.md)
* [As definições e roaming FAQ de dados](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Definições de política e o MDM para sincronização de definições de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Referência de definições de roaming Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Resolução de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
