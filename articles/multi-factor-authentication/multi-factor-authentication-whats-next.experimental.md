---
title: Configurar a MFA do Azure | Microsoft Docs
description: "Esta é a página do multi-factor authentication que descreve o que fazer em seguida, com a MFA.  Isto inclui o alerta de fraude, relatórios, mensagens de voz personalizadas, a colocação em cache, fidedignas ips aplicação palavras-passe e a omissão de uso individual."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: richagi
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: joflore
ms.openlocfilehash: 31b32079de19c6c9822c388f60269b07a8c70198
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurar definições de multi-factor Authentication do Azure
Este artigo ajuda-o a gerir o Azure multi-factor Authentication agora que já está em execução.  Abrange vários tópicos que ajudam a tirar o máximo partido do Azure multi-factor Authentication.  Nem todas estas funcionalidades estão disponíveis em cada versão do Azure multi-factor Authentication.

| Funcionalidade | Descrição | 
|:--- |:--- |
| [Alerta de fraude](#fraud-alert) |Alerta de fraude pode ser configurada e configurar para que os utilizadores podem comunicar fraudulenta tenta aceder aos respetivos recursos. |
| [Omissão de uso individual](#one-time-bypass) |Uma omissão de uso individual permite ao utilizador autenticar uma única vez, "Ignorar" multi-factor authentication. |
| [Mensagens de voz personalizadas](#custom-voice-messages) |Mensagens de voz personalizadas permitem-lhe utilizar as suas próprias gravações ou saudações com multi-factor authentication. |
| [A colocação em cache](#caching-in-azure-multi-factor-authentication) |A colocação em cache permite-lhe definir uma hora específica período para que as tentativas de autenticação subsequentes êxito automaticamente. |
| [IPs fidedignos](#trusted-ips) |Os administradores de um inquilino federado ou gerido podem utilizar IPs fidedignos para ignorar a verificação de dois passos para os utilizadores que iniciam sessão da intranet local da empresa. |
| [Palavras-passe de aplicação](#app-passwords) |Uma palavra-passe de aplicação permite que uma aplicação que não seja MFA com suporte para ignorar a multi-factor authentication e continuar a trabalhar. |
| [Lembre-se de multi-factor Authentication para browsers e dispositivos memorizados](#remember-multi-factor-authentication-for-devices-that-users-trust) |Permite-lhe não se esqueça de dispositivos para um número definido de dias após um utilizador tiver sessão com êxito através do MFA. |
| [Métodos de verificação selecionável](#selectable-verification-methods) |Permite-lhe escolher os métodos de autenticação que estão disponíveis para os utilizadores utilizem. |

## <a name="access-the-azure-mfa-management-portal"></a>Aceder ao Portal de gestão do MFA do Azure

As funcionalidades abordadas neste artigo são configuradas no Portal de gestão do Azure multi-factor Authentication. Existem duas formas de aceder ao portal de gestão de MFA através do portal clássico do Azure. A primeira é através da gestão de um fornecedor do multi-factor Auth. A segunda é através de definições do serviço MFA. 

### <a name="use-an-authentication-provider"></a>Utilize um fornecedor de autenticação

Se utilizar um fornecedor do multi-factor Auth para a MFA com base no consumo, utilize este método para aceder ao portal de gestão.

Para aceder ao Portal de gestão MFA através de um fornecedor do multi-factor Auth do Azure, inicie sessão no portal clássico do Azure como administrador e selecione a opção de Active Directory. Clique em de **fornecedores do multi-factor Auth** separador, em seguida, selecione o diretório e clique em de **gerir** na parte inferior.

### <a name="use-the-mfa-service-settings-page"></a>Utilize a página de definições do serviço de MFA 

Se tiver uma das seguintes licenças, pode utilizar a página de definições do serviço de MFA:
- Azure MFA
- Azure AD Premium 
- Enterprise Mobility + Security

Para aceder ao Portal de gestão MFA através da página de definições do serviço de MFA, inicie sessão no portal clássico do Azure como administrador e selecione a opção de Active Directory. Clique no seu diretório e, em seguida, no separador **Configurar**. Na secção da autenticação multifator, selecione **Gerir definições do serviço**. Na parte inferior da página Definições de Serviço do MFA, clique na ligação **Aceder ao portal**.


## <a name="fraud-alert"></a>Alerta de Fraude
Alerta de fraude pode ser configurada e configurar para que os utilizadores podem comunicar fraudulenta tenta aceder aos respetivos recursos.  Os utilizadores podem comunicar fraude com a aplicação móvel ou através do seu telefone.

### <a name="set-up-fraud-alert"></a>Configurar o alerta de fraude
1. Navegue até ao Portal de gestão de MFA, de acordo com as instruções na parte superior desta página.
2. No Portal de gestão do Azure multi-factor Authentication, clique em **definições** na secção de configuração.
3. Na secção de alerta de fraude da página de definições, verifique o **permitir que os utilizadores enviem alertas de fraude** caixa de verificação.
4. Selecione **guardar** para aplicar as suas alterações. 

### <a name="configuration-options"></a>Opções de configuração

- **Bloquear utilizador quando é reportada fraude** - se um fraude de relatórios do utilizador, a conta está bloqueada.
- **Código para relatório fraude durante inicial Greeting** -os utilizadores normalmente prima # para confirmar a verificação de dois passos. Se pretende reportar fraude, ele introduzir um código antes de premir #. Este código é **0** por predefinição, mas pode personalizá-lo.

> [!NOTE]
> Saudações de voz de predefinida da Microsoft instruir os utilizadores para premir # 0 para submeter um alerta de fraude. Se pretender utilizar um código diferente de 0, deve registar e carregar as suas próprias saudações de voz personalizadas com instruções adequadas.

![Opções de alerta de fraude - captura de ecrã](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="how-users-report-fraud"></a>Como os utilizadores reportam fraude 
Alerta de fraude pode ser comunicada duas formas.  Quer através da aplicação móvel ou através do telefone.  

#### <a name="report-fraud-with-the-mobile-app"></a>Reportar fraude com a aplicação móvel
1. Quando uma verificação é enviada para o seu telemóvel, selecione-o para abrir a aplicação Authenticator da Microsoft.
2. Selecione **negar** na notificação. 
3. Selecione **reportar fraude**.
4. Feche a aplicação.

#### <a name="report-fraud-with-a-phone"></a>Reportar fraude com um telemóvel
1. Quando uma chamada de verificação é apresentada no seu telemóvel, responda-lo.  
2. Para reportar fraude, introduza o código de fraude (a predefinição é 0) e, em seguida, a tecla #. Em seguida, for notificado de que foi submetido um alerta de fraude.
3. Termine a chamada.

### <a name="view-fraud-reports"></a>Ver relatórios de fraude
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. No lado esquerdo, selecione Active Directory.
3. Na parte superior, selecione **fornecedores do multi-factor Auth** para mostrar uma lista dos seus fornecedores do multi-factor Auth.
4. Selecione o fornecedor do multi-factor Auth e clique em **gerir** na parte inferior da página. Abre o Portal de gestão do Azure multi-factor Authentication.
5. O Portal de gestão do Azure multi-factor Authentication, em ver um relatório, clique em **alerta de fraude**.
6. Especifique o intervalo de datas que pretende ver o relatório. Também pode especificar nomes de utilizador, números de telefone e o estado do utilizador.
7. Clique em **executar** para mostrar um relatório de alertas de fraude. Clique em **exportar para CSV** se pretender exportar o relatório.

## <a name="one-time-bypass"></a>Omissão de uso individual
Uma omissão de uso individual permite ao utilizador autenticar-se de uma única vez sem efetuar verificação de dois passos. A omissão é temporária e expira após um número de segundos especificado. Em situações onde a aplicação móvel ou por telefone não está a receber uma notificação ou uma chamada telefónica, pode ativar uma omissão de uso individual para que o utilizador possa aceder o recursos desejados.

### <a name="create-a-one-time-bypass"></a>Criar uma omissão de uso individual
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Navegue até ao Portal de gestão de MFA, de acordo com as instruções na parte superior desta página.
3. Se vir o nome do seu fornecedor de MFA do Azure no lado esquerdo com um  **+**  junto ao mesmo, clique em de  **+** . São apresentados diferentes grupos de replicação do servidor MFA e o grupo predefinido do Azure. Selecione o grupo adequado.
4. Em administração de utilizador, selecione **omissão de uso individual**.
5. Na página omissão de uso individual, clique em **omissão de uso individual novo**.

  ![Nuvem](./media/multi-factor-authentication-whats-next/create1.png)

6. Introduza o nome de utilizador, a duração da desativação e a razão para a omissão. Clique em **ignorar**.
7. O limite de tempo entra em vigor imediatamente, pelo que o utilizador tem de iniciar sessão antes de expira a omissão de uso individual. 

### <a name="view-the-one-time-bypass-report"></a>Ver o relatório de omissão de uso individual
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. No lado esquerdo, selecione Active Directory.
3. Na parte superior, selecione **fornecedores do multi-factor Auth** para mostrar uma lista dos seus fornecedores do multi-factor Auth.
4. Selecione o fornecedor do multi-factor Auth e clique em **gerir** na parte inferior da página. Abre o Portal de gestão do Azure multi-factor Authentication.
5. O Portal de gestão do Azure multi-factor Authentication, à esquerda, em ver um relatório, clique em **omissão de uso individual**.
6. Especifique o intervalo de datas que pretende ver o relatório. Também pode especificar nomes de utilizador, números de telefone e o estado do utilizador.
7. Clique em **executar** para mostrar um relatório de omissões. Clique em **exportar para CSV** se pretender exportar o relatório.

## <a name="custom-voice-messages"></a>Mensagens de voz personalizadas
Mensagens de voz personalizadas permitem-lhe utilizar as suas próprias gravações ou saudações para verificação de dois passos. Pode utilizar mensagens de voz personalizadas para além ou substituir o Microsoft regista.

Antes de começar, tenha em atenção estes aspetos:

* Os formatos de ficheiro suportados são wav e. mp3.
* O limite de tamanho de ficheiro é 5 MB.
* Mensagens de autenticação devem ser inferiores a 20 segundos. Mensagens que são mais do que 20 segundos não conceder os utilizadores tempo suficiente para responder antes de executa a verificação de tempo.

### <a name="set-up-a-custom-message"></a>Configurar uma mensagem personalizada

Existem duas partes para criar a sua mensagem personalizada. Em primeiro lugar, carregue a mensagem e, em seguida, pode ativá-la para os seus utilizadores.

Para carregar a sua mensagem personalizada:

1. Crie uma mensagem de voz personalizadas através de um dos formatos de ficheiro suportados.
2. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
3. Navegue até ao Portal de gestão de MFA, de acordo com as instruções na parte superior desta página.
4. No Portal de gestão do Azure multi-factor Authentication, clique em **mensagens de voz** na secção de configuração.
5. Na configurar: página de mensagens de voz, clique em **nova mensagem de voz**.
   ![Cloud](./media/multi-factor-authentication-whats-next/custom1.png)
6. Na configurar: nova página de mensagens de voz, clique em **gerir ficheiros de som**.
   ![Cloud](./media/multi-factor-authentication-whats-next/custom2.png)
7. Na configurar: página de ficheiros de som, clique em **carregar o ficheiro de som**.
   ![Cloud](./media/multi-factor-authentication-whats-next/custom3.png)
8. Na configurar: carregue o ficheiro de som, clique em **procurar** e navegue para a sua mensagem de voz, clique em **abra**.
9. Adicionar uma descrição e clique em **carregar**.
10. Assim que a mensagem de voz é carregada, uma mensagem confirma que carregou com êxito o ficheiro.

Para ativar a mensagem para os seus utilizadores:

1. No lado esquerdo, clique em **mensagens de voz**.
2. Na secção de mensagens de voz, clique em **nova mensagem de voz**.
3. Na linguagem lista pendente, selecione um idioma.
4. Se esta mensagem para uma aplicação específica, especifique-o na caixa de aplicação.
5. Do tipo de mensagem lista pendente, selecione o tipo de mensagem a ser substituído com a mensagem personalizada de novo.
6. Do ficheiro de som lista pendente, selecione o ficheiro de som que carregou na primeira parte.
7. Clique em **Criar**. Uma mensagem confirma que criou com êxito uma mensagem de voz.
    ![Cloud](./media/multi-factor-authentication-whats-next/custom5.png)</center>

## <a name="caching-in-azure-multi-factor-authentication"></a>A colocação em cache no multi-factor Authentication do Azure
A colocação em cache permite-lhe definir uma hora específica período para que as tentativas de autenticação subsequentes durante esse período de tempo com êxito automaticamente. A colocação em cache é utilizada quando sistemas no local, tais como VPN enviam muitos pedidos de verificação, enquanto o primeiro pedido ainda está em curso. Permitir que os pedidos subsequentes seja bem sucedida automaticamente depois do utilizador é concluída com êxito a primeira verificação em curso. 

A colocação em cache não se destina a ser utilizado para inícios de sessão para o Azure AD.

### <a name="set-up-caching"></a>Configurar a colocação em cache 
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Navegue até ao Portal de gestão de MFA, de acordo com as instruções na parte superior desta página.
3. No Portal de gestão do Azure multi-factor Authentication, clique em **colocação em cache** na secção de configuração.
4. Clique em **nova Cache** na configurar a colocação em cache da página.
5. Selecione o tipo de Cache e o segundos de cache. Clique em **Criar**.

<center>![Nuvem](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>IPs Fidedignos
IPs fidedignos é uma funcionalidade do MFA do Azure que os administradores de um inquilino federado ou gerido podem utilizar para ignorar a verificação de dois passos. Qual é utilizado para os utilizadores que estão a iniciar sessão na intranet local da empresa. Esta funcionalidade está disponível com a versão completa do Azure multi-factor Authentication, não a versão gratuita para administradores. Para obter mais informações sobre como obter a versão completa do multi-factor Authentication do Azure, consulte [Azure multi-factor Authentication](multi-factor-authentication.md).

| Tipo de inquilino do Azure AD | Opções disponíveis do IP fidedigno |
|:--- |:--- |
| Gerido |<li>Intervalos de endereços IP específicos – os administradores podem especificar um intervalo de endereços IP que pode ignorar a verificação de dois passos para utilizadores que está a iniciar sessão na intranet da empresa.</li> |
| Federado |<li>Todos os utilizadores Federated - todos os utilizadores federados que estão a iniciar sessão de dentro da organização irão ignorar a verificação de dois passos utilizando uma afirmação emitida pelo AD FS.</li><br><li>Intervalos de endereços IP específicos – os administradores podem especificar um intervalo de endereços IP que pode ignorar a verificação de dois passos para utilizadores que está a iniciar sessão na intranet da empresa. |

Ignorar este só funciona no interior da intranet da empresa. 

**Experiência do utilizador final no interior da rede empresarial:**

Quando está desativado IPs fidedignos, verificação em dois passos é necessária para fluxos de browser e palavras-passe de aplicação são necessárias para aplicações de cliente avançado mais antigas. 

Quando IPs fidedignos estiver ativada, a verificação de dois passos é *não* necessários para fluxos de browser. Palavras-passe de aplicação são *não* necessários para aplicações de cliente avançado mais antigas, desde que o utilizador já não criado uma palavra-passe de aplicação. Depois de uma palavra-passe de aplicação está a ser utilizado, continua a ser necessário. 

**Corpnet fora de experiência de utilizador final:**

Se IPs fidedignos está ativada ou não, a verificação é necessária para fluxos de browser, e as palavras-passe de aplicação são necessárias para aplicações de cliente avançado mais antigas. 

### <a name="to-enable-trusted-ips"></a>Para ativar os IPs fidedignos
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Navegue até à página de definições de serviço de MFA de acordo com as instruções no início deste artigo.
3. Na página de definições de serviço, em IPs fidedignos, tem duas opções:
   
   * **Para pedidos de utilizadores federados com origem na minha intranet** – a caixa de verificação. Todos os utilizadores federados que são iniciar sessão a partir da rede empresarial ignorar a verificação de utilizando uma afirmação emitida pelo AD FS.
   * **Para pedidos de um intervalo específico de IPs públicos** – introduza os endereços IP na caixa de texto fornecida utilizando a notação CIDR. Por exemplo: xxx.xxx.xxx.0/24 para endereços IP na xxx.xxx.xxx.1 intervalo – xxx.xxx.xxx.254 ou xxx.xxx.xxx.xxx/32 para um único endereço IP. Pode introduzir até 50 intervalos de endereços IP. Os utilizadores que iniciar sessão a partir destes endereços IP ignorar a verificação de dois passos.
4. Clique em **Guardar**.
5. Depois de tem sido aplicadas as atualizações, clique em **fechar**.

![IPs Fidedignos](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Palavras-passe de Aplicação
Algumas aplicações, como o Office 2010 ou anterior e Apple Mail, não suportam a verificação de dois passos. Estas não estão configuradas para aceitar uma verificação de segundo. Para utilizar estas aplicações, tem de utilizar "palavras-passe de aplicação" em vez da palavra-passe tradicional. A palavra-passe de aplicação permite que a aplicação ignorar a verificação de dois passos e continuar a trabalhar.

> [!NOTE]
> Autenticação moderna para os clientes do Office 2013
> 
> Os clientes do Office 2013 (incluindo o Outlook) e os protocolos de autenticação moderna de suporte mais recentes e podem ser ativados para funcionarem com verificação de dois passos. Uma vez ativada, as palavras-passe de aplicação não são necessárias para estes clientes.  Para obter mais informações, consulte [Office 2013 autenticação moderna pré-visualização pública anunciada](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

### <a name="important-things-to-know-about-app-passwords"></a>Importa saber sobre palavras-passe de aplicação
Eis uma lista de pontos importantes que deve saber sobre palavras-passe de aplicação.

* As palavras-passe de aplicação devem ser introduzidas na caixa de entrada, uma vez por aplicação. Os utilizadores não têm monitorizá-los e introduzi-las sempre.
* A palavra-passe real é gerada automaticamente e não é fornecida pelo utilizador. A palavra-passe gerada automaticamente é mais difícil um atacante adivinhar e é mais segura.
* Não há um limite de 40 palavras-passe por utilizador. 
* As aplicações que as palavras-passe em cache e utilizá-lo em cenários locais poderão começam a falhar, uma vez que a palavra-passe de aplicação não é conhecida fora do id organizacional. Um exemplo é mensagens de correio eletrónico Exchange que estão no local, mas o correio é arquivado na nuvem. A mesma palavra-passe não funciona.
* Quando é iniciada a autenticação multifator, pode utilizar a palavra-passe com alguns clientes não baseados no browser. Não é possível utilizar palavras-passe de aplicação para tarefas administrativas. Certifique-se que crie uma conta de serviço com uma palavra-passe segura para executar scripts do PowerShell e não ative essa conta para a verificação de dois passos.

> [!WARNING]
> As palavras-passe de aplicação não funcionam em ambientes híbridos onde os clientes comunicam com no local e na nuvem pontos finais de deteção automática. As palavras-passe de domínio são necessários para autenticar no local e as palavras-passe de aplicação são necessárias para autenticar com a nuvem.

### <a name="naming-guidance-for-app-passwords"></a>Orientações de nomenclatura para palavras-passe de aplicação
Os nomes de palavra-passe de aplicações reflitam o dispositivo no qual são utilizados. Por exemplo, se tiver um portátil com aplicações não baseadas no browser, criar uma palavra-passe com o nome portátil e utilizar essa palavra-passe de aplicação. Em seguida, crie outra palavra-passe com o nome de ambiente de trabalho para as mesmas aplicações no seu computador de secretária. 

A Microsoft recomenda a criação de uma palavra-passe por dispositivo, não uma palavra-passe por aplicação.

### <a name="federated-sso-app-passwords"></a>Palavras-passe de aplicação Federado (SSO)
AD do Azure suporta a Federação (início de sessão) com o local no Windows Server Active Directory Domain Services (AD DS). Se a sua organização estiver federada com o Azure AD e, se pretender utilizar o multi-factor Authentication do Azure, as informações sobre palavras-passe de aplicação são importantes para si. Esta secção aplica-se apenas aos clientes do Federado (SSO).

* As palavras-passe de aplicação são verificadas pelo Azure AD e, por conseguinte, ignorar a Federação. Federação é utilizada apenas ativamente ao definir palavras-passe de aplicação.
* Para os utilizadores federados (SSO), iremos nunca vá para o fornecedor de identidade (IdP), ao contrário do fluxo passivo. As palavras-passe são armazenadas no id organizacional. Se o utilizador sai da empresa, informações de que tem de fluir para o id organizacional utilizando DirSync em tempo real. A desativação/eliminação da conta pode demorar até três horas a sincronização, atrasando a desativação/eliminação da palavra-passe de aplicação no Azure AD.
* As definições de Controlo de Acesso de Cliente no local não são honradas pela Palavra-passe de Aplicação.
* Sem autenticação no local a capacidade de registo/auditoria está disponível para a palavra-passe de aplicação.
* Algumas estruturas da arquitetura avançadas podem exigir uma combinação de nome de utilizador organizacional, as palavras-passe e palavras-passe de aplicação ao utilizar a verificação de dois passos com clientes. Embora depende em que o se autenticar. Para clientes que se autenticarem numa infraestrutura no local, teria de utilizar um nome de utilizador organizacional e a palavra-passe. Para clientes de autenticação no Azure AD, teria de utilizar a palavra-passe de aplicação.

  Por exemplo, suponha que tem uma arquitetura que é composta por estas instâncias:

  * Federação de instância no local do Active Directory com o Azure AD
  * Estiver a utilizar o Exchange online
  * Está a utilizar o Lync que seja especificamente no local
  * Está a utilizar o multi-factor Authentication do Azure

  ![Proofup](./media/multi-factor-authentication-whats-next/federated.png)

  Nestes casos, tem de seguir estes passos:

  * Quando iniciar sessão para o Lync, utilize o nome de utilizador e palavra-passe das suas organizações.
  * Quando tentar aceder o livro de endereços através de um cliente do Outlook que liga ao Exchange online, utilize uma palavra-passe de aplicação.

### <a name="allow-app-password-creation"></a>Permitir a criação de palavra-passe de aplicação
Por predefinição, os utilizadores não é possível criar palavras-passe de aplicação, mas pode ativar a funcionalidade de si próprio. Para permitir que os utilizadores a capacidade de criar palavras-passe de aplicação, utilize o seguinte procedimento:

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Navegue até à página de definições de serviço de MFA de acordo com as instruções no início deste artigo.
3. Selecione o botão de opção junto a **permitir aos utilizadores criar palavras-passe de aplicação para iniciar sessão em aplicações não baseadas no browser**.

![Criar palavras-passe de aplicação](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Criar palavras-passe de aplicação
Os utilizadores podem criar palavras-passe de aplicação durante o respetivo registo inicial. São fornecidos uma opção no final do processo de registo que lhe permita criar palavras-passe de aplicação.

Os utilizadores também podem criar palavras-passe de aplicação após o registo. É possível criar as palavras-passe de aplicação alterando as definições no portal do Azure ou no portal do Office 365. Para obter mais informações e os passos detalhados para os seus utilizadores, consulte [quais são as palavras-passe de aplicação no Azure multi-factor Authentication](./end-user/multi-factor-authentication-end-user-app-passwords.md).

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>Lembre-se de multi-factor Authentication para os dispositivos que os utilizadores de confiança
A memorizar o multi-factor Authentication para dispositivos e browsers que os utilizadores de confiança é uma funcionalidade livre para todos os utilizadores MFA. Autenticação multifator permite aos utilizadores ignorar MFA para um número definido de dias depois de iniciar sessão. Esta funcionalidade melhora a capacidade de utilização por minimizar o número de vezes que um utilizador pode executar a verificação de dois passos no mesmo dispositivo.

No entanto, se um dispositivo ou a conta for comprometido, memorizar MFA para dispositivos fidedignos pode afetar a segurança. Se uma conta de empresa fica comprometida ou se um dispositivo fidedigno é perdido ou roubado, terá de [restaurar o multi-factor Authentication em todos os dispositivos](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user). Esta ação revogará o estado de todos os dispositivos fidedigno e o utilizador é necessário para executar novamente a verificação de dois passos. Também pode instruir os utilizadores para restaurar o MFA nos respetivos dispositivos com as instruções em [gerir as definições de verificação em dois passos](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)

### <a name="how-it-works"></a>Como funciona

A memorizar o multi-factor Authentication funciona através da definição de um cookie persistente no browser quando verifica a um utilizador de "não perguntar novamente durante **X** dias" caixa no início de sessão. Não será solicitado ao utilizador para a MFA novamente esse browser até que o cookie expira. Se o utilizador abre um browser diferente no mesmo dispositivo ou limpa os cookies, estes recebem um pedido para verificar novamente. 

O "não perguntar novamente durante **X** dias" caixa de verificação não é apresentada nas aplicações não baseadas no browser, quer tenha ou não suportam autenticação moderna. Estas aplicações utilizam tokens de atualização que fornecem novos tokens de acesso a cada hora. Quando um token de atualização é validado, do Azure AD verifica que a última verificação de dois passos de tempo foi efetuada no número de dias configurado. 

No conclusion, memorizar MFA em dispositivos fidedignos reduz o número de autenticações nas aplicações web (que normalmente Perguntar sempre). Mas também aumenta o número de autenticações para clientes de autenticação moderna (que normalmente peça a todos os 90 dias).

> [!NOTE]
>Esta funcionalidade não é compatível com a funcionalidade "Manter a minha sessão iniciada" do AD FS quando os utilizadores executar verificação de dois passos para o AD FS através do servidor MFA do Azure ou uma solução MFA de terceiros. Se os utilizadores podem selecionar a opção "Manter a minha sessão iniciada" no AD FS e também marcar o respetivo dispositivo como fidedigna para a MFA, não poderá verificar depois do número de "Memorizar MFA" de dias expira. Azure AD solicita uma verificação de dois passos raiz, mas do AD FS devolve um token com a afirmação MFA original e a data em vez de efetuar novamente a verificação de dois passos. Este processo define desativar um ciclo de verificação entre o Azure AD e AD FS. 

### <a name="enable-remember-multi-factor-authentication"></a>Ativar Lembre-se de multi-factor authentication
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Navegue até à página de definições de serviço de MFA de acordo com as instruções no início deste artigo.
3. Na página de definições de serviço, na opção para gerir as definições de dispositivo do utilizador, verifique o **permitir que os utilizadores memorizem a autenticação multifator em dispositivos que eles confiam** caixa.
   ![Lembre-se de dispositivos](./media/multi-factor-authentication-whats-next/remember.png)
4. Defina o número de dias que pretende permitir que os dispositivos fidedignos ignorar a verificação de dois passos. A predefinição é 14 dias.
5. Clique em **Guardar**.
6. Clique em **Fechar**.

### <a name="mark-a-device-as-trusted"></a>Marcar um dispositivo como fidedigna

Depois de ativar esta funcionalidade, os utilizadores podem marcar um dispositivo como fidedigna quando iniciam sessão no verificando **não voltar a perguntar**.

![Não voltar a perguntar - captura de ecrã](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Métodos de verificação selecionável
Pode escolher que métodos de verificação estão disponíveis para os seus utilizadores. A tabela seguinte fornece uma breve descrição geral de cada método.

Quando das respetivas contas de inscrever os seus utilizadores para a MFA, se escolherem o método de verificação preferida fora as opções que ativou. As orientações para o seu processo de inscrição é abordada [configurar a minha conta para a verificação de dois passos](multi-factor-authentication-end-user-first-time.md)

| Método | Descrição |
|:--- |:--- |
| Ligar para telefone |Coloca uma chamada de voz automatizada. O utilizador atende a chamada e prime # no teclado do telefone para se autenticar. Este número de telefone não está sincronizado para o Active Directory no local. |
| Mensagem de texto para telefone |Envia uma mensagem de texto que contém um código de verificação. É pedido ao utilizador que responda ao texto, a mensagem com o código de verificação, ou introduza o código de verificação para a interface de início de sessão. |
| Notificação através de aplicação móvel |Envia uma notificação push para o seu telefone ou dispositivo registado. O utilizador vê a notificação e seleciona **verifique** para concluir a verificação. <br>A aplicação Authenticator da Microsoft está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Código de verificação da aplicação móvel |A aplicação Microsoft Authenticator gera um novo código de verificação de OATH cada 30 segundos. O utilizador introduz este código de verificação para a interface de início de sessão.<br>A aplicação Authenticator da Microsoft está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Como ativar/desativar os métodos de autenticação
1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Navegue até à página de definições de serviço de MFA de acordo com as instruções no início deste artigo.
3. Na página de definições de serviço, em Opções de verificação, selecionar/anular a seleção de opções que pretende utilizar.
   ![Opções de verificação](./media/multi-factor-authentication-whats-next/authmethods.png)
4. Clique em **Guardar**.
5. Clique em **Fechar**.
