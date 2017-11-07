---
title: Vincular um certificado SSL personalizado existente para Web Apps do Azure | Microsoft Docs
description: "Saiba como vincular um certificado SSL personalizado à sua aplicação web, o back-end da aplicação móvel ou a aplicação API no App Service do Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: c18ca8e81fefdee723714c6535160e75ef4d698d
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Vincular um certificado SSL personalizado existente para Web Apps do Azure

As aplicações Web do Azure fornece uma serviço de alojamento na web altamente dimensionável e aplicação de patches personalizada. Este tutorial mostra como vincular um certificado SSL personalizado comprado numa autoridade de certificação fidedignas para [Web Apps do Azure](app-service-web-overview.md). Quando tiver terminado, poderá aceder à sua aplicação web, o ponto final de HTTPS do seu domínio DNS personalizado.

![Aplicação Web com o certificado SSL personalizado](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar o escalão de preço da sua aplicação
> * Vincular o certificado SSL personalizado no App Service
> * Impor HTTPS para a sua aplicação
> * Automatizar o enlace de certificado SSL com scripts

> [!NOTE]
> Se precisar de obter um certificado SSL personalizado, pode obter uma no portal do Azure diretamente e vinculá-lo à sua aplicação web. Siga o [tutorial de certificados de serviço de aplicações](web-sites-purchase-ssl-web-site.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Criar uma aplicação de serviço de aplicações](/azure/app-service/)
- [Mapear um nome DNS personalizado à sua aplicação web](app-service-web-tutorial-custom-domain.md)
- Adquirir um certificado SSL de uma autoridade de certificação fidedignas
- Tem a chave privada utilizada para assinar o pedido de certificado SSL

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Requisitos para o seu certificado SSL

Para utilizar um certificado no App Service, o certificado tem de cumprir os seguintes requisitos:

* Assinado por uma autoridade de certificação fidedigna
* Exportado como um ficheiro PFX protegido por palavra-passe
* Contém os bits da chave privada, pelo menos, 2048 longa
* Contém todos os certificados intermédios da cadeia de certificados

> [!NOTE]
> **Certificados de criptografia de curva (ECC) elíptica** pode trabalhar com o serviço de aplicações, mas não são abrangidos por este artigo. Trabalhar com a autoridade de certificação nos passos exatos para criar certificados ECC.

## <a name="prepare-your-web-app"></a>Preparar a sua aplicação web

Vincular um certificado SSL personalizado à sua aplicação web, o [plano do App Service](https://azure.microsoft.com/pricing/details/app-service/) tem de constar o **básico**, **padrão**, ou **Premium** camada. Neste passo, certifique-se de que a aplicação web é suportado no escalão de preço.

### <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Abra o [Portal do Azure](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Navegue até à sua aplicação web

No menu à esquerda, clique em **serviços aplicacionais**e, em seguida, clique no nome da sua aplicação web.

![Selecione a aplicação web](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Atingiu a página de gestão da sua aplicação web.  

### <a name="check-the-pricing-tier"></a>Verifique o escalão de preço

No painel de navegação esquerdo da página web app, desloque-se para o **definições** secção e selecione **aumentar verticalmente (plano do App Service)**.

![Menu de dimensionamento](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Certifique-se de que a sua aplicação web não está no **livres** ou **partilhados** camada. Escalão atual da sua aplicação web é realçado por uma caixa de blue um escuro.

![Verifique o escalão de preço](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

SSL personalizado não é suportado a **livres** ou **partilhados** camada. Se precisar de aumentar verticalmente, siga os passos na secção seguinte. Caso contrário, feche o **escolher o escalão de preço** página e avançar para o [carregar e vincular o certificado SSL](#upload).

### <a name="scale-up-your-app-service-plan"></a>Dimensionar o seu plano de serviço de aplicações

Selecione uma do **básico**, **padrão**, ou **Premium** camadas.

Clique em **Selecionar**.

![Escolha o escalão de preço](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

Quando for apresentada a seguinte notificação, a operação de dimensionamento está concluída.

![Aumentar verticalmente a notificação](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Vincular o certificado SSL

Está pronto para carregar o certificado SSL à sua aplicação web.

### <a name="merge-intermediate-certificates"></a>Intercalar certificados intermédios

Se a autoridade de certificação dá-lhe vários certificados na cadeia de certificados, terá dos certificados na ordem de intercalação. 

Para tal, abra cada certificado recebido num editor de texto. 

Criar um ficheiro para o certificado intercalado, denominado _mergedcertificate.crt_. Num editor de texto, copie o conteúdo de cada certificado para este ficheiro. A ordem dos seus certificados deve seguir a ordem na cadeia de certificados, a partir do seu certificado e termina com o certificado de raiz. Se parece com o exemplo seguinte:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportar certificados para PFX

Exporte o certificado SSL intercalado com a chave privada que o pedido de certificado foi gerado com.

Se gerou o pedido de certificado utilizando OpenSSL, em seguida, criou um ficheiro de chave privada. Para exportar os certificados para PFX, execute o seguinte comando. Substitua os marcadores de posição  _&lt;o ficheiro de chave privada >_ e  _&lt;intercaladas--ficheiro de certificado >_ com os caminhos para a chave privada e o ficheiro de certificado intercalada.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Quando lhe for pedido, defina uma palavra-passe de exportação. Irá utilizar esta palavra-passe ao carregar o certificado SSL para o serviço de aplicações mais tarde.

Se utilizou o IIS ou _Certreq.exe_ para gerar o seu pedido de certificado, instale o certificado para o computador local e, em seguida, [exportar o certificado para PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Carregar o seu certificado SSL

Para carregar o certificado SSL, clique em **certificados SSL** na navegação à esquerda da sua aplicação web.

Clique em **carregar certificado**. 

No **ficheiro de certificado PFX**, selecione o ficheiro PFX. No **palavra-passe do certificado**, escreva a palavra-passe que criou quando exportou o ficheiro PFX.

Clique em **Carregar**.

![Carregar certificado](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

Quando estiver concluída do serviço de aplicações a carregar o certificado, é apresentado no **certificados SSL** página.

![Certificado carregado](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Vincular o certificado SSL

No **enlaces SSL** secção, clique em **Adicionar enlace**.

No **Adicionar enlace de SSL** página, utilize as dropdowns para selecionar o nome de domínio para proteger e o certificado a utilizar.

> [!NOTE]
> Se carregou o certificado, mas não consegue ver os nomes de domínio no **Hostname** lista pendente, tente atualizar a página de browser.
>
>

No **SSL tipo**, selecione se pretende utilizar  **[indicação de nome de servidor (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)**  ou SSL baseado em IP.

- **SSL SNI baseado** -enlaces SSL com base em vários SNI podem ser adicionados. Esta opção permite vários certificados SSL proteger vários domínios no mesmo endereço IP. Browsers mais modernos (incluindo o Internet Explorer, Chrome, Firefox e Opera) suportam SNI (encontrar informações mais abrangentes do suporte de browser em [indicação do nome de servidor](http://wikipedia.org/wiki/Server_Name_Indication)).
- **SSL baseado em IP** -apenas um enlace SSL baseado em IP pode ser adicionado. Esta opção permite apenas um certificado SSL proteger um endereço IP público dedicado. Para proteger vários domínios, tem de protegê-las todas com o mesmo certificado SSL. Esta é a opção tradicional para enlace de SSL.

Clique em **Adicionar enlace**.

![Vincular o certificado SSL](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Quando estiver concluída do serviço de aplicações a carregar o certificado, é apresentado no **enlaces SSL** secções.

![Certificado vinculado à aplicação web](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Remapear um registo para IP SSL

Se não utilizar SSL baseado em IP na sua aplicação web, avance para o [HTTPS de teste para o domínio personalizado](#test).

Por predefinição, a sua aplicação web utiliza um endereço IP público partilhado. Quando vincular um certificado com SSL baseado em IP, o serviço de aplicações cria um endereço IP novo e dedicado para a sua aplicação web.

Se ter mapeado um registo à sua aplicação web, atualize o registo de domínio com este endereço IP novo, dedicado.

A aplicação web **domínio personalizado** página é atualizada com o endereço IP novo, dedicado. [Copiar este endereço IP](app-service-web-tutorial-custom-domain.md#info), em seguida, [remapear o registo](app-service-web-tutorial-custom-domain.md#map-an-a-record) para este novo endereço IP.

<a name="test"></a>

## <a name="test-https"></a>Teste HTTPS

Agora só falta fazer é certificar-se de que funciona de HTTPS para o seu domínio personalizado. Vários browsers, navegue até à `https://<your.custom.domain>` para ver que serve verticalmente a sua aplicação web.

![Navegação do portal para aplicações do Azure](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Se a aplicação web fornece a erros de validação de certificado, provavelmente, está a utilizar um certificado autoassinado.
>
> Se não for esse o caso, poderá ter deixou os certificados intermédios ao exportar o certificado para o ficheiro PFX.

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Impor HTTPS

Serviço de aplicações não *não* impor HTTPS, pelo que qualquer pessoa pode continuar a aceder a aplicação web através de HTTP. Para impor o HTTPS para a sua aplicação web, definir uma regra de reescrever no _Web. config_ ficheiro para a sua aplicação web. Serviço de aplicações utiliza este ficheiro, independentemente da arquitetura de linguagem da sua aplicação web.

> [!NOTE]
> Não há específicas do idioma redirecionamento de pedidos. Pode utilizar o ASP.NET MVC o [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) filtro em vez da regra de reescrever no _Web. config_.

Se tiver um programador de .NET, deve estar relativamente familiarizado com este ficheiro. Trata-se na raiz da sua solução.

Em alternativa, se desenvolver com PHP, Node.js, Python ou Java, há a possibilidade deste ficheiro em seu nome é gerado no App Service.

Ligar ao ponto final FTP da sua aplicação web, seguindo as instruções apresentadas em [implementar a aplicação no serviço de aplicações do Azure através de FTP/S](app-service-deploy-ftp.md).

Este ficheiro deve estar localizado no _/home/site/wwwroot_. Se não, crie um _Web. config_ nesta pasta com o XML dos seguinte:

```xml   
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <!-- BEGIN rule ELEMENT FOR HTTPS REDIRECT -->
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
        <!-- END rule ELEMENT FOR HTTPS REDIRECT -->
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

Para um existente _Web. config_ ficheiro, copie todo o `<rule>` elemento no seu _Web. config_do `configuration/system.webServer/rewrite/rules` elemento. Se existirem outros `<rule>` os elementos da sua _Web. config_, coloque o copiados `<rule>` elemento antes de outros `<rule>` elementos.

Esta regra devolve um 301 HTTP (redirecionar permanente) para o protocolo HTTPS sempre que o utilizador faz um pedido de HTTP para a aplicação web. Por exemplo, será redirecionado do `http://contoso.com` para `https://contoso.com`.

Para obter mais informações sobre o módulo IIS URL Rewrite, consulte o [URL Rewrite](http://www.iis.net/downloads/microsoft/url-rewrite) documentação.

## <a name="enforce-https-for-web-apps-on-linux"></a>Impor HTTPS para aplicações Web no Linux

Serviço de aplicações no Linux não *não* impor HTTPS, pelo que qualquer pessoa pode continuar a aceder a aplicação web através de HTTP. Para impor o HTTPS para a sua aplicação web, definir uma regra de reescrever no _htaccess_ ficheiro para a sua aplicação web. 

Ligar ao ponto final FTP da sua aplicação web, seguindo as instruções apresentadas em [implementar a aplicação no serviço de aplicações do Azure através de FTP/S](app-service-deploy-ftp.md).

No _/home/site/wwwroot_, crie um _htaccess_ ficheiro com o seguinte código:

```
RewriteEngine On
RewriteCond %{HTTP:X-ARR-SSL} ^$
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

Esta regra devolve um 301 HTTP (redirecionar permanente) para o protocolo HTTPS sempre que o utilizador faz um pedido de HTTP para a aplicação web. Por exemplo, será redirecionado do `http://contoso.com` para `https://contoso.com`.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar enlaces SSL para a sua aplicação web com scripts, utilizando o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>CLI do Azure

O comando seguinte carrega um ficheiro PFX exportado e obtém o thumbprint.

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

O comando seguinte adiciona um enlace SSL baseado em SNI, utilizando a respetiva impressão digital do comando anterior.

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

### <a name="azure-powershell"></a>Azure PowerShell

O comando seguinte carrega um ficheiro PFX exportado e adiciona um enlace SSL baseado em SNI.

```PowerShell
New-AzureRmWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>Certificados públicos (opcionais)
Pode carregar [certificados públicos](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/) à sua aplicação web. Pode utilizar certificados públicos com as Web Apps no App Service ou ambiente de serviço de aplicações (ASE). Se tiver de armazenar o certificado no arquivo de certificados de LocalMachine, terá de utilizar uma aplicação web no ambiente de serviço de aplicações. Para obter mais detalhes, consulte [como configurar certificados públicos à sua aplicação Web](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer).

![Carregar o certificado público](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Atualizar o escalão de preço da sua aplicação
> * Vincular o certificado SSL personalizado no App Service
> * Impor HTTPS para a sua aplicação
> * Automatizar o enlace de certificado SSL com scripts

Avançar para o próximo tutorial para saber como utilizar a rede de entrega de conteúdos do Azure.

> [!div class="nextstepaction"]
> [Adicionar uma rede de entrega de conteúdos (CDN) para um serviço de aplicações do Azure](app-service-web-tutorial-content-delivery-network.md)
