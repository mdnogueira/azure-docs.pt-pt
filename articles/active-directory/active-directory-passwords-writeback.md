---
title: "Azure AD SSPR com repetição de escrita de palavras-passe | Microsoft Docs"
description: "Utilizar o Azure AD e do Azure AD Connect para palavras-passe de repetição de escrita para um diretório no local"
services: active-directory
keywords: "Gestão de palavras-passe do Active Directory, gestão de palavras-passe, do Azure AD Self-repor a palavra-passe do serviço"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: ea2d910611f1935cadd8eaaf5760460f480f0c6c
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="password-writeback-overview"></a>Descrição geral de repetição de escrita de palavras-passe

Com repetição de escrita de palavras-passe, pode configurar o Azure Active Directory (Azure AD) para escrever as palavras-passe do Active Directory no local. Repetição de escrita de palavras-passe remove a necessidade de configurar e gerir uma solução de reposição (SSPR) de palavra-passe self-service complicada no local e fornece uma maneira conveniente para os seus utilizadores para repor as respetivas palavras-passe no local onde quer que estejam baseado na nuvem. Repetição de escrita de palavras-passe é um componente do [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) que pode ser ativada e utilizado por subscritores atuais do Premium [edições do Azure Active Directory](active-directory-whatis.md).

Repetição de escrita de palavras-passe fornece as seguintes funcionalidades:

