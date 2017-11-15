---
title: 'O Azure AD Connect: Conceitos de Design | Microsoft Docs'
description: "Este tópico descreve algumas áreas de estrutura de implementação"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 53a0f766de9db7e6ee48b6659aad378620c0d727
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="azure-ad-connect-design-concepts"></a>O Azure AD Connect: Conceitos de Design
O objetivo deste tópico é descrever áreas que devem considerar durante a estrutura de implementação do Azure AD Connect. Este tópico é uma descrição profunda sobre algumas áreas e estes conceitos uma breve descrição, bem como outros tópicos.

## <a name="sourceanchor"></a>sourceAnchor
O atributo sourceAnchor é definido como *um atributo imutável durante a duração de um objeto*. Esta identifica exclusivamente um objeto como sendo o mesmo objeto no local e no Azure AD. O atributo é também denominado **immutableId** e os dois nomes são utilizados permutáveis.

A palavra imutável, que é "não é possível alterar", é importante neste tópico. Uma vez que o valor deste atributo não pode ser alterado depois de ter sido definido, é importante escolher uma estrutura que suporta o seu cenário.

O atributo é utilizado para os seguintes cenários:

* Quando um novo servidor do motor de sincronização é criado ou reconstruído após um cenário de recuperação após desastre, este atributo liga objetos existentes no Azure AD com objetos no local.
* Se mover de uma identidade apenas na nuvem para um modelo de identidade sincronizados, este atributo permite objetos em objetos existentes "correspondência de disco rígido" no Azure AD com objetos no local.
* Se utilizar a Federação, em seguida, este atributo em conjunto com o **userPrincipalName** a afirmação é utilizada para identificar exclusivamente um utilizador.

Este tópico aborda apenas sourceAnchor no que respeita aos utilizadores. As mesmas regras aplicam-se a todos os tipos de objeto, mas destina-se apenas os utilizadores que deste problema, normalmente, é importante.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Selecionar um atributo sourceAnchor boa
O valor do atributo tem de seguir as seguintes regras:

* Ter menos de 60 carateres de comprimento
  * Carateres que não estão a ser a-z, A-Z ou 0-9 são codificados e contabilizado como 3 carateres
* Não contém um caráter especial: &#92;! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @ _
* Deve ser globalmente exclusivo
* Tem de ser uma cadeia, número inteiro ou binário
* Não devem ser baseadas no nome do utilizador, estas alterações
* Não deve ser sensíveis a maiúsculas e evitar valores que podem variar devido por maiúsculas e minúsculas
* Deve ser atribuído quando o objeto é criado

Se o sourceAnchor selecionado não é do tipo cadeia, em seguida, do Azure AD Connect Base64Encode o valor de atributo para garantir que não existem carateres especiais são apresentados. Se utilizar outro servidor de federação que o AD FS, certifique-se o servidor, também pode Base64Encode o atributo.

O atributo sourceAnchor é maiúsculas e minúsculas. Um valor de "JohnDoe" não é igual a "johndoe". Mas não deve ter dois objectos diferentes com apenas uma diferença nas maiúsculas e minúsculas.

Se tiver uma única floresta no local, em seguida, o atributo deve utilizar seja **objectGUID**. Isto também se o atributo utilizado ao utilizar as definições rápidas no Azure AD Connect e também o atributo utilizado pelo DirSync.

Se tiver várias florestas e não mova os utilizadores entre florestas e domínios, em seguida, **objectGUID** é um bom atributo para utilizar o mesmo neste caso.

Se mover utilizadores entre florestas e domínios, em seguida, tem de encontrar um atributo que não alteram ou pode ser movido com os utilizadores durante a mudança. Uma abordagem recomendada é introduzir um atributo sintético. Um atributo que foi contêm algo que aparente ser um GUID seria adequado. Durante a criação do objeto, um novo GUID é criado e carimbo de data / no utilizador. Uma regra de sincronização personalizados pode ser criada no servidor do motor de sincronização para criar este valor com base no **objectGUID** e atualizar o atributo selecionado no ADDS. Ao mover o objeto, certifique-se também de copiar o conteúdo deste valor.

Outra solução é escolher um atributo existente a que se sabe não se altera. Atributos comuns incluem **campo IDdeEmpregado**. Se considerar um atributo que contém a letras, certificar-se de que não há que nenhuma oportunidade as maiúsculas e minúsculas (maiúsculas vs. minúsculas) pode ser alteradas para o valor do atributo. Atributos incorretos, que não devem ser utilizados incluem esses atributos com o nome do utilizador. Num marriage ou divorce, o nome deverá alterar, que não é permitido para este atributo. Isto também é um motivo por que motivo atributos como **userPrincipalName**, **correio**, e **targetAddress** não é possível selecionar na instalação do Azure AD Connect Assistente. Esses atributos também contêm a "@" caráter, que não é permitido no sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Alterar o atributo sourceAnchor
Não é possível alterar o valor do atributo sourceAnchor depois do objeto foi criado no Azure AD e a identidade está sincronizada.

