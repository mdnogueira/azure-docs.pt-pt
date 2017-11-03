---
title: "Ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD | Microsoft Docs"
description: "Abrange as noções básicas sobre como integrar um servidor do SharePoint no local com o Proxy de aplicações do Azure AD."
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
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 2b4ad3e7bda1346e606b2c185c204154b8f19f87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD

Este artigo descreve como integrar um servidor do SharePoint no local com o Proxy de aplicações do Azure Active Directory (Azure AD).

Para ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD, siga as secções neste artigo passo a passo.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já tenha SharePoint 2013 ou mais recente no seu ambiente. Além disso, considere os seguintes pré-requisitos:

* SharePoint inclui suporte nativo do Kerberos. Por conseguinte, os utilizadores que estão a aceder a sites internos remotamente através do Proxy de aplicações do Azure AD podem assumir a ter uma experiência de início de sessão único (SSO).

* Este cenário inclui a alterações de configuração para o servidor do SharePoint. Recomendamos que utilize um ambiente de teste. Desta forma, pode efetuar atualizações ao seu servidor de teste pela primeira vez e, em seguida, facilitar um ciclo de teste antes de avançar para a produção.

* É necessário SSL no URL publicado. Tem de ter SSL ativado no seu site interno para garantir que as ligações são enviadas/mapeada corretamente. Se ainda não configurou o SSL, consulte o artigo [configurar o SSL para o SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) para obter instruções. Além disso, certifique-se de que a máquina de conector confianças o certificado que emite. (O certificado não necessita de ser emitido publicamente.)

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>Passo 1: Configurar o início de sessão no SharePoint

