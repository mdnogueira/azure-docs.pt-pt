---
title: "Escolha uma solução de identidade híbrida do Azure | Microsoft Docs"
description: "Obter uma compreensão básica sobre as soluções de identidade híbrida disponíveis e as recomendações para que possa tomar a decisão de governação de identidade melhor para a sua organização."
keywords: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/5/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: 5838e3276765f4f074bca2e3cae81b17edfa7c69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-hybrid-identity-solutions"></a>Soluções de identidade híbrida da Microsoft
[Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) soluções de identidade híbrida permitem-lhe sincronizar objetos de diretório no local com o Azure AD enquanto ainda gerir os utilizadores no local. A primeira decisão a tomar ao planear a sincronizar o seu no Windows Server Active Directory local com o Azure AD é se pretende utilizar sincronizada identidade ou identidade federada. Identidades sincronizadas e, opcionalmente hashes de palavra-passe, permitem aos utilizadores utilizar a mesma palavra-passe para aceder a recursos organizacionais baseados em nuvem e no local. Para requisitos de cenário mais avançados, tais como single-sign-on (SSO) ou a MFA no local, terá de implementar os serviços de Federação do Active Directory (AD FS) para federar identidades. 

Existem várias opções disponíveis para a configuração de identidade híbrida. Este artigo fornece informações para ajudar a escolher a melhor mais para a sua organização com base em facilidade de implementação e tem do gestão de identidades e acessos específico. Tenha em conta o modelo de identidade que melhor se adeque às necessidades da sua organização, terá também de pensar em tempo, a infraestrutura existente, complexidade e custo. Estes fatores são diferentes para cada organização e podem ser alterados ao longo do tempo. No entanto, se alterar os seus requisitos, também tem a flexibilidade para mudar para um modelo de identidade diferentes.

