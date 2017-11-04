---
title: Interfaces de hardware para StorSimple 10 GbE | Microsoft Docs
description: "Descreve os transcetores (SFP) incorporável suportados fator de forma pequeno, cabos e comutadores para as interfaces de rede 10 GbE no dispositivo StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: db03b3cd668bf8e35913872ac4225de6d4d3edd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Hardware suportado para as interfaces de rede GbE 10 no seu dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Este artigo fornece informações sobre o hardware suplementares que funciona com o seu dispositivo do Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista de dispositivos testado pela Microsoft
Microsoft foi testado as seguintes transcetores (SFP) incorporável fator de forma pequeno, cabos e comutadores para se certificar de que forma ideal se funciona com dispositivos. (As tabelas seguintes serão atualizadas como novo hardware é testado.)

### <a name="sfp-transceivers"></a>SFP + Transcetores
| Certifique- | Modelo |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Cabos
| S. Não. | Certifique- | Modelo |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp Lite |N820 - 05M (OM3) |

### <a name="switches"></a>Comutadores
| S. Não. | Certifique- | Modelo |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lista de dispositivos testado no campo
Esta secção contém a lista de dispositivos que foram implementados com êxito no campo pelos clientes do StorSimple. Estes não foram testados pela Microsoft, mas são provável que funcione com o dispositivo StorSimple.

| Parâmetro | Valor |
| --- | --- |
| Certifique-comutador |Juniper |
| Modelo de comutador |ex4550 32F |
| Versão do sistema operativo de comutador |JunOS 12.3R9.4 |
| Modelo de painel |Portas carregar (PIC 0) |
| Disponibilizar TRANSCEIVER |Juniper |
| Modelo de TRANSCEIVER |Número de peça 740 021308 <br></br> Número de peça 740 030658 |
| Versão de firmware TRANSCEIVER |Rev versão de 01 0,0 (comunicadas) |
| Modelo de cabo |Duplex jumper LC/LC 50/125µ, OM3, LSZH |
| Modelo do StorSimple |8600 |
| Versão de software do StorSimple |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista de dispositivos testado pelo fornecedor OEM (Mellanox)
Mellanox foi testado a seguintes transcetores (SFP) incorporável fator de forma pequeno, cabos e comutadores para se certificar de que forma ideal se funcionar com Mellanox interfaces de rede, tais como as interfaces de rede GbE 10 no seu dispositivo StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Cabos e de módulos suportados pelo Mellanox
A tabela seguinte lista os cabos e de módulos suportados pelo Mellanox. Estes não foram testados pela Microsoft, mas são provável que funcione com o dispositivo StorSimple.

