---
title: "Publicar o ambiente de trabalho remoto com o Proxy de aplicações do Azure AD | Microsoft Docs"
description: "Abrange as noções básicas sobre conectores de Proxy de aplicações do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: kgremban
ms.custom: it-pro
ms.reviewer: harshja
ms.openlocfilehash: fa8f63c8da5019ed42ea8ec067d3d3d174976dd8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publicar o ambiente de trabalho remoto com o Proxy de aplicações do Azure AD

Serviço de ambiente de trabalho remoto e o Proxy de aplicações do Azure AD funcionam em conjunto para melhorar a produtividade dos funcionários habilitados longe da rede empresarial. 

O público-alvo para este artigo é:
- Clientes de Proxy de aplicações atuais que pretendem oferecer mais aplicações para os respetivos utilizadores finais através da publicação de aplicações no local através dos serviços de ambiente de trabalho remoto.
- Atuais dos serviços de ambiente de trabalho remoto que os clientes pretendem para reduzir a superfície de ataque da respetiva implementação através da utilização de Proxy de aplicações do Azure AD. Este cenário fornece um conjunto limitado de verificação em dois passos e controlos de acesso condicional para o RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Como o Proxy de aplicações enquadra na implementação de RDS padrão

Uma implementação de RDS padrão inclui vários serviços de função de ambiente de trabalho remoto em execução no Windows Server. Observar o [arquitetura dos serviços de ambiente de trabalho remoto](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture), existem várias opções de implementação. Ao contrário de outras opções de implementação de RDS de [implementação de RDS com o Proxy de aplicações do Azure AD](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (mostrado no diagrama seguinte) tem uma ligação permanente de saída do servidor a executar o serviço de conectores. Outras implementações deixam abrir ligações de entrada através de um balanceador de carga.

![Proxy de aplicações encontra-se entre a VM de RDS e a internet pública](./media/application-proxy-publish-remote-desktop/rds-with-app-proxy.png)

Numa implementação RDS, a função da Web de RD e a função de Gateway de RD executam em máquinas de acesso à Internet. Estes pontos finais são expostos pelas seguintes razões:
- Web de RD fornece ao utilizador um ponto de final público para iniciar sessão e ver as várias aplicações no local e ambientes de trabalho que podem aceder. Após selecionar um recurso, uma ligação de RDP é criada utilizando a aplicação nativa no sistema operativo.
- Gateway de RD entra imagem quando um utilizador inicia a ligação de RDP. O Gateway de RD processa tráfego RDP encriptado através da internet e converte-la para que o utilizador está a ligar ao servidor no local. Neste cenário, o tráfego que do Gateway de RD está a receber provém do Proxy de aplicações do Azure AD.

>[!TIP]
>Se ainda não implementado o RDS antes, ou se pretender obter mais informações antes de começar, saiba como [perfeitamente implementar RDS com o Azure Resource Manager e o Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Requisitos

- Pontos finais de Web de RD e Gateway de RD tem de estar localizados no mesmo computador e com uma raiz comuns. Web de RD e Gateway de RD são publicados como uma única aplicação com o Proxy da aplicação para que pode ter uma experiência único início de sessão entre as duas aplicações.

- Já deverá ter [implementado RDS](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure), e [ativado a Proxy da aplicação](active-directory-application-proxy-enable.md).

- Este cenário pressupõe que os utilizadores finais aceda através do Internet Explorer em ambientes de trabalho Windows 7 ou Windows 10 que se ligam através da página Web de RD. Se precisar de suportar outros sistemas operativos, consulte [suporte para outras configurações de cliente](#support-for-other-client-configurations).

  >[!NOTE]
  >Atualização do Windows 10 Creator não é atualmente suportada.

- No Internet Explorer, ative o suplemento do RDS ActiveX.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Implementar o cenário conjunta RDS e Proxy de aplicações

Depois de configurar RDS e Proxy de aplicações do Azure AD para o seu ambiente, siga os passos para combinar duas soluções. Estes passos guiá-publicar os dois web com acesso à RDS pontos finais (Web de RD e Gateway de RD) como aplicações e, em seguida, instruir o tráfego no seu RDS passar por Proxy de aplicações.

### <a name="publish-the-rd-host-endpoint"></a>Publicar o ponto final de anfitrião de RD

1. [Publicar uma nova aplicação de Proxy de aplicações](application-proxy-publish-azure-portal.md) com os seguintes valores:
   - URL interno: https://\<rdhost\>empresa>.com /, onde \<rdhost\> é a raiz comuns que partilham Web de RD e Gateway de RD.
   - URL externo: Este campo é preenchido automaticamente com base no nome da aplicação, mas pode modificá-la. Os utilizadores que serão enviadas para este URL quando estes acedem ao RDS.
   - Método de pré-autenticação: Azure Active Directory
   - Traduzir os cabeçalhos de URL: não
2. Atribua utilizadores para a aplicação publicada de RD. Certifique-se de que todas as tiverem acesso aos RDS, demasiado.
3. Deixe o método único início de sessão para a aplicação como **do Azure AD-início de sessão único desativada**. Os utilizadores são-lhe pedidos para autenticar uma vez para o Azure AD e outra para Web de RD, mas tem início de sessão único para o Gateway de RD.
4. Aceda a **do Azure Active Directory** > **registos de aplicação** > *a aplicação* > **definições**.
5. Selecione **propriedades** e atualizar o **URL da página inicial** campo para apontar para o ponto final de Web de RD (como https://\<rdhost\>empresa>.com/RDWeb).

### <a name="direct-rds-traffic-to-application-proxy"></a>Tráfego RDS direto para o Proxy de aplicações

Ligar para a implementação de RDS como administrador e altere o nome do servidor de Gateway de RD para a implementação. Esta configuração assegura que as ligações de percorrer o serviço de Proxy de aplicações do Azure AD.

1. Ligar ao servidor RDS a executar a função de Mediador de ligações de RD.
2. Iniciar **Gestor de servidor**.
3. Selecione **dos serviços de ambiente de trabalho remoto** a partir do painel à esquerda.
4. Selecione **descrição geral**.
5. Na secção Descrição geral da implementação, selecione o menu pendente e escolha **editar propriedades de implementação**.
6. No separador de Gateway de RD, altere o **nome do servidor** campo para o URL externo que definiu para o ponto de final de anfitrião de RD no Proxy de aplicações.
7. Alterar o **método de início de sessão** campo para **autenticação de palavra-passe**.

  ![Ecrã de propriedades de implementação no RDS](./media/application-proxy-publish-remote-desktop/rds-deployment-properties.png)

8. Execute este comando para cada coleção. Substitua  *\<yourcollectionname\>*  e  *\<proxyfrontendurl\>*  pelas suas informações. Este comando activa início de sessão único entre Web de RD e Gateway de RD e otimiza o desempenho:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Por exemplo:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://gateway.contoso.msappproxy.net/`nrequire pre-authentication:i:1"
   ```

9. Para verificar a modificação das propriedades RDP personalizadas, bem como ver os conteúdos do ficheiro RDP que serão transferidos da RDWeb para esta coleção, execute o seguinte comando:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Agora que configurou o ambiente de trabalho remoto, o Proxy de aplicações do Azure AD realizou como o componente de acesso à internet do RDS. Pode remover os outros para a internet pontos finais públicos no seu máquinas de Web de RD e Gateway de RD.

## <a name="test-the-scenario"></a>Testar o cenário

Teste o cenário com o Internet Explorer no computador 10 ou de um Windows 7.

1. Vá para o URL externo que configurou ou localizar a aplicação no [MyApps painel](https://myapps.microsoft.com).
2. É-lhe perguntado para autenticar para o Azure Active Directory. Utilize uma conta que atribuiu à aplicação.
3. É-lhe pedido para se autenticar Web de RD.
4. Depois da autenticação de RDS for bem sucedida, pode selecionar o ambiente de trabalho ou a aplicação que pretende e começar a trabalhar.

## <a name="support-for-other-client-configurations"></a>Suporte para outras configurações de cliente

A configuração descrita neste artigo é para os utilizadores no Windows 7 ou 10, com o Internet Explorer e o suplemento do RDS ActiveX. Se precisar de, no entanto, pode suportar outros sistemas operativos ou browsers. A diferença é o método de autenticação que utilizar.

| Método de autenticação | Configuração de cliente suportados |
| --------------------- | ------------------------------ |
| Pré-autenticação    | Windows 7/10 utilizando o Internet Explorer + suplemento ActiveX de RDS |
| Pass-through | Qualquer outro sistema operativo que suporte a aplicação de ambiente de trabalho remoto |

O fluxo de pré-autenticação oferece vantagens de segurança mais que o fluxo de pass-through. Com a pré-autenticação pode utilizar as funcionalidades de autenticação do Azure AD, como o início de sessão único, acesso condicional e verificação de dois passos para os seus recursos no local. É também assegurar que apenas autenticado atingir o tráfego da rede.

Para utilizar autenticação pass-through, existem apenas duas alterações para os passos apresentados neste artigo:
1. No [publicar o ponto final de anfitrião de RD](#publish-the-rd-host-endpoint) passo 1, definir o método de pré-autenticação **Passthrough**.
2. No [tráfego de RDS direta para o Proxy de aplicações](#direct-rds-traffic-to-application-proxy), ignore o passo 8 completamente.

## <a name="next-steps"></a>Passos seguintes

[Ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD](application-proxy-enable-remote-access-sharepoint.md)  
[Considerações de segurança para aceder remotamente a aplicações ao utilizar o Proxy de aplicações do Azure AD](application-proxy-security-considerations.md)
