---
title: "Monitorizar o seu dispositivo de série 8000 do StorSimple | Microsoft Docs"
description: "Descreve como utilizar o serviço do Gestor de dispositivos do StorSimple para monitorizar a utilização, o desempenho de e/s e utilização da capacidade."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: 679c1fc8775ad4481bc99c9aea79fe16e9bcac8f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Utilizar o serviço do Gestor de dispositivos do StorSimple para monitorizar o dispositivo StorSimple

## <a name="overview"></a>Descrição geral
Pode utilizar o serviço do Gestor de dispositivos do StorSimple para monitorizar dispositivos específicos dentro da sua solução StorSimple. Pode criar gráficos personalizados com base no desempenho e/s, utilização da capacidade, débito de rede e métricas de desempenho do dispositivo e afixá-las para o dashboard. Para obter mais informações, aceda a [personalizar o dashboard do portal do](../azure-portal/azure-portal-dashboards.md).

Para ver as informações de monitorização para um dispositivo específico, no portal do Azure, selecione o serviço do Gestor de dispositivos do StorSimple. Na lista de dispositivos, selecione o seu dispositivo e, em seguida, aceda a **Monitor**. Em seguida, pode ver o **capacidade**, **utilização**, e **desempenho** gráficos para o dispositivo selecionado.

## <a name="capacity"></a>Capacidade
**Capacidade** controla o espaço aprovisionado e o espaço restante no dispositivo. A capacidade restante, em seguida, é apresentada como afixado localmente ou em camadas.

A capacidade de aprovisionamento e restantes é reduzida ainda mais por volumes em camadas e afixados localmente. Para cada volume, é apresentada a capacidade de aprovisionamento e a capacidade restante no dispositivo.