| S. Não. | Velocidade | Modelo | Descrição | Certifique- |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |FICHEIRO CAB-SFP-SFP - 1M |cabo copper passivo SFP + 10 Gb/s 1M |Arista |
| 2. |10 GbE |FICHEIRO CAB-SFP-SFP - 2M |cabo copper passivo SFP + 10 Gb/s milhão 2 |Arista |
| 3. |10 GbE |FICHEIRO CAB-SFP-SFP - 3M |cabo copper passivo SFP + 10 Gb/s 3m |Arista |
| 4. |10 GbE |FICHEIRO CAB-SFP-SFP - 5M |cabo copper passivo SFP + 10 Gb/s milhão 5 |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |SFP + cabo do Cisco |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |SFP + cabo do Cisco |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |SFP + cabo do Cisco |Cisco |
| 8. |10 GbE |J9281B HP X242 10 G |SFP + para SFP + 1 milhão direta anexar Copper cabo |HP |
| 9. |10 GbE |455883 B21 HP BLc |10Gb SR SFP + optar ativamente por participar |HP |
| 10. |10 GbE |455886 B21 HP BLc |10Gb LR SFP + optar ativamente por participar |HP |
| 11. |10 GbE |487649 B21 HP BLc |Copper de 10 gbe 0.5m SFP + cabo |HP |
| 12. |10 GbE |487652 B21 HP BLc |Copper de 10 gbe 1 milhão SFP + cabo |HP |
| 13. |10 GbE |487655 B21 HP BLc |Copper de 10 gbe 3m SFP + cabo |HP |
| 14. |10 GbE |487658 B21 HP BLc |SFP + 7m 10 gbe Copper cabo |HP |
| 15. |10 GbE |537963 B21 HP BLc |SFP + 5m 10 gbe Copper cabo |HP |
| 16. |10 GbE |AP784A HP |3m C-série passivo Copper SFP + cabo |HP |
| 17. |10 GbE |AP785A HP |5m C-série passivo Copper SFP + cabo |HP |
| 18. |10 GbE |AP818A HP |1 milhão B-série SFP Active Directory Copper + cabo |HP |
| 19. |10 GbE |AP819A HP |3m B-série SFP Copper Active Directory + cabo |HP |
| 20. |10 GbE |J9150A HP |X132 10 G SFP + LC SR Transceiver |HP |
| 21. |10 GbE |J9151A HP |X132 10 G SFP + LC LR Transceiver |HP |
| 22. |10 GbE |J9283B HP |X242 10 G SFP + SFP + 3 m DAC cabo |HP |
| 23. |10 GbE |J9285B HP |X242 10 G SFP + SFP + 7 m DAC cabo |HP |
| 24. |10 GbE |JD095B HP |X240 10 G SFP + SFP + 0.65 m DAC cabo |HP |
| 25. |10 GbE |JD096B HP |X240 10 G SFP + SFP + 1.2 m DAC cabo |HP |
| 26. |10 GbE |JD097B HP |X240 10 G SFP + SFP + 3 m DAD cabo |HP |
| 27. |10 GbE |MAM1Q00A QSA Mellanox |QSFP SFP + adaptador |Tecnologias de Mellanox |
| 28. |10 GbE |MC2309124 006 Mt |Cabo Copper passivo 1 x SFP+ para QSFP 10 Gb/s 24awg milhão 7 |Tecnologias de Mellanox |
| 29. |10 GbE |MC2309124 007 Mt |Cabo Copper passivo 1 x SFP+ para QSFP 10 Gb/s 24awg milhão 7 |Tecnologias de Mellanox |
| 30. |10 GbE |MC2309130 003 Mt |Cabo Copper passivo 1 x SFP+ para QSFP 10 Gb/s 30awg 3 m |Tecnologias de Mellanox |
| 31. |10 GbE |MC2309130 00A Mt |Cabo Copper passivo 1 x SFP+ para QSFP 10 Gb/s 30awg 0.5 m |Tecnologias de Mellanox |
| 32. |10 GbE |MC3309124 005 Mt |Copper passivo instalar 1 24awg de 10 Gb/s x SFP+ milhão 5 |Tecnologias de Mellanox |
| 33. |10 GbE |MC3309124 007 Mt |Copper passivo instalar 1 24awg de 10 Gb/s x SFP+ milhão 7 |Tecnologias de Mellanox |
| 34. |10 GbE |MC3309130 003 Mt |Copper passivo instalar 1 30awg de 10 Gb/s x SFP+ 3 m |Tecnologias de Mellanox |
| 35. |10 GbE |MC3309130 00A Mt |Copper passivo instalar 1 30awg de 10 Gb/s x SFP+ 0.5 m |Tecnologias de Mellanox |

### <a name="switches-supported-by-mellanox"></a>Comutadores suportados pelo Mellanox
A tabela seguinte lista os comutadores suportados pelo Mellanox. Estes não foram testados pela Microsoft, mas são provável que funcione com o dispositivo StorSimple.

| S. Não. | Velocidade | Modelo | Descrição | Certifique- |
| --- | --- | --- | --- | --- |
| 1. |10 gbe |516733 B21 |HP ProCurve 6120XG 10 gbe Ethernet painel comutador |HP |
| 2. |10 gbe |538113 B21 |Módulo de pass-through de 10 gbe HP (PTM) |HP |
| 3. |10 gbe |EN4093 |IBM PureFlex sistema recursos de infraestrutura EN4093 de 10 Gigabit comutador dimensionável módulo |IBM |
| 4. |1GbE |3020 |Painel de comutador Cisco Catalyst 3020 1GbE |Cisco |
| 5. |1GbE |3020 X |Painel de comutador Cisco Catalyst 3020 X 1GbE |Cisco |
| 6. |1GbE |438030 B21 |Módulo de comutador de 1GbE HP - GbE2c camada 2/3 Ethernet painel comutador |HP |
| 7. |1GbE |6120G |Painel de comutador de 1GbE/XG de 6120G ProCurve HP |HP |

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre os componentes de hardware do StorSimple e estado](storsimple-monitor-hardware-status.md).

