---
title: "As especificações técnicas do StorSimple | Microsoft Docs"
description: "Descreve as informações de conformidade de regulamentação normas para os componentes de hardware do StorSimple e as especificações técnicas."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 17ebf6b3-0872-4332-ac6e-074cc20a2b8e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: ac1f6fbd40770374f68d0d280fc1cc040e41b1ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>As especificações técnicas e compatibilidade do dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Os componentes de hardware do dispositivo Microsoft Azure StorSimple cumprem as normas de regulamentação descritas neste artigo e as especificações técnicas. As especificações técnicas descrevem a energia e arrefecimento módulos (PCMs), unidades de disco, capacidade de armazenamento e inclusões. As informações de compatibilidade abrange essas coisas como as normas internacionais, segurança e emissões nos e cablagem.

## <a name="power-and-cooling-module-specifications"></a>Especificações de energia e arrefecimento módulo
O dispositivo StorSimple tem dois 100 240V ventoinha dupla, em conformidade SBB energia arrefecimento módulos (PCMs). Isto fornece uma configuração de alimentação redundantes. Se um PCM falhar, o dispositivo continua a funcionar normalmente a outro PCM até que o módulo de falha é substituído.  

A inclusão EBOD utiliza um PCM W 580 e inclusão primário utiliza um PCM W 764. As tabelas seguintes listam as especificações técnicas associadas as PCMs.

| Especificação | 580 W PCM (EBOD) | 764 W PCM (principal) |
| --- | --- | --- |
| Power de saída máximo |580 W |764 |
| Frequência |50/60 Hz |50/60 Hz |
| Seleção de intervalo de tensão |Vão automática: 90 – 264 V AC, 47/63 Hz |Vão automática: 90-264 V AC, 47/63 Hz |
| Inrush máximo atual |20 A |20 A |
| Correção de fator de energia |> 95% nominal tensão de entrada |> 95% nominal tensão de entrada |
| Harmonics |Cumpre EN61000-3-2 |Cumpre EN61000-3-2 |
| Saída |Tensão de modo de espera de 5 v @ 2.0 A |Tensão de modo de espera de 5 v @ 2.7 A |
| + 5 V @ 42 A |+ 5 V @ 40 A | |
| + 12V @ 38 A |+ 12V @ 38 A | |
| Acesso frequente incorporável |Sim |Sim |
| Comutadores e LEDs |Comutador de caixa de AC e o indicador de estado de quatro LEDs |Comutador de caixa de AC e o indicador de estado de seis LEDs |
| Inclusão de arrefecimento |Axial arrefecimento ventoinhas com controlo de velocidade de variável ventoinha |Axial arrefecimento ventoinhas com controlo de velocidade de variável ventoinha |

## <a name="power-consumption-statistics"></a>Estatísticas de consumo de energia
A tabela seguinte lista os dados de consumo de energia típico (valores reais poderão variar publicado) para vários modelos do dispositivo StorSimple. 

| Condições | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Unidades lentas, ventoinhas Inativas |1.45 A |0.31 kW |1057.76 BTU/hr |3.19 A |0.34 kW |1160.13 BTU/hr |
|  Unidades lentas, ventoinhas aceder |1.54 A |0.33 kW |1126.01 BTU/hr |3.27 A |0.36 kW |1228.37 BTU/hr |
|  Ventoinhas rápidas, unidades Inativas, dois PSUs ligados à corrente |2.14 A |0.49 kW |1671.95 BTU/hr |4.99 A |0.54 kW |1842.56 BTU/hr |
|  Ventoinhas rápidas, unidades inativo, um PSU ligado um inativo |2.05 A |0.48 kW |1637.83 BTU/hr |4.58 A |0.50 kW |1706.07 BTU/hr |
|  Unidades rápidas, ventoinhas aceder, dois PSUs ligados à corrente |2.26 A |0.51 kW |1740.19 BTU/hr |4.95 A |0.54 kW |1842.56 BTU/hr |
|  Ventoinhas rápidas, unidades aceder, um PSU ligado um inativo |2.14 A |0.49 kW |1671.95 BTU/hr |4.81 A |0.53 kW |1808.44 BTU/hr |

