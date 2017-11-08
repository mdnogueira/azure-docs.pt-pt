---
title: "Azure AD SSPR com repetição de escrita de palavras-passe | Microsoft Docs"
description: "Utilização do Azure AD e o Azure AD Connect para palavras-passe de repetição de escrita ao diretório no local"
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
ms.openlocfilehash: 9733774570f3148e0092f42c1321b4fac1c80b54
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="password-writeback-overview"></a>Descrição geral de repetição de escrita de palavras-passe

Repetição de escrita de palavras-passe permite-lhe configurar o Azure AD para escrever as palavras-passe do Active Directory no local. Remove a necessidade de configurar e gerir uma solução de reposição de palavra-passe self-service complicada no local e fornece uma maneira conveniente para os seus utilizadores para repor as respetivas palavras-passe no local onde quer que estejam baseado na nuvem. Repetição de escrita de palavras-passe é um componente do [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) que pode ser ativada e utilizado por subscritores atuais do Premium [edições do Azure Active Directory](active-directory-editions.md).

Repetição de escrita de palavras-passe fornece as seguintes funcionalidades:

* **Zero atraso comentários** -repetição de escrita de palavras-passe é uma operação síncrona. Os utilizadores são notificados imediatamente se a palavra-passe não foi possível corresponder a uma política ou não foi possível repor ou alterados por qualquer motivo.
* **Suporta a reposição de palavras-passe para utilizadores através do AD FS ou outras tecnologias de Federação** -com repetição de escrita de palavras-passe, desde que as contas de utilizador federado que são sincronizadas no seu inquilino do Azure AD, são capazes de gerir os respetivos no local as palavras-passe do AD da nuvem.
* **Suporta a reposição de palavras-passe para utilizadores que utilizam [sincronização de hash de palavra-passe](./connect/active-directory-aadconnectsync-implement-password-synchronization.md)**  - quando o serviço de reposição de palavra-passe Deteta que uma conta de utilizador sincronizadas esteja ativada para sincronização de hash de palavra-passe, repomos no local ambas desta conta cloud e palavra-passe em simultâneo.
* **Suporta a alteração de palavras-passe do painel de acesso e do Office 365** - quando federado ou utilizadores de palavra-passe sincronizada são fornecidos para alterar as expirado ou não expirou palavras-passe, podemos repetição de escrita dessas palavras-passe para o ambiente do AD local.
* **Suporta a escrever novamente as palavras-passe quando um administrador repô-los a partir do portal do Azure** - sempre que um administrador repõe a palavra-passe de um utilizador no [portal do Azure](https://portal.azure.com), se estiver federado que o utilizador ou palavra-passe sincronizado, iremos definir a palavra-passe o administrador seleciona no seu AD local, bem. Isto não é atualmente suportado no Portal de administração do Office.
* **Impõe no local políticas de palavra-passe do AD** - quando um utilizador repõe a palavra-passe, mas certifique-se de que cumpre no local política AD antes de consolidá-lo a esse diretório. Isto inclui histórico, complexidade, idade, filtros de palavra-passe e outras restrições de palavra-passe que definiu no seu AD local.
* **Não requer quaisquer regras de firewall de entrada** -repetição de escrita de palavras-passe utiliza um reencaminhamento do Service Bus do Azure como um canal de comunicação subjacente, que significa que não têm de abrir nenhuma porta de entrada na sua firewall para esta funcionalidade funcionar.
* **Não é suportada para contas de utilizador existem no âmbito de grupos protegidos no Active Directory no local** – para obter mais informações sobre grupos protegidos, consulte [protegidos contas e grupos no Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Como funciona a repetição de escrita de palavras-passe

Quando um hash de palavra-passe ou federado utilizador sincronizado vem reponham ou alterem a palavra-passe na nuvem, ocorre o seguinte:

1. -Verifique o tipo de palavra-passe tem do utilizador.
    * Se detetarmos a palavra-passe é gerida no local
        * Iremos verificar se o serviço de repetição de escrita está a funcionar e em execução, caso seja, permitem ao utilizador continuar
        * Se o serviço de repetição de escrita não está ativo, estamos indicar ao utilizador que a palavra-passe não pode ser reposta agora
2. Em seguida, o utilizador transmite as portas de autenticação adequado e atinge o ecrã reposição de palavra-passe.
3. O utilizador seleciona uma nova palavra-passe e confirma-lo.
4. Após clicar em submeter, é possível encriptar a palavra-passe de texto simples com uma chave simétrica que foi criada durante o processo de configuração de repetição de escrita.
5. Depois da palavra-passe de encriptação, iremos incluí-la num payload que é enviado através de um canal HTTPS para o reencaminhamento de barramento de serviço inquilino específico (que também configuramos para si durante o processo de configuração de repetição de escrita). Este reencaminhamento está protegido por uma palavra-passe gerada aleatoriamente que sabe apenas a instalação no local.
6. Quando a mensagem atingirem o barramento de serviço, o ponto final de reposição de palavra-passe reativado automaticamente e vê que tem um pedido de reposição pendentes.
7. O serviço, em seguida, procura o utilizador em questão utilizando o atributo âncora de nuvem. Para esta pesquisa com êxito:

    * O objeto de utilizador tem de existir no espaço de conector AD
    * O objeto de utilizador têm de estar associado ao objeto de MV correspondente
    * O objeto de utilizador têm de estar associado ao objeto de conector AAD correspondente.
    * A ligação do objeto de conector do AD para MV tem de ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` na ligação. <br> <br>
    Quando a chamada é apresentada da nuvem, o motor de sincronização utiliza o atributo de cloudAnchor para procurar o objeto de espaço de conector AAD, a ligação de volta para o objeto de MV, de forma e, em seguida, segue-se a ligação para o objecto do AD. Porque podem existir vários objetos do AD (multifloresta) para o mesmo utilizador, o motor de sincronização depende o `Microsoft.InfromADUserAccountEnabled.xxx` ligação escolha correto.

    > [!Note]
    > Como resultado esta lógica, o Azure AD Connect tem de ser capaz de comunicar com o emulador PDC para repetição de escrita de palavras-passe para trabalhar. Se precisar de ativar esta opção manualmente, pode ligar o Azure AD Connect para o emulador PDC clicando no **propriedades** do conector de sincronização do Active Directory, em seguida, selecionar **configurar diretório as partições**. A partir daí, procure a secção **definições de ligação do controlador de domínio** e marque a caixa intitulada **utilizar apenas controladores de domínio preferencial**. Mesmo que o DC preferencial não é um emulador PDC, o Azure AD Connect irá tentar estabelecer ligação com o PDC para repetição de escrita de palavras-passe.

8. Assim que a conta de utilizador for encontrada, vamos tentar repor a palavra-passe diretamente na floresta do AD adequada.
9. Se a operação de definição de palavra-passe for bem sucedida, iremos indicar ao utilizador que a palavra-passe foi alterada.
    > [!NOTE]
    > No caso de quando a palavra-passe do utilizador está sincronizado com o Azure AD com sincronização de palavras-passe, há a possibilidade de que a política de palavra-passe no local é mais fraco que a política de palavra-passe da nuvem. Neste caso, iremos ainda impor que a política no local é e, em vez disso, permitir a sincronização de hash de palavra-passe para sincronizar o hash dessa palavra-passe. Isto garante que a política no local é imposta na nuvem, no matter se utilizar a sincronização de palavra-passe ou Federação para fornecer o início de sessão único.

10. Se a palavra-passe definida falha da operação, iremos devolver o erro para o utilizador e dar-lhes tente novamente.
    * A operação poderá falhar devido à seguinte
        * O serviço foi pendente
        * A palavra-passe que selecionou não cumpria as políticas da organização
        * Não foi possível localizar o utilizador no local AD

    Estamos a ter uma mensagem específica para muitos nestes casos e indicar ao utilizador que eles podem fazer para resolver o problema.

## <a name="configuring-password-writeback"></a>Configurar a repetição de escrita de palavras-passe

Recomendamos que utilize a funcionalidade de atualização automática do [do Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) se quiser utilizar a repetição de escrita de palavras-passe.

O DirSync e o Azure AD Sync já não são suportado meio de ativar a repetição de palavras-passe do artigo [atualizar do DirSync e Azure AD Sync](connect/active-directory-aadconnect-dirsync-deprecated.md) tem mais informações para ajudar a transição.

Os passos abaixo assumem que já tenha configurado o Azure AD Connect no seu ambiente utilizando o [Express](./connect/active-directory-aadconnect-get-started-express.md) ou [personalizada](./connect/active-directory-aadconnect-get-started-custom.md) definições.

1. Para configurar e ativar o registo de repetição de escrita de palavras-passe para o servidor do Azure AD Connect e iniciar o **do Azure AD Connect** Assistente de configuração.
2. No ecrã de boas-vindas, clique em **configurar**.
3. No ecrã tarefas adicionais, clique em **personalizar as opções de sincronização** e, em seguida, escolha **seguinte**.
4. No ecrã ligar ao Azure AD, introduza uma credencial de Administrador Global e escolha **seguinte**.
5. Ligar a diretórios e o domínio e a UO filtragem ecrãs, pode optar **seguinte**.
6. No ecrã de funcionalidades opcionais, selecione a caixa junto **repetição de escrita de palavras-passe** e clique em **seguinte**.
   ![Ativar a repetição de escrita de palavras-passe no Azure AD Connect][Writeback]
7. O ecrã preparado para configurar, clique em **configurar** e aguarde pela conclusão do processo.
8. Quando vir configuração concluir, clique em **saída**

Para tarefas de resolução de problemas comuns relacionadas com a repetição de escrita de palavras-passe, consulte a secção [resolver problemas de repetição de escrita de palavras-passe](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) no nosso artigo de resolução de problemas.

## <a name="active-directory-permissions"></a>Permissões do Active Directory

A conta especificada no utilitário do Azure AD Connect tem de ter Repor palavra-passe, alterar palavra-passe, permissões de escrita lockoutTime e permissões de escrita no pwdLastSet, expandido direitos no objeto raiz de **cada domínio** em que floresta **ou** no utilizador UOs que pretende estar no âmbito de SSPR.

Se não tiver a certeza de que conta refere-se acima para, abra a IU de configuração do Azure Active Directory Connect e clique na opção de configuração atual do Vista. A conta que tem de adicionar permissão está listado em "Diretórios sincronizados"

Definir estas permissões permite que a conta de serviço MA para cada floresta gerir palavras-passe em nome de contas de utilizador nessa floresta. **Se não atribuir estas permissões, em seguida, apesar de repetição de escrita parece estar configurada corretamente, os utilizadores encontrarem erros quando tentarem gerir as respetivas palavras-passe no local a partir da nuvem.**

> [!NOTE]
> Pode demorar até uma hora ou mais até que estas permissões sejam replicadas em todos os objetos no seu diretório.
>

Para configurar as permissões adequadas para a palavra-passe repetição de escrita

1. Abra computadores e utilizadores do Active Directory com uma conta que possua as permissões de administração do domínio adequado
2. Menu Ver, certifique-se que funcionalidades avançadas está ativada
3. No painel esquerdo, faça duplo clique o objeto que representa a raiz do domínio e escolha Propriedades
    * Clique no separador Segurança
    * Em seguida, clique em Avançadas.
4. No separador permissões, clique em Adicionar
5. Escolha a conta que as permissões estão a ser aplicadas a (a partir do programa de configuração do Azure AD Connect)
6. O aplica-se a lista pendente caixa Selecionar objetos de utilizadores descendentes
7. Em permissões, marque as caixas para o seguinte
    * Unexpire-palavra-passe
    * Repor palavra-passe
    * Alterar palavra-passe
    * Escrever lockoutTime
    * Escrever pwdLastSet
8. Clicar aplicar/OK para aplicar e sair quaisquer caixas de diálogo aberta.

## <a name="licensing-requirements-for-password-writeback"></a>Requisitos de licenciamento para a repetição de escrita de palavras-passe

Para informações sobre o licenciamento, consulte [licenças necessárias para a repetição de escrita de palavras-passe](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) ou os seguintes sites

* [Site de preços do Active Directory do Azure](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Proteger a empresa produtiva](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-supported-for-password-writeback"></a>Modos de autenticação suportados para a repetição de escrita de palavras-passe no local

Repetição de escrita de palavras-passe funciona para os seguintes tipos de palavra-passe de utilizador:

* **Os utilizadores apenas na nuvem**: repetição de escrita de palavras-passe não se aplica esta situação, porque não há nenhuma palavra-passe de no local
* **Os utilizadores sincronizados de palavra-passe**: repetição de escrita de palavras-passe suportada
* **Os utilizadores federados**: repetição de escrita de palavras-passe suportada
* **Os utilizadores de autenticação pass-through**: repetição de escrita de palavras-passe suportada

### <a name="user-and-admin-operations-supported-for-password-writeback"></a>Operações de utilizador e administrador suportadas para a repetição de escrita de palavras-passe

As palavras-passe são gravadas nas seguintes situações:

* **Operações suportadas do utilizador final**
  * Operação de palavra-passe de alterar quaisquer voluntário pelo utilizador final de self-service
  * Nenhuma operação de palavra-passe de alteração de imposição self-service de utilizador final (por exemplo, a expiração de palavra-passe)
  * Qualquer utilizador final personalizada de palavra-passe repor provenientes de [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com)
* **Operações suportadas de administrador**
  * Operação de palavra-passe de alterar quaisquer voluntário administrador de self-service
  * Nenhuma operação de palavra-passe de alteração de imposição de self-service de administrador (por exemplo, a expiração de palavra-passe)
  * Qualquer administrador personalizada de palavra-passe repor provenientes de [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com)
  * Qualquer palavra-passe de utilizador final iniciadas pelo administrador de reposição do [portal clássico do Azure](https://manage.windowsazure.com)
  * Qualquer palavra-passe de utilizador final iniciadas pelo administrador de reposição do [portal do Azure](https://portal.azure.com)

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>Operações de utilizador e administrador não suportadas para a repetição de escrita de palavras-passe

Palavras-passe são **não** gravados em qualquer uma das seguintes situações:

* **Operações do utilizador final não suportadas**
  * Qualquer utilizador final repor os seus próprios palavra-passe utilizando o PowerShell v1, v2 ou o AD Graph API do Azure
* **Operações de administrador não suportadas**
  * Qualquer palavra-passe de utilizador final iniciadas pelo administrador de reposição do [portal de gestão do Office](https://portal.office.com)
  * Qualquer palavra-passe de utilizador final iniciadas pelo administrador de reposição do PowerShell v1, v2 ou a AD Graph API do Azure

Enquanto que estamos a trabalhar para remover estas limitações, não temos uma linha de tempo específica que partilhamos ainda.

## <a name="password-writeback-security-model"></a>Modelo de segurança de repetição de escrita de palavras-passe

Repetição de escrita de palavras-passe é um serviço altamente seguro.  Para garantir que as suas informações estão protegidas, ativamos a um modelo de segurança de 4 camadas que é descrito abaixo.

* **Reencaminhamento de barramento de serviço de inquilino específico**
  * Quando configurar o serviço, iremos definir um reencaminhamento de barramento de serviço de inquilino específico que está protegido por uma gerado aleatoriamente palavra-passe segura que Microsoft nunca tenha acesso à.
* **Chave de encriptação da palavra-passe bloqueado para baixo e criptograficamente forte,**
  * Depois de criado o reencaminhamento do service bus, criamos uma chave simétrica forte que utilizamos para encriptar a palavra-passe porque se trata através da transmissão. Esta chave se encontra apenas no arquivo de secreta da sua empresa na nuvem, que é fortemente bloqueado e auditado, tal como qualquer palavra-passe no diretório.
* **TLS padrão da indústria**
  1. Quando uma palavra-passe reponham ou alterem operação ocorre na nuvem, iremos assumir a palavra-passe de texto simples e encriptá-lo com a chave pública.
  2. Vamos, em seguida, coloque que numa mensagem de HTTPS, que é enviada através de um canal encriptado utilizando certificados SSL da Microsoft para o reencaminhamento do service bus.
  3. Depois de chega a mensagem para o Service Bus, o agente no local reativado e autentica o Service Bus utilizando a palavra-passe segura gerada anteriormente.
  4. Agente local escolherá a mensagem encriptada, desencripta-la utilizando a chave privada que são gerados.
  5. Em seguida, tenta agente no local definir a palavra-passe através da API de SetPassword do AD DS.
     * Este passo é o que permite-nos para impor a política de palavra-passe AD no local (complexidade, idade, histórico, filtros, etc.) na nuvem.
* **Políticas de expiração da mensagem** 
  * Se a mensagem se encontre no Service Bus, porque o serviço no local não está disponível, será de tempo e ser removida após alguns minutos para aumentar a segurança ainda mais.

### <a name="password-writeback-encryption-details"></a>Detalhes de encriptação de repetição de escrita de palavras-passe

Os passos de encriptação um pedido de reposição de palavra-passe atravessa depois de um utilizador submete, antes que chega no seu ambiente no local, para garantir a fiabilidade do serviço máximo e segurança são descritas abaixo.

* **Passo 1 - encriptação da palavra-passe com chave RSA de 2048 bits** - depois de um utilizador submete uma palavra-passe para ser gravados no local, em primeiro lugar, a palavra-passe submetido em si é encriptada com uma chave RSA de 2048 bits.
* **Passo 2 - encriptação a nível do pacote com o AES GCM** -, em seguida, o pacote completo (palavra-passe + metadados necessários) é encriptado com AES-GCM. Esta encriptação impede que qualquer pessoa com acesso direto para o canal de ServiceBus subjacente de visualização/adulteração com o conteúdo.
* **Passo 3 - todas as comunicações ocorre através de TLS / SSL** -todas as comunicações com ServiceBus ocorre um canal SSL/TLS. Esta encriptação protege o conteúdo de terceiros não autorizados.
* **Rollover de chave automático a cada seis meses** - a cada 6 meses, ou sempre que a repetição de escrita de palavras-passe está desativada / reativada no Azure AD Connect, iremos automaticamente o rollover de todas as chaves para garantir a segurança do serviço máximo e segurança.

### <a name="password-writeback-bandwidth-usage"></a>Utilização de largura de banda de repetição de escrita de palavras-passe

Repetição de escrita de palavras-passe é um serviço de pouca largura de banda que envia pedidos para o agente no local apenas nas seguintes circunstâncias:

1. Duas mensagens enviadas quando ativar ou desativar a funcionalidade através do Azure AD Connect.
2. Uma mensagem é enviada uma vez a cada cinco minutos como um heartbeat do serviço para, desde que o serviço está em execução.
3. Duas mensagens são enviadas a cada hora, uma nova palavra-passe é submetida
    * Primeira mensagem, como um pedido para executar a operação
    * Segunda mensagem, que contém o resultado da operação e é enviada nas seguintes circunstâncias:
        * Sempre que uma nova palavra-passe é submetida durante uma reposição de palavra-passe self-service do utilizador.
        * Sempre que uma nova palavra-passe é submetida durante uma operação de alteração de palavra-passe do utilizador.
        * Sempre que uma nova palavra-passe é submetida durante uma palavra-passe de utilizador iniciadas pelo administrador de reposição (a partir de portais apenas de administrador do Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Considerações de largura de banda e o tamanho da mensagem

O tamanho de cada uma das mensagens descrita acima é normalmente em 1 kb, mesmo sob cargas pesadas Alpine, o próprio serviço de repetição de escrita de palavras-passe está a consumir alguns kilobits por segundo de largura de banda. Uma vez que cada mensagem é enviada em tempo real, apenas quando uma operação de atualização de palavra-passe e, uma vez que o tamanho da mensagem é por isso, pequeno, a utilização de largura de banda da capacidade de repetição de escrita é efetivamente demasiado pequena para qualquer impacto mensuráveis real.

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Reponha ou altere a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre Licenciamento?](active-directory-passwords-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](active-directory-passwords-data.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política da SSPR?](active-directory-passwords-policy.md)
* [Como posso comunicar a atividade da SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](active-directory-passwords-how-it-works.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Ativar a repetição de escrita de palavras-passe no Azure AD Connect"
