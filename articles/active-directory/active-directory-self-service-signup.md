---
title: "O que é a inscrição Self-Service do Azure? | Microsoft Docs"
description: "Uma inscrição self-service de descrição geral do Azure, como gerir o processo de inscrição e, como assumir um nome de domínio DNS."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 511088156d3546e2e0f3ac40e72bf2b8e4ae2cb9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-self-service-signup-for-azure"></a>O que é a inscrição Self-Service do Azure?
Este tópico explica o processo de inscrição self-service e como assumir um nome de domínio DNS.  

## <a name="why-use-self-service-signup"></a>Porquê utilizar a inscrição self-service?
* Obter os clientes para serviços que pretendem mais rapidamente.
* Crie ofertas baseados em e-mail para um serviço.
* Crie fluxos de inscrição baseados em e-mail que rapidamente permitem aos utilizadores criar identidades utilizando os aliases de e-mail do trabalho fácil de lembrar.
* Diretórios do Azure não geridos podem ser efetuados em diretórios geridos mais tarde e ser reutilizados para outros serviços.

## <a name="terms-and-definitions"></a>Termos e definições
* **Inscrição self-service**: Este é o método pelo qual um utilizador se inscreve num serviço em nuvem e tem uma identidade criada automaticamente para os mesmos no Azure Active Directory (Azure AD) com base no respetivo domínio de correio eletrónico.
* **Diretório do Azure não gerido**: Este é o diretório onde essa identidade é criada. Um diretório não gerido é um diretório com nenhum administrador global.
* **Verificar o e-mail de utilizador**: Este é um tipo de conta de utilizador no Azure AD. Um utilizador que tem uma identidade criada automaticamente depois de inscrever-se para uma oferta de self-service é conhecido como um utilizador verificados por e-mail. Um utilizador verificado de e-mail é um membro de um diretório etiquetado com creationmethod regular = EmailVerified.

## <a name="user-experience"></a>Experiência de utilizador
Por exemplo, vamos supor que um utilizador cujo o e-mail é Dan@BellowsCollege.com recebe ficheiros confidenciais via e-mail. Os ficheiros foram protegidos pelo Azure Rights Management (Azure RMS). Mas organização de Dan, Bellows de ensino superior, não tiver assinado para o Azure RMS nem implementou Active Directory RMS. Neste caso, Dan pode inscrever-se para uma subscrição gratuita para o RMS para indivíduos para ler os ficheiros protegidos.

Se Dan é o primeiro utilizador com um endereço de correio eletrónico de BellowsCollege.com para se inscrever para esta Self-Service oferta, em seguida, será criado um diretório de não gerido para BellowsCollege.com no Azure AD. Se outros utilizadores do domínio BellowsCollege.com inscrevem esta oferta ou uma oferta de self-service de mensagens em fila semelhante, também terá de contas de utilizador verificadas de correio eletrónico criadas no mesmo diretório não gerido no Azure.

## <a name="admin-experience"></a>Experiência da administração
Um administrador que é proprietária do nome de domínio DNS de um diretório do Azure não gerido pode assumir ou o diretório de intercalação depois de comprovar a propriedade. As secções seguintes explicam a experiência da administração mais detalhadamente, mas Eis um resumo:

* Quando obtiver o controlo de um diretório do Azure não gerido, pode simplesmente tornar-se o administrador global do diretório não gerido. Isto denomina-se um aquisições interna.
* Quando intercalar um diretório do Azure não gerido, adicione o nome de domínio DNS do diretório não gerido para o seu diretório do Azure gerido e um mapeamento de utilizadores para recursos é criado, por isso, os utilizadores podem continuar a aceder aos serviços sem interrupção. Isto denomina-se um aquisições externa.

## <a name="what-gets-created-in-azure-active-directory"></a>O que é criado no Azure Active Directory?
#### <a name="directory"></a>diretório
* Um diretório do Azure Active Directory para o domínio é criado um diretório por domínio.
* O diretório do Azure AD não tem nenhum administrador global do.

#### <a name="users"></a>Utilizadores
* Para cada utilizador que se inscreve, é criado um objeto de utilizador no diretório do Azure AD.
* Cada objeto de utilizador está marcado como externo.
* Cada utilizador é dado acesso ao serviço que se inscreveu no.

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>Como afirmações do Azure AD self-service diretório para um domínio posso proprietário?
Podem reclamá do Azure AD self-service diretório efetuando a validação do domínio. Validação do domínio comprova que possuir o domínio através da criação de registos DNS.

Existem duas formas para fazer uma aquisições DNS de um diretório do Azure AD:

