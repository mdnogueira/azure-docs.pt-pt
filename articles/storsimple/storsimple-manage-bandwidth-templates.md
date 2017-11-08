---
title: "Gestão de modelos de largura de banda do StorSimple | Microsoft Docs"
description: Descreve como gerir modelos de largura de banda do StorSimple, que permitem controlar o consumo de largura de banda.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 0027b90e-91a5-437d-9bd0-06b05674aa5f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: b426a0fc8e0a6c8615b348d3e832c134be4dfbaf
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>Utilizar o serviço StorSimple Manager para gerir modelos de largura de banda do StorSimple
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os gestores de dispositivo do StorSimple será automaticamente mudada para o novo portal do Azure de acordo com a agenda de preterição. Irá receber uma mensagem de e-mail e uma notificação de movimentação do portal. Este documento também será descontinuado em breve. Para ver a versão deste artigo para o novo portal do Azure, aceda a [utilizar o serviço StorSimple Manager para gerir modelos de largura de banda do StorSimple](storsimple-8000-manage-bandwidth-templates.md). Para quaisquer perguntas sobre a mudança, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
Modelos de largura de banda permitem-lhe configurar a utilização de largura de banda de rede em vários agendamentos de hora do dia para a camada de dados do dispositivo StorSimple para a nuvem.

Com as agendas de limitação de largura de banda, pode:

* Especificar agendamentos personalizados de largura de banda, consoante as utilizações de rede de carga de trabalho.
* Centralizar a gestão e reutilizar as agendas em vários dispositivos de forma fácil e totalmente integrada.

> [!NOTE]
> Esta funcionalidade está disponível apenas para dispositivos físicos StorSimple e não para dispositivos virtuais.
> 
> 

Todos os modelos de largura de banda para o seu serviço são apresentados em formato tabular e contenham as seguintes informações:

* **Nome** – um nome exclusivo atribuído ao modelo de largura de banda quando foi criado.
* **Agenda** – o número de agendas contidas num modelo de largura de banda indicado.
* **Utilizado por** – o número de volumes utilizando os modelos de largura de banda.

Utilizar o serviço StorSimple Manager **configurar** página no portal clássico do Azure para gerir modelos de largura de banda.

Também pode encontrar informações adicionais para ajudar a configurar modelos de largura de banda no:

* Perguntas e respostas sobre modelos de largura de banda
* Melhores práticas para modelos de largura de banda

## <a name="add-a-bandwidth-template"></a>Adicionar um modelo de largura de banda
Execute os seguintes passos para criar um novo modelo de largura de banda.

#### <a name="to-add-a-bandwidth-template"></a>Para adicionar um modelo de largura de banda
1. O serviço StorSimple Manager **configurar** página, clique em **modelo de largura de banda de adicionar/editar**.
2. No **modelo de largura de banda de adicionar/editar** caixa de diálogo:
   
   1. Do **modelo** na lista pendente, selecione **criar nova** para adicionar um novo modelo de largura de banda.
   2. Especifique um nome exclusivo para o modelo de largura de banda.
3. Definir um **agendamento de largura de banda**. Para criar uma agenda:
   
   1. Na lista pendente, selecione os dias da semana que a agenda está configurada para. Pode selecionar vários dias, selecionando as caixas de verificação localizadas antes dos respetivos dias na lista.
   2. Selecione o **todos os dias** opção se a agenda é imposta para o dia todo. Quando esta opção for selecionada, já não pode especificar um **hora de início** ou um **hora de fim**. A agenda de execução de 12:00:00 para 23:59:00.
   3. Na lista pendente, selecione um **hora de início**. Isto é quando começará a agenda.
   4. Na lista pendente, selecione um **hora de fim**. Isto é quando a agenda será interrompida.
      
      > [!NOTE]
      > As agendas de sobreposição não são permitidas. Se as horas de início e de fim causará uma agenda sobreposta, verá uma mensagem de erro para esse efeito.
      > 
      > 
   5. Especifique o **taxa de largura de banda**. Esta é a largura de banda em Megabits por segundo (Mbps) utilizado pelo seu dispositivo StorSimple em operações que envolve a nuvem (carregamentos e transferências). Forneça um número entre 1 e 1000 para este campo.
   6. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). O modelo que criou será adicionado à lista de modelos de largura de banda no serviço **configurar** página.
      
      ![Criar novo modelo de largura de banda](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)
