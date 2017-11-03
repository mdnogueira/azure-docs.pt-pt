---
title: "Do Azure Active Directory B2C: Políticas personalizadas | Microsoft Docs"
description: "Um tópico sobre as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 1ff398a4-2079-4615-94f1-57de22c0aad6
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 25dada7bc04449c6e527b94d97780d9aef1c33a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C: As políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>Quais são as políticas personalizadas?

As políticas personalizadas são ficheiros de configuração que definem o comportamento do seu inquilino do Azure AD B2C. Enquanto **políticas incorporadas** estão predefinidas no portal do Azure AD B2C para as tarefas mais comuns de identidade, políticas personalizadas podem ser totalmente editadas por um programador de identidade para concluir um número ilimitado near de tarefas. Continue a ler para determinar se as políticas personalizadas são adequada a si e o seu cenário de identidade.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Comparar as políticas incorporadas e personalizadas

| | Políticas incorporadas | políticas personalizadas |
|-|-------------------|-----------------|
|Segmente utilizadores | Todos os programadores de aplicações, com ou sem conhecimentos de identidade | Os profissionais de identidade: integradores de sistemas, consultores e equipas internas de identidade. Se estiver familiarizados com fluxos de OpenIDConnect e compreender fornecedores de identidade e autenticação baseada em afirmações |
|Método de configuração | Portal do Azure com uma IU amigável de utilizador | Editar diretamente os ficheiros XML e, em seguida, carregar para o portal do Azure |
|Personalização de IU | Personalização de IU completa, incluindo suporte HTML, CSS e javascript (requer o domínio personalizado)<br><br>Suporte MultiLanguage com cadeias personalizada | mesmo |
| Personalização de atributo | Atributos de padrão e personalizados | mesmo |
|Gestão de token e de sessão | Token personalizado e várias opções de sessão | mesmo |
|Fornecedores de identidade| **Hoje em dia**: fornecedor predefinido de local, redes social<br><br>**Futuro**: OIDC baseada em normas, SAML, OAuth | **Hoje em dia**: OIDC baseada em normas, OAUTH, SAML<br><br>**Futuro**: WsFed |
|Tarefas de identidade (exemplos) | Inscrever-se ou iniciar sessão com muitas contas de redes sociais e local<br><br>Reposição Personalizada de Palavra-passe<br><br>Editar perfil<br><br>Cenários de autenticação Multifator<br><br>Personalizar Tokens e sessões<br><br>Fluxos de Token de acesso | Conclua as mesmas tarefas como políticas incorporadas a utilizar fornecedores de identidade personalizada ou utilize âmbitos personalizados<br><br>Utilizador de aprovisionar no outro sistema no momento de registo<br><br>Enviar um e-mail de boas-vindas utilizando o seu próprio fornecedor de serviço de e-mail<br><br>Utilize um arquivo de utilizador fora do B2C<br><br>Validar o utilizador fornecido informações com um sistema fidedigna através de API |

## <a name="policy-files"></a>Ficheiros de política

Uma política personalizada é representada como um ou vários ficheiros de formatado em XML que se referenciar entre si numa cadeia hierárquica. Definem os elementos XML: o esquema de afirmações, as afirmações transformações, definições de conteúdo, perfis de fornecedores/técnica de afirmações e etapas da orquestração Userjourney, entre outros elementos.

Recomendamos a utilização dos três tipos de ficheiros de política:

- **Um ficheiro de BASE**, que contém a maioria das definições e para que o Azure oferece um exemplo completo.  É recomendado que torne um número mínimo de alterações a este ficheiro para o ajudar a resolver problemas de mensagens em fila e a longo prazo manutenção das políticas de
- **um ficheiro de extensões** que contém as alterações de configuração exclusivo para o seu inquilino
- **um ficheiro de terceiros entidade Confiadora (RP)** que é o único ficheiro concentra-se tarefas que é invocado diretamente pela aplicação ou serviço (aka entidade Confiadora).  Leia o artigo em definições do ficheiro de política para obter mais informações.  Cada tarefa exclusiva requer a sua própria RP e consoante os requisitos de imagem corporativa o número pode ser "total de aplicações x o número total de cenários de utilização de".


As políticas incorporadas no Azure AD B2C seguem o padrão de ficheiro de 3 descrito acima, mas o programador só vê o ficheiro de terceiros entidade Confiadora (RP), enquanto o portal faz com que as alterações em segundo plano para o ficheiro EXTenstions.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Conceitos principais que deve conhecer ao utilizar políticas personalizadas

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Ao cliente identidades e acessos (CIAM) serviço de gestão do Azure. O serviço inclui:

1. Um diretório de utilizador no formato de uma oferta especial de objetivo do Azure Active Directory acessível através do Microsoft Graph e que contém dados de utilizador para contas locais e contas federadas 
2. Acesso a **identidade experiência Framework** que orquestra fidedignidade entre os utilizadores e as entidades e transmite afirmações entre-los para concluir uma tarefa de gestão de identidade/acesso 
3. Um token serviço de segurança (STS) emitir id tokens, atualização de tokens e acesso tokens (e equivalentes asserções de SAML) e a validá-las para proteger os recursos.

O Azure AD B2C interage com fornecedores de identidade, os utilizadores, outros sistemas e com o diretório de utilizador local na sequência para alcançar uma tarefa de identidade (por exemplo início de sessão um utilizador registar um novo utilizador, repor uma palavra-passe). A plataforma subjacente que estabelece a fidedignidade de múltiplos intervenientes e executa estes passos denomina-se a estrutura de experiência de identidade e uma política (também denominada journey um utilizador ou uma política de estrutura de confiança) explicitamente define os atores, as ações, os protocolos, e a sequência de passos para concluir.