* **Fornece comentários zero atraso**: repetição de escrita de palavras-passe é uma operação síncrona. Os utilizadores são notificados imediatamente se a palavra-passe não cumpria a política ou não foi possível ser repor ou alterada por qualquer motivo.
* **Reposições de palavra-passe de suporta para os utilizadores que utilizam serviços de Federação do Active Directory (AD FS) ou outras tecnologias de Federação**: com repetição de escrita de palavras-passe, desde que as contas de utilizador federado que são sincronizadas no seu inquilino do Azure AD, são capazes de gerir as respetivas palavras-passe no local do Active Directory na nuvem.
* **Reposições de palavra-passe de suporta para os utilizadores que utilizam** [sincronização de hash de palavra-passe](./connect/active-directory-aadconnectsync-implement-password-synchronization.md): quando o serviço de reposição de palavra-passe Deteta se uma conta de utilizador sincronizadas está ativada para sincronização de hash de palavra-passe, repomos no local ambas desta conta e palavra-passe da nuvem em simultâneo.
* **Alterações de palavra-passe de suporta do painel de acesso e do Office 365**: quando federado ou utilizadores de palavra-passe sincronizada são fornecidos para alterar as expirado ou não expirou palavras-passe, podemos repetição de escrita dessas palavras-passe para o seu ambiente do Active Directory local.
* **Suporta a repetição de escrita de palavras-passe quando um administrador repõe-los a partir do portal do Azure**: sempre que um administrador repõe a palavra-passe de um utilizador no [portal do Azure](https://portal.azure.com), se estiver federado que o utilizador ou palavra-passe sincronizado, iremos definir a palavra-passe Seleciona o administrador do Active Directory local, bem como. Esta funcionalidade não é atualmente suportada no portal de administração do Office.
* **Impõe as políticas de palavra-passe do Active Directory no local**: quando um utilizador repõe a palavra-passe, mas certifique-se de que cumpre a política do Active Directory no local antes que o consolidar a esse diretório. Esta revisão inclui a verificação do histórico, complexidade, idade, filtros de palavra-passe e outras restrições de palavra-passe que definiu no local Active Directory.
* **Não requer quaisquer regras de firewall de entrada**: repetição de escrita de palavras-passe utiliza um reencaminhamento do Service Bus do Azure como um canal de comunicação subjacente. Não tem de abrir nenhuma porta de entrada na sua firewall para esta funcionalidade funcionar.
* **Não é suportada para contas de utilizador existem no âmbito de grupos protegidos no Active Directory no local**: Para mais informações sobre grupos protegidos, consulte [protegidos contas e grupos no Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Como funciona a repetição de escrita de palavras-passe

Quando um hash de palavra-passe ou federado utilizador sincronizado vem reponham ou alterem a palavra-passe na nuvem, ocorre o seguinte:

1. -Verifique o tipo de palavra-passe tem do utilizador. Se, Vemos que a palavra-passe é gerido no local:
   * Iremos verificar se o serviço de repetição de escrita está a funcionar. Se estiver em execução, iremos permitem ao utilizador continuar.
   * Se o serviço de repetição de escrita não está ativo, estamos indicar ao utilizador que a palavra-passe não pode ser reposta neste momento.
2. Em seguida, o utilizador transmite as portas de autenticação adequado e atingir o **Repor palavra-passe** página.
3. O utilizador seleciona uma nova palavra-passe e confirma-lo.
4. Quando o utilizador seleciona **submeter**, iremos encriptar a palavra-passe de texto simples com uma chave simétrica que foi criada durante o processo de configuração de repetição de escrita.
5. Depois da palavra-passe de encriptação, iremos incluí-la num payload que é enviado através de um canal HTTPS para o reencaminhamento de barramento de serviço inquilino específico (que também configuramos para si durante o processo de configuração de repetição de escrita). Este reencaminhamento está protegido por uma palavra-passe gerada aleatoriamente que sabe apenas a instalação no local.
6. Depois da mensagem atinge o service bus, o ponto final de reposição de palavra-passe reativado automaticamente e vê que tem um pedido de reposição pendentes.
7. O serviço, em seguida, procura o utilizador utilizando o atributo âncora de nuvem. Para esta pesquisa com êxito:

    * O objeto de utilizador tem de existir no espaço de conector do Active Directory.
    * O objeto de utilizador têm de estar associado ao objeto de metaverso (MV) correspondente.
    * O objeto de utilizador têm de estar associado ao objeto de conector correspondente do Azure Active Directory.
    * A ligação do objeto de conector do Active Directory para o MV tem de ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` na ligação. <br> <br>
    Quando a chamada é apresentada da nuvem, o motor de sincronização utiliza o **cloudAnchor** atributo para consultar o objeto de espaço de conector do Azure Active Directory. Segue-se a ligação para o objeto de MV e, em seguida, segue-se a ligação para o objeto do Active Directory. Porque podem existir vários objetos do Active Directory (multifloresta) para o mesmo utilizador, o motor de sincronização depende o `Microsoft.InfromADUserAccountEnabled.xxx` ligação escolha correto.

    > [!Note]
    > Como resultado esta lógica, palavra-passe de repetição de escrita para trabalhar do Azure AD Connect tem de ser capaz de comunicar com o emulador de controlador (PDC) do domínio principal. Se precisar de ativar esta opção manualmente, pode ligar o Azure AD Connect para o emulador PDC. Clique com botão direito do **propriedades** do conector de sincronização do Active Directory, em seguida, selecione **configurar partições de diretório**. A partir daí, procure o **definições de ligação do controlador de domínio** secção e selecione a caixa intitulada **utilizam apenas os controladores de domínio preferencial**. Mesmo que o controlador de domínio preferencial não é um emulador PDC, o Azure AD Connect tenta estabelecer ligação ao PDC para repetição de escrita de palavras-passe.

8. Depois do utilizador for encontrado conta, vamos tentar repor a palavra-passe diretamente na floresta do Active Directory adequada.
9. Se a operação de definição de palavra-passe for bem sucedida, iremos indicar ao utilizador que a palavra-passe foi alterada.
    > [!NOTE]
    > Se a palavra-passe do utilizador está sincronizado com o Azure AD utilizando a sincronização de palavra-passe, há a possibilidade de que a política de palavra-passe no local é mais fraco que a política de palavra-passe da nuvem. Neste caso, iremos ainda impor que a política no local é em vez disso, sincronização de hash de palavra-passe para sincronizar o hash dessa palavra-passe. Esta política assegura que a política no local é imposta na nuvem, no matter se utilizar a sincronização de palavra-passe ou Federação para fornecer o início de sessão único.

10. Se a palavra-passe definida falha da operação, iremos devolver um erro ao utilizador e dar-lhes, tente novamente. A operação pode falhar porque:
    * O serviço foi desativado.
    * A palavra-passe que selecionou não cumpria as políticas da organização.
    * Poderá não encontrámos o utilizador no local Active Directory.

    Estamos a ter uma mensagem específica para muitos nestes casos e indicar ao utilizador que eles podem fazer para resolver o problema.

## <a name="configure-password-writeback"></a>Configurar a repetição de escrita de palavras-passe

Recomendamos que utilize a funcionalidade de atualização automática do [do Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) se quiser utilizar a repetição de escrita de palavras-passe.

O DirSync e o Azure AD Sync já não são suportadas como uma forma de ativar a repetição de escrita de palavras-passe. Para obter mais informações ajudar a transição, consulte [atualizar do DirSync e Azure AD Sync](connect/active-directory-aadconnect-dirsync-deprecated.md).

Os seguintes passos partem do princípio de que já configurou o Azure AD Connect no seu ambiente utilizando o [Express](./connect/active-directory-aadconnect-get-started-express.md) ou [personalizada](./connect/active-directory-aadconnect-get-started-custom.md) definições.

1. Para configurar e ativar a repetição de escrita de palavras-passe, inicie sessão no seu servidor do Azure AD Connect e iniciar o **do Azure AD Connect** Assistente de configuração.
2. No **boas-vindas** página, selecione **configurar**.
3. No **tarefas adicionais** página, selecione **personalizar as opções de sincronização**e, em seguida, selecione **seguinte**.
4. No **ligar para o Azure AD** página, introduza uma credencial de administrador global e, em seguida, selecione **seguinte**.
5. No **ligar a diretórios** e **domínio/UO** filtragem páginas, selecione **seguinte**.
6. No **funcionalidades opcionais** página, selecione a caixa junto a **repetição de escrita de palavras-passe** e selecione **seguinte**.
   ![Ativar a repetição de escrita de palavras-passe no Azure AD Connect][Writeback]
7. No **pronto para configurar** página, selecione **configurar** e aguarde a conclusão do processo.
8. Quando vir a configuração concluir, selecione **saída**.

Para tarefas de resolução de problemas comuns relacionadas com a repetição de escrita de palavras-passe, consulte a secção [resolver problemas de repetição de escrita de palavras-passe](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) no nosso artigo de resolução de problemas.

## <a name="active-directory-permissions"></a>Permissões do Active Directory

A conta especificada no utilitário do Azure AD Connect tem os seguintes itens definiu se pretender estar no âmbito de SSPR:

* **Repor palavra-passe** 
* **Alterar palavra-passe** 
* **Permissões de escrita** no`lockoutTime`  
* **Permissões de escrita** no`pwdLastSet`
* **Expandido direitos** no:
   * O objeto de raiz do *cada domínio* nessa floresta
   * As unidades organizacionais (UOs) de utilizador que pretende ser no âmbito da sspr

Se não tiver a certeza de que a conta descrita de conta refere-se ao, abra a IU de configuração do Azure Active Directory Connect e selecione o **configuração atual da vista** opção. A conta que terá de adicionar permissão está listado em **sincronizados diretórios**.

Se definir estas permissões, a conta de serviço MA para cada floresta pode gerir palavras-passe em nome as contas de utilizador nessa floresta. 

>[!IMPORTANT]
>Se não atribuir estas permissões, em seguida, apesar de repetição de escrita parece estar configurada corretamente, os utilizadores receberão erros quando tentarem que gerir as respetivas palavras-passe no local na nuvem.
>

> [!NOTE]
> Poderá demorar até uma hora ou mais até que estas permissões sejam replicadas em todos os objetos no seu diretório.
>

Para configurar as permissões adequadas para a palavra-passe repetição de escrita, conclua os seguintes passos:

1. Abra computadores e utilizadores do Active Directory com uma conta que tenha as permissões de administração do domínio adequado.
2. Do **vista** menu, certifique-se **funcionalidades avançadas** está ativada.
3. No painel esquerdo, clique no objeto que representa a raiz do domínio e selecione **propriedades** > **segurança** > **avançadas**.
4. Do **permissões** separador, selecione **adicionar**.
5. Escolha a conta que as permissões estão a ser aplicadas a (a partir do programa de configuração do Azure AD Connect).
6. No **aplica-se a** na lista pendente, selecione **utilizadores descendentes** objetos.
7. Em **permissões**, selecione as caixas para o seguinte:
    * **Unexpire-palavra-passe**
    * **Repor palavra-passe**
    * **Alterar palavra-passe**
    * **Escrever lockoutTime**
    * **Escrever pwdLastSet**
8. Selecione **aplicar/OK** para aplicar as alterações e sair quaisquer caixas de diálogo aberta.

## <a name="licensing-requirements-for-password-writeback"></a>Requisitos de licenciamento para a repetição de escrita de palavras-passe

Para informações sobre o licenciamento, consulte [licenças necessárias para a repetição de escrita de palavras-passe](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) ou os seguintes sites:

* [Azure Active Directory preços do site](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>Modos de autenticação no local que são suportados para a repetição de escrita de palavras-passe

Suporte de repetição de escrita de palavras-passe para os seguintes tipos de palavra-passe de utilizador:

* **Os utilizadores apenas na nuvem**: repetição de escrita de palavras-passe não se aplica esta situação, porque não há nenhuma palavra-passe de no local.
* **Os utilizadores sincronizados de palavra-passe**: repetição de escrita de palavras-passe é suportada.
* **Os utilizadores federados**: repetição de escrita de palavras-passe é suportada.
* **Os utilizadores de autenticação pass-through**: repetição de escrita de palavras-passe é suportada.

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>Utilizador e administrador de operações que são suportadas para repetição de escrita de palavras-passe

As palavras-passe são gravadas nas seguintes situações:

* **Operações do utilizador final suportados**
  * Operação de palavra-passe de alterar quaisquer voluntário pelo utilizador final de self-service
  * Operação de palavra-passe, por exemplo, a expiração de palavra-passe de alterar quaisquer force self-service do utilizador final
  * Qualquer utilizador final personalizada de palavra-passe de reposição que tem origem o [portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com)
* **Operações suportadas administrador**
  * Operação de palavra-passe de alterar quaisquer voluntário administrador de self-service
  * Imposição de self-service qualquer administrador alterar a operação de palavra-passe, por exemplo, a expiração de palavra-passe
  * Qualquer administrador personalizada de palavra-passe de reposição que tem origem o [portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com)
  * Qualquer palavra-passe de utilizador final iniciadas pelo administrador de reposição do [portal clássico do Azure](https://manage.windowsazure.com)
  * Qualquer palavra-passe de utilizador final iniciadas pelo administrador de reposição do [portal do Azure](https://portal.azure.com)

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>Utilizador e administrador de operações que não são suportadas para repetição de escrita de palavras-passe

Palavras-passe são *não* gravados em qualquer uma das seguintes situações:

* **Operações do utilizador final não suportado**
  * Qualquer utilizador final a repor a própria palavra-passe utilizando a versão 1, versão 2 ou a AD Graph API do Azure PowerShell
* **Operações de administrador não suportado**
  * Qualquer palavra-passe de utilizador final iniciadas pelo administrador de reposição do [portal de gestão do Office](https://portal.office.com)
  * Qualquer palavra-passe de utilizador final iniciadas pelo administrador de reposição da versão 1, versão 2 ou a AD Graph API do Azure PowerShell

Estamos a trabalhar para remover estas limitações, mas não temos uma linha de tempo específica que partilhamos ainda.

## <a name="password-writeback-security-model"></a>Modelo de segurança de repetição de escrita de palavras-passe

Repetição de escrita de palavras-passe é um serviço altamente seguro. Para garantir que as suas informações estão protegidas, ativamos a um modelo de segurança de quatro camadas que é descrito da seguinte forma:

* **Reencaminhamento de barramento de serviço de inquilino específico**
  * Quando configurar o serviço, iremos definir um reencaminhamento de barramento de serviço de inquilino específico que está protegido por uma gerado aleatoriamente palavra-passe segura que Microsoft nunca tenha acesso à.
* **Chave de encriptação da palavra-passe bloqueado para baixo e criptograficamente forte,**
  * Depois de criado o reencaminhamento do service bus, criamos uma chave simétrica forte que utilizamos para encriptar a palavra-passe porque se trata através da transmissão. Esta chave só se encontra no arquivo de secreta da sua empresa na nuvem, que é fortemente bloqueado e auditado, tal como qualquer outra palavra-passe no diretório.
* **Da indústria padrão segurança TLS (Transport Layer)**
  1. Quando uma palavra-passe reponham ou alterem operação ocorre na nuvem, iremos assumir a palavra-passe de texto simples e encriptá-lo com a chave pública.
  2. Vamos colocar que numa mensagem HTTPS que é enviada num canal encriptado através da utilização de certificados Microsoft SSL para o reencaminhamento do service bus.
  3. Depois da mensagem é recebido no service bus, o agente no local reativado e autentica para o service bus utilizando a palavra-passe segura gerada anteriormente.
  4. O agente no local escolherá a mensagem encriptada e desencripta a mesma, utilizando a chave privada que são gerados.
  5. O agente no local tenta definir a palavra-passe através da API de SetPassword do AD DS. Este passo é o que permite-nos para impor a política de palavra-passe do Active Directory no local (por exemplo, a complexidade, idade, histórico e os filtros) na nuvem.
* **Políticas de expiração da mensagem** 
  * Se a mensagem se encontre no service bus, porque o serviço no local está inativo, o tempo limite e é removido após vários minutos. O tempo limite e a remoção da mensagem aumenta ainda mais a segurança.

### <a name="password-writeback-encryption-details"></a>Detalhes de encriptação de repetição de escrita de palavras-passe

Depois de um utilizador submete uma reposição de palavra-passe, o pedido de reposição passa através de vários passos de encriptação antes que chega no seu ambiente no local. Estes passos de encriptação Certifique-se a segurança e fiabilidade de serviço máximo. São descritos da seguinte forma:

* **Passo 1: Encriptação de palavra-passe com chave RSA de 2048 bits**: depois de um utilizador submete uma palavra-passe para ser gravados no local, a palavra-passe submetida em si é encriptada com uma chave RSA de 2048 bits.
* **Passo 2: Encriptação de nível do pacote com o AES GCM**: O pacote completo da palavra-passe mais os metadados necessários, é encriptado utilizando AES GCM. Esta encriptação impede que qualquer pessoa com acesso direto para o canal de ServiceBus subjacente visualizar ou adulteração com o conteúdo.
* **Passo 3: Todas as comunicações ocorre através de TLS/SSL**: todas as comunicações com ServiceBus acontece um canal SSL/TLS. Esta encriptação protege o conteúdo de terceiros não autorizados.
* **Rollover de chave automático a cada seis meses**: a cada seis meses ou repetição de escrita da palavra-passe cada hora está desativada e depois reativada no Azure AD Connect. Iremos automaticamente rollover de todas as chaves para garantir a segurança do serviço máximo e segurança.

### <a name="password-writeback-bandwidth-usage"></a>Utilização de largura de banda de repetição de escrita de palavras-passe

Repetição de escrita de palavras-passe é um serviço de pouca largura de banda que só envia pedidos de volta para o agente no local nas seguintes circunstâncias:

* Duas mensagens são enviadas quando a funcionalidade está ativada ou desativada através do Azure AD Connect.
* Uma mensagem é enviada uma vez a cada cinco minutos como um heartbeat do serviço para, desde que o serviço está em execução.
* Duas mensagens são enviadas a cada hora, uma nova palavra-passe é submetida:
    * A primeira mensagem é um pedido para executar a operação.
    * A segunda mensagem contém o resultado da operação e é enviada nas seguintes circunstâncias:
        * Sempre que uma nova palavra-passe é submetida durante uma reposição de palavra-passe self-service do utilizador.
        * Sempre que uma nova palavra-passe é submetida durante uma operação de alteração de palavra-passe do utilizador.
        * Sempre que uma nova palavra-passe é submetida durante uma palavra-passe de utilizador iniciadas pelo administrador de reposição (apenas a partir de portais do administrador do Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Considerações de largura de banda e o tamanho da mensagem

O tamanho de cada uma das mensagens descrita anteriormente é normalmente em 1 KB. Mesmo sob cargas pesadas extremos, o próprio serviço de repetição de escrita de palavras-passe está a consumir alguns kilobits por segundo de largura de banda. Uma vez cada mensagem é enviada em tempo real, apenas quando uma operação de atualização de palavra-passe e, porque o tamanho da mensagem é por isso, pequeno, a utilização de largura de banda da capacidade de repetição de escrita é demasiado pequena para ter um impacto significativo.

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Reponha ou altere a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre licenciamento?](active-directory-passwords-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](active-directory-passwords-data.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política da SSPR?](active-directory-passwords-policy.md)
* [Como posso comunicar a atividade da SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](active-directory-passwords-how-it-works.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Ativar a repetição de escrita de palavras-passe no Azure AD Connect"
