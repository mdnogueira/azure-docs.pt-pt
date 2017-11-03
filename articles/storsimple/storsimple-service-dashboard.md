---
title: "Dashboard de serviço StorSimple Manager | Microsoft Docs"
description: "Descreve o dashboard do serviço StorSimple Manager e explica como utilizá-lo para monitorizar o estado de funcionamento da solução StorSimple."
services: storsimple
documentationcenter: 
author: SharS
manager: carmonm
editor: 
ms.assetid: fb0f131d-d60b-45d7-ace2-56d0502e6627
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2016
ms.author: v-sharos
ms.openlocfilehash: 596431b7279b753ca4da838eb028cdde2022ce02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-dashboard"></a>Utilize o dashboard do serviço StorSimple Manager
## <a name="overview"></a>Descrição geral
A página de dashboard do serviço StorSimple Manager fornece um resumo de todos os dispositivos que estão ligados ao serviço StorSimple Manager, realce aqueles que necessitam de atenção um administrador de sistema. Este tutorial apresenta a página do dashboard, explica o conteúdo de dashboard e a função e descreve as tarefas que pode realizar a partir desta página.

![Dashboard de serviço](./media/storsimple-service-dashboard/HCS_ServiceDashboard.png)

O dashboard de serviço do StorSimple Manager apresenta as seguintes informações:

* No **gráfico** área, pode ver o gráfico de métricas relevantes para os seus dispositivos. Pode ver o armazenamento primário (localmente afixado e em camadas) utilizada em todos os dispositivos, bem como o armazenamento de nuvem consumidos pelos dispositivos num período de tempo. Utilize os controlos no canto superior direito do gráfico para especificar uma escala de 1 semana, 1 mês, 3 meses ou anos de 1 hora.
* O **descrição geral da utilização** mostra o armazenamento primário que está aprovisionado e consumido por todos os dispositivos em relação ao armazenamento total disponível em todos os dispositivos. **Aprovisionado** refere-se para a quantidade de armazenamento que está preparado e atribuído para utilização, enquanto **utilizado** refere-se a utilização de volumes como visualizar os iniciadores que estejam ligados aos dispositivos.
* O **alertas** área fornece um instantâneo de todos os alertas ativos em todos os dispositivos, agrupados por gravidade do alerta. Clicar o nível de gravidade abre o **alertas** página, um âmbito para mostrar as alertas. No **alertas** página, pode clicar num alerta individual para ver detalhes adicionais sobre este alerta, incluindo quaisquer ações recomendadas. Também pode limpar o alerta se o problema foi resolvido.
* O **tarefas** área fornece um instantâneo de tarefas recentes em todos os dispositivos que estão ligados ao seu serviço. Existem ligações que pode utilizar para ver as tarefas que estão atualmente em curso, aqueles que falharam nas últimas 24 horas ou que estejam agendados para execução no próximas 24 horas.
* O **leitura rápida** área fornece informações úteis, tais como o estado do serviço, o número de dispositivos ligados para o serviço, a localização do serviço e os detalhes da subscrição que estão associados com o serviço. Também é uma ligação para o registo de operações. Clique na ligação para ver uma lista de todas as operações de serviço StorSimple Manager foi concluída.

Pode utilizar a página de dashboard do serviço StorSimple Manager para iniciar as seguintes tarefas:

* Ver ou voltar a gerar a chave de registo do serviço.
* Altere a chave de encriptação de dados do serviço.
* Ver os registos de operações.

## <a name="view-or-regenerate-the-service-registration-key"></a>Ver ou voltar a gerar a chave de registo do serviço
A chave de registo do serviço é utilizada para registar um dispositivo de Microsoft Azure StorSimple com o serviço StorSimple Manager, para que o dispositivo é apresentado no portal clássico do Azure para obter ações de gestão. A chave é criada no primeiro dispositivo e partilhada com o resto dos seus dispositivos.

Ao clicar em **chave de registo** (na parte inferior da página) abre o **chave de registo do serviço** caixa de diálogo, onde pode copiar a chave de registo atual do serviço para a área de transferência ou voltar a gerar o chave de registo do serviço.

A regenerar a chave não afeta dispositivos anteriormente registados: que isso afeta apenas os dispositivos registados com o serviço depois da chave for regenerada.

Para obter mais informações sobre a visualização e gerar a chave de registo do serviço, visite [obter a chave de registo do serviço](storsimple-manage-service.md#get-the-service-registration-key).

## <a name="change-the-service-data-encryption-key"></a>Alterar a chave de encriptação de dados do serviço
Chaves de encriptação de dados de serviço são utilizadas para encriptar dados de cliente confidencial, tais como credenciais de conta de armazenamento, que são enviados do serviço StorSimple Manager para o dispositivo StorSimple. Terá de alterar estas chaves periodicamente se a organização de TI tem uma política de rotação da chave nos dispositivos de armazenamento. O processo de alteração de chave pode ser ligeiramente diferente consoante esteja há um único ou vários dispositivos geridos pelo serviço StorSimple Manager.

Alterar a chave de encriptação de dados do serviço é um processo do passo 3:

1. Utilizar o portal clássico do Azure, autorize um dispositivo para alterar a chave de encriptação de dados do serviço.
2. Com o Windows PowerShell para StorSimple, inicie a alteração de chave de encriptação do serviço dados.
3. Se tiver mais do que um dispositivo StorSimple, atualize a chave de encriptação de dados do serviço nos outros dispositivos.

Os passos seguintes descrevem o processo de rollover de chave de encriptação de dados do serviço.

[!INCLUDE [storsimple-change-data-encryption-key](../../includes/storsimple-change-data-encryption-key.md)]

## <a name="view-the-operations-logs"></a>Consulte os registos de operações
Pode ver os registos de operação clicando na hiperligação de registos de operação disponíveis no **leitura rápida** painel do dashboard. Isto leva-o para a página de serviços de gestão, onde pode filtrar e consulte os registos específicos para o serviço StorSimple Manager.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [resolver problemas de um dispositivo StorSimple](storsimple-troubleshoot-operational-device.md).
* Saiba mais sobre como [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