> [!TIP]
> Estas soluções são entregues por [do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="synchronized-identity"></a>Identidade sincronizada 
Identidades sincronizadas é a forma mais simples para sincronizar objetos de diretório no local (utilizadores e grupos) com o Azure AD. 

![Identidade híbrida sincronizados](./media/choose-hybrid-identity-solution/synchronized-identity.png)

Enquanto identidade sincronizada é o método mais rápido e mais fácil, os utilizadores têm de manter uma palavra-passe separada para os recursos baseados na nuvem. Para evitar isto, pode também (opcionalmente) [sincronizar um hash de palavras-passe do utilizador](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization) ao seu diretório do Azure AD. Sincronização de hashes de palavra-passe permite que os utilizadores iniciem sessão nos recursos organizacionais baseados em nuvem com o mesmo nome de utilizador e palavra-passe que utilizam no local. O Azure AD Connect verifica periodicamente o diretório no local para as alterações e mantém o diretório do Azure AD sincronizado. Quando um atributo de utilizador ou palavra-passe é alterado no Active Directory local, é automaticamente atualizado no Azure AD. 

Na maioria das organizações que apenas necessitam para permitir que os utilizadores iniciar sessão no Office 365, aplicações SaaS e outros recursos do Azure baseada no AD, recomenda-se a opção de sincronização de palavra-passe predefinida. Se o problema persistir para si, terá de decidir entre a autenticação pass-through e o AD FS.

> [!TIP]
> As palavras-passe do utilizador são armazenadas no local no Windows Server Active Directory sob a forma de um valor de hash, que representa a palavra-passe de utilizador reais. Um valor de hash é um resultado de uma função matemática unidirecional (o algoritmo hash). Não há nenhum método para reverter o resultado de uma função unidirecional para a versão de texto simples de uma palavra-passe. Não é possível utilizar um hash de palavra-passe para iniciar sessão sua rede no local. Quando optar por sincronizar as palavras-passe, o Azure AD Connect extrai os hashes de palavra-passe do Active Directory no local e aplica-se a segurança adicional para o hash de palavra-passe de processamento antes que está sincronizado com o Azure AD. Sincronização de palavra-passe também pode ser utilizada em conjunto com a repetição de escrita de palavras-passe para permitir self-service reposição palavra-passe no Azure AD. Além disso, pode ativar início de sessão único (SSO) para os utilizadores em computadores associados a um domínio que estejam ligados à rede empresarial. Com início de sessão, os utilizadores ativados só tem de introduzir um nome de utilizador para aceder de forma segura a recursos na nuvem. 

## <a name="pass-through-authentication"></a>Autenticação pass-through
[Autenticação pass-through do Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) fornece uma solução de validação da palavra-passe simples para serviços do Azure baseada no AD utilizando o Active Directory no local. Se as políticas de segurança e conformidade para a sua organização não permitir o envio de palavras-passe dos utilizadores mesmo num formulário com hash e só tem de suportar ambientes de trabalho SSO para dispositivos associados a um domínio, recomenda-se que avalie a utilizar autenticação pass-through. A autenticação pass-through não requer qualquer implementação numa rede de Perímetro, o que simplifica a infraestrutura de implementação quando comparado com o AD FS. Quando os utilizadores iniciam sessão com o Azure AD, este método de autenticação valida palavras-passe dos utilizadores diretamente no seu Active Directory no local.

![Autenticação pass-through](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

Com a autenticação pass-through, não é necessário para uma infraestrutura de rede complexa e não precisa de armazenar palavras-passe no local na nuvem. Combinado com início de sessão único, autenticação pass-through fornece uma experiência integrada verdadeiramente quando iniciar sessão no Azure AD ou outros serviços em nuvem.

A autenticação pass-through está configurada com o Azure AD Connect, que utiliza um agente de simples no local que escuta pedidos de validação da palavra-passe. O agente pode ser facilmente implementado em várias máquinas para fornecer elevada disponibilidade e o balanceamento de carga. Uma vez que todas as comunicações de saída apenas, não é necessário para o conector ser instalada numa rede de Perímetro. Os requisitos de computador do servidor para o conector são os seguintes:

- Windows Server 2012 R2 ou superior
- Associado a um domínio na floresta através do qual os utilizadores são validados

> [!NOTE]
> Autenticação pass-through do Azure AD está atualmente em pré-visualização e é suportada para clientes de baseada no browser da web e os clientes do Office que suportam a autenticação moderna. Para clientes que não são suportados, tais como clientes legados do Office e do Exchange ActiveSync (incluindo clientes de e-mail nativo em dispositivos móveis), é recomendado que utilize a autenticação moderna equivalente. A autenticação moderna não só permite a autenticação pass-through, mas também permite a políticas de acesso condicional ser aplicado, tais como a autenticação multifator. 

A autenticação pass-through não é atualmente suportada quando utilizam dispositivos Windows 10 associados para o Azure AD. No entanto, pode utilizar a sincronização de hash de palavra-passe como uma reversão automática para o suporte do Windows 10 e os clientes legados mencionado anteriormente. Durante a pré-visualização, sincronização de hash de palavra-passe está ativada por predefinição, quando a autenticação pass-through é selecionada como a opção de início de sessão no Azure AD Connect.


## <a name="federated-identity-ad-fs"></a>Identidade federada (AD FS)
Para obter mais controlo sobre como os utilizadores acedem do Office 365 e outros serviços em nuvem, pode configurar a sincronização de diretórios com-início de sessão único (SSO) utilizando [serviços de Federação do Active Directory (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server-2016). Federação inícios do utilizador de sessão com o AD FS delega autenticação para um servidor no local que valida as credenciais do utilizador. Neste modelo, as credenciais do Active Directory no local nunca são transmitidas para o Azure AD.

![Identidade federada](./media/choose-hybrid-identity-solution/federated-identity.png)

Também denominado de Federação de identidade, este método de início de sessão garante que todos os autenticação de utilizador é controlado no local e permite aos administradores implementar mais rigorosas níveis de controlo de acesso. A Federação de identidade com o AD FS é a opção mais complicada e necessita de implementar servidores adicionais no seu ambiente no local. Federação de identidade também consolida para fornecer suporte de 24x7 para a sua infraestrutura do Active Directory e o AD FS. Este nível elevado de suporte é necessário porque se aceder ao seu Internet no local, controlador de domínio ou servidores do AD FS não estão disponíveis, os utilizadores não podem iniciar sessão para serviços em nuvem.

> [!TIP]
> Se optar por utilizar a Federação com serviços de Federação do Active Directory (AD FS), pode, opcionalmente, configurar sincronização de palavras-passe como uma cópia de segurança no caso de falha da sua infraestrutura do AD FS.


## <a name="common-scenarios-and-recommendations"></a>Recomendações e cenários comuns
Seguem-se alguns cenários comuns de híbrida identidades e acessos gestão com base em recomendações relativamente a qual híbrida opção de identidade (ou opções) podem ser apropriadas para cada.

|Necessário:|PWS e SSO<sup>1</sup>| PTA e SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Sincronização nova contas de utilizador, contacte e grupo criadas automaticamente no meu diretório Active Directory no local para a nuvem.|![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png) |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Configurar os meus inquilinos para cenários de híbridos do Office 365|![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png) |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Ativar os meus utilizadores iniciar sessão e aceder aos serviços em nuvem com a palavra-passe no local|![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png) |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Implementar o início de sessão utilizando as credenciais da empresa|![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png) |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Certifique-se de que nenhum hashes de palavra-passe são armazenados na nuvem| |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Permitir soluções de autenticação multifator no local| | |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Autenticação de smart card de suporte para os meus utilizadores<sup>4</sup>| | |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|
|Apresentar notificações de expiração de palavra-passe no Portal do Office e no ambiente de trabalho Windows 10| | |![Recomendado](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> sincronização de palavras-passe com o início de sessão único. 

> <sup>2</sup> autenticação pass-through e o início de sessão único. 

> <sup>3</sup> federado início de sessão único com o AD FS.

> <sup>4</sup> do AD FS pode ser integrado com a sua empresa PKI para permitir início de sessão através de certificados. Estes certificados podem ser implementados através de canais de aprovisionamento fidedignos, tais como certificados MDM ou GPO ou smart card (incluindo cartões PIV/CAC) ou Hello para empresas (confiança de certificados) de certificados de forma recuperável. Para obter mais informações sobre o suporte de autenticação de smart card, consulte [este blogue](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).


## <a name="next-steps"></a>Passos seguintes
[Saiba mais num ambiente do Azure prova de conceito](https://aka.ms/aad-poc)

[Instalar o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

[Monitorizar a sincronização de identidade híbrida](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)

