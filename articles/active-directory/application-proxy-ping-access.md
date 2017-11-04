---
title: "Autenticação baseada no cabeçalho com PingAccess para Proxy de aplicações do Azure AD | Microsoft Docs"
description: "Publica aplicações com PingAccess e o Proxy de aplicação para suportar a autenticação baseada no cabeçalho."
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
ms.date: 10/11/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: f6e6bb39164f9b3dea206ebcf850ee98e2506dcf
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Autenticação baseada no cabeçalho para início de sessão com o Proxy de aplicações e PingAccess

Azure Active Directory para o Proxy de aplicações e PingAccess ter parcerias em conjunto para fornecer aos clientes do Azure Active Directory acesso ao mesmo mais aplicações. PingAccess expande o [ofertas de Proxy de aplicações existentes](active-directory-application-proxy-get-started.md) para incluir o acesso de início de sessão único para aplicações que utilizam os cabeçalhos de autenticação.

## <a name="what-is-pingaccess-for-azure-ad"></a>O que é PingAccess para o Azure AD?

PingAccess do Azure Active Directory é uma oferta de PingAccess permite-lhe conceder acesso de utilizadores e o início de sessão único para aplicações que utilizam os cabeçalhos de autenticação. Proxy de aplicação trata estas aplicações, como qualquer outro, utilizar o Azure AD para autenticar o acesso e, em seguida, transmitir o tráfego através do serviço de conector. PingAccess encontra-se à frente as aplicações e traduz o token de acesso do Azure AD para um cabeçalho para que a aplicação receba a autenticação no formato que pode lê-lo.

Os utilizadores não irão reparar que algo de forma diferente quando iniciam sessão para utilizar as suas aplicações empresariais. Pode ainda funcionam de qualquer lugar em qualquer dispositivo. 

Uma vez que os conectores de Proxy de aplicações direcionam o tráfego remoto para todas as aplicações, independentemente do respetivo tipo de autenticação, este irá continuar para o balanceamento de carga automaticamente, bem.

## <a name="how-do-i-get-access"></a>Como posso obter acesso?

Uma vez que este cenário é disponibilizado através de uma parceria entre o Azure Active Directory e PingAccess, terá de licenças para ambos os serviços. No entanto, as subscrições do Azure Active Directory Premium incluem uma licença de PingAccess básica que abrange até 20 aplicações. Se precisar de publicar aplicações com base no cabeçalho de mais de 20, pode comprar uma licença adicional de PingAccess. 

Para obter mais informações, consulte [Edições do Azure Active Directory](active-directory-editions.md).

## <a name="publish-your-application-in-azure"></a>Publicar a aplicação no Azure

