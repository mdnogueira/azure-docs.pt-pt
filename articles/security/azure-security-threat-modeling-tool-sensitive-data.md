---
title: "Os dados confidenciais - ferramenta de modelação de ameaça Microsoft - Azure | Microsoft Docs"
description: "Mitigações ameaças exposta na ferramenta de modelação de ameaça"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 21d1ba02052862e16ef27ec313d53cd0bffcc21a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-sensitive-data--mitigations"></a>Moldura de segurança: Dados confidenciais | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Limite de fidedignidade de máquina** | <ul><li>[Certifique-se de que os binários ocultados se contêm informações confidenciais](#binaries-info)</li><li>[Considere utilizar o sistema de ficheiros encriptados (EFS) é utilizado para proteger dados confidenciais do utilizador específico](#efs-user)</li><li>[Certifique-se de que são encriptados os dados confidenciais armazenados pela aplicação no sistema de ficheiros](#filesystem)</li></ul> | 
| **Aplicação Web** | <ul><li>[Certifique-se de que o conteúdo confidencial não esteja em cache no browser](#cache-browser)</li><li>[Encriptar secções dos ficheiros de configuração da aplicação Web que contêm dados confidenciais](#encrypt-data)</li><li>[Desativar explicitamente o atributo HTML de conclusão automática no entradas e formulários confidenciais](#autocomplete-input)</li><li>[Certifique-se de que os dados confidenciais apresentados no ecrã de utilizador está oculto](#data-mask)</li></ul> | 
| **Base de Dados** | <ul><li>[Máscara de dados dinâmicos implementar para limitar os utilizadores de exposição não privilegiado dados confidenciais](#dynamic-users)</li><li>[Certifique-se de que as palavras-passe são armazenadas no formato de salted hash](#salted-hash)</li><li>[Certifique-se de que os dados confidenciais nas colunas de base de dados são encriptados](#db-encrypted)</li><li>[Certifique-se de que a encriptação ao nível da base de dados (TDE) está ativada](#tde-enabled)</li><li>[Certifique-se de que as cópias de segurança da base de dados são encriptadas](#backup)</li></ul> | 
| **API Web** | <ul><li>[Certifique-se de que os dados confidenciais relevantes à Web API não são armazenados no armazenamento do browser](#api-browser)</li></ul> | 
| Base de dados de documento do Azure | <ul><li>[Encriptar dados confidenciais armazenados no DocumentDB](#encrypt-docdb)</li></ul> | 
| **Limite de fidedignidade de VM do IaaS do Azure** | <ul><li>[Utilizar o Azure Disk Encryption para encriptar os discos utilizados por máquinas virtuais](#disk-vm)</li></ul> | 
| **Limite de fidedignidade de recursos de infraestrutura de serviço** | <ul><li>[Encriptar os segredos em aplicações de Service Fabric](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Efetuar modelação de segurança e utilizar unidades de negócio/equipas quando necessário](#modeling-teams)</li><li>[Minimizar o acesso à partilha funcionalidade entidades crítico](#entities)</li><li>[Preparar os utilizadores sobre os riscos associados a funcionalidade de partilha do Dynamics CRM e práticas de segurança boa](#good-practices)</li><li>[Incluir uma regra de padrões de desenvolvimento proscribing que mostra detalhes de configuração na gestão de exceções](#exception-mgmt)</li></ul> | 
| **Armazenamento do Azure** | <ul><li>[Utilizar a encriptação do serviço de armazenamento do Azure (SSE) de dados Inativos (pré-visualização)](#sse-preview)</li><li>[Utilizar encriptação do lado do cliente para armazenar dados confidenciais no armazenamento do Azure](#client-storage)</li></ul> | 
| **Cliente para dispositivos móveis** | <ul><li>[Encriptar confidenciais ou dados PII escritos para o armazenamento local de telemóveis](#pii-phones)</li><li>[Obfuscate binários gerados antes de distribuir aos utilizadores finais](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Definir o clientCredentialType certificado ou o Windows](#cert)</li><li>[Modo de segurança de WCF não está ativado](#security)</li></ul> | 

## <a id="binaries-info"></a>Certifique-se de que os binários ocultados se contêm informações confidenciais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os binários ocultados se contêm informações confidenciais, tais como segredos de negócio, lógica de negócio confidenciais que deverá invertida não. Trata-se parar a engenharia inversa, de assemblagens. Ferramentas como `CryptoObfuscator` pode ser utilizado para esta finalidade. |

## <a id="efs-user"></a>Considere utilizar o sistema de ficheiros encriptados (EFS) é utilizado para proteger dados confidenciais do utilizador específico

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Considere utilizar o sistema de ficheiros encriptados (EFS) é utilizado para proteger os dados de utilizadores específicos confidenciais adversários qualificados com acesso físico ao computador. |

## <a id="filesystem"></a>Certifique-se de que são encriptados os dados confidenciais armazenados pela aplicação no sistema de ficheiros

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que são encriptados os dados confidenciais armazenados pela aplicação no sistema de ficheiros (por exemplo, utilizando a DPAPI), se não pode ser forçado EFS |

## <a id="cache-browser"></a>Certifique-se de que o conteúdo confidencial não esteja em cache no browser

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC6 de formulários, MVC5, Web |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Browsers podem armazenar informações para fins de colocação em cache e histórico. Estes ficheiros em cache são armazenados numa pasta, como a pasta Temporary Internet Files no caso do Internet Explorer. Quando estas páginas são referidas novamente, o browser apresenta-os partir da respetiva cache. Se a informações sensíveis, são apresentadas ao utilizador (por exemplo, os respetivos endereços, os detalhes do cartão de crédito, número de Segurança Social ou nome de utilizador), em seguida, esta informação pode ser armazenado na cache do browser e, por conseguinte, recuperável através de examinar a cache do browser ou pela Basta premir o botão "Novo" do browser. Defina o valor de cabeçalho de resposta de cache-control para "nenhum arquivo de" para todas as páginas. |

### <a name="example"></a>Exemplo
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-cache" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Exemplo
Isto pode ser implementado através de um filtro. Pode ser utilizado o exemplo seguinte: 
```C#
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a id="encrypt-data"></a>Encriptar secções dos ficheiros de configuração da aplicação Web que contêm dados confidenciais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Como: Encriptar secções de configuração no ASP.NET 2.0 utilizando DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [especificar um fornecedor de configuração protegidos](https://msdn.microsoft.com/library/68ze1hb2.aspx), [utilizando o Cofre de chaves do Azure para proteger os segredos de aplicação](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Passos** | Ficheiros de configuração, tais como o Web. config, appsettings.json são frequentemente utilizados para conter informações confidenciais, incluindo os nomes de utilizador, palavras-passe, as cadeias de ligação de base de dados e chaves de encriptação. Se não proteger estas informações, a aplicação é vulnerável a atacantes ou obtenção de informações confidenciais, tais como nomes de contas de utilizador e palavras-passe, os nomes de base de dados e os nomes dos servidores que utilizadores mal intencionados. Consoante o tipo de implementação (azure/no local), encriptar as secções confidenciais dos ficheiros de configuração utilizando a DPAPI ou serviços como o Cofre de chaves do Azure. |

## <a id="autocomplete-input"></a>Desativar explicitamente o atributo HTML de conclusão automática no entradas e formulários confidenciais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN: atributo de conclusão automática](http://msdn.microsoft.com/library/ms533486(VS.85).aspx), [utilizando a conclusão automática no HTML](http://msdn.microsoft.com/library/ms533032.aspx), [HTML Sanitização vulnerabilidade](http://technet.microsoft.com/security/bulletin/MS10-071), [Autocomplete., novamente?](http://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Passos** | O atributo de conclusão automática Especifica se um formulário deve ter a conclusão automática ou desativar. Quando autocomplete está ativado, o browser concluir automaticamente os valores com base nos valores que o utilizador ter introduzido antes. Por exemplo, quando um novo nome e a palavra-passe é introduzido num formulário e o formulário é submetido, o browser pede-se a palavra-passe deve ser guardada. Depois disso quando o formulário é apresentado, o nome e a palavra-passe são preenchidos automaticamente ou estão concluídas, como o nome é introduzido. Um atacante com acesso local foi possível obter a palavra-passe de texto não encriptado da cache do browser. Por predefinição conclusão automática está ativada e explicitamente devem ser desativada. |

### <a name="example"></a>Exemplo
```C#
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Certifique-se de que os dados confidenciais apresentados no ecrã de utilizador está oculto

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Os dados confidenciais, tais como palavras-passe, números de cartão de crédito, etc. da SSN devem ser mascarados quando apresentado no ecrã. Isto é para impedir que técnico não autorizados acedam os dados (por exemplo, por cima ombro ficará surfing palavras-passe, visualização de números de SSN de utilizadores do suporte técnico). Certifique-se de que estes elementos de dados não estão visíveis em texto simples e são mascarados adequadamente. Isto tem de ser tratado ao aceitá-los como entrada (por exemplo. tipo de entrada = "password"), bem como apresentar novamente no ecrã (por exemplo, apresentar apenas os 4 últimos dígitos do número de cartão de crédito). |

## <a id="dynamic-users"></a>Máscara de dados dinâmicos implementar para limitar os utilizadores de exposição não privilegiado dados confidenciais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | MsSQL2016 de versão - V12, versão do SQL - SQL |
| **Referências**              | [Máscara de dados dinâmicos](https://msdn.microsoft.com/library/mt130841) |
| **Passos** | É o objetivo da máscara de dados dinâmica limitar a exposição de dados confidenciais, impedindo que os utilizadores que não devem ter acesso aos dados da visualizá-lo. Máscara de dados dinâmicos não têm como objetivo impedir que os utilizadores de base de dados de ligar diretamente à base de dados e executar consultas exaustivas que expõem partes dos dados confidenciais. A máscara de dados dinâmico é complementares para outras funcionalidades de segurança do SQL Server (auditoria, encriptação e linha segurança ao nível da …) e recomenda vivamente para utilizar esta funcionalidade em conjunto com os mesmos além para ajudar a proteger os dados confidenciais do base de dados. Tenha em atenção que esta funcionalidade só é suportada por SQL Server a partir de 2016 e SQL Database do Azure. |

## <a id="salted-hash"></a>Certifique-se de que as palavras-passe são armazenadas no formato de salted hash

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Hashing de palavra-passe com APIs de criptografia do .NET](http://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Passos** | As palavras-passe não devem ser armazenadas em bases de dados de arquivo de utilizador personalizadas. Os hashes de palavra-passe devem ser armazenados em vez disso, com valores de salt. Certifique-se o salt para o utilizador é sempre único e aplicar b-crypt, s-crypt ou PBKDF2 antes da palavra-passe, com uma contagem de iteração de fator de trabalho mínimo de 150.000 ciclos para eliminar a possibilidade de forçar a força o armazenamento.| 

## <a id="db-encrypted"></a>Certifique-se de que os dados confidenciais nas colunas de base de dados são encriptados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Versão do SQL Server - todos os |
| **Referências**              | [Encriptar dados confidenciais no SQL server](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [como: encriptar uma coluna de dados no SQL Server](https://msdn.microsoft.com/library/ms179331), [encriptar pelo certificado](https://msdn.microsoft.com/library/ms188061) |
| **Passos** | Os dados confidenciais, tais como números de cartão de crédito tem de ser encriptados na base de dados. Dados podem ser encriptados utilizando a encriptação de nível de coluna ou por uma função de aplicação utilizando as funções de encriptação. |

## <a id="tde-enabled"></a>Certifique-se de que a encriptação ao nível da base de dados (TDE) está ativada

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Noções sobre o SQL Server a encriptação de dados transparente (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Passos** | Funcionalidade de encriptação de dados (TDE) transparente no SQL server ajuda a encriptação de dados confidenciais numa base de dados e proteger as chaves que são utilizadas para encriptar os dados com um certificado. Isto impede que qualquer pessoa sem as chaves a utilizar os dados. TDE protege os dados de "Inativos", que significa que os ficheiros de dados e de registo. Fornece a capacidade para estar em conformidade com muitos leis, normas e diretrizes estabelecidas no indústrias vários. |

## <a id="backup"></a>Certifique-se de que as cópias de segurança da base de dados são encriptadas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | MsSQL2014 de versão - V12, versão do SQL - SQL |
| **Referências**              | [Encriptação de cópias de segurança de base de dados do SQL Server](https://msdn.microsoft.com/library/dn449489) |
| **Passos** | SQL Server tem a capacidade para encriptar os dados ao criar uma cópia de segurança. Ao especificar o algoritmo de encriptação e o encriptador (um certificado ou chave assimétrica) ao criar uma cópia de segurança, um pode criar um ficheiro de cópia de segurança encriptado. |

## <a id="api-browser"></a>Certifique-se de que os dados confidenciais relevantes à Web API não são armazenados no armazenamento do browser

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 5, 6 DE MVC |
| **Atributos**              | Fornecedor de identidade do fornecedor - ADFS, identidade - do Azure AD |
| **Referências**              | N/D  |
| **Passos** | <p>Em determinadas implementações, artefactos confidenciais relevantes para a autenticação da API Web são armazenados no armazenamento local do browser. Por exemplo, artefactos de autenticação do Azure AD, como adal.idtoken adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key etc.</p><p>Todas estas artefactos estão disponíveis, mesmo após terminar sessão ou browser é fechado. Se um adversário obtém acesso destes artefactos, he/she pode reutilizar-lhes aceder os recursos protegidos (APIs). Certifique-se de que todos os artefactos confidenciais relacionadas com a Web API não está armazenada no armazenamento do browser. Em casos onde o armazenamento do lado do cliente é obrigatório (por exemplo, única página aplicações (SPA) que tiram partido implícita OpenIdConnect/OAuth fluxos necessário para armazenar localmente os tokens de acesso), utilize as opções de armazenamento com não dispõe de persistência. Por exemplo, prefere SessionStorage LocalStorage.</p>| 

### <a name="example"></a>Exemplo
O abaixo JavaScript fragmento é de uma biblioteca de autenticação personalizado que armazena artefactos de autenticação no local de armazenamento. Estas implementações devem ser evitadas. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a id="encrypt-docdb"></a>Encriptar dados confidenciais armazenados na base de dados do Cosmos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de dados de documento do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Encriptar dados sensíveis ao nível da aplicação antes de o armazenamento na base de dados de documento ou armazenar dados confidenciais em outras soluções de armazenamento como o Storage do Azure ou SQL do Azure| 

## <a id="disk-vm"></a>Utilizar o Azure Disk Encryption para encriptar os discos utilizados por máquinas virtuais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de VM do IaaS do Azure | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Utilizar o Azure Disk Encryption para encriptar os discos utilizados pela suas máquinas virtuais](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Passos** | <p>Encriptação de disco do Azure é uma funcionalidade que está atualmente em pré-visualização. Esta funcionalidade permite-lhe encriptar os discos de SO e discos de dados utilizados pela máquina Virtual IaaS. Para o Windows, as unidades estão encriptadas com tecnologia de encriptação do BitLocker de norma da indústria. Para Linux, os discos estão encriptados com a tecnologia de DM-Crypt. Isto é integrado com o Cofre de chaves do Azure para permitem controlar e gerir as chaves de encriptação de disco. A solução do Azure Disk Encryption suporta os seguintes cenários de encriptação de três cliente:</p><ul><li>Ative a encriptação em novas VMs de IaaS criados a partir de ficheiros do VHD encriptados de cliente e as chaves de encriptação fornecida de cliente, que são armazenadas no Cofre de chaves do Azure.</li><li>Ative a encriptação em novas VMs de IaaS criada no Azure Marketplace.</li><li>Ative a encriptação em VMs de IaaS existentes já em execução no Azure.</li></ul>| 

## <a id="fabric-apps"></a>Encriptar os segredos em aplicações de Service Fabric

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de recursos de infraestrutura de serviço | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Gerir os segredos em aplicações de Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Passos** | Os segredos podem ser qualquer informações confidenciais, tais como cadeias de ligação de armazenamento, as palavras-passe ou outros valores que não devem ser processados em texto simples. Utilize o Cofre de chaves do Azure para gerir as chaves e segredos em aplicações de recursos de infraestrutura de serviço. |

## <a id="modeling-teams"></a>Efetuar modelação de segurança e utilizar unidades de negócio/equipas quando necessário

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Efetuar modelação de segurança e utilizar unidades de negócio/equipas quando necessário |

## <a id="entities"></a>Minimizar o acesso à partilha funcionalidade entidades crítico

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Minimizar o acesso à partilha funcionalidade entidades crítico |

## <a id="good-practices"></a>Preparar os utilizadores sobre os riscos associados a funcionalidade de partilha do Dynamics CRM e práticas de segurança boa

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Preparar os utilizadores sobre os riscos associados a funcionalidade de partilha do Dynamics CRM e práticas de segurança boa |

## <a id="exception-mgmt"></a>Incluir uma regra de padrões de desenvolvimento proscribing que mostra detalhes de configuração na gestão de exceções

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Inclua uma regra de padrões de desenvolvimento proscribing que mostra detalhes de configuração na gestão de exceções fora de desenvolvimento. Testar este como parte do código revisões ou inspeção periódica.|

## <a id="sse-preview"></a>Utilizar a encriptação do serviço de armazenamento do Azure (SSE) de dados Inativos (pré-visualização)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | StorageType - BLOBs |
| **Referências**              | [Encriptação do serviço de armazenamento do Azure para dados Inativos (pré-visualização)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Passos** | <p>Azure armazenamento serviço encriptação (SSE) para dados Inativos ajuda a proteger e a salvaguardar os seus dados para satisfazer os seus compromissos de conformidade e segurança organizacional. Com esta funcionalidade, o Storage do Azure automaticamente encripta os dados antes de a persistência para armazenamento e desencripta antes da obtenção. A encriptação, desencriptação e gestão de chaves é completamente transparente para os utilizadores. SSE aplica-se apenas aos blobs, os blobs de páginas de blocos e blobs de acréscimo. Outros tipos de dados, incluindo tabelas, filas e ficheiros, não serão encriptados.</p><p>Encriptação e desencriptação fluxo de trabalho:</p><ul><li>O cliente ativa a encriptação na conta de armazenamento</li><li>Quando o cliente escreve novos dados (colocar Blob, colocar bloco, colocar página, etc.) para o Blob storage; cada escrita é encriptada utilizando a encriptação de AES 256 bits, uma das cifras de bloco maior disponíveis</li><li>Quando o cliente tem de aceder a dados (obter BLOBs, etc.), os dados são desencriptados automaticamente antes de o devolver ao utilizador</li><li>Se a encriptação estiver desativada, escritas novas já não são encriptadas e os dados encriptados existentes permanecem encriptados até foi reescrita pelo utilizador. Enquanto a encriptação estiver ativada, serão encriptadas escritas para o Blob storage. O estado de dados não se altera com o utilizador ativando ou desativando entre ativação/desativação da encriptação para a conta de armazenamento</li><li>Todas as chaves de encriptação são armazenadas, encriptadas e geridas pela Microsoft</li></ul><p>Tenha em atenção que, neste momento, as chaves utilizadas para a encriptação são geridas pela Microsoft. A Microsoft gera originalmente as chaves e gerir o armazenamento seguro das chaves, bem como a rotação regular, tal como definido pela política interna do Microsoft. No futuro, os clientes irão receber a capacidade de gerir os seus próprios > chaves de encriptação e forneça um caminho de migração a partir das chaves gerida pela Microsoft para chaves gerida pelo cliente.</p>| 

## <a id="client-storage"></a>Utilizar encriptação do lado do cliente para armazenar dados confidenciais no armazenamento do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Encriptação do lado do cliente e o Cofre de chaves do Azure para armazenamento do Microsoft Azure](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [Tutorial: encriptar e desencriptar blobs no armazenamento do Microsoft Azure com o Cofre de chaves do Azure](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [armazenar dados em forma segura no Blob do Azure Armazenamento com extensões de encriptação do Azure](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Passos** | <p>A biblioteca de clientes de armazenamento do Azure para o pacote Nuget do .NET suporta a encriptação de dados dentro de aplicações de cliente antes do carregamento para o Storage do Azure e a desencriptação de dados durante a transferência para o cliente. A biblioteca também suporta a integração com o Cofre de Chaves do Azure para a gestão de chaves da conta do Storage. Eis uma breve descrição como funciona a encriptação do lado do cliente:</p><ul><li>O SDK do cliente do Storage do Azure gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica one time utilização</li><li>Dados de cliente são encriptados utilizando este CEK</li><li>O CEK, em seguida, é moldado (encriptado) utilizando a chave de encriptação de chaves (KEK). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerida localmente ou armazenada no Cofre de chaves do Azure. O cliente do armazenamento nunca tem acesso para a KEK. Apenas invoca o algoritmo de chave de encapsulamento de aplicações fornecido pelo Cofre de chaves. Os clientes podem optar por utilizar fornecedores personalizados para a chave de encapsulamento de aplicações/abertura se pretenderem</li><li>Os dados encriptados, em seguida, são carregados para o serviço de armazenamento do Azure. Verifique as ligações na secção de referências para detalhes de implementação de baixo nível.</li></ul>|

## <a id="pii-phones"></a>Encriptar confidenciais ou dados PII escritos para o armazenamento local de telemóveis

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente para dispositivos móveis | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, Xamarin  |
| **Atributos**              | N/D  |
| **Referências**              | [Gerir definições e funcionalidades nos seus dispositivos com políticas do Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies#create-a-configuration-policy), [Valet de Keychain](https://components.xamarin.com/view/square.valet) |
| **Passos** | <p>Se a aplicação escreve informações confidenciais, como PII do utilizador (e-mail, número de telefone, nome próprio, apelido, preferências etc.) -no sistema de ficheiros do mobile, em seguida, deve ser encriptado antes de escrever no sistema de ficheiros local. Se a aplicação é uma aplicação empresarial, em seguida, explore a possibilidade de aplicação publicação utilizando o Windows Intune.</p>|

### <a name="example"></a>Exemplo
Intune pode ser configurado com as políticas de segurança seguintes para salvaguardar os dados confidenciais: 
```C#
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Exemplo
Se a aplicação não é uma aplicação empresarial, em seguida, utilizar plataforma fornecida keystore, pode ser efetuada keychains para armazenar chaves de encriptação, utilizando a operação criptográfica no sistema de ficheiros. O fragmento de código seguinte mostra como a chave de acesso de keychain utilizando xamarin: 
```C#
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a id="binaries-end"></a>Obfuscate binários gerados antes de distribuir aos utilizadores finais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente para dispositivos móveis | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Criptografia Obfuscation para .net](http://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Passos** | Binários gerados (assemblagens dentro apk) devem estar ocultados para parar a engenharia inversa, de assemblagens. Ferramentas como `CryptoObfuscator` pode ser utilizado para esta finalidade. |

## <a id="cert"></a>Definir o clientCredentialType certificado ou o Windows

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [Fortify](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passos** | Utilizando um UsernameToken com uma palavra-passe de texto simples através de um canal não encriptado expõe a palavra-passe para os atacantes que pode sniff as mensagens SOAP. Fornecedores de serviços que utilizam o UsernameToken pode aceitar as palavras-passe enviadas em texto não encriptado. Enviar palavras-passe de texto simples através de um canal não encriptado, pode expor as credenciais para os atacantes que pode sniff a mensagem SOAP. | 

### <a name="example"></a>Exemplo
A seguinte configuração de fornecedor de serviço WCF utiliza o UsernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Definir o clientCredentialType certificado ou o Windows. 

## <a id="security"></a>Modo de segurança de WCF não está ativado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, .NET Framework 3 |
| **Atributos**              | Mensagem de modo - transporte, modo de segurança - de segurança |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Unido](https://vulncat.fortify.com/en/vulncat/index.html), [Noções básicas de segurança WCF CoDe Magazine](http://www.codemag.com/article/0611051) |
| **Passos** | Não foi definida nenhuma segurança transport ou da mensagem. Aplicações que transmitir mensagens sem transporte ou mensagem de segurança não é possível garantir a integridade ou confidencialidade das mensagens. Quando um enlace de segurança WCF está definido como None, segurança de transporte e a mensagem estão desativadas. |

### <a name="example"></a>Exemplo
A seguinte configuração define o modo de segurança como None. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Exemplo
Modo de segurança em todos os enlaces de serviço existe são cinco modos de segurança possíveis: 
* nenhum. Desativa a segurança. 
* Transporte. Utilizações de transporte de segurança para proteção de mensagem e autenticação mútua. 
* Mensagem. Utiliza a segurança de mensagens para a proteção de mensagem e autenticação mútua. 
* Ambos. Permite-lhe fornecer definições para o transporte e a segurança de nível de mensagem (MSMQ só suporta este). 
* TransportWithMessageCredential. As credenciais são transmitidas com a mensagem e a proteção de mensagem e autenticação de servidor são fornecidos pela camada de transporte. 
* TransportCredentialOnly. Credenciais do cliente são transmitidas com a camada de transporte e nenhuma mensagem de proteção é aplicada. Utilize a segurança de transporte e a mensagem para proteger a integridade e confidencialidade de mensagens em fila. A configuração abaixo indica que o serviço para utilizar a segurança de transporte com credenciais de mensagem.
```
<system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
</system.serviceModel> 
```
