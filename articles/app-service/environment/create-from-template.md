---
title: Criar um ambiente do App Service do Azure utilizando um modelo do Resource Manager
description: Explica como criar um ambiente externo ou o App Service do Azure ILB utilizando um modelo do Resource Manager
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: b3829f0e1b87451bf0706edc268359be5c4480bc
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Criar ASE utilizando um modelo Azure Resource Manager

## <a name="overview"></a>Descrição geral
Ambientes do App Service do Azure (ASEs) podem ser criados com um ponto final acessível através da internet ou de um ponto final de um endereço interno numa rede virtual do Azure (VNet). Quando é criado com um ponto final interno, esse ponto final é fornecida por um Azure componente chamado um balanceador de carga interno (ILB). ASE num endereço IP é denominado ILB ASE. ASE com um ponto final público é denominado ASE externo. 

ASE pode ser criado utilizando o portal do Azure ou um modelo Azure Resource Manager. Este artigo explica os passos e a sintaxe que terá de criar um ASE externo ou o ILB ASE com modelos do Resource Manager. Para saber como criar ASE no portal do Azure, consulte [tornar ASE externo] [ MakeExternalASE] ou [tornar ILB ASE][MakeILBASE].

Quando cria ASE no portal do Azure, pode criar a VNet em simultâneo ou escolha uma VNet pré-existentes para implementar numa. Quando cria o ASE a partir de um modelo, tem de começar com: 

* Uma VNet do Resource Manager.
* Uma sub-rede essa VNet. Recomendamos um tamanho de sub-rede ASE de `/25` com endereços de 128 para permitir um crescimento futuro. Depois do ASE é criada, não é possível alterar o tamanho.
* O ID de recurso da sua VNet. Pode obter estas informações a partir do portal do Azure nas propriedades de rede virtual.
* A subscrição que pretende implementar numa.
* A localização que pretende implementar numa.

Para automatizar a criação da ASE:

1. Crie o ASE a partir de um modelo. Se criar ASE externo, tiver terminado, após este passo. Se criar ILB ASE, existem mais algumas coisas fazer.

2. Depois de criar o ILB ASE, um certificado SSL que corresponda ao seu domínio ILB ASE é carregado.

