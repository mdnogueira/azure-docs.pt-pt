---
title: 'FAQ: Do Azure AD SSPR | Microsoft Docs'
description: Perguntas mais frequentes sobre a palavra-passe self-service do Azure AD repor
services: active-directory
keywords: "Gestão de palavras-passe do Active Directory, gestão de palavras-passe, do Azure AD Self-repor a palavra-passe do serviço"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 112bfeea29eacd0615f1d134567ec52264318b5f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="password-management-frequently-asked-questions"></a>Gestão de palavras-passe perguntas mais frequentes

Seguem-se algumas perguntas mais frequentes (FAQ) para todos os aspetos relacionados com a reposição de palavra-passe.

Se tiver uma pergunta geral sobre o Azure Active Directory (Azure AD) e palavra-passe self-service reposição (SSPR) que não é atendida aqui, pode pedir da Comunidade para obter ajuda no [fórum do Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Membros da Comunidade do incluem engineers, gestores de produto, Most e para profissionais de TI.

Estas FAQ é dividida nas seguintes secções:

* [Registo de reposição de perguntas sobre a palavra-passe](#password-reset-registration)
* [Perguntas sobre a reposição de palavra-passe](#password-reset)
* [Perguntas sobre a alteração de palavra-passe](#password-change)
* [Perguntas sobre os relatórios de gestão de palavra-passe](#password-management-reports)
* [Perguntas sobre a repetição de escrita de palavras-passe](#password-writeback)

## <a name="password-reset-registration"></a>Registo de reposição de palavra-passe

* **P: os meus utilizadores registar os seus próprios dados de reposição de palavra-passe?**

  > **R:** Sim. Desde que a reposição de palavra-passe está ativada e são licenciados, os utilizadores podem aceder para o portal de registo de reposição (http://aka.ms/ssprsetup) de palavra-passe para registar as suas informações de autenticação. Os utilizadores também podem registar através do painel de acesso (http://myapps.microsoft.com). Para registar através do painel de acesso, que precisam para selecionar a imagem do perfil, selecione **perfil**e, em seguida, selecione o **registar para a reposição de palavra-passe** opção.
  >
  >
* **P: Definir dados de reposição de palavra-passe em nome os meus utilizadores?**

  > **R:** Sim, pode fazê-com o Azure AD Connect, PowerShell, o [portal do Azure](https://portal.azure.com), ou do Centro de administração do Office 365. Para obter mais informações, consulte [de dados utilizadas por palavra-passe self-service do Azure AD repor](active-directory-passwords-data.md).
  >
  >
* **P: sincronizar dados perguntas de segurança no local?**

  > **R:** não, isto não for possível hoje.
  >
  >
* **P: os meus utilizadores registar dados de forma a que outros utilizadores não podem ver estes dados?**

  > **R:** Sim. Quando os utilizadores para registar o portal de registo de reposição de dados utilizando a palavra-passe, os dados são guardados em campos de autenticação privadas que estão visíveis apenas para os administradores globais e o utilizador.
  >
  >
* **P: os meus utilizadores precisa de ser registados antes de poder utilizar a reposição de palavra-passe?**

  > **R:** Não. Se definir suficiente informações de autenticação em seu nome, os utilizadores não têm a registar. A reposição de palavra-passe funciona, desde que tenha formatado corretamente os dados armazenados em campos apropriados no diretório.
  >
  >
* **P: sincronizar ou definir o telefone de autenticação, o e-mail de autenticação ou a campos de telefone de autenticação alternativo em nome os meus utilizadores?**

  > **R:** não, isto não for possível hoje.
  >
  >
* **P: como é que o portal de registo determinar quais as opções para mostrar os meus utilizadores?**

  > **R:** mostra portal de registo de reposição da palavra-passe, apenas as opções que tiver ativado para os seus utilizadores. Estas opções encontram-se sob o **a política de reposição de palavra-passe do utilizador** secção do seu diretório **configurar** separador. Por exemplo, se não ativar a perguntas de segurança, em seguida, os utilizadores não são capazes de se registar para essa opção.
  >
  >
* **P: quando um utilizador é considerado registado?**

  > **R:** utilizadores são considerados registado para SSPR quando que tenham sido registadas, pelo menos, o **diversos métodos necessárias para repor** uma palavra-passe que definiu no [portal do Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Reposição de palavras-passe

* **P: quanto deve aguardar para receber um e-mail, SMS ou chamada telefónica de reposição de palavra-passe?**

  > **R:** mensagens de correio eletrónico, mensagens SMS, e as chamadas telefónicas deve chegam sob um minuto. As maiúsculas e minúsculas normal é 5 a 20 segundos.
    >Se não receber a notificação neste período de tempo:
        > * Verifique a pasta lixo.
        > * Verifique se o número ou o e-mail que está a ser contactado é aquele que esperava.
        > * Verifique se os dados de autenticação no diretório estão corretamente formatado, por exemplo, + 1 4255551234 ou  *user@contoso.com* . 
  >
  >
* **P: quais idiomas são suportados pela reposição de palavra-passe?**

  > **R:** IU, de repor a palavra-passe mensagens SMS e chamadas estão localizados nos mesmos idiomas de que são suportados no Office 365.
  >
  >
* **P: as partes da experiência de reposição de palavra-passe de obterem a imagem corporativa quando posso definir os itens de imagem corporativa organizacionais no meu diretório do separador Configurar?**

  > **R:** o portal de reposição de palavra-passe mostra o logótipo da sua organização e permite-lhe configurar a ligação "Contacte o administrador" para apontar para um e-mail personalizada ou um URL. Qualquer mensagem de correio eletrónico é enviada pelo reposição de palavra-passe inclui o logótipo da sua organização, cores e nome no corpo da mensagem de e-mail e personalizada das definições para que o nome específico.
  >
  >
* **P: como podem a informe os meus utilizadores sobre o que fazer a repor as palavras-passe?**

  > **R:** experimente algumas das sugestões no nosso [implementação SSPR](active-directory-passwords-best-practices.md#email-based-rollout) artigo.
  >
  >
* **P: Posso utilizar esta página de um dispositivo móvel?**

  > **R:** Sim, esta página funciona em dispositivos móveis.
  >
  >
* **P: suportam desbloqueio contas locais do Active Directory quando os utilizadores reponham as respetivas palavras-passe?**

  > **R:** Sim. Quando um utilizador repõe a palavra-passe, se tiver sido implementada a repetição de escrita de palavras-passe através do Azure AD Connect, conta de utilizador que está desbloqueada automaticamente quando poderem repor a palavra-passe.
  >
  >
* **P: como integrar a palavra-passe, repor diretamente na minha experiência do utilizador ambiente de trabalho início de sessão?**

  > **R:** se a um cliente do Azure AD Premium, pode instalar o Microsoft Identity Manager sem custos adicionais e implementar a solução de reposição de palavra-passe no local.
  >
  >
* **P: Definir perguntas de segurança diferentes para diferentes regiões?**

  > **R:** não, isto não for possível hoje.
  >
  >
* **P: o número de perguntas pode configurar para a opção de autenticação de perguntas de segurança?**

  > **R:** pode configurar até 20 perguntas de segurança personalizada no [portal do Azure](https://portal.azure.com).
  >
  >
* **P: quanto tempo podem perguntas de segurança ser?**

  > **R:** perguntas de segurança podem ser 3 a 200 carateres.
  >
  >
* **P: quanto tempo podem as respostas às perguntas de segurança ser?**

  > **R:** respostas podem ser 3 para 40 carateres.
  >
  >
* **P: são duplicadas respostas a perguntas de segurança rejeitadas?**

  > **R:** Sim, iremos rejeitar duplicadas respostas a perguntas de segurança.
  >
  >
* **P: um utilizador registar a mesma pergunta de segurança mais do que uma vez?**

  > **R:** Não. Depois de um utilizador se regista uma pergunta específica, estes não é possível registar para essa questão uma segunda vez.
  >
  >
* **P: é possível definir um limite mínimo de perguntas de segurança para o registo e reposição?**

  > **R:** Sim, um limite pode ser definido para o registo e outra para a reposição. Perguntas de segurança de três a cinco podem ser necessárias para o registo, perguntas e três a cinco podem ser necessárias para a reposição.
  >
  >
* **P: Posso configurado meu política para exigir que os utilizadores a utilizar perguntas de segurança para reposição, mas os administradores do Azure parecem ser configurado de forma diferente.**

  > **R:** este é o comportamento esperado. Microsoft impõe uma política de reposição de palavra-passe de porta de dois predefinido segura para qualquer função de administrador do Azure. Isto impede que os administradores utilizar perguntas de segurança. Pode encontrar mais informações sobre esta política no [políticas de palavra-passe e restrições no Azure Active Directory](active-directory-passwords-policy.md#administrator-password-policy-differences) artigo.
  >
  >
* **P: se um utilizador registou mais do que o número máximo de perguntas necessárias para repor, como são as perguntas de segurança selecionadas durante a reposição?**

  > **R:** *N* número de perguntas de segurança é seleccionado aleatoriamente entre o número total de perguntas a que um utilizador tiver registado para onde *N* é a quantidade que está definida para o  **Número de perguntas necessárias para repor** opção. Por exemplo, se um utilizador registou cinco perguntas de segurança, mas apenas três são necessárias para repor uma palavra-passe, três das cinco perguntas são selecionadas aleatoriamente e são apresentadas na reposição. Para evitar pergunta hammering, se o utilizador obtém as respostas às questões errado o processo de seleção é iniciado através de.
  >
  >
* **P: pode impedir que utilizadores várias tentativas para repor uma palavra-passe num curto período de tempo?**

  > **R:** Sim, existem funcionalidades de segurança incorporadas no protegê-lo contra utilização indevida de reposição de palavra-passe. Os utilizadores podem tentar apenas cinco tentativas de reposição de palavra-passe dentro de uma hora até estão bloqueadas durante 24 horas. Os utilizadores podem tentar validar um número de telefone só cinco vezes dentro de uma hora até estão bloqueadas durante 24 horas. Os utilizadores podem experimentar um método de autenticação única só cinco vezes dentro de uma hora até estão bloqueadas durante 24 horas.
  >
  >
* **P: quanto são o e-mail e o SMS Monouso códigos de acesso válidos?**

  > **R:** a duração de sessão para a reposição de palavra-passe é de 15 minutos. Desde o início da operação de reposição de palavra-passe, o utilizador tem de 15 minutos para repor a palavra-passe. Mensagem de e-mail e código de acesso Monouso SMS são inválidos após este período de tempo expira.
  >
  >
* **P: posso bloquear os utilizadores de repor a palavra-passe?**

  > **R:** Sim, se utilizar um grupo para ativar SSPR, pode remover um utilizador individual do grupo que permite aos utilizadores para repor a palavra-passe.
  >
  >

## <a name="password-change"></a>Alteração de palavra-passe

* **P: onde os meus utilizadores devem passar para alterar as palavras-passe?**

  > **R:** os utilizadores podem alterar as palavras-passe em qualquer lugar veem respetivo perfil imagem ou no ícone, como no canto superior direito dos respetivos [do Office 365](https://portal.office.com) portal ou [painel de acesso](https://myapps.microsoft.com) experiências. Os utilizadores podem alterar as palavras-passe do [página perfis do painel de acesso](https://account.activedirectory.windowsazure.com/r#/profile). Os utilizadores também podem ser-lhe pedidos para alterar as palavras-passe automaticamente na página de início de sessão do AD do Azure se as palavras-passe tem expirado. Por fim, os utilizadores podem procurar para o [portal de alteração de palavra-passe do Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) diretamente se pretenderem alterar as palavras-passe.
  >
  >
* **P: os meus utilizadores notificados no portal do Office quando expira a palavra-passe no local?**

  > **R:** Sim, isto é possível hoje se utilizar os serviços de Federação do Active Directory (AD FS). Se utilizar o AD FS, siga as instruções no [enviar afirmações de política de palavra-passe com o AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) artigo. Se utilizar a sincronização de hash de palavra-passe, isto não é possível hoje. Iremos não sincronizar as políticas de palavra-passe de diretórios no local, pelo que não é possível-na colocar as notificações de expiração para experiências de nuvem. Em ambos os casos, é também possível [notificar os utilizadores cujas palavras-passe está prestes a expirar através do PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **P: posso bloquear os utilizadores alterem a palavra-passe?**

  > **R:** para os utilizadores apenas na nuvem, as alterações de palavra-passe não podem ser bloqueadas. Para os utilizadores no local, pode definir o **utilizador não é possível alterar a palavra-passe** opção selecionada. Os utilizadores selecionados não é possível alterar a palavra-passe.
  >
  >

## <a name="password-management-reports"></a>Relatórios de gestão de palavra-passe

* **P: quanto tempo demora para os dados apareçam nos relatórios de gestão de palavra-passe?**

  > **R:** dados devem aparecer nos relatórios de gestão de palavra-passe em 5 a 10 minutos. Em alguns casos,-lo poderá demorar até uma hora a aparecer.
  >
  >
* **P: como pode filtrar os relatórios de gestão de palavra-passe?**

  > **R:** para filtrar os relatórios de gestão de palavra-passe, selecione a Lupa pequena para a direita Alpine das etiquetas de coluna, perto da parte superior do relatório. Se pretender mais rico de filtragem, pode transferir o relatório para Excel e criar uma tabela dinâmica.
  >
  >
* **P: qual é o número máximo de eventos que são armazenados nos relatórios de gestão de palavra-passe?**

  > **R:** até palavra-passe 75,000 eventos de registo de reposição de palavra-passe de reposição ou são armazenados nos relatórios de gestão de palavra-passe, a expansão novamente as far as 30 dias. Estamos a trabalhar para expandir este número para incluir os eventos mais.
  >
  >
* **P: até que ponto novamente os relatórios de gestão de palavra-passe aceda?**

  > **R:** a gestão de palavras-passe relatórios Mostrar operações que ocorreram nos últimos 30 dias. Por agora, se necessário arquivar estes dados, pode transferir os relatórios periodicamente e guardá-las num local separado.
  >
  >
* **P: existe um número máximo de linhas que pode ser apresentado nos relatórios de gestão de palavra-passe?**

  > **R:** Sim. Um máximo de 75,000 linhas pode aparecer em qualquer um dos relatórios de gestão de palavra-passe, se estes são apresentados na IU ou são transferidas.
  >
  >
* **P: existe uma API para aceder a reposição de palavra-passe ou o registo de dados de relatórios?**

  > **R:** Sim. Para saber como pode aceder a reposição de palavra-passe fluxo de dados de relatórios, consulte [aprender a aceder a reposição de palavra-passe através de programação de eventos de relatório](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Repetição de escrita de palavras-passe

* **P: como funciona a repetição de escrita de palavras-passe nos bastidores?**

  > **R:** , consulte o artigo [como funciona a repetição de escrita de palavras-passe](active-directory-passwords-writeback.md) para obter uma explicação sobre o que acontece quando ativar a repetição de escrita de palavras-passe e a forma como os dados fluem através do sistema novamente para o seu ambiente no local.
  >
  >
* **P: quanto repetição de escrita de palavras-passe necessário trabalhar? É não existe uma sincronização atrasar, tal como acontece com sincronização de hash de palavra-passe?**

  > **R:** repetição de escrita de palavras-passe é instantânea. É um pipeline síncrono que funciona de forma fundamentalmente diferente do que a sincronização de hash de palavra-passe. Repetição de escrita de palavras-passe permite aos utilizadores obter comentários em tempo real sobre o sucesso da respetiva reposição de palavra-passe ou altere a operação. O tempo médio para uma repetição de escrita com êxito de uma palavra-passe está em 500 ms.
  >
  >
* **P: se a minha conta local é desativada, como é meu acesso afetados e a conta na nuvem?**

  > **R:** se o seu ID de local é desativada, o seu ID de nuvem e acesso também serão desativadas no próximo intervalo de sincronização através do Azure AD Connect. Por predefinição, esta sincronização é a cada 30 minutos.
  >
  >
* **P: se a minha conta local é restrita por uma política de palavra-passe do Active Directory no local, SSPR obedecer esta política quando posso alterar a minha palavra-passe?**

  > **R:** Sim, SSPR depende e rege-se pela política de palavra-passe do Active Directory no local. Esta política inclui a política de palavra-passe de domínio do Active Directory típica, bem como as políticas de palavra-passe detalhada e definidas são direcionadas para um utilizador.
  >
  >
* **P: quais são os tipos de contas de repetição de escrita de palavras-passe funciona?**

  > **R:** palavra-passe de repetição de escrita funciona para Federado e hash de palavra-passe sincronizados os utilizadores.
  >
  >
* **P: repetição de escrita de palavras-passe impor políticas de palavra-passe do meu domínio?**

  > **R:** Sim. Repetição de escrita de palavras-passe impõe antiguidade de palavra-passe, histórico, complexidade, os filtros e quaisquer outra restrição que poderá colocar no local as palavras-passe no seu domínio local.
  >
  >
* **P: é a repetição de escrita de palavras-passe segura?  Como posso ser se que posso não ilícito?**

  > **R:** Sim, a repetição de escrita de palavras-passe é segura. Para ler mais sobre as quatro camadas de segurança implementados pelo serviço de repetição de escrita de palavras-passe, consulte o [modelo de segurança de repetição de escrita de palavras-passe](active-directory-passwords-writeback.md#password-writeback-security-model) secção o [descrição geral de repetição de escrita de palavras-passe](active-directory-passwords-writeback.md) artigo.
  >
  >

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Repor ou alterar a palavra-passe](active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](active-directory-passwords-reset-register.md)
* [Tem uma pergunta licenciamento?](active-directory-passwords-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](active-directory-passwords-data.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política da SSPR?](active-directory-passwords-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](active-directory-passwords-writeback.md)
* [Como posso comunicar a atividade da SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](active-directory-passwords-how-it-works.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
