---
title: StorSimple componentes de hardware e Estado | Microsoft Docs
description: "Saiba como monitorizar os componentes de hardware do dispositivo StorSimple através do serviço StorSimple Manager."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 0d56a2ba-daf0-45ad-9610-8b8712dd5750
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 686a4faf37342e844f3aa0166d9311a438fa753a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-monitor-hardware-components-and-status"></a>Utilizar o serviço StorSimple Manager para monitorizar componentes de hardware e o Estado
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [utilizar o serviço StorSimple Manager para monitorizar componentes de hardware e estado](storsimple-8000-monitor-hardware-status.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
Este artigo descreve os vários componentes físicos e lógicos no dispositivo StorSimple no local. Também explica como monitorizar o estado do componente de dispositivo utilizando o **manutenção** página no serviço StorSimple Manager. 

O **manutenção** página mostra o hardware do Estado de todos os componentes de dispositivo StorSimple. 

Na lista de componentes para 8100, existem três secções que descrevem:

* **Partilhado componentes** – estes não fazem parte de controladores, tais como unidades de disco, bastidor, componentes PCM temperatura PCM, linha tensão e linha sensores atuais.
* **Componentes de controlador 0** – os componentes que residam no controlador 0, como o controlador, SAS expander e conector, sensores de temperatura de controlador e as várias interfaces de rede.
* **Componentes de controlador 1** – os componentes que constituem o controlador 1 semelhantes às detalhadas para o controlador 0.

Um dispositivo 8600 tem componentes adicionais que correspondem ao bastidor expandido Bunch de discos (EBOD). Na lista de componentes, existem cinco secções. Deles, existem três secções que contenham os componentes a inclusão principal e são idênticos aos que estão descrito para 8100. Existem duas secções adicionais para o bastidor EBOD que descrevem:

* **EBOD bastidor partilhado componentes** – apresentam os componentes no bastidor EBOD e PCM que não fazem parte do controlador de EBOD.
* **Componentes de EBOD controlador 0** – os componentes que residem no bastidor EBOD 0, tais como o controlador de EBOD, sensores de temperatura de expander e o conector e o controlador SAS.
* **Componentes de 1 de controlador EBOD** – os componentes que constituem a inclusão EBOD 1, semelhante a esses detalhadas para inclusão EBOD 0.

> [!NOTE]
> **A secção de estado de hardware não está presente na página de manutenção para um dispositivo virtual StorSimple (1100).**
> 
> 

## <a name="monitor-the-hardware-status"></a>Monitorizar o estado de hardware
Execute os seguintes passos para ver o estado de hardware de um componente de dispositivo:

1. Navegue para **dispositivos**, selecione um dispositivo StorSimple específico. Clique aqui para ir para o menu de nível do dispositivo e, em seguida, clique em **manutenção**. 
2. Localize o **estado do Hardware** secção e escolha entre os componentes disponíveis (conforme descrito acima). Basta clicar uma seta anterior a etiqueta do componente para expandir a lista e ver o estado dos vários componentes do dispositivo. Consulte o [lista de componentes de detalhado para o bastidor primário](#component-list-for-primary-enclosure-of-storsimple-device) e [lista de componentes de detalhado para a inclusão EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).
3. Utilize o esquema de codificação de cores seguintes para interpretar o estado do componente:
   
   * **Verde verificação** – Denotes um **bom estado de funcionamento** ou **OK** componente.
   * **Amarelo** – indica um componente na **aviso** estado.
   * **Exclamação vermelha** – Denotes um componente que tenha um **falha** ou **necessita de atenção** estado.
   * **Branco com texto preto** – indica um componente que não está presente.
4. Se tiver um componente que não esteja num **bom estado de funcionamento** de estado, contacte Support da Microsoft. Se os alertas são ativados no seu dispositivo, receberá um alerta por e-mail. Se precisar de substituir um componente de falha de hardware, consulte [substituição de componente de hardware do StorSimple](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Lista de componentes para inclusão principal do dispositivo StorSimple
A tabela seguinte descreve os componentes físicos e lógicos contidos a inclusão principal do dispositivo StorSimple no local.

| Componente | Módulo | Tipo | Localização | Unidade de substituível em campo (FRU)? | Descrição |
| --- | --- | --- | --- | --- | --- |
| Unidade na ranhura [0-11] |Unidades de disco |Físico |Partilhado |Sim |É apresentada uma linha para cada um dos SSD ou as unidades de HDD a inclusão principal. |
| Sensor de temperatura de ambiente |Inclusão |Físico |Partilhado |Não |Mede a temperatura dentro de chassis. |
| Sensor de temperatura média dimensão que plane |Inclusão |Físico |Partilhado |Não |Mede a temperatura do plane intermédio. |
| Audible alarme |Inclusão |Físico |Partilhado |Não |Indica se o subsistema de alarme audible dentro de chassis está funcional. |
| Inclusão |Inclusão |Físico |Partilhado |Sim |Indique a presença de um chassis. |
| Definições de inclusão |Inclusão |Físico |Partilhado |Não |Refere-se para o painel frontal do chassis. |
| Sensores de tensão de linha |PCM |Físico |Partilhado |Não |Várias sensores de tensão de linha tem o respetivo estado apresentado, que indica se a medida tensão respeitam a tolerância. |
| Sensores atuais de linha |PCM |Físico |Partilhado |Não |Várias sensores atual de linha tem o respetivo estado apresentado, que indica se a medida atual é respeitam a tolerância. |
| Sensores de temperatura no PCM |PCM |Físico |Partilhado |Não |Várias sensores de temperatura como entrada e os sensores de hotspots têm o respetivo estado apresentado, que indica se a temperatura medida é respeitam a tolerância. |
| Fonte de alimentação [0-1] |PCM |Físico |Partilhado |Sim |É apresentada uma linha para cada um das fontes de alimentação os dois PCMs localizados in the back of o dispositivo. |
| Arrefecimento [0-1] |PCM |Físico |Partilhado |Sim |É apresentada uma linha para cada um das ventoinhas de arrefecimento quatro que residem nos dois PCMs. |
| Bateria [0-1] |PCM |Físico |Partilhado |Sim |É apresentada uma linha para cada um dos módulos de bateria cópia de segurança que são seated no PCM. |
| Metis |N/D |Lógica |Partilhado |N/D |Apresenta o estado dos baterias: tem de charging e estão a aproximar-se o fim-de-vida. |
| Cluster |N/D |Lógica |Partilhado |N/D |Apresenta o estado do cluster é criado entre os dois módulos de controlador integrado. |
| Nó de cluster |N/D |Lógica |Partilhado |N/D |Indica o estado do controlador de como parte do cluster. |
| Quórum de cluster |N/D |Lógica | |N/D |Indique a presença de associação de disco maioria HDD agrupamento de armazenamento. |
| Espaço de dados HDD |N/D |Lógica |Partilhado |N/D |O espaço de armazenamento que é utilizado para dados no agrupamento de armazenamento de disco rígido (HDD). |
| Espaço de gestão de HDD |N/D |Lógica |Partilhado |N/D |O espaço reservado no agrupamento de armazenamento HDD para tarefas de gestão. |
| Espaço de quórum HDD |N/D |Lógica |Partilhado |N/D |O espaço reservado no agrupamento de armazenamento HDD para quórum de cluster. |
| Espaço de substituição de HDD |N/D |Lógica |Partilhado |N/D |O espaço reservado no agrupamento de armazenamento de HDD para substituição de controlador. |
| Espaço de dados SSD |N/D |Lógica |Partilhado |N/D |O espaço de armazenamento utilizado para os dados na unidade de estado sólido agrupamento de armazenamento (SSD). |
| Espaço de SSD NVRAM |N/D |Lógica |Partilhado |N/D |O espaço de armazenamento no agrupamento de armazenamento SSD que está dedicado para a lógica de NVRAM. |
| Agrupamento de armazenamento HDD |N/D |Lógica |Partilhado |N/D |Apresenta o Estado o lógica do agrupamento de armazenamento criada a partir desse dispositivo HDDs. |
| Agrupamento de armazenamento SSD |N/D |Lógica |Partilhado |N/D |Apresenta o Estado o lógica do agrupamento de armazenamento criada a partir desse dispositivo SSDs. |
| Controlador [0-1], [Estado] |E/S |Físico |Controlador |Sim |Apresenta o estado de controlador, e se está no modo de reserva dinâmica ou Active Directory dentro de chassis. |
| Sensores de temperatura no controlador |E/S |Físico |Controlador |Não |Várias sensores de temperatura, tais como o módulo de e/s, temperatura de CPU, sensores DIMM e PCIe tem o respetivo estado apresentado, que indica se é ou não a temperatura encontrou respeitam a tolerância. |
| SAS expander |E/S |Físico |Controlador |Não |Indica o estado da série anexado SCSI (SAS) expander, que é utilizada para ligar o armazenamento integrado para o controlador. |
| Conector SAS [0-1] |E/S |Físico |Controlador |Não |Indica o estado de cada conector SAS, que é utilizada para ligar o armazenamento integrado para o expander SAS. |
| Interligação de média dimensão que plane SBB |E/S |Físico |Controlador |Não |Indica o estado do conector plane intermédio, que é utilizado para ligar cada controlador para o plane intermédio. |
| Núcleo do processador |E/S |Físico |Controlador |Não |Indica o estado dos núcleos de processador dentro de cada controlador. |
| Energia electronics de inclusão |E/S |Físico |Controlador |Não |Indica o estado do sistema de energia utilizado pelo bastidor. |
| Diagnóstico de electronics bastidor |E/S |Físico |Controlador |Não |Indica o estado dos subsistemas de diagnóstico fornecida pelo controlador. |
| Controlador de gestão de placa base (BMC) |E/S |Físico |Controlador |Não |Indica o estado do controlador de gestão de placas base (BMC), que é um processador de serviço especializadas que monitoriza o dispositivo de hardware através de sensores e comunica com o administrador de sistema através de uma ligação independente. |
| Ethernet |E/S |Físico |Controlador |Não |Indica o estado de cada uma das interfaces de rede, ou seja, de gestão e de portas de dados fornecidas no controlador de. |
| NVRAM |E/S |Físico |Controlador |Não |Indica o estado da NVRAM, uma memória de acesso aleatório não volátil cópia de segurança de bateria que serve para manter informações críticas para a aplicação em caso de falha de energia. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Lista de componentes para inclusão EBOD do dispositivo StorSimple
A tabela seguinte descreve os componentes físicos e lógicos contidos no bastidor EBOD do dispositivo StorSimple no local.

| Componente | Módulo | Tipo | Localização | FRU? | Descrição |
| --- | --- | --- | --- | --- | --- |
| Unidade na ranhura [0-11] |Unidades de disco |Físico |Partilhado |Sim |É apresentada uma linha para cada unidades HDD no início a inclusão EBOD. |
| Sensor de temperatura de ambiente |Inclusão |Físico |Partilhado |Não |Mede a temperatura dentro de chassis. |
| Sensor de temperatura média dimensão que plane |Inclusão |Físico |Partilhado |Não |Mede a temperatura do plane intermédio. |
| Audible alarme |Inclusão |Físico |Partilhado |Não |Indica se o subsistema de alarme audible dentro de chassis está funcional. |
| Inclusão |Inclusão |Físico |Partilhado |Sim |Indique a presença de um chassis. |
| Definições de inclusão |Inclusão |Físico |Partilhado |Não |Refere-se a OPS ou o painel frontal do chassis. |
| Sensores de tensão de linha |PCM |Físico |Partilhado |Não |Várias sensores de tensão de linha tem o respetivo estado apresentado, que indica se a medida tensão respeitam a tolerância. |
| Sensores atuais de linha |PCM |Físico |Partilhado |Não |Várias sensores atual de linha tem o respetivo estado apresentado, que indica se a medida atual é respeitam a tolerância. |
| Sensores de temperatura no PCM |PCM |Físico |Partilhado |Não |Várias sensores de temperatura como entrada e os sensores de hotspots têm o respetivo estado apresentado, que indica se a temperatura medida respeitam a tolerância. |
| Fonte de alimentação [0-1] |PCM |Físico |Partilhado |Sim |É apresentada uma linha para cada um das fontes de alimentação os dois PCMs localizados in the back of o dispositivo. |
| Arrefecimento [0-1] |PCM |Físico |Partilhado |Sim |É apresentada uma linha para cada um das ventoinhas de arrefecimento quatro que residem nos dois PCMs. |
| Armazenamento local [HDD] |N/D |Lógica |Partilhado |N/D |Apresenta o Estado o lógica do agrupamento de armazenamento criada a partir desse dispositivo HDDs. |
| Controlador [0-1], [Estado] |E/S |Físico |Controlador |Sim |Apresenta o estado dos controladores de no módulo EBOD. |
| Sensores de temperatura no EBOD |E/S |Físico |Controlador |Não |Várias sensores de temperatura de cada controlador de tem o respetivo estado apresentado, que indica se a temperatura encontrou respeitam a tolerância. |
| SAS expander |E/S |Físico |Controlador |Não |Indica o estado da expander a SAS, que é utilizada para ligar o armazenamento integrado para o controlador. |
| Conector SAS [0-2] |E/S |Físico |Controlador |Não |Indica o estado de cada conector SAS, que é utilizada para ligar o armazenamento integrado para o expander SAS. |
| Interligação de média dimensão que plane SBB |E/S |Físico |Controlador |Não |Indica o estado do conector plane intermédio, que é utilizado para ligar cada controlador para o plane intermédio. |
| Energia electronics de inclusão |E/S |Físico |Controlador |Não |Indica o estado do sistema de energia utilizado pelo bastidor. |
| Diagnóstico de electronics bastidor |E/S |Físico |Controlador |Não |Indica o estado dos subsistemas de diagnóstico fornecida pelo controlador. |
| Ligação ao controlador de dispositivo |E/S |Físico |Controlador |Não |Indica o estado da ligação entre o módulo de e/s de EBOD e o controlador de dispositivo. |

## <a name="next-steps"></a>Passos seguintes
* Para utilizar o serviço StorSimple Manager para administrar o seu dispositivo, vá para [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).
* Se precisar de um componente de dispositivo que tem um Estado de degradada ou falhado de resolução de problemas, consulte [StorSimple monitorização indicadores](storsimple-monitoring-indicators.md). 
* Para substituir um componente de falha de hardware, consulte [substituição de componente de hardware do StorSimple](storsimple-hardware-component-replacement.md).
* Se continuar a ter problemas de dispositivo, [contacte a Microsoft Support](storsimple-contact-microsoft-support.md).

