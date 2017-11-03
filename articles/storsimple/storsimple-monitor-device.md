---
title: Monitorizar o dispositivo StorSimple | Microsoft Docs
description: "Descreve como utilizar o serviço StorSimple Manager para monitorizar o desempenho de e/s, utilização da capacidade, débito de rede e desempenho do dispositivo."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: bd4f7704-4f6f-47d0-927a-b1c91eabc453
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/16/2016
ms.author: alkohli
ms.openlocfilehash: d45bb37c8417785db0ea38be4375a998b6d9f109
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>Utilizar o serviço StorSimple Manager para monitorizar o dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Pode utilizar o serviço StorSimple Manager para monitorizar dispositivos específicos dentro da sua solução StorSimple. Pode criar gráficos personalizados com base no desempenho e/s, utilização da capacidade, débito de rede e métricas de desempenho do dispositivo. 

Para ver as informações de monitorização para um dispositivo específico, no portal clássico do Azure, selecione o serviço StorSimple Manager. Clique em de **Monitor** separador e, em seguida, selecione na lista de dispositivos. O **Monitor** página contém as seguintes informações.

## <a name="io-performance"></a>Desempenho de e/s
**Desempenho de e/s** controla as métricas relacionadas com o número de leitura e escrita operações entre às interfaces de iniciador do iSCSI no servidor anfitrião e o dispositivo ou o dispositivo e a nuvem. Este desempenho pode ser medido para um volume específico, um contentor de volume específico ou todos os contentores de volume.

A tabela abaixo mostra a e/s para o iniciador para o seu dispositivo para todos os volumes para um dispositivo de produção. As métricas desenhadas são lidas e escrever bytes por segundo, ler e escrever operações de e/s por segundo e leem e escrevem latências.

