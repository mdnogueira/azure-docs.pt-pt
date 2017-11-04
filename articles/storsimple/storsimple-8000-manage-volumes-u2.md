---
title: "Gerir volumes do StorSimple (atualização 3) | Microsoft Docs"
description: "Explica como adicionar, modificar, monitorizar e eliminar os volumes do StorSimple e como colocar offline se necessário."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: alkohli
ms.openlocfilehash: 09f4de79ab9b0cdfafd10c7c7c29b0f8e6304f14
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Utilizar o serviço StorSimple Manager de dispositivos para gerir volumes (atualização 3 ou posterior)

## <a name="overview"></a>Descrição geral

Este tutorial explica como utilizar o serviço do Gestor de dispositivos do StorSimple para criar e gerir volumes nos dispositivos de série 8000 do StorSimple com o Update 3 e posterior.

O serviço do Gestor de dispositivos do StorSimple é uma extensão no portal do Azure permite-lhe gerir solução StorSimple a partir de uma interface web único. Utilize o portal do Azure para gerir volumes em todos os seus dispositivos. Também pode criar e gerir serviços StorSimple, gerir dispositivos, as políticas de cópia de segurança e o catálogo de cópias de segurança e ver alertas.

## <a name="volume-types"></a>Tipos de volume

Volumes do StorSimple podem ser:

* **Afixado localmente volumes**: dados destes volumes permanecem no dispositivo StorSimple local permanente.
* **Volumes em camadas**: dados destes volumes podem transbordam para a nuvem.

Um volume de arquivo é um tipo de volume em camadas. O tamanho de segmento de eliminação de duplicados maior utilizado para volumes de arquivo permite que o dispositivo de transferência maior segmentos de dados para a nuvem.

