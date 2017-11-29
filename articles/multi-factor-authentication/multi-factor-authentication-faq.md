---
title: Azure multi-factor Authentication FAQ | Microsoft Docs
description: "Perguntas mais frequentes e respostas relacionadas com a multi-factor Authentication do Azure. Autenticação Multifator é um método de verificar a identidade de um utilizador que precisa de mais do que um nome de utilizador e palavra-passe. Fornece uma camada adicional de segurança para o utilizador inicie sessão e transações."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: richagi
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: joflore
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8881fc9193aa905b5d7083644a3ce8ac4411d693
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Perguntas mais frequentes sobre o multi-factor Authentication do Azure
Estas FAQ responde a questões recorrentes sobre o multi-factor Authentication do Azure e utilizar o serviço de multi-factor Authentication. Este é dividido em perguntas sobre o serviço em geral, modelos, experiências de utilizador, de faturação e resolução de problemas.

## <a name="general"></a>Geral
**P: como é que o servidor do Azure multi-factor Authentication processar dados de utilizador?**

Com o servidor do multi-factor Authentication, os dados de utilizador são armazenados apenas nos servidores no local. Nenhuns dados de utilizador persistentes são armazenados na nuvem. Quando o utilizador efetua a verificação de dois passos, o servidor multi-factor Authentication envia dados para o serviço de nuvem do Azure multi-factor Authentication para autenticação. A comunicação entre o servidor multi-factor Authentication e o serviço de nuvem do multi-factor Authentication utiliza Secure Sockets Layer (SSL) ou Transport Layer Security (TLS) através da porta 443 de saída.

Pedidos de autenticação são enviados para o serviço em nuvem, os dados são recolhidos para autenticação e a utilização de relatórios. Os campos de dados incluídos nos registos de verificação de dois passos são os seguintes:

* **ID exclusivo** (o nome ou no local multi-factor Authentication Server ID de utilizador)
* **Primeiro e último nome** (opcional)
* **Endereço de correio eletrónico** (opcional)
* **Número de telefone** (ao utilizar uma chamada de voz ou autenticação por SMS)
* **Token do dispositivo** (ao utilizar a autenticação da aplicação móvel)
* **Modo de autenticação**
* **Resultado da autenticação**
* **Nome do servidor multi-factor Authentication**
* **Servidor multi-factor Authentication IP**
* **Cliente IP** (se disponível)

Os campos opcionais podem ser configurados no servidor multi-factor Authentication.

O resultado de verificação (êxito ou recusa) e o motivo pelo qual se foi negado, é armazenada com os dados de autenticação. Estes dados ficarem disponíveis nos relatórios de utilização e de autenticação.

**P: quais códigos curtos de SMS são utilizados para enviar mensagens SMS para os meus utilizadores?**

Dos Estados Unidos, a Microsoft utiliza os seguintes códigos de curtos de SMS:

   * 97671
   * 69829
   * 51789
   * 99399

No Canadá, a Microsoft utiliza os seguintes códigos de curtos de SMS:

   * 759731 
   * 673801

A Microsoft não garante consistente SMS ou baseada em voz multi-factor Authentication pedida de entrega pelo mesmo número. Com o intuito de nossos utilizadores, o Microsoft poderá de adicionar ou remover códigos curtos em qualquer altura, como podemos efetuar ajustes de rota para melhorar a deliverability SMS. Microsoft não suporta códigos curtos para países para além dos Estados Unidos e Canadá

