---
title: "Começar a utilizar o Proxy de aplicações do Azure AD - instalar o conector | Microsoft Docs"
description: "Ativar o Proxy da aplicação no portal do Azure e instalar os conectores para o proxy reverso."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 3b0a3e315ecd98565a852b3a8190d78ccdefe42d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Introdução ao Proxy de aplicações e instalar o conector
Este artigo explica como ativar a Proxy da Aplicação do Microsoft Azure AD para o diretório em nuvem no Azure AD.

Se não estiver ainda em consideração as vantagens de segurança e a produtividade do Proxy de aplicações traz para a sua organização, saiba mais sobre [como fornecer acesso remoto seguro a aplicações no local](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Pré-requisitos do Proxy da Aplicação
Para poder ativar e utilizar os serviços do Proxy da Aplicação, terá de ter:

* Uma [subscrição Basic ou Premium do Microsoft Azure AD](active-directory-editions.md) e um diretório do Azure AD para o qual terá de ser um administrador global.
* Um servidor a executar o Windows Server 2012 R2 ou 2016, em que pode instalar o conector do Proxy da aplicação. O servidor tem de ser possível estabelecer ligação com os serviços do Proxy de aplicações na nuvem e as aplicações no local que está a publicar.
  * Para início de sessão para aplicações publicadas utilizando a delegação restrita de Kerberos, esta máquina deve ser associado a um domínio no mesmo domínio do AD como as aplicações que está a publicar. Para informações, consulte [KCD para o início de sessão único com o Proxy da aplicação](active-directory-application-proxy-sso-using-kcd.md).

Se a organização utilizar servidores proxy para estabelecer ligação à internet, leia [funcionam com existente no local servidores proxy](application-proxy-working-with-proxy-servers.md) para obter detalhes sobre como configurá-las antes de começar com o Proxy de aplicações.

## <a name="open-your-ports"></a>Abrir as portas

Para preparar o ambiente para o Proxy de aplicações do Azure AD, terá primeiro de ativar a comunicação para os centros de dados do Azure. Se estiver colocada uma firewall no caminho, certifique-se de que está aberta, para que o Conector possa fazer pedidos HTTPS (TCP) para a Proxy da Aplicação.

1. Abrir as portas seguintes para **saída** tráfego:

   | Número de porta | Como é utilizado |
   | --- | --- |
   | 80 | Transferência de revogação de certificados as CRLs () ao validar o certificado SSL |
   | 443 | Todas as comunicações de saída com o serviço de Proxy de aplicações |

   Se a firewall impuser tráfego de acordo com os utilizadores de origem, abra estas portas para o tráfego dos serviços do Windows que são executados como um serviço de rede.

   > [!IMPORTANT]
   > A tabela reflete os requisitos de porta para versões de conector 1.5.132.0 e mais recentes. Se ainda tiver uma versão mais antiga do conector, também terá de ativar as seguintes portas 80 e 443: 5671, 8080, além de 9090-9091 9350, 9352, 10100 – 10120.
   >
   >Para obter informações sobre como atualizar os conectores para a versão mais recente, consulte [conetores da Proxy da aplicação Azure compreender AD](application-proxy-understand-connectors.md#automatic-updates).

2. Se a sua firewall ou proxy permite adicionar à lista branca DNS, pode ligações de lista branca msappproxy.net e servicebus.windows.net. Se não, terá de permitir o acesso à [intervalos de IP de DataCenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizadas por semana.

3. A Microsoft utiliza quatro endereços para verificar os certificados. Permitir o acesso aos URLs seguintes se ainda não o fez, para outros produtos:
   * mscrl.microsoft.com:80
   * CRL.microsoft.com:80
   * OCSP.msocsp.com:80
   * www.microsoft.com:80

4. O conector precisa de acesso à login.windows.net e login.microsoftonline.com para o processo de registo.

5. Utilize o [do Azure AD aplicação Proxy portas teste ferramenta do conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) para verificar que o conector pode contactar o serviço de Proxy de aplicações. No mínimo, certifique-se de que a região EUA Central e a região mais próxima têm todas as marcas de verificação verdes. Para além disso, as marcas de verificação verde mais significa maior resiliência.

## <a name="install-and-register-a-connector"></a>Instalar e registar um conector
1. Inicie sessão como administrador no [portal do Azure](https://portal.azure.com/).
2. O diretório atual é apresentado em seu nome de utilizador no canto superior direito. Se precisar de mude de diretório, selecione esse ícone.
3. Aceda a **do Azure Active Directory** > **Proxy de aplicações**.

   ![Navegue para o Proxy de aplicações](./media/active-directory-application-proxy-enable/app_proxy_navigate.png)

4. Selecione **transferir conector**.

   ![Transferir o conector](./media/active-directory-application-proxy-enable/download_connector.png)

5. Execute **AADApplicationProxyConnectorInstaller.exe** no servidor que preparou de acordo com os pré-requisitos.
6. Siga as instruções do assistente de instalação. Durante a instalação, é solicitado que registe o conector com o Proxy da aplicação do inquilino do Azure AD.

   * Indique as suas credenciais de administrador global do Azure AD. O inquilino do administrador global pode ser diferente das suas credenciais do Microsoft Azure.
   * Certifique-se o administrador que regista o conetor está no mesmo diretório onde ativou o serviço da Proxy da Aplicação. Por exemplo, se o domínio de inquilino for contoso.com, o administrador deve ser admin@contoso.com ou qualquer outro alias nesse domínio.
   * Se **configuração de segurança avançada do IE** está definido como **no** no servidor onde está a instalar o conector, poderá não ver o ecrã de registo. Para obter acesso, siga as instruções na mensagem de erro. Certifique-se de que a Segurança Avançada do Internet Explorer está desativada.

Para fins de elevada disponibilidade, deve implementar pelo menos dois conetores. Cada conetor tem de ser registado separadamente.

## <a name="test-that-the-connector-installed-correctly"></a>Teste o conector instalado corretamente

Pode confirmar que um novo conector instalado corretamente verificando-la no portal do Azure ou no seu servidor. 

No portal do Azure, inicie sessão no seu inquilino e navegue para **do Azure Active Directory** > **Proxy de aplicações**. Todos os seus conectores e grupos de conetor aparecem nesta página. Seleccione um conector para ver os respetivos detalhes ou movê-la para um grupo de conector diferentes. 

No seu servidor, verifique a lista de serviços do Active Directory para o conector e do atualizador do conector. Os dois serviços devem iniciar a execução de imediato, mas se não estiver, ativá-los: 

   * O **Conector do Proxy da Aplicação do Microsoft AAD** ativa a conectividade

   * **Atualizador de conector do Proxy de aplicações do Microsoft AAD** é um serviço de atualização automática. O atualizador verifica a existência de novas versões do conector e atualiza o conector conforme necessário.

   ![Serviços do Conector do Proxy da Aplicação – captura de ecrã](./media/active-directory-application-proxy-enable/app_proxy_services.png)

Para obter informações sobre conectores e como continuar atualizados, consulte [conetores da Proxy da aplicação Azure compreender AD](application-proxy-understand-connectors.md).


## <a name="next-steps"></a>Passos seguintes
Está agora pronto para [Publicar aplicações com o Proxy da Aplicação](application-proxy-publish-azure-portal.md).

Se tiver aplicações que estão em redes separadas ou em diferentes localizações, utilize grupos de conetor para organizar os diferentes conetores em unidades lógicas. Saiba mais sobre [Trabalhar com conetores da Proxy da Aplicação](active-directory-application-proxy-connectors-azure-portal.md).