Este artigo destina-se das pessoas que estão a publicar uma aplicação com este cenário pela primeira vez. Explica de como começar com a aplicação e PingAccess, além dos passos de publicação. Se já tiver configurado a ambos os serviços, mas pretende um atualizador os passos de publicação, pode ignorar a instalação do conector e mover para [adicionar a sua aplicação para o Azure AD com o Proxy da aplicação](#add-your-app-to-Azure-AD-with-Application-Proxy).

>[!NOTE]
>Uma vez que este cenário é uma parceria entre o Azure AD e PingAccess, algumas das instruções existem no site de identidade de Ping.

### <a name="install-an-application-proxy-connector"></a>Instalar um conector do Proxy de aplicações

Se já tiver ativado o Proxy de aplicações e tem um conector instalado, pode ignorar esta secção e mover para [adicionar a sua aplicação para o Azure AD com o Proxy da aplicação](#add-your-app-to-azure-ad-with-application-proxy).

O conector do Proxy de aplicações é um serviço do Windows Server que direciona o tráfego do que os empregados remotos para as aplicações publicadas. Para obter mais instruções de instalação, consulte [ativar o Proxy da aplicação no portal do Azure](active-directory-application-proxy-enable.md).

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) como um administrador global.
2. Selecione **do Azure Active Directory** > **proxy de aplicações**.
3. Selecione **transferir o conector** para iniciar a transferência de conector do Proxy de aplicações. Siga as instruções de instalação.

   ![Ativar o Proxy da aplicação e transferir o conector](./media/application-proxy-ping-access/install-connector.png)

4. Transferir o conector automaticamente deve ativar o Proxy de aplicações para o seu diretório, mas se não pode selecionar **ativar Proxy de aplicações**.


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Adicionar a sua aplicação para o Azure AD com o Proxy da aplicação

Existem duas ações que precisa de tomar no portal do Azure. Em primeiro lugar, tem de publicar a aplicação com o Proxy da aplicação. Em seguida, terá de recolher algumas informações sobre a aplicação que pode utilizar durante os passos de PingAccess.

Siga estes passos para publicar a aplicação. Para um mais detalhadas instruções dos passos 1-8, consulte [publicar aplicações através do Proxy de aplicações do Azure AD](application-proxy-publish-azure-portal.md).

1. Se não na última secção, inicie sessão no [portal do Azure](https://portal.azure.com) como um administrador global.
2. Selecione **do Azure Active Directory** > **aplicações empresariais**.
3. Selecione **adicionar** na parte superior do painel.
4. Selecione **no local aplicação**.
5. Preencha os campos obrigatórios, com informações sobre a nova aplicação. Utilize as seguintes orientações para as definições:
   - **URL interno**: normalmente de fornecer o URL que o leva à página de início de sessão da aplicação quando tiver na rede empresarial. Para este cenário, o conector tem de tratar o proxy de PingAccess como a página da aplicação. Utilize este formato: `https://<host name of your PA server>:<port>`. A porta é 3000 por predefinição, mas pode configurá-la no PingAccess.
   - **Método de pré-autenticação**: Azure Active Directory
   - **Traduzir URL nos cabeçalhos**: não

   >[!NOTE]
   >Se esta for a sua primeira aplicação, utilize a porta 3000 para iniciar e voltar atrás para atualizar esta definição, se alterar a configuração de PingAccess. Se esta for a sua aplicação de segundo ou posterior, esta terá de corresponder ao serviço de escuta que configurou no PingAccess. Saiba mais sobre [os serviços de escuta no PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

6. Selecione **adicionar** na parte inferior do painel. A aplicação é adicionada e abre o menu de início rápido.
7. No menu de início rápido, selecione **atribuir um utilizador de teste**, e adicione, pelo menos, um utilizador para a aplicação. Certifique-se que esta conta de teste tem acesso à aplicação no local.
8. Selecione **atribuir** para guardar a atribuição de utilizador de teste.
9. No painel de gestão de aplicações, selecione **de sessão único-**.
10. Escolha **com base no cabeçalho de início de sessão** no menu pendente. Selecione **Guardar**.

   >[!TIP]
   >Se esta for a primeira vez utilizando com base no cabeçalho de início de sessão, terá de instalar PingAccess. Para garantir que a sua subscrição do Azure é automaticamente associada a sua instalação PingAccess, utilize a ligação nesta página de início de sessão único para transferir PingAccess. Pode abrir o site de transferir agora ou volte a esta página mais tarde. 

   ![Selecione com base no cabeçalho de início de sessão](./media/application-proxy-ping-access/sso-header.PNG)

11. Feche o painel de aplicações da empresa ou desloque-se até à esquerda para regressar ao menu do Azure Active Directory.
12. Selecione **registos de aplicação**.

   ![Selecione os registos de aplicação](./media/application-proxy-ping-access/app-registrations.png)

13. Selecione a aplicação que acabou de adicionar, em seguida, **URLs de resposta**.

   ![Selecione os URLs de resposta](./media/application-proxy-ping-access/reply-urls.png)

14. Verifique se o externo URL que atribuiu à sua aplicação no passo 5 se encontra na lista de URLs de resposta. Se não for, adicione-o agora.
15. No painel de definições de aplicação, selecione **as permissões necessárias**.

  ![Selecione as permissões necessárias](./media/application-proxy-ping-access/required-permissions.png)

16. Selecione **Adicionar**. Para a API, escolha **Windows Azure Active Directory**, em seguida, **selecione**. Para conhecer as permissões, escolha **leitura e escrita todas as aplicações** e **iniciar sessão e ler o perfil de utilizador**, em seguida, **selecione** e **feito**.  

  ![Selecione as permissões](./media/application-proxy-ping-access/select-permissions.png)

17. Conceder permissões antes de fechar o ecrã de permissões. 
![Conceder permissões](media/application-proxy-ping-access/grantperms.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>Recolher informações para obter os passos PingAccess

1. No painel de definições da aplicação, selecione **propriedades**. 

  ![Selecionar propriedades](./media/application-proxy-ping-access/properties.png)

2. Guardar o **Id da aplicação** valor. Isto é utilizado para o ID de cliente quando configurar PingAccess.
3. No painel de definições de aplicação, selecione **chaves**.

  ![Selecione as chaves](./media/application-proxy-ping-access/Keys.png)

4. Crie uma chave ao introduzir uma descrição de chave e escolher uma data de expiração no menu pendente.
5. Selecione **Guardar**. Um GUID que é apresentado no **valor** campo.

  Guarde agora, este valor como não conseguir vê-lo novamente depois de fechar esta janela.

  ![Criar uma nova chave](./media/application-proxy-ping-access/create-keys.png)

6. Feche o painel de registos de aplicação ou desloque-se até à esquerda para regressar ao menu do Azure Active Directory.
7. Selecione **propriedades**.
8. Guardar o **ID de diretório** GUID.

### <a name="optional---update-graphapi-to-send-custom-fields"></a>Opcional - GraphAPI de atualização para enviar os campos personalizados

Para obter uma lista de tokens de segurança do Azure AD envia para autenticação, consulte [referência de token do Azure AD](./develop/active-directory-token-and-claims.md). Se precisar de uma afirmação personalizada que envia outros tokens, utilize GraphAPI para definir o campo de aplicação *acceptMappedClaims* para **verdadeiro**. Só pode utilizar o Explorador do Azure AD Graph para fazer esta configuração. 

Este exemplo utiliza o Explorador do gráfico:

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```

>[!NOTE]
>Para utilizar uma afirmação personalizada, também tem de ter uma política personalizada definida e atribuída à aplicação.  Esta política deve incluir todos os atributos personalizados necessários.
>
>Definição de política e a atribuição podem ser feitos através do PowerShell, o Explorador do Azure AD Graph ou MS Graph.  Se está a fazer isto no PowerShell, poderá ter de utilizar o primeiro `New-AzureADPolicy `e, em seguida, atribua-o para a aplicação com `Set-AzureADServicePrincipalPolicy`.  Para obter mais informações, consulte o [documentação da política do Azure AD](active-directory-claims-mapping.md#claims-mapping-policy-assignment).

### <a name="optional---use-a-custom-claim"></a>Opcional - utilize uma afirmação personalizada
Para tornar a sua aplicação utilizar uma afirmação personalizada e incluir campos adicionais, não se esqueça de que tem também [criado uma política de mapeamento de afirmações personalizadas e atribuído-lo à aplicação](active-directory-claims-mapping.md#claims-mapping-policy-assignment).

## <a name="download-pingaccess-and-configure-your-app"></a>Transferir PingAccess e configure a sua aplicação

Agora que concluiu todos os passos de configuração do Azure Active Directory, pode mover-se para configurar PingAccess. 

Os passos detalhados para a parte PingAccess deste cenário continuar na documentação do Identity de Ping, [PingAccess configurar para o Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html).

Esses passos guiá-lo durante o processo de obtenção de uma conta de PingAccess se ainda não tiver um, instalar o servidor de PingAccess e criar uma ligação de fornecedor do Azure AD OIDC com o ID de diretório que copiou do portal do Azure. Em seguida, utilize os valores de ID de aplicação e a chave para criar uma sessão Web PingAccess. Depois disso, pode configurar o mapeamento de identidades e criar um anfitrião virtual, o site e a aplicação.

### <a name="test-your-app"></a>Testar a aplicação

Quando tiver terminado de todos estes passos, a aplicação deve ser configurado e em execução. Para testar, abra um browser e navegue para o URL externo que criou quando publicado a aplicação no Azure. Inicie sessão com a conta de teste que atribuídos à aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Configurar PingAccess para o Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Como Proxy de aplicações do Azure AD fornece o início de sessão único?](application-proxy-sso-overview.md)
- [Resolver problemas de Proxy de aplicações](active-directory-application-proxy-troubleshoot.md)
