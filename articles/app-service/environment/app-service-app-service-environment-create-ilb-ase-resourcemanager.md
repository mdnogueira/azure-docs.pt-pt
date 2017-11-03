---
title: Como criar ASE ILB utilizando os modelos Azure Resource Manager | Microsoft Docs
description: Saiba como criar ASE de Balanceador de carga interno utilizando os modelos Azure Resource Manager.
services: app-service
documentationcenter: 
author: stefsch
manager: nirma
editor: 
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.openlocfilehash: ea9407208f1bf555cf1a6d166825896dec89ec29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Como Criar um ASE do ILB Utilizando Modelos do Azure Resource Manager

> [!NOTE] 
> Este artigo é sobre o ambiente de serviço de aplicações v1. Há uma versão mais recente do ambiente de serviço de aplicações que são mais fáceis de utilizar e é executada na infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).
>

## <a name="overview"></a>Descrição geral
Ambientes de serviço de aplicações podem ser criados com um endereço interno da rede virtual em vez de um VIP público.  Este endereço interno é fornecido por um componente do Azure chamado Balanceador de carga interno (ILB).  ILB ASE podem ser criada no portal do Azure.  Pode também ser criado através da automatização através de modelos Azure Resource Manager.  Este artigo explica os passos e a sintaxe necessária para criar ILB ASE com modelos Azure Resource Manager.

Existem três passos envolvidos na criação de ILB ASE a automatizar:

