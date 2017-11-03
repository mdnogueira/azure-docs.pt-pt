---
title: "Aprovisionar um dispositivo com o serviço de aprovisionamento de dispositivos do Azure IoT Hub | Microsoft Docs"
description: "Aprovisionar o seu dispositivo para um único IoT hub com o serviço de aprovisionamento de dispositivos do Azure IoT Hub"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: bf50699d2dc67294d554ba15713254a8b88d8ade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Aprovisionar o dispositivo a um IoT hub, utilizando o serviço de aprovisionamento de dispositivos do Azure IoT Hub

O tutorial anterior, aprendeu a configurar um dispositivo para ligar ao seu serviço de aprovisionamento de dispositivos. Neste tutorial, irá aprender a utilizar este serviço para aprovisionar o seu dispositivo ao IoT hub único, utilizando  **_listas de inscrição_**. Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Inscrever o dispositivo
> * Iniciar o dispositivo
> * Certifique-se de que o dispositivo está registado

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, certifique-se configurar o seu dispositivo e a respetiva *módulo de Hardware de segurança* tal como explicado no tutorial [configurar um dispositivo para aprovisionar a utilizar o serviço de aprovisionamento de dispositivos do Azure IoT Hub](./tutorial-set-up-device.md).


<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Inscrever o dispositivo

Este passo envolve a adição de artefactos de segurança exclusiva do dispositivo para o serviço de aprovisionamento de dispositivos. Destes artefactos de segurança são os seguintes:

- Para dispositivos baseados em TPM:
    - O *chave de endossamento* que seja exclusivo para cada TPM chip ou simulação. Leia o [compreender chave de endossamento TPM](https://technet.microsoft.com/library/cc770443.aspx) para obter mais informações.
    - O *ID de registo* que é utilizado para identificar exclusivamente um dispositivo no espaço de nomes/âmbito. Esta pode ou não pode ser o mesmo que o ID de dispositivo. O ID é obrigatório para cada dispositivo. Para dispositivos baseados em TPM, o ID de registo pode ser derivado da TPM, por exemplo, um hash SHA-256 da chave de endossamento TPM.

    ![Informações de inscrição para TPM no portal](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)

- Para dispositivos de x. 509 com base em:
    - O [certificado emitido para o x. 509](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) chip ou simulação, na forma de um *. pem* ou um *. cer* ficheiro. Para a inscrição individuais, tem de utilizar o *certificado signatário* para o seu sistema de x. 509, enquanto para os grupos de inscrição, tem de utilizar o *certificado de raiz*.

    ![Informações de inscrição para os x. 509 no portal](./media/tutorial-provision-device-to-hub/x509-device-enrollment.png)


Existem duas formas de inscrever o dispositivo para o serviço de aprovisionamento de dispositivos:

- **Grupos de inscrição** representa um grupo de dispositivos que partilham um mecanismo de atestado específico. Recomendamos que utilize um grupo de inscrição de um grande número de dispositivos, que partilham uma configuração desejada inicial, ou para dispositivos que todos os mesmo inquilino.

    ![Grupos de inscrição para x. 509 no portal](./media/tutorial-provision-device-to-hub/x509-enrollment-groups.png)

- **As inscrições individuais** representa uma entrada para um único dispositivo pode registar com o serviço de aprovisionamento de dispositivos. As inscrições individuais podem utilizar qualquer um dos x509 certificados ou SAS tokens (por um TPM real ou virtual) como mecanismos de atestado. Recomendamos que utilize inscrições individuais para os dispositivos que requerem configurações iniciais exclusivas, ou para dispositivos que só podem utilizar os tokens SAS através do TPM ou virtual TPM como o mecanismo de atestado. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

Seguem-se os passos para inscrever o dispositivo no portal:

1. Tenha em atenção os artefactos de segurança para o HSM no seu dispositivo. Poderá ter de utilizar as APIs mencionadas na secção intitulada [extrair artefactos de segurança](./tutorial-set-up-device.md#extractsecurity) do tutorial anterior, num ambiente de desenvolvimento.

1. Inicie sessão no portal do Azure, clique no botão **Todos os recursos**, no menu do lado esquerdo, e abra o Serviço Aprovisionamento de Dispositivos.

1. No painel de resumo do Serviço Aprovisionamento de Dispositivos, selecione **Gerir inscrições**. Selecione **inscrições individuais** separador ou **inscrição grupos** separador de acordo com a configuração do dispositivo. Clique em de **adicionar** botão na parte superior. Selecione **TPM** ou **x. 509** como o atestado de identidade *mecanismo*e introduza os artefactos de segurança adequados, tal como explicado anteriormente. Pode introduzir um novo **ID do dispositivo IoT Hub**. Quando tiver terminado, clique no botão **Guardar**. 

1. Quando o dispositivo é inscrito com êxito, deve ver apresentá-la no portal da seguinte forma:

    ![Inscrição concluída com êxito do TPM no portal](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)


## <a name="start-the-device"></a>Iniciar o dispositivo

Neste momento, o programa de configuração seguinte está preparado para o registo de dispositivos:

1. O dispositivo ou grupo de dispositivos são inscritos ao seu serviço de aprovisionamento de dispositivos, e 
2. O dispositivo está pronto com o chip HSM configurado e acessível através da aplicação utilizando o SDK do cliente do serviço de aprovisionamento de dispositivos.

Inicie o dispositivo para permitir que a aplicação de cliente iniciar o registo com o serviço de aprovisionamento de dispositivos.  


## <a name="verify-the-device-is-registered"></a>Certifique-se de que o dispositivo está registado

Uma vez arranques seu dispositivo, as seguintes ações que devem ter lugar. Consulte a aplicação de exemplo do simulador TPM [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c) para obter mais detalhes. 

1. O dispositivo envia um pedido de registo ao seu serviço de aprovisionamento de dispositivos.
2. Para dispositivos do TPM, o serviço de aprovisionamento de dispositivos envia um desafio de registo para o qual o dispositivo responde. 
3. No registo com êxito, o serviço de aprovisionamento de dispositivos envia que o URI do IoT hub, ID de dispositivo e a chave encriptada de volta para o dispositivo. 
4. A IoT Hub aplicação cliente no dispositivo, em seguida, liga ao seu hub. 
5. Em ligação com êxito para o hub, deverá ver o dispositivo aparecem do hub IoT **Explorador de dispositivo**. 

    ![Ligação com êxito ao hub no portal](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Inscrever o dispositivo
> * Iniciar o dispositivo
> * Certifique-se de que o dispositivo está registado

Avançar para o próximo tutorial para saber como aprovisionar vários dispositivos em hubs com balanceamento de carga. 

> [!div class="nextstepaction"]
> [Aprovisionar dispositivos entre os hubs IoT do balanceamento de carga](./tutorial-provision-multiple-hubs.md)
