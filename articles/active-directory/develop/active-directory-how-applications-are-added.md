---
title: "Como são adicionadas aplicações ao Azure Active Directory."
description: "Este artigo descreve como as aplicações são adicionadas a uma instância do Azure Active Directory."
services: active-directory
documentationcenter: 
author: shoatman
manager: kbrint
editor: 
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2016
ms.author: shoatman
ms.custom: aaddev
ms.openlocfilehash: 6ffcfcb7ed071a12b0b3495ad534fd00f6d6ad99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Como e por que razão são adicionadas aplicações para o Azure AD
Uma das ações inicialmente puzzling quando visualizar uma lista de aplicações na sua instância do Azure Active Directory é compreender onde as aplicações provém e por que razão são não existe.  Este artigo irá fornecer uma descrição geral de alto nível de como as aplicações são representadas no diretório e lhe fornecem contexto que irá ajudá-lo a compreender como uma aplicação veio para estar no seu diretório.

## <a name="what-services-does-azure-ad-provide-to-applications"></a>Os serviços do Azure AD fornece às aplicações?
São adicionadas aplicações ao Azure AD para tirar partido de uma ou mais serviços fornece.  Esses serviços incluem:

* Aplicação de autenticação e autorização
* Autenticação de utilizador & autorização
* Início de sessão único (SSO) utilizando a palavra-passe ou Federação
* Aprovisionamento de utilizador e sincronização
* Controlo de acesso baseado em funções; Utilize o diretório para definir funções da aplicação para executar funções com base em verificações de autorização numa aplicação.
* Serviços de autorização do oAuth (utilizados pelo Office 365 e outras aplicações Microsoft para autorizar o acesso a APIs/recursos).
* Publicação de aplicações & proxy; Publicar uma aplicação de uma rede privada à internet

## <a name="how-are-applications-represented-in-the-directory"></a>Como as aplicações são representadas no diretório?
As aplicações são representadas no Azure AD com objetos de 2: um objeto de aplicação e um objeto principal do serviço.  Há um objeto de aplicação, registado em "home" / "proprietário" ou "publicar" diretório e um ou mais objetos principais que representa a aplicação de cada diretório em que este aja de serviço.  

O objeto de aplicação descreve a aplicação para o Azure AD (o serviço de multi-inquilino) e pode incluir qualquer um dos seguintes procedimentos: (*nota*: não se trata de uma lista exaustiva.)

* Nome, o logótipo e fabricante
* Segredos (simétricas e/ou assimétricos chaves utilizados para autenticar a aplicação)
* Dependências de API (oAuth)
* APIs/recursos/âmbitos publicados (oAuth)
* Funções (RBAC) de aplicação
* Metadados SSO e a configuração (SSO)
* Aprovisionamento de utilizadores metadados e configuração
* Metadados de proxy e de configuração

O principal de serviço é um registo da aplicação em cada diretório, onde a aplicação funciona, incluindo o respetivo diretório raiz.  O principal de serviço:

* Refere-se voltar a um objeto de aplicação através da propriedade de id de aplicação
* Registos de utilizador e local atribuições de funções de aplicação de grupo
* Regista as permissões de utilizador e administrador local concedidos à aplicação
  * Por exemplo: permissão para a aplicação aceder a um e-mail de determinados utilizadores
* Regista as políticas locais, incluindo a política de acesso condicional
* Regista locais alternativas as definições locais para uma aplicação
  * Regras de transformação de afirmações
  * Mapeamentos de atributos (aprovisionamento de utilizadores)
  * Funções de aplicação específica de inquilino (se a aplicação suporta funções personalizadas)
  * Nome do/logótipo

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>Um diagrama de objetos de aplicação e principais de serviço em diretórios
![Um diagrama que ilustra como aplicação objetos e serviço principais existente com instâncias do Azure AD.][apps_service_principals_directory]

Como pode ver do diagrama acima.  A Microsoft tem dois diretórios internamente (no lado esquerdo) utiliza para publicar aplicações.

* Uma para Microsoft Apps (diretório de serviços da Microsoft)
* Uma para aplicações de terceiros 3rd previamente integradas (diretório de Galeria de aplicações)

Aplicação publicadores/fornecedores que integrar com o Azure AD são necessários para ter um diretório de publicação.  (Alguns diretório SAAS).

Aplicações que adicionou sozinho incluem:

* Aplicações que desenvolvidas (integradas com o AAD)
* Aplicações ligados para single-sign-on
* As aplicações publicadas com o proxy de aplicações do Azure AD.

### <a name="a-couple-of-notes-and-exceptions"></a>Algumas notas e exceções
* Nem todos os principais de serviço apontam de volta para objectos da aplicação.  Huh? Quando o Azure AD foi criado originalmente os serviços fornecidos às aplicações muito mais limitados e o principal de serviço era suficiente para estabelecer uma identidade de aplicação.  O principal de serviço original era próximo na forma da conta de serviço do Windows Server Active Directory.  Por este motivo 's ainda é possível criar principais de serviço utilizando o Azure AD PowerShell sem primeiro criar um objeto de aplicação.  A Graph API requer um objeto de aplicação antes de criar um serviço principal.
* Nem todas as informações descritas acima atualmente está exposto através de programação.  Seguem-se apenas disponível na IU:
  * Regras de transformação de afirmações
  * Mapeamentos de atributos (aprovisionamento de utilizadores)
