---
title: "Do Azure Active Directory B2C: Notas do programador sobre como utilizar políticas personalizadas | Microsoft Docs"
description: "Notas para os programadores na configuração e manutenção do Azure AD B2C com as políticas personalizadas"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/13/2017
ms.author: joroja
ms.openlocfilehash: 4fa4665115e0682df7c3fe3d8e2664a0f7a77a07
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Notas de versão de pré-visualização pública do Azure Active Directory B2C política personalizada
O conjunto de funcionalidades de política personalizada está agora disponível para avaliação em pré-visualização pública para todas as do Azure Active Directory B2C clientes (Azure AD B2C). Este conjunto de funcionalidades é direcionado para programadores de identidades avançada criar soluções de identidade mais complexas.  

Hoje em dia, este conjunto de funcionalidades requer que os programadores de configurar o Framework de experiência de identidade diretamente através da edição de ficheiro XML. Este método de configuração é complexa e poderosa. Avançadas identidade programadores utilizando a estrutura de experiência de identidade devem planear a investir algum tempo a concluir passagens e ler documentos de referência. 

## <a name="features-included-in-this-public-preview"></a>Funcionalidades incluídas nesta pré-visualização pública
Com as novas funcionalidades introduzidas na pré-visualização pública, os programadores podem efetuar as seguintes tarefas:<br>

* Criar e carregar autenticação personalizada utilizador percursos utilizando as políticas personalizadas. 
   * Descrevem percursos de utilizador passo a passo como trocas entre fornecedores de afirmações. 
   * Defina a ramificação condicional em percursos de utilizador. 
* Integre os serviços de ativar a REST API de mensagens em fila no seu percursos de utilizador de autenticação personalizado.  
* Adicione a Federação com fornecedores de identidade que são compatíveis com OpenIDConnect padrão. <br>
* Adicione a Federação com fornecedores de identidade respeitar o protocolo SAML 2.0. 

## <a name="terms-of-the-public-preview"></a>Termos de pré-visualização pública

* Aconselhamo-lo para utilizar as novas funcionalidades para fins de avaliação.<br>
* As novas funcionalidades não foram concebidas para utilização num ambiente de produção.<br>
* Contratos de nível de serviço (SLAs) não se aplicam a novas funcionalidades. <br>
* Pedidos de suporte podem ser registados através dos canais de suporte regular. <br>
* Não há nenhum data promised para disponibilidade geral.<br>
* No nosso discrição e, por qualquer motivo, Microsoft pode sinalizador e rejeitar ou restringir cenários e percursos de utilizador que excedem o âmbito da charter de produto do Azure AD B2C para servir como uma plataforma de gestão (CIAM) de identidade e acesso ao cliente.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilidades de programadores do conjunto de funcionalidades de política personalizada
Configuração da política manual concede acesso de nível inferior para a plataforma subjacente do Azure AD B2C e resulta na criação de uma arquitetura de confiança exclusivos e totalmente personalizável. As combinações possíveis dos fornecedores de identidade personalizada, relações de confiança, o integrações com serviços externos e fluxos de trabalho passo a passo colocar exigências superiores nos programadores avançados consumi-los.

Para beneficiar completamente a pré-visualização pública, sugerimos que os programadores de consumir o conjunto de funcionalidades de política personalizada de respeitar as seguintes diretrizes:
* Se familiarize com o idioma de configuração da estrutura de experiência de identidade e gestão de chaves/secrets.
* Assumir a propriedade dos cenários e integrações personalizadas.
* Efetue testes de cenário methodical.
* Siga o desenvolvimento de software e as melhores práticas com um mínimo de um ambiente de teste e desenvolvimento e um ambiente de produção de teste.
* Manter-se informado sobre desenvolvimentos novo dos fornecedores de identidade e integrar com os serviços. Por exemplo, manter registo das alterações dos segredos e agendados e alterações ao serviço.
* Configurar a monitorização do Active Directory e monitorize a capacidade de resposta de ambientes de produção.
* Manter os endereços de correio eletrónico de contacto atual na subscrição do Azure e permaneça responder a mensagens de correio eletrónico site em direto equipa da Microsoft.
* Ação atempadamente quando que aconselhado fazê-lo pela equipa do site em direto de Microsoft. 

