---
title: Integrar o DNS do Azure com os recursos do Azure | Microsoft Docs
description: Saiba como utilizar o DNS do Azure ao longo para fornecer o DNS para os seus recursos do Azure.
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: gwallace
ms.openlocfilehash: 41c1649bfff035bc641d7c1f5d7803cd105e8297
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Utilizar o DNS do Azure para fornecer definições de domínio personalizado para um serviço do Azure

O DNS do Azure fornece DNS para um domínio personalizado para qualquer um dos seus recursos do Azure que suporte de domínios personalizados ou que têm um nome de domínio completamente qualificado (FQDN). Um exemplo é tiver uma aplicação web do Azure e pretende que os utilizadores para aceder ao mesmo ao utilizar contoso.com ou www.contoso.com como um FQDN. Este artigo explica como configurar o serviço do Azure com o DNS do Azure para a utilização de domínios personalizados.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o DNS do Azure para o domínio personalizado, tem primeiro de delegar o domínio ao DNS do Azure. Visite [delegar um domínio ao DNS do Azure](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar os servidores de nome para delegação. Assim que o seu domínio é delegado à sua zona DNS do Azure, é possível configurar os registos DNS necessários.

Pode configurar um domínio personalizado para ou [aplicações de função do Azure](#azure-function-app), [Azure IoT](#azure-iot), [endereços IP públicos](#public-ip-address), [do serviço de aplicações (aplicações Web)](#app-service-web-apps), [Armazenamento de BLOBs](#blob-storage), e [CDN do Azure](#azure-cdn).

## <a name="azure-function-app"></a>Aplicação de função do Azure

Para configurar um domínio personalizado para aplicações de função do Azure, é criado um registo CNAME, bem como a configuração da aplicação de função em si.
 
Navegue para **outros** > **aplicação de função** e selecione a sua aplicação de função. Clique em **funcionalidades da plataforma** e, em **redes** clique **domínios personalizados**.

![Painel de aplicação de função](./media/dns-custom-domain/functionapp.png)

Tenha em atenção o atual url no **domínios personalizados** painel, este endereço é utilizado como o alias para o registo DNS criado.

![Painel de domínio personalizado](./media/dns-custom-domain/functionshostname.png)

Navegue até à sua zona DNS e clique em **+ conjunto de registos**. Preencha as informações seguintes sobre o **adicionar conjunto de registos** painel e clique em **OK** criá-la.

|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | myfunctionapp        | Este valor, juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Utilize um registo CNAME está a utilizar um alias.        |
|VALOR DE TTL     | 1        | 1 é utilizado para 1 hora        |
|Unidade TTL     | Horas        | Horas são utilizadas como a medição do tempo         |
|Alias     | adatumfunction.azurewebsites.NET        | O nome DNS está a criar o alias, neste exemplo é o nome DNS adatumfunction.azurewebsites.net fornecido por predefinição para a aplicação de função.        |

Navegue de volta para a sua aplicação de função, clique em **funcionalidades da plataforma**e, em **redes** clique **domínios personalizados**, em seguida, em **Hostnames**clique **+ adicionar hostname**.

No **adicionar hostname** painel, introduza o registo CNAME no **hostname** campo de texto e clique em **validar**. Se o registo foi possível encontrar o **adicionar hostname** botão aparece. Clique em **adicionar hostname** para adicionar o alias.

![Painel de nome de anfitrião de adicionar a função aplicações](./media/dns-custom-domain/functionaddhostname.png)

## <a name="azure-iot"></a>IoT do Azure

IoT do Azure não tem quaisquer personalizações que são necessárias no próprio serviço. Para utilizar um domínio personalizado com um IoT Hub apenas um registo CNAME indicado para os recursos é necessárias.

Navegue para **Internet das coisas** > **IoT Hub** e selecione o seu IoT hub. No **descrição geral** painel, tenha em atenção o FQDN do IoT hub.

![Painel do hub IoT](./media/dns-custom-domain/iot.png)

Em seguida, navegue até à sua zona DNS e clique em **+ conjunto de registos**. Preencha as informações seguintes sobre o **adicionar conjunto de registos** painel e clique em **OK** criá-la.


|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | myiothub        | Este valor, juntamente com a etiqueta de nome de domínio é o FQDN para o IoT hub.        |
|Tipo     | CNAME        | Utilize um registo CNAME está a utilizar um alias.
|VALOR DE TTL     | 1        | 1 é utilizado para 1 hora        |
|Unidade TTL     | Horas        | Horas são utilizadas como a medição do tempo         |
|Alias     | adatumIOT.azure devices.net        | O nome DNS está a criar o alias, neste exemplo é o nome de anfitrião adatumIOT.azure devices.net fornecido pelo IoT hub.

Assim que o registo é criado, testar a resolução de nome a utilizar o registo CNAME`nslookup`

## <a name="public-ip-address"></a>Endereço IP público

Para configurar um domínio personalizado para serviços que utilizam um IP público de recursos, tais como o Gateway de aplicação, o Balanceador de carga, o serviço em nuvem, o VMs do Gestor de recursos de endereços e, registar VMs clássicas, um CNAME utilizado.

Navegue para **redes** > **endereço IP público**, selecione o recurso de IP público e clique em **configuração**. Notate o endereço IP mostrado.

![painel do ip público](./media/dns-custom-domain/publicip.png)

Navegue até à sua zona DNS e clique em **+ conjunto de registos**. Preencha as informações seguintes sobre o **adicionar conjunto de registos** painel e clique em **OK** criá-la.


|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | mywebserver        | Este valor, juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | A        | Utilize um registo de recursos é um endereço IP.        |
|VALOR DE TTL     | 1        | 1 é utilizado para 1 hora        |
|Unidade TTL     | Horas        | Horas são utilizadas como a medição do tempo         |
|Endereço IP     | <your ip address>       | O endereço IP público.|

![criar um registo](./media/dns-custom-domain/arecord.png)

Depois do registo é criado, executar `nslookup` para validar o registo resolve.

![pesquisa de dns do ip público](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>Serviço de aplicações (aplicações Web)

Os seguintes passos orienta-o por configurar um domínio personalizado para uma aplicação de web do serviço de aplicações.

Navegue para **Web & Mobile** > **do serviço de aplicações** e selecione o recurso que está a configurar um nome de domínio personalizado e clique em **domínios personalizados**.

Tenha em atenção o atual url no **domínios personalizados** painel, este endereço é utilizado como o alias para o registo DNS criado.

![Painel de domínios personalizados](./media/dns-custom-domain/url.png)

Navegue até à sua zona DNS e clique em **+ conjunto de registos**. Preencha as informações seguintes sobre o **adicionar conjunto de registos** painel e clique em **OK** criá-la.


|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | mywebserver        | Este valor, juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Utilize um registo CNAME está a utilizar um alias. Se o recurso utilizado um endereço IP, seria possível utilizar um registo.        |
|VALOR DE TTL     | 1        | 1 é utilizado para 1 hora        |
|Unidade TTL     | Horas        | Horas são utilizadas como a medição do tempo         |
|Alias     | WebServer.azurewebsites.NET        | O nome DNS está a criar o alias, neste exemplo é o nome DNS webserver.azurewebsites.net fornecido por predefinição para a aplicação web.        |


![Crie um registo CNAME](./media/dns-custom-domain/createcnamerecord.png)

Navegue de volta para o serviço de aplicação que está configurado para o nome de domínio personalizado. Clique em **domínios personalizados**, em seguida, clique em **Hostnames**. Para adicionar o registo CNAME que criou, clique em **+ adicionar hostname**.

![figura 1](./media/dns-custom-domain/figure1.png)

Depois do processo estiver concluído, execute **nslookup** para validar a resolução de nomes está a funcionar.

![figura 1](./media/dns-custom-domain/finalnslookup.png)

Para mais informações sobre o mapeamento de um domínio personalizado para o serviço de aplicações, visite [mapear um nome DNS personalizado existente para Web Apps do Azure](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Se precisar de comprar um domínio personalizado, visite [comprar um nome de domínio personalizado para Web Apps do Azure](../app-service/custom-dns-web-site-buydomains-web-app.md) para obter mais informações sobre domínios de serviço de aplicações.

## <a name="blob-storage"></a>Armazenamento de blobs

Os seguintes passos orienta-o por configurar um registo CNAME para uma conta do blob storage utilizando o método asverify. Este método garante que não há nenhum período de indisponibilidade.

Navegue para **armazenamento** > **contas do Storage**, selecione a sua conta de armazenamento e, em **domínio personalizado**. Notate o FQDN no passo 2, este valor é utilizado para criar o primeiro registo CNAME

![domínio personalizado de armazenamento de BLOBs](./media/dns-custom-domain/blobcustomdomain.png)

Navegue até à sua zona DNS e clique em **+ conjunto de registos**. Preencha as informações seguintes sobre o **adicionar conjunto de registos** painel e clique em **OK** criá-la.


|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | asverify.mystorageaccount        | Este valor, juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Utilize um registo CNAME está a utilizar um alias.        |
|VALOR DE TTL     | 1        | 1 é utilizado para 1 hora        |
|Unidade TTL     | Horas        | Horas são utilizadas como a medição do tempo         |
|Alias     | asverify.adatumfunctiona9ed.blob.Core.Windows.NET        | O nome DNS está a criar o alias, neste exemplo é o nome DNS asverify.adatumfunctiona9ed.blob.core.windows.net fornecido por predefinição para a conta de armazenamento.        |

Navegue de volta para a sua conta do storage, clicando em **armazenamento** > **contas do Storage**, selecione a sua conta de armazenamento e clique em **domínio personalizado**. Escreva o alias criado sem o prefixo de asverify na caixa de texto, verificação * * Utilizar validação de CNAME indireta e clique em **guardar**. Depois deste passo é concluído, voltar à sua zona DNS e crie um registo CNAME sem o prefixo de asverify.  Depois de que o ponto, são seguros eliminar o registo CNAME com o prefixo de cdnverify.

![domínio personalizado de armazenamento de BLOBs](./media/dns-custom-domain/indirectvalidate.png)

Validar a resolução de DNS ao executar`nslookup`

Para saber mais sobre o mapeamento de um domínio personalizado para um ponto final do blob storage visitam [configurar um nome de domínio personalizado para o ponto de final do Blob storage](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>CDN do Azure

Os seguintes passos orienta-o por um registo CNAME para um ponto final de CDN utilizando o método cdnverify a configurar. Este método garante que não há nenhum período de indisponibilidade.

Navegue para **redes** > **perfis da CDN**, selecione o perfil de CDN e clique em **pontos finais** em **geral**.

Selecione o ponto final que está a trabalhar e clique em **+ domínio personalizado**. Tenha em atenção o **nome de anfitrião de ponto final** como este valor é o registo de que o registo CNAME aponta para.

![Domínio personalizado de CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Navegue até à sua zona DNS e clique em **+ conjunto de registos**. Preencha as informações seguintes sobre o **adicionar conjunto de registos** painel e clique em **OK** criá-la.

|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | cdnverify.mycdnendpoint        | Este valor, juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Utilize um registo CNAME está a utilizar um alias.        |
|VALOR DE TTL     | 1        | 1 é utilizado para 1 hora        |
|Unidade TTL     | Horas        | Horas são utilizadas como a medição do tempo         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.NET        | O nome DNS está a criar o alias, neste exemplo é o nome DNS cdnverify.adatumcdnendpoint.azureedge.net fornecido por predefinição para a conta de armazenamento.        |

Navegue de volta para o ponto final de CDN clicando **redes** > **perfis da CDN**e selecione o perfil de CDN. Clique em **+ domínio personalizado** e introduza o seu alias de registo CNAME sem o prefixo de cdnverify e clique em **adicionar**.

Depois deste passo é concluído, voltar à sua zona DNS e crie um registo CNAME sem o prefixo de cdnverify.  Depois de que o ponto, são seguros eliminar o registo CNAME com o prefixo de cdnverify. Para obter mais informações sobre a CDN e como configurar um domínio personalizado sem o passo de registo intermédio visite [conteúdo da CDN do Azure de mapeamento a um domínio personalizado](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar inversa de DNS para serviços alojados no Azure](dns-reverse-dns-for-azure-services.md).