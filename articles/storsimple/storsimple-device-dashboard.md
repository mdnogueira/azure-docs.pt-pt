---
title: Utilize o dashboard do dispositivo StorSimple Manager | Microsoft Docs
description: "Descreve o dashboard de dispositivo do serviço StorSimple Manager e como utilizá-la para ver as métricas do storage e ligados iniciadores e localizar o número de série e IQN."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6c213969-a385-461f-b698-78ef5b8a79cc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d2209790b54f5dd18a413f2e08ddc6ffd6aef662
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-device-dashboard-in-storsimple-manager-service"></a>Utilize o dashboard do dispositivo no serviço StorSimple Manager  
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [utilize o dashboard de dispositivo no serviço StorSimple Manager ](storsimple-8000-device-dashboard.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Descrição geral
O dashboard de dispositivo do StorSimple Manager fornece uma descrição geral de informações para um dispositivo específico do StorSimple, ao contrário do dashboard de serviço, o que dá-lhe informações sobre todos os dispositivos incluídos na sua solução do Microsoft Azure StorSimple.

![Página do dashboard do dispositivo](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

O dashboard contém as seguintes informações:

* **Área de gráfico** – pode ver as métricas de armazenamento relevantes na área de gráfico na parte superior do dashboard. Este gráfico, pode ver as métricas para o total (a quantidade de dados escritos pelos anfitriões para o seu dispositivo) de armazenamento primário e o armazenamento de nuvem total consumido pelo seu dispositivo durante um período de tempo.
  
     Neste contexto, *armazenamento primário* refere-se para a quantidade total de dados escritos pelo anfitrião e podem ser divididos por tipo de volume: *primário camadas armazenamento* inclui os armazenadas localmente dados e os dados em camadas a nuvem; *primário afixado localmente armazenamento* inclui apenas os dados armazenados localmente. *Armazenamento na nuvem*, por outro lado, é uma medida da quantidade total de dados armazenados na nuvem. Isto inclui dados em camadas e de cópias de segurança. Tenha em atenção que os dados armazenados na nuvem de eliminação de duplicados e comprimidos, enquanto o armazenamento primário indica a quantidade de armazenamento utilizado antes dos dados de eliminação de duplicados e comprimidos. (Pode comparar estes dois números para obter uma ideia da taxa de compressão.) Para os dois principais e de armazenamento na nuvem, as quantidades mostrados serão com base na frequência de controlo que configura. Por exemplo, se escolher uma frequência de uma semana, em seguida, o gráfico irá mostrar dados para cada dia da semana anterior.
  
     Pode configurar o gráfico da seguinte forma:
  
  * Para ver a quantidade de armazenamento na nuvem consumido ao longo do tempo, selecione o **NUVEM armazenamento utilizado** opção. Para ver o armazenamento total foi escrito pelo anfitrião, selecione o **primário CAMADAS armazenamento utilizado** e **primário LOCALMENTE AFIXADO armazenamento utilizado** opções. Na ilustração, estão selecionadas ambas as opções; Por conseguinte, o gráfico mostra quantidades de armazenamento de nuvem e de armazenamento primário. Tenha em atenção que qualquer armazenamento primário utilizado antes de instalar a atualização 2 é representado pelo **primário CAMADAS armazenamento utilizado** linha.
  * Utilize o menu pendente no canto superior direito do gráfico para especificar um período de tempo de 1 semana, mês 1, 3 meses ou 1 ano. Tenha em atenção que o gráfico de nível superior é atualizado apenas uma vez por dia e, por conseguinte, irá refletir totais o dia anterior.
    
    Para obter mais informações, consulte [utilizar o serviço StorSimple Manager para monitorizar o dispositivo StorSimple](storsimple-monitor-device.md).
* **Descrição geral da utilização** – no **descrição geral da utilização** área, pode ver a quantidade de armazenamento primário utilizado, a quantidade de armazenamento aprovisionada e a capacidade de armazenamento máximo para o seu dispositivo. Ao comparar estes números de utilização para a quantidade máxima de armazenamento que esteja disponível, pode ver rapidamente se precisar de obter armazenamento adicional. Tenha em atenção que esta descrição geral é atualizado a cada 15 minutos e, devido à diferença na frequência de atualização, pode mostrar diferentes números que são apresentadas na área de gráfico acima, que é atualizada diariamente. Para obter mais informações, consulte [utilizar o serviço StorSimple Manager para monitorizar o dispositivo StorSimple](storsimple-monitor-device.md).
* **Alertas** – o **alertas** área contém uma descrição geral dos alertas para o seu dispositivo. Alertas são agrupados por gravidade, e é fornecida uma contagem do número de alertas em cada nível de gravidade. Clicar a gravidade do alerta abre uma vista de âmbito do separador alertas para mostrar apenas os alertas de que nível de gravidade para este dispositivo.
* **As tarefas** – o **tarefas** área mostra-lhe o resultado da atividade recente de tarefa. Isto pode assegurar que é que o sistema está a funcionar conforme esperado, ou pode informá-lo de que precisa de tomar as medidas corretivas. Para obter mais informações sobre as tarefas concluídas recentemente, clique em **tarefas foi concluída com êxito nas últimas 24 horas**.
* O **leitura rápida** área à direita do dashboard fornece informações úteis, tais como o modelo do dispositivo, o número de série, o estado, a descrição e número de volumes.

Também pode configurar a ativação pós-falha e ver iniciadores ligados a partir do dashboard do dispositivo.

As tarefas comuns que podem ser efetuadas nesta página são:

* Ver iniciadores ligados
* Determinar o número de série do dispositivo
* Localizar o destino de dispositivo IQN

## <a name="view-connected-initiators"></a>Ver iniciadores ligados
Pode ver os iniciadores iSCSI que estão ligados, para o seu dispositivo, clicando a **vista ligado iniciadores** hiperligação fornecida no **leitura rápida** área do seu dashboard do dispositivo. Esta página fornece uma listagem tabular dos iniciadores que ligou-se com êxito para o seu dispositivo. Para cada iniciador, consulte:

* O iSCSI nome qualificado (IQN) da sessão do iniciador ligado.
* O nome do registo de controlo de acesso (ACR) que permite que este iniciador ligado.
* O endereço IP do iniciador de ligado.
* As interfaces de rede que o iniciador está ligado no seu dispositivo de armazenamento. Estes podem variar entre dados 0 a 5 de dados.
* Todos os volumes que o iniciador ligado está autorizado a aceder, de acordo com a configuração de ACR atual.

Se a ver iniciadores inesperados nesta lista ou não as esperado, reveja a configuração de ACR. Um máximo de 512 iniciadores pode ligar para o seu dispositivo.

## <a name="find-the-device-serial-number"></a>Determinar o número de série do dispositivo
Poderá ter o número de série do dispositivo quando configurar o Microsoft Multipath i / o (MPIO) no dispositivo. Execute os passos seguintes para determinar o número de série do dispositivo.

#### <a name="to-find-the-device-serial-number"></a>Para determinar o número de série do dispositivo
1. Navegue para **dispositivos** > **Dashboard**.
2. No painel da direita do dashboard, localize o **leitura rápida** área.
3. Desloque para baixo e localize o número de série.

## <a name="find-the-device-target-iqn"></a>Localizar o destino de dispositivo IQN
Poderá ter o IQN de destino do dispositivo quando configurar o protocolo de autenticação de Handshake de desafio (CHAP) no dispositivo StorSimple. Execute os passos seguintes para localizar o destino de dispositivo IQN.

### <a name="to-find-the-device-target-iqn"></a>Para localizar o destino de dispositivo IQN
1. Navegue para **dispositivos** > **Dashboard**.
2. No painel da direita do dashboard, localize o **leitura rápida** área.
3. Desloque para baixo e localize o IQN de destino.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [dashboard do serviço StorSimple Manager](storsimple-service-dashboard.md).
* Saiba mais sobre [utilizando o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

