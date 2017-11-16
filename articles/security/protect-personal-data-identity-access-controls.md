---
title: Proteger os dados pessoais com controlos de identidades e acessos do Azure | Microsoft Docs
description: Utilizar a identidade do Azure e acesso controla para o ajudar a proteger os seus dados pessoais
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: e6de9526a1a72cfc81caca51207e000f8b3673cc
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-active-directory-and-multi-factor-authentication-protect-personal-data-with-identity-and-access-controls"></a>Azure Active Directory e o multi-factor Authentication: proteger os dados pessoais com controlos de identidades e acessos

Este artigo fornece informações e procedimentos que pode utilizar para proteger os dados pessoais utilizar os serviços e funcionalidades de segurança do Azure Active Directory e o multi-factor authentication.

## <a name="scenario"></a>Cenário

Uma empresa de grande cruise headquartered nos Estados Unidos, está a expandir as suas operações para oferecer itineraries no Mediterranean, Adriatic e Baltic seas, bem como o Isles território. Para suportar os esforços, obteve várias linhas cruise mais pequenas com base em (Itália), na Alemanha, Dinamarca e o U.K. 

A empresa utiliza o Microsoft Azure para armazenar dados empresariais na nuvem. Isto inclui informações pessoais tais como nomes, endereços, números de telefone e informações de cartão de crédito da respetiva base de cliente global. Também inclui informações de recursos humanos tradicionais, como endereços, números de telefone, os números de identificação de dedução dos impostos e outras informações sobre os funcionários da empresa em todas as localizações. A linha cruise mantém também uma grande base de dados de membros de programa reward e loyalty que inclui informações pessoais, para controlar as relações com clientes atuais e anteriores.

Os funcionários empresarias acedam à rede de escritórios remotos da empresa e agentes levar localizados em todo o mundo tem acesso a alguns recursos da empresa.

## <a name="problem-statement"></a>Declaração do problema

A empresa tem de proteger a privacidade dos dados pessoais dos clientes e dos empregados de atacantes pesquisa utilizar identidades comprometidas para obter acesso. Estes também tem de garantir que o acesso a dados pessoais pelos utilizadores legítimos é restringido aos apenas a quem será necessário efetuar as respetivas tarefas.

## <a name="company-goal"></a>Objetivo da empresa

