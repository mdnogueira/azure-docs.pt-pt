---
title: Acesso condicional do Azure Active Directory | Microsoft Docs
description: "Utilize o controlo de acesso condicional no Azure Active Directory para procurar condições específicas, durante a autenticação para acesso a aplicações."
services: active-directory
keywords: "acesso condicional para aplicações, o acesso condicional com o Azure AD, o acesso seguro aos recursos da empresa, as políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/27/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 4cf30130907151ade9eaf9db28748b8141dac8e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="conditional-access-in-azure-active-directory"></a>Acesso condicional no Azure Active Directory

No mundo mobile-primeiro, primeiro de nuvem, o Azure Active Directory permite início de sessão único para dispositivos, aplicações e serviços em qualquer lugar. Com a proliferação de dispositivos (incluindo o BYOD), trabalham em redes empresariais e aplicações de SaaS de terceiros 3rd, profissionais de TI são confrontadas com dois objetivos adversária:

- Capacitar os utilizadores finais para serem produtivos, onde quer que e sempre
- Proteger os ativos da empresa em qualquer altura

Para melhorar a produtividade, o Azure Active Directory fornece os seus utilizadores com uma vasta gama de opções para aceder os recursos da empresa. Gestão de acesso de aplicações, o Azure Active Directory permite-lhe assegurar que apenas *as pessoas corretas* possam aceder às suas aplicações. E se pretende ter mais controlo sobre a forma como as pessoas corretas estão a aceder os recursos em determinadas condições? E se tiver mesmo condições sob as quais pretende bloquear o acesso a algumas aplicações, mesmo para o *com o botão direito pessoas*? Por exemplo, poderá ser OK para si se as pessoas corretas estão a aceder a determinadas aplicações de uma rede fidedigna; No entanto, não poderá-os para aceder a estas aplicações a partir de uma rede que não fidedigna. Pode resolver estas questões, utilizando o acesso condicional.

Acesso condicional é uma funcionalidade do Azure Active Directory permite-lhe aplicar controlos de acesso a aplicações no seu ambiente com base nas condições específicas. Com controlos, ou pode juntar requisitos adicionais para o acesso ou pode bloqueá-lo. A implementação de acesso condicional é com base nas políticas. Uma abordagem baseada em políticas simplifica a sua experiência de configuração porque segue a forma que tem em consideração os requisitos de acesso.  

Normalmente, definir os requisitos de acesso utilizando as instruções que se baseiam o padrão do seguinte:

![Controlo](./media/active-directory-conditional-access-azure-portal/10.png)

Quando substituir dois ocorrências de "*isto*" com as informações do mundo real, tiver um exemplo de uma declaração de política que provavelmente procura parecer-lhe:

*Quando contratantes estão a tentar aceder à nossa aplicações na nuvem de redes que não são fidedignas, bloquear o acesso.*

Declaração de política acima destaca potência de acesso condicional. Enquanto pode ativar contratantes para basicamente aceder às suas aplicações em nuvem (**quem**), com o acesso condicional, também pode definir condições sob as quais o acesso é possível (**como**).

No contexto do acesso condicional do Azure Active Directory,

- "**Quando isto acontece**" denomina **declaração de condição**
- "**, Em seguida, fazer isto**" denomina **controlos**

![Controlo](./media/active-directory-conditional-access-azure-portal/11.png)

A combinação de uma instrução de condição com os controlos representa uma política de acesso condicional.