Por este motivo, as seguintes restrições aplicam-se ao Azure AD Connect:

* O atributo sourceAnchor só pode ser definido durante a instalação inicial. Se voltar a executar o Assistente de instalação, esta opção é só de leitura. Se precisar de alterar esta definição, tem de desinstalar e reinstalar.
* Se instalar outro servidor do Azure AD Connect, tem de selecionar o mesmo atributo sourceAnchor utilizado anteriormente. Se anteriormente tiver sido utilizando o DirSync e mover para o Azure AD Connect, em seguida, tem de utilizar **objectGUID** , uma vez que é que o atributo utilizado pelo DirSync.
* Se o valor de sourceAnchor é alterado depois do objeto foi exportado para o Azure AD, em seguida, o Azure AD Connect, emite um erro de sincronização e não permite mais alterações em que o objeto antes do problema e o sourceAnchor é alterado no diretório de origem y.

## <a name="using-msds-consistencyguid-as-sourceanchor"></a>Utilizar msDS-ConsistencyGuid como sourceAnchor
Por predefinição, o Azure AD Connect (versão 1.1.486.0 e anterior) utiliza objectGUID como o atributo sourceAnchor. ObjectGUID é gerado pelo sistema. Não é possível especificar o respetivo valor durante a criação de locais objetos do AD. Conforme explicado na secção [sourceAnchor](#sourceanchor), existem cenários onde é necessário especificar o valor de sourceAnchor. Se os cenários são aplicáveis para si, tem de utilizar um atributo de AD configurável (por exemplo, msDS-ConsistencyGuid) como o atributo sourceAnchor.

O Azure AD Connect (versão 1.1.524.0 e após) agora facilita a utilização de msDS-ConsistencyGuid como atributo sourceAnchor. Quando utilizar esta funcionalidade, o Azure AD Connect configura automaticamente as regras de sincronização para:

1. Utilize msDS-ConsistencyGuid como o atributo sourceAnchor para objetos de utilizador. ObjectGUID é utilizada para outros tipos de objeto.

2. Para qualquer fornecido no local utilizador AD objeto cujo atributo msDS-ConsistencyGuid não está povoadas, Azure AD Connect escritas de paginações valor objectGUID de volta para o atributo msDS-ConsistencyGuid no Active Directory no local. Depois do atributo msDS-ConsistencyGuid é preenchido, o Azure AD Connect, em seguida, exporta o objeto para o Azure AD.

>[!NOTE]
> Uma vez no local objecto do AD é importado para o Azure AD Connect (que é, importado para o espaço de conector do AD e projetado para o Metaverso), não é possível alterar o valor de sourceAnchor já. Para especificar o valor de sourceAnchor para uma dada no local AD objeto, configure o atributo msDS-ConsistencyGuid antes de serem importados para o Azure AD Connect.

### <a name="permission-required"></a>Permissão necessária
Para esta funcionalidade funcione, a conta do AD DS utilizada para sincronizar com o Active Directory no local tem de possuir permissão de escrita para o atributo msDS-ConsistencyGuid no Active Directory no local.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Como ativar a funcionalidade de ConsistencyGuid - nova instalação
Pode ativar a utilização de ConsistencyGuid como sourceAnchor durante a instalação de novo. Esta secção abrange instalação rápida e personalizados nos detalhes.

  >[!NOTE]
  > Apenas as versões mais recentes do Azure AD Connect (1.1.524.0 e após) suporta a utilização de ConsistencyGuid como sourceAnchor durante a instalação de novo.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Como ativar a funcionalidade de ConsistencyGuid
Atualmente, a funcionalidade só pode ser ativada durante a nova instalação do Azure AD Connect apenas.

#### <a name="express-installation"></a>Instalação rápida
Ao instalar o Azure AD Connect com o modo de Express, o Assistente do Azure AD Connect determina automaticamente o atributo de AD mais adequado para utilizar como o atributo sourceAnchor utilizando a seguinte lógica:

* Em primeiro lugar, o Assistente do Azure AD Connect consulta o seu inquilino do Azure AD para obter o atributo de AD utilizado como o atributo sourceAnchor na instalação do Azure AD Connect anterior (se aplicável). Se esta informação está disponível, o Azure AD Connect utiliza o mesmo atributo de AD.

  >[!NOTE]
  > Apenas as versões mais recentes do Azure AD Connect (1.1.524.0 e após) armazena informações no seu inquilino do Azure AD sobre o atributo sourceAnchor utilizado durante a instalação. As versões mais antigas do Azure AD Connect não.

* Se a informações sobre o atributo sourceAnchor utilizado não estiver disponíveis, o assistente verifica o estado do atributo msDS-ConsistencyGuid no Active Directory no local. Se o atributo não está configurado qualquer objeto no diretório, o assistente utiliza msDS-ConsistencyGuid como o atributo sourceAnchor. Se o atributo está configurado numa ou mais objetos no diretório, o assistente conclui o atributo está a ser utilizado por outras aplicações e não é adequado como atributo sourceAnchor...

* Caso em que o assistente retrocede para utilizar objectGUID como o atributo sourceAnchor.

* Depois do atributo sourceAnchor é decidido, o assistente armazena as informações no seu inquilino do Azure AD. As informações serão utilizadas pela instalação futura do Azure AD Connect.

Uma vez concluída a instalação rápida, o assistente informa-o atributo de que tem sido selecionado como o atributo âncora de origem.

![Assistente informa o atributo de AD selecionado para sourceAnchor](./media/active-directory-aadconnect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Instalação personalizada
Ao instalar o Azure AD Connect com o modo personalizado, o Assistente do Azure AD Connect fornece duas opções ao configurar o atributo sourceAnchor:

![Instalação personalizada - sourceAnchor configuração](./media/active-directory-aadconnect-design-concepts/consistencyGuid-02.png)

| Definição | Descrição |
| --- | --- |
| Permitir que seja o Azure a gerir a âncora de por mim | Selecione esta opção se pretender que o Azure AD escolha o atributo por si. Se selecionar esta opção, o Assistente do Azure AD Connect aplica o mesmo [lógica de seleção de atributo sourceAnchor utilizada durante a instalação rápida](#express-installation). Semelhante à instalação rápida, o assistente informa-o atributo foi selecionado como o atributo âncora de origem após a conclusão da instalação personalizada. |
| Um atributo específico | Selecione esta opção se pretender especificar um atributo do AD existente como o atributo sourceAnchor. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Como ativar a funcionalidade de ConsistencyGuid - implementação existente
Se tiver uma implementação existente do Azure AD Connect que está a utilizar objectGUID como o atributo âncora de origem, pode mudá-lo para utilizar ConsistencyGuid em substituição.

>[!NOTE]
> Apenas as versões mais recentes do Azure AD Connect (1.1.552.0 e após) suporta mudar de ObjectGuid para ConsistencyGuid como o atributo âncora de origem.

Para mudar de objectGUID ConsistencyGuid como o atributo âncora de origem:

1. Iniciar o Assistente do Azure AD Connect e clique em **configurar** para ir para o ecrã de tarefas.

2. Selecione o **configurar âncora de origem** opção de tarefas e clique em **seguinte**.

   ![Ativar ConsistencyGuid para implementação existente - passo 2](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment01.png)

3. Introduza as credenciais de administrador do Azure AD e clique em **seguinte**.

4. Assistente do Azure AD Connect analisa o estado do atributo msDS-ConsistencyGuid no Active Directory no local. Se o atributo não está configurado qualquer objeto no diretório, o Azure AD Connect conclui que nenhuma outra aplicação está atualmente a utilizar o atributo sendo segura para utilizá-lo como o atributo âncora de origem. Clique em **seguinte** para continuar.

   ![Ativar ConsistencyGuid para implementação existente - passo 4](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment02.png)

5. No **pronto para configurar** ecrã, clique em **configurar** para efetuar a alteração de configuração.

   ![Ativar ConsistencyGuid para implementação existente - passo 5](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment03.png)

6. Uma vez concluída a configuração, o assistente indica que msDS-ConsistencyGuid agora está a ser utilizado como o atributo âncora de origem.

   ![Ativar ConsistencyGuid para implementação existente - passo 6](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment04.png)

Durante a análise (passo 4), se o atributo está configurado numa ou mais objetos no diretório, conclui o Assistente para o atributo está a ser utilizado por outra aplicação e devolve um erro, conforme ilustrado no diagrama abaixo. Este erro também pode ocorrer se anteriormente tiver ativado a funcionalidade de ConsistencyGuid no seu principal do Azure AD Connect servidor e está a tentar fazer no seu servidor de teste.

![Ativar ConsistencyGuid para implementação existente - erro](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Se tiver a certeza de que o atributo não é utilizado por outras aplicações existentes, pode suprimir o erro reiniciando o Assistente do Azure AD Connect com o **/SkipLdapSearchcontact** especificado. Para tal, execute o seguinte comando numa linha de comandos:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Impacto no AD FS ou a configuração de Federação de terceiros
Se estiver a utilizar o Azure AD Connect gerir no local implementação do AD FS, o Azure AD Connect atualiza automaticamente as regras de afirmação a utilizar o mesmo atributo de AD como sourceAnchor. Isto garante que a afirmação ImmutableID gerada pelo AD FS é consistente com os valores de sourceAnchor exportados para o Azure AD.

Se estiver a gerir o AD FS fora do Azure AD Connect ou estiver a utilizar servidores de Federação de terceiros para autenticação, tem de atualizar manualmente as regras de afirmação para ImmutableID afirmação estar consistente com os valores de sourceAnchor exportados para o Azure AD, conforme descrito em artigo secção [regras de afirmação de modificar o AD FS](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). O assistente devolve o seguinte aviso após a conclusão da instalação:

![Configuração de Federação de terceiros](./media/active-directory-aadconnect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>A adição de novos diretórios a implementação existente
Suponha que tiver implementado o Azure AD Connect com a funcionalidade de ConsistencyGuid ativada e agora pretende adicionar outro diretório para a implementação. Quando tenta adicionar o diretório, o Assistente do Azure AD Connect verifica o estado do atributo mSDS-ConsistencyGuid no diretório. Se o atributo está configurado numa ou mais objetos no diretório, o assistente conclui o atributo está a ser utilizado por outras aplicações e devolve um erro, conforme ilustrado no diagrama abaixo. Se tiver a certeza de que o atributo não é utilizado por aplicações existentes, terá de contactar o suporte para obter informações sobre como suprimir o erro.

![A adição de novos diretórios a implementação existente](./media/active-directory-aadconnect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Início de sessão no Azure AD
Ao integrar o seu diretório no local com o Azure AD, é importante compreender a forma como as definições de sincronização podem afetar o utilizador de forma efetua a autenticação. Azure AD utiliza userPrincipalName (UPN) para autenticar o utilizador. No entanto, quando sincroniza os seus utilizadores, tem de escolher o atributo a ser utilizado para o valor do userPrincipalName cuidadosamente.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Escolher o atributo para userPrincipalName
Quando selecionar o atributo para fornecer deve certificar-se o valor de UPN a ser utilizado no Azure

* Os valores de atributo está em conformidade com a sintaxe UPN (RFC 822), que deve ter o formatousername@domain
* O sufixo nos valores corresponde a um dos domínios personalizados verificados no Azure AD

As definições rápidas, a escolha assumida para o atributo é userPrincipalName. Se o atributo userPrincipalName não contém o valor que pretende que os utilizadores iniciar sessão no Azure, em seguida, tem de escolher **instalação personalizada**.

### <a name="custom-domain-state-and-upn"></a>Estado de domínio personalizado e UPN
É importante garantir que existe um domínio verificado para o sufixo UPN.

O João é um utilizador em contoso.com. Pretende que o João para utilizar o UPN no local john@contoso.com para iniciar sessão no Azure depois de ter sincronizadas utilizadores à sua contoso.onmicrosoft.com de diretório do Azure AD. Para tal, terá de adicionar e verificar contoso.com como um domínio personalizado no Azure AD antes de começar a sincronizar os utilizadores. Se o sufixo UPN de John, por exemplo, contoso.com, não corresponde um domínio verificado no Azure AD, o Azure AD substitui o sufixo UPN com contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Domínios não encaminhável no local e o UPN para o Azure AD
Algumas organizações têm domínios não encaminháveis internos, como contoso. local, ou domínios de etiqueta única simples, como contoso. Não é possível verificar um domínio não encaminháveis internos no Azure AD. O Azure AD Connect pode sincronizar a apenas um domínio verificado no Azure AD. Quando cria um diretório do Azure AD, cria um domínio encaminhável que torna-se o domínio predefinido do Azure AD, por exemplo, contoso.onmicrosoft.com. Por conseguinte, torna-se verificar qualquer outro domínio encaminhável neste cenário, no caso de não quiser sincronizar para o domínio onmicrosoft.com predefinido que é necessário.

Leitura [adicionar o seu nome de domínio personalizado ao Azure Active Directory](../active-directory-domains-add-azure-portal.md) para obter mais informações sobre adicionar e verificar domínios.

O Azure AD Connect Deteta se estiver a executar num ambiente de domínio não encaminháveis internos e iria adequadamente avisá-lo de aceder diretamente com as definições rápidas. Se estão a funcionar num domínio não encaminháveis internos, em seguida, é provável que o UPN dos utilizadores têm também sufixos não encaminháveis internos. Por exemplo, se estiver a executar em contoso. local, o Azure AD Connect sugere que utilize definições personalizadas, em vez de utilizar definições expressas. Utilizar as definições personalizadas, é capazes de especificar o atributo que deve ser utilizado como o UPN para iniciar sessão no Azure depois dos utilizadores são sincronizados com o Azure AD.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
