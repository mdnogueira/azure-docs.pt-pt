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
ms.openlocfilehash: 53075d20aff073ff46dcd6dccaefea5fc8ec3483
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="password-management-frequently-asked-questions"></a>Gestão de palavras-passe perguntas mais frequentes

Seguem-se algumas perguntas mais frequentes para todos os aspetos relacionados com a reposição de palavra-passe.

Se tiver uma pergunta geral acerca do Azure AD e a palavra-passe self-service reposição, o que não é atendida aqui, pode pedir da Comunidade para obter assistência sobre a [fóruns do Azure Ad](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Membros da Comunidade do incluem Engineers, gestores de produto, Most e fellow profissionais de TI.

Estas FAQ é dividida nas seguintes secções:

* [**Perguntas sobre o registo de reposição de palavra-passe**](#password-reset-registration)
* [**Perguntas sobre a reposição de palavra-passe**](#password-reset)
* [**Perguntas sobre a alteração de palavra-passe**](#password-change)
* [**Perguntas sobre a gestão de palavra-passe de relatórios**](#password-management-reports)
* [**Perguntas sobre a repetição de escrita de palavras-passe**](#password-writeback)

## <a name="password-reset-registration"></a>Registo de reposição de palavra-passe

* **P: os meus utilizadores registar os seus próprios dados de reposição de palavra-passe?**

  > **R:** Sim, desde que a reposição de palavra-passe está ativada e são licenciados, podem aceder ao portal do registo de reposição de palavra-passe em http://aka.ms/ssprsetup para registar as suas informações de autenticação. Os utilizadores também podem registar-se ao aceder ao painel de acesso em http://myapps.microsoft.com, clicando no separador de perfil e clicando em registar para a opção de reposição de palavra-passe.
  >
  >
* **P: Definir dados de reposição de palavra-passe em nome os meus utilizadores?**

  > **R:** Sim, pode fazê-com o Azure AD Connect, PowerShell, o [portal do Azure](https://portal.azure.com), ou o portal de administração do Office. Para obter mais informações, consulte o artigo [dados utilizados pelo Azure AD Self-Service palavra-passe reposta](active-directory-passwords-data.md).
  >
  >
* **P: sincronizar dados perguntas de segurança no local?**

  > **R:** não for possível hoje.
  >
  >
* **P: os meus utilizadores registar dados de forma a que outros utilizadores não podem ver estes dados?**

  > **R:** Sim, quando os utilizadores para registar dados utilizando a palavra-passe de reposição de Portal de registo é guardado em campos de autenticação privada que apenas estão visíveis por administradores globais e o utilizador.
  >
  >
* **P: os meus utilizadores precisa de ser registados antes de poder utilizar a reposição de palavra-passe?**

  > **R:** não, se definir suficiente informações de autenticação em seu nome, os utilizadores têm de registar. A reposição de palavra-passe funciona, desde que tenha formatado corretamente os dados armazenados em campos apropriados no diretório.
  >
  >
* **P: sincronizar ou definir o telefone de autenticação, a autenticação de E-Mail ou telefone de autenticação alternativo campos em nome os meus utilizadores?**

  > **R:** não for possível hoje.
  >
  >
* **P: como é que o portal de registo saber quais as opções para mostrar os meus utilizadores?**

  > **R:** o portal de registo de reposição de palavra-passe apenas mostra as opções que tiver ativado para os seus utilizadores. Estas opções encontram-se na secção de política de reposição de palavra-passe de utilizador do separador de configuração do seu diretório. Por exemplo, isto significa que se não ativar a perguntas de segurança, em seguida, os utilizadores não são capazes de se registar para essa opção.
  >
  >
* **P: quando um utilizador é considerado registado?**

  > **R:** utilizadores são considerados registado para SSPR quando que tenham sido registadas, pelo menos, o **diversos métodos necessárias para repor** que definiu [portal do Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Reposição de palavras-passe

* **P: quanto deve aguardar para receber um e-mail, SMS ou chamada telefónica de reposição de palavra-passe?**

  > **R:** correio eletrónico, mensagens SMS, e as chamadas telefónicas deve chegam num minuto, com as maiúsculas e minúsculas normal que está a ser 5-20 segundos.
    >Se receber a notificação este período de tempo:
        > * Verifique a pasta lixo.
        > * Verifique o número ou o e-mail que está a ser contactado é aquele que esperava.
        > * Verifique os dados de autenticação no diretório estão formatados corretamente.
                >     * Exemplo: "+ 1 4255551234" ou "user@contoso.com"
  >
  >
* **P: quais idiomas são suportados pela reposição de palavra-passe?**

  > **R:** IU, de repor a palavra-passe mensagens SMS e chamadas estão localizados nos mesmos idiomas de que são suportados no Office 365.
  >
  >
* **P: as partes da experiência de reposição de palavra-passe de obterem a imagem corporativa quando posso definida organizacional de imagem corporativa no meu diretório do separador Configurar?**

  > **R:** o portal de reposição de palavra-passe mostra o logótipo organizacional e permite-lhe configurar o contacto a ligação de administrador apontar para um e-mail personalizada ou um URL. Qualquer mensagem de e-mail que é enviada pela reposição de palavra-passe inclui o logótipo da sua organização, cores, um nome no corpo da mensagem de e-mail e personalizados do nome.
  >
  >
* **P: como podem a informe os meus utilizadores sobre o que fazer a repor as palavras-passe?**

  > **R:** experimente algumas das sugestões no nosso [artigo sobre implementação de SSPR](active-directory-passwords-best-practices.md#email-based-rollout)
  >
  >
* **P: Posso utilizar esta página de um dispositivo móvel?**

  > **R:** Sim, esta página funciona em dispositivos móveis.
  >
  >
* **P: suportam desbloqueio contas do local active directory quando os utilizadores reponham as respetivas palavras-passe?**

  > **R:** Sim, quando um utilizador repõe a palavra-passe e a repetição de escrita de palavras-passe tiver sido implementada com o Azure AD Connect, essa conta de utilizador estiver desbloqueada automaticamente ao repor a palavra-passe.
  >
  >
* **P: como integrar a palavra-passe, repor diretamente na minha experiência do utilizador ambiente de trabalho início de sessão?**

  > **R:** se for um cliente do Azure AD Premium, pode instalar o Microsoft Identity Manager sem custos adicionais e implementar a solução de reposição de palavra-passe no local para cumprir este requisito.
  >
  >
* **P: Definir perguntas de segurança diferentes para diferentes regiões?**

  > **R:** não for possível hoje.
  >
  >
* **P: o número de perguntas, configurar para a opção de autenticação de perguntas de segurança?**

  > **R:** pode configurar até 20 perguntas de segurança personalizada no [portal do Azure](https://portal.azure.com).
  >
  >
* **P: quanto tempo poderá perguntas de segurança?**

  > **R:** perguntas de segurança podem ter entre 3 e 200 carateres.
  >
  >
* **P: quanto tempo poderá obter respostas a perguntas de segurança?**

  > **R:** respostas podem ser 3 para 40 carateres.
  >
  >
* **P: são duplicadas respostas a perguntas de segurança rejeitadas?**

  > **R:** Sim, iremos rejeitar duplicadas respostas a perguntas de segurança.
  >
  >
* **P: Maio um utilizador registar a mesma pergunta de segurança mais do que uma vez?**

  > **R:** não, assim que um utilizador se regista uma pergunta específica, podem não se registar para essa questão uma segunda vez.
  >
  >
* **P: é possível definir um limite mínimo de perguntas de segurança para o registo e reposição?**

  > **R:** Sim, um limite pode ser definido para o registo e outra para a reposição. perguntas de segurança de 3 a 5 podem ser necessárias para o registo e 3 a 5 poderá ser necessário para a reposição.
  >
  >
* **P: Posso configurado meu política para exigir que os utilizadores a utilizar perguntas de segurança para reposição mas os administradores do Azure parecem ser configurado de forma diferente.**

  > **R:** este é o comportamento esperado. Microsoft impõe uma política de reposição de palavra-passe duas porta predefinida segura para qualquer função de administrador do Azure. Isto desativa a administradores de utilizar perguntas de segurança. Podem encontrar mais informações sobre esta política no artigo [políticas de palavra-passe e restrições no Azure Active Directory](active-directory-passwords-policy.md#administrator-password-policy-differences)
  >
  >
* **P: se um utilizador registou mais do que o número máximo de perguntas necessárias para repor, como são perguntas de segurança selecionadas durante a reposição?**

  > **R:** perguntas de segurança de N são selecionadas aleatoriamente entre o número total de perguntas a que um utilizador tiver registado para, em que N é o **número de perguntas necessárias para repor**. Por exemplo, se um utilizador tiver 5 perguntas de segurança registadas, mas apenas 3 são necessárias para repor, 3 da 5 estão selecionados aleatoriamente e apresentadas na reposição. Se o utilizador obtém as respostas às questões errado, o processo de seleção reoccurs impedir a pergunta hammering.
  >
  >
* **P: pode impedir que os utilizadores tentam demasiadas vezes num curto período de tempo de reposição de palavra-passe?**

  > **R:** Sim, existem funcionalidades de segurança incorporadas para proteger contra utilização indevida de reposição de palavra-passe. Os utilizadores só podem tentar 5 tentativas de reposição de palavra-passe dentro de uma hora antes de a ser bloqueada durante 24 horas. Os utilizadores só podem tentar validar um número de telefone 5 vezes dentro de uma hora antes de a ser bloqueada durante 24 horas. Os utilizadores apenas tente um método de autenticação única 5 vezes dentro de uma hora antes de a ser bloqueada durante 24 horas.
  >
  >
* **P: para o período de tempo é de e-mail e o código de acesso Monouso SMS válido?**

  > **R:** a duração de sessão para a reposição de palavra-passe é de 15 minutos. A partir do início da operação de reposição de palavra-passe, o utilizador tem de 15 minutos para repor a palavra-passe. Mensagem de e-mail e código de acesso Monouso SMS são inválidos após este período de tempo expira.
  >
  >

## <a name="password-change"></a>Alteração de palavra-passe

* **P: onde os meus utilizadores devem passar para alterar as palavras-passe?**

  > **R:** os utilizadores podem alterar as palavras-passe em qualquer lugar veem o respetivo perfil imagem ou no ícone (tal como no canto superior direito dos respetivos [do Office 365](https://portal.office.com) ou [painel de acesso](https://myapps.microsoft.com) experiências. Os utilizadores podem alterar as palavras-passe do [página de perfis do painel de acesso](https://account.activedirectory.windowsazure.com/r#/profile). Os utilizadores também podem ser-lhe pedidos para alterar as palavras-passe automaticamente no ecrã de início de sessão do AD do Azure se as palavras-passe tem expirado. Por fim, os utilizadores podem navegar para o [Portal de alteração de palavra-passe do Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) diretamente se pretenderem alterar as palavras-passe.
  >
  >
* **P: os meus utilizadores notificados no Portal do Office quando expira a palavra-passe no local?**

  > **R:** isto é possível hoje se estiver a utilizar o AD FS ao seguir as instruções aqui: [enviar afirmações de política de palavra-passe com ADFS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396). Se estiver a utilizar sincronização de hash de palavra-passe, isto não é possível hoje. Isto acontece porque não estamos sincronizar a políticas de palavra-passe no local, pelo que não é possível-na colocar as notificações de expiração para experiências de nuvem. Em ambos os casos, é também possível [notificar os utilizadores cujas palavras-passe está prestes a expirar utilizando o PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >

## <a name="password-management-reports"></a>Relatórios de gestão de palavra-passe

* **P: quanto tempo demora para os dados apareçam nos relatórios de gestão de palavra-passe?**

  > **R:** dados devem aparecer nos relatórios de gestão de palavra-passe em 5-10 minutos. -Algumas instâncias podem demorar até uma hora a aparecer.
  >
  >
* **P: como pode filtrar os relatórios de gestão de palavra-passe?**

  > **R:** pode filtrar os relatórios de gestão de palavra-passe clicando a Lupa pequena para a direita Alpine das etiquetas de coluna, perto da parte superior do relatório. Se pretender mais rico de filtragem, pode transferir o relatório para excel e criar uma tabela dinâmica.
  >
  >
* **P: qual é o número máximo de eventos são armazenadas nos relatórios de gestão de palavra-passe?**

  > **R:** até 75,000 palavra-passe de reposição ou palavra-passe repor o registo de eventos são armazenados nos relatórios de gestão de palavra-passe expansão fazer uma cópia de 30 dias.  Estamos a trabalhar para expandir este número para incluir os eventos mais.
  >
  >
* **P: até que ponto novamente os relatórios de gestão de palavra-passe aceda?**

  > **R:** a gestão de palavras-passe relatórios Mostrar operações em curso nos últimos 30 dias. Por agora, se necessário arquivar estes dados, pode transferir os relatórios periodicamente e guardá-las num local separado.
  >
  >
* **P: existe um número máximo de linhas que pode ser apresentado nos relatórios de gestão de palavra-passe?**

  > **R:** Sim, um máximo de 75,000 linhas pode aparecer em qualquer um dos relatórios de gestão de palavra-passe, se estes estão a ser mostradas na IU ou a ser transferido.
  >
  >
* **P: existe uma API para aceder a reposição de palavra-passe ou o registo de dados de relatórios?**

  > **R:** Sim, consulte a seguinte documentação para saber como pode aceder a reposição de palavra-passe fluxo de dados de relatórios.  [Saiba como aceder a reposição de palavra-passe através de programação de eventos de relatório](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Repetição de escrita de palavras-passe

* **P: como funciona a repetição de escrita de palavras-passe nos bastidores?**

  > **R:** consulte [como funciona a repetição de escrita de palavras-passe](active-directory-passwords-writeback.md) para obter uma explicação sobre o que acontece quando ativar a repetição de escrita de palavras-passe e a forma como os dados fluem através do sistema no seu ambiente no local.
  >
  >
* **P: quanto repetição de escrita de palavras-passe necessário trabalhar?  Existe um atraso de sincronização como com sincronização de hash de palavra-passe?**

  > **R:** repetição de escrita de palavras-passe é instantânea. É um pipeline síncrono que funciona de forma fundamentalmente diferente do que a sincronização de hash de palavra-passe. Repetição de escrita de palavras-passe permite aos utilizadores obter comentários em tempo real sobre o sucesso da respetiva reposição de palavra-passe ou altere a operação. O tempo médio para uma repetição de escrita com êxito de uma palavra-passe está em 500 ms.
  >
  >
* **P: se a minha conta local é desativada, como é afetado a minha conta/acesso à nuvem?**

  > **R:** se o ID de local é desativado, sua nuvem ID/acesso também será desativado no próximo intervalo de sincronização através do AAD Connect por predefinição é a cada 30 minutos.
  >
  >
* **P: se a minha conta local é restrita por uma política de palavra-passe do Active Directory no local, SSPR obedecer esta política quando posso alterar a palavra-passe?**

  > **R:** Sim, SSPR depende e rege-se pela no local política de palavras-passe do AD, incluindo a política de palavras-passe de domínio AD típica, bem como qualquer definidas as políticas de palavras-passe detalhada direcionadas para um determinado utilizador.
  >
  >
* **P: quais são os tipos de contas de repetição de escrita de palavras-passe funciona?**

  > **R:** funciona de repetição de escrita de palavras-passe para utilizadores Federated e sincronização de Hash de palavra-passe.
  >
  >
* **P: repetição de escrita de palavras-passe impor políticas de palavra-passe do meu domínio?**

  > **R:** Sim, a repetição de escrita de palavras-passe impõe a idade da palavra-passe, histórico, complexidade, os filtros e quaisquer outra restrição poderá colocar no local as palavras-passe no seu domínio local.
  >
  >
* **P: é a repetição de escrita de palavras-passe segura?  Como posso ser se que posso não ilícito?**

  > **R:** Sim, a repetição de escrita de palavras-passe é segura. Para ler mais sobre as quatro camadas de segurança implementados pelo serviço de repetição de escrita de palavras-passe, consulte o [modelo de segurança de repetição de escrita de palavras-passe](active-directory-passwords-writeback.md#password-writeback-security-model) secção como funciona a repetição de escrita de palavras-passe.
  >
  >

## <a name="next-steps"></a>Passos seguintes

* [Como concluir a uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Repor ou alterar a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [O registo para a reposição de palavra-passe self-service](active-directory-passwords-reset-register.md).
* [Tem uma pergunta de licenciamento?](active-directory-passwords-licensing.md)
* [Os dados que são utilizados pelo SSPR e os dados que deve preencher para os seus utilizadores?](active-directory-passwords-data.md)
* [Os métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política com SSPR?](active-directory-passwords-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que motivo importantes acerca do mesmo?](active-directory-passwords-writeback.md)
* [Como comunicar na atividade na SSPR](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que fazer significa?](active-directory-passwords-how-it-works.md)
* [Julgo que algo está danificado. Como posso resolver problemas SSPR?](active-directory-passwords-troubleshoot.md)