* Para obter mais informações detalhadas sobre o principal de serviço e objetos de aplicação consulte a documentação de referência da API de REST do Azure AD Graph.  *Sugestão*: documentação do Azure AD Graph API é a coisa mais próxima para uma referência de esquema para o Azure AD que está atualmente disponível.  
  * [Aplicação](https://msdn.microsoft.com/library/azure/dn151677.aspx)
  * [Principal de serviço](https://msdn.microsoft.com/library/azure/dn194452.aspx)

## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>Como aplicações são adicionadas à minha instância do Azure AD?
Existem várias formas de que uma aplicação pode ser adicionada para o Azure AD:

* Adicionar uma aplicação a partir de [Galeria de aplicações do Azure Active Directory](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Inicie a cópia de segurança/para um 3rd aplicações de terceiros integrada com o Azure Active Directory (por exemplo: [Smartsheet](https://app.smartsheet.com/b/home) ou [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
  * Durante o início de sessão verticalmente/sessão dos utilizadores-lhe pedido para conceder permissão para a aplicação para aceder ao respetivo perfil e outras permissões.  A primeira pessoa a dar consentimento faz com que um principal de serviço que representa a aplicação a ser adicionado ao diretório.
* Início de sessão cima/para serviços online da Microsoft, como [do Office 365](http://products.office.com/)
  * Quando subscreve o Office 365 ou iniciar uma versão de avaliação são criados um ou mais principais de serviço no diretório que representa os serviços que são utilizados para fornecer todas as funcionalidades associadas com o Office 365.
  * Alguns serviços do Office 365, como o SharePoint criar principais do serviço de forma em curso para permitir a comunicação segura entre componentes, incluindo fluxos de trabalho.
* Adicionar uma aplicação estiver a desenvolver, consulte o Portal de gestão do Azure: https://msdn.microsoft.com/library/azure/dn132599.aspx
* Adicione uma aplicação estiver a desenvolver com o Visual Studio consulte:
  * [Métodos de autenticação ASP.Net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
  * [Serviços ligados](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Adicionar uma aplicação para utilizar para utilizar o [Proxy de aplicações do Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Ligar uma aplicação para início de sessão único utilizando SAML ou SSO de palavra-passe
* Muitas outras incluindo várias experiências do Programador no Azure e/no Explorador de API ocorre em todos os centros de programador

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Quem tem permissão para adicionar aplicações para os meus instância do Azure AD?
Apenas os administradores globais podem:

* Adicionar aplicações a partir da Galeria de aplicações do Azure AD (aplicações pré-integradas de terceiros 3rd)
* Publicar uma aplicação com o Proxy de aplicações do Azure AD

Todos os utilizadores no seu diretório tem direitos para adicionar as aplicações que estiver a desenvolver e discrição sobre quais as aplicações que partilham/conceder acesso aos respetivos dados organizacionais.  *Lembre-se a cópia de segurança/início de sessão de utilizador para uma aplicação e conceder permissões pode resultar num principal de serviço a ser criado.*

Este pode inicialmente som que dizem respeito à, mas manter o seguinte em consideração:

* As aplicações foram capazes de tirar partido do Windows Server Active Directory para a autenticação de utilizador de muitos anos sem necessidade de aplicação a ser registado/registados no diretório.  Agora a organização será tem melhorado visibilidade para exatamente como muitas aplicações estão a utilizar o diretório e what for.
* Não é necessário para administração orientadas por processo de publicação/registo de aplicação.  Serviços de Federação do Active Directory foi provável que um administrador tinha de adicionar uma aplicação como uma entidade confiadora em nome de programadores.  Agora, os programadores podem Self-Service.
* Os utilizadores a assinatura de entrada/até aplicações ao utilizar as contas da organização para fins empresariais são uma boa coisa.  Se são, subsequentemente, deixam a organização que irão perder o acesso a respetiva conta na aplicação que estavam a utilizar.
* Ter um registo das quais os dados que foi partilhados com a qual a aplicação é uma boa coisa.  Os dados são mais transportable que nunca e ter um registo de quem partilhados os dados com as aplicações que limpar é útil.
* As aplicações que utilizam o Azure AD para oAuth decidir exatamente que permissões que os utilizadores têm capacidade para conceder a aplicações e de que necessita de permissões de administrador para aceitar.  Deve passar a sem a indicar que apenas os administradores podem autorizar âmbitos maiores e mais significativas permissões.
* Os utilizadores a adicionar e permitindo que as aplicações aceder aos respetivos dados são auditadas eventos, para que possa visualizar os relatórios de auditoria no portal do Azure Managment para determinar a forma como uma aplicação foi adicionada para o diretório.

**Nota:** *Microsoft próprio foi operar com a configuração predefinida para muitos meses agora.*

Todos os que consiga aceder tal é possível impedir os utilizadores no seu diretório de adicionar aplicações e do exercising discrição sobre as informações que partilham com aplicações ao modificar a configuração de diretório no portal de gestão do Azure.  A seguinte configuração pode ser acedida no portal de gestão do Azure, no separador de "Configurar" do seu diretório.

![Uma captura de ecrã da IU para configurar as definições de aplicação integrada][app_settings]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como adicionar aplicações para o Azure AD e como configurar os serviços de aplicações.

* Os programadores: [Saiba como integrar uma aplicação no AAD](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* Os programadores: [rever o código de exemplo para aplicações integradas do Active Directory do Azure no GitHub](https://github.com/AzureADSamples)
* Os programadores e profissionais de TI: [reveja a documentação da REST API para o Active Directory Graph API do Azure](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* Os profissionais de TI: [aprender a utilizar aplicações previamente integradas do Active Directory do Azure a partir da Galeria de aplicações](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* Os profissionais de TI: [obter tutoriais para configurar aplicações previamente integradas específicas](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* Os profissionais de TI: [Saiba como publicar uma aplicação com o Proxy de aplicações do Active Directory do Azure](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>Consultar também
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](../active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:../media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg
