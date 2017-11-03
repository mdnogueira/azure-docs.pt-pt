---
title: "Sincronização do Azure AD Connect: impedir eliminações acidentais | Microsoft Docs"
description: "Este tópico descreve a funcionalidade de impedir eliminações acidentais (impedir eliminações acidentais) no Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: a33fb729cff5007e40820af696cfec823a3ecfde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Sincronização do Azure AD Connect: impedir eliminações acidentais
Este tópico descreve a funcionalidade de impedir eliminações acidentais (impedir eliminações acidentais) no Azure AD Connect.

Quando instalar o Azure AD Connect, impedir acidental eliminações está ativada por predefinição e configurado para não permitir uma exportação com mais do que 500 eliminações. Esta funcionalidade foi concebida para protegê-lo contra as alterações de configuração acidental e alterações ao seu diretório no local que iria afetar vários utilizadores e outros objetos.

## <a name="what-is-prevent-accidental-deletes"></a>O que está a impedir eliminações acidentais
Cenários comuns quando vir eliminações muitos incluem:

* Alterações ao [filtragem](active-directory-aadconnectsync-configure-filtering.md) onde uma toda [UO](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) ou [domínio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) é não seleccionado.
* Todos os objetos numa UO são eliminados.
* Uma UO é mudada para que todos os objetos são considerados como sendo fora do âmbito para sincronização.

O valor predefinido de 500 objetos pode ser alterado com PowerShell utilizando `Enable-ADSyncExportDeletionThreshold`. Deve configurar este valor para se ajustar ao tamanho da sua organização. Uma vez que o agendador de sincronização é executada a cada 30 minutos, o valor é o número de eliminações visto dentro de 30 minutos.

Se existirem demasiados eliminações testadas seja exportado para o Azure AD, em seguida, interrompe a exportação e receber uma mensagem de e-mail como esta:

![Impedir eliminações acidentais e-mail](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Olá (contacte technical). Em (hora) o serviço de sincronização de identidade detetado que o número de eliminações excedeu o limiar de eliminação configurado para (nome da organização). Um total de (número) de objetos foram enviadas para a eliminação nesta sincronização de identidade executada. Isto alcançado ou excedido o limiar configurado de eliminação de objetos de (número). Tem de fornecer a confirmação de que estes eliminações devem ser processadas antes que irá prosseguir. Consulte as eliminações acidentais impedindo para obter mais informações sobre o erro indicado nesta mensagem de correio eletrónico.*
>
> 

Também pode ver o estado `stopped-deletion-threshold-exceeded` quando procurar no **Synchronization Service Manager** IU para o perfil de exportação.
![Impedir eliminações acidentais da IU do Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Se isto for inesperado, investigue e executar ações corretivas. Para ver quais os objetos que estão prestes a ser eliminado, efetue o seguinte:

1. Iniciar **serviço de sincronização** no Menu Iniciar.
2. Aceda a **conectores**.
3. Seleccione o conector com o tipo **do Azure Active Directory**.
4. Em **ações** à direita, selecione **espaço de conector de pesquisa**.
5. No pop-up em **âmbito**, selecione **desligado, uma vez que** e escolha uma hora no passado. Clique em **pesquisa**. Esta página fornece uma vista de todos os objetos prestes a ser eliminada. Ao clicar em cada item, pode obter informações adicionais sobre o objeto. Também pode clicar em **definição de coluna** para adicionar atributos adicionais para ser visíveis na grelha.

![Espaço de conector de pesquisa](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Se forem pretendidas todas as eliminações, em seguida, efetue o seguinte:

1. Para obter o limiar de eliminação atual, execute o cmdlet do PowerShell `Get-ADSyncExportDeletionThreshold`. Forneça uma conta de Administrador Global do AD do Azure e a palavra-passe. O valor predefinido é 500.
2. Temporariamente desativar esta proteção e permitir que esses eliminações percorrer, execute o cmdlet do PowerShell: `Disable-ADSyncExportDeletionThreshold`. Forneça uma conta de Administrador Global do AD do Azure e a palavra-passe.
   ![Credenciais](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
3. Com o conector Azure Active Directory ainda selecionada, selecione a ação **executar** e selecione **exportar**.
4. Para reativar a proteção, execute o cmdlet do PowerShell: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Substitua o valor que é reparado ao obter o limiar de eliminação atual 500. Forneça uma conta de Administrador Global do AD do Azure e a palavra-passe.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Noções e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