![Controlo](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>Controlos

Uma política de acesso condicional, controlos de definem o que é que deve acontecer quando uma instrução de condição foram satisfeita.  
Com controlos, pode bloquear o acesso ou permitir o acesso com requisitos adicionais.
Quando configura uma política que permite o acesso, tem de selecionar pelo menos um requisito.  

Existem dois tipos de controlos: 

- **Controla a conceder** -controlos de conceder governem se ou não pode concluir a autenticação e aceder o recursos que está a tentar iniciar sessão para um utilizador. Se tiver vários controlos selecionados, pode configurar se todos eles são necessários quando a política for processada.
A implementação atual do Azure Active Directory permite-lhe configurar os seguintes requisitos de controlo da concessão:

    ![Controlo](./media/active-directory-conditional-access-azure-portal/73.png)

- **Controlos de sessão** -sessão controla ativar a limitação de experiência de uma aplicação de nuvem. Os controlos de sessão são impostos pelo aplicações na nuvem e dependem das informações adicionais fornecidas pelo Azure AD para a aplicação sobre a sessão.

    ![Controlo](./media/active-directory-conditional-access-azure-portal/31.png)


Para obter mais informações, consulte [controlos no Azure Active Directory condicional acesso](active-directory-conditional-access-controls.md).


## <a name="condition-statement"></a>Declaração de condição

A secção anterior tem introduzidos as opções suportadas para bloquear ou restringir o acesso aos seus recursos no formulário de controlos. Na política de acesso condicional, é possível definir os critérios que precisam de ser cumpridas para que os controlos sejam aplicadas no formulário de uma instrução de condição.  

Pode incluir as seguintes atribuições para a sua declaração de condição:

![Controlo](./media/active-directory-conditional-access-azure-portal/07.png)


### <a name="who"></a>Who?

Quando configura uma política de acesso condicional, tem de selecionar os utilizadores ou grupos que a política se aplica. Em muitos casos, pretende que os controlos sejam aplicadas a um conjunto específico de utilizadores. Uma instrução de condição, pode definir este conjunto selecionando os necessários utilizadores e grupos que a política se aplica. Se necessário, também explicitamente pode excluir um conjunto de utilizadores da sua política, exempting-los.  

![Controlo](./media/active-directory-conditional-access-azure-portal/08.png)



### <a name="what"></a>What?

Quando configura uma política de acesso condicional, tem de selecionar as aplicações em nuvem, que a política se aplica.
Normalmente, existem determinadas aplicações no seu ambiente que necessitam de uma perspetiva de proteção, a mais atenção que outros. Este procedimento afeta, por exemplo, as aplicações que têm acesso a dados confidenciais.
Ao selecionar as aplicações na nuvem, é possível definir o âmbito da sua política se aplica a aplicações na nuvem. Se necessário, pode excluir explicitamente também um conjunto de aplicações da sua política.

![Controlo](./media/active-directory-conditional-access-azure-portal/09.png)

Para obter uma lista completa das aplicações em nuvem que pode utilizar na sua política de acesso condicional, consulte o [referência técnica do Azure Active Directory condicional acesso](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).

### <a name="how"></a>Como?

Enquanto aceder às suas aplicações é efetuada em condições que pode controlar, poderá ser necessário para impor controlos adicionais sobre como as suas aplicações em nuvem são acedidas pelos seus utilizadores. No entanto, coisas podem parecer diferentes se aceder às suas aplicações em nuvem é efetuada, por exemplo, de redes que não são fidedignas ou dispositivos que não são compatíveis. Uma instrução de condição, pode definir determinadas condições de acesso que têm requisitos adicionais para a forma como é efetuada a aceder às suas aplicações.

![Condições](./media/active-directory-conditional-access-azure-portal/01.png)


## <a name="conditions"></a>Condições

Na implementação atual do Azure Active Directory, pode definir condições para as seguintes áreas:

- Risco de início de sessão
- Plataformas de dispositivos
- Localizações
- Aplicações de cliente


![Condições](./media/active-directory-conditional-access-azure-portal/01.png)

### <a name="sign-in-risk"></a>Risco de início de sessão

Um risco de início de sessão é um objeto que é utilizado pelo Azure Active Directory para controlar a probabilidade de tentativa de um início de sessão não foi efetuado pelo proprietário de uma conta de utilizador legítimo. Este objeto, a probabilidade (alta, média ou baixa) é armazenada em formato de um atributo chamado [ao nível do risco de início de sessão](active-directory-reporting-risk-events.md#risk-level). Este objeto é gerado durante um início de sessão de um utilizador se o início de sessão riscos foram detetados pelo Azure Active Directory. Para obter mais informações, veja [Inícios de sessão arriscados](active-directory-identityprotection.md#risky-sign-ins).  
Pode utilizar o nível de risco de início de sessão calculados como condição numa política de acesso condicional. 

![Condições](./media/active-directory-conditional-access-azure-portal/22.png)

### <a name="device-platforms"></a>Plataformas de dispositivos

A plataforma do dispositivo é caracterizada pelo sistema operativo que está a executar no seu dispositivo:

- Android
- iOS
- Windows Phone
- Windows
- macOS (pré-visualização). 

![Condições](./media/active-directory-conditional-access-azure-portal/02.png)

Pode definir as plataformas de dispositivos que estão incluídas, bem como as plataformas de dispositivos que são excluídas de uma política.  
Para utilizar plataformas de dispositivos na política, alterar os botões de alternar configurar para **Sim**e, em seguida, selecione todos os ou plataformas de dispositivos individuais a política se aplica. Se selecionar plataformas de dispositivos individuais, a política tem apenas um impacto nas plataformas seguintes. Neste caso, os inícios de sessão para outras plataformas suportadas não são afetados pela política.


### <a name="locations"></a>Localizações

Com as localizações, tem a opção para definir condições que se baseiam em que uma tentativa de ligação foi iniciada a partir da. As entradas na lista de localizações são **denominado localizações** ou **MFA IPs fidedignos**.  

**Com o nome localizações** é uma funcionalidade do Azure Active Directory permite-lhe definir as etiquetas para foram efetuadas tentativas de ligação a localizações de. Para definir uma localização, pode optar por configurar um IP intervalos de endereços ou selecione um país / região.  

![Condições](./media/active-directory-conditional-access-azure-portal/42.png)

Além disso, pode marcar uma localização nomeada como localização fidedigna. Para uma política de acesso condicional, a localização fidedigna é outra opção de filtro que permite selecionar *todas as localizações de fidedignas* a condição de localizações.
Localizações com nome também são importantes no contexto da deteção de [eventos de risco](active-directory-reporting-risk-events.md) para reduzir o número de Falso-positivos para deslocação impossível para eventos de risco localizações atípicas. 

O número de localizações com nome, que pode configurar é limitado pelo tamanho do objeto relacionado no Azure AD. Pode configurar:
 
 - Uma localização com o nome com até 500 intervalos IP
 - Um máximo de 60 localizações com nome (pré-visualização) com um intervalo IP atribuídos a cada um deles 

Para obter mais informações, consulte [localizações no Azure Active Directory com o nome](active-directory-named-locations.md).


**IPs fidedignos do MFA** é uma funcionalidade de autenticação multifator que lhe permite definir fidedignos intervalos de endereços IP que representa a intranet local da sua organização. Quando configura uma condição de localização, o IPs fidedignos permite-lhe distinguir entre ligações efetuadas a partir da rede da sua organização e todas as outras localizações. Para obter mais informações, consulte [IPs fidedignos](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  

Na sua política de acesso condicional, pode:

- Incluir
    - Qualquer localização
    - Todas as localizações de fidedignas
    - Localizações selecionadas
- Excluir
    - Todas as localizações de fidedignas
    - Localizações selecionadas
     
![Condições](./media/active-directory-conditional-access-azure-portal/03.png)


### <a name="client-apps"></a>Aplicações de cliente

A aplicação cliente pode ser num nível genérico a aplicação (browser da web, aplicação móvel, cliente de ambiente de trabalho) que utilizou para ligar ao Azure Active Directory ou pode selecionar especificamente Exchange Active Sync.  
Autenticação legada refere-se aos clientes que utilizam a autenticação básica, tais como clientes mais antigos do Office que não utilizam autenticação moderna. Acesso condicional não é atualmente suportado com a autenticação de legado.

![Condições](./media/active-directory-conditional-access-azure-portal/04.png)


Para obter uma lista completa das aplicações de cliente pode utilizar na sua política de acesso condicional, consulte o [referência técnica do Azure Active Directory condicional acesso](active-directory-conditional-access-technical-reference.md#client-apps-condition).




## <a name="common-scenarios"></a>Cenários comuns

### <a name="requiring-multi-factor-authentication-for-apps"></a>Exigir a autenticação multifator para aplicações

As aplicações que requerem um nível mais elevado de proteção que as outras de ter vários ambientes.
Isto é, por exemplo, as maiúsculas e minúsculas para aplicações que tenham acesso a dados confidenciais.
Se pretender adicionar outra camada de proteção para estas aplicações, pode configurar uma política de acesso condicional que requer autenticação multifator, quando os utilizadores estão a aceder a estas aplicações.


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>Exigir a autenticação multifator para acesso a partir de redes que não são fidedignas

Este cenário é semelhante ao cenário anterior porque adiciona um requisito de autenticação multifator.
No entanto, a principal diferença é a condição para este requisito.  
Enquanto o foco do cenário anterior foi em aplicações com o acesso aos dados sensitve, o foco deste cenário é fidedignas localizações.  
Por outras palavras, pode ter um requisito de autenticação multifator se aceder a uma aplicação por um utilizador de uma rede que não fidedigna.


### <a name="only-trusted-devices-can-access-office-365-services"></a>Apenas os dispositivos fidedignos podem aceder a serviços do Office 365

Se estiver a utilizar o Intune no seu ambiente, pode começar imediatamente a utilizar a interface de política de acesso condicional na consola do Azure.

Muitos clientes do Intune estão a utilizar acesso condicional para garantir que apenas os dispositivos fidedignos podem aceder a serviços do Office 365. Isto significa que os dispositivos móveis estão inscritos no Intune e cumpram os requisitos da política de conformidade e de que os PCs Windows estão associados a um domínio no local. Uma melhoria de chave é que não têm de definir a mesma política para cada um dos serviços do Office 365.  Quando cria uma nova política, configure as aplicações em nuvem para incluir cada uma das aplicações do Office 365 que pretende proteger com o acesso condicional.

## <a name="next-steps"></a>Passos seguintes

- Se quiser saber como configurar uma política de acesso condicional, consulte [introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte o [melhores práticas para acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md). 
