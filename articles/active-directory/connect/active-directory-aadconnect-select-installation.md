---
title: "O Azure AD Connect: Selecione o tipo de instalação | Microsoft Docs"
description: "Este tópico explica como selecionar o tipo de instalação a utilizar para o Azure AD Connect"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: a5697686bd1f41d581554b27ce78897963e38c74
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Selecione o tipo de instalação a utilizar para o Azure AD Connect
O Azure AD Connect tem dois tipos de instalação para a instalação do novo: Express e personalizados. Este tópico ajuda-o a decidir qual a opção a utilizar durante a instalação.

## <a name="express"></a>Express
Express é a opção mais comum e é utilizado por cerca de 90% de todas as instalações de novo. Foi concebido para fornecer uma configuração que funciona para os cenários mais comuns do cliente.

Pressupõe que:

- Tiver um único do Active Directory no local de floresta.
- Tiver uma conta de administrador de empresa que pode utilizar para a instalação.
- Tiver menos de 100 000 objetos no Active Directory no local.

Obter:

- [Sincronização de palavra-passe](active-directory-aadconnectsync-implement-password-synchronization.md) no local ao Azure AD para o início de sessão único.
- Uma configuração que se sincroniza [utilizadores, grupos, contactos e computadores Windows 10](active-directory-aadconnectsync-understanding-default-configuration.md).
- Sincronização de todos os objetos elegíveis todos os domínios e UOs todos os.
- [A atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) está ativada para se certificar de que utilize sempre a versão mais recente disponível.

Opções de onde pode continuar a utilizar rápida:

- Se não quiser sincronizar todas as OUs, ainda pode utilizar o Express e na última página, anule a seleção de **iniciar o processo de sincronização...** *. Em seguida, execute novamente o Assistente de instalação e alterar as UOs na [opções de configuração](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options) e ativar a sincronização agendada.
- Pretende ativar uma das funcionalidades no Azure AD Premium, tais como a repetição de escrita de palavras-passe. Percorra primeiro rápida para obter a instalação inicial foi concluída. Em seguida, execute novamente o Assistente de instalação e altere o [opções de configuração](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Personalizado
O caminho personalizado permite muitos mais opções que rápida. Deve ser utilizado em todos os casos em que a configuração descrita na secção anterior para express não é representativa da sua organização.

Utilize se:

- Não tem acesso a uma conta de administrador de empresa no Active Directory.
- Tiver mais do que uma floresta ou se pretende sincronizar mais do que uma floresta no futuro.
- Ter domínios na floresta não acessível a partir do servidor do Connect.
- Planeia utilizar autenticação pass-through ou de Federação para o início de sessão do utilizador.
- Pode ter mais de 100 000 objetos e tem de utilizar um servidor SQL completo.
- Planear a utilização da filtragem baseada em grupo e não apenas domínio ou filtragem baseada na UO.

## <a name="upgrade-from-dirsync"></a>Atualização do DirSync
Se estiver a utilizar o DirSync, em seguida, siga os passos em [atualizar do DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) para atualizar a sua configuração existente. Existem duas opções de atualização diferentes:

- Atualização no local para instalar o Connect no mesmo servidor.
- Implementação paralela para instalar o Connect num servidor novo enquanto o servidor do DirSync existente ainda está operacional.

## <a name="upgrade-from-azure-ad-sync"></a>Atualização do Azure AD Sync
Se estiver a utilizar o Azure AD Sync, em seguida, pode seguir o [mesmos passos](active-directory-aadconnect-upgrade-previous-version.md) como ao atualizar de uma versão de ligar a uma mais recente. Existem duas opções de atualização diferentes:

- Atualização no local para instalar o Connect no mesmo servidor.
- A migração swing para instalar o Connect num servidor novo ao existente servidor de sincronização do Azure AD ainda está operacional.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migrar a partir de FIM2010 ou MIM2016
Se estiver a utilizar o Forefront Identity Manager 2010 ou Microsoft Identity Manager 2016 com o conector do Azure AD, a única opção é uma migração. Siga os passos descritos no [swing migração](active-directory-aadconnect-upgrade-previous-version.md#swing-migration). Nos passos, substitua qualquer menção do Azure AD Sync FIM2010/MIM2016.

## <a name="next-steps"></a>Passos seguintes
Dependendo da opção que selecionou para utilizar, utilize a tabela de conteúdo para a esquerda para localizar o seu artigo com os passos detalhados.