* aquisições interna (Admin Deteta um diretório do Azure não gerido e pretende ativar para um diretório gerido)
* aquisições externa (Admin tenta adicionar um novo domínio ao seu diretório do Azure gerido)

Poderá estar interessado na validação do que é proprietário de um domínio porque está a demorar através de um diretório não gerido depois de um utilizador efetuou a inscrição self-service ou que pode adicionar um novo domínio para um diretório existente gerido. Por exemplo, se tiver um domínio com o nome contoso.com e pretender adicionar um novo domínio com o nome contoso.co.uk ou contoso.uk.

## <a name="what-is-domain-takeover"></a>O que é aquisições domínio?
Esta secção abrange como validar que é proprietário de um domínio

### <a name="what-is-domain-validation-and-why-is-it-used"></a>O que é a validação do domínio e por que motivo é utilizado?
Para efetuar operações de um diretório, o Azure AD requer que valide a propriedade de domínio DNS.  Validação do domínio permite-lhe o diretório e a promover o diretório de self-service para um diretório gerido, de afirmação ou o diretório de self-service de intercalação para um diretório existente gerido.

## <a name="examples-of-domain-validation"></a>Exemplos de validação do domínio
Existem duas formas para fazer uma aquisições DNS de um diretório:

* aquisições internas (por exemplo, um administrador Deteta um diretório de self-service, não gerido e pretende ativar no diretório gerido)
* aquisições externas (por exemplo, um administrador tenta adicionar um novo domínio a um diretório gerido)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>Aquisições interna - promover um diretório de self-service, não gerido para ser um diretório gerido
Quando o fizer aquisições interna, o diretório obtém convertido a partir de um diretório de não gerido para um diretório gerido. Tem de concluir a validação de nome de domínio DNS, onde criar um registo MX ou um registo TXT na zona DNS. Essa ação:

* Valida que é seu domínio
* Faz com que o diretório gerido
* Faz com que o administrador global do diretório

Vamos supor que um administrador de TI de ensino superior a Bellows Deteta que os utilizadores a partir da escola inscreveram ofertas de self-service. Como o proprietário registado DNS nome BellowsCollege.com, o administrador de TI pode validar a propriedade de nome de DNS no Azure e, em seguida, assumir o diretório não gerido. O diretório, em seguida, passa a ser um diretório gerido e o administrador de TI é atribuído a função de administrador global para o diretório de BellowsCollege.com.

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>Aquisições externa - intercalar um diretório de self-service um diretório existente gerido
Numa aquisições externa, já tem um diretório gerido e pretender que todos os utilizadores e grupos a partir de um diretório de não gerido para associar esse diretório gerido, em vez de separam os seus próprios dois diretórios.

Como administrador de um diretório gerido, adicionar um domínio e, nesse domínio acontece ter um diretório de não gerido associado à mesma.

Por exemplo, vamos supor que é um administrador de TI e já tiver um diretório de gerido para Contoso.com, um nome de domínio que está registado para a sua organização. Detetar a que os utilizadores da sua organização efetuou self-service sessão cópias de segurança de uma oferta utilizando o nome de domínio do e-mail user@contoso.co.uk, que é outro nome de domínio que a sua organização é proprietária. Esses utilizadores tiverem atualmente contas num diretório para contoso.co.uk não gerido.

Não pretende gerir dois diretórios separados, para intercalar o diretório não gerido para contoso.co.uk diretório existente no IT gerido para contoso.com.

Aquisições externa segue o mesmo processo de validação de DNS como aquisições interno.  Diferença a ser: utilizadores e serviços são novamente mapeados para o diretório IT gerido.

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>O que é o impacto da execução de uma aquisições externas?
Com uma aquisições externa, um mapeamento de utilizadores para recursos é criado para que os utilizadores podem continuar a aceder aos serviços sem interrupção. Muitas aplicações, incluindo o RMS para indivíduos, lidar com o mapeamento de utilizadores para recursos bem e os utilizadores podem continuar a aceder a esses serviços sem alteração. Se uma aplicação não processa o mapeamento de utilizadores para recursos de forma eficaz, aquisições externas podem estar bloqueadas explicitamente para impedir que utilizadores uma experiência fraca.

#### <a name="directory-takeover-support-by-service"></a>suporte alimentar de diretório pelo serviço
Os serviços seguintes suportam atualmente aquisições:

* RMS

Os seguintes serviços em breve suportar aquisições:

* PowerBI

O seguinte não e requerem uma ação de admin adicionais para migrar os dados de utilizador após uma aquisições externa.

