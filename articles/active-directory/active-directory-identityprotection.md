---
title: "Proteção de identidade do Azure Active Directory | Microsoft Docs"
description: Saiba como o Azure AD Identity Protection lhe permite limitar a capacidade de um atacante para explora um dispositivo ou identidade comprometida e para proteger uma identidade ou um dispositivo que anteriormente era suspeito ou conhecido for comprometida.
services: active-directory
keywords: "proteção de identidade do Azure Active Directory, o cloud app discovery, gestão de aplicações, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: a4473f25fefd65de75996240955302abb08762da
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

Azure Active Directory Identity Protection é uma funcionalidade da edição do Azure AD Premium P2 que lhe permite:

- Detetar potenciais vulnerabilidades que afetam as identidades da organização

- Configurar respostas de automatizada para ações suspeitas detetadas que estão relacionados com as identidades da organização  

- Investigue incidentes suspeitas e tome as medidas necessárias resolvê-los   


## <a name="getting-started"></a>Introdução

Microsoft tem seguros identidades baseado na nuvem para a mais do que um decade. Com o Azure Active Directory Identity Protection, no seu ambiente, pode utilizar os sistemas de proteção mesmo, a Microsoft utiliza para proteger as identidades.

A grande maioria das falhas de segurança ocorrer quando os atacantes ganham acesso a um ambiente roubo de identidade do utilizador. Ao longo dos anos, os atacantes tem tornar-se cada vez mais eficientes tirar partido das violações de terceiros e utilização de ataques de phishing sofisticadas. Assim que um atacante obtiver acesso ao mesmo de contas de utilizador com privilégios baixa, é relativamente fácil de obter acesso a recursos importantes da empresa através de movimento lateral.

Como consequência, tem de:

- Proteger todas as identidades independentemente do respetivo nível de privilégios

- Proativamente impedir identidades comprometidas que está a ser abused

Detetar identidades comprometidas não é nenhuma tarefa fácil. Azure Active Directory utiliza adaptável algoritmos de aprendizagem automática e heurística para detetar anomalias e incidentes suspeitas que indiquem potencialmente comprometido identidades. Utilizando estes dados, Identity Protection gera relatórios e alertas que lhe permite avaliar os problemas detetados e demorar mitigação adequada ou ações de remediação.

Azure Active Directory Identity Protection é maior do que uma monitorização e a ferramenta de relatórios. Para proteger as identidades da organização, pode configurar as políticas baseadas em risco que respondam automaticamente a problemas detetados quando for atingido a um nível de risco especificado. Automaticamente estas políticas, além de outros controlos de acesso condicional fornecidas pelo Azure Active Directory e o EMS, podem bloquear ou iniciar ações de remediação adaptável repõe a palavra-passe, incluindo e imposição de autenticação multifator.


#### <a name="identity-protection-capabilities"></a>Capacidades de proteção de identidade

**Detetar vulnerabilidades e contas de risco:**  

* Fornecer recomendações personalizadas para melhorar a postura de segurança geral por realce vulnerabilidades
* A calcular níveis de risco de início de sessão
* A calcular níveis de risco do utilizador


**Investigar os eventos de risco:**

* Enviar notificações para eventos de risco
* Investigar os eventos de risco utilizando informações relevantes e contextuais
* Fornecer os fluxos de trabalho básicos para controlar as investigações
* Fornecer o facilitar o acesso para ações de remediação, tais como a reposição de palavra-passe

**Políticas de acesso condicional baseado em risco:**

* Política para mitigar o risco de inícios de sessão por bloquear inícios de sessão ou exigindo desafios de autenticação multifator
* Política para bloquear ou contas de utilizador de risco segura
* Política para exigir que os utilizadores para se registar para autenticação multifator



## <a name="identity-protection-roles"></a>Funções de proteção de identidade

Para o balanceamento de carga as atividades de gestão em torno da sua implementação Identity Protection, pode atribuir várias funções. Azure AD Identity Protection suporta 3 funções do diretório:

| Função                         | Pode fazê-lo                          | Não é possível efetuar
| :--                          | ---                                |  ---   |
| Administrador global         | Acesso completo a proteção de identidade, carregar Identity Protection| |
| Administrador de segurança       | Acesso total ao Identity Protection | Carregar Identity Protection, repor palavras-passe para um utilizador |
| Leitor de segurança              | Acesso só de leitura para Identity Protection | Carregar Identity Protection, remidiate utilizadores, configurar políticas, repor palavras-passe |