Se necessário, pode alterar o volume de tipo de locais em camadas ou de camadas para local. Para obter mais informações, aceda a [alterar o tipo de volume](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Volumes localmente afixados

Volumes localmente afixados são totalmente aprovisionados volumes que não os dados de camada para a nuvem, assegurando local garante para dados primários, independentemente da conectividade de nuvem. Em volumes localmente afixados não é eliminação de duplicados e dados comprimidos; No entanto, os instantâneos de volumes localmente afixados tenham os duplicados eliminados. 

Volumes localmente afixados são totalmente aprovisionados; Por conseguinte, tem de ter espaço suficiente no seu dispositivo quando criá-los. Pode aprovisionar volumes localmente afixados até um tamanho máximo de 8 TB no dispositivo StorSimple 8100 e 20 TB no dispositivo 8600. StorSimple reserva-se o espaço local restante no dispositivo para instantâneos, metadados e o processamento de dados. Pode aumentar o tamanho de um volume afixado localmente para o espaço máximo disponível, mas não é possível reduzir o tamanho de um volume depois de criado.

Quando cria um volume localmente afixado, o espaço disponível para a criação de volumes em camadas é reduzido. Também se aplica a reversão: Se tiver de volumes em camadas existentes, o espaço disponível para criar localmente afixado volumes será mais baixo do que o máximos limites indicados acima. Para obter mais informações sobre volumes locais, consulte o [perguntas mais frequentes em volumes localmente afixados](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Volumes em camadas

Volumes em camadas são volumes de aprovisionamento dinâmico no qual os dados acedidos com frequência permanecem locais no dispositivo e menos utilizados frequentemente dados é automaticamente em camadas para a nuvem. Aprovisionamento dinâmico é uma tecnologia de virtualização em que o armazenamento disponível aparece exceder os recursos físicos. Em vez de reservar antecipadamente armazenamento suficiente, StorSimple utiliza o aprovisionamento dinâmico para alocar espaço apenas suficiente para satisfazer os requisitos atuais. A natureza elástica do armazenamento na nuvem facilita esta abordagem porque StorSimple pode aumentar ou diminuir o armazenamento na nuvem para satisfazer os requisitos evolutivos.

Se estiver a utilizar o volume em camadas para dados de arquivo, selecione o **utilizar este volume para menos dados de arquivo acedidos com frequência** caixa de verificação para alterar o tamanho de segmentos de eliminação de duplicados para o volume para 512 KB. Se não selecionar esta opção, o volume em camadas correspondente irá utilizar um tamanho de segmentos de 64 KB. Um tamanho de segmentos de eliminação de duplicados maior permite ao dispositivo agilizar a transferência de dados de arquivo de grandes dimensões para a nuvem.


### <a name="provisioned-capacity"></a>Capacidade de aprovisionamento

Consulte a tabela seguinte para a capacidade máxima de aprovisionado para cada tipo de dispositivo e de volume. (Tenha em atenção de que os volumes localmente afixados não estão disponíveis num dispositivo virtual.)

|  | Tamanho máximo de volume em camadas | Tamanho do volume de afixado localmente máximo |
| --- | --- | --- |
| **Dispositivos físicos** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Dispositivos virtuais** | | |
| 8010 |30 TB |N/D |
| 8020 |64 TB |N/D |

## <a name="the-volumes-blade"></a>O painel de volumes

O **Volumes** painel permite-lhe gerir os volumes de armazenamento que estão aprovisionados no dispositivo Microsoft Azure StorSimple para os iniciadores (servidores). Apresenta a lista de volumes nos dispositivos StorSimple ligados ao seu serviço.

 ![Página de volumes](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Um volume é constituída por uma série de atributos:

* **Nome do volume** – um nome descritivo que têm de ser exclusivos e ajuda a identificar o volume. Este nome também é utilizado em relatórios de monitorização ao filtrar num volume específico. Assim que o volume é criado, não pode ser mudado.
* **Estado** – pode ser online ou offline. Se um volume estiver offline, não é visível para os iniciadores (servidores) que estão a permissão para aceder ao utilizar o volume.
* **Capacidade** – Especifica a quantidade total de dados que podem ser armazenados pelo iniciador (servidor). Volumes localmente afixado são totalmente aprovisionados e residem no dispositivo StorSimple. Volumes em camadas são fracamente aprovisionados e tem eliminação de duplicados de dados. Com volumes de aprovisionamento dinâmico, o dispositivo não previamente alocar a capacidade de armazenamento físico internamente ou na nuvem de acordo com a capacidade do volume configurado. A capacidade do volume está atribuída e consumida a pedido.
* **Tipo** – indica se o volume é **em camadas** (predefinição) ou **afixado localmente**.

Utilize as instruções neste tutorial para realizar as seguintes tarefas:

* Adicionar um volume 
* Modificar um volume 
* Alterar o tipo de volume
* Eliminar um volume 
* Colocar offline um volume 
* Monitorizar um volume 

## <a name="add-a-volume"></a>Adicionar um volume

[Criado um volume](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) durante a implementação do seu dispositivo de série 8000 do StorSimple. A adição de um volume é um procedimento semelhante.

#### <a name="to-add-a-volume"></a>Para adicionar um volume

1. A partir da lista tabular de dispositivos no painel **Dispositivos**, selecione o seu dispositivo. Clique em **+ Adicionar volume**.

    ![Adicionar um novo volume](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. No painel **Adicionar um volume**:
   
    1. O campo **Selecionar dispositivo** é preenchido automaticamente com o seu dispositivo atual.

    2. Na lista pendente, selecione o contentor de volumes onde precisa de adicionar um volume.

    3.  Escreva um **Nome** para o volume. Assim que o volume é criado, não é possível mudar o nome de volume.

    4. Na lista pendente, selecione o **Tipo** do volume. Para cargas de trabalho que necessitem de garantias locais, latências baixas e desempenho superior, selecione um volume **Afixado localmente**. Para todos os outros dados, selecione um volume **Em camadas**. Se estiver a utilizar este volume para os dados de arquivo, marque **Utilizar este volume para dados de arquivo acedidos com menos frequência**.
      
       Um volume em camadas é fracamente aprovisionado e pode ser criado rapidamente. Selecionar **Utilizar este volume para dados de arquivo acedidos com menos frequência** para o volume em camadas direcionado para dados de arquivo altera o tamanho dos segmentos de eliminação de duplicados do volume para 512 KB. Se este campo não estiver selecionado, o volume em camadas correspondente utiliza um tamanho do segmento de 64 KB. Um tamanho de segmentos de eliminação de duplicados maior permite ao dispositivo agilizar a transferência de dados de arquivo de grandes dimensões para a nuvem.
       
       Um volume localmente afixado é fortemente aprovisionado e assegura que os dados primários no volume permanecem locais no dispositivo e não transbordam para a nuvem.  Se criar um volume localmente afixado, o dispositivo verifica o espaço disponível nas camadas locais para aprovisionar o volume do tamanho necessário. A operação de criação de um volume localmente afixado pode envolver transbordar dados existentes do dispositivo para a nuvem e o tempo necessário para criar o volume poderá ser longo. O tempo total depende do tamanho do volume aprovisionado, da largura de banda de rede disponível e dos dados no dispositivo.

    5. Especifique a **Capacidade Aprovisionada** para o volume. Tome nota da capacidade que está disponível, consoante o tipo de volume selecionado. O tamanho do volume especificado não pode exceder o espaço disponível.
      
       Pode aprovisionar volumes localmente afixados até 8,5 TB ou volumes em camadas até 200 TB no dispositivo 8100. No dispositivo 8600 maior, pode aprovisionar volumes localmente afixados até 22,5 TB ou volumes em camadas até 500 TB. Como é preciso espaço local no dispositivo para alojar o conjunto de trabalho de volumes em camadas, a criação de volumes localmente afixados tem impacto no espaço disponível para o aprovisionamento de volumes em camadas. Por conseguinte, se criar um volume afixado localmente, é reduzido o espaço disponível para criação de volumes em camadas. Do mesmo modo, se for criado um volume em camadas, o espaço disponível para a criação de volumes localmente afixados é reduzido.
      
       Se aprovisionar um volume localmente afixado de 8,5 TB (tamanho máximo admissível) no dispositivo 8100, terá esgotado todo o espaço local disponível no dispositivo. Não pode criar nenhum volume escalonado a partir desse ponto, uma vez que não há espaço local no dispositivo para alojar o conjunto de trabalho do volume escalonado. Os volumes em camadas existentes também afetam o espaço disponível. Por exemplo, se tiver um dispositivo 8100 que já tem volumes em camadas de, aproximadamente, 106 TB, está disponível apenas um espaço de 4 TB para volumes afixados localmente.

    6. No campo **Anfitriões ligados**, clique na seta. 

        ![Anfitriões ligados](./media/storsimple-8000-manage-volumes-u2/step5createvol2.png)

    7. No **ligados a anfitriões** painel, escolha um ACR existente ou adicionar um novo ACR. Se escolher uma nova ACR, em seguida, forneça um **nome** para o ACR, forneça o **iSCSI Qualified nome** (IQN) de anfitrião do Windows. Se não tiver o IQN, veja [Get the IQN of a Windows Server host (Obter o IQN de um anfitrião do Windows Server)](#get-the-iqn-of-a-windows-server-host). Clique em **Criar**. É criado um volume com as definições especificadas.

        ![Clique em Criar](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

O novo volume agora está pronto a utilizar.

> [!NOTE]
> Se criar um volume localmente afixado e, em seguida, criar outro volume localmente afixado imediatamente posteriormente, a criação do volume tarefas são executados sequencialmente. A tarefa de criação do volume primeiro deve ser concluído antes de pode começar a próxima tarefa de criação do volume.

## <a name="modify-a-volume"></a>Modificar um volume

Modificar um volume quando precisa de expandir ou alterar os anfitriões que o volume de acesso.

> [!IMPORTANT]
> * Se modificar o tamanho do volume no dispositivo, tem de ser alterada no anfitrião, bem como o tamanho do volume.
> * Os passos do lado do anfitrião descritos aqui são para o Windows Server 2012 (2012R2). Procedimentos para Linux ou outros sistemas operativos de anfitrião será diferentes. Consulte as instruções de sistema operativo do anfitrião ao modificar o volume de um anfitrião com outro sistema operativo.

#### <a name="to-modify-a-volume"></a>Para modificar um volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lista tabela dos dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **definições > Volumes**.

    ![Aceda ao painel de Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. A partir de listagem tabular de volumes, selecione o volume e faça duplo clique para invocar o menu de contexto. Selecione **colocar offline** para colocar offline o volume vai modificar.

    ![Selecione e coloque o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. No **colocar offline** painel, reveja o impacto de colocar offline o volume e selecione a caixa de verificação correspondente. Certifique-se de que o volume correspondente no anfitrião está offline pela primeira vez. Para obter informações sobre como colocar offline um volume no seu servidor de anfitrião ligado ao StorSimple, consulte as instruções específicas do sistema operativo. Clique em **colocar offline**.

    ![Reveja o impacto de colocar offline o volume](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Depois do volume estiver offline (conforme exibido o estado do volume), selecione o volume e faça duplo clique para invocar o menu de contexto. Selecione **modificar volume**.

    ![Selecione modificar volume](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. No **modificar volume** painel, pode efetuar as seguintes alterações:
   
   1. O volume **nome** não pode ser editado.
   2. Converter o **tipo** de afixado localmente para camadas ou de camadas para afixado localmente (consulte [alterar o tipo de volume](#change-the-volume-type) para obter mais informações).
   3. Aumentar a **aprovisionado capacidade**. O **capacidade aprovisionada** só pode ser aumentada. Não é possível encolher o volume depois de criado.
   4. Em **ligados a anfitriões**, pode modificar o ACR. Para modificar um ACR, o volume tem de ser offline.

       ![Reveja o impacto de colocar offline o volume](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Clique em **guardar** para guardar as alterações. Quando lhe for pedida a confirmação, clique em **Sim**. O portal do Azure irá apresentar uma mensagem de volume de atualização. Apresentará uma mensagem de êxito quando o volume tiver sido atualizado com êxito.

    ![Reveja o impacto de colocar offline o volume](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Se está a expandir um volume, conclua os seguintes passos no seu computador de anfitrião do Windows:
   
   1. Aceda a **gestão de computadores** ->**disco gestão**.
   2. Clique com botão direito **gestão de discos** e selecione **reanalisar discos**.
   3. Na lista de discos, selecione o volume que tenha atualizado, rato e, em seguida, selecione **Expandir Volume**. O Assistente de expandir o Volume é iniciado. Clique em **Seguinte**.
   4. Conclua o assistente, aceitando os valores predefinidos. Depois de concluído o assistente, o volume deve mostrar o tamanho maior.
      
      > [!NOTE]
      > Se expandir um volume afixado localmente e, em seguida, expanda outro localmente afixado volume imediatamente posteriormente, as tarefas de expansão de volume são executados sequencialmente. A tarefa de expansão de volume primeiro deve ser concluído antes de pode começar a próxima tarefa de expansão de volume.
      

## <a name="change-the-volume-type"></a>Alterar o tipo de volume

Pode alterar o tipo de volume em camadas para afixado localmente ou de afixado localmente a em camadas. No entanto, esta conversão não deve ser uma ocorrência frequente.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Camadas de considerações de conversão de local volume

Algumas razões para converter um volume de camadas para afixado localmente são:

* Garantias locais sobre desempenho e disponibilidade de dados
* Eliminação de latências de nuvem e de problemas de conectividade de nuvem.

Normalmente, estes são pequenos volumes existentes que pretende aceder com frequência. Um volume localmente afixado é totalmente aprovisionado quando é criado. 

Se estiver a converter um volume em camadas para um volume localmente afixado, StorSimple verifica que tem espaço suficiente no seu dispositivo antes de iniciar a conversão. Se tiver espaço suficiente, receberá um erro e a operação, será cancelada. 

> [!NOTE]
> Antes de iniciar uma conversão de camadas para afixado localmente, certifique-se de que considerar os requisitos de espaço das outras cargas de trabalho. 

A conversão de um em camadas para um volume localmente afixado pode afetar negativamente o desempenho do dispositivo. Além disso, os seguintes fatores podem aumentar o tempo que demora a concluir a conversão:

* Não há largura de banda suficiente.
* Não há nenhuma cópia de segurança atual.

Para minimizar os efeitos que possam ter estes fatores:

* Reveja a políticas de limitação de largura de banda e certifique-se de que uma largura de banda Mbps dedicado 40 está disponível.
* Agende a conversão para o horário de pico.
* Tire um instantâneo de nuvem antes de iniciar a conversão.

Se estiver a converter vários volumes (suportar cargas de trabalho diferentes), em seguida, deve atribuir prioridades a conversão de volume para que os volumes de prioridade superiores são convertidos em primeiro. Por exemplo, deve converter volumes que alojam máquinas virtuais (VMs) ou volumes com cargas de trabalho do SQL Server antes de converter volumes com cargas de trabalho de partilha de ficheiros.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Local considerações de conversão do volume em camadas

Poderá pretender alterar um volume localmente afixado para um volume em camadas, se precisar de espaço adicional para aprovisionar outros volumes. Quando converter o volume localmente afixado de camadas, a capacidade disponível no aumenta dispositivo pelo tamanho da capacidade de lançamento. Se os problemas de conectividade impedirem a conversão de um volume do tipo de local para o tipo em camadas, o volume local plantas propriedades de um volume em camadas até que a conversão esteja concluída. Isto acontece porque poderão ter spilled alguns dados na nuvem. Estes dados spilled continuam a ocupam espaço local no dispositivo que não pode ser libertado enquanto a operação é reiniciada e concluída.

> [!NOTE]
> Converter um volume pode demorar algum tempo e não é possível cancelar uma conversão depois desta ser iniciada. O volume permanece online durante a conversão e pode fazer cópias de segurança, mas não é possível expandir ou restaurar o volume enquanto decorre a conversão.


#### <a name="to-change-the-volume-type"></a>Para alterar o tipo de volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lista tabela dos dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **definições > Volumes**.

    ![Aceda ao painel de Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. A partir de listagem tabular de volumes, selecione o volume e faça duplo clique para invocar o menu de contexto. Selecione **modificar**.

    ![Selecione modificar a partir do menu de contexto](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. No **modificar volume** painel, alterar o tipo de volume ao selecionar o tipo de novo o **tipo** na lista pendente.
   
   * Se estiver a alterar o tipo para **afixado localmente**, StorSimple irá verificar para ver se existe suficiente capacidade.
   * Se estiver a alterar o tipo para **em camadas** e este volume será utilizado para dados de arquivo, selecionados o **utilizar este volume para menos dados de arquivo acedidos com frequência** caixa de verificação.
   * Se estiver a configurar um volume localmente afixado como camadas ou _vice-versa-se_, é apresentada a mensagem seguinte.
   
    ![Mensagem de tipo de volume de alterações](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Clique em **Guardar** para guardar as alterações. Quando lhe for pedido para confirmação, clique em **Sim** para iniciar o processo de conversão. 

    ![Guarde e confirmar](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. O portal do Azure apresenta uma notificação para a criação da tarefa que atualizará o volume. Clique na notificação para monitorizar o estado da tarefa de conversão de volume.

    ![Tarefa de conversão de volume](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Colocar offline um volume

Poderá ter de colocar offline um volume quando estiver a planear modificar ou eliminar o volume. Quando um volume estiver offline, não está disponível para acesso de leitura e escrita. Tem de colocar offline o volume no anfitrião e o dispositivo.

#### <a name="to-take-a-volume-offline"></a>Para colocar offline um volume

1. Certifique-se de que o volume em questão não está a ser utilizado antes de colocar-offline.
2. Colocar offline o volume anfitrião primeiro. Isto elimina a qualquer potencial risco de danos nos dados no volume. Para obter passos específicos, consulte as instruções para o seu sistema operativo do anfitrião.
3. Depois do anfitrião estiver offline, colocar o volume no dispositivo offline, efetuando os seguintes passos:
   
    1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lista tabela dos dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **definições > Volumes**.

        ![Aceda ao painel de Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. A partir de listagem tabular de volumes, selecione o volume e faça duplo clique para invocar o menu de contexto. Selecione **colocar offline** para colocar offline o volume vai modificar.

        ![Selecione e coloque o volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. No **colocar offline** painel, reveja o impacto de colocar offline o volume e selecione a caixa de verificação correspondente. Clique em **colocar offline**. 

    ![Reveja o impacto de colocar offline o volume](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Será notificado quando o volume está offline. Também atualiza o estado do volume para Offline.
      
4. Depois de um volume estiver offline, se selecionar o volume e o rato, **colocar Online** opção torna-se disponíveis no menu de contexto.

> [!NOTE]
> O **Colocar Offline** comando envia um pedido para o dispositivo para colocar offline o volume. Se anfitriões ainda estiver a utilizar o volume, isto resulta em ligações quebradas, mas colocar offline o volume não irá falhar.

## <a name="delete-a-volume"></a>Eliminar um volume

> [!IMPORTANT]
> Pode eliminar um volume apenas se estiver offline.

Conclua os seguintes passos para eliminar um volume.

#### <a name="to-delete-a-volume"></a>Para eliminar um volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lista tabela dos dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **definições > Volumes**.

    ![Aceda ao painel de Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Verifique o estado do volume que pretende eliminar. Se o volume que pretende eliminar não estiver offline, coloque-offline pela primeira vez. Siga os passos no [colocar offline um volume](#take-a-volume-offline).
4. Depois do volume estiver offline, selecione o volume, faça duplo clique para invocar o menu de contexto e, em seguida, selecione **eliminar**.

    ![Selecione eliminação a partir do menu de contexto](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. No **eliminar** painel, rever e selecionar a caixa de verificação contra o impacto da eliminação de um volume. Quando elimina um volume, todos os dados que reside no volume é perdido. 

    ![Guarde e confirmar as alterações](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Depois do volume é eliminado, atualiza a lista de tabela de volumes para indicar a eliminação.

    ![Lista de volume atualizado](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Se eliminar um volume localmente afixado, o espaço disponível para novos volumes não pode ser atualizado imediatamente. O serviço do Gestor de dispositivos do StorSimple atualiza periodicamente o espaço local disponível. Sugerimos que aguarde alguns minutos antes de tentar criar o novo volume.
   >
   > Além disso, se eliminar um volume afixado localmente e, em seguida, elimine o outro localmente afixado volume imediatamente posteriormente, a eliminação de volume, as tarefas são executadas sequencialmente. A tarefa de eliminação do volume primeiro deve ser concluído antes de pode começar a próxima tarefa de eliminação do volume.

## <a name="monitor-a-volume"></a>Monitorizar um volume

Monitorização de volume permite-lhe recolher estatísticas de I/O-relacionadas com para um volume. Monitorização é ativada por predefinição para os 32 pela primeira vez volumes que criar. Monitorização de volumes adicionais está desativada por predefinição. 

> [!NOTE]
> Monitorização de volumes clonados está desativada por predefinição.


Execute os seguintes passos para ativar ou desativar a monitorização para um volume.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Para ativar ou desativar a monitorização de volume

1. Aceda ao seu serviço do Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lista tabela dos dispositivos, selecione o dispositivo que tem o volume que pretende modificar. Clique em **definições > Volumes**.
2. A partir de listagem tabular de volumes, selecione o volume e faça duplo clique para invocar o menu de contexto. Selecione **modificar**.
3. No **modificar volume** painel, para **monitorização** selecione **ativar** ou **desativar** para ativar ou desativar a monitorização.

    ![Desativar a monitorização](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Clique em **guardar** e quando for pedida a confirmação, clique em **Sim**. O portal do Azure apresenta uma notificação para atualizar o volume e, em seguida, uma mensagem de êxito, depois do volume foi atualizado com êxito.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [clonar um volume StorSimple](storsimple-8000-clone-volume-u2.md).
* Saiba como [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

