---
title: "Assistente de instalação executando novamente o Azure AD Connect | Microsoft Docs"
description: "Explica como funciona o segundo o Assistente de instalação do tempo que executá-la."
keywords: "O Assistente de instalação do Azure AD Connect permite-lhe configurar as definições de manutenção na segunda vez que executá-la"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 42855b785c0ab334e33a622c8db912ce2438c627
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Sincronização do Azure AD Connect: executar o Assistente de instalação de uma segunda vez
Na primeira vez que executar o Assistente de instalação do Azure AD Connect, explica como configurar a sua instalação. Se executar novamente o Assistente de instalação, oferece opções de manutenção.

Pode encontrar o Assistente de instalação no menu Iniciar com o nome **do Azure AD Connect**.

![Menu Iniciar](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Quando iniciar o Assistente de instalação, verá uma página com estas opções:

![Página com uma lista de tarefas adicionais](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Se tiver instalado o AD FS com o Azure AD Connect, terá ainda mais opções. As opções adicionais tiver para AD FS estão documentados na [gestão ADFS](active-directory-aadconnect-federation-management.md#manage-ad-fs).

Selecione uma das tarefas e clique em **seguinte** para continuar.

> [!IMPORTANT]
> Enquanto tiver o Assistente de instalação, abrir, são suspensas todas as operações no motor de sincronização. Certifique-se que fechar o Assistente de instalação, assim que concluir as alterações de configuração.
>
>

## <a name="view-current-configuration"></a>Ver a configuração atual
Esta opção fornece-lhe uma vista rápida do que as suas opções atualmente configuradas.

![Página com uma lista de todas as opções e o respetivo estado](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Clique em **anterior** para voltar atrás. Se selecionar **saída**, feche o Assistente de instalação.

## <a name="customize-synchronization-options"></a>Personalizar opções de sincronização
Esta opção é utilizada para efetuar alterações à configuração de sincronização. Verá um subconjunto das opções do caminho de instalação de configuração personalizada. Ver esta opção, mesmo se tiver utilizado inicialmente instalação rápida.

* [Adicionar mais diretórios](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Para remover um diretório, consulte [eliminar um conector](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
* [Alterar o domínio e a UO filtragem](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
* Remova a filtragem de grupos.
* [Alterar as funcionalidades opcionais](active-directory-aadconnect-get-started-custom.md#optional-features).

As outras opções da instalação inicial não podem ser alteradas e não estão disponíveis. Estas opções são:

* Altere o atributo a utilizar para userPrincipalName e sourceAnchor.
* Altere o método joining para objetos de floresta diferente.
* Ative a filtragem baseada no grupo.

## <a name="refresh-directory-schema"></a>Atualizar o esquema de diretório
Esta opção é utilizada se tiver alterado o esquema de uma das seguintes no local florestas do AD DS. Por exemplo, poderá ter instalado o Exchange ou atualizado para um esquema do Windows Server 2012 com objetos de dispositivo. Neste caso, terá de ao instruir o Azure AD Connect, leia o esquema novamente do AD DS e atualize a cache. Esta ação gera também de novo as regras de sincronização. Se adicionar o esquema do Exchange, por exemplo, são adicionadas as regras de sincronização para o Exchange para a configuração.

Quando seleciona esta opção, são listados todos os diretórios na sua configuração. Pode manter a predefinição e atualizar todas as florestas ou anule a seleção de algumas delas.

![Página com uma lista de todos os diretórios no ambiente](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Configurar o modo de teste
Esta opção permite-lhe ativar e desativar o modo de teste no servidor. Podem encontrar mais informações sobre testes de modo e como são utilizadas no [operações](active-directory-aadconnectsync-operations.md#staging-mode).

A opção mostra se o teste está atualmente ativada ou desativada:  
![Opção que também está a ser mostrado o estado atual do modo de teste](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Para alterar o estado, selecione esta opção e selecione ou desmarque a caixa de verificação.  
![Opção que também está a ser mostrado o estado atual do modo de teste](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Alterar a sessão do utilizador
Esta opção permite-lhe alterar a partir da sincronização de palavra-passe para Federação ou o inverso. Não é possível alterar a **não configurar**.

Para obter mais informações sobre esta opção, consulte [sessão do utilizador](active-directory-aadconnect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o modelo de configuração utilizado pela sincronização do Azure AD Connect no [compreender de aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Noções e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
