---
title: "Serviços em nuvem e certificados de gestão | Microsoft Docs"
description: Saiba como criar e utilizar certificados com o Microsoft Azure
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: fc70d00d-899b-4771-855f-44574dc4bfc6
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 689977491e6df37e48536f59234bf4ddba6a1575
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Descrição geral de certificados para serviços de nuvem do Azure
Certificados são utilizados no Azure para serviços em nuvem ([certificados de serviço](#what-are-service-certificates)) e para a autenticação com a API de gestão ([certificados de gestão](#what-are-management-certificates) quando utilizar o portal clássico do Azure e não a portal do Azure não clássica). Este tópico fornece uma descrição geral de ambos os tipos de certificado, como a [criar](#create) e [implementar](#deploy) -las para o Azure.

Certificados utilizados no Azure são x. 509 v3 certificados e podem ser assinados por outro certificado fidedigno ou podem ser auto-assinados. Um certificado autoassinado está assinado pela sua própria criador, por conseguinte, não é considerado fidedigno por predefinição. A maioria dos browsers podem ignorar este problema. Só deve utilizar certificados autoassinados quando desenvolver e testar os seus serviços em nuvem. 

Certificados utilizados pelo Azure podem conter um private ou uma chave pública. Certificados têm um thumbprint que fornece um meio para identificá-las de uma forma inequívoca. Esta impressão digital é utilizada do Azure [ficheiro de configuração](cloud-services-configure-ssl-certificate.md) para identificar o certificado que um serviço em nuvem deverá utilizar. 

## <a name="what-are-service-certificates"></a>Quais são os certificados de serviço?
Certificados de serviço estão ligados ao serviços em nuvem e ativar a comunicação segura de e para o serviço. Por exemplo, se tiver implementado uma função da web, seria aconselhável fornecer um certificado que pode autenticar-se de um ponto final de HTTPS exposto. Certificados de serviço, definidos na definição do serviço, são implementados automaticamente para a máquina virtual que está a executar uma instância da sua função. 

Pode carregar certificados de serviço para o portal clássico do Azure utilizando o portal clássico do Azure ou utilizando o modelo de implementação clássica. Certificados de serviço estão associados um serviço em nuvem específica. São atribuídas a uma implementação no ficheiro de definição de serviço.

Certificados de serviço podem ser geridos separadamente em relação os serviços e podem ser geridos pelo diferentes pessoas. Por exemplo, um programador pode carregar um pacote de serviço que se refere a um certificado que um Gestor de TI foi carregada anteriormente para o Azure. Um Gestor de TI pode gerir e renovar o certificado (alteração da configuração do serviço) sem ser necessário carregar um pacote de serviço novo. É possível atualizar sem um novo pacote de serviço porque o nome lógico, o nome do arquivo e a localização do certificado está no ficheiro de definição de serviço e enquanto o thumbprint do certificado é especificado no ficheiro de configuração do serviço. Para atualizar o certificado, só é necessário carregar um certificado novo e altere o valor de thumbprint no ficheiro de configuração do serviço.

>[!Note]
>O [FAQ de serviços em nuvem](cloud-services-faq.md) artigo tem algumas informações úteis sobre certificados.

## <a name="what-are-management-certificates"></a>Quais são os certificados de gestão?
Certificados de gestão permitem-lhe autenticar com o modelo de implementação clássica. Muitos programas e ferramentas (tais como o Visual Studio ou o SDK do Azure) utilizam estes certificados para automatizar a configuração e implementação de vários serviços do Azure. Estes não estão realmente relacionados com serviços em nuvem. 

> [!WARNING]
> Tenha cuidado! Estes tipos de certificados permite que qualquer pessoa que efetua a autenticação com os mesmos para gerir a subscrição que se encontram associados. 
> 
> 

### <a name="limitations"></a>Limitações
Não há um limite de 100 certificados de gestão por subscrição. Há também um limite de 100 certificados de gestão para todas as subscrições no ID de utilizador de. um administrador de serviço específicos Se o ID de utilizador para o administrador de conta já foi utilizado para adicionar 100 certificados de gestão e for necessário mais certificados, pode adicionar um coadministrador para adicionar os certificados adicionais. 

Antes de adicionar mais do que 100 certificados, consulte o artigo se pode reutilizar um certificado existente. Utilizar os coadministradores adiciona complexidade potencialmente desnecessárias consoante o processo de gestão de certificados.

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Criar um novo certificado autoassinado
Pode utilizar qualquer ferramenta disponível para criar um certificado autoassinado, desde adira a estas definições:

* Um certificado x. 509.
* Contém uma chave privada.
* Criar para a troca de chaves (ficheiro. pfx).
* Nome do requerente tem de corresponder ao domínio utilizado para aceder ao serviço de nuvem.

    > Não é possível adquirir um certificado SSL para o cloudapp.net (ou para qualquer relacionadas com o Azure) domínio; nome do requerente do certificado tem de corresponder ao nome de domínio personalizado utilizado para aceder à sua aplicação. Por exemplo, **contoso.net**, não **contoso.cloudapp.net**.

* Mínimo de encriptação de 2048 bits.
* **Certificado de serviço só**: certificado do lado do cliente tem de residir no *pessoais* arquivo de certificados.

Existem duas formas de fácil de criar um certificado no Windows, com o `makecert.exe` utilitário nem o IIS.

### <a name="makecertexe"></a>MakeCert.exe
Este utilitário foi preterido e já não é documentado aqui. Para obter mais informações, consulte [neste artigo do MSDN](https://msdn.microsoft.com/library/windows/desktop/aa386968).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Se pretender utilizar o certificado com um endereço IP, em vez de um domínio, utilize o endereço IP no parâmetro - DnsName.


Se pretender utilizar esta opção [certificado com o portal de gestão](../azure-api-management-certs.md), exportá-lo para um **. cer** ficheiro:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Serviços de informação Internet (IIS)
Existem várias páginas na internet que abrangem como fazê-lo com o IIS. [Aqui](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) é uma excelente encontrei julgo explica-la corretamente. 

### <a name="linux"></a>Linux
[Isto](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artigo descreve como criar certificados com SSH.

## <a name="next-steps"></a>Passos seguintes
[Carregue o certificado de serviço para o portal clássico do Azure](cloud-services-configure-ssl-certificate.md) (ou o [portal do Azure](cloud-services-configure-ssl-certificate-portal.md)).

Carregar um [certificado da API de gestão](../azure-api-management-certs.md) no portal clássico do Azure. O portal do Azure utiliza certificados de gestão para a autenticação.

