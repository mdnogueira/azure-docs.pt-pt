---
title: Adicionar um certificado para o arquivo de ACs de Java | Microsoft Docs
description: "Saiba como adicionar um certificado de autoridade (AC) de certificado para o arquivo de certificados (cacerts) de AC de Java Twilio para ou de serviço do Azure Service Bus."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: b6e1a305e19415ab1c4b4c208dac98ad1e2689c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Adicionar um certificado para o arquivo de certificados de AC de Java
Os passos seguintes mostram como adicionar um certificado de autoridade (AC) de certificado para o arquivo de certificados (cacerts) de AC de Java. O exemplo utilizado é para o certificado de AC necessário para o serviço do Twilio. As informações fornecidas mais tarde no tópico descrevem como instalar o certificado de AC para o Service Bus do Azure. 

Pode utilizar keytool para adicionar o certificado de AC antes de zipping o JDK e adicioná-lo ao seu projeto do Azure **approot** pasta, ou foi possível executar uma tarefa de arranque do Azure que utiliza keytool para adicionar o certificado. Este exemplo assume que irá adicionar um certificado de AC antes do JDK que está a ser comprimido. Além disso, será utilizado um certificado de AC específico no exemplo, mas os passos para obter um certificado de AC diferente e importá-la para o arquivo de cacerts seriam semelhantes.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Para adicionar um certificado para o arquivo de cacerts
1. Numa linha de comandos de administrador que está definido como o JDK **jdk\jre\lib\security** pasta, execute o seguinte para ver os certificados estão instalados:
   
    `keytool -list -keystore cacerts`
   
    Será solicitada a palavra-passe do arquivo. A palavra-passe predefinida é **changeit**. (Se pretender alterar a palavra-passe, consulte a documentação de keytool em <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.) Este exemplo assume que o certificado com o MD5 identificação digital 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 não está listado e de que pretende importar (este certificado específico é necessária para o serviço de API do Twilio).
2. Obter o certificado da lista de certificados listados em [certificados de raiz GeoTrust](http://www.geotrust.com/resources/root-certificates/). Faça duplo clique na ligação para o certificado com o número de série 35:DE:F4:CF e guardá-lo para o **jdk\jre\lib\security** pasta. Para efeitos deste exemplo, foi guardada para um ficheiro denominado **Equifax\_seguro\_certificado\_Authority.cer**.
3. Importe o certificado através do seguinte comando:
   
    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`
   
    Quando lhe for pedido para confiar este certificado, se o certificado possui MD5 identificação digital 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, responder, escrevendo **y**.
4. Execute o seguinte comando para garantir que o certificado da AC foi importado com êxito:
   
    `keytool -list -keystore cacerts`
5. Zip o JDK e adicioná-la ao seu projeto do Azure **approot** pasta.

Para obter informações sobre keytool, consulte <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Certificados de raiz do Azure
As aplicações que utilizam os serviços do Azure (por exemplo, o Service Bus do Azure) tem de confiar no certificado de Root da CyberTrust Baltimore. (A partir do dia 15 de Abril de 2013, Azure começou a migrar GTE Root da CyberTrust Global raiz para a fase de Root da CyberTrust Baltimore. Esta migração demorou vários meses para concluir.)

Baltimore certificado poderá já estar instalado no arquivo de cacerts, lembre-se, por isso executar o **keytool-lista** comando pela primeira vez para ver se já existe.

Se precisar de adicionar a entrada de Root da CyberTrust Baltimore, tem o número de série 02:00:00:b9 e SHA1 identificação digital d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Pode ser transferido a partir de <https://cacert.omniroot.com/bc2025.crt>, guardadas num ficheiro local com a extensão **. cer**e, em seguida, importá-lo utilizando **keytool** conforme mostrado acima.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os certificados de raiz utilizado pelo Azure, consulte [migração de certificados de raiz do Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Para obter mais informações sobre o Java, consulte [do Azure para programadores de Java](/java/azure).

