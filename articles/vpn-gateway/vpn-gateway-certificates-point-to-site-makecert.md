---
title: 'Gerar e exportar certificados para ponto a Site: MakeCert: Azure | Microsoft Docs'
description: "Este artigo contém passos para criar um certificado de raiz autoassinado, exportar a chave pública e gerar os certificados de cliente utilizando o MakeCert."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2017
ms.author: cherylmc
ms.openlocfilehash: 2beacc461370f268e54e1eedcb32939f7c606b14
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Gerar e exportar certificados para ligações ponto a Site utilizando o MakeCert

As ligações ponto a Site utilizam certificados para autenticar. Este artigo mostra como criar um certificado de raiz autoassinado e gerar os certificados de cliente utilizando o MakeCert. Se estiver à procura de passos de configuração de ponto a Site, tais como carregar certificados de raiz, selecione um dos artigos ' Configurar ponto a Site' na lista seguinte:

> [!div class="op_single_selector"]
> * [Criar certificados autoassinados - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Criar certificados autoassinados - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Configurar o ponto a Site - Resource Manager - portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Configurar o ponto a Site - Gestor de recursos - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Configurar o ponto a Site - Classic - portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Enquanto Recomendamos a utilização de [passos do Windows 10 PowerShell](vpn-gateway-certificates-point-to-site.md) para criar os certificados, podemos fornecer estas instruções MakeCert como um método opcional. Os certificados que geram o utilizando um dos métodos que podem ser instalados num [qualquer sistema operativo de cliente suportada](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). No entanto, o MakeCert tem a limitação seguinte:

* MakeCert foi preterido. Isto significa que esta ferramenta pode ser removida em qualquer ponto. Todos os certificados que já gerou utilizando o MakeCert não serão afetados quando MakeCert já não está disponível. MakeCert só é utilizado para gerar os certificados, não como um mecanismo de validação.

## <a name="rootcert"></a>Criar um certificado de raiz autoassinado

Os passos seguintes mostram como criar um certificado autoassinado utilizando o MakeCert. Estes passos não são específicos do modelo de implementação. Que são válidos para o Resource Manager e clássico.

1. Transfira e instale [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Após a instalação, normalmente, pode encontrar o utilitário makecert.exe sob este caminho: ' C:\Program Files (x86) \Windows Kits\10\bin\<arquitetura >'. Contudo, é possível que tenha sido instalado para outra localização. Abra uma linha de comandos como administrador e navegue para a localização do utilitário MakeCert. Pode utilizar o exemplo seguinte, ajuste para a localização adequada:

  ```cmd
  cd C:\Program Files (x86)\Windows Kits\10\bin\x64
  ```
3. Crie e instale um certificado no arquivo de certificados pessoais no seu computador. O exemplo seguinte cria um correspondente *. cer* ficheiro que carregar para o Azure quando configurar P2S. Substitua o nome que pretende utilizar para o certificado 'P2SRootCert' e 'P2SRootCert.cer'. O certificado está localizado na sua 'certificados - User\Personal\Certificates atual'.

  ```cmd
  makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
  ```

## <a name="cer"></a>Exportar a chave pública (. cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

O ficheiro exported.cer tem de ser carregado para o Azure. Para obter instruções, consulte [configurar uma ligação ponto a Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Para adicionar um certificado de raiz fidedigna adicionais, consulte [nesta secção](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) do artigo.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportar o certificado autoassinado e armazene-(opcional) a chave privada

Pode pretender exportar o certificado de raiz autoassinado e armazena de forma segura. Se necessário, mais tarde pode instalá-lo noutro computador e gerar mais certificados de cliente ou exportar outro ficheiro. cer. Para exportar o certificado de raiz autoassinado como um ficheiro. pfx, selecione o certificado de raiz e utilize os mesmos passos conforme descrito em [exportar um certificado de cliente](#clientexport).

## <a name="create-and-install-client-certificates"></a>Criar e instalar certificados de cliente

Não instale o certificado autoassinado diretamente no computador cliente. Tem de gerar um certificado de cliente a partir do certificado autoassinado. Exportar e instalar o certificado de cliente para o computador cliente. Os passos seguintes não são específicos do modelo de implementação. Que são válidos para o Resource Manager e clássico.

### <a name="clientcert"></a>Gerar um certificado de cliente

Cada computador cliente que se ligue uma VNet por Ponto a Site tem de ter um certificado de cliente instalado. Gerar um certificado de cliente a partir do certificado de raiz autoassinado e, em seguida, exportar e instalar o certificado de cliente. Se o certificado de cliente não estiver instalado, a autenticação falha. 

Os seguintes passos guiá-lo a gerar um certificado de cliente de um certificado de raiz autoassinado. Poderá gerar vários certificados de cliente a partir do mesmo certificado de raiz. Ao gerar os certificados de cliente utilizando os passos abaixo, o certificado de cliente é automaticamente instalado no computador que utilizou para gerar o certificado. Se pretender instalar um certificado de cliente no outro computador cliente, pode exportar o certificado.
 
1. No mesmo computador que utilizou para criar o certificado autoassinado, abra uma linha de comandos como administrador.
2. Modificar e execute o exemplo para gerar um certificado de cliente.
  * Alteração *"P2SRootCert"* ao nome de raiz autoassinado que estão a gerar o certificado de cliente do. Certifique-se de que está a utilizar o nome do certificado de raiz, que é que o ' CN =' foi de valor que especificou quando criou a raiz autoassinada.
  * Alteração *P2SChildCert* para o nome que pretende gerar um certificado de cliente seja.

  Se executar o exemplo seguinte sem modificá-lo, o resultado será um certificado de cliente com o nome P2SChildcert no arquivo de certificados pessoais que foi gerado a partir do certificado de raiz P2SRootCert.

  ```cmd
  makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
  ```

### <a name="clientexport"></a>Exportar um certificado de cliente

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>Instalar um certificado de cliente exportado

Para instalar um certificado de cliente, consulte [instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Passos seguintes

Continue com a sua configuração de ponto a Site. 

* Para **Resource Manager** passos de modelo de implementação, consulte [configurar P2S através da autenticação do certificado Azure nativo](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Para **clássico** passos de modelo de implementação, consulte [configurar uma ligação VPN ponto a Site para uma VNet (clássica)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
