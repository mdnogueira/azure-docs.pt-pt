---
title: "Migrar a partir do serviço de controlo de acesso do Azure (ACS)"
description: "As opções para mover as aplicações e serviços termina sessão do serviço de controlo de acesso do Azure | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: e32cac7feda929a63c4a80fc0078b221117eb2b5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="migrating-from-azure-access-control-service-acs"></a>Migrar a partir do serviço de controlo de acesso do Azure (ACS)

Microsoft Azure Active Directory controlo de acesso (também conhecido como o serviço de controlo de acesso ou ACS) vai ser reformado no Novembro de 2018.  As aplicações e serviços a utilizar atualmente ACS necessário totalmente migrar para um mecanismo de autenticação diferentes antes desta data. Este documento descreve as recomendações para os clientes atuais, se planear despromover a utilização dos ACS. Se não estiver atualmente a utilizar ACS, não terá de efetuar qualquer ação.


## <a name="brief-acs-overview"></a>Breve descrição geral do ACS

ACS é um serviço de autenticação em nuvem que fornece uma forma fácil de autenticar e autorizar utilizadores para obterem acesso às suas aplicações web e serviços, permitindo que muitas funcionalidades de autenticação e autorização para ser factored fora do seu código. É utilizado principalmente por programadores e arquitetos de clientes .NET, aplicações web ASP.NET e serviços web WCF.

Casos de utilização de ACS podem ser divididos em três categorias principais:

- Autenticar determinadas cloud Services da Microsoft, incluindo o Service Bus do Azure, o Dynamics CRM e outras pessoas. Aplicações de cliente foi possível obter os tokens do ACS que podem ser utilizados para autenticar para estes serviços para efetuar diversas ações.
- Adicionar autenticação para aplicações web, da variedade de personalizadas incorporadas & de variedade pré-empacotadas (por exemplo, o Sharepoint). Utilizar a autenticação "passiva" dos ACS, aplicações web foi suporta início de sessão com as contas do Google, Facebook, Yahoo, Microsoft (Live ID) de conta, do Azure Active Directory e ADFS.
- Proteger os serviços web de conceção personalizada com tokens emitidos dos ACS. Utilizar a autenticação "ativa", serviços web foi Certifique-se de que apenas permitem acesso de clientes conhecidos que tenham autenticadas com ACS.

Cada um destes casos de utilização e as respetivas estratégias de migração recomendada é abordados nas secções seguintes. A vasta maioria dos casos, as alterações de código significativas necessário migrar existentes de aplicações e serviços para tecnologias mais recentes. É recomendado que comece a planear e executar a migração imediatamente para evitar qualquer possibilidade de falhas ou período de indisponibilidade.

> [!WARNING]
> Na maioria dos casos, as alterações de código importantes são necessários para migrar existentes de aplicações e serviços para tecnologias mais recentes. É recomendado que comece a planear e executar a migração imediatamente para evitar qualquer possibilidade de falhas ou período de indisponibilidade.

Em termos de arquitetura, ACS inteiramente inclui os seguintes componentes:

- Um token serviço seguro (STS), que recebe pedidos de autenticação & emite tokens de segurança no return.
- Portal clássico do Azure, que é utilizado para criar, eliminar e ativação/desativação de espaços de nomes do ACS.
- Um separado dos ACS portal de gestão, que é utilizado para personalizar e configurar o comportamento de um espaço de nomes do ACS.
- Um serviço de gestão, que pode ser utilizado para automatizar as funções dos portais.
- Um motor de regra de transformação token, que pode ser utilizado para definir lógica complexa para manipular o formato de saída de tokens emitidos dos ACS.

Para utilizar estes componentes, tem de criar um ou mais ACS **espaços de nomes**. Um espaço de nomes é uma instância dedicada do ACS que as aplicações e serviços comunicam com; está isolado de todos os outros clientes de ACS, que utilizam os seus próprios espaços de nomes. Um espaço de nomes em ACS tem um URL dedicado, como:

```HTTP
https://mynamespace.accesscontrol.windows.net
```

