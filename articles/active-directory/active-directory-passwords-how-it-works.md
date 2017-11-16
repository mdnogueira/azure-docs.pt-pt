---
title: Como funciona o Azure AD SSPR | Microsoft Docs
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
ms.openlocfilehash: 56ddd5742b63851b9477bae0705ebd24e30ff185
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Self-service reposição palavra-passe na descrição profunda do Azure AD

Como a palavra-passe self-service reposição trabalho (SSPR)? O que significa essa opção na interface? Continue a ler para saber mais sobre SSPR do Azure Active Directory (Azure AD).

## <a name="how-does-the-password-reset-portal-work"></a>Como é que a palavra-passe repor trabalho portal?

Quando um utilizador passa para o portal de reposição de palavra-passe, um fluxo de trabalho é arrancou para determinar:

   * Como deve ser localizada página?
   * É a conta de utilizador válido?
   * Que organização o utilizador pertence ao?
   * Em que é gerida a palavra-passe do utilizador?
   * O utilizador estiver licenciado para utilizar a funcionalidade?

Leia os seguintes passos para saber mais sobre a lógica por trás da palavra-passe de reposição de página:

1. O utilizador seleciona o **não é possível aceder à sua conta** ligar ou passa diretamente para [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
   * Com base na região do browser, a experiência é composta no idioma adequado. A experiência de reposição de palavra-passe está localizada nos mesmos idiomas de que suporta o Office 365.
2. O utilizador introduz um ID de utilizador e transmite um captcha.
3. Azure AD verifica se o utilizador é capaz de utilizar esta funcionalidade, efetuando as seguintes verificações:
   * Verifica se o utilizador tem esta funcionalidade ativada e tem um Azure AD licença atribuída.
     * Se o utilizador não tem esta funcionalidade ativada ou ter uma licença atribuída, é pedido ao utilizador para contactar o administrador para repor a palavra-passe.
   * Verifica se o utilizador tem o direito de desafio dados definidos na respetiva conta de acordo com a política do administrador.
     * Se a política requer apenas um desafio, em seguida, assegura que o utilizador tem os dados adequados definidos para, pelo menos, um dos desafios ativados pela política de administrador.
       * Se o desafio de utilizador não estiver configurado, em seguida, o utilizador é aconselhado a contactar o administrador para repor a palavra-passe.
     * Se a política requer dois desafios, em seguida, assegura que o utilizador tem os dados adequados definidos para, pelo menos, dois desafios ativados pela política de administrador.
       * Se o desafio de utilizador não estiver configurado, em seguida, o utilizador é aconselhado a contactar o administrador para repor a palavra-passe.
   * Verifica se a palavra-passe do utilizador é gerida no local (federado ou sincronizadas de hash de palavra-passe).
     * Se a repetição de escrita está implementada e palavra-passe do utilizador geridos no local, em seguida, o utilizador tem permissão para continuar para autenticar e repor a palavra-passe.
     * Se a repetição de escrita não está implementada e palavra-passe do utilizador geridos no local, em seguida, é pedido ao utilizador para contactar o administrador para repor a palavra-passe.
4. Se é determinado que o utilizador é capaz de reposto com êxito a palavra-passe, o utilizador é orientado através do processo de reposição.

## <a name="authentication-methods"></a>Métodos de autenticação

Se estiver ativada SSPR, tem de selecionar pelo menos uma das seguintes opções para os métodos de autenticação. Por vezes, que ouvir estas opções referidas como "uma vez." Recomendamos vivamente que escolher, pelo menos, dois métodos de autenticação para que os utilizadores tenham uma maior flexibilidade.

* E-mail
* Número de telemóvel
* Telefone do emprego
* Perguntas de segurança

![Autenticação][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>Os campos são utilizados no diretório para os dados de autenticação?

* **Telefone do escritório**: correspondente para o telefone do escritório.
    * Os utilizadores não conseguem defina este campo próprios. Tem de ser definida por um administrador.
* **Telemóvel**: corresponda o telefone de autenticação (publicamente invisível) ou o telemóvel (publicamente visível).
    * O serviço procura primeiro o telefone de autenticação e, em seguida, volta para o telemóvel se for o telefone de autenticação não estão presentes.
* **Alternate endereço de correio eletrónico**: corresponde do e-mail de autenticação (publicamente invisível) ou mensagem de correio eletrónico alternativa.
    * O serviço procura primeiro o e-mail de autenticação e, em seguida, falha novamente para o correio eletrónico alternativo.

Por predefinição, apenas o telefone do escritório de atributos de nuvem e o telemóvel são sincronizados para o diretório em nuvem do seu diretório no local para dados de autenticação.

Os utilizadores só podem repor a palavra-passe se tiver dados presentes nos métodos de autenticação que o administrador tiver ativado e requer.

Se os utilizadores não pretenderem que o número de telemóvel seja visível no diretório, mas ainda pretende utilizá-lo para a reposição de palavra-passe, os administradores devem preenchê-lo não no diretório. Os utilizadores, em seguida, devem preencher os respetivos **telefone de autenticação** atributo através de [portal de registo de reposição de palavra-passe](http://aka.ms/ssprsetup). Os administradores podem ver estas informações no perfil de utilizador, mas não está publicada noutro local.

### <a name="the-number-of-authentication-methods-required"></a>O número de métodos de autenticação necessário

Esta opção determina o número mínimo de métodos de autenticação disponíveis ou um utilizador tem de passar por para repor ou desbloquear a palavra-passe uma vez. Pode ser definido para um ou dois.

Os utilizadores podem optar por fornecer mais métodos de autenticação, se o administrador ativa a esse método de autenticação.

Se um utilizador não tem os métodos necessários mínimos registados, verá uma página de erro que são direcionados para pedir que um administrador repor a palavra-passe.

#### <a name="change-authentication-methods"></a>Métodos de autenticação de alteração

Se começar com uma política que tem apenas um necessário método de autenticação para repor ou desbloquear registado e alterar a que, para os dois métodos, o que acontece?

| Número de métodos registado | Número de métodos necessários | resultado |
| :---: | :---: | :---: |
| 1 ou mais | 1 | **Capaz de** para repor ou desbloquear |
| 1 | 2 | **Não é possível** para repor ou desbloquear |
| 2 ou mais | 2 | **Capaz de** para repor ou desbloquear |

Se alterar os tipos de métodos de autenticação que um utilizador pode utilizar, poderá inadvertidamente interromper a que os utilizadores a capacidade de utilizar SSPR se não tiverem a quantidade mínima de dados disponíveis.

Exemplo: 
1. A política original é configurada com dois métodos de autenticação necessários. Utiliza apenas o número de telefone do office e as perguntas de segurança. 
2. O administrador altera a política para deixar de utilizar as perguntas de segurança, mas permite a utilização de um telemóvel e mensagens de correio eletrónico alternativa.
3. Os utilizadores sem o telemóvel e campos de correio eletrónico alternativo preenchidos não é possível repor as palavras-passe.

### <a name="how-secure-are-my-security-questions"></a>Como protegem são os meus perguntas de segurança?

Se utilizar perguntas de segurança, recomendamos a utilizá-los em conjunto com outro método. Perguntas de segurança podem ser menos seguras do que outros métodos, porque algumas pessoas poderão saber as respostas às perguntas de outro utilizador.

> [!NOTE] 
> Perguntas de segurança são armazenadas em privado e segura num objeto de utilizador no diretório e apenas podem ser respondidas pelos utilizadores durante o registo. Não há nenhuma forma de um administrador ler ou modificar um utilizador perguntas ou respostas.
>

### <a name="security-question-localization"></a>Localização da pergunta de segurança

Todas as perguntas predefinidas que se seguem estão localizadas para o conjunto completo de idiomas do Office 365 e baseiam-se na região do browser do utilizador:

* Em que cidade conheceu o seu primeiro namorado/a ou /companheiro/a?
* Em que cidade os seus pais se conheceram?
* Em que cidade vive o/a seu/sua irmão/irmã que está mais próximo/a?
* Em que cidade nasceu o seu pai?
* Em que cidade teve o seu primeiro emprego?
* Em que cidade nasceu a sua mãe?
* Em que cidade estava na passagem para 2000?
* Qual é o último nome do/a seu/sua professor/a preferido/a na escola secundária?
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

Perguntas de segurança personalizado não estão localizadas para diferentes regiões. Todas as perguntas personalizadas são apresentadas no mesmo idioma que estes são introduzidos na interface do utilizador administrativo, mesmo se a região de browser do utilizador é diferente. Se tiver perguntas localizadas, deve utilizar as perguntas predefinidas.

O comprimento máximo de uma pergunta de segurança personalizada é de 200 carateres.

### <a name="security-question-requirements"></a>Requisitos de pergunta de segurança

* O limite de caracteres de resposta mínimo é de três carateres.
* O limite de caracteres de resposta máximo é de 40 carateres.
* Os utilizadores não podem responder a mesma pergunta mais do que uma vez.
* Os utilizadores não é possível fornecer a mesma resposta à pergunta mais do que um.
* Qualquer conjunto de carateres pode ser utilizado para definir as perguntas e respostas, incluindo carateres Unicode.
* O número de perguntas definidas tem de ser maior que ou igual ao número de perguntas que eram necessários para registar.

## <a name="registration"></a>Registo

### <a name="require-users-to-register-when-they-sign-in"></a>Exigir que os utilizadores se registem quando iniciam sessão no

Para ativar esta opção, um utilizador que está ativado para a reposição de palavra-passe tem de concluir o registo de reposição de palavra-passe se iniciam sessão no aplicações ao utilizar o Azure AD. Isto inclui o seguinte:

* Office 365
* Portal do Azure
* Painel de Acesso
* Aplicações federadas
* Aplicações personalizadas ao utilizar o Azure AD

Quando a necessidade de registo está desativada, os utilizadores podem manualmente registar as suas informações de contactos. Estes requisitos podem ambos visite [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) ou selecione o **registar para a reposição de palavra-passe** ligação sob o **perfil** separador no painel de acesso.

> [!NOTE]
> Os utilizadores podem ignorar o portal de registo de reposição de palavra-passe, selecionando **Cancelar** ou se fechar a janela. Mas, será pedido para registar cada vez que iniciar sessão até que concluírem o respetivo registo.
>
> Tal não interromper a ligação do utilizador que já estejam assinados.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Definir o número de dias antes dos utilizadores são-lhe pedidos para reconfirm as suas informações de autenticação

Esta opção determina o período de tempo entre a definição e reconfirming informações de autenticação e está disponível apenas se ativar o **exigir que os utilizadores se registem quando iniciam** opção.

Os valores válidos são 0 para 730 dias, com "0", que significa que os utilizadores nunca-lhe pedidos para reconfirm as suas informações de autenticação.

## <a name="notifications"></a>Notificações

### <a name="notify-users-on-password-resets"></a>Notificar os utilizadores sobre reposições de palavras-passe

Se esta opção estiver definida como **Sim**, em seguida, o utilizador que está a repor a palavra-passe recebe uma mensagem de e-mail a indicar que a palavra-passe foi alterada. O e-mail é enviado através do portal SSPR para os respetivos endereços de e-mail primário e alternativo que estão no ficheiro no Azure AD. Ninguém mais é notificado de que esta reposição de evento.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notificar todos os administradores quando outros administradores reponham as palavras-passe

Se esta opção estiver definida como **Sim**, em seguida, *todos os administradores* receber um e-mail para o respetivo endereço de correio eletrónico principal no ficheiro no Azure AD. O e-mail notifica-los se outro administrador foi alterado a palavra-passe através da utilização de SSPR.

Exemplo: Existem quatro administradores num ambiente. O administrador A repõe a palavra-passe através da utilização de SSPR. Os administradores B, C e D recebem uma mensagem de e-mail para alertas-los de reposição de palavra-passe.

## <a name="on-premises-integration"></a>Integração no local

Se instalar, configurar e ativar o Azure AD Connect, terá as seguintes opções adicionais para integrações no local. Se estas opções estão desativadas, em seguida, repetição de escrita não foi corretamente configurada. Para obter mais informações, consulte [configurar a repetição de escrita de palavras-passe](active-directory-passwords-writeback.md#configuring-password-writeback).

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Repetição de escrita palavras-passe para o seu diretório no local

Este controlo determina se a repetição de escrita de palavras-passe está ativada para este diretório. Se a repetição de escrita está ativado, indica o estado do serviço de repetição de escrita no local. Isto é útil se pretender desativar temporariamente a repetição de escrita de palavras-passe sem ter de reconfigurar o Azure AD Connect.

* Se o comutador está definido como **Sim**, em seguida, repetição de escrita ativada e federada e os utilizadores sincronizado de hash de palavra-passe são capazes de repor as palavras-passe.
* Se o comutador está definido como **não**, em seguida, repetição de escrita está desativada e federada e os utilizadores sincronizado de hash de palavra-passe não são possível repor as palavras-passe.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Permitir que os utilizadores desbloqueiem as contas sem repor a palavra-passe

Este controlo designa se os utilizadores que visitarem o portal de reposição de palavra-passe devem ser dada a opção para desbloquear as respetivas contas do Active Directory no local, sem ter de repor a palavra-passe. Por predefinição, o Azure AD desbloqueia contas quando ele efetuar uma reposição de palavra-passe. Utilize esta definição para separar as duas operações. 

* Se definido como **Sim**, em seguida, os utilizadores recebem a opção para repor a palavra-passe e desbloquear a conta ou para desbloquear a conta sem ter de repor a palavra-passe.
* Se definido como **não**, em seguida, os utilizadores são só poderá efetuar uma reposição de palavra-passe combinado e a operação de desbloqueio de conta.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Como a palavra-passe reposição trabalho para os utilizadores de B2B?
Reposição de palavra-passe e de alterações são totalmente suportadas em todas as configurações do empresa-empresa (B2B). Reposição de palavra-passe de utilizador de B2B é suportada nos seguintes três casos:

   * **Os utilizadores de uma organização de parceiro com um inquilino do Azure AD existente**: se a organização está a ser partnering com um inquilino do Azure AD existente, iremos *Respeitamos a qualquer políticas de reposição de palavra-passe estão ativadas nesse inquilino*. Palavra-passe repor funcione, a organização do parceiro apenas tem de certificar-se de que o Azure AD SSPR está ativado. Não há sem encargos adicionais para os clientes do Office 365 e pode ser ativado ao seguir os passos no nosso [introdução à gestão de palavra-passe](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) guia.
   * **Os utilizadores inscrever-se através de** inscrição Self-Service: se a organização está a partnering utilizado com o [inscrição Self-Service](active-directory-self-service-signup.md) funcionalidade para obter um inquilino, vamos deixá-los a repor a palavra-passe com o e-mail registados.
   * **Utilizadores de B2B**: quaisquer novos utilizadores B2B criados utilizando a nova [capacidades do Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md) também conseguirá repor as palavras-passe com o e-mail registados durante o processo de convite.

Para testar este cenário, aceda à http://passwordreset.microsoftonline.com com um destes utilizadores do parceiro. Se tiverem um correio eletrónico alternativo ou o e-mail de autenticação definido, a palavra-passe reposição funciona conforme esperado.

> [!NOTE]
> As contas do Microsoft que tenham sido concedidas acesso de convidado para o inquilino do Azure AD, tais como Hotmail.com, Outlook.com ou outros endereços de e-mail pessoal, não são capazes de utilizar o Azure AD SSPR. Têm de repor a palavra-passe, utilizando as informações localizadas no [quando não é possível iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.

## <a name="next-steps"></a>Passos seguintes

Os artigos seguintes fornecem informações adicionais sobre a reposição através do Azure AD palavra-passe:

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
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Authentication]: ./media/active-directory-passwords-how-it-works/sspr-authentication-methods.png "Métodos de autenticação do Azure AD disponíveis e a quantidade necessária"