3. O certificado SSL carregado é atribuído a ASE o ILB, como o certificado SSL "predefinida".  Este certificado é utilizado para o tráfego SSL para as aplicações em ASE o ILB quando utilizarem o domínio de raiz comuns que está atribuído a ASE (por exemplo, https://someapp.mycustomrootcomain.com).


## <a name="create-the-ase"></a>Criar o ASE
Um modelo do Resource Manager, que cria o ficheiro de parâmetros associados e ASE está disponível [um exemplo] [ quickstartasev2create] no GitHub.

Se pretender efetuar ILB ASE, utilize estes modelo do Resource Manager [exemplos][quickstartilbasecreate]. Estes se adaptar a que utilizar maiúsculas e minúsculas. A maioria dos parâmetros de *azuredeploy.parameters.json* ficheiro são comuns para a criação do ILB ASEs e ASEs externo. A lista seguinte, chama os parâmetros de nota especial ou que são exclusivos, quando criar ILB ASE:

* *internalLoadBalancingMode*: na maioria dos casos, definido para 3, o que significa tráfego HTTP/HTTPS portas 80/443 tanto os/dados de controlo de portas de canal listened pelo serviço FTP num ASE, será vinculado a uma atribuído o ILB rede virtual interna endereço. Se esta propriedade estiver definida como 2, apenas as FTP relacionados com o serviço portas (canais de controlo e dados) estão vinculadas a um endereço do ILB. O tráfego HTTP/HTTPS permanece no VIP público.
* *dnsSuffix*: este parâmetro define o domínio de raiz predefinido que está atribuído a ASE. A variação pública do App Service do Azure, o domínio de raiz predefinido para todas as aplicações web é *azurewebsites.net*. Porque é interno a rede virtual de um cliente ILB ASE, este não fazer sentido para utilizar o domínio de raiz do serviço público predefinido. Em vez disso, ILB ASE deve ter um domínio de raiz predefinido que faz sentido para utilização na rede virtual interna de uma empresa. Por exemplo, Contoso Corporation poderá utilizar um domínio de raiz da predefinição de *interna contoso.com* para aplicações que se destinam a ser resolvível e acessível apenas dentro da rede virtual da Contoso. 
* *ipSslAddressCount*: este parâmetro será assumida automaticamente a um valor de 0 no *azuredeploy. JSON* ficheiro porque o ILB ASEs ter apenas um único endereço ILB. Não existem nenhum endereços de IP-SSL explícitos para ILB ASE. Por conseguinte, o conjunto de endereços IP SSL para ILB ASE deve ser definido como zero. Caso contrário, ocorre um erro de aprovisionamento. 

Depois do *azuredeploy.parameters.json* ficheiro é preenchido, crie o ASE utilizando o fragmento de código do PowerShell. Altere os caminhos de ficheiro para corresponder as localizações de ficheiro de modelo do Resource Manager no seu computador. Lembre-se de fornecer os seus próprios valores para o nome da implementação do Resource Manager e o nome do grupo de recursos:

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Demora cerca de uma hora para ASE a ser criado. Em seguida, o ASE aparece no portal na lista de ASEs para a subscrição que acionou a implementação.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Carregar e configurar o certificado SSL "predefinido"
Um certificado SSL tem de ser associado a ASE como o certificado SSL de "predefinida" que é utilizado para estabelecer ligações de SSL para aplicações. Se o sufixo DNS do ASE predefinido é *interna contoso.com*, uma ligação para https://some-random-app.internal-contoso.com requer um certificado SSL que é válido para **.internal contoso.com*. 

Obter um certificado SSL válido ao utilizar autoridades de certificação interna, comprar um certificado de um emissor externo ou utilizar um certificado autoassinado. Independentemente da origem do certificado SSL, os seguintes atributos de certificado tem de ser configurados corretamente:

* **Requerente**: este atributo deve ser definido como **.your-raiz-domínio-here.com*.
* **Nome alternativo do requerente**: este atributo tem de incluir os **.your-raiz-domínio-here.com* e **.scm.your-raiz-domínio-here.com*. Ligações de SSL para o site SCM/Kudu associados a cada aplicação utilizam um endereço do formulário *your-app-name.scm.your-root-domain-here.com*.

Com um certificado SSL válido mão, são necessários dois passos preparatórios adicionais. Converter/guardar o certificado SSL como um ficheiro. pfx. Lembre-se de que o ficheiro. pfx tem de incluir todos os intermédio e certificados de raiz. Proteja-a com uma palavra-passe.

O ficheiro. pfx tem de ser convertido numa cadeia base64 porque o certificado SSL é carregado utilizando um modelo do Resource Manager. Porque os modelos do Resource Manager são ficheiros de texto, o ficheiro. pfx tem de ser convertido numa cadeia base64. Desta forma pode ser incluído como um parâmetro do modelo.

Utilize o seguinte fragmento de código do PowerShell para:

* Gere um certificado autoassinado.
* Exporte o certificado como um ficheiro. pfx.
* Converta o ficheiro. pfx numa cadeia com codificação base64.
* Guarde a cadeia com codificação base64 para um ficheiro separado. 

Este código do PowerShell para codificação base64 foi adaptado do [blogue de scripts do PowerShell][examplebase64encoding]:

        $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

        $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
        $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

        $fileName = "exportedcert.pfx"
        Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

        $fileContentBytes = get-content -encoding byte $fileName
        $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
        $fileContentEncoded | set-content ($fileName + ".b64")

Depois do certificado SSL é gerado e convertido numa cadeia com codificação base64 com êxito, utilize o modelo de Gestor de recursos de exemplo [configurar o certificado SSL predefinida] [ quickstartconfiguressl] no GitHub. 

Os parâmetros de *azuredeploy.parameters.json* ficheiro estão listados aqui:

* *appServiceEnvironmentName*: O nome do ASE ILB ser configurados.
* *existingAseLocation*: cadeia de texto que contém a região do Azure onde o ILB ASE foi implementada.  Por exemplo: "Sul Central EUA".
* *pfxBlobString*: A representação de cadeia com codificação based64 do ficheiro. pfx. Utilize o fragmento de código mostrado anteriormente e copie a cadeia contida em "exportedcert.pfx.b64". Cole-a no como o valor de *pfxBlobString* atributo.
* *palavra-passe*: A palavra-passe utilizada para proteger o ficheiro. pfx.
* *certificateThumbprint*: O thumbprint do certificado. Se é possível obter este valor a partir do PowerShell (por exemplo, *$certificate. Thumbprint* de fragmento de código anterior), pode utilizar o valor como está. Se copiar o valor da caixa de diálogo de certificado do Windows, lembre-se da faixa enviados os espaços de supérfluas. O *certificateThumbprint* deve ter um aspeto semelhante AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: um identificador de cadeia amigável da sua própria escolha utilizado para identidade o certificado. O nome é utilizado como parte do identificador exclusivo do Resource Manager para o *Microsoft.Web/certificates* entidade que representa o certificado SSL. O nome *tem* terminar com o seguinte sufixo: \_yourASENameHere_InternalLoadBalancingASE. O portal do Azure utiliza este sufixo como um indicador de que o certificado é utilizado para proteger ASE ativado o ILB.

Um exemplo de abreviado *azuredeploy.parameters.json* é mostrada aqui:

    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Depois do *azuredeploy.parameters.json* ficheiro é preenchido, configure o certificado SSL predefinida, utilizando o fragmento de código do PowerShell. Altere os caminhos de ficheiro a corresponder onde os ficheiros de modelo do Resource Manager estão localizados no seu computador. Lembre-se de fornecer os seus próprios valores para o nome da implementação do Resource Manager e o nome do grupo de recursos:

     $templatePath="PATH\azuredeploy.json"
     $parameterPath="PATH\azuredeploy.parameters.json"

     New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Demora cerca de 40 minutos por front-end de ASE para aplicar a alteração. Por exemplo, para um ASE tamanho predefinido que utiliza dois front-ends, o modelo demora aproximadamente uma hora e 20 minutos a concluir. Enquanto o modelo está em execução, não é possível aumentar o ASE.  

Quando o modelo estiver concluído, as aplicações em ASE o ILB podem ser acedidas através de HTTPS. As ligações são protegidas utilizando o certificado SSL predefinido. O certificado SSL predefinido é utilizado quando as aplicações em ASE o ILB são resolvidas utilizando uma combinação do nome da aplicação, o nome de anfitrião predefinido. Por exemplo, https://mycustomapp.internal-contoso.com utiliza o certificado SSL predefinido para **.internal contoso.com*.

No entanto, tal como as aplicações que são executados no serviço multi-inquilino público, os programadores podem configurar os nomes de anfitrião personalizado para aplicações individuais. Também pode configurar enlaces de certificado de SNI SSL exclusivos para aplicações individuais.

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicações v1 ##
Ambiente de serviço de aplicações tem duas versões: ASEv1 e ASEv2. As informações foi com base no ASEv2. Esta secção mostra as diferenças entre ASEv1 e ASEv2.

No ASEv1, pode gere todos os recursos manualmente. Que inclui o front-ends, funcionários e endereços IP utilizados para SSL baseado em IP. Antes de pode ampliar o plano de serviço de aplicações, tem de ampliar o conjunto de trabalho que pretende alojá-la.

ASEv1 utiliza um modelo de preços diferentes de ASEv2. ASEv1, paga para cada vCPU atribuído. Que inclui vCPUs que são utilizados para front-ends ou trabalhadores que não alojam quaisquer cargas de trabalho. ASEv1, o tamanho de dimensionamento máximo predefinido de ASE é 55 total de anfitriões. Que inclui os trabalhadores e front-ends. Uma vantagem em ASEv1 é que pode ser implementada numa rede virtual clássica e uma rede virtual do Gestor de recursos. Para saber mais sobre ASEv1, consulte o artigo [introdução do ambiente de serviço de aplicações v1][ASEv1Intro].

Para criar um ASEv1 utilizando um modelo do Resource Manager, consulte [criar v1 um ILB ASE com um modelo do Resource Manager][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