Para aplicações no local que utilizam a autenticação do Windows, pode conseguir início de sessão único (SSO) com o protocolo de autenticação Kerberos e uma funcionalidade denominada delegação restringida de Kerberos (KCD). KCD, quando configurado, permite que o conector do Proxy de aplicação obter um token do Windows para um utilizador, mesmo que o utilizador ainda não iniciou sessão no Windows diretamente. Para saber mais sobre KCD, consulte o artigo [descrição geral de delegação restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Para configurar o KCD para um servidor do SharePoint, utilize os procedimentos das secções seguintes sequenciais:

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>Certifique-se de que o SharePoint está em execução sob uma conta de serviço

Em primeiro lugar, certifique-se de que o SharePoint está em execução sob uma conta de serviço definido - não local sistema, serviço local ou serviço de rede. Opte por fazê-lo para que pode anexar os nomes de principais de serviço (SPNs) para uma conta válida. Os SPNs são a forma como o protocolo Kerberos identifica diferentes serviços. E terá da conta mais tarde para configurar o KCD.

> [!NOTE]
Tem de ter um criado anteriormente conta do Azure AD para o serviço. Sugerimos que permitem uma alteração de palavra-passe automática. Para obter mais informações sobre o conjunto completo de passos e resolução de problemas, consulte [configurar alteração de palavra-passe automática no SharePoint 2013](https://technet.microsoft.com/library/ff724280.aspx).

Para garantir que os sites estão em execução sob uma conta de serviço definido, execute os seguintes passos:

1. Abra o **Administração Central do SharePoint 2013** site.
2. Aceda a **segurança** e selecione **configurar contas de serviço**.
3. Selecione **Web conjunto aplicacional - SharePoint - 80**. As opções poderão ser ligeiramente diferentes com base no nome do seu conjunto de web, ou se o conjunto de web utiliza SSL por predefinição.

  ![Opções para configurar uma conta de serviço](./media/application-proxy-remote-sharepoint/service-web-application.png)

4. Se **selecionar uma conta para este componente** campo está definido como **Serviço Local** ou **serviço de rede**, terá de criar uma conta. Se não tiver terminado e pode mover para a secção seguinte.
5. Selecione **Register conta gerida novos**. Depois de criar a sua conta, tem de definir **conjunto aplicacional da Web** antes de poder utilizar a conta.

### <a name="configure-sharepoint-for-kerberos"></a>Configurar o SharePoint para Kerberos

Utilize o KCD efetuar início de sessão único para o servidor do SharePoint.

Para configurar o site do SharePoint para a autenticação Kerberos:

1. Abra o **Administração Central do SharePoint 2013** site.
2. Aceda a **gestão de aplicações**, selecione **gerir aplicações web**e selecione o site SharePoint. Neste exemplo, é **SharePoint - 80**.

  ![Selecionar o site do SharePoint](./media/application-proxy-remote-sharepoint/manage-web-applications.png)

3. Clique em **fornecedores de autenticação** na barra de ferramentas.
4. No **fornecedores de autenticação** , clique em **predefinido zona** para ver as definições.
5. No **editar autenticação** diálogo caixa, desloque para baixo até verá **tipos de autenticação de afirmações**. Certifique-se de que ambos **ativar a autenticação do Windows** e **autenticação integrada do Windows** estão selecionadas.
6. Na caixa de lista pendente para o campo de autenticação integrada do Windows, certifique-se de que **negociar (Kerberos)** está selecionada.

  ![Editar a caixa de diálogo de autenticação](./media/application-proxy-remote-sharepoint/service-edit-authentication.png)

7. Na parte inferior do **editar autenticação** caixa de diálogo, clique em **guardar**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Defina um nome principal de serviço para a conta de serviço do SharePoint

Antes de configurar KCD, tem de identificar o serviço do SharePoint a ser executado como conta de serviço que configurou. Identificar o serviço, definindo um SPN. Para obter mais informações, consulte [nomes principais de serviço](https://technet.microsoft.com/library/cc961723.aspx).

O formato SPN é:

```
<service class>/<host>:<port>
```

O formato de SPN:

* _classe de serviço_ é um nome exclusivo para o serviço. Para o SharePoint, utilize **HTTP**.

* _anfitrião_ é o domínio completamente qualificado ou o nome NetBIOS do anfitrião que o serviço está em execução. Para um site do SharePoint, este texto poderá ter de ser o URL do site, dependendo da versão do IIS que estiver a utilizar.

* _porta_ é opcional.

Se o FQDN do servidor do SharePoint:

```
sharepoint.demo.o365identity.us
```

Em seguida, o SPN é:

```
HTTP/sharepoint.demo.o365identity.us demo
```

Também poderá ter de definir os SPNs para sites específicos no seu servidor. Para obter mais informações, consulte [a autenticação Kerberos configurar](https://technet.microsoft.com/library/cc263449(v=office.12).aspx). Preste especial atenção à secção "Criar nomes de principais de serviço para as suas aplicações Web utilizando a autenticação Kerberos".

A forma mais fácil para definir o SPN é a seguir os formatos SPN poderão já estar presentes para os sites. Copie os SPNs para registar contra a conta de serviço. Para efetuar este procedimento:

1. Navegar para o site com o SPN de outro computador.
 Quando o fizer, o conjunto de permissões de Kerberos relevante é colocado em cache no computador. Estes pedidos de suporte de contenham o SPN do site de destino que navegado para.

2. Pode solicitar o SPN para esse site utilizando uma ferramenta chamada [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html). Na janela de comandos que está a ser executado no contexto mesmo que o utilizador a quem acedeu a sites no browser, execute o seguinte comando:
```
Klist
```
Klist, em seguida, devolve o conjunto de SPN de destino. Neste exemplo, o valor realçado é o SPN necessários:

  ![Resultados de Klist de exemplo](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)

4. Agora que tem o SPN, certifique-se de que está configurado corretamente na conta de serviço que configurou para a aplicação web anteriormente. Execute o seguinte comando na linha de comandos como administrador do domínio:

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 Este comando define o SPN para a conta de serviço do SharePoint em execução como _demo\sp_svc_.

 Substitua _http/sharepoint.demo.o365identity.us_ com o SPN para o servidor e _demo\sp_svc_ com a conta de serviço no seu ambiente. O comando Setspn procura o SPN antes de adicioná-lo. Neste caso, poderá ver um **valor de SPN duplicado** erro. Se vir este erro, certifique-se de que o valor é associado com a conta de serviço.

Pode verificar se o SPN foi adicionado ao executar o comando Setspn com a opção -l. Para obter mais informações sobre este comando, consulte [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>Certifique-se de que o conector está definido como um delegado fidedigno para SharePoint

Configure o KCD para que o serviço de Proxy de aplicações do Azure AD pode delegar identidades de utilizador para o serviço do SharePoint. Configure KCD Ativando o conector do Proxy de aplicação obter permissões de Kerberos para os seus utilizadores que tenham sido autenticados no Azure AD. Em seguida, esse servidor transmite o contexto para a aplicação de destino ou o SharePoint neste caso.

Para configurar o KCD, repita os passos seguintes para cada máquina de conector:

1. Inicie sessão como administrador de domínio para um controlador de domínio e, em seguida, abra **computadores e utilizadores do Active Directory**.
2. Localize o computador que o conector está em execução. Neste exemplo, é o mesmo servidor do SharePoint.
3. Faça duplo clique o computador e, em seguida, clique o **delegação** separador.
4. Certifique-se de que as definições de delegação estão definidas com **confiar no computador para delegação para os serviços especificados apenas**. Em seguida, selecione **utilizar qualquer protocolo de autenticação**.

  ![Definições de delegação](./media/application-proxy-remote-sharepoint/delegation-box.png)

5. Clique em de **adicionar** botão, clique em **utilizadores ou computadores**e localize a conta de serviço.

  ![Adicionar o SPN da conta de serviço](./media/application-proxy-remote-sharepoint/users-computers.png)

6. Na lista de SPNs, selecione de que criou anteriormente para a conta de serviço.
7. Clique em **OK**. Clique em **OK** novamente para guardar as alterações.

## <a name="step-2-enable-remote-access-to-sharepoint"></a>Passo 2: Ativar o acesso remoto no SharePoint

Agora que ativou a SharePoint para Kerberos e KCD configurado, está pronto para publicar o farm do SharePoint para acesso remoto através do Proxy de aplicações do Azure AD.

1. Publica o site do SharePoint com as seguintes definições. Para obter instruções passo a passo, consulte [publicar aplicações através do Proxy de aplicações do Azure AD](application-proxy-publish-azure-portal.md). 
   - **URL interno**: o URL do site do SharePoint internamente, tal como **https://SharePoint/**. Neste exemplo, certifique-se de que utiliza **https**
   - **Método de pré-autenticação**: do Azure Active Directory
   - **Traduzir URL nos cabeçalhos**: não

   >[!TIP]
   >SharePoint utiliza o _cabeçalho de anfitrião_ valor para procurar o site. Também gera ligações baseadas neste valor. O efeito net está qualquer ligação de SharePoint gera um URL publicado corretamente está configurado para utilizar o URL externo. Definir o valor como **Sim** também permite que o conector reencaminhar o pedido para a aplicação de back-end. No entanto, definindo o valor como **não** significa que o conector não irá enviar o nome de anfitrião interno. Em vez disso, o conector envia o cabeçalho de anfitrião como o URL publicado para a aplicação de back-end.

   ![Publicar SharePoint como uma aplicação](./media/application-proxy-remote-sharepoint/publish-app.png)

2. Assim que a sua aplicação for publicada, configure as definições de início de sessão único com os seguintes passos:

   1. Na página de aplicação no portal, selecione **de sessão único-**.
   2. Para início de sessão no modo único, selecione **autenticação integrada do Windows**.
   3. Definir o SPN de aplicação interno para o valor que configurou anteriormente. Neste exemplo, que seria **http/sharepoint.demo.o365identity.us**.

   ![Configurar a autenticação integrada do Windows para SSO](./media/application-proxy-remote-sharepoint/configure-iwa.png)

3. Para concluir a configuração da sua aplicação, vá para o **utilizadores e grupos** secção e atribuir utilizadores a aceder a esta aplicação. 

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>Passo 3: Certifique-se de que o SharePoint conhece o URL externo

É o último passo para se certificar de que o SharePoint pode encontrar o site com base no URL externo, para que compõe ligações com base nesse URL externo. Fazê-lo ao configurar os mapeamentos de acesso alternativo para o site do SharePoint.

1. Abra o **Administração Central do SharePoint 2013** site.
2. Em **definições do sistema**, selecione **configurar mapeamentos de acesso alternativo**. Abre a caixa de mapeamentos de acesso alternativo.

  ![Caixa de mapeamentos de acesso alternativa](./media/application-proxy-remote-sharepoint/alternate-access1.png)

3. Na lista pendente junto a **a coleção de mapeamento de acesso alternativo**, selecione **alteração alternativo acesso mapeamento coleção**.
4. Selecione o seu site – por exemplo, **SharePoint - 80**.
5. Pode optar por adicionar o URL publicado como um URL interno ou um URL público. Este exemplo utiliza um URL público como extranet.
6. Clique em **editar pública URLs** no **Extranet** caminho e, em seguida, introduza o URL externo que foi criada quando publicado a aplicação. Por exemplo, introduza **https://sharepoint-iddemo.msappproxy.net**.

  ![Introduzir o caminho](./media/application-proxy-remote-sharepoint/alternate-access3.png)

7. Clique em **Guardar**.

Agora pode aceder ao site de SharePoint externamente através do Proxy de aplicações do Azure AD.

## <a name="next-steps"></a>Passos seguintes

- [Trabalhar com domínios personalizados no Proxy de aplicações do Azure AD](active-directory-application-proxy-custom-domains.md)
- [Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-understand-connectors.md)

