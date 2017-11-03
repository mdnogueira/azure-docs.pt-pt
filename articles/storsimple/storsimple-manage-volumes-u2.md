---
title: Gerir os volumes do StorSimple (U2) | Microsoft Docs
description: "Explica como adicionar, modificar, monitorizar e eliminar os volumes do StorSimple e como colocar offline se necessário."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 57896932-0aa5-4805-970c-d13403ae7551
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/28/2016
ms.author: alkohli
ms.openlocfilehash: a61c57cd74a0df8363648dd8df40e433b0e6489d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-volumes-update-2"></a>Utilizar o serviço StorSimple Manager para gerir volumes (atualização 2)
[!INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Descrição geral
Este tutorial explica como utilizar o serviço StorSimple Manager para criar e gerir volumes no dispositivo StorSimple e dispositivo virtual StorSimple com o Update 2 instalado.

O serviço StorSimple Manager é uma extensão no portal clássico do Azure que permite-lhe gerir solução StorSimple a partir de uma interface web único. Além da gestão de volumes, pode utilizar o serviço StorSimple Manager para criar e gerir serviços StorSimple, ver e gerir dispositivos, ver alertas e ver e gerir políticas de cópia de segurança e o catálogo de cópias de segurança.

## <a name="volume-types"></a>Tipos de volume
Volumes do StorSimple podem ser:

* **Afixado localmente volumes**: dados destes volumes permanecem no dispositivo StorSimple local permanente.
* **Volumes em camadas**: dados destes volumes podem transbordam para a nuvem.

Um volume de arquivo é um tipo de volume em camadas. O tamanho de segmento de eliminação de duplicados maior utilizado para volumes de arquivo permite que o dispositivo de transferência maior segmentos de dados para a nuvem. 

Se necessário, pode alterar o volume de tipo de locais em camadas ou de camadas para local. Para obter mais informações, aceda a [alterar o tipo de volume](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Volumes localmente afixados
Volumes localmente afixados são totalmente aprovisionados volumes que não os dados de camada para a nuvem, assegurando local garante para dados primários, independentemente da conectividade de nuvem. Em volumes localmente afixados não é eliminação de duplicados e dados comprimidos; No entanto, os instantâneos de volumes localmente afixados tenham os duplicados eliminados. 

Volumes localmente afixados são totalmente aprovisionados; Por conseguinte, tem de ter espaço suficiente no seu dispositivo quando criá-los. Pode aprovisionar volumes localmente afixados até um tamanho máximo de 8 TB no dispositivo StorSimple 8100 e 20 TB no dispositivo 8600. StorSimple reserva-se o espaço local restante no dispositivo para instantâneos, metadados e o processamento de dados. Pode aumentar o tamanho de um volume afixado localmente para o espaço máximo disponível, mas não é possível reduzir o tamanho de um volume depois de criado.

Quando cria um volume localmente afixado, o espaço disponível para a criação de volumes em camadas é reduzido. Também se aplica a reversão: Se tiver de volumes em camadas existentes, o espaço disponível para criar localmente afixado volumes será mais baixo do que o máximos limites indicados acima. Para obter mais informações sobre volumes locais, consulte o [perguntas mais frequentes em volumes localmente afixados](storsimple-local-volume-faq.md).   

### <a name="tiered-volumes"></a>Volumes em camadas
Volumes em camadas são volumes de aprovisionamento dinâmico no qual os dados acedidos com frequência permanecem locais no dispositivo e menos utilizados frequentemente dados é automaticamente em camadas para a nuvem. Aprovisionamento dinâmico é uma tecnologia de virtualização em que o armazenamento disponível aparece exceder os recursos físicos. Em vez de reservar antecipadamente armazenamento suficiente, StorSimple utiliza o aprovisionamento dinâmico para alocar espaço apenas suficiente para satisfazer os requisitos atuais. A natureza elástica do armazenamento na nuvem facilita esta abordagem porque StorSimple pode aumentar ou diminuir o armazenamento na nuvem para satisfazer os requisitos evolutivos.

Se estiver a utilizar o volume em camadas para dados de arquivo, selecionar o **utilizar este volume para menos dados de arquivo acedidos com frequência** caixa de verificação altera o tamanho de segmentos de eliminação de duplicados para o volume para 512 KB. Se não selecionar esta opção, o volume em camadas correspondente irá utilizar um tamanho de segmentos de 64 KB. Um tamanho de segmentos de eliminação de duplicados maior permite ao dispositivo agilizar a transferência de dados de arquivo de grandes dimensões para a nuvem.

> [!NOTE]
> Arquivo volumes criados com versão anterior à atualização 2 do StorSimple serão importados como em camadas com a caixa de verificação arquivo selecionada.
> 
> 

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

## <a name="the-volumes-page"></a>A página de Volumes
O **Volumes** página permite-lhe gerir os volumes de armazenamento que estão aprovisionados no dispositivo Microsoft Azure StorSimple para os iniciadores (servidores). Apresenta a lista de volumes no dispositivo StorSimple.

 ![Página de volumes](./media/storsimple-manage-volumes-u2/VolumePage.png)

Um volume é constituída por uma série de atributos:

* **Nome do volume** – um nome descritivo que têm de ser exclusivos e ajuda a identificar o volume. Este nome também é utilizado em relatórios de monitorização ao filtrar num volume específico.
* **Estado** – pode ser online ou offline. Se um volume estiver offline, não é visível para os iniciadores (servidores) que estão a permissão para aceder ao utilizar o volume.
* **Capacidade** – Especifica a quantidade total de dados que podem ser armazenados pelo iniciador (servidor). Volumes localmente afixado são totalmente aprovisionados e residem no dispositivo StorSimple. Volumes em camadas são fracamente aprovisionados e tem eliminação de duplicados de dados. Com volumes de aprovisionamento dinâmico, o dispositivo não previamente alocar a capacidade de armazenamento físico internamente ou na nuvem de acordo com a capacidade do volume configurado. A capacidade do volume está atribuída e consumida a pedido.
* **Tipo** – indica se o volume é **em camadas** (predefinição) ou **afixado localmente**.
* **Cópia de segurança** – indica se uma política de cópia de segurança predefinida existe para o volume.
* **Acesso** – Especifica os iniciadores (servidores) que têm permissão de acesso a este volume. Os iniciadores que não sejam membros de registo de controlo de acesso (ACR) que estão associados com o volume não irão ver o volume.
* **Monitorização** – Especifica se pretende ou não está a ser monitorizado um volume. Um volume terão monitorização ativada por predefinição, quando é criado. Monitorização irá, no entanto, ser desativado para um clone de volume. Para ativar a monitorização de um volume, siga as instruções em [monitorizar um volume](#monitor-a-volume). 

Utilize as instruções neste tutorial para realizar as seguintes tarefas:

* Adicionar um volume 
* Modificar um volume 
* Alterar o tipo de volume
* Eliminar um volume 
* Colocar offline um volume 
* Monitorizar um volume 

## <a name="add-a-volume"></a>Adicionar um volume
[Criado um volume](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) durante a implementação da solução StorSimple. A adição de um volume é um procedimento semelhante.

#### <a name="to-add-a-volume"></a>Para adicionar um volume
1. No **dispositivos** página, selecione o dispositivo, faça duplo clique e, em seguida, clique em de **contentores de Volume** separador.
2. Selecione um contentor de volume na lista e faça duplo clique para aceder aos volumes associados ao contentor.
3. Clique em **adicionar** na parte inferior da página. Adicionar um Assistente de volume é iniciado.
   
     ![Adicionar Assistente de volume definições básicas](./media/storsimple-manage-volumes-u2/TieredVolEx.png)
4. No assistente Adicionar um volume, em **Definições Básicas**, faça o seguinte:
   
   1. Forneça um **Nome** para o volume.
   2. Selecione um **tipo de utilização** na lista pendente. Para cargas de trabalho que necessitam de dados disponíveis localmente no dispositivo permanente, selecione **afixado localmente**. Para todos os outros tipos de dados, selecione **em camadas**. (**Em camadas** é a predefinição.)
   3. Se tiver selecionado **em camadas** no passo 2, pode selecionar o **utilizar este volume para menos dados de arquivo acedidos com frequência** caixa de verificação para configurar um arquivo volume.
   4. Introduza o **capacidade aprovisionada** para o volume em GB ou TB. Consulte [aprovisionado capacidade](#provisioned-capacity) para tamanhos máximos para cada tipo de dispositivo e de volume. Observe o **capacidade disponível** para determinar a quantidade de armazenamento está, efetivamente, disponível no seu dispositivo.
5. Clique no ícone de seta![Ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Se estiver a configurar um volume localmente afixado, verá a mensagem seguinte.
   
    ![Alterar a mensagem do tipo de Volume](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
6. Clique no ícone de seta ![ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png)novamente para ir para o **definições adicionais** página.
   
    ![Adicionar definições adicionais do Assistente de Volume](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>
7. Em **definições adicionais**, adicione um novo registo de controlo de acesso (ACR):
   
   1. Selecione um registo de controlo de acesso (ACR) na lista pendente. Em alternativa, pode adicionar um novo ACR. ACRs determinar os anfitriões que podem aceder a sua volumes por correspondente anfitrião IQN com que listados no registo. Se não especificar um ACR, verá a mensagem seguinte.
      
        ![Especifique o ACR](./media/storsimple-manage-volumes-u2/SpecifyACR.png)
   2. Recomendamos que selecione o **ativar uma cópia de segurança predefinido para este volume** caixa de verificação.
   3. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) Para criar o volume com as definições especificadas.

O novo volume agora está pronto a utilizar.

> [!NOTE]
> Se criar um volume localmente afixado e, em seguida, criar outro volume localmente afixado imediatamente posteriormente, a criação do volume tarefas são executados sequencialmente. A tarefa de criação do volume primeiro deve ser concluído antes de pode começar a próxima tarefa de criação do volume.
> 
> 

## <a name="modify-a-volume"></a>Modificar um volume
Modificar um volume quando precisa de expandir ou alterar os anfitriões que o volume de acesso.

> [!IMPORTANT]
> * Se modificar o tamanho do volume no dispositivo, tem de ser alterada no anfitrião, bem como o tamanho do volume. 
> * Os passos do lado do anfitrião descritos aqui são para o Windows Server 2012 (2012R2). Procedimentos para Linux ou outros sistemas operativos de anfitrião será diferentes. Consulte as instruções de sistema operativo do anfitrião ao modificar o volume de um anfitrião com outro sistema operativo. 
> 
> 

#### <a name="to-modify-a-volume"></a>Para modificar um volume
1. No **dispositivos** página, selecione o dispositivo, faça duplo clique e, em seguida, clique em de **contentores de Volume** separador.
2. Selecione um contentor de volume na lista e faça duplo clique para ver os volumes associados ao contentor.
3. Selecione um volume e, na parte inferior da página, clique em **modificar**. Inicia o Assistente de volume modificar.
4. No Assistente de volume modificar, sob **definições básicas**, pode fazer o seguinte:
   
   * Editar o **nome**.
   * Converter o **tipo de utilização** de afixado localmente para camadas ou de camadas para afixado localmente (consulte [alterar o tipo de volume](#change-the-volume-type) para obter mais informações).
   * Aumentar a **aprovisionado capacidade**. O **capacidade aprovisionada** só pode ser aumentada. Não é possível encolher o volume depois de criado.
5. Em **definições adicionais**, pode modificar o ACR, desde que o volume está offline. Se o volume estiver online, terá de levá-los offline pela primeira vez. Consulte os passos descritos para [colocar offline um volume](#take-a-volume-offline) antes de modificar o ACR.
   
   > [!NOTE]
   > Não é possível alterar o **ativar uma cópia de segurança predefinida** opção para o volume.
   > 
   > 
6. Guardar as alterações ao clicar no ícone de verificação ![ícone de verificação](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). Portal clássico do Azure irá apresentar uma mensagem de volume de atualização. Apresentará uma mensagem de êxito quando o volume tiver sido atualizado com êxito.
7. Se está a expandir um volume, conclua os seguintes passos no seu computador de anfitrião do Windows:
   
   1. Aceda a **gestão de computadores** ->**disco gestão**.
   2. Clique com botão direito **gestão de discos** e selecione **reanalisar discos**.
   3. Na lista de discos, selecione o volume que tenha atualizado, rato e, em seguida, selecione **Expandir Volume**. O Assistente de expandir o Volume é iniciado. Clique em **Seguinte**.
   4. Conclua o assistente, aceitando os valores predefinidos. Depois de concluído o assistente, o volume deve mostrar o tamanho maior.
      
      > [!NOTE]
      > Se expandir um volume afixado localmente e, em seguida, expanda outro localmente afixado volume imediatamente posteriormente, as tarefas de expansão de volume são executados sequencialmente. A tarefa de expansão de volume primeiro deve ser concluído antes de pode começar a próxima tarefa de expansão de volume.
      > 
      > 

![Vídeo disponível](./media/storsimple-manage-volumes-u2/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que demonstra como expandir um volume, clique em [aqui](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="change-the-volume-type"></a>Alterar o tipo de volume
Pode alterar o tipo de volume em camadas para afixado localmente ou de afixado localmente a em camadas. No entanto, esta conversão não deve ser uma ocorrência frequente. Algumas razões para converter um volume de camadas para afixado localmente são:

* Garantias locais sobre desempenho e disponibilidade de dados
* Eliminação de latências de nuvem e de problemas de conectividade de nuvem.

Normalmente, estes são pequenos volumes existentes que pretende aceder com frequência. Um volume localmente afixado é totalmente aprovisionado quando é criado. Se estiver a converter um volume em camadas para um volume localmente afixado, StorSimple verifica que tem espaço suficiente no seu dispositivo antes de iniciar a conversão. Se tiver espaço suficiente, receberá um erro e a operação, será cancelada. 

> [!NOTE]
> Antes de iniciar uma conversão de camadas para afixado localmente, certifique-se de que considerar os requisitos de espaço das outras cargas de trabalho. 
> 
> 

Poderá alterar um volume localmente afixado para um volume em camadas, se precisar de espaço adicional para aprovisionar outros volumes. Quando converter o volume localmente afixado de camadas, a capacidade disponível no aumenta dispositivo pelo tamanho da capacidade de lançamento. Se os problemas de conectividade impedirem a conversão de um volume do tipo de local para o tipo em camadas, o volume local plantas propriedades de um volume em camadas até concluir a conversão. Isto acontece porque poderão ter spilled alguns dados na nuvem. Estes dados spilled irão continuar a ocupam espaço local no dispositivo que não pode ser libertado enquanto a operação é reiniciada e concluída.

> [!NOTE]
> Converter um volume pode demorar algum tempo e não é possível cancelar uma conversão depois desta ser iniciada. O volume permanece online durante a conversão e pode fazer cópias de segurança, mas não é possível expandir ou restaurar o volume enquanto decorre a conversão.  
> 
> 

A conversão de um em camadas para um volume localmente afixado pode afetar negativamente o desempenho do dispositivo. Além disso, os seguintes fatores podem aumentar o tempo que demora a concluir a conversão:

* Não há largura de banda suficiente.
* Não há nenhuma cópia de segurança atual.

Para minimizar os efeitos que possam ter estes fatores:

* Reveja a políticas de limitação de largura de banda e certifique-se de que uma largura de banda Mbps dedicado 40 está disponível.
* Agende a conversão para o horário de pico.
* Tire um instantâneo de nuvem antes de iniciar a conversão.

Se estiver a converter vários volumes (suportar cargas de trabalho diferentes), em seguida, deve atribuir prioridades a conversão de volume para que os volumes de prioridade superiores são convertidos em primeiro. Por exemplo, deve converter volumes que alojam máquinas virtuais (VMs) ou volumes com cargas de trabalho do SQL Server antes de converter volumes com cargas de trabalho de partilha de ficheiros.

#### <a name="to-change-the-volume-type"></a>Para alterar o tipo de volume
1. No **dispositivos** página, selecione o dispositivo, faça duplo clique e, em seguida, clique em de **contentores de Volume** separador.
2. Selecione um contentor de volume na lista e faça duplo clique para ver os volumes associados ao contentor.
3. Selecione um volume e, na parte inferior da página, clique em **modificar**. Inicia o Assistente de volume modificar.
4. No **definições básicas** página, altere o tipo de utilização ao selecionar o tipo de novo o **tipo de utilização** na lista pendente.
   
   * Se estiver a alterar o tipo para **afixado localmente**, StorSimple irá verificar para ver se existe suficiente capacidade.
   * Se estiver a alterar o tipo para **em camadas** e este volume será utilizado para dados de arquivo, selecionados o **utilizar este volume para menos dados de arquivo acedidos com frequência** caixa de verificação.
     
       ![Caixa de verificação do arquivo](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)
5. Clique no ícone de seta ![ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) para ir para o **definições adicionais** página. Se estiver a configurar um volume localmente afixado, é apresentada a mensagem seguinte.
   
    ![Alterar a mensagem do tipo de Volume](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)
6. Clique no ícone de seta ![ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) novamente para continuar.
7. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) para iniciar o processo de conversão. O portal do Azure irá apresentar uma mensagem de volume de atualização. Apresentará uma mensagem de êxito quando o volume tiver sido atualizado com êxito.

## <a name="take-a-volume-offline"></a>Colocar offline um volume
Poderá ter de colocar offline um volume quando estiver a planear modificá-lo ou eliminá-lo. Quando um volume estiver offline, não está disponível para acesso de leitura e escrita. Terá de colocar offline o volume do anfitrião, bem como no dispositivo. 

#### <a name="to-take-a-volume-offline"></a>Para colocar offline um volume
1. Certifique-se de que o volume em questão não está a ser utilizado antes de colocar-offline.
2. Colocar offline o volume anfitrião primeiro. Isto elimina a qualquer potencial risco de danos nos dados no volume. Para obter passos específicos, consulte as instruções para o seu sistema operativo do anfitrião.
3. Depois do anfitrião estiver offline, colocar o volume no dispositivo offline, efetuando os seguintes passos:
   
   1. No **dispositivos** página, selecione o dispositivo, faça duplo clique e, em seguida, clique em de **contentores de Volume** separador. O **contentores de Volume** separador lista em formato tabular, todos os contentores de volume que estão associados ao dispositivo.
   2. Selecione um contentor de volume e clique na mesma para ver a lista de todos os volumes no contentor.
   3. Selecione um volume e clique em **colocar offline**.
   4. Quando lhe for pedida a confirmação, clique em **Sim**. O volume deve agora estar offline.
      
      Depois de um volume estiver offline, o **colocar Online** opção fica disponível.

> [!NOTE]
> O **Colocar Offline** comando envia um pedido para o dispositivo para colocar offline o volume. Se anfitriões ainda estiver a utilizar o volume, isto resulta em ligações quebradas, mas colocar offline o volume não irá falhar. 
> 
> 

## <a name="delete-a-volume"></a>Eliminar um volume
> [!IMPORTANT]
> Pode eliminar um volume apenas se estiver offline.
> 
> 

Conclua os seguintes passos para eliminar um volume.

#### <a name="to-delete-a-volume"></a>Para eliminar um volume
1. No **dispositivos** página, selecione o dispositivo, faça duplo clique e, em seguida, clique em de **contentores de Volume** separador.
2. Selecione o contentor de volume que contém o volume que pretende eliminar. Clique no contentor de volume para aceder a **Volumes** página.
3. Todos os volumes associados a este contentor são apresentados em formato tabular. Verifique o estado do volume que pretende eliminar. Se o volume que pretende eliminar não estiver offline, coloque-offline em primeiro lugar, seguindo os passos no [colocar offline um volume](#take-a-volume-offline).
4. Depois do volume estiver offline, clique em **eliminar** na parte inferior da página.
5. Quando lhe for pedida a confirmação, clique em **Sim**. O volume agora será eliminado e o **Volumes** página mostrará a lista atualizada de volumes no contentor.
   
   > [!NOTE]
   > Se eliminar um volume localmente afixado, o espaço disponível para novos volumes não pode ser atualizado imediatamente. O serviço StorSimple Manager atualiza periodicamente o espaço local disponível. Sugerimos que aguarde alguns minutos antes de tentar criar o novo volume.<br> Além disso, se eliminar um volume afixado localmente e, em seguida, elimine o outro localmente afixado volume imediatamente posteriormente, a eliminação de volume, as tarefas são executadas sequencialmente. A tarefa de eliminação do volume primeiro deve ser concluído antes de pode começar a próxima tarefa de eliminação do volume.
   > 
   > 

## <a name="monitor-a-volume"></a>Monitorizar um volume
Monitorização de volume permite-lhe recolher estatísticas de I/O-relacionadas com para um volume. Monitorização é ativada por predefinição para os 32 pela primeira vez volumes que criar. Monitorização de volumes adicionais está desativada por predefinição. Monitorização de volumes clonados também é desativado por predefinição.

Execute os seguintes passos para ativar ou desativar a monitorização para um volume.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Para ativar ou desativar a monitorização de volume
1. No **dispositivos** página, selecione o dispositivo, faça duplo clique e, em seguida, clique em de **contentores de Volume** separador.
2. Selecione o contentor de volume em que reside o volume e, em seguida, clique no contentor de volume para aceder a **Volumes** página.
3. Todos os volumes associados a este contentor estão listados na tabela de apresentação. Clique e selecione o volume ou a clonagem de volume.
4. Na parte inferior da página, clique em **modificar**.
5. No assistente modificar Volume, em **definições básicas**, selecione **ativar** ou **desativar** do **monitorização** na lista pendente.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [clonar um volume StorSimple](storsimple-clone-volume.md).
* Saiba como [utilizam o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).

