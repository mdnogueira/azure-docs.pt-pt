---
title: "Resolver problemas de Proxy de aplicações | Microsoft Docs"
description: "Aborda como resolver erros no Proxy de aplicações do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 970caafb-40b8-483c-bb46-c8b032a4fb74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 6534320d36653d296f254dfff129d4c5031f8ce8
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Resolver problemas de Proxy de aplicações e as mensagens de erro
Se ocorrerem erros ao aceder a uma aplicação publicada ou numa publicação de aplicações, verifique as seguintes opções para ver se o Proxy de aplicações do Microsoft Azure AD está a funcionar corretamente:

* Abra a consola de serviços do Windows e certifique-se de que o **conector do Proxy de aplicações do Microsoft AAD** serviço está ativado e em execução. Também poderá consultar a página de propriedades do serviço de Proxy de aplicações, conforme mostrado na imagem seguinte:  
  ![Captura de ecrã de janela de propriedades de conector do Proxy de aplicações do Microsoft AAD](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)
* Abra o Visualizador de eventos e procure eventos do conector de Proxy de aplicações no **registos de serviços e aplicações** > **Microsoft** > **AadApplicationProxy** > **conector** > **Admin**.
* Se for necessário, registos mais detalhados estão disponíveis por [ativar os registos de sessão do conector de Proxy de aplicações](application-proxy-understand-connectors.md#under-the-hood).

Para obter mais informações sobre a ferramenta de resolução de problemas de AD do Azure, consulte [ferramenta de resolução de problemas para validar a pré-requisitos de rede do conector](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/03/troubleshooting-tool-to-validate-connector-networking-prerequisites).

## <a name="the-page-is-not-rendered-correctly"></a>A página não é composta corretamente
Poderá ter problemas com a sua aplicação de composição ou está a funcionar incorretamente sem receber mensagens de erro específicas. Isto pode ocorrer se publicou o caminho do artigo, mas a aplicação requer o conteúdo que existe fora nesse caminho.

Por exemplo, se publicar o https://yourapp/app caminho, mas a aplicação chama imagens em https://yourapp/media, podem não ser compostos. Certifique-se de que pode publicar a aplicação utilizando o caminho de nível mais elevado, que terá de incluir todo o conteúdo relevante. Neste exemplo, seria possível http://yourapp/.

Se alterar o caminho para incluir conteúdo referenciado, mas ainda tem dos utilizadores apresentado numa ligação mais profunda no caminho, consulte a mensagem de blogue [definição na hiperligação adequada para aceder a aplicações de Proxy de aplicações no Azure AD painel e iniciador da aplicação do Office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Erros de conector

Utilize o [do Azure AD aplicação Proxy portas teste ferramenta do conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) para verificar que o conector pode contactar o serviço de Proxy de aplicações. No mínimo, certifique-se de que a região EUA Central e a região mais próxima têm todas as marcas de verificação verdes. Para além disso, as marcas de verificação verde mais significa maior resiliência. 

Se o registo falhar durante a instalação de assistente do conector, existem duas formas de ver o motivo da falha. O procure no registo de eventos em **aplicações e serviços Logs\Microsoft\AadApplicationProxy\Connector\Admin**, ou execute o seguinte comando do Windows PowerShell:

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

Depois de localizar o erro do conector do registo de eventos, utilize esta tabela dos erros comuns para resolver o problema:

| Erro | Passos recomendados |
| ----- | ----------------- |
| Falha no registo do conector: Certifique-se de que ativou o Proxy da aplicação no Portal de gestão do Azure e se introduziu o nome de utilizador do Active Directory e a palavra-passe corretamente. Erro: 'ocorreram um ou mais erros.' | Se fechar a janela de registo sem início de sessão para o Azure AD, execute novamente o Assistente do conector e registar o conector. <br><br> Se a janela de registo abre e, em seguida, fecha imediatamente sem que permite iniciar sessão, provavelmente, irá obter este erro. Este erro ocorre quando existe um erro de rede no seu sistema. Certifique-se de que é possível ligar a partir de um browser para um Web site público e que as portas são abertas conforme especificado no [pré-requisitos do Proxy de aplicações](active-directory-application-proxy-enable.md). |
| Limpar erro é apresentado na janela de registo. Não é possível continuar | Se vir este erro e, em seguida, fecha a janela, introduzir o nome de utilizador errado ou a palavra-passe. Tente novamente. |
| Falha no registo do conector: Certifique-se de que ativou o Proxy da aplicação no Portal de gestão do Azure e se introduziu o nome de utilizador do Active Directory e a palavra-passe corretamente. Erro: ' AADSTS50059: não existem informações de identificação de inquilino encontrado no pedido ou implícito por qualquer fornecido as credenciais e pesquisa por serviço principal URI falhou. | Está a tentar iniciar sessão com uma Account Microsoft e não um domínio que faz parte do ID de organização do diretório que está a tentar aceder. Certifique-se de que o administrador faz parte do mesmo nome de domínio como o domínio de inquilino, por exemplo, se o domínio do Azure AD for contoso.com, o administrador deve ser admin@contoso.com. |
| Falha ao obter a política de execução atual para executar scripts do PowerShell. | Se a instalação do conector falhar, certifique-se certifique-se de que a política de execução do PowerShell não está desativada. <br><br>1. Abra o Editor de políticas de grupo.<br>2. Aceda a **configuração do computador** > **modelos administrativos** > **componentes do Windows** > **do Windows PowerShell** e faça duplo clique **ativar a execução do Script**.<br>3. A política de execução pode ser definida para o **não configurado** ou **ativado**. Se definido como **ativado**, certifique-se de que em Opções, a política de execução está definida como **permitir scripts locais e remotos scripts assinados** ou **permitir todos os scripts**. |
| Não foi possível transferir a configuração do conector. | Certificado de cliente o conector, que é utilizado para autenticação, expirou. Isto pode também ocorrer se tiver o conector instalado atrás de um proxy. Neste caso, o conector não é possível aceder à Internet e não será capaz de fornecer aplicações para os utilizadores remotos. Renovar a confiança manualmente utilizando o `Register-AppProxyConnector` cmdlet no Windows PowerShell. Se o seu conector estiver atrás de um proxy, é necessário conceder acesso à Internet para as contas de conector "serviços de rede" e "sistema local." Isto pode ser conseguido concedendo-lhes acesso ao Proxy ou definindo-los para ignorar o proxy. |
| Falha no registo do conector: Certifique-se de que um Administrador Global do seu Active Directory para registar o conector. Erro: "o pedido de registo foi negado." | O alias que está a tentar iniciar sessão com não é administrador neste domínio. O conector é sempre instalado para o diretório que detém o domínio do utilizador. Certifique-se de que está a tentar iniciar sessão com a conta de administrador tem as permissões global para o inquilino do Azure AD. |

## <a name="kerberos-errors"></a>Erros de Kerberos

Esta tabela aborda os erros mais comuns que vêm da configuração de Kerberos e a configuração e inclui sugestões para resolução.

| Erro | Passos recomendados |
| ----- | ----------------- |
| Falha ao obter a política de execução atual para executar scripts do PowerShell. | Se a instalação do conector falhar, certifique-se certifique-se de que a política de execução do PowerShell não está desativada.<br><br>1. Abra o Editor de políticas de grupo.<br>2. Aceda a **configuração do computador** > **modelos administrativos** > **componentes do Windows** > **do Windows PowerShell** e faça duplo clique **ativar a execução do Script**.<br>3. A política de execução pode ser definida para o **não configurado** ou **ativado**. Se definido como **ativado**, certifique-se de que em Opções, a política de execução está definida como **permitir scripts locais e remotos scripts assinados** ou **permitir todos os scripts**. |
| 12008 - as do azure AD foi excedido o número máximo de tentativas de autenticação do Kerberos permitidos para o servidor de back-end. | Este erro pode indicar uma configuração incorreta entre o Azure AD e o servidor de aplicação de back-end, ou um problema na configuração de data e hora em ambas as máquinas. O servidor de back-end recusado a permissão de Kerberos criada pelo Azure AD. Certifique-se de que o Azure AD e o servidor de aplicação de back-end estão configuradas corretamente. Certifique-se de que a configuração de data e o Azure AD e o servidor de aplicação de back-end estão sincronizadas. |
| 13016 - as do azure AD não é possível obter uma permissão de Kerberos em nome do utilizador, porque não há nenhum UPN no token de limite ou o cookie de acesso. | Não há um problema com a configuração de STS. Corrija a configuração de afirmação UPN STS. |
| 13019 - as do azure AD não é possível obter uma permissão de Kerberos em nome do utilizador devido ao seguinte erro geral de API. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor de controlador de domínio ou um problema na configuração de data e hora em ambas as máquinas. O controlador de domínio recusado a permissão de Kerberos criada pelo Azure AD. Certifique-se de que o Azure AD e o servidor de aplicação de back-end estão corretamente configurados, especialmente a configuração de SPN. Certifique-se de que o Azure AD domínio associado ao mesmo domínio que o controlador de domínio para se certificar de que o controlador de domínio estabelece a fidedignidade com o Azure AD. Certifique-se de que a configuração de data e o Azure AD e o controlador de domínio estão sincronizadas. |
| 13020 - as do azure AD não é possível obter uma permissão de Kerberos em nome do utilizador, porque o SPN do servidor de back-end não está definida. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor de controlador de domínio ou um problema na configuração de data e hora em ambas as máquinas. O controlador de domínio recusado a permissão de Kerberos criada pelo Azure AD. Certifique-se de que o Azure AD e o servidor de aplicação de back-end estão corretamente configurados, especialmente a configuração de SPN. Certifique-se de que o Azure AD domínio associado ao mesmo domínio que o controlador de domínio para se certificar de que o controlador de domínio estabelece a fidedignidade com o Azure AD. Certifique-se de que a configuração de data e o Azure AD e o controlador de domínio estão sincronizadas. |
| 13022 - as do azure AD não consegue autenticar o utilizador porque o servidor de back-end responde a tentativas de autenticação Kerberos com um erro de HTTP 401. | Este evento pode indicar uma configuração incorreta entre o Azure AD e o servidor de aplicação de back-end, ou um problema na configuração de data e hora em ambas as máquinas. O servidor de back-end recusado a permissão de Kerberos criada pelo Azure AD. Certifique-se de que o Azure AD e o servidor de aplicação de back-end estão configuradas corretamente. Certifique-se de que a configuração de data e o Azure AD e o servidor de aplicação de back-end estão sincronizadas. |

## <a name="end-user-errors"></a>Erros de utilizador final

Esta lista inclui os erros que os utilizadores finais podem surgir quando tentarem aceder à aplicação e falhar. 

| Erro | Passos recomendados |
| ----- | ----------------- |
| O Web site não é possível apresentar a página. | O utilizador pode obter este erro quando tentar aceder à aplicação publicada se a aplicação é uma aplicação IWA. O SPN definido para esta aplicação pode estar incorreto. Para aplicações IWA, certifique-se de que o SPN configurado para esta aplicação está correto. |
| O Web site não é possível apresentar a página. | O utilizador pode obter este erro quando tentar aceder à aplicação publicada se a aplicação é uma aplicação do OWA. Isto pode ser causado por um dos seguintes:<br><li>O SPN definido para esta aplicação está incorreto. Certifique-se de que o SPN configurado para esta aplicação está correto.</li><li>O utilizador tentou aceder à aplicação está a utilizar uma conta Microsoft em vez da conta empresarial correta para iniciar sessão ou o utilizador é um utilizador convidado. Certifique-se de que o utilizador inicia sessão utilizando a respetiva conta empresarial, que corresponde ao domínio da aplicação publicada. Os utilizadores da Microsoft Account e de convidado não é possível aceder a aplicações de IWA.</li><li>O utilizador tentou aceder à aplicação não está corretamente definido para esta aplicação no lado no local. Certifique-se de que este utilizador tem as permissões adequadas, tal como definido para esta aplicação de back-end na máquina no local. |
| Não é possível aceder esta aplicação empresarial. Não está autorizado a aceder a esta aplicação. Falha de autorização. Certifique-se atribuir o utilizador com acesso a esta aplicação. | Os utilizadores poderão obter este erro quando tentar aceder à aplicação publicada se utilizarem contas Microsoft em vez da respetiva conta empresarial para iniciar sessão. Os utilizadores convidados também poderão obter este erro. Os utilizadores da Microsoft Account e nos convidados não podem aceder a aplicações IWA. Certifique-se de que o utilizador inicia sessão utilizando a respetiva conta empresarial, que corresponde ao domínio da aplicação publicada.<br><br>Poderá não ter atribuída ao utilizador para esta aplicação. Vá para o **aplicação** separador e, em **utilizadores e grupos**, atribua este utilizador ou grupo de utilizadores para esta aplicação. |
| Esta aplicação empresarial não pode ser acedida neste momento. Tente novamente mais tarde... O conector foi excedido. | Os utilizadores poderão obter este erro quando tentar aceder à aplicação publicada se não estão corretamente definidos para esta aplicação no lado no local. Certifique-se de que os utilizadores têm as permissões adequadas, conforme definido para esta aplicação de back-end na máquina no local. |
| Não é possível aceder esta aplicação empresarial. Não está autorizado a aceder a esta aplicação. Falha de autorização. Certifique-se de que o utilizador tem uma licença para o Azure Active Directory Premium ou Basic. | Os utilizadores poderão obter este erro quando tentar aceder à aplicação publicada se não foram atribuídos explicitamente com uma licença de Premium/básica pelo administrador do subscritor. Vá para Active Directory o subscritor **licenças** separador e certifique-se de que este utilizador ou grupo de utilizadores é atribuído uma licença Premium ou Basic. |

## <a name="my-error-wasnt-listed-here"></a>A minha erro não listado aqui

Se ocorrer um erro ou problema com o Proxy da aplicação AD do Azure que não está listado neste guia de resolução de problemas, gostaríamos de ouvir acerca do mesmo. Enviar um e-mail ao nosso [equipa comentários](mailto:aadapfeedback@microsoft.com) com os detalhes do erro que encontrou.

## <a name="see-also"></a>Consultar também
* [Ativar o Proxy de aplicações do Azure Active Directory](active-directory-application-proxy-enable.md)
* [Publicar aplicações com o Proxy de aplicações](active-directory-application-proxy-publish.md)
* [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
* [Ativar o acesso condicional](application-proxy-enable-remote-access-sharepoint.md)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