## <a name="disk-drive-specifications"></a>Especificações da unidade de disco
O dispositivo StorSimple suporta até 12 unidades de disco do formulário 3.5 polegadas fator Serial Attached SCSI (SAS). As unidades reais podem ser uma mistura de unidades de estado sólido (SSDs) ou unidades de disco rígido (HDDs), dependendo da configuração do produto. As 12 ranhuras de unidade de disco estão localizadas numa configuração de 3 a 4 à frente de inclusão. Armazenamento adicional para o outro 12 unidades de disco permite a inclusão EBOD. Estes são sempre HDDs.  

## <a name="storage-specifications"></a>Especificações de armazenamento
Os dispositivos StorSimple tem uma combinação de unidades de disco rígido e unidades de estado sólido para os 8100 e 8600. A capacidade total utilizável do 8100 e 8600 são aproximadamente 15 TB e 38 TB, respetivamente. A tabela seguinte documentos os detalhes de SSD, HDD e capacidade de nuvem no contexto da capacidade de solução StorSimple.

| O modelo de dispositivo / capacidade | 8100 | 8600 |
| --- | --- | --- |
| Número de unidades de disco rígido (HDDs) |8 |19 |
| Número de unidades de estado sólido (SSDs) |4 |5 |
| Capacidade HDD único |4 TB |4 TB |
| Capacidade SSD único |400 GB |800 GB |
| Capacidade de reserva |4 TB |4 TB |
| Capacidade HDD utilizável |14 TB |36 TB |
| Utilizável capacidade SSD |800 GB |2 TB |
| Capacidade total utilizável * |~ 15 TB |~ 38 TB |
| Capacidade máxima de solução (incluindo a nuvem) |200 TB |500 TB |

<sup>* </sup>- *A capacidade de utilizável total inclui a capacidade disponível para memórias intermédias, metadados e dados.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>As dimensões de inclusão e especificações de ponderação
As tabelas seguintes listam as especificações de inclusão várias das dimensões e ponderação.  

### <a name="enclosure-dimensions"></a>Dimensões de inclusão
A tabela seguinte lista as dimensões de inclusão em milímetros e polegadas.

| Inclusão | Milímetros | Polegadas |
| --- | --- | --- |
| Altura |87.9 |3.46 |
| Largura em flange de montagem |483 |19.02 |
| Largura em corpo da inclusão |443 |17.44 |
| Profundidade da flange de front-montagem para extremity do corpo de inclusão |577 |22.72 |
| Profundidade do painel de operações para extremity furthest de inclusão |630.5 |24.82 |
| Profundidade da flange a montagem para extremity furthest de inclusão |603 |23.74 |

### <a name="enclosure-weight"></a>Peso da inclusão
Dependendo da configuração, um bastidor primário totalmente carregado pode pesar de 21 para 33 kgs e requer duas pessoas processá-lo. 

| Inclusão | Peso |
| --- | --- |
| Peso máximo (depende da configuração) |30 kg – 33 kg |
| Vazio (não existem unidades fitted) |21 – kg 23 |

## <a name="enclosure-environment-specifications"></a>Especificações de ambiente de inclusão
Esta secção lista as especificações relacionados com o ambiente de inclusão. A temperatura, humidade, altitude, shock, vibration, orientação, segurança e compatibilidade Electromagnetic (EMC) estão incluídos nesta categoria.  

