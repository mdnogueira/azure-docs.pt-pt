---
title: "Comunicação segurança - ferramenta de modelação de ameaça Microsoft - Azure | Microsoft Docs"
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
ms.openlocfilehash: 68bf128824a40afb25b3e088965f38a4cb4d1332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-communication-security--mitigations"></a>Moldura de segurança: Segurança de comunicação | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Hub de eventos do Azure** | <ul><li>[Comunicação segura para o Hub de eventos utilizando SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Verifique os privilégios de conta de serviço e verifique se as páginas do ASP.NET ou serviços personalizada Respeitamos a segurança do CRM](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Utilizar o Data management gateway durante a ligação de SQL Server no local ao Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Servidor de identidades** | <ul><li>[Certifique-se de que todo o tráfego para o servidor de identidades através de ligação HTTPS](#identity-https)</li></ul> |
| **Aplicação Web** | <ul><li>[Certifique-se de x. 509 certificados utilizados para autenticar as ligações de SSL, TLS e DTLS](#x509-ssltls)</li><li>[Configurar um certificado SSL para o domínio personalizado no App Service do Azure](#ssl-appservice)</li><li>[Forçar todo o tráfego para o App Service do Azure através de ligação HTTPS](#appservice-https)</li><li>[Ativar segurança de transporte Strict HTTP (HSTS)](#http-hsts)</li></ul> |
| **Base de Dados** | <ul><li>[Certifique-se o SQL server certificado de encriptação e validação da ligação](#sqlserver-validation)</li><li>[Forçar a comunicação encriptada ao SQL server](#encrypted-sqlserver)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Certifique-se de que a comunicação ao Storage do Azure através de HTTPS](#comm-storage)</li><li>[Validar o MD5 hash depois de transferir BLOBs se HTTPS não pode ser ativado](#md5-https)</li><li>[Utilize o cliente compatível de SMB 3.0 para garantir a encriptação de dados em trânsito para partilhas de ficheiros do Azure](#smb-shares)</li></ul> |
| **Cliente para dispositivos móveis** | <ul><li>[Implementar a afixação de certificado](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Ativar HTTPS - Proteja o canal de transporte](#https-transport)</li><li>[WCF: Segurança de mensagens de conjunto nível de proteção como EncryptAndSign](#message-protection)</li><li>[WCF: Utilizar uma conta de menor privilégio para executar o serviço WCF](#least-account-wcf)</li></ul> |
| **API Web** | <ul><li>[Forçar todo o tráfego a APIs da Web através de ligação HTTPS](#webapi-https)</li></ul> |
| **Cache de Redis do Azure** | <ul><li>[Certifique-se de que a comunicação para a Cache de Redis do Azure através de SSL](#redis-ssl)</li></ul> |
| **Gateway de campo de IoT** | <ul><li>[Proteger o dispositivo para a comunicação de Gateway de campo](#device-field)</li></ul> |
| **Gateway de nuvem do IoT** | <ul><li>[Proteger o dispositivo para a comunicação de Gateway de nuvem, utilizando SSL/TLS](#device-cloud)</li></ul> |

## <a id="comm-ssltls"></a>Comunicação segura para o Hub de eventos utilizando SSL/TLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de eventos do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Event Hubs autenticação e segurança descrição geral do modelo](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Proteger as ligações AMQP ou HTTP para o Hub de eventos utilizando SSL/TLS |

## <a id="priv-aspnet"></a>Verifique os privilégios de conta de serviço e verifique se as páginas do ASP.NET ou serviços personalizada Respeitamos a segurança do CRM

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Verifique os privilégios de conta de serviço e verifique se as páginas do ASP.NET ou serviços personalizada Respeitamos a segurança do CRM |

## <a id="sqlserver-factory"></a>Utilizar o Data management gateway durante a ligação de SQL Server no local ao Azure Data Factory

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure Data Factory | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Tipos de serviço ligado - do Azure e no local |
| **Referências**              |[Mover dados entre no local e o Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [o Data management gateway](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Passos** | <p>A ferramenta de Gateway de gestão de dados (DMG) é necessária para ligar às origens de dados que estão protegidas por trás da rede empresarial ou uma firewall.</p><ol><li>Bloquear a máquina isola a ferramenta DMG e impede programas malfunctioning prejudiciais ou monitorização na máquina de origem de dados. (Por exemplo as atualizações mais recentes tem de estar instaladas, ativar mínimo necessário portas, controladas contas de aprovisionamento, a auditoria ativada, encriptação de disco ativada etc.)</li><li>Chave do Gateway de dados tem rotação intervalos frequentes ou sempre que a palavra-passe da conta do serviço DMG renova</li><li>Tem de estar encriptados transits dados através da ligação de serviço</li></ol> |

## <a id="identity-https"></a>Certifique-se de que todo o tráfego para o servidor de identidades através de ligação HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidades | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [IdentityServer3 - e criptografia de chaves, assinaturas](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 - implementação](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Passos** | Por predefinição, IdentityServer requer que todas as ligações recebidas fique através de HTTPS. É absolutamente obrigatório que a comunicação com IdentityServer é efetuada através de apenas transportes protegidos. Existem determinados cenários de implementação, como a descarga de SSL, onde pode ser flexibilizado este requisito. Consulte a página de implementação do servidor de identidades em referências para obter mais informações. |

## <a id="x509-ssltls"></a>Certifique-se de x. 509 certificados utilizados para autenticar as ligações de SSL, TLS e DTLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>As aplicações que utilizam o SSL, TLS ou DTLS totalmente tem de verificar os certificados x. 509 das entidades que se ligam ao. Isto inclui a verificação dos certificados para:</p><ul><li>Nome de domínio</li><li>Datas de validade (datas de início e de expiração)</li><li>Estado de revogação</li><li>Utilização (por exemplo, autenticação de servidor para servidores, autenticação de cliente para clientes)</li><li>Cadeia de fidedignidade. Certificados devem estar encadeados com uma autoridade de certificação de raiz (AC) considerada fidedigna pela plataforma do ou explicitamente configurada pelo administrador</li><li>Comprimento de chave de chave pública do certificado tem de ser > 2048 bits</li><li>Algoritmo hash tem de ser SHA256 e acima |

## <a id="ssl-appservice"></a>Configurar um certificado SSL para o domínio personalizado no App Service do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | EnvironmentType - Azure |
| **Referências**              | [Ativar HTTPS para uma aplicação no App Service do Azure](../app-service/app-service-web-tutorial-custom-ssl.md) |
| **Passos** | Por predefinição, o Azure já permite HTTPS para todas as aplicações com um certificado de caráter universal para o *. domínio azurewebsites.net. No entanto, como todos os domínios de caráter universal, não é como segura como utilizar um domínio personalizado com certificado próprio [consulte](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). É recomendado ativar SSL para o domínio personalizado que a aplicação implementada irá ser acedida através do|

## <a id="appservice-https"></a>Forçar todo o tráfego para o App Service do Azure através de ligação HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | EnvironmentType - Azure |
| **Referências**              | [Impor HTTPS no App Service do Azure](../app-service/app-service-web-tutorial-custom-ssl.md#enforce-https) |
| **Passos** | <p>Embora o Azure permite já HTTPS para os serviços de aplicações do Azure com um certificado de caráter universal para o domínio *. azurewebsites.net, não a impor o HTTPS. Visitantes ainda podem aceder à aplicação a utilizar HTTP, que pode comprometer a segurança da aplicação e, por conseguinte, HTTPS tem de ser imposta explicitamente. Aplicações de ASP.NET MVC devem utilizar o [RequireHttps filtro](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) que força um pedido HTTP não segura novamente sejam enviados através de HTTPS.</p><p>Em alternativa, o URL Rewrite module, que está incluído no App Service do Azure pode ser utilizado para impor o HTTPS. Módulo URL Rewrite permite aos programadores definir regras que são aplicadas aos pedidos recebidos antes dos pedidos são entregar à sua aplicação. Estiverem definidas regras de URL Rewrite num ficheiro Web. config armazenado na raiz da aplicação</p>|

### <a name="example"></a>Exemplo
O exemplo seguinte contém uma regra de URL Rewrite básica que força a todo o tráfego de entrada para utilizar HTTPS
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Esta regra funciona ao devolver um código de estado HTTP de 301 (redirecionar permanente) quando o utilizador solicita uma página utilizando HTTP. O 301 redireciona o pedido para o mesmo URL que o visitante solicitado, mas substitui a porção HTTP do pedido por HTTPS. Por exemplo, HTTP://contoso.com poderia ser redirecionado para HTTPS://contoso.com. 

## <a id="http-hsts"></a>Ativar segurança de transporte Strict HTTP (HSTS)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Segurança de transporte Strict OWASP HTTP Cheat folha](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Passos** | <p>Segurança de transporte Strict da HTTP (HSTS) é uma melhoria de opção de segurança que é especificada por uma aplicação web através da utilização de um cabeçalho de resposta especiais. Depois de um browser suportado recebe este cabeçalho esse browser irá impedir que as comunicações de que está a ser enviados através de HTTP para o domínio especificado e enviará em vez disso, todas as comunicações através de HTTPS. Também impede que clique HTTPS através de pedidos em browsers.</p><p>Para implementar HSTS, o seguinte cabeçalho de resposta tem de ser configurada para um Web site globalmente, no código ou na configuração. Transporte-segurança de Strict: idade máxima = 300; includeSubDomains HSTS aborda as ameaças seguintes:</p><ul><li>Utilizador marcadores ou manualmente tipos http://example.com e está sujeita a um atacante ataques man-in-the-middle: HSTS redireciona automaticamente pedidos HTTP para HTTPS para o domínio de destino</li><li>Aplicação Web que se destina a ser puramente HTTPS inadvertidamente contém ligações HTTP ou serve conteúdo através de HTTP: HSTS redireciona automaticamente pedidos HTTP para HTTPS para o domínio de destino</li><li>Um atacante ataques man-in-the-middle tentativas de tráfego de um utilizador de vítima utilizando um certificado inválido de intercetar e hopes o utilizador irá aceitar o certificado incorreto: HSTS não permitir que um utilizador substituir a mensagem de certificado inválido</li></ul>|

## <a id="sqlserver-validation"></a>Certifique-se o SQL server certificado de encriptação e validação da ligação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure  |
| **Atributos**              | Versão do SQL Server - V12 |
| **Referências**              | [Melhores práticas na escrita proteger cadeias de ligação para base de dados SQL](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Passos** | <p>Todas as comunicações entre a base de dados SQL e uma aplicação de cliente são encriptadas utilizando Secure Sockets Layer (SSL) permanente. Base de dados do SQL Server não suporta ligações não encriptadas. Para validar o certificado com o código da aplicação ou ferramentas, explicitamente pedir uma ligação encriptada e não confiar nos certificados de servidor. Se o código da aplicação ou ferramentas pediu uma ligação encriptada, ainda receberão ligações encriptadas</p><p>No entanto, não pode validar os certificados de servidor e, por conseguinte, irá ser susceptível a ataques "man no meio". Para validar o certificado com o código de aplicação do ADO.NET, defina `Encrypt=True` e `TrustServerCertificate=False` na cadeia de ligação de base de dados. Para validar os certificados através do SQL Server Management Studio, abra o ligar a caixa de diálogo de servidor. Clique em encriptar a ligação no separador de propriedades de ligação</p>|

## <a id="encrypted-sqlserver"></a>Forçar a comunicação encriptada ao SQL server

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | OnPrem |
| **Atributos**              | SQL Server versão - MsSQL2016, SQL Server versão - MsSQL2012, versão do SQL - MsSQL2014 |
| **Referências**              | [Ativar ligações encriptadas para o motor de base de dados](https://msdn.microsoft.com/library/ms191192)  |
| **Passos** | Ativar SSL encriptação aumenta a segurança dos dados transmitidos através de redes entre instâncias do SQL Server e aplicações. |

## <a id="comm-storage"></a>Certifique-se de que a comunicação ao Storage do Azure através de HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Encriptação de nível de transporte do Storage do Azure – através de HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Passos** | Para garantir a segurança de armazenamento do Azure dados em do trânsito, utilize sempre o protocolo HTTPS quando chamar as APIs REST ou aceder aos objetos no armazenamento. Além disso, assinaturas de acesso partilhado, que pode ser utilizado para delegar o acesso a objetos de armazenamento do Azure, incluir uma opção para especificar que apenas o protocolo HTTPS pode ser utilizado quando utilizar assinaturas de acesso partilhado, garantindo que qualquer pessoa enviar ligações com SAS tokens irá utilizar o protocolo correto.|

## <a id="md5-https"></a>Validar o MD5 hash depois de transferir BLOBs se HTTPS não pode ser ativado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | StorageType - BLOBs |
| **Referências**              | [Descrição geral do Windows Azure Blob MD5](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Passos** | <p>Serviço Blob do Windows Azure fornece mecanismos para garantir a integridade dos dados, tanto na aplicação e de camadas de transporte. Se por qualquer motivo, que terá de utilizar HTTP em vez de HTTPS e estiver a trabalhar com blobs de blocos, pode utilizar a verificar o MD5 para ajudar a validar a integridade dos blobs a serem transferidos</p><p>Isto irá ajudar com proteção de erros de camada de transporte/rede, mas não necessariamente com ataques intermediário. Se é possível utilizar HTTPS, que fornece segurança ao nível do transporte, em seguida, a utilização de MD5 a verificação é redundante e desnecessários.</p>|

## <a id="smb-shares"></a>Utilize o cliente compatível de SMB 3.0 para garantir a encriptação de dados em trânsito para partilhas de ficheiros do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente para dispositivos móveis | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | StorageType - ficheiro |
| **Referências**              | [File Storage do Azure](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [suporte SMB de armazenamento de ficheiros do Azure para clientes do Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Passos** | File Storage do Azure suporta HTTPS ao utilizar a API REST, mas é mais frequentemente utilizado como uma partilha de ficheiros SMB ligado a uma VM. SMB 2.1 não suporta a encriptação, pelo que as ligações só são permitidas na mesma região no Azure. No entanto, suporta a encriptação SMB 3.0 e pode ser utilizado com o Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10, permitindo por várias regiões aceder e até mesmo acesso no ambiente de trabalho. |

## <a id="cert-pinning"></a>Implementar a afixação de certificado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, Windows Phone |
| **Atributos**              | N/D  |
| **Referências**              | [Certificado e a afixação de chave pública](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Passos** | <p>Afixação de certificado defends contra ataques Man-In-The-Middle (MITM). Afixação é o processo de associação de um anfitrião com o respetivo X509 esperado certificado ou chave pública. Assim que um certificado ou chave pública é conhecida ou vista para um anfitrião, o certificado ou chave pública é associada ou 'afixada' para o anfitrião. </p><p>Assim, quando um adversário tenta fazer a ataques SSL MITM, durante o handshake SSL a chave do servidor de acesso do atacante é diferente da chave do certificado afixado e o pedido será eliminado, impedindo MITM certificado afixação pode ser conseguido através da implementação do ServicePointManager `ServerCertificateValidationCallback` delegar.</p>|

### <a name="example"></a>Exemplo
```C#
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a id="https-transport"></a>Ativar HTTPS - Proteja o canal de transporte

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Unido](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passos** | A configuração da aplicação deve certificar-se de que o HTTPS é utilizado para todo o acesso a informações confidenciais.<ul><li>**EXPLICAÇÃO:** se uma aplicação processa informações confidenciais e não utiliza encriptação de nível da mensagem, em seguida, só deve ser permitido para comunicar através de um canal de transporte encriptados.</li><li>**RECOMENDAÇÕES:** Certifique-se de que o transporte HTTP está desativada e ativar o transporte HTTPS em vez disso. Por exemplo, substitua o `<httpTransport/>` com `<httpsTransport/>` etiquetas. Não confie numa configuração de rede (firewall) para garantir que a aplicação só pode ser acedida através de um canal seguro. Do ponto de vista philosophical, a aplicação não deve depender na rede para a segurança.</li></ul><p>Do ponto de vista prático, as pessoas responsáveis por proteger a rede não sempre controlam os requisitos de segurança da aplicação como estes evoluem.</p>|

## <a id="message-protection"></a>WCF: Segurança de mensagens de conjunto nível de proteção como EncryptAndSign

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Passos** | <ul><li>**EXPLICAÇÃO:** quando proteção nível está definido como "none" irá desativar a proteção de mensagem. Confidencialidade e integridade é conseguido com o nível adequado de definição.</li><li>**RECOMENDAÇÕES:**<ul><li>Quando `Mode=None` -desativa a proteção de mensagem</li><li>Quando `Mode=Sign` -inicia, mas não encriptar a mensagem; deve ser utilizada se a integridade dos dados é importante</li><li>Quando `Mode=EncryptAndSign` -inicia e encripta a mensagem</li></ul></li></ul><p>Considere desativar a encriptação e assinatura apenas a sua mensagem quando apenas precisam de validar a integridade das informações sem preocupações de confidencialidade. Isto poderá ser útil para operações ou serviço contrai que precisa de validar do remetente original, mas não existem dados confidenciais são transmitidos. Ao reduzir o nível de proteção, tenha o cuidado de que a mensagem não contém qualquer informação identificativa (PII).</p>|

### <a name="example"></a>Exemplo
Configurar o serviço e a operação de apenas assinou a mensagem é apresentada nos exemplos seguintes. Exemplo de contrato de serviço de `ProtectionLevel.Sign`: segue-se um exemplo de utilização ProtectionLevel.Sign ao nível do contrato de serviço: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Exemplo
Exemplo de contrato de operação de `ProtectionLevel.Sign` (para um controlo Granular): O seguinte é um exemplo de utilização `ProtectionLevel.Sign` ao nível do OperationContract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a id="least-account-wcf"></a>WCF: Utilizar uma conta de menor privilégio para executar o serviço WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Passos** | <ul><li>**EXPLICAÇÃO:** não executam serviços do WCF em admin ou conta de privilégio elevado. em caso de comprometimento de serviços irá resultar num elevado impacto.</li><li>**RECOMENDAÇÕES:** utilizar uma conta de menor privilégio para alojar o serviço WCF porque irá reduzir a superfície de ataque da sua aplicação e reduzir os potenciais danos se estão a ser atacado. Se a conta de serviço requer direitos de acesso adicionais nos recursos de infraestrutura, tais como o MSMQ, o registo de eventos, contadores de desempenho e o sistema de ficheiros, as permissões adequadas devem ser atribuídas a estes recursos para que o serviço WCF pode ser executado com êxito.</li></ul><p>Se o serviço tem de aceder a recursos específicos em nome do autor da chamada original, utilize a representação e a delegação para fluir a identidade do emissor de uma verificação de autorização a jusante. Num cenário de desenvolvimento, utilize a conta de serviço de rede local, o que é uma conta incorporada especial que tenha reduzido privilégios. Num cenário de produção, crie uma conta de serviço de domínio personalizado de menor privilégio.</p>|

## <a id="webapi-https"></a>Forçar todo o tráfego a APIs da Web através de ligação HTTPS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Impor o SSL num controlador de API Web](http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Passos** | Se uma aplicação tiver um HTTPS e um enlace de HTTP, os clientes ainda podem utilizar HTTP para aceder ao site. Para evitar esta situação, utilize um filtro de ação para garantir que os pedidos às APIs protegidos são sempre através de HTTPS.|

### <a name="example"></a>Exemplo 
O código seguinte mostra um filtro de autenticação de Web API que verifica a existência de SSL: 
```C#
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Adicione este filtro para as ações de Web API exigem SSL: 
```C#
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a id="redis-ssl"></a>Certifique-se de que a comunicação para a Cache de Redis do Azure através de SSL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cache de Redis do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Suporte de SSL de Redis do Azure](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Passos** | Redis servidor não suporta SSL a Box, mas não de Cache de Redis do Azure. Se estiver a ligar à Cache de Redis do Azure e o cliente suportar SSL, como stackexchange. redis, em seguida, deve utilizar SSL. Por predefinição a porta não SSL está desativada para novas instâncias de Cache de Redis do Azure. Certifique-se de que as predefinições seguras não são alteradas a menos que exista uma dependência no suporte SSL para clientes de redis. |

Tenha em atenção que o Redis foi concebido para ser acedido por clientes fidedignos dentro de ambientes fidedignas. Isto significa que, normalmente, não é uma boa ideia para expor a instância de Redis diretamente à internet ou, em geral, para um ambiente em que os clientes não fidedignos podem aceder diretamente à porta Redis TCP ou UNIX socket. 

## <a id="device-field"></a>Proteger o dispositivo para a comunicação de Gateway de campo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo de IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Para dispositivos IP com base, o protocolo de comunicação, normalmente, pode ser encapsulado num canal SSL/TLS para proteger dados em trânsito. Para outros protocolos que não suportam SSL/TLS investigue se existem versões do protocolo seguras que fornecem segurança na camada de transporte ou da mensagem. |

## <a id="device-cloud"></a>Proteger o dispositivo para a comunicação de Gateway de nuvem, utilizando SSL/TLS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem do IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Escolher o protocolo de comunicação](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Passos** | Proteja HTTP/AMQP ou protocolos MQTT utilizar SSL/TLS. |
