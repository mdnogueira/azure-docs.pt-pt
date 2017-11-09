---
title: Configurar a MFA do Azure | Microsoft Docs
description: "Esta é a página do multi-factor authentication que descreve o que fazer em seguida, com a MFA.  Isto inclui o alerta de fraude, relatórios, mensagens de voz personalizadas, a colocação em cache, fidedignas ips aplicação palavras-passe e a omissão de uso individual."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: joflore
ms.reviewer: alexwe
ms.openlocfilehash: 723bd7135a59bcc0bce648460f871a841a684d3c
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="configure-azure-multi-factor-authentication-settings---public-preview"></a>Configurar definições de multi-factor Authentication do Azure - pré-visualização pública

Este artigo ajuda-o a gerir o Azure multi-factor Authentication agora que já está em execução.  Abrange vários tópicos que ajudam a tirar o máximo partido do Azure multi-factor Authentication.  Nem todas estas funcionalidades estão disponíveis em cada [versão do Azure multi-factor Authentication](/multi-factor-authentication-get-started.md#what-features-do-i-need).

>[!NOTE]
>Estas definições estão em pré-visualização pública no portal do Azure. Para obter documentação sobre como gerir as definições de multi-factor Authentication do Azure no portal do pfweb, consulte [definições de configurar o Azure multi-factor Authentication](multi-factor-authentication-whats-next-pfweb.md).

| Funcionalidade | Descrição | 
|:--- |:--- |
| [Bloquear/desbloquear utilizadores](#block/unblock-users) |Bloquear/desbloquear utilizadores podem impedir os utilizadores de receber pedidos de autenticação. |
| [Alerta de fraude](#fraud-alert) |Alerta de fraude pode ser configurada e configurar para que os utilizadores podem comunicar fraudulenta tenta aceder aos respetivos recursos. |
| [Omissão de uso individual](#one-time-bypass) |Uma omissão de uso individual permite ao utilizador autenticar uma única vez, "Ignorar" multi-factor authentication. |
| [Mensagens de voz personalizadas](#custom-voice-messages) |Mensagens de voz personalizadas permitem-lhe utilizar as suas próprias gravações ou saudações com multi-factor authentication. |
| [A colocação em cache](#caching-in-azure-multi-factor-authentication) |A colocação em cache permite-lhe definir uma hora específica período para que as tentativas de autenticação subsequentes êxito automaticamente. |
| [IPs fidedignos](#trusted-ips) |Os administradores de um inquilino federado ou gerido podem utilizar IPs fidedignos para ignorar a verificação de dois passos para os utilizadores que iniciam sessão da intranet local da empresa. |
| [Palavras-passe de aplicação](#app-passwords) |Uma palavra-passe de aplicação permite que uma aplicação que não seja MFA com suporte para ignorar a multi-factor authentication e continuar a trabalhar. |
| [Lembre-se de multi-factor Authentication para browsers e dispositivos memorizados](#remember-multi-factor-authentication-for-devices-that-users-trust) |Permite-lhe não se esqueça de dispositivos para um número definido de dias após um utilizador tiver sessão com êxito através do MFA. |
| [Métodos de verificação selecionável](#selectable-verification-methods) |Permite-lhe escolher os métodos de autenticação que estão disponíveis para os utilizadores utilizem. |

## <a name="blockunblock-users"></a>Bloquear/desbloquear utilizadores
Bloquear/desbloquear utilizadores podem ser utilizados para impedir que os utilizadores receber pedidos de autenticação. Quaisquer tentativas de autenticação para utilizadores bloqueados serão rejeitadas automaticamente. Utilizadores bloqueados irão permanecer bloqueados para 90 dias desde o momento em estão bloqueados.

### <a name="block-a-user"></a>Bloquear um utilizador
1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue para **do Azure Active Directory** > **servidor MFA** > **bloquear/desbloquear utilizadores**.
3. Clique em **adicionar** para bloquear um utilizador.
4. Selecione o **grupo de replicação**, introduza o nome de utilizador bloqueado como  **username@domain.com** e introduza um comentário no **razão** campo.
5. Clique em **adicionar** para concluir a bloquear o utilizador.

### <a name="unblock-a-user"></a>Desbloquear um utilizador
1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue para **do Azure Active Directory** > **servidor MFA** > **bloquear/desbloquear utilizadores**.
3. Clique em **desbloqueio** no **ação** coluna junto ao utilizador que pretende desbloquear.
4. Introduza um comentário no **pelo motivo para desbloquear** campo.
5. Clique em **desbloqueio** para concluir a desbloquear o utilizador.

## <a name="fraud-alert"></a>Alerta de fraude
Alerta de fraude pode ser configurada e configurar para que os utilizadores podem comunicar fraudulenta tenta aceder aos respetivos recursos.  Os utilizadores podem comunicar fraude com a aplicação móvel ou através do seu telefone.

### <a name="turn-on-fraud-alert"></a>Ativar o alerta de fraude
1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue para **do Azure Active Directory** > **servidor MFA** > **alerta de fraude**.

   ![Alerta de fraudes](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. Ativar **permitir que os utilizadores enviem alertas de fraude** para **no**.
4. Selecione **Guardar**.

### <a name="configuration-options"></a>Opções de configuração

- **Bloquear utilizador quando é reportada fraude** - se um fraude de relatórios do utilizador, a conta está bloqueada.
- **Código para reportar fraude durante a saudação inicial** - quando os utilizadores recebem uma chamada telefónica para efetuar a verificação de dois passos, normalmente prima # para confirmar o seu início de sessão. Se pretende reportar fraude, ele introduzir um código antes de premir #. Este código é **0** por predefinição, mas pode personalizá-lo.

> [!NOTE]
> Saudações de voz de predefinida da Microsoft instruir os utilizadores para premir # 0 para submeter um alerta de fraude. Se pretender utilizar um código diferente de 0, deve registar e carregar as suas próprias saudações de voz personalizadas com instruções adequadas.

### <a name="view-fraud-reports"></a>Ver relatórios de fraude
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. No lado esquerdo, selecione **Active Directory**.
3. Selecione o diretório que pretende gerir. 
4. Selecione **configurar**
5. Em multi-factor Authentication, selecione **gerir definições do serviço**.
6. Na parte inferior da página de definições de serviço, selecione **aceda ao portal do**.
7. No Portal do Azure multi-factor Authentication gestão, em ver um relatório, clique em **alerta de fraude**.
8. Especifique o intervalo de datas que pretende ver o relatório. Também pode especificar nomes de utilizador, números de telefone e o estado do utilizador.
9. Clique em **Executar**. Isto apresenta um relatório de alertas de fraude. Clique em **exportar para CSV** se pretender exportar o relatório.

## <a name="one-time-bypass"></a>Omissão de uso individual
Uma omissão de uso individual permite ao utilizador autenticar-se de uma única vez sem efetuar verificação de dois passos. A omissão é temporária e expira após um número de segundos especificado. Em situações onde a aplicação móvel ou por telefone não está a receber uma notificação ou uma chamada telefónica, pode ativar uma omissão de uso individual para que o utilizador possa aceder o recursos desejados.

### <a name="create-a-one-time-bypass"></a>Criar uma omissão de uso individual

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue para **do Azure Active Directory** > **servidor MFA** > **omissão de uso individual**.

   ![Omissão de uso individual](./media/multi-factor-authentication-whats-next/onetimebypass.png)
3. Selecione **Adicionar**.
4. Se necessário, selecione o grupo de replicação para esta omissão.
5. Introduza o nome de utilizador (sob a forma de username@domain.com), o número de segundos que a omissão existirá e o motivo para a omissão. 
6. Selecione **Adicionar**. O limite de tempo entra em vigor imediatamente, pelo que o utilizador tem de iniciar sessão antes de expira a omissão de uso individual. 

### <a name="view-the-one-time-bypass-report"></a>Ver o relatório de omissão de uso individual
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. No lado esquerdo, selecione **Active Directory**.
3. Selecione o diretório que pretende gerir. 
4. Selecione **configurar**
5. Em multi-factor Authentication, selecione **gerir definições do serviço**.
6. Na parte inferior da página de definições de serviço, selecione **aceda ao portal do**.
7. No Portal do Azure multi-factor Authentication gestão, em ver um relatório, clique em **omissão de uso individual**.
8. Especifique o intervalo de datas que pretende ver o relatório. Também pode especificar nomes de utilizador, números de telefone e o estado do utilizador.
9. Clique em **Executar**. Isto apresenta um relatório de omissões. Clique em **exportar para CSV** se pretender exportar o relatório.

## <a name="custom-voice-messages"></a>Mensagens de voz personalizadas
Mensagens de voz personalizadas permitem-lhe utilizar as suas próprias gravações ou saudações para verificação de dois passos. Estes podem ser utilizados para além ou substituir o Microsoft regista.

Antes de começar, tenha em atenção o seguinte:

* Os formatos de ficheiro suportados são wav e. mp3.
* O limite de tamanho de ficheiro é 5 MB.
* Mensagens de autenticação devem ser inferiores a 20 segundos. Algo mais do que pode causar a verificação falhou porque o utilizador pode não responder antes da mensagem for concluído, fazendo com que a verificação de tempo limite.

### <a name="set-up-a-custom-message"></a>Configurar uma mensagem personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue para **do Azure Active Directory** > **servidor MFA** > **definições de chamada telefónica**.

   ![Definições de chamada telefónica](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. Selecione **adicionar saudação**.
4. Escolha o tipo de saudação inicial e o idioma.
5. Selecione um ficheiro de som. mp3 ou WAV para carregar.
6. Selecione **Adicionar**.

## <a name="caching-in-azure-multi-factor-authentication"></a>A colocação em cache no multi-factor Authentication do Azure
A colocação em cache permite-lhe definir uma hora específica período para que as tentativas de autenticação subsequentes durante esse período de tempo com êxito automaticamente. Isto é principalmente utilizado quando sistemas no local, tais como VPN enviam vários pedidos de verificação, enquanto o primeiro pedido ainda está em curso. Isto permite que os pedidos subsequentes seja bem sucedida automaticamente depois do utilizador é concluída com êxito a primeira verificação em curso. 

A colocação em cache não se destina a ser utilizado para inícios de sessão para o Azure AD.

### <a name="set-up-caching"></a>Configurar a colocação em cache 
1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue para **do Azure Active Directory** > **servidor MFA** > **regras a colocação em cache**.

   ![Regras de colocação em cache](./media/multi-factor-authentication-whats-next/cachingrules.png)

4. Selecione **Adicionar**.
5. Selecione o tipo de cache da lista pendente e especificar o número de segundos de cache máximo. 
6. Se necessário, selecione um tipo de autenticação e especifique uma aplicação. 
7. Selecione **Adicionar**.


## <a name="trusted-ips"></a>IPs Fidedignos
Fidedigno que IPs é uma funcionalidade do MFA do Azure que os administradores de um inquilino federado ou gerido podem utilizar para ignorar a verificação de dois passos para utilizadores que está a iniciar sessão na intranet local da empresa. Esta funcionalidade está disponível com a versão completa do Azure multi-factor Authentication, não a versão gratuita para administradores. Para obter mais informações sobre como obter a versão completa do multi-factor Authentication do Azure, consulte [Azure multi-factor Authentication](multi-factor-authentication.md).

| Tipo de inquilino do Azure AD | Opções disponíveis do IP fidedigno |
|:--- |:--- |
| Gerido |<li>Intervalos de endereços IP específicos – os administradores podem especificar um intervalo de endereços IP que pode ignorar a verificação de dois passos para utilizadores que está a iniciar sessão na intranet da empresa.</li> |
| Federado |<li>Todos os utilizadores Federated - todos os utilizadores federados que estão a iniciar sessão de dentro da organização irão ignorar a verificação de dois passos utilizando uma afirmação emitida pelo AD FS.</li><br><li>Intervalos de endereços IP específicos – os administradores podem especificar um intervalo de endereços IP que pode ignorar a verificação de dois passos para utilizadores que está a iniciar sessão na intranet da empresa. |

Ignorar este só funciona no interior da intranet da empresa. Por exemplo, se tiver selecionado os utilizadores federados todas as e um utilizador inicia sessão de fora da intranet da empresa, esse utilizador tem de autenticar com verificação de dois passos, mesmo que o utilizador apresente uma afirmação de AD FS. 

**Experiência do utilizador final no interior da rede empresarial:**

Quando está desativado IPs fidedignos, verificação em dois passos é necessária para fluxos de browser e palavras-passe de aplicação são necessárias para aplicações de cliente avançado mais antigas. 

Quando IPs fidedignos estiver ativada, a verificação de dois passos é *não* necessário para o browser fluxos e palavras-passe de aplicação são *não* necessários para aplicações de cliente avançado mais antigas, desde que o utilizador já não criado uma palavra-passe de aplicação. Depois de uma palavra-passe de aplicação está a ser utilizado, continua a ser necessário. 

**Corpnet fora de experiência de utilizador final:**

Se IPs fidedignos está ativada ou não, a verificação é necessária para fluxos de browser, e as palavras-passe de aplicação são necessárias para aplicações de cliente avançado mais antigas. 

### <a name="to-enable-trusted-ips"></a>Para ativar os IPs fidedignos
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. No lado esquerdo, selecione **Active Directory**.
3. Selecione o diretório que pretende gerir. 
4. Selecione **configurar**
5. Em multi-factor Authentication, selecione **gerir definições do serviço**.
6. Na página de definições de serviço, em IPs fidedignos, tem duas opções:
   
   * **Para pedidos de utilizadores federados com origem na minha intranet** – a caixa de verificação. Todos os utilizadores federados que estão a iniciar sessão na rede da empresa irão ignorar a verificação de dois passos utilizando uma afirmação emitida pelo AD FS. Certifique-se de que o AD FS tem uma regra para adicionar a afirmação de intranet para o tráfego adequado. Deve criar a regra seguinte no AD FS se já existir: "c: [tipo = ="http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] = > issue(claim = c);"



   * **Para pedidos de um intervalo específico de IPs públicos** – introduza os endereços IP na caixa de texto fornecida utilizando a notação CIDR. Por exemplo: xxx.xxx.xxx.0/24 para endereços IP na xxx.xxx.xxx.1 intervalo – xxx.xxx.xxx.254 ou xxx.xxx.xxx.xxx/32 para um único endereço IP. Pode introduzir até 50 intervalos de endereços IP. Os utilizadores que iniciar sessão a partir destes endereços IP ignorar a verificação de dois passos.
7. Clique em **Guardar**.
8. Depois de tem sido aplicadas as atualizações, clique em **fechar**.

![IPs Fidedignos](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Palavras-passe de aplicação
Algumas aplicações, como o Office 2010 ou anterior e Apple Mail, não suportam a verificação de dois passos. Estas não estão configuradas para aceitar uma verificação de segundo. Para utilizar estas aplicações, tem de utilizar "palavras-passe de aplicação" em vez da palavra-passe tradicional. A palavra-passe de aplicação permite que a aplicação ignorar a verificação de dois passos e continuar a trabalhar.

> [!NOTE]
> Autenticação moderna para os clientes do Office 2013
> 
> Os clientes do Office 2013 (incluindo o Outlook) e os protocolos de autenticação moderna de suporte mais recentes e podem ser ativados para funcionarem com verificação de dois passos. Uma vez ativada, as palavras-passe de aplicação não são necessárias para estes clientes.  Para obter mais informações, consulte [Office 2013 autenticação moderna pré-visualização pública anunciada](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

### <a name="important-things-to-know-about-app-passwords"></a>Importa saber sobre palavras-passe de aplicação
Segue-se uma lista das ações que deve conhecer sobre palavras-passe de aplicação importante.

* Só deverá precisar de palavras-passe de aplicações a serem inseridas uma vez por aplicação. Os utilizadores não têm monitorizá-los e introduzi-las sempre.
* A palavra-passe real é gerada automaticamente e não é fornecida pelo utilizador. Isto acontece porque é mais difícil um atacante adivinhar a palavra-passe gerada automaticamente e é mais seguro.
* Não há um limite de 40 palavras-passe por utilizador. 
* As aplicações que as palavras-passe em cache e utilizá-lo em cenários locais poderão começam a falhar, uma vez que a palavra-passe de aplicação não é conhecida fora do id organizacional. Um exemplo é mensagens de correio eletrónico Exchange que estão no local, mas o correio é arquivado na nuvem. A mesma palavra-passe não funciona.
* Quando a autenticação multifator estiver ativada na conta de utilizador, palavras-passe de aplicação podem ser utilizadas com a maioria dos clientes não baseados no browser, tais como o Outlook e o Lync, mas não não possível executar ações administrativas utilizando palavras-passe de aplicação através de aplicações não baseadas no browser, tais como o Windows PowerShell, mesmo que esse utilizador tem uma conta de administrador.  Certifique-se que crie uma conta de serviço com uma palavra-passe segura para executar scripts do PowerShell e não ative essa conta para a verificação de dois passos.

> [!WARNING]
> As palavras-passe de aplicação não funcionam em ambientes híbridos onde os clientes comunicam com no local e na nuvem pontos finais de deteção automática. Isto acontece porque as palavras-passe de domínio são necessários para autenticar no local e as palavras-passe de aplicação são necessários para autenticar com a nuvem.

### <a name="naming-guidance-for-app-passwords"></a>Orientações de nomenclatura para palavras-passe de aplicação
Os nomes de palavra-passe de aplicações reflitam o dispositivo no qual são utilizados. Por exemplo, se tiver um portátil com aplicações não baseadas no browser, tais como o Outlook, Word e Excel, crie uma palavra-passe com o nome portátil e utilizarem essa palavra-passe de aplicação nestas aplicações. Em seguida, crie outra palavra-passe com o nome de ambiente de trabalho para as mesmas aplicações no seu computador de secretária. 

A Microsoft recomenda a criação de uma palavra-passe por dispositivo, não uma palavra-passe por aplicação.

### <a name="federated-sso-app-passwords"></a>Palavras-passe de aplicação Federado (SSO)
AD do Azure suporta a Federação (início de sessão) com o local no Windows Server Active Directory Domain Services (AD DS). Se a sua organização estiver federada com o Azure AD e, se pretender utilizar o multi-factor Authentication do Azure, as seguintes informações sobre as palavras-passe de aplicação são importantes para si. Esta secção aplica-se apenas aos clientes do Federado (SSO).

* As palavras-passe de aplicação são verificadas pelo Azure AD e, por conseguinte, ignorar a Federação. Federação é utilizada apenas ativamente ao definir palavras-passe de aplicação.
* Para os utilizadores federados (SSO), iremos nunca vá para o fornecedor de identidade (IdP), ao contrário do fluxo passivo. As palavras-passe são armazenadas no id organizacional. Se o utilizador sai da empresa, informações de que tem de fluir para o id organizacional utilizando DirSync em tempo real. A desativação/eliminação da conta pode demorar até três horas a sincronização, atrasando a desativação/eliminação da palavra-passe de aplicação no Azure AD.
* As definições de Controlo de Acesso de Cliente no local não são honradas pela Palavra-passe de Aplicação.
* Sem autenticação no local a capacidade de registo/auditoria está disponível para a palavra-passe de aplicação.
* Determinados estruturas da arquitetura avançadas podem exigir uma combinação de nome de utilizador organizacional e palavras-passe e palavras-passe de aplicação ao utilizar a verificação de dois passos com clientes, consoante onde se autenticar. Para clientes que se autenticarem numa infraestrutura no local, teria de utilizar um nome de utilizador organizacional e a palavra-passe. Para clientes de autenticação no Azure AD, teria de utilizar a palavra-passe de aplicação.

  Por exemplo, suponha que tem uma arquitetura que consiste no seguinte:

  * Federação de instância no local do Active Directory com o Azure AD
  * Estiver a utilizar o Exchange online
  * Está a utilizar o Lync que seja especificamente no local
  * Está a utilizar o multi-factor Authentication do Azure

  ![Proofup](./media/multi-factor-authentication-whats-next/federated.png)

  Nestes casos, terá de efetuar o seguinte:

  * Quando iniciar sessão para o Lync, utilize o nome de utilizador e palavra-passe das suas organizações.
  * Quando tentar aceder o livro de endereços através de um cliente do Outlook que liga ao Exchange online, utilize uma palavra-passe de aplicação.

### <a name="allow-app-password-creation"></a>Permitir a criação de palavra-passe de aplicação
Por predefinição, os utilizadores não é possível criar palavras-passe de aplicação. Esta funcionalidade tem de estar ativada. Para permitir que os utilizadores a capacidade de criar palavras-passe de aplicação, utilize o seguinte procedimento:

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. No lado esquerdo, selecione **Active Directory**.
3. Selecione o diretório que pretende gerir. 
4. Selecione **configurar**
5. Em multi-factor Authentication, selecione **gerir definições do serviço**.
6. Selecione o botão de opção junto a **permitir aos utilizadores criar palavras-passe de aplicação para iniciar sessão em aplicações não baseadas no browser**.

![Criar palavras-passe de aplicação](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Criar palavras-passe de aplicação
Os utilizadores podem criar palavras-passe de aplicação durante o respetivo registo inicial. São fornecidos uma opção no final do processo de registo que lhe permita criar palavras-passe de aplicação.

Os utilizadores também podem criar palavras-passe de aplicação após o registo, alterando as definições no portal do Azure ou no portal do Office 365. Para obter mais informações e os passos detalhados para os seus utilizadores, consulte [quais são as palavras-passe de aplicação no Azure multi-factor Authentication](./end-user/multi-factor-authentication-end-user-app-passwords.md).

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>Lembre-se de multi-factor Authentication para os dispositivos que os utilizadores de confiança
A memorizar o multi-factor Authentication para dispositivos e browsers que os utilizadores de confiança é uma funcionalidade livre para todos os utilizadores MFA. Permite-lhe conceder aos utilizadores a opção para ignorar MFA para um número definido de dias depois de efetuar um bem-sucedida início de sessão através do MFA. Isto pode melhorar a facilidade de utilização por minimizar o número de vezes que um utilizador pode executar a verificação de dois passos no mesmo dispositivo.

No entanto, se um dispositivo ou a conta for comprometido, memorizar MFA para dispositivos fidedignos pode afetar a segurança. Se uma conta de empresa fica comprometida ou se um dispositivo fidedigno é perdido ou roubado, deve [restaurar o multi-factor Authentication em todos os dispositivos](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user). Esta ação revogará o estado de todos os dispositivos fidedigno e o utilizador é necessário para executar novamente a verificação de dois passos. Também pode instruir os utilizadores para restaurar o MFA nos respetivos dispositivos com as instruções em [gerir as definições de verificação em dois passos](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)

### <a name="how-it-works"></a>Como funciona

A memorizar o multi-factor Authentication funciona através da definição de um cookie persistente no browser quando verifica a um utilizador de "não perguntar novamente durante **X** dias" caixa no início de sessão. Não será solicitado ao utilizador para a MFA novamente do que broswer até que o cookie expira. Se o utilizador abre um browser diferente no mesmo dispositivo ou limpa os cookies, estes recebem um pedido para verificar novamente. 

O "não perguntar novamente durante **X** dias" caixa de verificação não é apresentada nas aplicações não baseadas no browser, quer tenha ou não suportam autenticação moderna. Estas aplicações utilizam tokens de atualização que fornecem novos tokens de acesso a cada hora. Quando um token de atualização é validadas, Azure AD as verificações que foi efetuada a última verificação de dois passos tempo estava dentro do número de dias configurado. 

Por conseguinte, memorizar MFA em dispositivos fidedignos reduz o número de autenticações nas aplicações web (que normalmente solicitar a cada hora), mas aumenta o número de autenticações para clientes de autenticação moderna (que normalmente pedem todos os 90 dias).

> [!NOTE]
>Esta funcionalidade não é compatível com a funcionalidade "Manter a minha sessão iniciada" do AD FS quando os utilizadores executar verificação de dois passos para o AD FS através do servidor MFA do Azure ou uma solução MFA de terceiros. Se os utilizadores podem selecionar a opção "Manter a minha sessão iniciada" no AD FS e também marcar o respetivo dispositivo como fidedigna para a MFA, não poderá verificar depois do número de "Memorizar MFA" de dias expira. Azure AD solicita uma verificação de dois passos raiz, mas do AD FS devolve um token com a afirmação MFA original e a data em vez de efetuar novamente a verificação de dois passos. Isto define desativar um ciclo de verificação entre o Azure AD e AD FS. 

### <a name="enable-remember-multi-factor-authentication"></a>Ativar Lembre-se de multi-factor authentication
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. No lado esquerdo, selecione **Active Directory**.
3. Selecione o diretório que pretende gerir. 
4. Selecione **configurar**
5. Em multi-factor Authentication, selecione **gerir definições do serviço**.
6. Na página de definições de serviço, na opção para gerir as definições de dispositivo do utilizador, verifique o **permitir que os utilizadores memorizem a autenticação multifator em dispositivos que eles confiam** caixa.
   ![Lembre-se de dispositivos](./media/multi-factor-authentication-whats-next/remember.png)
7. Defina o número de dias que pretende permitir que os dispositivos fidedignos ignorar a verificação de dois passos. A predefinição é 14 dias.
8. Clique em **Guardar**.
9. Clique em **Fechar**.

### <a name="mark-a-device-as-trusted"></a>Marcar um dispositivo como fidedigna

Depois de ativar esta funcionalidade, os utilizadores podem marcar um dispositivo como fidedigna quando iniciam sessão no verificando **não voltar a perguntar**.

![Não voltar a perguntar - captura de ecrã](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Métodos de verificação selecionável
Pode escolher que métodos de verificação estão disponíveis para os seus utilizadores. A tabela abaixo fornece uma breve descrição geral de cada método.

Quando das respetivas contas de inscrever os seus utilizadores para a MFA, se escolherem o método de verificação preferida fora as opções que ativou. As orientações para o seu processo de inscrição é abordada [configurar a minha conta para a verificação de dois passos](multi-factor-authentication-end-user-first-time.md)

| Método | Descrição |
|:--- |:--- |
| Ligar para telefone |Coloca uma chamada de voz automatizada. O utilizador atende a chamada e prime # no teclado do telefone para se autenticar. Este número de telefone não está sincronizado para o Active Directory no local. |
| Mensagem de texto para telefone |Envia uma mensagem de texto que contém um código de verificação. É pedido ao utilizador que responda à mensagem de texto com o código de verificação ou introduzir o código de verificação para a interface de início de sessão. |
| Notificação através de aplicações móveis |Envia uma notificação push para o seu telefone ou dispositivo registado. O utilizador vê a notificação e seleciona **verifique** para concluir a verificação. <br>A aplicação Authenticator da Microsoft está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Código de verificação da aplicação móvel |A aplicação Microsoft Authenticator gera um novo código de verificação de OATH cada trinta segundos. O utilizador introduz este código de verificação para a interface de início de sessão.<br>A aplicação Authenticator da Microsoft está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Como ativar/desativar os métodos de autenticação
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. No lado esquerdo, selecione **Active Directory**.
3. Selecione o diretório que pretende gerir. 
4. Selecione **configurar**
5. Em multi-factor Authentication, selecione **gerir definições do serviço**.
6. Na página de definições de serviço, em Opções de verificação, selecionar/anular a seleção de opções que pretende utilizar.
   ![Opções de verificação](./media/multi-factor-authentication-whats-next/authmethods.png)
7. Clique em **Guardar**.
8. Clique em **Fechar**.