![Desempenho de e/s do Iniciador do dispositivo](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

Para o mesmo dispositivo, as operações de e/s são desenhadas para os dados do dispositivo para a nuvem para todos os contentores de volume. Este dispositivo, os dados são apenas na camada de linear em nada tem spilled para a nuvem. Não existem não existem operações de leitura e escrita do dispositivo a ocorrer para a nuvem. Por conseguinte, os picos no gráfico são um intervalo de 5 minutos, que corresponde a frequência com que o heartbeat é verificado entre o dispositivo e o serviço. 

![Desempenho de e/s de dispositivo para a nuvem](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)

Para o mesmo dispositivo, foi obtido um instantâneo da nuvem para dados de volume começando 2:00 pm. Isto resultou em dados que fluem do dispositivo para a nuvem. Escritas de leituras foram processadas para a nuvem esta duração. O gráfico de e/s mostra um horário de pico em várias métricas correspondente para a hora quando o instantâneo foi tirado. 

![Desempenho de e/s de dispositivo para nuvem após o instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)

## <a name="capacity-utilization"></a>Utilização de capacidade
**Utilização de capacidade** controla as métricas relacionadas com a quantidade de espaço de armazenamento de dados que é utilizado pelos volumes, contentores de volume ou dispositivo. Pode criar relatórios baseados na utilização da capacidade de armazenamento primário, o seu armazenamento na nuvem ou o armazenamento do dispositivo. Utilização de capacidade pode ser medida em volume específico, um contentor de volume específico ou todos os contentores de volume.

A nuvem principal, e a capacidade de armazenamento do dispositivo pode ser descrita da seguinte forma:

### <a name="primary-storage-capacity-utilization"></a>Utilização da capacidade de armazenamento primário
Estes gráficos mostram a quantidade de dados escritos para volumes do StorSimple antes dos dados de eliminação de duplicados e comprimidos. Pode ver a utilização de armazenamento primário por todos os volumes ou de um único volume.

Quando visualiza os gráficos de utilização do armazenamento primário volume capacidade para todos os volumes versus cada um dos volumes individuais e some os dados primários em ambos nestes casos, poderão ocorrer um erro de correspondência entre dois números. Total de dados primária em todos os volumes não pode adicionar para o total da soma dos dados primários dos volumes individuais. Esta situação poderá dever-se um dos seguintes:

* **Incluído em todos os volumes de dados de instantâneos**: Este comportamento é utilizado apenas se estiver a executar versão anterior ao Update 3. Os dados primários apresentados para todos os volumes são a soma dos dados primários para cada volume e os dados de instantâneos. Os dados primários mostrados para um determinado volume corresponde ao apenas a quantidade de dados alocados no volume (e não incluem os dados de instantâneos de volume correspondente).
  
    Também pode ser explicado por equação a seguinte:
  
    *(Todos os volumes) de dados principal = soma de (dados primários (volume i) + tamanho dos dados de instantâneos (volume i))*
  
    *WHERE, dados primários (volume i) = tamanho dos dados primários atribuídos ao volume i*
  
    Se os instantâneos são eliminados através do serviço, a eliminação é feita de forma assíncrona em segundo plano. Pode demorar algum tempo para que o tamanho de dados do volume a ser atualizado seguindo a eliminação do instantâneo. 
  
    Se executar a atualização 3 ou posterior, os dados de instantâneos não estão incluídos nos dados de volume. E a utilização principal é calculada da seguinte forma:
  
    * Dados principal (todos os volumes) = soma de (dados primários (volume i)
  
    *WHERE, dados primários (volume i) = tamanho dos dados primários atribuídos ao volume i*
* **Volumes com a monitorização desativada incluídos em todos os volumes**: Se tiver de volumes no seu dispositivo para o qual monitorização está desativada, os dados de monitorização para estes volumes individuais não estarão disponíveis nos gráficos. No entanto, os dados para todos os volumes no gráfico incluem os volumes para a qual monitorização está desativada. 
* **Eliminar os volumes com em direto cópias de segurança associadas incluídas para todos os volumes**: se volumes que contêm dados de instantâneos são eliminados, mas ainda existem instantâneos associados, em seguida, poderá ver um erro de correspondência.
* **Eliminar os volumes incluídos em todos os volumes**: em alguns casos, os volumes antigos poderão existir, apesar de estes foram eliminados. O efeito de eliminação não é utilizado e o dispositivo pode mostrar inferior capacidade disponível. Terá de contactar a Microsoft Support para remover estes volumes.

Os gráficos seguintes mostram a utilização da capacidade de armazenamento primário de um dispositivo StorSimple antes e depois foi obtido um instantâneo de nuvem. Como esta é apenas os dados de volume, um instantâneo na nuvem não deve alterar o armazenamento principal. Como pode ver, o gráfico não mostra nenhuma diferença na utilização da capacidade primário como resultado de um instantâneo de nuvem. O instantâneo de nuvem foi iniciado no aproximadamente 2:00 pm nesse dispositivo.

![Utilização da capacidade primário antes de instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![Utilização da capacidade primário depois do instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

Se estiver a executar a atualização 2 ou superior, pode dividir a utilização da capacidade de armazenamento primário por um volume individuais, todos os volumes, todos os volumes em camadas e todos os volumes localmente afixados conforme mostrado abaixo. Última hora para baixo por todos os volumes localmente afixados permitirá ascertain rapidamente a quantidade de escalão local se esgotar.

![Utilização da capacidade de principal para todos os volumes localmente afixados](./media/storsimple-monitor-device/localvolumes.png)

### <a name="cloud-storage-capacity-utilization"></a>Utilização da capacidade de armazenamento na nuvem
Estes gráficos mostram a quantidade de armazenamento em nuvem utilizado. Estes dados é de eliminação de duplicados e comprimidos. Esta quantidade inclui os instantâneos de nuvem que podem conter dados não serão refletidos na qualquer volume primário e são mantidos por motivos de retenção de legado ou necessário. Pode comparar o principal e na nuvem valores de consumo de armazenamento para obter uma ideia da taxa de redução de dados, apesar do número não será exato. Os gráficos seguintes mostram a utilização da capacidade de armazenamento na nuvem de um dispositivo StorSimple antes e depois foi obtido um instantâneo de nuvem. O instantâneo de nuvem foi iniciado no aproximadamente 2:00 pm nesse dispositivo e pode ver a utilização da capacidade de nuvem captura cópias de segurança ao mesmo tempo, o aumento de 5.73 MB 4.04 GB.

![Utilização da capacidade de nuvem antes de instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![Utilização da capacidade de nuvem após o instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)

### <a name="device-storage-capacity-utilization"></a>Utilização de capacidade de armazenamento do dispositivo
Estes gráficos que mostram a utilização total para o dispositivo, que será a utilização de armazenamento primário mais do que pois inclui camada SSD linear. Esta camada contém uma quantidade de dados também existe num dispositivo da outros escalões. A capacidade da camada linear de SSD é cycled para que, quando novos dados é apresentada no, os dados antigos são movidos para a camada HDD (em que momento é eliminação de duplicados e comprimido) e, subsequentemente, para a nuvem.

Ao longo do tempo, utilização da capacidade primário e de utilização da capacidade de dispositivo irão provavelmente aumentar em conjunto até que os dados começa a ser colocado em camadas para a nuvem. Nessa altura, a utilização da capacidade de dispositivo, provavelmente, começará a nivelar, mas a utilização da capacidade primário irá aumentar à medida que são escritos mais dados.

Os gráficos seguintes mostram a utilização da capacidade de armazenamento primário de um dispositivo StorSimple antes e depois foi obtido um instantâneo de nuvem. O instantâneo de nuvem iniciada às 2:00 pm e a utilização da capacidade de dispositivo iniciado diminuição nessa altura. A utilização de capacidade de armazenamento do dispositivo frequentou para baixo de 11.58 GB 7.48 GB. Isto indica que provavelmente a dados não comprimidos na camada de SSD linear eliminação de duplicados, comprimida e movida para a camada HDD. Tenha em atenção que, se o dispositivo já tiver uma grande quantidade de dados em camadas de SSD e HDD, não poderá ver este diminuir. Neste exemplo, o dispositivo tem uma pequena quantidade de dados.

![Utilização da capacidade de dispositivo antes de instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![Utilização da capacidade de dispositivo depois de instantâneo na nuvem](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)

## <a name="network-throughput"></a>Débito de rede
**Débito de rede** controla as métricas relacionadas com a quantidade de dados transferidos de interfaces de rede de iniciador iSCSI no servidor de anfitrião e o dispositivo e entre o dispositivo e a nuvem. Pode monitorizar esta métrica para cada uma das interfaces de rede iSCSI no seu dispositivo.

Os gráficos seguintes mostram o débito de rede para a Data 0 e 4 de dados, ambas as interfaces de rede GbE 1 no seu dispositivo. Neste caso, dados 0 foi ativado para a nuvem enquanto 4 de dados foi preparada para iSCSI. Pode ver a entrada e o tráfego de saída para o dispositivo StorSimple. A linha simples no gráfico a partir de às 15:24 é owing facto que iremos recolher dados a cada 5 minutos e deve ser ignorados. 

![Débito de rede para Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Débito de rede para Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)

## <a name="device-performance"></a>Desempenho de dispositivo
**Desempenho de dispositivo** controla as métricas relacionadas com o desempenho do seu dispositivo. O gráfico seguinte mostra as estatísticas de utilização da CPU para um dispositivo em produção.

![Utilização da CPU de dispositivo](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilize o dashboard de dispositivo do serviço StorSimple Manager](storsimple-device-dashboard.md).
* Saiba como [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