## <a name="billing"></a>Faturação
Perguntas sobre faturação a maioria das pode ser respondido ao referir-se quer o [página de preços do multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ou a documentação sobre [como obter o Azure multi-factor Authentication](multi-factor-authentication-versions-plans.md).

**P: é a minha organização cobrada para enviar as mensagens de texto que são utilizadas para autenticação e as chamadas telefónicas?**

Não, não lhe serem cobrados para texto ou individuais chamadas telefónicas colocadas as mensagens enviadas aos utilizadores através do Azure multi-factor Authentication. Se utilizar um fornecedor MFA por autenticação, é-lhe faturado para cada autenticação, mas não para o método utilizado.

Os utilizadores poderão cobrados a chamadas de telefone ou as mensagens de texto que recebem, de acordo com o seu serviço de telefone pessoal.

**P: o modelo de faturação por utilizador cobram que me são ativados todos os utilizadores ou apenas aqueles que efetuar a verificação de dois passos?**

Faturação baseada no número de utilizadores configurados para utilizar o multi-factor Authentication, independentemente de se poderem efetuar verificação de dois passos desse mês.

**P: como funciona a faturação do multi-factor Authentication?**

Quando criar um fornecedor MFA por utilizador ou por autenticação, a subscrição do Azure da sua organização é faturada mensalmente com base na utilização. Este modelo de faturação é semelhante à forma como o Azure cobra para utilização de máquinas virtuais e os Web sites.

Ao adquirir uma subscrição do Azure multi-factor Authentication, a organização pays apenas a taxa de licença anuais para cada utilizador. Licenças MFA e do Office 365, Azure AD Premium, ou Enterprise Mobility + os pacotes de segurança são faturados desta forma. 

Saiba mais sobre as suas opções em [como obter o Azure multi-factor Authentication](multi-factor-authentication-versions-plans.md).

**P: existe uma versão gratuita do Azure multi-factor Authentication?**

Em alguns casos, Sim.

Autenticação Multifator para os administradores do Azure oferece um subconjunto das funcionalidades de MFA do Azure sem custos de acesso a serviços online da Microsoft, incluindo os portais de administrador do Azure e o Office 365. Esta oferta aplica-se apenas para os administradores globais em instâncias do Azure Active Directory que não têm a versão completa do MFA do Azure através de uma licença do MFA, um pacote ou um fornecedor de autónomo com base no consumo. Se os seus administradores de utilizam a versão gratuita e, em seguida, comprar uma versão completa do MFA do Azure, em seguida, todos os administradores globais são elevados para a versão paga automaticamente.

Autenticação Multifator para utilizadores do Office 365 oferece um subconjunto das funcionalidades de MFA do Azure sem custos para aceder aos serviços do Office 365, incluindo o Exchange Online e SharePoint Online. Esta oferta aplica-se os utilizadores que possuem uma licença do Office 365 atribuída, quando a instância correspondente do Azure Active Directory não tem a versão completa do MFA do Azure através de uma licença do MFA, um pacote ou um fornecedor de autónomo com base no consumo.

**P: pode mudar a minha organização entre por utilizador e por autenticação modelos de faturação consumo em qualquer altura?**

Se a sua organização comprar o MFA como um serviço autónomo com faturação baseada no consumo, escolha um modelo de faturação quando criar um fornecedor de MFA. Não é possível alterar o modelo de faturação depois de criar um fornecedor de MFA. No entanto, pode eliminar o fornecedor MFA e, em seguida, criar uma com um modelo de faturação diferente.

Quando é criado um fornecedor de MFA, podem ser associado a um Azure Active Directory (também conhecido como "inquilino do Azure AD"). Se o fornecedor de MFA atual estiver associado a um inquilino do Azure AD, pode eliminar o fornecedor MFA com segurança e criar um que está ligada ao mesmo inquilino do Azure AD. Em alternativa, se comprou licenças suficientes do MFA, Azure AD Premium ou Enterprise Mobility + Security (EMS) para abranger todos os utilizadores ativados para o MFA, pode eliminar totalmente o fornecedor do MFA.

Se for o fornecedor MFA *não* ligado a um inquilino do Azure AD ou ligar o novo fornecedor MFA um Azure AD diferentes inquilinos, as definições de utilizador e as opções de configuração não são transferidas. Além disso, os Servidores MFA do Azure existentes têm de ser reativados com as credenciais de ativação geradas através do novo Fornecedor do MFA. Reativar os Servidores MFA para associá-los ao novo Fornecedor do MFA não afeta a autenticação por chamada telefónica e mensagem de texto, mas as notificações da aplicação móvel deixarão de funcionar para todos os utilizadores até que reativem a aplicação móvel.

Saiba mais sobre os fornecedores MFA no [introdução de um fornecedor do Azure multi-factor Auth](multi-factor-authentication-get-started-auth-provider.md).

**P: a minha organização alternar entre com base no consumo de faturação e subscrições (um modelo baseado em licenças) em qualquer altura?**

Em alguns casos, Sim.

Se tiver o seu diretório um *por utilizador* fornecedor do multi-factor Authentication do Azure, pode adicionar licenças MFA. Os utilizadores com licenças não são contabilizadas na faturação baseada no consumo por utilizador. Os utilizadores sem licenças ainda podem ser ativados para a MFA através do fornecedor MFA. Se comprar e atribuir licenças para todos os utilizadores configurados para utilizar o multi-factor Authentication, pode eliminar o fornecedor de multi-factor Authentication do Azure. Pode criar sempre outro fornecedor MFA por utilizador se tiver mais utilizadores a licenças no futuro.

Se tiver o seu diretório um *por autenticação* fornecedor do multi-factor Authentication do Azure, é sempre faturado por cada autenticação, desde que o fornecedor MFA está ligado à sua subscrição. Pode atribuir licenças do MFA para utilizadores, mas irá ainda ser-lhe faturado para cada pedido de verificação de dois passos, se se trata de outra pessoa com uma licença do MFA atribuída ou não.

**P: a minha organização tem de utilizar e sincronizar identidades ao utilizar o multi-factor Authentication do Azure?**

Se a organização utilizar um modelo de faturação baseada no consumo, o Azure Active Directory é opcional, mas não é necessária. Se o seu fornecedor MFA não está ligado a um inquilino do Azure AD, pode implementar apenas no local do servidor do Azure multi-factor Authentication.

Azure Active Directory é necessário para o modelo de licença porque licenças são adicionadas ao inquilino do Azure AD quando comprar e atribuí-las aos utilizadores no diretório.

## <a name="manage-and-support-user-accounts"></a>Gerir e suportam contas de utilizador

**P: o que devo dizer os meus utilizadores fazer se não recebeu uma resposta no seu telefone ou não tem o telefone com os mesmos?**

Hopefully todos os seus utilizadores configurado mais do que um método de verificação. Diga-lhes que selecionem um método de verificação diferente na página de início de sessão e que tentem iniciar sessão novamente.

Pode apontar os seus utilizadores para o [guia de resolução de problemas do utilizador final](./end-user/multi-factor-authentication-end-user-troubleshoot.md).


**P: o que posso fazer se um dos meus utilizadores não é possível obter a conta?**

Só pode repor a conta de utilizador por torná-los para seguir o processo de registo novamente. Saiba mais sobre [gerir definições de utilizadores e dispositivos com o Azure multi-factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md).

**P: o que devo fazer se perder um dos meus utilizadores um telefone que está a utilizar palavras-passe de aplicação?**

Para impedir o acesso não autorizado, elimine palavras-passe de aplicação do utilizador. Depois do utilizador tiver um dispositivo de substituição, estes podem recriar as palavras-passe. Saiba mais sobre [gerir definições de utilizadores e dispositivos com o Azure multi-factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md).

**P: E se um utilizador não é possível iniciar sessão em aplicações não baseadas no browser?**

Se a organização ainda utilizar clientes legados e [permitida a utilização de palavras-passe de aplicação](multi-factor-authentication-whats-next.md#app-passwords), em seguida, os utilizadores não é possível iniciar sessão para estes clientes legados com o respetivo nome de utilizador e palavra-passe. Em vez disso, precisam de [configurar palavras-passe de aplicação](./end-user/multi-factor-authentication-end-user-app-passwords.md). Os utilizadores tem de limpar (delete) as suas informações de início de sessão, reiniciar a aplicação e, em seguida, inicie sessão com o nome de utilizador e *palavra-passe de aplicação* em vez da palavra-passe normal.

Se a sua organização não tiver clientes legados, não deve permitir que os utilizadores criem palavras-passe de aplicação.

> [!NOTE]
> Autenticação moderna para clientes do Office 2013
>
> As palavras-passe de aplicação só são necessárias para as aplicações que não suportem a autenticação moderna. Os clientes do Office 2013 suportam os protocolos de autenticação moderna, mas tem de ser configurado. Clientes mais recentes do Office suportam automaticamente os protocolos de autenticação moderna. Para obter mais informações, consulte o [anúncio de pré-visualização pública de autenticação moderna do Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**P: os meus utilizadores dizem que por vezes, não recebem a mensagem de texto, ou responder a mensagens de texto bidirecional, mas a verificação de tempo excedido.**

Não é garantido que entrega de mensagens de texto e a receção de respostas na SMS bidirecional porque existem uncontrollable fatores que podem afetar a fiabilidade do serviço. Estes fatores incluem o país/região de destino, operadora telemóvel e força o sinal.

Se os utilizadores têm, muitas vezes, problemas com a receção de forma fiável as mensagens de texto, informe-o para utilizar o método de aplicação ou chamada telefónica móvel em vez disso. A aplicação móvel pode receber notificações tanto através de ligações Wi-Fi e de rede móvel. Além disso, a aplicação móvel pode gerar códigos de verificação, mesmo quando o dispositivo tem sem sinal de todo. A aplicação Authenticator da Microsoft está disponível para [Android](http://go.microsoft.com/fwlink/?Linkid=825072), [IOS](http://go.microsoft.com/fwlink/?Linkid=825073), e [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

Se tiver de utilizar as mensagens de texto, recomendamos que utilize o SMS unidirecional em vez de SMS bidirecional sempre que possível. SMS unidirecional é mais fiável e impede que os utilizadores incorrer em custos globais de SMS de resposta a uma mensagem de texto foi enviada a partir de outro país.

**P: Posso alterar a quantidade de tempo, que os meus utilizadores tenham de introduzir o código de verificação a partir de uma mensagem de texto, antes do sistema exceder o tempo limite?**

Em alguns casos, Sim. 

Para SMS unidirecional com o servidor MFA do Azure v-7.0 ou superior, pode configurar o tempo limite ao efectuar a definição uma chave de registo. Depois do serviço de nuvem MFA envia a mensagem de texto, é devolvido o código de verificação (ou o código de acesso Monouso) para o servidor MFA. O servidor MFA armazena o código na memória para 300 segundos por predefinição. Se o utilizador não introduzir o código antes de que passaram os 300 segundos, a autenticação é negada. Utilize estes passos para alterar a definição de tempo limite predefinido:

1. Aceda à HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Crie uma chave de registo DWORD denominada **pfsvc_pendingSmsTimeoutSeconds** e defina o tempo em segundos que pretende que o servidor MFA do Azure para armazenar os códigos de acesso Monouso.

>[!TIP] 
>Se tiver vários servidores de MFA, apenas o um que processou o pedido de autenticação original sabe o código de verificação que foi enviado ao utilizador. Quando o utilizador introduz o código, o pedido de autenticação para validar é necessário enviar para o mesmo servidor. Se a validação do código é enviada para um servidor diferente, a autenticação é negada. 

Para obter SMS bidirecional com o servidor MFA do Azure, pode configurar a definição de tempo limite no Portal de gestão MFA. Se os utilizadores não responder a SMS dentro do período de tempo limite definido, a autenticação é negada. 

Para obter SMS unidirecional com a Azure MFA na nuvem (incluindo o adaptador AD FS ou a extensão de servidor de políticas de rede), não é possível configurar a definição de tempo limite. Azure AD armazena o código de verificação para 180 segundos. 

**P: Posso utilizar tokens de hardware com o servidor do Azure multi-factor Authentication?**

Se estiver a utilizar o servidor do Azure multi-factor Authentication, pode importar tokens de baseados no tempo, uma única palavra-passe (TOTP) de autenticação aberta (OATH) de terceiros e, em seguida, utilizá-los para a verificação de dois passos.

Pode utilizar ActiveIdentity tokens que são tokens OATH TOTP se colocar a chave secreta num ficheiro CSV e importar para o servidor do Azure multi-factor Authentication. Pode utilizar OATH tokens com serviços de Federação do Active Directory (ADFS), autenticação baseada em formulários do servidor de informação Internet (IIS) e RADIUS Remote Authentication Dial-In User Service (), desde que o sistema de cliente pode aceitar a entrada do utilizador.

Pode importar tokens OATH TOTP de terceiros com os seguintes formatos:  

- Contentor de chave simétrico (PSKC) portátil  
- Se o ficheiro contém um número de série, uma chave secreta no formato de Base 32 e um intervalo de tempo CSV  

**P: Posso utilizar servidor do Azure multi-factor Authentication para proteger os serviços de Terminal?**

Sim, mas se estiver a utilizar o Windows Server 2012 R2 ou posterior só pode proteger os serviços de Terminal utilizando o Gateway de ambiente de trabalho remoto (Gateway de RD).

Alterações de segurança no Windows Server 2012 R2 alterar como o servidor do Azure multi-factor Authentication liga ao pacote de segurança de autoridade de segurança Local (LSA) no Windows Server 2012 e versões anteriores. Para as versões dos serviços de Terminal no Windows Server 2012 ou anterior, pode [proteger uma aplicação com a autenticação do Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se estiver a utilizar o Windows Server 2012 R2, tem de Gateway de RD.

**P: Posso configurado o ID do autor da chamada no servidor MFA, mas os meus utilizadores continuar a receber chamadas de multi-factor Authentication de um emissor anónimo.**

Quando são colocadas chamadas de multi-factor Authentication através da rede pública telefone, por vezes, estes são encaminhados através de uma operadora que não suporta o ID do autor da chamada. Por este motivo, ID do autor da chamada não é garantido, apesar do sistema de multi-factor Authentication sempre envia-a.

**P: por que razão os meus utilizadores que está a ser solicitado que registe as suas informações de segurança?**
Existem vários motivos para que os utilizadores podem ser solicitado que registe as suas informações de segurança:

- O utilizador foi ativado para a MFA pelo seu administrador no Azure AD, mas não tem informações de segurança ainda registadas para a respetiva conta.
- O utilizador foi ativado para o self-service reposição palavra-passe no Azure AD. As informações de segurança irão ajudá-lo a repor a palavra-passe no futuro, se se esquecer da-lo.
- O utilizador acedeu a uma aplicação que tenha uma política de acesso condicional para exigir a MFA e ainda não registada para a MFA.
- O utilizador está a registar um dispositivo com o Azure AD (incluindo a associação do Azure AD) e organização exige a MFA para registo de dispositivos, mas o utilizador ainda não registada para a MFA.
- O utilizador está a gerar Windows Hello para empresas no Windows 10 (o que necessita de MFA) e não registada para a MFA.
- A organização tem criado e ativado uma política de registo do MFA que foi aplicada ao utilizador.
- O utilizador registado para MFA anteriormente, mas escolher um método de verificação que desativada uma vez que um administrador. O utilizador, por conseguinte, deve passar por registo do MFA novamente para selecionar um novo método de verificação de predefinição.


## <a name="errors"></a>Erros
**P: o que devem fazer os utilizadores se veem uma mensagem de erro "o pedido de autenticação não é para uma conta ativada" quando utilizar notificações de aplicação móvel?**

Informe-os a seguir este procedimento para remover a conta da aplicação móvel e, em seguida, adicioná-la novamente:

1. Aceda a [o perfil de portal do Azure](https://account.activedirectory.windowsazure.com/profile/) e inicie sessão com a sua conta organizacional.
2. Selecione **verificação adicional de segurança**.
3. Remova a conta existente da aplicação móvel.
4. Clique em **configurar**e, em seguida, siga as instruções para reconfigurar a aplicação móvel.

**P: o que devem fazer os utilizadores se veem uma mensagem de erro de 0x800434D4L quando iniciar sessão a uma aplicação não baseadas no browser?**

O erro 0x800434D4L ocorre quando tenta iniciar sessão para uma aplicação não baseadas no browser, instalada num computador local, que não funciona com contas que necessitem de verificação em dois passos.

Uma solução para este erro é ter utilizador separado das contas para relacionadas com administração e as operações de não administrador. Mais tarde, pode ligar caixas de correio entre a sua conta de administrador e a conta não administrativa, de modo a que pode iniciar sessão no Outlook ao utilizar a sua conta não administrativa. Para obter mais detalhes sobre esta solução, saiba como [conceder a capacidade de abrir e ver o conteúdo da caixa de correio de um utilizador de administrador](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Passos seguintes
Se a sua pergunta não é atendida aqui, deixe-nos comentários na parte inferior da página. Em alternativa, aqui estão algumas opções adicionais para obter ajuda:

* Pesquisa de [Base de dados de conhecimento de suporte do Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) para soluções para problemas técnicos comuns.
* Procure e procurar técnicas perguntas e respostas da Comunidade ou peça ao seu próprio pergunta no [fóruns do Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Se tiver um cliente legado do PhoneFactor e tiver dúvidas ou precisar de ajuda para repor a palavra-passe, utilize o [reposição de palavra-passe](mailto:phonefactorsupport@microsoft.com) ligação para abrir um incidente de suporte.
* Contactar um profissional de suporte através de [suporte de servidor do Azure multi-factor Authentication (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Quando contactar-nos, é útil se pode incluir a maior quantidade informações sobre o problema quanto possível. Pode fornecer as informações incluem a página onde vimos o erro, o código de erro específico, o ID de sessão específico e o ID do utilizador que vimos o erro.
