---
title: "Azure Active Directory híbrida identidade considerações de design - determinar os requisitos de gestão de conteúdo | Microsoft Docs"
description: "Fornece informações sobre como determinar os requisitos de gestão de conteúdos do seu negócio. Normalmente, quando um utilizador tiver a sua própria dispositivo ele pode ter também várias credenciais que irão ser alternados, de acordo com a aplicação que utiliza. É importante diferenciar o conteúdo que foi criado utilizando credenciais pessoais versus aqueles criada utilizando as credenciais da empresa. A solução de identidade deve ser capaz de interagir com cloud services para fornecer uma experiência totalmente integrada para o utilizador final ao garantir a privacidade e aumentar a proteção contra fugas de dados."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 840de1e1fcba74285788d51d8f544375f0affa77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de gestão de conteúdos para a sua solução de identidade híbrida
Compreender os requisitos de gestão de conteúdos para a sua empresa direto pode influenciar a sua decisão em que solução de identidade híbrida a utilizar. Com a proliferação de vários dispositivos e a capacidade dos utilizadores para colocar os seus próprios dispositivos ([BYOD](http://aka.ms/byodcg)), a empresa tem de proteger os seus próprios dados, mas também deve manter a privacidade do utilizador intacto. Normalmente, quando um utilizador tiver a sua própria dispositivo ele pode ter também várias credenciais que irão ser alternados, de acordo com a aplicação que utiliza. É importante diferenciar o conteúdo que foi criado utilizando credenciais pessoais versus aqueles criada utilizando as credenciais da empresa. A solução de identidade deve ser capaz de interagir com cloud services para fornecer uma experiência totalmente integrada para o utilizador final ao garantir a privacidade e aumentar a proteção contra fugas de dados. 

A solução de identidade irá ser aproveitada pelas diferentes controlos técnicos para fornecer gestão de conteúdo, conforme mostrado na imagem abaixo:

![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Controlos de segurança que irão tirar partido do sistema de gestão de identidade**

Em geral, os requisitos de gestão de conteúdo irão tirar partido do seu sistema de gestão de identidades nas seguintes áreas:

* Privacidade: identificar o utilizador que possui um recurso e aplicando os controlos adequados para manter a integridade.
* Classificação de dados: identifica o utilizador ou grupo e o nível de acesso a um objeto de acordo com a respetiva classificação. 
* Proteção de fuga de dados: responsáveis por proteger os dados para evitar fugas de controlos de segurança tem de interagir com o sistema de identidade para validar a identidade do utilizador. Isto também é importante para fins de registo de auditoria.

> [!NOTE]
> Leitura [classificação de dados de preparação de nuvem](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) para obter mais informações sobre as melhores práticas e diretrizes para classificação de dados.
> 
> 

Quando planear a sua solução de identidade híbrida Certifique-se de que as perguntas seguintes são respondidas, de acordo com requisitos da sua organização:

* A sua empresa tem controlos de segurança no local para impor a privacidade dos dados?
  * Se Sim, os controlos de segurança será possível integrar a solução de identidade híbrida que vai adotar?
* A sua empresa utiliza a classificação de dados?
  * Se Sim, é a atual solução capaz de integrar a solução de identidade híbrida que vai adotar?
* A sua empresa tem atualmente qualquer solução de fuga de dados? 
  * Se Sim, é a atual solução capaz de integrar a solução de identidade híbrida que vai adotar?
* A sua empresa precisa de auditar o acesso aos recursos?
  * Se Sim, que tipo de recursos?
  * Se Sim, o nível de informações é necessário?
  * Se Sim, em que o registo de auditoria tem de residir? No local ou na nuvem?
* A sua empresa precisa encriptar os e-mails que contêm dados confidenciais (números da segurança social, números de cartão de crédito, etc.)
* A sua empresa precisa de encriptar todos os documentos/conteúdo partilhado com parceiros de negócios externos?
* A sua empresa precisa de impor políticas empresariais em determinados tipos de mensagens de correio eletrónico (não responder a todos os, não reencaminhar)?

> [!NOTE]
> Certifique-se de que toma nota de cada resposta e que compreende os fundamentos. [Definir a estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) abordará as opções disponíveis e as vantagens/desvantagens de cada opção.  Ao responder a estas questões, vai selecionar que opções melhor se adapta às sua empresa precisa.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar os requisitos de controlo de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Veja Também
[Descrição geral das considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)