![Capacidade de e/s](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Utilização
**Utilização** controla as métricas relacionadas com a quantidade de espaço de armazenamento de dados que é utilizado pelos volumes, contentores de volume ou dispositivo. Pode criar relatórios baseados na utilização da capacidade de armazenamento primário, o seu armazenamento na nuvem ou o armazenamento do dispositivo. Utilização de capacidade pode ser medida em volume específico, um contentor de volume específico ou todos os contentores de volume.
Por predefinição, é reportada a utilização das últimas 24 horas. Pode editar o gráfico para alterar o período durante o qual a utilização é considerada selecionando a partir de:
* Após 24 horas
* Últimos 7 dias
* Últimos 30 dias
* Últimos 90 dias
* Passado ano

Dois metrices chaves, o crescimento e o intervalo são comunicadas para os gráficos de utilização. Intervalo refere-se para o valor máximo e os valores mínimo de utilização comunicados ao longo da duração selecionada (instância fo, nos últimos 7 dias).

Crescimento refere-se o aumento na utilização do primeiro dia para o último dia ao longo da duração selecionada. 

Intervalo de crescimento e também podem ser representados pelas equações seguintes:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

A nuvem principal, e o armazenamento local utilizado pode ser descrito da seguinte forma:

### <a name="primary-storage-usage"></a>Utilização de armazenamento primário
Estes gráficos mostram a quantidade de dados escritos para volumes do StorSimple antes dos dados de eliminação de duplicados e comprimidos. Pode ver o armazenamento primário utilizado por todos os volumes num contentor de volume ou de um único volume. O armazenamento primário utilizado é reduzido ainda mais por armazenamento em camadas primário utilizado e primário localmente afixado armazenamento utilizado.

Os gráficos seguintes mostram o armazenamento primário utilizado para um dispositivo StorSimple antes e depois foi obtido um instantâneo de nuvem. Como esta é apenas os dados de volume, um instantâneo na nuvem não deve alterar o armazenamento principal. Como pode ver, o gráfico não mostra nenhuma diferença no armazenamento em camadas ou localmente afixado primário utilizado como resultado de um instantâneo de nuvem. O instantâneo de nuvem foi iniciado no cerca 23:50:00 nesse dispositivo.

![Utilização da capacidade primário depois do instantâneo na nuvem](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Se executar agora a e/s no anfitrião ligado ao dispositivo StorSimple, verá um aumento de armazenamento em camadas primário ou primário localmente afixado consoante o armazenamento utilizado após os volumes (camadas ou afixado localmente) escreve os dados. Seguem-se os gráficos de utilização de armazenamento primário para um dispositivo StorSimple. Neste dispositivo, o anfitrião do StorSimple iniciado que serve escreve em aproximadamente 2:30 da tarde num volume em camadas no dispositivo. Pode ver o pico em bytes/s de escrita correspondente para a e/s em execução no anfitrião.

![Desempenho quando em execução em e/s volumes em camadas](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Se observar o armazenamento em camadas primário utilizado, que tornou-enquanto o principal localmente afixado utilização permanece inalterada porque existem não existem escritas fornecidas para os volumes localmente afixados no dispositivo.

![Utilização da capacidade primário quando e/s estão em execução em volumes em camadas](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Se estiver a executar a atualização 3 ou superior, pode dividir a utilização da capacidade de armazenamento primário por um volume individuais, todos os volumes, todos os volumes em camadas e todos os volumes localmente afixados conforme mostrado abaixo. Última hora para baixo por todos os volumes localmente afixados permitirá ascertain rapidamente a quantidade de escalão local se esgotar.

![Utilização da capacidade de principal para todos os volumes em camadas](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Utilização da capacidade de principal para todos os volumes localmente afixados](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Ainda pode clicar em cada um dos volumes na lista e ver a utilização correspondente.

![Utilização da capacidade de principal para todos os volumes localmente afixados](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Utilização do armazenamento na nuvem
Estes gráficos mostram a quantidade de armazenamento em nuvem utilizado. Estes dados é de eliminação de duplicados e comprimidos. Esta quantidade inclui os instantâneos de nuvem que podem conter dados não serão refletidos na qualquer volume primário e são mantidos por motivos de retenção de legado ou necessário. Pode comparar o principal e na nuvem valores de consumo de armazenamento para obter uma ideia da taxa de redução de dados, apesar do número não será exato.

Os gráficos seguintes mostram a utilização do armazenamento de nuvem de um dispositivo StorSimple quando foi obtido um instantâneo de nuvem.

* O instantâneo de nuvem foi iniciado no cerca 11:50 am nesse dispositivo e pode ver antes da nuvem de instantâneos, não ocorreu nenhum armazenamento em nuvem utilizado. 
* Uma vez o instantâneo de nuvem concluído, a utilização do armazenamento de nuvem captura segurança 0.89 GB. 
* Enquanto o instantâneo de nuvem estava em curso, há também um horário de pico correspondente na e/s de dispositivo para a nuvem.

    ![Utilização de armazenamento da nuvem antes de instantâneo na nuvem](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Utilização do armazenamento em nuvem depois de instantâneo na nuvem](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![E/s de dispositivo para nuvem durante um instantâneo na nuvem](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Utilização do armazenamento local
Estes gráficos que mostram a utilização total para o dispositivo, que será a utilização de armazenamento primário mais do que pois inclui camada SSD linear. Esta camada contém uma quantidade de dados também existe num dispositivo da outros escalões. A capacidade da camada linear de SSD é cycled para que, quando novos dados é apresentada no, os dados antigos são movidos para a camada HDD (em que momento é eliminação de duplicados e comprimido) e, subsequentemente, para a nuvem.

Ao longo do tempo, primário armazenamento local e utilizado armazenamento utilizado irá provavelmente aumentar em conjunto até que os dados começa a ser colocado em camadas para a nuvem. Nessa altura, o armazenamento local utilizado provavelmente irá começar a nivelar, mas o armazenamento primário utilizado irá aumentar à medida que são escritos mais dados.

Os gráficos seguintes mostram o armazenamento primário utilizado para um dispositivo StorSimple quando foi obtido um instantâneo de nuvem. O instantâneo de nuvem iniciado às 11:50 am e o armazenamento local iniciado diminuição nessa altura. O armazenamento local utilizado frequentou para baixo de 1.445 GB 1.09 GB. Isto indica que provavelmente a dados não comprimidos na camada de SSD linear eliminação de duplicados, comprimida e movida para a camada HDD. Tenha em atenção que, se o dispositivo já tiver uma grande quantidade de dados em camadas de SSD e HDD, não poderá ver este diminuir. Neste exemplo, o dispositivo tem uma pequena quantidade de dados.

![Utilização do armazenamento local depois de instantâneo na nuvem](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Desempenho
**Desempenho** controla as métricas relacionadas com o número de leitura e escrita operações entre às interfaces de iniciador do iSCSI no servidor anfitrião e o dispositivo ou o dispositivo e a nuvem. Este desempenho pode ser medido para um volume específico, um contentor de volume específico ou todos os contentores de volume. Desempenho também inclui a utilização da CPU e débito de rede para as várias interfaces de rede no seu dispositivo.

### <a name="io-performance-for-initiator-to-device"></a>Desempenho de e/s para o Iniciador do dispositivo
A tabela abaixo mostra a e/s para o iniciador para o seu dispositivo para todos os volumes para um dispositivo de produção. As métricas desenhadas são leitura e escrita bytes por segundo. Também pode gráfico leitura, escrita e e/s pendentes, ou leitura e escrita latências.

![Desempenho de e/s para o Iniciador do dispositivo](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Desempenho de e/s de dispositivo para a nuvem
Para o mesmo dispositivo, as operações de e/s são desenhadas para os dados do dispositivo para a nuvem para todos os contentores de volume. Este dispositivo, os dados são apenas na camada de linear em nada tem spilled para a nuvem. Não existem não existem operações de leitura e escrita do dispositivo a ocorrer para a nuvem. Por conseguinte, os picos no gráfico são um intervalo de 5 minutos, que corresponde a frequência com que o heartbeat é verificado entre o dispositivo e o serviço.

Para o mesmo dispositivo, foi obtido um instantâneo da nuvem para dados de volume começando 11 50 da manhã. Isto resultou em dados que fluem do dispositivo para a nuvem. Escritas foram fornecidas para a nuvem esta duração. O gráfico de e/s mostra um horário de pico que os Bytes de escrita/s correspondente para a hora quando o instantâneo foi tirado.

![E/s de dispositivo para nuvem durante um instantâneo na nuvem](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Débito de rede para interfaces de rede do dispositivo
**Débito de rede** controla as métricas relacionadas com a quantidade de dados transferidos de interfaces de rede de iniciador iSCSI no servidor de anfitrião e o dispositivo e entre o dispositivo e a nuvem. Pode monitorizar esta métrica para cada uma das interfaces de rede iSCSI no seu dispositivo.

Os gráficos seguintes mostram o débito de rede para os dados 0, 1 1 rede GbE no seu dispositivo, que era ambas nuvem ativada (predefinição) e o iSCSI ativado. Neste dispositivo em 14 de Junho em cerca de 9 pm, dados foi camados para a nuvem (não existem instantâneos de nuvem foram executados a essa hora que aponta para a criação de camadas de que está a ser o mecanismo de mover os dados para a nuvem) que resultou numa ser servido para a nuvem e/s. Existe um horário de pico correspondente no gráfico de débito de rede para o mesmo tempo e a maioria do tráfego de rede é de saída para a nuvem.

![Débito de rede para dados 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Se Vamos observar o gráfico de débito da interface de 1 de dados, outro 1 GbE duplos interface que apenas foi preparada para iSCSI, em seguida, não ocorreu virtualmente nenhum tráfego de rede desta duração.

![Débito de rede para dados 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Utilização da CPU de dispositivo
**Utilização da CPU** controla as métricas relacionadas com a CPU utilizada no seu dispositivo. O gráfico seguinte mostra as estatísticas de utilização da CPU para um dispositivo em produção.

![Utilização da CPU de dispositivo](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilize o dashboard de dispositivo de serviço do Gestor de dispositivos do StorSimple](storsimple-device-dashboard.md).
* Saiba como [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