Para obter mais detalhes, consulte [atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)



## <a name="detection"></a>Deteção

### <a name="vulnerabilities"></a>Vulnerabilidades

Azure Active Directory Identity Protection analyses a configuração e Deteta que pode ter um impacto nas identidades dos utilizadores. Para obter mais detalhes, consulte [vulnerabilidades detetadas pelo Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md).

### <a name="risk-events"></a>Eventos de risco

Azure Active Directory utiliza adaptável algoritmos de aprendizagem automática e heurística para detetar as ações suspeitas que estão relacionados com as identidades dos utilizadores. O sistema cria um registo para cada ação suspeito detetado. Estes registos são também conhecidos como eventos de risco.  
Para obter mais detalhes, veja [Eventos de risco do Azure Active Directory](active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Investigação
Da sua viagem através da proteção de identidade normalmente começa com o dashboard de Identity Protection.

![Remediação](./media/active-directory-identityprotection/1000.png "remediação")

O dashboard fornece acesso ao:

* Os relatórios tal como **utilizadores sinalizados para risco**, **eventos de risco** e **vulnerabilidades**
* Definições, tais como a configuração do seu **políticas de segurança**, **notificações** e **registo de autenticação multifator**

É, geralmente, o ponto de partida para investigação, que é o processo de rever as atividades, registos e outras informações relevantes relacionados com um evento de risco para decidir se os passos de remediação ou atenuação são necessários, e como a identidade foi comprometido e compreender a forma como a identidade comprometida foi utilizada.

Pode associar as atividades de investigação para o [notificações](active-directory-identityprotection-notifications.md) do Azure Active Directory proteção enviar por e-mail.

As secções seguintes lhe fornecem mais detalhes e os passos que estão relacionados com uma investigação.  


## <a name="risky-sign-ins"></a>Risco inícios de sessão

Azure Active Directory Deteta [tipos de eventos de risco](active-directory-reporting-risk-events.md#risk-event-types) no offline e em tempo real. Cada evento de risco que foi detetado para um início de sessão de um utilizador contribui para um conceito lógico chamado arriscado início de sessão. Um risco início de sessão é um indicador para uma tentativa de início de sessão não pode ter sido efetuada pelo proprietário de uma conta de utilizador legítimo.


### <a name="sign-in-risk-level"></a>Nível de risco de início de sessão

Um nível de risco de início de sessão é uma indicação (alta, média ou baixa) a probabilidade que uma tentativa de início de sessão não foi efetuada pelo proprietário de uma conta de utilizador legítimo.

### <a name="mitigating-sign-in-risk-events"></a>Mitigar o risco de início de sessão de eventos

Uma mitigação é uma ação para limitar a capacidade de um atacante exploram uma identidade comprometida ou dispositivo sem restaurar o dispositivo ou a identidade para um Estado de segurança. Uma mitigação não resolver eventos de início de sessão risco anteriores associados o identidade ou o dispositivo.

Para mitigar o risco de inícios de sessão automaticamente, pode configurar policicies de segurança de início de sessão de risco. Utilizar estas políticas, considere o nível de risco do utilizador ou o início de sessão para bloquear o risco de inícios de sessão ou exigir que o utilizador para efetuar a autenticação multifator. Estas ações podem impedir que um atacante explorá uma identidade roubada para causar danos e poderão dar-lhe algum tempo para proteger a identidade.

### <a name="sign-in-risk-security-policy"></a>Política de segurança de início de sessão risco
Uma política de início de sessão risco é uma política de acesso condicional que avalia o risco um específico início de sessão e aplica-se mitigações com base nas condições predefinidas e as regras.

![Política de início de sessão risco](./media/active-directory-identityprotection/1014.png "início de sessão da política de risco")

Azure AD Identity Protection ajuda a gerir a mitigação de risco inícios de sessão, permitindo:

* Defina os utilizadores e grupos que a política aplica-se a:

    ![Política de início de sessão risco](./media/active-directory-identityprotection/1015.png "início de sessão da política de risco")
* Defina o risco de início de sessão nível limiar (baixa, média ou alta) que aciona a política:

    ![Política de início de sessão risco](./media/active-directory-identityprotection/1016.png "início de sessão da política de risco")
* Defina os controlos para ser imposta quando aciona a política:  

    ![Política de início de sessão risco](./media/active-directory-identityprotection/1017.png "início de sessão da política de risco")
* Mude o estado da sua política:

    ![Registo do MFA](./media/active-directory-identityprotection/403.png "registo do MFA")
* Rever e avaliar o impacto de uma alteração antes de ativar esta:

    ![Política de início de sessão risco](./media/active-directory-identityprotection/1018.png "início de sessão da política de risco")

#### <a name="what-you-need-to-know"></a>O que precisa de saber
Pode configurar uma política de segurança de início de sessão risco para exigir a autenticação multifator:

![Política de início de sessão risco](./media/active-directory-identityprotection/1017.png "início de sessão da política de risco")

No entanto, por motivos de segurança, esta definição só funciona para os utilizadores que já foram registados para autenticação multifator. Se a condição para exigir a autenticação multifator é satisfeita para um utilizador que ainda não está registado para multi-factor authentication, o utilizador está bloqueado.

Como melhor prática, se pretende exigir a autenticação multifator para inícios de sessão risco, deve:

1. Ativar o [política de registo de autenticação multifator](#multi-factor-authentication-registration-policy) para os utilizadores afetados.
2. Exigir os que os utilizadores afetados início de sessão numa sessão não arriscados para efetuar um registo do MFA

Concluir estes passos garante que a autenticação multifator é necessária para um risco início de sessão.

#### <a name="best-practices"></a>Melhores práticas
Escolher um **elevada** limiar reduz o número de vezes que uma política é acionada e minimiza o impacto para os utilizadores.  

No entanto, exclui **baixa** e **média** inícios de sessão sinalizados para risco da política, que não pode bloquear um atacante de explorá uma identidade comprometida.

Quando definir a política

* Excluir os utilizadores que não o fizer / não podem ter a autenticação multifator
* Excluir utilizadores nas regiões onde o ativar a política não é prático (por exemplo, sem acesso para o suporte técnico)
* Excluir os utilizadores que se prevê a gerar uma grande quantidade de Falso-positivos (programadores, os analistas de segurança)
* Utilize um **elevada** limiar durante a agregação de política iniciais, ou se tem minimizar desafios vistos pelos utilizadores finais.
* Utilize um **baixa** limiar se a sua organização precisar de maior segurança. Selecionar um **baixa** limiar apresenta utilizador adicionais início de sessão desafios, mas uma maior segurança.

A predefinição recomendada na maioria das organizações é para configurar uma regra para um **média** limiar para trazem um equilíbrio entre capacidade de utilização e segurança.

A política de início de sessão risco é:

* Aplicar a todos os tráfego do browser e inícios de sessão que utilizam autenticação moderna.
* Não aplicado a aplicações através de protocolos de segurança mais antigos, desativando o ponto final de WS-Trust no IDP federado, tais como o ADFS.

O **eventos de risco** página na consola do Identity Protection apresenta uma lista de todos os eventos:

* Esta política foi aplicada ao
* Pode rever a atividade e determinar se a ação foi adequada ou não

Para obter uma descrição geral da experiência de utilizador relacionadas, consulte:

* [Recuperação de risco início de sessão](active-directory-identityprotection-flows.md#risky-sign-in-recovery)
* [Risco início de sessão bloqueado](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  
* [Início de sessão experiências com o Azure AD Identity Protection](active-directory-identityprotection-flows.md)  

**Para abrir a caixa de diálogo de configuração relacionados**:

- No **do Azure AD Identity Protection** painel, no **configurar** secção, clique em **início de sessão da política de risco**.

    ![Política do utilizador ridk](./media/active-directory-identityprotection/1014.png "ridk de política de utilizador")



## <a name="users-flagged-for-risk"></a>Utilizadores sinalizados para risco

Todas as ativas [eventos de risco](active-directory-identity-protection-risk-events.md) que foram detetados pelo Azure Active Directory para um utilizador contribuir para um conceito lógico chamado risco do utilizador. Um utilizador sinalizado para risco é um indicador de uma conta de utilizador que possam ter sido comprometido.

![Utilizadores sinalizados para risco](./media/active-directory-identityprotection/1200.png)


### <a name="user-risk-level"></a>Nível de risco do utilizador

Um nível de risco do utilizador é uma indicação (alta, média ou baixa) a probabilidade de que a identidade do utilizador foi comprometida. Esta é calculada com base nos eventos de risco de utilizador que estão associados a identidade do utilizador.

O estado de um evento de risco é **Active Directory** ou **fechado**. Apenas os eventos que são de risco **Active Directory** contribuir para o cálculo de nível de risco de utilizador.

O nível de risco do utilizador é calculado com as seguintes entradas:

* Eventos de risco Active Directory afetar o utilizador
* Nível de risco destes eventos
* Indica se foram efetuadas as ações de remediação

![Riscos de utilizador](./media/active-directory-identityprotection/1031.png "riscos de utilizador")

Pode utilizar os níveis de risco do utilizador para criar políticas de acesso condicional que impeça os utilizadores de risco de início de sessão ou forçá-los de forma segura alterar a palavra-passe.

### <a name="closing-risk-events-manually"></a>Fechar os eventos de risco manualmente

Na maioria dos casos, irá tome as ações de remediação, tais como uma palavra-passe segura repor para fechar automaticamente os eventos de risco. No entanto, isto poderá não ser sempre possível.  
Isto é, por exemplo, o caso, quando:

* Foi eliminado um utilizador com eventos de risco Active Directory
* Uma investigação revela de que tem sido efetuar um evento de risco comunicado pelo utilizador legítimo

Uma vez que os eventos de risco que são **Active Directory** contribuir para o cálculo de risco do utilizador, poderá ter de ser manualmente atribuídos de menor um nível de risco fechando os eventos de risco manualmente.  
Durante a investigação, pode optar por colocar qualquer uma destas ações para alterar o estado de um evento de risco:

![Ações](./media/active-directory-identityprotection/34.png "ações")

* **Resolver** - se depois de investigar um evento de risco, demorou uma ação de remediação adequado fora Identity Protection e considerar de que o evento de risco deve ser considerado fechado, marcar o evento como resolvido. Resolvido eventos irão definir o estado do evento de risco para fechado e o evento de risco já não irá contribuir para o risco de utilizador.
* **Marcar como falso-positivos** -em alguns casos, pode investigar um evento de risco e detetar que incorretamente foi sinalizado como um risco. Pode ajudar a reduzir o número de tais ocorrências assinalando o evento de risco como falso-positivos. Isto irá ajudar os algoritmos do machine learning para melhorar a classificação de eventos semelhantes no futuro. O estado de eventos de Falso-positivos é **fechado** e já não irão contribuir para risco de utilizador.
* **Ignorar** - se de que não tenha sido qualquer ação de remediação, mas pretende que o evento de risco a serem removidos da lista de Active Directory, pode marcá-um evento de risco ignorar e o estado do evento será fechado. Eventos ignorados não contribuir para o risco de utilizador. Esta opção só deve ser utilizada em circunstâncias invulgares.
* **Reativar** -o risco de eventos que foram fechados manualmente (escolhendo **resolver**, **falsos positivos**, ou **ignorar**) pode ser reativado, definir o estado do evento para **Active Directory**. Eventos de risco reativado contribuem para o cálculo de nível de risco de utilizador. Eventos de risco fechados através de remediação (tal como repor uma palavra-passe segura) não podem ser reativados.

**Para abrir a caixa de diálogo de configuração relacionados**:

1. No **do Azure AD Identity Protection** painel, em **investigar**, clique em **eventos de risco**.

    ![Reposição de palavra-passe manual](./media/active-directory-identityprotection/1002.png "reposição de palavra-passe Manual")
2. No **eventos de risco** lista, clique em risco.

    ![Reposição de palavra-passe manual](./media/active-directory-identityprotection/1003.png "reposição de palavra-passe Manual")
3. No painel de risco, faça duplo clique um utilizador.

    ![Reposição de palavra-passe manual](./media/active-directory-identityprotection/1004.png "reposição de palavra-passe Manual")

### <a name="closing-all-risk-events-for-a-user-manually"></a>Fechar todos os eventos de risco de um utilizador manualmente
Em vez de manualmente fechar os eventos de risco de um utilizador individualmente, do Azure Active Directory Identity Protection também fornece um método para fechar todos os eventos para um utilizador com um clique.

![Ações](./media/active-directory-identityprotection/2222.png "ações")

Ao clicar em **dispensar todos os eventos**, todos os eventos estão fechados e o utilizador afectado já não está em risco.

### <a name="remediating-user-risk-events"></a>Eventos de risco de utilizador de remediação

Uma remediação é uma ação para proteger uma identidade ou um dispositivo que anteriormente era suspeito ou conhecido for comprometida. Uma ação de remediação restaura a identidade ou o dispositivo para um Estado de funcionamento seguro e resolve os eventos de risco anteriores associados o identidade ou o dispositivo.

Para corrigir eventos de risco do utilizador, pode:

* Efetuar uma palavra-passe segura repor manualmente a remediar eventos de risco do utilizador
* Configurar a política de segurança de risco de utilizador para mitigar ou remediar automaticamente os eventos de risco de utilizador
* Recriar a imagem de dispositivos infetados  

#### <a name="manual-secure-password-reset"></a>Reposição de palavra-passe segura manual
Uma reposição de palavra-passe segura é uma remediação eficaz para muitos eventos de risco e, quando efetuada, automaticamente fecha estes eventos de risco e recalcula o nível de risco do utilizador. Pode utilizar o dashboard de proteção de identidade para iniciar uma reposição para um utilizador de risco de palavra-passe.

A caixa de diálogo relacionada fornece dois métodos diferentes para repor uma palavra-passe:

**Repor palavra-passe** - selecione **exigir que o utilizador para repor a palavra-passe** para permitir que o utilizador Self-recuperar se o utilizador tiver registado para autenticação multifator. Durante próximo início de sessão o utilizador, o utilizador será necessária para resolver um desafio de autenticação multifator com êxito e, em seguida, forçado para alterar a palavra-passe. Esta opção não está disponível se a conta de utilizador já não está registada autenticação multifator.

**Palavra-passe temporária** - selecione **gerar uma palavra-passe temporária** imediatamente invalidar a palavra-passe existente e criar uma nova palavra-passe temporária ao utilizador. Envie a nova palavra-passe temporária para um endereço de correio eletrónico alternativo para o utilizador ou o Gestor do utilizador. Dado que a palavra-passe temporária, será pedido ao utilizador alterar a palavra-passe após o início de sessão.

![Política](./media/active-directory-identityprotection/1005.png "política")

**Para abrir a caixa de diálogo de configuração relacionados**:

1. No **do Azure AD Identity Protection** painel, clique em **utilizadores sinalizados para risco**.

    ![Reposição de palavra-passe manual](./media/active-directory-identityprotection/1006.png "reposição de palavra-passe Manual")
2. Na lista de utilizadores, selecione um utilizador com eventos de risco pelo menos um.

    ![Reposição de palavra-passe manual](./media/active-directory-identityprotection/1007.png "reposição de palavra-passe Manual")
3. No painel do utilizador, clique em **Repor palavra-passe**.

    ![Reposição de palavra-passe manual](./media/active-directory-identityprotection/1008.png "reposição de palavra-passe Manual")

### <a name="user-risk-security-policy"></a>Política de segurança de risco do utilizador
Uma política de segurança do utilizador risco é uma política de acesso condicional que avalia o nível de risco para um utilizador específico e aplica as ações de remediação e atenuação com base nas condições predefinidas e as regras.

![Política do utilizador ridk](./media/active-directory-identityprotection/1009.png "ridk de política de utilizador")

Azure AD Identity Protection ajuda a gerir a mitigação e remediação de utilizadores sinalizados para risco ao permitir-lhe:

* Defina os utilizadores e grupos que a política aplica-se a:

    ![Política do utilizador ridk](./media/active-directory-identityprotection/1010.png "ridk de política de utilizador")
* Defina o utilizador risco nível limiar (baixa, média ou alta) que aciona a política:

    ![Política do utilizador ridk](./media/active-directory-identityprotection/1011.png "ridk de política de utilizador")
* Defina os controlos para ser imposta quando aciona a política:

    ![Política do utilizador ridk](./media/active-directory-identityprotection/1012.png "ridk de política de utilizador")
* Mude o estado da sua política:

    ![Política do utilizador ridk](./media/active-directory-identityprotection/403.png "registo do MFA")
* Rever e avaliar o impacto de uma alteração antes de ativar esta:

    ![Política do utilizador ridk](./media/active-directory-identityprotection/1013.png "ridk de política de utilizador")

Escolher um **elevada** limiar reduz o número de vezes que uma política é acionada e minimiza o impacto para os utilizadores.
No entanto, exclui **baixa** e **média** utilizadores sinalizados para risco da política, o que poderá não segura identidades ou dispositivos que foram anteriormente suspeito ou conhecidos for comprometida.

Quando definir a política

* Excluir os utilizadores que se prevê a gerar uma grande quantidade de Falso-positivos (programadores, os analistas de segurança)
* Excluir utilizadores nas regiões onde o ativar a política não é prático (por exemplo, sem acesso para o suporte técnico)
* Utilize um **elevada** limiar durante a agregação de política iniciais, ou se tem minimizar desafios vistos pelos utilizadores finais.
* Utilize um **baixa** limiar se a sua organização precisar de maior segurança. Selecionar um **baixa** limiar apresenta utilizador adicionais início de sessão desafios, mas uma maior segurança.

A predefinição recomendada na maioria das organizações é para configurar uma regra para um **média** limiar para trazem um equilíbrio entre capacidade de utilização e segurança.

Para obter uma descrição geral da experiência de utilizador relacionadas, consulte:

* [Comprometido fluxo da recuperação de conta](active-directory-identityprotection-flows.md#compromised-account-recovery).  
* [Comprometido fluxo conta bloqueada](active-directory-identityprotection-flows.md#compromised-account-blocked).  

**Para abrir a caixa de diálogo de configuração relacionados**:

- No **do Azure AD Identity Protection** painel, no **configurar** secção, clique em **política do utilizador risco**.

    ![Política do utilizador ridk](./media/active-directory-identityprotection/1009.png "ridk de política de utilizador")

### <a name="mitigating-user-risk-events"></a>Mitigar os eventos de risco do utilizador
Os administradores podem definir uma política de segurança de risco do utilizador para impedir que os utilizadores após o início de sessão consoante o nível de risco.

Bloquear um início de sessão:

* Impede a geração de novos eventos de risco de utilizador para o utilizador afectado
* Permite aos administradores manualmente remediar os eventos de risco que afetam a identidade do utilizador e restaurá-lo para um estado seguro



## <a name="multi-factor-authentication-registration-policy"></a>Política de registo de autenticação multifator
A autenticação multifator do Azure é um método de verificar que é que requer a utilização de mais do que apenas um nome de utilizador e palavra-passe. Fornece uma segunda camada de segurança para inícios de sessão de utilizador e de transações.  
Recomendamos que necessitam do Azure multi-factor authentication para inícios de sessão de utilizador porque esta:

* Fornece autenticação forte com uma gama de opções de verificação fácil
* Desempenha uma função de chave na preparação da sua organização para proteger e recuperar os compromissos de conta

![Política do utilizador ridk](./media/active-directory-identityprotection/1019.png "ridk de política de utilizador")

Para obter mais detalhes, consulte [que é o Azure multi-factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)

Azure AD Identity Protection ajuda a gerir o fora de agregação de registo de autenticação multifator ao configurar uma política que permite-lhe:

* Defina os utilizadores e grupos que a política aplica-se a:

    ![Política da MFA](./media/active-directory-identityprotection/1020.png "política da MFA")
* Definir os controlos para ser imposta quando aciona a política:  

    ![Política da MFA](./media/active-directory-identityprotection/1021.png "política da MFA")
* Mude o estado da sua política:

    ![Política da MFA](./media/active-directory-identityprotection/403.png "política da MFA")
* Ver o estado atual do registo:

    ![Política da MFA](./media/active-directory-identityprotection/1022.png "política da MFA")

Para obter uma descrição geral da experiência de utilizador relacionadas, consulte:

* [Fluxo de registo de autenticação multifator](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  
* [Início de sessão experiências com o Azure AD Identity Protection](active-directory-identityprotection-flows.md).  

**Para abrir a caixa de diálogo de configuração relacionados**:

- No **do Azure AD Identity Protection** painel, no **configurar** secção, clique em **registo de multi-factor authentication**.

    ![Política da MFA](./media/active-directory-identityprotection/1019.png "política da MFA")

## <a name="next-steps"></a>Passos seguintes
* [Canal 9: Do Azure AD e mostrar de identidade: identidade pré-visualização de proteção](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

* [Ativar a proteção de identidade do Azure Active Directory](active-directory-identityprotection-enable.md)

* [Vulnerabilidades detetadas pelo Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md)

* [Eventos de risco do Azure Active Directory](active-directory-identity-protection-risk-events.md)

* [Notificações de proteção de identidade do Active Directory do Azure](active-directory-identityprotection-notifications.md)

* [Azure Active Directory Identity Protection manual de comunicação social](active-directory-identityprotection-playbook.md)

* [Glossário do Azure Active Directory Identity Protection](active-directory-identityprotection-glossary.md)

* [Início de sessão experiências com o Azure AD Identity Protection](active-directory-identityprotection-flows.md)

* [Proteção do Azure Active Directory identidade - como desbloquear utilizadores](active-directory-identityprotection-unblock-howto.md)

* [Introdução ao Azure Active Directory Identity Protection e o Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
