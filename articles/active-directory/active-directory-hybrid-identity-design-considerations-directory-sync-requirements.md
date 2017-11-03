---
title: "Azure Active Directory híbrida identidade considerações de design - determine os requisitos de sincronização de diretórios | Microsoft Docs"
description: "Identificar requisitos de que são necessárias para sincronizar todos os utilizadores entre = no local e na nuvem para a empresa."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 5ef87e606f055359ca325befd6048353ce57ca2b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="determine-directory-synchronization-requirements"></a>Determinar os requisitos de sincronização de diretórios
A sincronização é tudo para fornecer aos utilizadores uma identidade na nuvem com base na respetiva identidade no local. Se pretende ou não irão utilizar a conta sincronizada para autenticação ou autenticação federada, os utilizadores têm de ter uma identidade na nuvem.  Esta identidade tem de ser mantidos e atualizada periodicamente.  As atualizações podem tomar várias formas, de alterações de título a alterações de palavra-passe.  

Início através da avaliação das organizações requisitos de utilizador e a solução de identidade no local. Esta avaliação é importante definir os requisitos técnicos para como identidades de utilizador serão criadas e mantidas na nuvem.  Para a maioria das organizações, Active Directory está no local e será ser o diretório no local que os utilizadores serão por sincronizadas a partir do, no entanto em alguns casos este não será o caso.  

Certifique-se responder às seguintes questões:

* Tem uma floresta do AD, vários ou nenhum?
  
  * Quantos diretórios do Azure AD será tem de ser a sincronizar com o?
    
    1. Está a utilizar a filtragem?
    2. Tem vários servidores do Azure AD Connect planeados?
* Atualmente tem uma sincronização ferramenta no local?
  
  * Se Sim, efetua os seus utilizadores se os utilizadores têm uma virtual/integração de diretórios de identidades?
* É necessário qualquer outro diretório no local que pretende sincronizar (por exemplo, diretório LDAP, base de dados de RH, etc.)?
  * Vai fazer qualquer GALSync?
  * O que é o estado atual da UPNs na sua organização? 
  * Tem um diretório diferente que os utilizadores autenticam-se contra?
  * A sua empresa utiliza o Microsoft Exchange?
    * Se planear de ter uma implementação híbrida do exchange?

Agora que tem uma ideia sobre os requisitos de sincronização, terá de determinar que ferramenta é o correto para satisfazer estes requisitos.  A Microsoft fornece várias ferramentas para efetuar a integração de diretórios e sincronização.  Consulte o [tabela de comparação de ferramentas de integração de diretórios de identidade híbrida](active-directory-hybrid-identity-design-considerations-tools-comparison.md) para obter mais informações. 

Agora que tem os requisitos de sincronização e a ferramenta que irá realizar esta tarefa para a sua empresa, terá de avaliar as aplicações que utilizam estes serviços de diretório. Esta avaliação é importante definir os requisitos técnicos para integrar estas aplicações para a nuvem. Certifique-se responder às seguintes questões:

* Estas aplicações ser movidas para a nuvem e utilizar o diretório?
* Existem atributos especiais que precisam de ser sincronizados para a nuvem para que estas aplicações podem utilizá-los com êxito?
* Estas aplicações terá de ser escritos novamente para tirar partido da autenticação em nuvem?
* Estas aplicações continuarão a em direto no local, enquanto que os utilizadores aceder aos mesmos utilizando a identidade de nuvem?

Também precisa de determinar a sincronização de diretórios de requisitos e restrições de segurança. Esta avaliação é importante para obter uma lista dos requisitos que serão necessários para criar e manter as identidades dos utilizadores na nuvem. Certifique-se responder às seguintes questões:

* Onde o servidor de sincronização estarão localizado?
* Irá ser associado a um domínio?
* O servidor irá estar localizado numa rede restrita atrás de uma firewall, tais como uma rede de Perímetro?
  * Será poderá abrir as portas de firewall necessárias para suportar a sincronização?
* Tem um plano de recuperação após desastre para o servidor de sincronização?
* Tiver uma conta com as permissões corretas para todas as florestas que pretende de sincronização com?
  * Se a sua empresa não souber a resposta para esta questão, consulte a secção "Permissões para a sincronização de palavra-passe" no artigo [instalar o serviço de sincronização do Active Directory do Azure](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) e determinar se já tiver uma conta com estas permissões, ou se precisar de criar um.
* Se tiver sincronização estiverem floresta é o servidor de sincronização foi possível obter para cada floresta?

> [!NOTE]
> Certifique-se de que toma nota de cada resposta e que compreende os fundamentos. [Determinar os requisitos de resposta a incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) abordará as opções disponíveis. Ao responder a estas questões, vai selecionar que opções melhor se adapta às sua empresa precisa.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar os requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Consultar também
[Descrição geral das considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)

