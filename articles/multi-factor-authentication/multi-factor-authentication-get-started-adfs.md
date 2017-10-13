---
title: "Verificação de dois passos e AD FS – Azure MFA | Microsoft Docs"
description: "Esta é a página do Multi-Factor Authentication do Azure que descreve como começar a utilizar o MFA do Azure e o AD FS."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 44fbba68-6cf9-46c1-a9df-736580b68ae3
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/25/2017
ms.author: kgremban
ms.openlocfilehash: 564e98a4b6b9bd8bf9b58f06cee0027bfdf84458
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Introdução ao Multi-Factor Authentication do Azure e aos Serviços de Federação do Active Directory
<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Se a sua organização tiver federado o seu Active Directory no local com o Azure Active Directory através do AD FS, existem duas opções para utilizar o Multi-Factor Authentication do Azure.

* Proteger recursos da nuvem com o Multi-Factor Authentication do Azure ou os Serviços de Federação do Active Directory
* Proteger recursos na nuvem e no local com o Servidor Multi-Factor Authentication do Azure

A tabela seguinte resume a experiência de verificação entre proteger recursos com o Multi-Factor Authentication do Azure e o AD FS

| Experiência de Verificação - Aplicações baseadas no browser | Experiência de Verificação - Aplicações não baseadas no browser |
|:--- |:--- |:--- |
| Proteger recursos do Azure AD com o Multi-Factor Authentication do Azure |<li>O primeiro passo de verificação é efetuado no local com o AD FS.</li> <li>O segundo passo é um método telefónico efetuado através da autenticação na nuvem.</li> |
| Proteger recursos do Azure AD com os Serviços de Federação do Active Directory |<li>O primeiro passo de verificação é efetuado no local com o AD FS.</li><li>O segundo passo é executado no local honrando a afirmação.</li> |

Advertências com palavras-passe de aplicação para utilizadores federados:

* As palavras-passe da aplicação são verificadas com a autenticação na nuvem, para que possam ignorar a federação. A federação apenas é utilizada ativamente ao definir uma palavra-passe de aplicação.
* As definições de Controlo de Acesso de Cliente no local não são honradas pelas palavras-passe de aplicações.
* Perde a capacidade de registos de autenticação no local para as palavras-passe de aplicações.
* A desativação/eliminação da conta pode demorar até três horas para a sincronização do diretório, atrasando a desativação/eliminação das palavras-passe das aplicações na identidade da nuvem.

Para obter informações sobre como configurar o Multi-Factor Authentication do Azure ou o Servidor Multi-Factor Authentication do Azure com o AD FS, veja os seguintes artigos:

* [Proteger recursos da nuvem com o Multi-Factor Authentication do Azure e o AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
* [Proteger recursos na nuvem e no local utilizando o Servidor Multi-Factor Authentication do Azure com o Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
* [Proteger recursos na nuvem e no local utilizando o Servidor Multi-Factor Authentication do Azure com o AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)
