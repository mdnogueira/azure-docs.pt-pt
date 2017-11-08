---
title: Como funciona? Azure AD SSPR | Microsoft Docs
description: Repor o Azure AD self-service palavra-passe detalhada
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: fd9515120049dd3837a43c95de8a9b6822719e19
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Self-service reposição palavra-passe na descrição profunda do Azure AD

Como funciona o SSPR? O que significa essa opção na interface? Continuar a ler para saber mais sobre a palavra-passe self-service do Azure AD repor.

## <a name="how-does-the-password-reset-portal-work"></a>Como é que a palavra-passe redefinir trabalho portal

Quando um utilizador navega para o portal de reposição de palavra-passe, um fluxo de trabalho é arrancou para determinar:

   * Como deve ser localizada página?
   * É a conta de utilizador válido?
   * Que organização o utilizador pertence ao?
   * Em que é gerida a palavra-passe do utilizador?
   * O utilizador estiver licenciado para utilizar a funcionalidade?


Leia os seguintes passos para saber mais sobre a lógica por trás da palavra-passe de reposição de página.

1. Utilizador clica no não consegue aceder à sua ligação de conta ou ficar diretamente para [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
2. Com base na região do browser que a experiência é composta no idioma adequado. A experiência de reposição de palavra-passe está localizada para os mesmos idiomas de que suporte do Office 365.
3. Utilizador deve introduzir um ID de utilizador e transmite um captcha.
4. Azure AD verifica se o utilizador é capaz de utilizar esta funcionalidade, efetuando as seguintes verificações:
   * Verifica se o utilizador tem esta funcionalidade ativada e atribuída uma licença do Azure AD.
     * Se o utilizador não tiver esta funcionalidade ativada ou uma licença atribuída, é pedido ao utilizador para contactar o administrador para repor a palavra-passe.
   * Verifica se o utilizador tem o direito de desafio dados definidos na respetiva conta de acordo com a política do administrador.
     * Se a política requer apenas um desafio, em seguida,-lo é certificar-se de que o utilizador tem os dados adequados definidos para, pelo menos, um dos desafios ativados pela política de administrador.
       * Se o utilizador não estiver configurado, em seguida, o utilizador é aconselhado a contactar o administrador para repor a palavra-passe.
     * Se a política requer dois desafios, em seguida, este é certificar-se de que o utilizador tem os dados adequados definidos para, pelo menos, dois desafios ativados pela política de administrador.
       * Se o utilizador não está configurado, então, o utilizador é aconselhado para contactar o administrador para repor a palavra-passe.
   * Verifica se a palavra-passe do utilizador é gerida no local (federado ou sincronizadas de hash de palavra-passe).
     * Se for implementada a repetição de escrita e a palavra-passe do utilizador é gerida no local, o utilizador tem permissão para continuar para autenticar e repor a palavra-passe.
     * Se não estiver implementada a repetição de escrita e a palavra-passe do utilizador é gerida no local, em seguida, é pedido ao utilizador para contactar o administrador para repor a palavra-passe.
5. Se é determinado que o utilizador é capaz de reposto com êxito a palavra-passe, o utilizador é orientado através do processo de reposição.

## <a name="authentication-methods"></a>Métodos de autenticação

Se estiver ativada a reposição de palavra-passe Self-Service (SSPR), tem de selecionar pelo menos uma das seguintes opções para métodos de autenticação. Por vezes poderá ouvir estas opções referidas como portas. Recomendamos vivamente que escolher, pelo menos, dois métodos de autenticação para que os utilizadores tenham uma maior flexibilidade.

* E-mail
* Telemóvel
* Telefone do escritório
* Perguntas de Segurança

![Autenticação][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-authentication-data"></a>Os campos são utilizados no diretório de dados de autenticação

* Telefone do escritório corresponde ao telefone do escritório
    * Os utilizadores não conseguem defina este campo próprios tem de ser definida por um administrador
* Telemóvel corresponde ao telefone de autenticação (publicamente invisível) ou telemóvel (publicamente visível)
    * O serviço procura primeiro telefone de autenticação, em seguida, retrocede para o telemóvel se não estiver presente
* Endereço de correio eletrónico alternativo corresponde a um E-Mail de autenticação (publicamente invisível) ou correio eletrónico alternativo
    * O serviço procura primeiro o E-Mail de autenticação, em seguida, Falha ao correio eletrónico alternativo

Por predefinição, apenas os atributos de nuvem telefone do escritório e telemóvel são sincronizados para o diretório em nuvem do seu diretório no local para dados de autenticação.

Os utilizadores só podem repor a palavra-passe se tiver dados presentes nos métodos de autenticação que o administrador tiver ativado e requer.

Se os utilizadores não pretendem que o respetivo número de telemóvel seja visível no diretório, mas ainda pretenderem utilizá-lo para a reposição de palavra-passe, os administradores devem não preenchê-lo no diretório e o utilizador, em seguida, deve preencher os respetivos **detelefonedeautenticação** atributo através de [portal de registo de reposição de palavra-passe](http://aka.ms/ssprsetup). Os administradores podem ver estas informações no perfil de utilizador, mas não está publicada noutro local.

### <a name="number-of-authentication-methods-required"></a>Número de métodos de autenticação necessário

Esta opção determina o número mínimo de portas de um utilizador tem de passar por para repor ou desbloquear a palavra-passe e pode ser definido para 1 ou 2 ou métodos de autenticação disponíveis.

Os utilizadores podem optar por fornecer mais métodos de autenticação, caso estejam ativados pelo administrador.

Se um utilizador não tem os métodos necessários mínimos registados, verá uma página de erro que são direcionados para pedir um administrador para repor a palavra-passe.

#### <a name="changing-authentication-methods"></a>A alteração de métodos de autenticação

Se começar com uma política que tem apenas um método de autenticação necessário para repor ou desbloquear registado e alterar a que a dois o que acontece?

| Número de métodos registado | Número de métodos necessários | resultado |
| :---: | :---: | :---: |
| 1 ou mais | 1 | **Capaz de** para repor ou desbloquear |
| 1 | 2 | **Não é possível** para repor ou desbloquear |
| 2 ou mais | 2 | **Capaz de** para repor ou desbloquear |

Se alterar os tipos de métodos de autenticação que um utilizador pode utilizar inadvertidamente poderão impedir que os utilizadores a capacidade de utilizar SSPR se não tiver a quantidade mínima de dados disponíveis.

Exemplo: 
1. Política original configurada com 2 métodos de autenticação necessários utilizar perguntas de segurança e o telefone office apenas. 
2. Administrador altera a política para deixar de utilizar perguntas de segurança, mas permitir a utilização de telemóvel e correio eletrónico alternativo.
3. Os utilizadores sem telemóvel e campos de correio eletrónico alternativo preenchidos não é possível repor as palavras-passe.

### <a name="how-secure-are-my-security-questions"></a>Como protegem são os meus perguntas de segurança

Se utilizar perguntas de segurança, recomendamos-los em utilização com outro método que podem ser menos seguros do que outros métodos, uma vez que algumas pessoas poderão conhece as respostas às perguntas de outro utilizador.

> [!NOTE] 
> Perguntas de segurança são armazenadas em privado e segura num objeto de utilizador no diretório e apenas podem ser respondidas pelos utilizadores durante o registo. Não há nenhuma forma de um administrador ler ou modificar um utilizador perguntas ou respostas.
>

### <a name="security-question-localization"></a>Localização da pergunta de segurança

Todas as perguntas predefinidas que se seguem estão localizadas para o conjunto completo de idiomas do Office 365 com base na região do browser do utilizador.

* Em que cidade conheceu o seu primeiro namorado/a ou /companheiro/a?
* Em que cidade os seus pais se conheceram?
* Em que cidade vive o/a seu/sua irmão/irmã que está mais próximo/a?
* Em que cidade nasceu o seu pai?
* Em que cidade teve o seu primeiro emprego?
* Em que cidade nasceu a sua mãe?
* Em que cidade estava na passagem para 2000?
* O que é o último nome do seu/sua professor no alta * escola?
* Qual é o nome da instituição de ensino superior a que candidatou mas não frequentou?
* Qual é o nome do lugar onde organizou a sua primeira festa de casamento?
* Qual é o segundo nome do seu pai?
* Qual é o seu prato favorito?
* Quais são o primeiro e último nome da sua avó materna?
* Qual é o segundo nome da sua mãe?
* O que é seu colateral mais antigo que mês e ano? (por exemplo, Novembro de 1985)
* Qual é o segundo nome do/a seu/sua irmão/irmã mais velho/a?
* Quais são o primeiro e último nome do seu avô paterno?
* Qual é o nome do meio do/a seu/sua irmão/irmã mais novo/a?
* Em que escola andou quando estava no 6º ano?
* Quais eram o primeiro e último nome do seu/sua melhor amigo/a de infância?
* Quais eram o primeiro e último nome do/a seu/sua companheiro/a?
* Qual era o último nome do/a seu/sua professor/a da escola primária preferido/a?
* Qual era a marca e o modelo do seu primeiro carro ou da sua motocicleta?
* Qual é o nome da primeira escola que frequentou?
* Qual é o nome do hospital em que nasceu?
* Qual é o nome da rua da primeira casa onde viveu em criança?
* Qual é o nome do/a seu/sua herói/heroína preferido/a quando era criança?
* Qual era o nome do seu animal de peluche preferido?
* Qual era o nome do seu primeiro animal de estimação?
* Qual era a sua alcunha em criança?
* Qual era o seu desporto favorito na escola secundária?
* Qual foi o seu primeiro emprego?
* Quais eram os últimos quatro dígitos do seu número telefone de infância?
* Quando era mais novo/a, onde queria estar quando fosse adulto/a?
* Quem é a pessoa mais famosa que já conheceu?

### <a name="custom-security-questions"></a>Perguntas de segurança personalizadas

Perguntas de segurança personalizado não estão localizadas para diferentes regiões. Todas as perguntas personalizadas são apresentadas no mesmo idioma que são introduzidos na interface de utilizador administrativo, mesmo que esteja diferentes região do browser do utilizador. Se tiver perguntas localizadas, utilize as perguntas predefinidas.

O comprimento máximo de uma pergunta de segurança personalizada é de 200 carateres.

### <a name="security-question-requirements"></a>Requisitos de pergunta de segurança

* Limite de caracteres de resposta mínimo é de 3 carateres
* Limite de caracteres de resposta máximo é 40 carateres
* Os utilizadores não podem responder a mesma pergunta mais do que uma vez
* Os utilizadores não podem fornecer a mesma resposta à pergunta mais do que um
* Qualquer conjunto de carateres que pode ser utilizado para definir as perguntas e respostas, incluindo carateres Unicode
* O número de perguntas definidas tem de ser maior que ou igual ao número de perguntas necessárias para registar

## <a name="registration"></a>Registo

### <a name="require-users-to-register-when-signing-in"></a>Exigir que os utilizadores se registem ao iniciar sessão

A ativação desta opção necessita de registo de reposição de um utilizador que está ativado para concluir a palavra-passe de reposição de palavra-passe se poderem iniciar sessão para aplicações com o Azure AD para iniciar sessão como aqueles que se seguem:

* Office 365
* Portal do Azure
* Painel de Acesso
* Aplicações federadas
* Aplicações personalizadas com o Azure AD

Quando esta estiver desativada os utilizadores podem registar manualmente as respetivas informações de contactos, visitando [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) ou clicando a **registar para a reposição de palavra-passe** ligação no separador no perfil de Painel de acesso.

> [!NOTE]
> Os utilizadores podem ignorar o portal de registo de reposição de palavra-passe ao clicar em Cancelar ou fechar a janela mas recebem sempre que iniciar sessão até à sua conclusão de registo.
>
> Não tal irá interromper a ligação do utilizador que se encontrem alreay com sessão iniciada.

### <a name="number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Número de dias antes de ser pedido aos utilizadores que voltem a confirmar as informações de autenticação

Esta opção determina o período de tempo entre a definição e reconfirming informações de autenticação e apenas está disponível se ativar o **exigir que os utilizadores se registem quando iniciam** opção.

Os valores válidos são 0-730 dias com 0, o que significa nunca peça aos utilizadores para reconfirm as suas informações de autenticação

## <a name="notifications"></a>Notificações

### <a name="notify-users-on-password-resets"></a>Notificar os utilizadores sobre reposições de palavras-passe

Se esta opção estiver definida como Sim, o utilizador que está a repor a palavra-passe recebe uma mensagem de e-mail a indicar que a palavra-passe foi alterada através do portal SSPR para os respetivos endereços de e-mail alternativo e principal no ficheiro no Azure AD. Ninguém mais é notificado de que esta reposição de evento.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notificar todos os administradores quando outros administradores reponham as palavras-passe

Se esta opção estiver definida como Sim, em seguida, **todos os administradores** receber um e-mail para o respetivo endereço de correio eletrónico principal no ficheiro no Azure AD para indicar se outro administrador foi alterado a palavra-passe SSPR a utilizar.

Exemplo: Existem quatro administradores num ambiente. Administrador "A" repõe a palavra-passe SSPR a utilizar. Os administradores B, C e D recebem um e-mail de alerta-los de tal acontecer.

## <a name="on-premises-integration"></a>Integração no local

Se tiver instalado, configurado e ativado o Azure AD Connect, terá as seguintes opções adicionais para integrações no local. Se estas opções são greyed escalamento, em seguida, repetição de escrita não foi corretamente configurada consulte [configurar a repetição de escrita de palavras-passe](active-directory-passwords-writeback.md#configuring-password-writeback) para obter mais informações.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Repetição de escrita palavras-passe para o seu diretório no local

Controla se a repetição de escrita de palavras-passe está ativada para este diretório e, se a repetição de escrita está ativado, indica o estado do serviço de repetição de escrita no local. Isto é útil se pretender desativar temporariamente a repetição de escrita de palavras-passe sem necessidade de reconfigurar o Azure AD Connect.

* Se o comutador está definido como Sim, em seguida, repetição de escrita ativada e federada e os utilizadores sincronizado de hash de palavra-passe são capazes de repor as palavras-passe.
* Se o comutador está definido para não, em seguida, repetição de escrita está desativada e federada e os utilizadores sincronizado de hash de palavra-passe não são conseguir repor as palavras-passe.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Permitir que os utilizadores desbloqueiem as contas sem repor a palavra-passe

Designa se ou não, os utilizadores que visitarem o portal de reposição de palavra-passe devem ser dada a opção para desbloquear as respetivas contas do Active Directory no local sem repor a palavra-passe. Por predefinição, do Azure AD desbloqueia contas ao efetuar uma reposição de palavra-passe, esta definição permite-lhe separar as duas operações. 

* Se definido como "yes", em seguida, os utilizadores recebem a opção para repor a palavra-passe e desbloquear a conta ou para desbloquear sem repor a palavra-passe.
* Se definido como "não", em seguida, os utilizadores são só poderá efetuar uma palavra-passe combinada reposto e operação de desbloqueio de conta.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Como a palavra-passe reposição trabalho para os utilizadores de B2B?
Reposição de palavra-passe e de alterações são totalmente suportadas com todas as configurações de B2B. Os seguintes três casos são suportados para a reposição de palavra-passe de utilizador de B2B.

1. **Os utilizadores de uma organização de parceiro com um inquilino do Azure AD existente** - se a organização são partnering com um inquilino do Azure AD existente, iremos **Respeitamos a qualquer políticas de reposição de palavra-passe estão ativadas nesse inquilino**. Para palavra-passe repor para funcionar, o parceiro organização apenas tem de certificar-se de que está ativado o Azure AD SSPR, ou seja, sem encargos adicionais para os clientes do Office 365, e pode ser ativado ao seguir os passos no nosso [introdução à gestão de palavra-passe](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)guia.
2. **Os utilizadores que inscreveu utilizando [inscrição Self-Service](active-directory-self-service-signup.md)**  - se a organização partnering utilizado com o [inscrição Self-Service](active-directory-self-service-signup.md) funcionalidade para obter um inquilino, vamos deixá-los reposto com o e-mail registados.
3. **Utilizadores de B2B** -os novos utilizadores B2B criados utilizando a nova [capacidades do Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md) também conseguirá repor as palavras-passe com o e-mail registados durante o processo de convite.

Para testar este cenário, aceda à http://passwordreset.microsoftonline.com com um destes utilizadores do parceiro. Desde tenham um correio eletrónico alternativo ou o e-mail de autenticação definido, a palavra-passe reposição funciona conforme esperado.

> [!NOTE]
> Contas Microsoft que tenham sido concedidas acesso de convidado para o seu Azure AD de inquilino, tais como as da Hotmail.com, Outlook.com, ou outros endereços de e-mail pessoais não são capazes de utilizar o Azure AD SSPR e terão de repor a palavra-passe utilizando a informação foi encontrada no artigo [quando não é possível iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="next-steps"></a>Passos seguintes

As ligações seguintes disponibilizam informações adicionais relativamente à reposição de palavras-passe com o Azure AD

* [Como posso concluir uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Reponha ou altere a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre Licenciamento?](active-directory-passwords-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](active-directory-passwords-data.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política da SSPR?](active-directory-passwords-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](active-directory-passwords-writeback.md)
* [Como posso comunicar a atividade da SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](active-directory-passwords-how-it-works.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Authentication]: ./media/active-directory-passwords-how-it-works/sspr-authentication-methods.png "Métodos de autenticação do Azure AD disponíveis e a quantidade necessária"