## <a name="features-by-stage-and-known-issues"></a>Funcionalidades por fase e problemas conhecidos
Capacidades de estrutura de experiência de política/identidade personalizadas estão em desenvolvimento rápido e constante.  Esta tabela é um índice de disponibilidade de funcionalidades/componentes.

Colocar perguntas no Stack Overflow em [aka.ms/aadb2cso](http://aka.ms/aadb2cso)


### <a name="identity-providers-tokens-protocols"></a>Protocolos de fornecedores, Tokens, de identidade
Interfaces com componentes externos e de aplicações

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|---------------------------------------------|-------------|---------|----|-------|
| IDP OpenIDConnect |  | x |  | Por exemplo, Google + |
| IDP OAUTH2 |  | x |  | Por exemplo, Facebook  |
| IDP OAUTH1 |  | x |  | Por exemplo, Twitter |
| IDP SAML |  | x |  | Por exemplo, Salesforce, ADFS |
| IDP WSFED | x |  |  |  |
| Entidade confiadora intervenientes OAUTH |  | x |  |  |
| Entidade confiadora OIDC intervenientes |  | x |  |  |
| Entidade confiadora intervenientes SAML | x |  |  |  |
| Entidade confiadora WSFED intervenientes | x |  |  |  |
| API de REST com a autenticação basic e do certificado. |  | x |  | Por exemplo, as funções do Azure |


### <a name="component-support"></a>Suporte de componente


| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|-------------------------------------------|-------------|---------|----|-------|
| Multi-Factor Authentication do Azure |  | x |  |  |
| Azure Active Directory como o diretório local |  | x |  |  |
| Subsistema de E-Mail do Azure para 2FA |  | x |  |  |
| Suporte de vários idiomas|  | x |  |  |
| Complexidade de palavra-passe | x |  |  |  |


### <a name="content-definition"></a>Definição de conteúdo

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   Página de erro, api.error |  | x |  |  |
|   Página de seleção de IDP, api.idpselections |  | x |  |  |
|   Seleção de IDP para inscrição, api.idpselections.signup |  | x |  |  |
|   Se esqueceu a palavra-passe, api.localaccountpasswordreset |  | x |  |  |
|   Conta local. o início de sessão, api.localaccountsignin |  | x |  |  |
|   Conta local api.localaccountsignup de inscrição, |  | x |  |  |
|   Página MFA, api.phonefactor |  | x |  |  |
|   Self-permitido-por exemplo conta sociais sig cópia de segurança, api.selfasserted |  | x |  |  |
|   Self-permitido atualizar o perfil, api.selfasserted.profileupdate |  | x |  |  |
|   Inscrição unificada ou página de início de sessão, api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>Integração de IEF de aplicação
| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| Id_token_hint de parâmetro de cadeia de consulta | x |  |  |  |
| Domain_hint de parâmetro de cadeia de consulta |  | x |  | disponível como afirmações, pode ser transferido para IDP |
| Login_hint de parâmetro de cadeia de consulta |  | x |  | disponível como afirmações, pode ser transferido para IDP |
| Inserir JSON UserJourney através de client_assertion | x |  |  | vão ser preteridas |
| Inserir JSON UserJourney como id_token_hint | x |  |  | abordagem de reencaminhamento Ir para passar JSON |


### <a name="session-management"></a>Gestão de sessão

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|---------------------------------|-------------|---------|----|-------|
| Fornecedor de SSO de sessão |  | x |  |  |
| Fornecedor de sessão de início de sessão externo |  | x |  |  |
| Fornecedor de sessão SAML SSO |  | x |  |  |


### <a name="security"></a>Segurança
| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|---------------------------------------------|-------------|---------|----|-------|
| As chaves de política: gerar, Manual, carregamento |  | x |  |  |
| Política de chaves - RSA/certificados, os segredos |  | x |  |  |


### <a name="developer-interface"></a>Interface de programação
| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|---------------------------------------------|-------------|---------|----|-------|
| Portal do Azure-IEF UX |  | x |  |  |
| Registos de UserJourney do Application Insights  |  | x |  |  |
| Registos de eventos do Application Insights |x|  |  |  |



## <a name="next-steps"></a>Passos seguintes
[Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).
