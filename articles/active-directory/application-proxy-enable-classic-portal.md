---
title: "Ativar o Proxy de aplicações do Azure AD no portal clássico | Microsoft Docs"
description: "Ativar o Proxy da Aplicação no Portal Clássico do Azure e instalar os Conectores para o proxy reverso."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/02/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: ea97fdc8d146ed524a932018b572ceda0982738b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-application-proxy-in-the-classic-portal-and-download-connectors"></a>Ativar o Proxy da aplicação no portal clássico e transferir conectores
Este artigo explica como ativar a Proxy da Aplicação do Microsoft Azure AD para o diretório em nuvem no Azure AD.

Se não estiver familiarizado com o que o Proxy da Aplicação fazer por si, saiba mais sobre [Como fornecer acesso remoto seguro a aplicações no local](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Pré-requisitos do Proxy da Aplicação
Para poder ativar e utilizar os serviços do Proxy da Aplicação, terá de ter:

* Uma [subscrição Basic ou Premium do Microsoft Azure AD](active-directory-editions.md) e um diretório do Azure AD para o qual terá de ser um administrador global.
* Um servidor a executar o Windows Server 2012 R2 ou 2016, em que pode instalar o conector do Proxy da aplicação. O servidor envia pedidos para os serviços da Proxy da Aplicação na nuvem, e necessita de uma ligação HTTP ou HTTPS para as aplicações que está a publicar.
  * Para obter o início de sessão único nas aplicações publicadas, esta máquina deverá ser associada ao mesmo domínio do AD como as aplicações que está a publicar. Para informações, consulte [-início de sessão único com o Proxy da aplicação](active-directory-application-proxy-sso-using-kcd.md)
* Se a organização utilizar servidores proxy para estabelecer ligação à internet, leia [funcionam com existente no local servidores proxy](application-proxy-working-with-proxy-servers.md) para obter detalhes sobre como configurá-las.

## <a name="open-your-ports"></a>Abrir as portas

Para preparar o ambiente para o Proxy de aplicações do Azure AD, terá primeiro de ativar a comunicação para os centros de dados do Azure. Se estiver colocada uma firewall no caminho, certifique-se de que está aberta, para que o Conector possa fazer pedidos HTTPS (TCP) para a Proxy da Aplicação.

1. Abrir as portas seguintes para **saída** tráfego:

   | Número de porta | Como é utilizado |
   | --- | --- |
   | 80 | Transferência de revogação de certificados as CRLs () ao validar o certificado SSL |
   | 443 | Todas as comunicações de saída com o serviço de Proxy de aplicações |

   Se a firewall impuser tráfego de acordo com os utilizadores de origem, abra estas portas para o tráfego dos serviços do Windows que são executados com um Serviço de Rede.

   > [!IMPORTANT]
   > A tabela reflete os requisitos de porta para versões de conector 1.5.132.0 e mais recentes. Se ainda tiver uma versão mais antiga do conector, também terá de ativar as seguintes portas: 5671, 8080, 9090, 9091, 9350, 9352 e 10100 – 10120.
   >
   >Para obter informações sobre como atualizar os conectores para a versão mais recente, consulte [conetores da Proxy da aplicação Azure compreender AD](application-proxy-understand-connectors.md#automatic-updates).

2. Se a sua firewall ou proxy permite adicionar à lista branca DNS, pode ligações de lista branca msappproxy.net e servicebus.windows.net. Se não, terá de permitir o acesso à [intervalos de IP de DataCenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizadas por semana.

3. Utilize o [do Azure AD aplicação Proxy portas teste ferramenta do conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) para verificar que o conector pode contactar o serviço de Proxy de aplicações. No mínimo, certifique-se de que a região EUA Central e a região mais próxima têm todas as marcas de verificação verdes. Para além disso, as marcas de verificação verde mais significa maior resiliência.

## <a name="enable-application-proxy-in-azure-ad"></a>Ativar o Proxy da aplicação no Azure AD
1. Inicie sessão como administrador no [Portal Clássico do Azure](https://manage.windowsazure.com/).
2. Aceda ao Active Directory e selecione o diretório no qual pretende ativar o Proxy da Aplicação.

    ![Active Directory – ícone](./media/active-directory-application-proxy-enable/ad_icon.png)
3. Selecione **Configurar** na página do diretório e desloque o ecrã para baixo até **Proxy da Aplicação**.
4. Mude **Ativar Serviços do Proxy da Aplicação deste Diretório** para **Ativado**.

    ![Ativar o Proxy da Aplicação](./media/active-directory-application-proxy-enable/app_proxy_enable.png)
5. Selecione **Transferir agora**. O **do Azure AD aplicação transferir Conetor da Proxy** abre. Leia e aceite os termos de licenciamento e clique em **Transferir** para guardar o ficheiro (.exe) do Windows Installer do conetor.

## <a name="install-and-register-the-connector"></a>Instalar e registar o conector
1. Execute **AADApplicationProxyConnectorInstaller.exe** no servidor que preparou de acordo com os pré-requisitos.
2. Siga as instruções do assistente de instalação.
3. Durante a instalação, é solicitado que registe o conector com o Proxy da aplicação do inquilino do Azure AD.

   * Indique as suas credenciais de administrador global do Azure AD. O inquilino do administrador global pode ser diferente das suas credenciais do Microsoft Azure.
   * Certifique-se o administrador que regista o conetor está no mesmo diretório onde ativou o serviço da Proxy da Aplicação. Por exemplo, se o domínio de inquilino for contoso.com, o administrador deve ser admin@contoso.com ou qualquer outro alias nesse domínio.
   * Se **configuração de segurança avançada do IE** está definido como **no** no servidor, o ecrã de registo poderá ser bloqueado. Para permitir o acesso, siga as instruções na mensagem de erro. Certifique-se de que a Segurança Avançada do Internet Explorer está desativada.
   * Se o registo do conetor falhar, veja [Resolver Problemas da Proxy da Aplicação](active-directory-application-proxy-troubleshoot.md).  
4. Quando a instalação estiver concluída, serão adicionados dois novos serviços ao servidor:

   * O **Conector do Proxy da Aplicação do Microsoft AAD** ativa a conectividade

     * **Atualizador de conector do Proxy de aplicações do Microsoft AAD** é um serviço de atualização automática. Periodicamente verifica a existência de novas versões do conector e atualiza o conector conforme necessário.

     ![Serviços do Conector do Proxy da Aplicação – captura de ecrã](./media/active-directory-application-proxy-enable/app_proxy_services.png)
5. Clique em **Concluir** na janela de instalação.

Para obter informações sobre conectores, consulte [Understand Azure AD Application Proxy connectors (Compreender os conectores do Proxy da Aplicação do Azure AD)](application-proxy-understand-connectors.md).

Para fins de elevada disponibilidade, deve implementar pelo menos dois conetores. Para implementar mais conetores, repita os passos 2 e 3. Cada conetor tem de ser registado separadamente.

Se pretender desinstalar o Conetor, desinstale o serviço Conetor e o serviço Atualizador. Reinicie o computador para remover completamente o serviço.

## <a name="next-steps"></a>Passos seguintes
Está agora pronto para [Publicar aplicações com o Proxy da Aplicação](active-directory-application-proxy-publish.md).

Se tiver aplicações que estão em redes separadas ou em diferentes localizações, pode utilizar grupos de conetor para organizar os diferentes conetores em unidades lógicas. Saiba mais sobre [Trabalhar com conetores da Proxy da Aplicação](active-directory-application-proxy-connectors.md).
