---
title: "Como Configurar a Autenticação Mútua de TLS para a Aplicação Web"
description: "Saiba como configurar a sua aplicação web para utilizar a autenticação de certificado de cliente no TLS."
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: naziml
ms.openlocfilehash: db69852cffd1ff331ac4a640b04ea4360d00bf75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Como Configurar a Autenticação Mútua de TLS para a Aplicação Web
## <a name="overview"></a>Descrição geral
Pode restringir o acesso à sua aplicação web do Azure, permitindo a diferentes tipos de autenticação para o mesmo. É uma forma de fazê-lo a autenticação utilizando um certificado de cliente quando o pedido é efetuada através de TLS/SSL. Este mecanismo denomina-se a autenticação mútua TLS ou certificado de cliente de autenticação e este artigo pormenor como configurar a sua aplicação web para utilizar a autenticação de certificado de cliente.

> **Nota:** se aceder ao site por HTTP e HTTPS não, não irá receber qualquer certificado de cliente. Por isso, se a sua aplicação precisar de certificados de cliente, deve não permitir pedidos a sua aplicação através de HTTP.
> 
> 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="configure-web-app-for-client-certificate-authentication"></a>Configurar aplicação Web para autenticação de certificados de cliente
Para configurar a sua aplicação web para exigir certificados de cliente tem de adicionar a definição do site clientCertEnabled para a sua aplicação web e defina-o como true. Esta definição não está atualmente disponível através da experiência de gestão no Portal e a API REST, terá de ser utilizado para realizar esta tarefa.

Pode utilizar o [ARMClient ferramenta](https://github.com/projectkudu/ARMClient) para facilitar a craft a chamada de REST API. Depois de iniciar sessão com a ferramenta, terá de emita o comando seguinte:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose

Substituir tudo na {} com informações da sua aplicação web e criar um ficheiro denominado enableclientcert.json com o seguinte JSON conteúdo:

    {
        "location": "My Web App Location",
        "properties": {
            "clientCertEnabled": true
        }
    }

Certifique-se de que altere o valor de "localização" para onde quer que está localizada a sua aplicação web por exemplo, Norte Central nos ou oeste E.U.A. etc.

Também pode utilizar https://resources.azure.com para inverter a `clientCertEnabled` propriedade `true`.

> **Nota:** se executar ARMClient a partir do Powershell, terá de terminar o símbolo para o ficheiro JSON com uma escala de back-@ '.
> 
> 

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Aceder ao certificado de cliente da sua aplicação Web
Se estiver a utilizar o ASP.NET e configurar a sua aplicação para utilizar a autenticação de certificado de cliente, o certificado estará disponível através de **HttpRequest.ClientCertificate** propriedade. Para outras pilhas de aplicação, o certificado de cliente estarão disponível na sua aplicação através de um valor de codificado em base64 no cabeçalho do pedido de "X-ARR-ClientCert". A aplicação pode criar um certificado a partir deste valor e, em seguida, utilizá-lo para efeitos de autenticação e autorização na sua aplicação.

## <a name="special-considerations-for-certificate-validation"></a>Considerações especiais sobre a validação de certificado
O certificado de cliente que é enviado para a aplicação não seguir qualquer validação pela plataforma do Web Apps do Azure. Validar este certificado é da responsabilidade da aplicação web. Eis o código de ASP.NET de exemplo que valida as propriedades do certificado para efeitos de autenticação.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
