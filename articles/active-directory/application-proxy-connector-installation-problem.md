---
title: "Problema de instalar o conector de agente de Proxy de aplicações | Microsoft Docs"
description: "Como resolver problemas que poderá enfrentam ao instalar o conector de agente de Proxy da aplicação"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 91b3f6f3c8339647f568a509e9efd8e1fffb13dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Instalar o conector de agente de Proxy de aplicações do problema

Conector do Proxy de aplicações do Microsoft AAD é um componente de domínio interno que utiliza ligações de saída para estabelecer a conectividade do ponto de final nuvem disponível para o domínio interno.

## <a name="general-problem-areas-with-connector-installation"></a>Áreas de problema geral com a instalação do conector

Quando a instalação de um conector falhar, a causa raiz é normalmente uma das seguintes áreas:

1.  **Conectividade** – para concluir uma instalação com êxito, as novas necessidades de conector para registar e estabelecer propriedades de fidedignidade futuras. Isto é feito através da ligação ao serviço de nuvem de Proxy de aplicações do AAD.

2.  **Estabelecimento de confiança** – o novo conector cria um certificado autoassinado e regista no serviço em nuvem.

3.  **Autenticação do administrador do** – durante a instalação, o utilizador tem de fornecer credenciais de administrador para concluir a instalação do conector.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Verifique a conectividade para o serviço de Proxy de aplicações em nuvem e a página do Microsoft Login

**Objetivo:** Certifique-se de que a máquina do conector pode ligar para o ponto final do registo de Proxy de aplicações do AAD, bem como a página de início de sessão da Microsoft.

1.  Abra um browser e aceda à página web seguinte: <https://aadap-portcheck.connectorporttest.msappproxy.net> e certifique-se de que a conectividade a datacenters EUA Central e de EUA Leste com as portas 9090 e 9091 está a funcionar.

2.  Se qualquer um dessas portas não for bem sucedida (não tem uma marca de verificação verde), certifique-se de que tem o proxy de Firewall ou back-end \*. msappproxy.net com portas 9090 e 9091 definido corretamente.

3.  Abra um browser (separador separado) e aceda à página web seguinte: <https://login.microsoftonline.com>, certifique-se de que pode iniciar sessão nessa página.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Certifique-se de que os componentes de máquina e back-end suportem para o certificado de confiança do Proxy de aplicações

**Objetivo:** Certifique-se de que a máquina de conector, o proxy de back-end e a firewall podem suportar o certificado criado pelo conector do confiança futuras.

>[!NOTE]
>O conector tenta criar um certificado de SHA512 que é suportado pelo TLS1.2. Se a máquina ou a firewall back-end e proxy não suportar TLS1.2, a instalação falhar.
>
>

**Para resolver o problema:**

1.  Certifique-se de que a máquina suporta TLS1.2 – versões de todas as janelas após 2012 R2 devem suportar TLS 1.2. Se a máquina de conector está a partir de uma versão do 2012 R2 ou versões anteriores, certifique-se de que o KBs seguintes estão instalados na máquina: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Contacte o administrador de rede e peça verificar que o proxy de back-end e de firewall não bloqueia SHA512 para tráfego de saída.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Certifique-se de admin é utilizado para instalar o conector

**Objetivo:** Certifique-se de que o utilizador tenta instalar o conector é um administrador com as credenciais corretas. Atualmente, o utilizador tem de ser um administrador global para a instalação com êxito.

**Para verificar que as credenciais estão corretas:**

Ligar ao <https://login.microsoftonline.com> e utilizar as mesmas credenciais. Certifique-se o início de sessão com êxito. Pode verificar a função de utilizador acedendo a **do Azure Active Directory**  - &gt; **utilizadores e grupos**  - &gt; **todos os utilizadores**. 

Selecione a sua conta de utilizador, em seguida, "função de diretório" no menu resultante. Verifique se a função selecionada "Administrador Global". Se não for possível aceder a qualquer uma das páginas ao longo estes passos, não é um administrador global.

## <a name="next-steps"></a>Passos seguintes
[Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-understand-connectors.md)
