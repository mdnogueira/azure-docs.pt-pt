---
title: "Gerir o acesso a aplicações em nuvem, restringindo os inquilinos - Azure | Microsoft Docs"
description: "Como utilizar as restrições de inquilino para gerir os utilizadores que pode aceder a aplicações com base no seu inquilino do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: kgremban
ms.openlocfilehash: 7288f8fa173f8018570cd17aa7274f56a4eead41
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>As aplicações em nuvem restrições de inquilino de utilização para gerir o acesso ao SaaS

Grandes organizações destacar segurança pretendem mover para serviços como o Office 365 em nuvem, mas precisa de saber que os seus utilizadores só podem aceder a recursos aprovados. Tradicionalmente, as empresas restringir os nomes de domínio ou endereços IP, quando pretendem gerir o acesso. Esta abordagem ocorrer uma falha de um universo de onde as aplicações SaaS estão alojadas numa nuvem pública, em execução em nomes de domínio partilhado como outlook.office.com e login.microsoftonline.com. Bloquear estes endereços seria manter os utilizadores acedam ao Outlook na web completamente, em vez de apenas restringi-los a identidades aprovadas e os recursos.

Solução do Azure do Active Directory para este desafio é uma funcionalidade denominada restrições de inquilino. Restrições de inquilino permite às organizações controlar o acesso a aplicações de cloud de SaaS, com base no inquilino do Azure AD, as aplicações que utilizam para o início de sessão único. Por exemplo, poderá permitir o acesso a aplicações do Office 365 da sua organização, impedindo o acesso a instâncias de outras organizações destas mesmas aplicações.  

Restrições de inquilino permite às organizações para especificar a lista de inquilinos que os utilizadores têm permissão de acesso. Em seguida, do Azure AD apenas concede acesso a estes permitido inquilinos.