Objetivo da empresa é certificar-se de que o acesso a dados pessoais estritamente é controlado. É essencial que as identidades de utilizadores com acesso a dados pessoais estar protegido por autenticação forte. Uma política do [menor privilégio] (https://en.wikipedia.org/wiki/Principle_of_least_privilege) tem de ser imposta para que os utilizadores legítimos tenham apenas o nível de acesso que precisam e não existem mais.

## <a name="solutions"></a>Soluções

Microsoft Azure fornece ferramentas de gestão de identidades e acessos para ajudar a controlar as empresas que tem acesso a recursos que contêm dados pessoais.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (AAD) gere as identidades e controla o acesso ao Azure, bem como outro no local e outros recursos na nuvem, dados e aplicações. [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) ajuda os administradores do Azure para minimizar o número de pessoas que têm acesso para determinadas informações tais como os dados pessoais. Permite que os mesmos possam detetar, restringir e monitorizar as identidades privilegiadas e o respetivo acesso aos recursos e atribuir temporário, direitos de administrativos just (JIT) para os utilizadores elegíveis. Também fornece informações sobre os utilizadores que possuem os privilégios administrativos AAD.

As atividades envolvidas no utilizando AAD PIM incluem:

- Ativar o Privileged Identity Management para o seu diretório

- Utilizar o dashboard de administração de Privileged Identity Management para ver informações importantes de forma rápida

- Gerir identidades privilegiadas (administradores) adicionando ou removendo administradores permanentes ou elegíveis para cada função

- Configurar as definições de ativação de função

- Ativar funções

- Rever a actividade de função

#### <a name="how-do-i-enable-aad-pim"></a>Como ativar a PIM do AAD?

Para começar a utilizar o PIM para o seu diretório, efetue o seguinte:

1. Inicie sessão no portal do Azure como um administrador global do seu diretório.

2. Se a organização tiver mais do que um diretório, selecione o nome de utilizador no canto superior direito do Portal do Azure. Selecione o diretório onde utilizará o Azure AD Privileged Identity Management.

3. Selecione **mais serviços** e utilizar o **filtro** caixa de texto para procurar o Azure AD Privileged Identity Management.

4. Marque **Afixar ao dashboard** e, em seguida, clique em **Criar**. A aplicação Privileged Identity Management abre.

Assim que o Azure AD Privileged Identity Management estiver configurado, verá o painel de navegação sempre que abrir a aplicação.

![](media/protect-personal-data-identity-access-controls/azure-pim.png)

Para obter mais informações e instruções sobre como começar a PIM do AAD, consulte [começar a utilizar o Azure AD Privileged Identity Management.](https://docs.microsoft.com/active-directory/active-directory-privileged-identity-management-getting-started)

### <a name="azure-role-based-access-control"></a>Controlo de acesso baseado em funções do Azure

[Controlo de acesso baseado em funções do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) ajuda os administradores do Azure, gerir o acesso aos recursos do Azure, permitindo a concessão de permissões de acesso com base na função atribuída ao utilizador. Pode segregar funções dentro de uma equipa e conceder apenas a quantidade de acesso a utilizadores, grupos e aplicações que precisam para desempenhar as suas funções.

O acesso baseado em funções pode ser concedido aos utilizadores através do portal do Azure, das ferramentas da Linha de Comandos do Azure ou de APIs de Gestão do Azure.

Para obter mais informações sobre noções básicas do RBAC do Azure, consulte [introdução ao controlo de acesso baseado em funções no Portal do Azure.](https://docs.microsoft.com/active-directory/role-based-access-control-what-is)

#### <a name="how-do-i-manage-azure-rbac-with-powershell"></a>Como gerir o RBAC do Azure com o PowerShell

Pode utilizar os cmdlets do PowerShell para gerir o RBAC do Azure, incluindo as seguintes tarefas de gestão:

- Lista de funções

- Ver quem tem acesso

- Conceder acesso

- Remover o acesso

- Criar uma função personalizada

- Obter as ações de um fornecedor de recursos

- Modificar uma função personalizada

- Eliminar uma função personalizada

- Lista de funções personalizadas

Para obter instruções sobre como gerir o RBAC do Azure com o PowerShell, consulte [baseada em funções gerir acesso com o Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell).

### <a name="azure-multi-factor-authentication"></a>Multi-Factor Authentication do Azure

[Multi-factor Authentication do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/) (MFA) é uma solução de verificação de dois passos que ajuda a salvaguardar o acesso a dados e aplicações, cumprindo o pedido do utilizador para um processo de início de sessão simple. Oferece autenticação forte através de vários métodos de verificação, incluindo chamada telefónica, mensagem de texto ou verificação de aplicação móvel.

Para implementar o MFA na nuvem do Azure, terá de ativar primeiro e, em seguida, ative a verificação em dois passos para os utilizadores.

#### <a name="how-do-i-enable-azure-to-use-mfa"></a>Como ativar o Azure para utilizar a MFA?

Se os utilizadores têm de licenças que incluem o Azure multi-factor Authentication, basta terá de configurar a MFA do Azure num por utilizador ou a base de grupo. 

![Utilizadores MFA ativada](media/protect-personal-data-identity-access-controls/enable-mfa.png)

Se não tiver licenças atualmente tem de aceder através do processo de determinar o tipo de implementação mais adequado para o seu cenário. Pode começar ao observar o artigo intitulado [escolher a solução de Azure multi-factor Autehntication para si](../multi-factor-authentication/multi-factor-authentication-get-started.md). Se decidir que precisa de criar um servidor multi-factor Authentication. Pode iniciar, seguindo estes passos:

1. Selecione **do Active Directory** no portal do Azure (com sessão iniciado como administrador).

2. Selecione **servidor MFA**

3. Especifique um valor de tempo limite. 

    ![](media/protect-personal-data-identity-access-controls/mfa-server-settings.png)

4. Clique em **guardar**

Nesta janela também tem a opção para transferir o servidor MFA. Pode obter detalhes adicionais sobre como o tamanho e planear a implementação, revendo o artigo [introdução com o servidor multi-factor Authentication do Azure](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)

Para obter mais instruções sobre como gerir o seu fornecedor de autenticação Multifator, consulte [introdução de um fornecedor do multi-factor Auth do Azure.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-auth-provider)

#### <a name="how-do-i-turn-on-two-step-verification-for-users"></a>Como ativar verificação em dois passos para os utilizadores?

Pode impor a verificação de dois passos para todos os inícios de sessão ou pode criar políticas de acesso condicional para exigir a verificação apenas quando aplicam condições específicas.

Ativar a MFA do Azure, alterando os Estados de utilizador é a abordagem tradicional para exigir a verificação de dois passos. Todos os utilizadores que ativar terão o mesmo requisito para efetuar a verificação de cada vez que iniciar sessão. Ativar um utilizador substitui quaisquer políticas de acesso condicional que podem afetar esse utilizador.

Ativar a MFA do Azure com uma política de acesso condicional é uma abordagem mais flexível para exigir a verificação de dois passos. Pode criar políticas de acesso condicional que se aplicam a grupos, bem como os utilizadores individuais. Grupos de alto risco podem ser especificados mais restrições de grupos de baixo risco, ou pode ser necessário apenas para aplicações na nuvem de alto risco e ignorada para baixo risco aqueles verificação em dois passos. No entanto, o acesso condicional é uma funcionalidade paga do Azure Active Directory.

Para ativar a MFA alterando o estado do utilizador, efetue o seguinte:

1. Inicie sessão no portal do Azure como administrador.
2. Aceda a **do Azure Active Directory \> utilizadores e grupos \> todos os utilizadores**.
3. Selecione **multi-factor Authentication**.
4. Localize o utilizador que pretende ativar a MFA do Azure. Pode ter de alterar a vista na parte superior.
5. Selecione a caixa junto ao nome do utilizador.
6. À direita, em passos rápidos, escolha **ativar**.

   ![](media/protect-personal-data-identity-access-controls/mfa-bulk.png)

7. Confirme a sua selecção na janela de pop-up que é aberta.  Os utilizadores para o qual o MFA tiver sido ativada serão solicitados que registe da próxima vez que iniciar sessão.

Para ativar a MFA do Azure com uma política de acesso condicional, efetue o seguinte:

1. Inicie sessão no portal do Azure como administrador.

2. Aceda a **do Azure Active Directory \> acesso condicional**.

3. Selecione **nova política**.

4. Em **atribuições**, selecione **utilizadores e grupos**. Utilize o **incluir** e **excluir** separadores para especificar quais os utilizadores e grupos serão geridos pela política.

5. Em **atribuições**, selecione **aplicações em nuvem.** Optar por **incluem todas as aplicações em nuvem**.
6.  Em **controlos de acesso**, selecione **conceder**. Escolha **requer autenticação multifator**.
7.  Ativar **ativar política de** para **no** e, em seguida, selecione **guardar**.

Para informações sobre como configurar as definições da MFA do Azure para configurar alertas de fraude, crie uma omissão de uso individual, utilizar mensagens de voz personalizadas, configurar a colocação em cache, especifique os IPs fidedignos, criar palavras-passe de aplicação, ativar a memorizar MFA para dispositivos que os utilizadores de confiança, selecione métodos de verificação, consulte [configurar definições de autenticação de multi-factor do Azure.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)

## <a name="next-steps"></a>Passos seguintes

- [Proteger o acesso privilegiado no Azure AD](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)

- [Perguntas mais frequentes sobre o Multi-Factor Authentication do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-faq)

- [Resolução de problemas de controlo de acesso baseado em funções](https://docs.microsoft.com/azure/active-directory/role-based-access-control-troubleshooting)

- [Proteção de identidade do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