4. Clique em **guardar** na parte inferior da página e, em seguida, clique em **Sim** quando um pedido de confirmação. Isto irá guardar as alterações de configuração que efetuou.

## <a name="edit-a-bandwidth-template"></a>Editar um modelo de largura de banda
Execute os seguintes passos para editar um modelo de largura de banda.

### <a name="to-edit-a-bandwidth-template"></a>Para editar um modelo de largura de banda
1. Clique em **modelo de largura de banda de adicionar/editar**.
2. No **modelo de largura de banda de adicionar/editar** caixa de diálogo:
   
   1. Do **modelo** pendente lista, escolha um modelo de largura de banda existente que pretende modificar.
   2. Conclua as suas alterações. (Pode modificar qualquer uma das definições existentes.)
   3. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Verá o modelo de modificação na lista de modelos de largura de banda na página de configuração de serviço.
3. Para guardar as alterações, clique em **guardar** na parte inferior da página. Clique em **Sim** quando um pedido de confirmação.

> [!NOTE]
> Não ser permitido para guardar as alterações, se a agenda editada sobrepõe-se uma agenda existente no modelo de largura de banda que está a modificar.
> 
> 

## <a name="delete-a-bandwidth-template"></a>Eliminar um modelo de largura de banda
Execute os seguintes passos para eliminar um modelo de largura de banda.

#### <a name="to-delete-a-bandwidth-template"></a>Para eliminar um modelo de largura de banda
1. Na lista de modelos de largura de banda para o seu serviço tabela, selecione o modelo que pretende eliminar. Um ícone Eliminar (**x**) será apresentado à direita Alpine do modelo selecionado. Clique em de **x** ícone para eliminar o modelo.
2. Será solicitado para confirmação. Clique em **OK** para continuar.

Se o modelo está a ser utilizada por quaisquer volumes, não será permitida eliminá-la. Verá uma mensagem de erro a indicar que o modelo está a ser utilizado. Uma caixa de diálogo de mensagem de erro será apresentada a indicar o de que todas as referências para o modelo devem ser removidas.

Pode eliminar todas as referências ao modelo acedendo a **contentores de Volume** página e modificar os contentores de volume que utilizam este modelo para que possam utilizam outro modelo ou utilizam uma definição de largura de banda personalizado ou ilimitados. Quando todas as referências foram removidas, pode eliminar o modelo.

## <a name="use-a-default-bandwidth-template"></a>Utilizar um modelo de largura de banda predefinido
Um modelo de largura de banda predefinido é fornecido e é utilizado pelo contentores de volume para impor controlos de largura de banda quando a nuvem a aceder por predefinição. O modelo predefinido também funciona como uma referência preparada para os utilizadores que criar os seus próprios modelos. Os detalhes deste modelo de predefinição são:

* **Nome** – nights ilimitados e fins de semana
* **Agenda** – uma única agenda desde segunda-feira a sexta-feira que aplica-se numa taxa de largura de banda de 1 Mbps entre 8 AM e as 17: 00 hora do dispositivo. A largura de banda está definida como ilimitada durante o restante período da semana.

O modelo predefinido pode ser editado. A utilização deste modelo (incluindo versões editadas) está registada.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Criar um modelo de largura de banda all-day que inicia no tempo especificado
Siga este procedimento para criar uma agenda que inicia a uma determinada hora e executa todos os dias. No exemplo, a agenda é iniciada à 09: 00 da manhã e executa até 09: 00 da manhã seguinte. É importante ter em atenção que os tempos de início e de fim para uma determinada agenda têm ambos de estar contidos na mesma agenda de 24 horas e não pode abranger vários dias. Se precisar de configurar modelos de largura de banda que abrangem vários dias, terá de utilizar várias agendas (conforme ilustrado no exemplo).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Para criar um modelo de largura de banda all-day
1. Crie uma agenda que começa em 09: 00 da manhã e é executada até à meia-noite.
2. Adicione outra agenda. Configure a agenda de segunda a execução da meia-noite até 09: 00 da manhã.
3. Guarde o modelo de largura de banda.