### <a name="identity-experience-framework"></a>Estrutura de Experiência de Identidade

Formatos de uma plataforma do Azure totalmente configurável, orientadas por políticas e baseado na nuvem que orquestra confiança entre entidades (amplamente fornecedores de afirmações) no protocolo padrão, tais como OpenIDConnect, OAuth, SAML, WSFed e alguns dos não padrão (por exemplo REST API-based sistema para sistema afirmações trocas). O I2E cria amigável de utilizador, whitelabelled experiências que suportam HTML, CSS e javascript.  Atualmente, a arquitetura de experiência de identidade está disponível apenas no contexto do serviço do Azure AD B2C e prioritários para as tarefas relacionadas com CIAM.

### <a name="built-in-policies"></a>Políticas incorporadas

Predefinidas de ficheiros de configuração que direcionam o comportamento do Azure AD B2C para efetuar mais frequentemente utilizada identidade tarefas (ou seja, registo de utilizador, de início de sessão, reposição de palavra-passe) e interagir com entidades fidedignas cuja relação também predefinida no Azure AD B2C (para exemplo Facebook fornecedor de identidade, LinkedIn, Microsoft Account, contas Google).  No futuro, também podem fornecer políticas incorporadas para personalização de fornecedores de identidade que são normalmente no realm enterprise como o Azure Active Directory Premium, do Active Directory/ADFS, Salesforce ID fornecedor etc.


### <a name="custom-policies"></a>políticas personalizadas

Ficheiros de configuração que definem o comportamento do Framework de experiência de identidade no seu inquilino do Azure AD B2C. Uma política personalizada está acessível como um ou vários ficheiros XML (consulte as definições de política de ficheiros) que são executados pelo Framework de experiência de identidade quando foi invocado por uma entidade confiadora (por exemplo uma aplicação). As políticas personalizadas podem ser editadas diretamente por um programador de identidade para concluir um número ilimitado near de tarefas. Os programadores de configurar as políticas personalizadas tem de definir as relações fidedignas em detalhe cuidado para incluir pontos finais de metadados, afirmações exatas definições do exchange e configurar certificados conforme necessário, por cada fornecedor de identidade, as chaves e segredos.

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>Definições do ficheiro de política para Trustframeworks de estrutura de experiência de identidade

### <a name="policy-files"></a>Ficheiros de política

Uma política personalizada é representada como um ou vários ficheiros de formatado em XML que se referenciar entre si numa cadeia hierárquica. Definem os elementos XML: o esquema de afirmações, as afirmações transformações, definições de conteúdo, perfis de fornecedores/técnica de afirmações e etapas da orquestração journey utilizador, entre outros elementos.  Recomendamos a utilização dos três tipos de ficheiros de política:

- **Um ficheiro de BASE** contém a maioria das definições e para que o Azure oferece um exemplo completo.  É recomendado que torne um número mínimo de alterações a este ficheiro para o ajudar a resolver problemas de mensagens em fila e a longo prazo manutenção das políticas de
- **um ficheiro de extensões** que contém as alterações de configuração exclusivo para o seu inquilino
- **um ficheiro de terceiros entidade Confiadora (RP)** é o único ficheiro concentra-se tarefas que é invocado diretamente pela aplicação ou serviço (aka entidade Confiadora).  Leia o artigo em definições do ficheiro de política para obter mais informações.  Cada tarefa exclusiva requer a sua própria RP e consoante os requisitos de imagem corporativa o número pode ser "total de aplicações x o número total de cenários de utilização de".

![Tipos de ficheiro de política](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Tipo de ficheiro de política | Nome de ficheiro de exemplos | Utilização recomendada | Herda a partir do |
|---------------------|--------------------|-----------------|---------------|
| BASE |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | Inclui o esquema de afirmações de núcleos, transformações de afirmações, fornecedores de afirmações e percursos de utilizador configurados pela Microsoft<br><br>Efetuar alterações mínimas este ficheiro | Nenhuma |
| Extensão (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | Consolidar as alterações para o ficheiro de BASE aqui<br><br>Fornecedores de afirmações modificado<br><br>Percursos de utilizadores modificados<br><br>As suas próprias definições de esquema personalizadas | Ficheiro de BASE |
| Entidade Confiadora (RP) | B2C_1A_sign_up_sign_in.XML| Alterar token forma e sessão definições aqui| Ficheiro Extensions(Ext) |

### <a name="inheritance-model"></a>Modelo de herança

Quando uma aplicação chama o ficheiro de política de RP, a arquitetura de experiência de identidade no B2C adicionará todos os elementos de BASE, em seguida, EXTENSÕES de e por fim do ficheiro de política RP a Monte a política atual em vigor.  Elementos do mesmo tipo e nome no ficheiro de RP substituirão as nas EXTENSÕES, e as EXTENSÕES de substituições BASE.

**Políticas incorporadas** no Azure AD B2C seguem o padrão de ficheiro de 3 descrito acima, mas o programador só vê o ficheiro de terceiros entidade Confiadora (RP), enquanto o portal faz com que as alterações em segundo plano para o ficheiro EXTenstions.  Todas as do Azure AD B2C partilha um ficheiro de política BASE que está sob o controlo da equipa de B2C do Azure e é atualizado frequentemente.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md)