### <a name="temperature-and-humidity"></a>Relativos à temperatura e humidade
| Inclusão | Intervalo de temperatura de ambiente | Ambiente de caminho relativo humidade | Bolbo wet máximo |
| --- | --- | --- | --- |
| Operacional |5-35° C (41° F - 95° F) |20% - 80% não-condensing- |28° C (82° F) |
| Não operacionais |-40-70° C (40° F - 158° F) |% 5% - 100 não-condensing |29° C (84° F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Airflow, altitude, shock, vibration, orientação, segurança e EMC
| Inclusão | Especificações operacionais |
| --- | --- |
| Airflow |Airflow do sistema é frente para rear. Sistema tem de ser utilizado com uma instalação low-pressure, esgotar os rear. Back-pressão criado por portas de bastidor e obstacles não deve exceder 5 pascals (0.5 mm máximo medidor). |
| Altitude, operacional |-30 medidores para medidores 3045 (feet-100 para 10 000 feet) com máxima temperatura operativo anular classificados por 5c ° acima 7000 feet. |
| Altitude,-na inoperacional |-305 medidores para medidores 12,192 (-1,000 feet para feet 40 000) |
| Shock, operacional |seno de 10 ms ½ 5g |
| Shock,-na inoperacional |seno de 10 ms ½ 30g |
| Vibration, operacional |0.21g RMS 5-500 Hz aleatório |
| Vibration,-na inoperacional |1.04g RMS 2 200 Hz aleatório |
| Vibration, reposicionamento |seno de 2 200 Hz 3g |
| Orientação e montagem |19" montagem de bastidor (2 unidades EIA) |
| Bastidor rails |Para se ajustar a profundidade de mm 700 mínimo (31.50 polegadas) bastidores em conformidade com IEC 297 |
| Segurança e aprovações |CE e UL EN 61000-3, IEC 61000-3, UL 61000 3 |
| EMC |EN55022 (CISPR - D), FCC A |

## <a name="international-standards-compliance"></a>Conformidade de padrões internacional
O dispositivo StorSimple do Microsoft Azure está em conformidade com as normas internacionais seguintes:  

* CE - EN 60950-1  
* Relatório de CB para IEC 60950-1  
* UL e cUL para UL 60950-1  

## <a name="safety-compliance"></a>Conformidade de segurança
O seu dispositivo do Microsoft Azure StorSimple cumpre as seguintes classificações de segurança:  

* Aprovação de tipo de produto do sistema: UL, cUL, CE  
* Conformidade de segurança: UL 60950 IEC 60950, EN 60950  

## <a name="emc-compliance"></a>Conformidade EMC
O seu dispositivo do Microsoft Azure StorSimple cumpre as seguintes classificações de EMC.  

### <a name="emissions"></a>Emissões nos
O dispositivo é compatível com o EMC para níveis de emissões nos radiated e a realização de um.  

* A realização de um emissões nos níveis de limitar: CFR 47 parte 15b global classe A EN55022 classe A CISPR classe A  
* Limite de emissões radiated nos níveis: CFR 47 parte 15b global classe A EN55022 classe A CISPR classe A   

### <a name="harmonics-and-flicker"></a>Harmonics e flicker
O dispositivo está em conformidade com EN61000-3-2/3.  

### <a name="immunity-limit-levels"></a>Níveis de limite de Immunity
O dispositivo está em conformidade com EN55024.  

## <a name="ac-power-cord-compliance"></a>Compatibilidade de cabo de energia de AC
O plug e a assemblagem de cabo energia completa têm de cumprir as normas adequadas para o país/região na qual o dispositivo está a ser utilizado e têm de ter aprovações de segurança que são aceitáveis desse país. As tabelas seguintes listam as normas para os E.U.A. e na Europa.  

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Cabos de energia de AC - EUA (tem de ser NRTL listado)
| Componente | Especificação |
| --- | --- |
| Tipo de cabo |Comprimento de máximo de medidores 2.0 SV ou SVT, 18 mínimo AWG, 3 conductor, |
| Plug |Plug de anexo de tipo de grounding NEMA 5-15P classificado 120 V, 10 A; ou IEC 320 C14, 250 V, 10 A |
| Socket |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Cabos de energia de AC - Europa
| Componente | Especificação |
| --- | --- |
| Tipo de cabo |Harmonized H05-VVF-3G1.0 |
| Socket |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Cabos de rede suportados
Para as interfaces de rede GbE 10, dados 2 e dados 3, consulte o [lista de módulos e de cabos de rede suportados](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Passos seguintes
Agora está pronto para implementar um dispositivo StorSimple no seu centro de dados. Para obter mais informações, consulte [implementar o seu dispositivo no local](storsimple-deployment-walkthrough-u2.md).  