* SharePoint/OneDrive

## <a name="how-to-perform-a-dns-domain-name-takeover"></a>Como efetuar uma aquisições de nome de domínio DNS
Tem algumas opções efetuar uma validação de domínio (e fazer uma aquisições se desejar):

1. Portal de Gestão do Azure

   É acionada uma aquisições efetuando a adição de um domínio.  Se já existe um diretório para o domínio, terá a opção para efetuar uma aquisições externa.

   Inicie sessão no portal do Azure com as suas credenciais.  Navegue para o seu diretório existente e, em seguida, **Adicionar domínio**.
2. Office 365

   Pode utilizar as opções no [gerir domínios](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) página no Office 365 para trabalhar com os seus domínios e registos DNS. Consulte [Verifique o seu domínio no Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).
3. Windows PowerShell

   Os seguintes passos são necessários para efetuar uma validação utilizando o Windows PowerShell.

   | Passo | Cmdlet para utilizar |
   | --- | --- |
   | Criar um objeto de credencial |Get-Credential |
   | Ligar ao Azure AD |Connect-MsolService |
   | obter uma lista de domínios |Get-MsolDomain |
   | Criar um desafio |Get-MsolDomainVerificationDns |
   | Criar registo DNS |Fazê-lo no seu servidor DNS |
   | Verifique o desafio |Confirm-MsolEmailVerifiedDomain |

Por exemplo:

1. Ligar ao Azure AD com as credenciais que foram utilizadas para responder a oferta de self-service:

        import-module MSOnline
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
2. Obter uma lista de domínios:

    Get-MsolDomain
3. Em seguida, execute o cmdlet Get-MsolDomainVerificationDns para criar um desafio de:

    Get-MsolDomainVerificationDns – DomainName *your_domain_name* – modo DnsTxtRecord

    Por exemplo:

    Get-MsolDomainVerificationDns – DomainName contoso.com – modo DnsTxtRecord
4. Copie o valor (o desafio) que é devolvido a partir deste comando.

    Por exemplo:

    MS = 32DD01B82C05D27151EA9AE93C5890787F0E65D9
5. No seu espaço de nomes DNS público, crie um registo txt DNS que contém o valor que copiou no passo anterior.

    O nome para este registo é o nome do domínio principal, por isso, se criar este registo de recursos utilizando a função DNS do Windows Server, deixe colar registo nome em branco e apenas o valor na caixa de texto
6. Execute o cmdlet MsolDomain confirmar para verificar o desafio:

    MsolEmailVerifiedDomain confirmar - DomainName *your_domain_name*

    Por exemplo:

    MsolEmailVerifiedDomain confirmar - DomainName contoso.com

Um desafio bem-sucedida devolve o pedido sem um erro.

## <a name="how-do-i-control-self-service-settings"></a>Como controlar o self-service definições?
Administradores tem dois controlos self-service hoje. Estes podem controlar:

* Se os utilizadores podem associar o diretório através de e-mail.
* Se pretende ou não os utilizadores podem licenças próprios para aplicações e serviços.

### <a name="how-can-i-control-these-capabilities"></a>Como posso controlar estas capacidades?
Um administrador pode configurar estas capacidades utilizando estes parâmetros do cmdlet Set-MsolCompanySettings do Azure AD:

* **AllowEmailVerifiedUsers** controla se um utilizador pode criar ou associar um diretório de não gerido. Se definir esse parâmetro para $false, nenhum utilizador verificado de e-mail pode associar o diretório.
* **AllowAdHocSubscriptions** controla a capacidade dos utilizadores efetuar o início de sessão personalizado cópias de segurança. Se definir esse parâmetro para $false, nenhum utilizador pode efetuar a inscrição self-service.

### <a name="how-do-the-controls-work-together"></a>Como os controlos funcionam em conjunto?
Estes dois parâmetros podem ser utilizados em conjunto para definir o controlo mais preciso sobre o início de sessão personalizado cópias de segurança. Por exemplo, o comando seguinte irá permitir aos utilizadores efetuar o início de sessão personalizado, mas apenas se esses utilizadores já tiverem uma conta no Azure AD (por outras palavras, os utilizadores que teriam uma conta de e-mail verificada a ser criado não é possível efetuar self-service sessão cópias de segurança):

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

O fluxograma a seguir explica todas as diferentes combinações para estes parâmetros e as condições resultantes para o diretório e o início de sessão personalizado cópias de segurança.

![][1]

Para obter mais informações e exemplos de como utilizar estes parâmetros, consulte [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="see-also"></a>Veja Também
* [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referência de Cmdlet do Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
