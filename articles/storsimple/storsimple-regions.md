---
title: "Disponibilidade de região StorSimple | Microsoft Docs"
description: "Explica as regiões do Azure na qual estão disponíveis vários modelos de dispositivos do StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: d47109d541a3df93d9234e27e53d1538f6bc4c6e
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="available-regions-for-your-storsimple"></a>Regiões disponíveis para o StorSimple

## <a name="overview"></a>Descrição geral

Os centros de dados do Azure funcionam em várias localizações geográficas em todo o mundo para satisfazer os pedidos de cliente de desempenho, requisitos e preferências de localização de dados. Uma geografia do Azure é uma área definida o mundo que contenha pelo menos uma região do Azure. Uma região do Azure é uma área dentro de uma geografia, que contém um ou mais dos centros de dados.

Escolher uma região do Azure é muito importante e a escolha da região é deve influenciada pelos fatores, tais como residency de dados e soberania dos, disponibilidade de serviço, desempenho, custo e redundância. Para obter mais informações sobre como escolher uma região, aceda a [região do Azure que é mais adequada para mim?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

Para a solução StorSimple, a escolha da região especificamente é determinada pelos seguintes fatores:

- Regiões onde o serviço do Gestor de dispositivos do StorSimple está disponível.
- Países/regiões onde o físico StorSimple, uma nuvem ou um dispositivo virtual está disponível.
- As regiões onde as contas de armazenamento que armazenam dados StorSimple devem estar localizadas para um desempenho ideal.

Este tutorial descreve a disponibilidade de região para o serviço do Gestor de dispositivos do StorSimple, físicos no local e os dispositivos de nuvem. As informações contidas neste artigo são aplicáveis a dispositivos de série de 1200 e 8000 do StorSimple.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Disponibilidade de região para o serviço do Gestor de dispositivos do StorSimple

Serviço do Gestor de dispositivos do StorSimple é atualmente suportado em 12 regiões públicas e 2 regiões do Azure Government.

Defina uma região ou localização quando cria pela primeira vez o serviço do Gestor de dispositivos do StorSimple. Em geral, é escolhida uma localização mais próxima da região geográfica onde o dispositivo está implementado. Mas o dispositivo e o serviço também podem ser implementados em localizações diferentes.

Aqui está uma lista de regiões onde o serviço do Gestor de dispositivos do StorSimple está disponível para a nuvem pública do Azure e pode ser implementado.

![storsimple-dispositivo-manager-service-regiões](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Para a nuvem do Azure Government, o serviço do Gestor de dispositivos do StorSimple está disponível no Iowa us-nos e Virginia us-nos centros de dados.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Disponibilidade de região de dados armazenados no StorSimple

Dados do StorSimple fisicamente são armazenados em contas do storage do Azure e estas contas estão disponíveis em todas as regiões do Azure. Quando cria uma conta de armazenamento do Azure, a localização primária da conta de armazenamento é escolhida e que determina a região onde residem os dados.

Ao primeiro criar um serviço do Gestor de dispositivos do StorSimple e associar uma conta de armazenamento com o mesmo, o serviço StorSimple Manager de dispositivo e o armazenamento do Azure podem ser em duas localizações separadas. Se for esse o caso, terá de criar separadamente a conta do Gestor de Dispositivos do StorSimple e a conta de armazenamento do Azure.

Em geral, escolha a região mais próximo para o seu serviço para a sua conta de armazenamento. No entanto, a região mais próximo do Microsoft Azure não poderão, na verdade, a região com a latência mais baixa. É a latência que determinam o desempenho do serviço de rede e, por conseguinte, o desempenho da solução. Por isso, se escolher uma conta de armazenamento numa região diferente, é importante saber quais são as latências entre o serviço e a região associada à sua conta do storage.

Se estiver a utilizar uma aplicação de nuvem do StorSimple, em seguida, recomendamos que o serviço e a conta do storage associada estão na mesma região. As contas de armazenamento numa região diferente poderão resultar num fraco desempenho.

## <a name="availability-of-storsimple-device"></a>Disponibilidade do dispositivo StorSimple

Consoante o modelo, os dispositivos StorSimple podem estar disponíveis em diferentes localizações geográficas ou países.

### <a name="storsimple-physical-device-models-81008600"></a>Dispositivo físico StorSimple (modelos 8100/8600)

Se utilizar um 8100 StorSimple ou 8600 dispositivo físico, o dispositivo está disponível na seguintes países/regiões.

| #  | País        | #  | País     | #  | País      | #  | País              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | Austrália      | 16 | RAE de Hong Kong   | 31 | Nova Zelândia  | 46 | África do Sul         |
| 2  | Áustria        | 17 | Hungria     | 32 | Nigéria      | 47 | Coreia do Sul          |
| 3  | Bahrain        | 18 | Islândia     | 33 | Noruega       | 48 | Espanha                |
| 4  | Bélgica        | 19 | Índia       | 34 | Peru         | 49 | Sri Lanka            |
| 5  | Brasil         | 20 | Indonésia   | 35 | Filipinas  | 50 | Suécia               |
| 6  | Canadá         | 21 | Irlanda     | 36 | Polónia       | 51 | Suíça          |
| 7  | Chile          | 22 | Israel      | 37 | Portugal     | 52 | Taiwan               |
| 8  | Colômbia       | 23 | Itália       | 38 | Porto Rico  | 53 | Tailândia             |
| 9  | República Checa | 24 | Japão       | 39 | Catar        | 54 | Turquia               |
| 10 | Dinamarca        | 25 | Quénia       | 40 | Roménia      | 55 | Ucrânia              |
| 11 | Egito          | 26 | Kuwait      | 41 | Rússia       | 56 | Emirados Árabes Unidos |
| 12 | Finlândia        | 27 | Macau       | 42 | Arábia Saudita | 57 | Reino Unido       |
| 13 | França         | 28 | Malásia    | 43 | Singapura    | 58 | Estados Unidos        |
| 14 | Alemanha        | 29 | México      | 44 | Eslováquia     | 59 | Vietname              |
| 15 | Grécia         | 30 | Países Baixos | 45 | Eslovénia     | 60 | Croácia              |

Esta lista é alterado à medida que são adicionadas mais países. Para obter uma lista mais atualizadas à sua das localizações geográficas, vá para o anexo de termos de matriz de armazenamento na [termos de produto](https://www.microsoft.com/en-us/Licensing/product-licensing).

A Microsoft pode são enviados hardware físico e fornecer a substituição de peças sobressalentes de hardware para StorSimple para as localizações geográficas na lista anterior.

> [!IMPORTANT]
> Não coloque um dispositivo físico StorSimple numa região onde StorSimple não é suportado. Microsoft não será capaz de enviar quaisquer partes de substituição para países/regiões onde o StorSimple não é suportado.

### <a name="storsimple-cloud-appliance-models-80108020"></a>Aplicação de nuvem do StorSimple (modelos 8010/8020)

Se utilizar um 8010 de aplicação de nuvem do StorSimple ou 8020, em seguida, o dispositivo é suportada e disponível em todas as regiões onde o VM subjacente é suportada. O 8010 utiliza um _Standard_A3_ VM que é suportado em todas as regiões do Azure.

O 8020 utiliza o premium storage e _Standard_DS3_ VM para criar uma aplicação de nuvem. O 8020 é suportado em regiões do Azure que suportam o Premium Storage e _Standard_DS3_ VMs do Azure. Utilize [esta lista](https://azure.microsoft.com/regions/services/) para ver se ambas as **Máquinas Virtuais > série DS** e o **Armazenamento > Armazenamento em Disco** estão disponíveis na sua região.

### <a name="storsimple-virtual-array-model-1200"></a>Matriz de Virtual StorSimple (modelo 1200)

Se utilizar uma série de 1200 matriz Virtual StorSimple, a imagem de disco virtual é suportada em todas as regiões do Azure.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [preços para vários modelos StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Saiba mais sobre [gerir a conta de armazenamento StorSimple](storsimple-8000-manage-storage-accounts.md).
* Saiba mais sobre como [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