Todas as comunicações com o STS e operações de gestão são efetuadas neste URL, com caminhos diferentes para diferentes fins. Para determinar se a aplicações ou serviços utilizam ACS, monitorizar o tráfego para `https://{namespace}.accesscontrol.windows.net`.  Qualquer tráfego para este URL é o tráfego é processado pelo ACS e tem de ser descontinuada.  A única exceção é o tráfego para `https://accounts.accesscontrol.windows.net` -o tráfego para este URL já está a ser processado por um serviço diferente e não é afetado por descontinuação dos ACS.  Deve também não se esqueça de iniciar sessão no portal do Azure clássico e verifique a existência de quaisquer espaços de nomes do ACS em subscrições que é proprietário.  Espaços de nomes do ACS estão listados no **do Active Directory** serviço, sob o **espaços de nomes de controlo de acesso** separador.

Para obter mais informações sobre ACS, consulte [arquivados esta documentação no MSDN](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="retirement-schedule"></a>Agenda de extinção

A partir de Novembro de 2017, todos os componentes de ACS são totalmente suportadas & operacional. A única restrição é que [não não possível criar novos espaços de nomes do ACS através do portal clássico do Azure](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

A linha cronológica para preterição destes componentes segue esta agenda:

- **Novembro de 2017**: O administrador do Azure AD no portal clássico do Azure experiência [é retirado](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Neste momento, gestão de espaço de nomes para ACS estarão disponíveis neste novo, URL dedicado: `http://manage.windowsazure.com?restoreClassic=true`. Este procedimento permite-lhe ver os espaços de nomes existentes, ativar/desativá-los e eliminá-los inteiramente se desejar.
- **De 2018 Abril**: gestão de espaço de nomes do ACS é já não estar disponível neste URL dedicado. Neste ponto no tempo, não é possível ativar/desativar, eliminar ou enumerar os espaços de nomes do ACS. Portal de gestão dos ACS, no entanto, é totalmente funcional e localizado em `https://{namespace}.accesscontrol.windows.net`. Todos os outros componentes do ACS continuam a funcionar normalmente bem.
- **De 2018 Novembro**: os componentes de todas as ACS são encerrados permanentemente. Isto inclui o portal de gestão dos ACS, o serviço de gestão, STS e o motor de regra de transformação de token. Neste momento, quaisquer pedidos enviados para ACS (localizado em `{namespace}.accesscontrol.windows.net`) falhar. Deve ter migrou todas as aplicações e serviços existentes para outras tecnologias bem antes deste período de tempo.


## <a name="migration-strategies"></a>Estratégias de migração

As secções seguintes descrevem recomendações de nível elevadas para migrar retire ACS para outras tecnologias da Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Clientes do cloud services da Microsoft

Cada um dos serviços cloud Microsoft que aceita tokens emitidos pelo ACS agora suporta, pelo menos, uma forma alternativa de autenticação. O mecanismo de autenticação correto varia para cada serviço, pelo que recomendamos que faça referência a documentação específica de cada serviço para obter orientações sobre oficial. Para sua comodidade, cada conjunto de documentação é fornecido aqui:

| Serviço | Orientação |
| ------- | -------- |
| Service Bus do Azure | [Migrar para assinaturas de acesso partilhado](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Reencaminhamento do Azure | [Migrar para assinaturas de acesso partilhado](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Cache do Azure | [Migrar para a Cache de Redis do Azure](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Mercado de dados do Azure | [Migrar para as APIs de serviços cognitivos](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Serviços BizTalk | [Migrar para as aplicações lógicas do Azure](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Os Media Services do Azure | [Migrar para a autenticação do Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Atualize o agente de cópia de segurança do Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-using-passive-authentication"></a>Aplicações Web através de autenticação passiva

Para aplicações web através de ACS para autenticação de utilizador, de ACS fornecidas as seguintes funcionalidades e capacidades para programadores e arquitetos de aplicações web:

- A integração com o Windows Identity Foundation (WIF).
- Federação com o Google, Facebook, Yahoo, Microsoft (Live ID) de conta, do Azure Active Directory e ADFS.
- Suporte para os seguintes protocolos de autenticação: 13 de rascunho de OAuth 2.0, Ws-Trust e Ws-Federation.
- Suporte para os seguintes formatos de token: token de web JSON (JWT), SAML 1.1, SAML 2.0 e simples web token (SWT).
- Uma experiência de deteção de realm inicial, integrada no WIF, que podem os utilizadores escolher o tipo de conta que utilizam para iniciar sessão. Esta experiência encontrava alojada pela aplicação web e totalmente personalizável.
- Token transformação permitido personalização avançada de afirmações recebidas pela aplicação web de ACS, incluindo:
    - passar pela afirmações a partir de fornecedores de identidade
    - a adição de afirmações personalizadas adicionais
    - lógica depois de se simples para emitir afirmações em determinadas condições

Infelizmente, não é não um serviço que fornece todas estas capacidades equivalentes.  Deve avaliar que capacidades de ACS que precisa e, em seguida, escolha entre utilizar [ **do Azure Active Directory** ](https://azure.microsoft.com/develop/identity/signin/) (Azure AD), [ **do Azure AD B2C** ](https://azure.microsoft.com/services/active-directory-b2c/), ou outro serviço de autenticação em nuvem.

#### <a name="migrating-to-azure-active-directory"></a>Migrar para o Azure Active Directory

Um caminho da ter em consideração é integrar a aplicações e serviços diretamente no Azure Active Directory. Azure AD é o fornecedor de identidade baseada em nuvem para a Microsoft funciona & profissional contas - é o fornecedor de identidade para o Office 365, Azure e muito mais. Fornece semelhante federado capacidades de autenticação para ACS, mas não suporta todas as funcionalidades de ACS. O exemplo primário é federação com fornecedores de identidade de redes sociais, como o Facebook, Google e Yahoo. Se os seus utilizadores iniciar sessão com estes tipos de credenciais, do Azure AD não é para si. Também efetua não necessariamente suporte de protocolos de autenticação do mesma exata como ACS - ao ambas as ACS e suporte do Azure AD OAuth, por exemplo, existem ligeiras diferenças entre cada implementação de exigir que modifique o código como parte da migração.

Azure AD no entanto fornecer várias vantagens potenciais aos clientes do ACS. Nativamente suporta Microsoft trabalha & profissional contas alojadas na nuvem, que são frequentemente utilizadas pelos clientes de ACS.  Também pode ser federado um inquilino do Azure AD para uma ou mais instâncias no local do Active Directory através do ADFS, permitindo que a sua aplicação autenticar utilizadores baseados na cloud e alojados no local de utilizadores.  Também suporta o protocolo Ws-Federation, o que torna relativamente simples integrar com uma aplicação web utilizando o Windows Identity Foundation (WIF).

A tabela seguinte compara as funcionalidades do ACS relevantes para as aplicações web com estão disponíveis no Azure AD. Um nível elevado, **do Azure Active Directory é, provavelmente, a opção adequada para a migração se que apenas permitem que os utilizadores início de sessão com o respetivo trabalho Microsoft & profissional contas**.

| Capacidade | Suporte de ACS | Suporte do Azure AD |
| ---------- | ----------- | ---------------- |
| **Tipos de contas** | | |
| Trabalhar & profissional contas Microsoft | Suportado | Suportado |
| Contas do Active Directory do Windows Server & ADFS | Suportada através de federação com um inquilino do Azure AD <br /> Suportada através de Federação direta com o AD FS | Só é suportada através de federação com um inquilino do Azure AD | 
| Contas de outros sistemas de gestão de identidades empresariais | Possível através de federação com um inquilino do Azure AD <br />Suportada através de Federação direta | Possível através de federação com um inquilino do Azure AD |
| Contas Microsoft para utilização pessoal (Windows Live ID) | Suportado | Suportada através v 2.0 do Azure AD protocolo de OAuth, mas não através de quaisquer outros protocolos. | 
| Contas do Facebook, Google, Yahoo | Suportado | Não é suportado whatsoever. |
| **Protocolos de & compatibilidade SDK** | | |
| Windows Identity Foundation (WIF) | Suportado | Suportado, mas instruções limitadas disponíveis. |
| WS-Federation | Suportado | Suportado |
| OAuth 2.0 | Suporte para rascunho 13 | Suporte para RFC 6749, a especificação de mais moderna. |
| WS-Trust | Suportado | Não suportado |
| **Formatos de token** | | |
| JSON Web Tokens (JWTs) | Suportado na versão Beta | Suportado |
| Tokens de SAML 1.1 | Suportado | Suportado |
| Tokens de SAML 2.0 | Suportado | Suportado |
| Tokens Web simples (SWT) | Suportado | Não suportado |
| **Personalizações** | | |
| Realm inicial personalizável/conta de deteção diretriz da IU | Código transferível, que pode ser incorporado nas aplicações | Não suportado |
| Carregar certificados de assinatura de tokens personalizado | Suportado | Suportado |
| Personalizar afirmações no | Afirmações de entrada de pass-through a partir de fornecedores de identidade<br />Obtenha o acesso token do fornecedor de identidade como uma afirmação<br />Emitir afirmações de saída com base nos valores de afirmações de entrada<br />Emitir afirmações de saída com valores constantes | Não é possível passthrough afirmações de fornecedores de identidade federada<br />Não é possível obter o token de acesso do fornecedor de identidade como uma afirmação<br />Não é possível emitir afirmações de saída com base nos valores de afirmações de entrada<br />Pode emitir afirmações de saída com valores constantes<br />Podem emitir afirmações de saída com base nas propriedades de utilizadores synched para o Azure AD |
| **Automatização** | | |
| Automatizar tarefas de gestão de configuração / | Suportadas através do serviço de gestão dos ACS | Suportadas através do Microsoft Graph & do Azure AD Graph API. |

Se decidir que do Azure AD é o caminho correto reencaminhar para as aplicações e serviços, deve ter conhecimento de duas formas de integrar a sua aplicação com o Azure AD.

Para utilizar o Ws-Federação/WIF para integrar com o Azure AD, recomendamos seguintes [a abordagem descrita neste artigo](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). O artigo refere-se à configuração do Azure AD para baseados em SAML-início de sessão único, mas funciona para a configuração, bem como o Ws-Federation. Seguir esta abordagem é necessária uma licença do Azure AD Premium, mas tem duas vantagens:

- Obter a total flexibilidade de personalização de token do Azure AD. Isto permite-lhe personalizar afirmações emitidas pelo Azure AD para corresponderem às emitido por ACS, especialmente, incluindo a afirmação de identificador de nome ou o ID de utilizador. Tem de se certificar de que o utilizador IDs emitidos por correspondência do Azure AD, os que são emitidos por ACS, para que continuar a receber de identificadores de utilizador consistente para os seus utilizadores, mesmo depois de alterar as tecnologias.
- Pode configurar um token de assinatura de certificado específico da aplicação, cuja duração pode controlar.

<!--

Possible nameIdentifiers from ACS (via AAD or ADFS):
- ADFS - Whatever ADFS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> Contínuo com esta abordagem requer uma licença do Azure AD Premium. Se for um cliente de ACS e requerem uma licença de premium para configurar o início de sessão único para uma aplicação, nos entrar e, iremos satisfeitos fornecer licenças de programador para utilização.

Uma abordagem alternativa passa por seguir [este exemplo de código](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), que fornece ligeiramente diferentes instruções sobre como configurar o Ws-Federation. Este exemplo de código não utiliza WIF, mas em vez disso, o middleware de ASP.NET 4.5 OWIN. No entanto, as instruções para o registo de aplicação são válidas para aplicações que utilizam o WIF e não necessitam de uma licença do Azure AD Premium.  Se escolher esta abordagem, heneed compreender [rollover da chave de assinatura no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Esta abordagem utiliza o Azure AD global de assinatura de tokens de problema a chave. Por predefinição, o WIF não atualiza automaticamente as chaves de assinatura. Quando o Azure AD roda respetivas chaves de assinatura global, a implementação de WIF tem de estar preparado para aceitar as alterações.

Se integrar com o Azure AD através de protocolos de OpenID Connect ou OAuth, é recomendável fazê-lo.  Temos um vasto conjunto documentação e orientações sobre como integrar o Azure AD na sua aplicação web disponível no nosso [guia para programadores do Azure AD](http://aka.ms/aaddev).

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrating-to-azure-ad-b2c"></a>Migrar para o Azure AD B2C

Outro caminho de migração a ter em consideração é o Azure AD B2C.  B2C é uma autenticação na nuvem de serviço, semelhante às ACS, permite aos programadores outsource os respetivos lógica de gestão de identidade e autenticação para um serviço em nuvem.  É um serviço pago (com os escalões gratuitos & premium) concebido para o consumidor com aplicações que podem ter até milhões de utilizadores ativos.

Como ACS, uma das funcionalidades do B2C mais apelativo é que suporta vários tipos de contas. Com o B2C, que pode iniciar sessão utilizadores com os respetivos Facebook, Google, Microsoft, LinkedIn, GitHub, contas de Yahoo e muito mais. Além disso B2C suporta "contas locais", ou nome de utilizador e palavras-passe que os utilizadores criam especificamente para a sua aplicação. O Azure AD B2C também oferece extensibilidade avançada que pode utilizar para personalizar os fluxos de início de sessão. Não, no entanto, suporta o leque de protocolos de autenticação & formatos de token que podem necessitar de clientes de ACS. Também não pode ser utilizado para obter os tokens & consultar informações adicionais sobre o utilizador do fornecedor de identidade, Microsoft ou, caso contrário.

A tabela seguinte compara as funcionalidades do ACS relevantes para as aplicações web com os disponíveis no Azure AD B2C. Um nível elevado, **do Azure AD B2C provavelmente é a escolha certa para a migração se consumidor com acesso à sua aplicação, ou se suporta vários tipos de contas.**

| Capacidade | Suporte de ACS | Suporte do Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Tipos de contas** | | |
| Trabalhar & profissional contas Microsoft | Suportado | Suportada através de políticas personalizadas  |
| Contas do Active Directory do Windows Server & ADFS | Suportada através de Federação direta com o AD FS | Suportada através de Federação de SAML através de políticas personalizadas |
| Contas de outros sistemas de gestão de identidades empresariais | Suportada através de Federação direta através de Ws-Federation | Suportada através de Federação de SAML através de políticas personalizadas |
| Contas Microsoft para utilização pessoal (Windows Live ID) | Suportado | Suportado | 
| Contas do Facebook, Google, Yahoo | Suportado | Facebook & Google suportado nativamente, Yahoo suportada através de Federação OpenID Connect utilizando as políticas personalizadas |
| **Protocolos de & compatibilidade SDK** | | |
| Windows Identity Foundation (WIF) | Suportado | Não é suportado. |
| WS-Federation | Suportado | Não é suportado. |
| OAuth 2.0 | Suporte para rascunho 13 | Suporte para RFC 6749, a especificação de mais moderna. |
| WS-Trust | Suportado | Não é suportado. |
| **Formatos de token** | | |
| JSON Web Tokens (JWTs) | Suportado na versão Beta | Suportado |
| Tokens de SAML 1.1 | Suportado | Não suportado |
| Tokens de SAML 2.0 | Suportado | Não suportado |
| Tokens Web simples (SWT) | Suportado | Não suportado |
| **Personalizações** | | |
| Realm inicial personalizável/conta de deteção diretriz da IU | Código transferível, que pode ser incorporado nas aplicações | IU totalmente personalizável através do CSS personalizado. |
| Carregar certificados de assinatura de tokens personalizado | Suportado | Assinatura chaves personalizadas, certificados, suportadas através de políticas personalizadas. |
| Personalizar afirmações no | Afirmações de entrada de pass-through a partir de fornecedores de identidade<br />Obtenha o acesso token do fornecedor de identidade como uma afirmação<br />Emitir afirmações de saída com base nos valores de afirmações de entrada<br />Emitir afirmações de saída com valores constantes | Pode afirmações de pass-through a partir de fornecedores de identidade. Políticas personalizadas necessárias para alguns afirmações.<br />Não é possível obter o token de acesso do fornecedor de identidade como uma afirmação<br />Podem emitir afirmações de saída com base nos valores de afirmações de entrada através de políticas personalizadas<br />Pode emitir afirmações de saída com valores constantes através de políticas personalizadas |
| **Automatização** | | |
| Automatizar tarefas de gestão de configuração / | Suportadas através do serviço de gestão dos ACS | Criação de utilizadores permitido através do Azure AD Graph API. Não é possível criar inquilinos B2C, aplicações ou políticas através de programação. |

Se decidir que o Azure AD B2C é o caminho correto reencaminhar para as aplicações e serviços, deve começar com os seguintes recursos:

- [Documentação do Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Políticas personalizadas do Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [O Azure AD B2C preços](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="other-migration-options"></a>Outras opções de migração

Se nem do Azure AD nem do Azure AD B2C satisfaça as necessidades da sua aplicação web, contacte-nos e podemos ajudar a identificar o caminho melhor reencaminhar.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD nor Azure AD B2C is sufficient to replace ACS in your web applications without making major code changes.  Some common examples might include:

- web applications using WIF and/or WS-Federation for sign-in with social identity providers such as Google or Facebook.
- web applications performing direct federation to an enterprise IDP over the Ws-Federation protocol.
- web applications that require the access token issued by a social identity provider (such as Google or Facebook) as a claim in the tokens issued by ACS.
- web applications with complex token transformation rules that Azure AD or Azure AD B2C cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

<!--

## Sharepoint 2010, 2013, 2016

TODO: AAD only, use AAD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-using-active-authentication"></a>Serviços Web através da autenticação do Active Directory

Para os serviços web protegidos com tokens emitidos pelo ACS, ACS fornecidas as seguintes funcionalidades e capacidades:

- Capacidade de registar um ou mais **identidades do serviço** no seu espaço de nomes do ACS que pode ser utilizado para pedir tokens.
- Suporte para os protocolos de OAuth MOLDAR &13;de rascunho do OAuth 2.0 para pedir tokens, utilizando os seguintes tipos de credenciais:
    - Uma palavra-passe simple criada para a identidade de serviço
    - A sessão iniciada SWT utilizando uma chave simétrica ou X509 certificado
    - Um token SAML emitido por um fornecedor de identidade fidedigna (normalmente, uma instância do AD FS)
- Suporte para os seguintes formatos de token: token de web JSON (JWT), SAML 1.1, SAML 2.0 e simples web token (SWT).
- Regras de transformação de token simples

Identidades de serviço em ACS são tipicamente usadas para implementar o servidor para servidor (S2S), como a autenticação.  

#### <a name="migrating-to-azure-active-directory"></a>Migrar para o Azure Active Directory

A nossa recomendação para este tipo de fluxo de autenticação está a migrar para [ **do Azure Active Directory** ](https://azure.microsoft.com/develop/identity/signin/) (Azure AD). Azure AD é o fornecedor de identidade baseada em nuvem para a Microsoft funciona & profissional contas - é o fornecedor de identidade para o Office 365, Azure e muito mais. Mas também pode ser utilizado para autenticação de servidor para servidor através da implementação do Azure AD a concessão de credenciais de cliente OAuth.  A tabela seguinte compara as capacidades do ACS na autenticação de servidor para servidor com estão disponíveis no Azure AD.

| Capacidade | Suporte de ACS | Suporte do Azure AD |
| ---------- | ----------- | ---------------- |
| Como registar um serviço web | Crie uma entidade confiadora no portal de gestão dos ACS. | Crie uma aplicação web do Azure AD no portal do Azure. |
| Como registar um cliente | Crie uma identidade de serviço no portal de gestão dos ACS. | Crie outra aplicação web do Azure AD no portal do Azure. |
| Protocolo utilizado | Protocolo OAuth ENCAPSULAR<br />As credenciais de cliente OAuth 2.0 rascunho 13 conceder | Conceder credenciais de cliente OAuth 2.0 |
| Métodos de autenticação de cliente | Palavra-passe simples<br />SWT assinado<br />Token SAML do IDP federada | Palavra-passe simples<br />JWT assinado |
| Formatos de token | JWT<br />SAML 1.1<br />SAML 2.0<br />SWT<br /> | Apenas JWT |
| Transformação de token | Adicionar afirmações personalizadas<br />Lógica de emissão de afirmações simples de depois de se | Adicionar afirmações personalizadas | 
| Automatizar tarefas de gestão de configuração / | Suportadas através do serviço de gestão dos ACS | Suportadas através do Microsoft Graph & do Azure AD Graph API. |

Para obter orientações sobre a implementação de servidor para servidor de cenários, consulte os seguintes recursos:

- [Secção de serviços do Guia para programadores do Azure AD](https://aka.ms/aaddev).
- [Exemplo de código do daemon utilizando as credenciais de cliente de palavra-passe simples](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Exemplo de código do daemon utilizando credenciais de certificado de cliente](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="other-migration-options"></a>Outras opções de migração

Se o Azure AD não satisfazer as necessidades do seu serviço web, deixe um comentário e podemos ajudar a identificar o plano melhor do seu incidente específico.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD's client credentials OAuth grant implementation is not sufficient to replace ACS in your architecture without major code changes.  Some common examples might include:

- server-to-server authentication using token formats other than JWTs.
- server-to-server authentication using an input token provided by an external identity provider.
- server-to-server authentication with token transformation rules that Azure AD cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

## <a name="questions-concerns--feedback"></a>Perguntas, preocupações e comentários

Compreendemos que muitos clientes de ACS não irão encontrar um caminho de migração limpar depois de ler este artigo e poderão ter alguns assistência ou orientações para determinar o plano adequado. Se gostaria de abordar a cenários de migração & perguntas, deixe um comentário nesta página.
