---
title: "Descrição geral de reposição do Azure AD personalizada de palavra-passe | Microsoft Docs"
description: "Que efetue de reposição de palavra-passe self-service podem do Azure AD para a sua organização?"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: f577ee95c34f9bc3065c026bc943d30622b9d658
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Azure AD self-service reposição palavra-passe para profissionais de TI

Reposição de palavra-passe self-service do Azure Active Directory (Azure AD) (SSPR) permite aos utilizadores para repor as palavras-passe por si próprios quando e onde é que necessitam, permitindo que os administradores de controlo do como obtém a reposição de palavra-passe. Os utilizadores já não tem de chamar o suporte técnico apenas para repor a palavra-passe.

* **Alteração de palavra-passe self-service** – utilizador sabe a palavra-passe, mas pretende alterá-lo para algo de novo.
* **Reposição de palavra-passe self-service** – utilizador não é possível iniciar sessão e pretende repor a palavra-passe a utilizar um ou mais dos seguintes métodos de autenticação validado.
   * Mensagem de texto para um telemóvel validado
   * Chamada telefónica para um telefone do escritório ou mobile validado
   * Por e-mail para uma conta de e-mail secundário validado
   * Respostas às respectivas perguntas de segurança
* **Desbloqueio de conta personalizada** – não é possível ao utilizador inicie sessão com a palavra-passe foi bloqueada e pretende desbloquear a conta sem a intervenção do administrador utilizam os respetivos métodos de autenticação.

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Por que motivo escolhê Azure reposição de palavra-passe self-service de AD

* **Reduzir os custos** como o suporte técnico palavra-passe assistido repõe normalmente de conta para 20% de chamadas de suporte de uma organização de TI. 
* **Melhorar as experiências de utilizador final** e **reduzir o volume de suporte técnico** , concedendo aos utilizadores finais a capacidade para resolver problemas as suas próprias palavras-passe em simultâneo sem chamar um suporte técnico ou a abrir um pedido de suporte.
* **Unidade mobilidade** como os utilizadores podem repor as palavras-passe de onde quer que estão.
* **Manter o controlo** da política de segurança. Os administradores podem continuar para impor políticas que têm atualmente.

Se estiver pronto, pode começar com o Azure AD SSPR com o nosso [orientações de início rápido](active-directory-passwords-getting-started.md) sendo rapidamente os seus utilizadores repor as suas próprias palavras-passe.

## <a name="azure-ad-self-service-password-reset-availability"></a>Disponibilidade da reposição do Azure AD self-service palavra-passe

Azure AD self-service palavra-passe reposição está disponível em três camadas, dependendo da sua subscrição.

* **Azure AD livres** – os administradores só de nuvem podem repor as suas próprias palavras-passe.
* **Azure AD Basic** ou qualquer **paga subscrição do Office 365** – os utilizadores de apenas na nuvem podem repor as respetivas palavras-passe.
* **O Azure AD Premium** – qualquer utilizador ou administrador, incluindo apenas na nuvem, federado ou palavra-passe sincronizar os utilizadores, pode repor as respetivas palavras-passe. Palavras-passe no local requerem a repetição de escrita de palavras-passe para ser ativada.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Do Azure AD preços, SLA, atualizações e informações gerais

Podem encontrar mais detalhes sobre o licenciamento, preço e planos futuros em locais que se seguem.

* [**Detalhes dos preços do Azure AD**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Preços do Office 365**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Contratos de nível de serviço do Azure**](https://azure.microsoft.com/support/legal/sla/)
* [**Contrato de nível de serviço para o Microsoft Online Services**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Atualizações do Azure**](https://azure.microsoft.com/updates/)
* [**Plano do Azure**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Passos seguintes

* Está pronto para começar a utilizar SSPR? [Configuração do Azure AD Self-repor a palavra-passe serviço](active-directory-passwords-getting-started.md).
* Planear uma implementação de SSPR com êxito para os seus utilizadores com a documentação de orientação encontrada no nosso [ **guia de implementação**](active-directory-passwords-best-practices.md).
* [Repor ou alterar a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [O registo para a reposição de palavra-passe self-service](active-directory-passwords-reset-register.md).