1. É criado pela primeira vez o ASE base numa rede virtual com um endereço de Balanceador de carga interno em vez de um VIP público.  Como parte deste passo, é atribuído um nome de domínio de raiz para o ILB ASE.
2. Quando o ASE do ILB for criado, um certificado SSL é carregado.  
3. O certificado SSL carregado explicitamente é atribuído para o ILB ASE como o certificado SSL "predefinida".  Este certificado SSL será utilizado para tráfego SSL para as aplicações em ASE o ILB quando as aplicações são resolvidas utilizando o domínio de raiz comuns atribuído para o ASE (por exemplo, https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Criar a Base do ILB ASE
Um modelo do Azure Resource Manager de exemplo e o respetivo ficheiro de parâmetros associados, estão disponíveis no GitHub [aqui][quickstartilbasecreate].

A maioria dos parâmetros de *azuredeploy.parameters.json* ficheiro são comuns para criar o ILB ASEs, bem como ASEs vinculadas a um VIP público.  A lista abaixo chama os parâmetros de nota especial ou que são exclusivos, quando criar ILB ASE:

* *interalLoadBalancingMode*: na maioria dos casos conjunto esta opção para 3, o que significa tráfego HTTP/HTTPS portas 80/443 tanto os/dados de controlo de portas de canal listened pelo serviço FTP num ASE, será vinculado a um ILB atribuído um endereço de rede virtual.  Se esta propriedade em vez disso, é definida como 2, apenas o serviço FTP relacionados com portas (canais de controlo e dados) serão vinculadas a um endereço do ILB, enquanto o tráfego HTTP/HTTPS-á no VIP público.
* *dnsSuffix*: este parâmetro define o domínio de raiz predefinido que será atribuído para o ASE.  A variação pública do App Service do Azure, o domínio de raiz predefinido para todas as aplicações web é *azurewebsites.net*.  No entanto, uma vez que ILB ASE interno a rede virtual de um cliente, este não fazer sentido para utilizar o domínio de raiz do serviço público predefinido.  Em vez disso, ILB ASE deve ter um domínio de raiz predefinido que faz sentido para utilização na rede virtual interna de uma empresa.  Por exemplo, um Contoso Corporation hipotético poderá utilizar um domínio de raiz da predefinição de *interna contoso.com* para aplicações que se destinam-se apenas ser resolvível e é acessível na rede virtual da Contoso. 
* *ipSslAddressCount*: este parâmetro é automaticamente predefinido para um valor de 0 no *azuredeploy. JSON* ficheiro porque o ILB ASEs ter apenas um único endereço ILB.  Não existem nenhum endereços de IP-SSL explícitos para ASE ILB e, por conseguinte, o conjunto de endereços IP SSL para ILB ASE tem de ser definido para zero, caso contrário, ocorrerá um erro de aprovisionamento. 

Uma vez a *azuredeploy.parameters.json* ficheiro tiver sido preenchido para ILB ASE, ASE o ILB, em seguida, podem ser criada utilizando o seguinte fragmento de código do Powershell.  Altere o ficheiro de caminhos para corresponder onde os ficheiros de modelo Azure Resource Manager estão localizados no seu computador.  Lembre-se também de fornecer os seus próprios valores para o nome de implementação Azure Resource Manager e o nome do grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Depois do Gestor de recursos do Azure modelo é submetido irá demorar algumas horas para o ILB ASE a ser criado.  Uma vez concluída a criação, o ILB ASE irá aparecer no portal do UX na lista de ambientes do App Service para a subscrição que acionou a implementação.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Carregar e configurar o certificado SSL "Predefinido"
Quando o ASE do ILB for criado, um certificado SSL deve ser associado a ASE "predefinido" certificado SSL utiliza para estabelecer ligações de SSL para aplicações.  Continuar com o exemplo de Contoso Corporation hipotético, se o ASE predefinido DNS sufixo é *interna contoso.com*, em seguida, uma ligação para *https://some-random-app.internal-contoso.com* requer um certificado SSL que é válido para **.internal contoso.com*. 

Existem várias formas de obter um certificado SSL válido, incluindo ACs internas, comprar um certificado de um emissor externo e a utilizar um certificado autoassinado.  Independentemente da origem do certificado SSL, os seguintes atributos de certificado tem de ser configurados corretamente:

* *Requerente*: este atributo deve ser definido como **.your-raiz-domínio-here.com*
* *Nome alternativo do requerente*: este atributo tem de incluir os **.your-raiz-domínio-here.com*, e **.scm.your-raiz-domínio-here.com*.  O motivo para a segunda entrada é que as ligações de SSL para o site SCM/Kudu associados a cada aplicação serão efetuadas utilizando um endereço do formulário *your-app-name.scm.your-root-domain-here.com*.

Com um certificado SSL válido mão, são necessários dois passos preparatórios adicionais.  O certificado SSL tem de ser convertida/guardada como um ficheiro. pfx.  Lembre-se de que o ficheiro. pfx tem de incluir todos os intermédio e certificados de raiz e também tem de estar protegido por uma palavra-passe.

Em seguida, o ficheiro. pfx resultante tem de ser convertido numa cadeia base64 porque o certificado SSL será carregado através de um modelo Azure Resource Manager.  Uma vez que os modelos Azure Resource Manager são ficheiros de texto, o ficheiro. pfx tem de ser convertido numa cadeia base64, pelo que pode ser incluído como um parâmetro do modelo.

O fragmento de código do Powershell abaixo mostra um exemplo de gerar um certificado autoassinado, exportar o certificado como um ficheiro. pfx, ao converter o ficheiro. pfx num base64 cadeia codificada e, em seguida, guarde o base64 codificado cadeia para um ficheiro separado.  O código do Powershell para codificação base64 foi adaptado do [blogue de Scripts do Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Depois do certificado SSL foi gerado e êxito convertido para um codificado em base64 de cadeia, o modelo Azure Resource Manager de exemplo no GitHub para [configurar o certificado SSL predefinido] [ configuringDefaultSSLCertificate] pode ser utilizado.

Os parâmetros de *azuredeploy.parameters.json* ficheiro estão listados abaixo:

* *appServiceEnvironmentName*: O nome do ASE ILB ser configurados.
* *existingAseLocation*: cadeia de texto que contém a região do Azure onde o ILB ASE foi implementada.  Por exemplo: "Sul Central EUA".
* *pfxBlobString*: O based64 codificado representação de cadeia do ficheiro. pfx.  Utilizando o fragmento de código mostrado anteriormente, seria copie a cadeia contida em "exportedcert.pfx.b64" e cole-a no como o valor do *pfxBlobString* atributo.
* *palavra-passe*: A palavra-passe utilizada para proteger o ficheiro. pfx.
* *certificateThumbprint*: O thumbprint do certificado.  Se é possível obter este valor a partir do Powershell (por exemplo, *$certificate. Thumbprint* de fragmento de código anterior), pode utilizar o valor como-é.  No entanto se copiar o valor da caixa de diálogo de certificado da Windows, lembre-se da faixa enviados os espaços de supérfluas.  O *certificateThumbprint* deve ter um aspeto semelhante: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: um identificador de cadeia amigável da sua própria escolha utilizado para identidade o certificado.  O nome é utilizado como parte do identificador exclusivo do Azure Resource Manager para o *Microsoft.Web/certificates* entidade que representa o certificado SSL.  O nome **tem** terminar com o seguinte sufixo: \_yourASENameHere_InternalLoadBalancingASE.  Este sufixo é utilizado pelo portal como um indicador de que o certificado é utilizado para proteger ASE ativado o ILB.

Um exemplo de abreviado *azuredeploy.parameters.json* é mostrado abaixo:

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

Uma vez a *azuredeploy.parameters.json* ficheiro tiver sido preenchido, o certificado SSL predefinido pode ser configurado através do seguinte fragmento de código do Powershell.  Altere o ficheiro de caminhos para corresponder onde os ficheiros de modelo Azure Resource Manager estão localizados no seu computador.  Lembre-se também de fornecer os seus próprios valores para o nome de implementação Azure Resource Manager e o nome do grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Depois do modelo Azure Resource Manager é submetido irá demorar aproximadamente minutos forty minutos por ASE front-end para aplicar a alteração.  Por exemplo, com um ASE predefinido dimensionado utilizando dois frente-ends, o modelo irá demorar cerca uma hora e twenty minutos para concluir.  Enquanto o modelo está em execução a ASE não conseguirá ampliada.  

Após a conclusão do modelo, as aplicações em ASE o ILB podem ser acedidas através de HTTPS e as ligações serão protegidas com o certificado SSL predefinido.  O certificado SSL predefinida será utilizado quando as aplicações em ASE o ILB são resolvidas utilizando uma combinação do nome da aplicação, o nome de anfitrião predefinido.  Por exemplo *https://mycustomapp.internal-contoso.com* utilizaria o certificado SSL predefinido para **.internal contoso.com*.

No entanto, tal como aplicações em execução no serviço multi-inquilino público, os programadores podem também configurar os nomes de anfitrião personalizado para aplicações individuais e, em seguida, configure os enlaces de certificado de SNI SSL exclusivos para aplicações individuais.  

## <a name="getting-started"></a>Introdução
Para começar com ambientes do App Service, consulte o artigo [introdução ao ambiente de serviço de aplicações](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