A agenda composta será, em seguida, inicie num momento à sua escolha e execute all-day.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Perguntas e respostas sobre modelos de largura de banda
**Q**. O que acontece para controlos de largura de banda quando estiver entre as agendas? (Uma agenda terminou e outro ainda não foi iniciado.)

**A**. Nestes casos, irão ser utilizados sem controlos de largura de banda. Isto significa que o dispositivo pode utilizar a largura de banda ilimitada quando a criação de camadas de dados para a nuvem.

**Q**. Pode modificar os modelos de largura de banda num dispositivo offline?

**A**. Não será possível modificar os modelos de largura de banda em contentores de volumes, se o dispositivo correspondente estiver offline.

**Q**. Pode editar um modelo de largura de banda associado um contentor de volume quando os volumes associados estão offline?

**A**. Pode modificar um modelo de largura de banda associado um contentor de volume cujas volumes estão offline. Tenha em atenção que quando volumes estiverem offline, não existem dados irão ser colocado em camadas do dispositivo para a nuvem.

**Q**. Pode eliminar um modelo predefinido?

**A**. Apesar de poder eliminar um modelo predefinido, não é uma boa ideia fazê-lo. A utilização de um modelo predefinido, incluindo versões editadas, está registada. Os dados de controlo são analisados e ao longo do tempo, são utilizados para melhorar o modelo predefinido.

**Q**. Como pode determinar que os modelos de largura de banda tem de ser modificados?

**A**. Um dos sinais que necessitar de modificar os modelos de largura de banda é quando começar a ver rede mais lentos ou choke várias vezes num dia. Se isto acontecer, monitorize a rede de armazenamento e utilização ao observar os gráficos de desempenho de e/s e débito de rede.

Os dados de débito de rede, identifique a hora do dia e os contentores de volume no qual ocorre o congestionamento de rede. Se esta situação acontece quando os dados que estão a ser camados para a nuvem (obter estas informações de desempenho de e/s para todos os contentores de volume do dispositivo para a nuvem), em seguida, terá de modificar os modelos de largura de banda associados os contentores de volume.

Depois dos modelos modificados estão em utilização, terá de monitorizar a rede novamente para latências significativas. Se estes ainda existem, terá de revê os modelos de largura de banda.

**Q**. O que acontece se tem vários contentores de volume no meu dispositivo agendas que sobreposição, mas aplicam limites diferentes para cada?

**A**. Vamos assumir que tem um dispositivo com 3 contentores de volume. As agendas associadas esses contentores completamente sobrepor-se. Para cada um destes contentores, os limites de largura de banda utilizados são 5, 10 a 15 Mbps, respetivamente. Quando as e/s estão a ocorrer em todos os destes contentores ao mesmo tempo, o mínimo dos 3 limites de largura de banda pode ser aplicado: neste caso, 5 Mbps como estes pedidos de e/s de saída partilham a mesma fila.

## <a name="best-practices-for-bandwidth-templates"></a>Melhores práticas para modelos de largura de banda
Siga estas práticas recomendadas para o dispositivo StorSimple:

* Configure modelos de largura de banda no seu dispositivo para ativar a variável da limitação do débito de rede pelo dispositivo em alturas diferentes do dia. Estes modelos de largura de banda quando utilizado com as agendas de cópia de segurança eficazmente podem tirar partido da largura de banda de rede adicionais para operações de nuvem durante as horas de ponta.
* Calcule a largura de banda realmente necessária para uma implementação específica, com base no tamanho da implementação e o objetivo de tempo de recuperação necessários (RTO).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [utilizando o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