Este artigo incida no inquilino restrições para o Office 365, mas a funcionalidade deverá trabalhar com qualquer aplicação de cloud de SaaS que utiliza protocolos de autenticação moderna com o Azure AD para o início de sessão único. Se utilizar aplicações com um diferente do Azure AD de inquilino do inquilino utilizado pelo Office 365 de SaaS, certifique-se de que todas as necessárias inquilinos são permitidos. Para obter mais informações sobre as aplicações de cloud de SaaS, consulte o [Active Directory Marketplace](https://azure.microsoft.com/en-us/marketplace/active-directory/).

## <a name="how-it-works"></a>Como funciona

A solução global inclui os seguintes componentes: 

1. **Azure AD** – se o `Restrict-Access-To-Tenants: <permitted tenant list>` está presentes, Azure AD apenas problemas tokens de segurança para os inquilinos permitidos. 

2. **Infraestrutura de servidor de proxy no local** – um dispositivo de proxy com capacidade de inspeção de SSL, configurada para inserir o cabeçalho que contém a lista de permitidos inquilinos para o tráfego destinado para o Azure AD. 

3. **Software de cliente** – para suportar restrições de inquilino, software de cliente tem de pedir tokens diretamente a partir do Azure AD, para que o tráfego pode ser intercetado pela infraestrutura do proxy. Restrições de inquilino é atualmente suportado por aplicações do Office 365 baseada no browser e pelos clientes do Office quando é utilizada a autenticação moderna (por exemplo, o OAuth 2.0). 

4. **A autenticação moderna** – serviços em nuvem tem de utilizar a autenticação moderna para utilizar as restrições de inquilino e bloquear o acesso a todos os inquilinos não permitido. Serviços do Office 365 na nuvem tem de ser configurados para utilizar protocolos de autenticação moderna por predefinição. Para informações mais recentes sobre o suporte do Office 365 para a autenticação moderna, leia o artigo [autenticação moderna do Office 365 atualizado](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/).

O diagrama seguinte ilustra o fluxo de tráfego de alto nível. Inspeção SSL é necessário apenas nos tráfego para o Azure AD, não para os serviços em nuvem do Office 365. Este distinção é importante porque o volume de tráfego de autenticação para o Azure AD é normalmente muito menor volume de tráfego para aplicações de SaaS, como o Exchange Online e SharePoint Online.

![Fluxo de tráfego de restrições de inquilino - diagrama](./media/active-directory-tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurar restrições de inquilino

Existem dois passos para começar com restrições de inquilino. O primeiro passo é certificar-se de que os clientes podem ligar para os endereços corretos. O segundo consiste em configurar a sua infraestrutura de proxy.

### <a name="urls-and-ip-addresses"></a>Os URLs e endereços IP

Para utilizar o inquilino restrições, os clientes tem de ser possível estabelecer ligação com os seguintes URLs de AD do Azure para autenticar: login.microsoftonline.com, login.microsoft.com e login.windows.net. Além disso, para o acesso do Office 365, os clientes também tem de ser capazes de ligar para os FQDNs/URLs e endereços IP definidos no [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Configuração de proxy e de requisitos

A configuração seguinte é obrigatório para ativar restrições de inquilino através da infraestrutura de proxy. Esta orientação é genérica, pelo que devem consultar a documentação do seu fornecedor de proxy para passos de implementação específicos.

#### <a name="prerequisites"></a>Pré-requisitos

- O proxy tem de ser capaz de efetuar interception SSL, inserção do cabeçalho HTTP e filtrar destinos utilizando FQDNs/URLs. 

- Os clientes têm de confiar a cadeia de certificados apresentada por proxy para as comunicações SSL. Por exemplo, se forem utilizados certificados a partir de uma PKI interna, o certificado interno de emissora raiz certificado autoridade tem de ser fidedigno.

- Esta funcionalidade está incluída em subscrições do Office 365, mas se pretender utilizar as restrições de inquilino para controlar o acesso a outras aplicações de SaaS são necessárias, em seguida, licenças do Azure AD Premium 1.

#### <a name="configuration"></a>Configuração

Para cada pedido recebido login.microsoftonline.com, login.microsoft.com e login.windows.net, insira dois cabeçalhos HTTP: *restringir acesso para inquilinos* e *contexto de restringir acesso*.

Os cabeçalhos devem incluir os seguintes elementos: 
- Para *restringir acesso para inquilinos*, um valor de \<permitido lista inquilino\>, que é uma lista separada por vírgulas de inquilinos para permitir que os utilizadores acedam a. Qualquer domínio que está registado com um inquilino pode ser utilizado para identificar o inquilino nesta lista. Por exemplo, para permitir o acesso aos inquilinos Contoso e Fabrikam, o par nome/valor aspeto:`Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- Para *contexto de restringir acesso*, um valor de um ID de diretório única, declarar que inquilino é definir as restrições de inquilino. Por exemplo, para declarar Contoso do inquilino que defina a política de restrições de inquilino, o par nome/valor aspeto:`Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Pode encontrar o ID de diretório no [portal do Azure](https://portal.azure.com). Inicie sessão como administrador, selecione **do Azure Active Directory**, em seguida, selecione **propriedades**.

Para impedir que os utilizadores a inserir os seus próprios cabeçalho HTTP com inquilinos não aprovado, o proxy tem de substituir o cabeçalho de restringir acesso para inquilinos se já se encontra presente no pedido recebido. 

Os clientes tem de ser forçados a utilizar o proxy para todos os pedidos login.microsoftonline.com, login.microsoft.com e login.windows.net. Por exemplo, se os ficheiros PAC são utilizados para direcionar clientes para utilizar o proxy, os utilizadores finais devem conseguir editar ou desativar os ficheiros PAC.

## <a name="the-user-experience"></a>A experiência de utilizador

Esta secção mostra a experiência dos utilizadores finais e administradores.

### <a name="end-user-experience"></a>Experiência do utilizador final

Um utilizador de exemplo está na rede Contoso, mas está a tentar aceder à instância de uma partilhado aplicação SaaS, como o Outlook online Fabrikam. Se a Contoso é um inquilino não permitido para essa instância, o utilizador verá a seguinte página:

![Acesso negado página utilizadores inquilinos não permitido](./media/active-directory-tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>Experiência da administração

Enquanto a configuração de restrições de inquilino é efetuada na infraestrutura de proxy empresarial, administradores podem aceder os relatórios de restrições de inquilino no portal do Azure diretamente. Para ver os relatórios, aceda à página de descrição de geral do Azure Active Directory, em seguida, procure em 'Outras capacidades' '.

ID de administrador para o inquilino especificado como o inquilino de contexto de acesso de restrito pode utilizar este relatório para ver todos os inícios de sessão bloqueados devido à política de restrições de inquilino, incluindo a identidade utilizada e o diretório de destino.

![Utilizar o portal do Azure para ver as tentativas de início de sessão restritas](./media/active-directory-tenant-restrictions/portal-report.png)

Como outros relatórios no portal do Azure, pode utilizar filtros para especificar o âmbito do seu relatório. Pode filtrar por um utilizador específico, a aplicação, o cliente ou o intervalo de tempo.

## <a name="office-365-support"></a>Suporte do Office 365

Aplicações do Office 365 tem de cumprir os critérios de dois para suportar totalmente as restrições de inquilino:

1. O cliente utilizado suporta a autenticação moderna
2. A autenticação moderna está ativada como o protocolo de autenticação predefinido para o serviço em nuvem.

Consulte [autenticação moderna do Office 365 atualizado](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) para as informações mais recentes sobre do Office que os clientes suportam atualmente autenticação moderna. Nessa página também inclui ligações para instruções para ativar a autenticação moderna específico Exchange Online e Skype para empresas Online aos inquilinos. A autenticação moderna já está ativada por predefinição no SharePoint Online.

Restrições de inquilino é atualmente suportado por aplicações baseadas no browser do Office 365 (do SharePoint do Portal do Office, Yammer, sites, Outlook no Web, etc.). Para clientes Espesso (Outlook, Skype para empresas, Word, Excel, PowerPoint, etc.) Restrições de inquilino podem ser impostas apenas quando é utilizada a autenticação moderna.  

Outlook e o Skype para clientes de negócio que suportam a autenticação moderna são ainda podem utilizar protocolos legados contra inquilinos em que a autenticação moderna não está ativada, ignorando eficazmente as restrições de inquilino. Para o Outlook no Windows, os clientes podem optar por implementar restrições de impedir que os utilizadores finais adicionar contas de correio não aprovado para os seus perfis. Por exemplo, consulte o [impedir adicionar contas de Exchange não predefinidas](http://gpsearch.azurewebsites.net/default.aspx?ref=1) definição de política de grupo. Para o Outlook em plataformas não sejam Windows e para o Skype para empresas em todas as plataformas, suporte completo de restrições de inquilino não está atualmente disponível.

## <a name="testing"></a>Testes

Se pretender experimentar as restrições de inquilino antes de a implementar para toda a organização, existem duas opções: uma abordagem baseada em anfitrião utilizando uma ferramenta como o Fiddler ou uma implementação faseada de definições de proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler para uma abordagem baseada no anfitrião

Fiddler é uma web livre depuração proxy que pode ser utilizado para capturar e modificam o tráfego HTTP/HTTPS, incluindo a inserir os cabeçalhos de HTTP. Para configurar o Fiddler para testar as restrições de inquilino, execute os seguintes passos:

1.  [Transfira e instale o Fiddler](http://www.telerik.com/fiddler).
2.  Configurar o Fiddler para desencriptar o tráfego HTTPS, por [documentação de ajuda do Fiddler](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Configurar o Fiddler para inserir o *restringir acesso para inquilinos* e *contexto de restringir acesso* cabeçalhos utilizando regras personalizadas:
  1. Na ferramenta de depurador de Web Fiddler, selecione o **regras** menu e selecione **personalizar regras...** Para abrir o ficheiro de CustomRules.
  2. Adicione as seguintes linhas no início do *OnBeforeRequest* função. Substitua \<domínio de inquilino\> com um domínio registado seu inquilino, por exemplo, contoso.onmicrosoft.com. Substitua \<ID de diretório\> com o identificador do seu inquilino do Azure AD GUID.

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
  ```

  Se precisar de permitir que vários inquilinos, utilize uma vírgula para separar os nomes de inquilino. Por exemplo:

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. Guarde e feche o ficheiro CustomRules.

Depois de configurar o Fiddler, pode capturar tráfego acedendo ao **ficheiro** menu e selecionando **capturar tráfego**.

### <a name="staged-rollout-of-proxy-settings"></a>Implementação faseada de definições de proxy

Consoante as capacidades da sua infraestrutura de proxy, poderá testar a implementação das definições para os seus utilizadores. Seguem-se algumas opções de alto nível para consideração:

1.  Utilize ficheiros PAC para os utilizadores de teste de apontar para uma infraestrutura de proxy de teste, enquanto que os utilizadores normais continuam a utilizar a infraestrutura do proxy de produção.
2.  Alguns servidores proxy podem suportar utilizar grupos de configurações diferentes.

Consulte a documentação do servidor proxy para obter detalhes específicos.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre [autenticação moderna do Office 365 atualizado](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)

- Reveja o [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
