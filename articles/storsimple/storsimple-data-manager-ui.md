---
title: IU do Gestor de dados do Microsoft Azure StorSimple | Microsoft Docs
description: "Descreve como utilizar o serviço do Gestor de dados do StorSimple IU (pré-visualização privada)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 53a8599df2c647613122cd791b680e2e658586b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-using-the-storsimple-data-manager-service-ui-private-preview"></a>Gerir utilizando o serviço do Gestor de dados do StorSimple IU (pré-visualização privada)

Este artigo explica como pode utilizar a IU do Gestor de dados do StorSimple para efetuar a transformação de dados em dados que residem nos dispositivos de série 8000 do StorSimple. Os dados transformados, em seguida, podem ser utilizados por outros serviços do Azure, tais como os Media Services do Azure, Azure HDInsight, do Azure Machine Learning e da Azure Search. 


## <a name="use-storsimple-data-transformation"></a>Utilizar transformação de dados do StorSimple

O Gestor de dados do StorSimple é o recurso no qual a transformação de dados pode ser instanciada. O serviço de transformação de dados permite-lhe mover dados do dispositivo StorSimple no local para os blobs no armazenamento do Azure. Por conseguinte, num fluxo de trabalho tem de especificar os detalhes sobre o dispositivo StorSimple e os dados de interesse que pretende mover para a conta de armazenamento.

### <a name="create-a-storsimple-data-manager-service"></a>Criar um serviço StorSimple Manager de dados

Execute os seguintes passos para criar um serviço StorSimple Manager de dados.

1. Para criar um serviço StorSimple Manager de dados, vá para [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager)

2. Clique em de  **+**  ícone e pesquisa para o Gestor de dados do StorSimple. Clique em serviço StorSimple Manager de dados e, em seguida, clique em **criar**.

3. Se a sua subscrição está ativada para criar este serviço, verá o painel seguinte.

    ![Crie um recurso de gestores de dados do StorSimple](./media/storsimple-data-manager-ui/create-new-data-manager-service.png)

4. Introduza as entradas e clique em **criar**. A localização especificada deve ser o que aloja as contas do storage e o serviço StorSimple Manager. Atualmente, são suportadas apenas as regiões EUA oeste e na Europa Ocidental. Por conseguinte, serviço StorSimple Manager, o serviço Gestor de dados e a conta de armazenamento associados devem ser em regiões suportadas anteriores. Demora um minuto para criar o serviço.

### <a name="create-a-data-transformation-job-definition"></a>Criar uma definição de tarefa de transformação de dados

Dentro de um serviço do Gestor de dados do StorSimple, terá de criar uma definição de tarefa de transformação de dados. Uma definição de tarefa Especifica os detalhes dos dados que está interessado em Mover para uma conta do storage no formato nativo. 

Execute os seguintes passos para criar uma nova definição de tarefa de transformação de dados.

1.  Navegue para o serviço que criou. Clique em **+ definição de tarefa**.

    ![Clique em + definição de tarefa](./media/storsimple-data-manager-ui/click-add-job-definition.png)

2. Abre o painel de definição de tarefa nova cópia de segurança. Dê um nome de definição de tarefa e clique em **origem**. No **configurar origem de dados** painel, especifique os detalhes do dispositivo StorSimple e os dados de interesse.

    ![Criar a definição de tarefa](./media/storsimple-data-manager-ui//create-new-job-deifnition.png)

3. Uma vez que este é um novo serviço do Gestor de dados, não repositórios de dados estão configurados. Para adicionar o StorSimple Manager como um repositório de dados, clique em **adicionar novo** na lista pendente do repositório de dados e, em seguida, clique em **adicionar repositório de dados**.

4. Escolha **série 8000 do StorSimple Manager** como o repositório de tipo e introduza as propriedades da sua **StorSimple Manager**. Para o **Id de recurso** campo, tem de introduzir o número antes do **:** na chave do registo do seu StorSimple manager.

    ![Criar origem de dados](./media/storsimple-data-manager-ui/create-new-data-source.png)

5.  Clique em **OK** quando terminar. Isto poupa o seu repositório de dados e esta StorSimple Manager pode ser reutilizado nas outras definições da tarefa sem introduzir estes parâmetros novamente. Demora alguns segundos depois de clicar em **OK** do StorSimple Manager apareçam na lista pendente.

6.  No **configurar origem de dados** painel, introduza o nome de dispositivo e o nome do volume que tem os dados de interesse.

7.  No **filtro** subsecção, introduza o diretório de raiz que contenha os dados de interesse (Este campo deve começar com uma `\`). Também pode adicionar aqui quaisquer filtros de ficheiro.

8.  O serviço de transformação de dados funciona nos dados que são enviados para o Azure através de instantâneos. Ao executar esta tarefa, pode optar por fazer uma cópia de cada vez que esta tarefa é executada (para trabalhar em dados mais recentes) ou para utilizar a última cópia de segurança existente na nuvem (se estiver a trabalhar em alguns dados arquivados).

    ![Detalhes da nova origem de dados](./media/storsimple-data-manager-ui/new-data-source-details.png)

9. Em seguida, as definições de destino tem de ser configurado. Existem 2 tipos de destinos suportados – contas do Storage do Azure e contas de Media Services do Azure. Escolha contas do storage para colocar ficheiros em blobs nessa conta. Escolha o suporte de dados de conta de serviços para colocar ficheiros em recursos nessa conta. Novamente, precisamos de adicionar um repositório. Na lista pendente, selecione **adicionar novo** e, em seguida, **configurar as definições**.

    ![Criar o sink de dados](./media/storsimple-data-manager-ui/create-new-data-sink.png)

10. Aqui, pode selecionar o tipo de repositório que pretende adicionar e os outros parâmetros associados com o repositório. Em ambos os casos, uma fila de armazenamento é criada quando a tarefa é executada. Esta fila é preenchida com mensagens sobre os blobs transformados, à medida que ficam prontos. O nome desta fila é igual ao nome da definição da tarefa. Se selecionar **dos Media Services** como o tipo de repositório, em seguida, também pode introduzir as credenciais da conta de armazenamento em que a fila é criada.

    ![Detalhes de receptores novos dados](./media/storsimple-data-manager-ui/new-data-sink-details.png)

11. Depois de adicionar o repositório de dados (que demora alguns segundos), irá encontrar o repositório na lista pendente no **nome da conta de destino**.  Escolha o destino que precisa.

12. Clique em **OK** para criar a definição de tarefa. A definição da tarefa está agora definida. Pode utilizar esta definição de tarefa várias vezes através da IU.

    ![Adicionar nova definição de tarefa](./media/storsimple-data-manager-ui/add-new-job-definition.png)

### <a name="run-the-job-definition"></a>Executar a definição de tarefa

Sempre que precisar de mover dados do StorSimple para a conta de armazenamento que especificou na definição de tarefa, terá de invoke-lo. Não há alguma flexibilidade na alteração dos parâmetros sempre invocar a tarefa. Os passos são os seguintes:

1. Selecione o serviço StorSimple Manager de dados e aceda a **monitorização**. Clique em **executar agora**.

    ![Definição de tarefa do acionador](./media/storsimple-data-manager-ui/run-now.png)

2. Escolha a definição de tarefa que pretende executar. Clique em **definições de execução** para modificar as definições que poderá pretender alterar esta execução da tarefa.

    ![Definições de tarefa de execução](./media/storsimple-data-manager-ui/run-settings.png)

3. Clique em **OK** e, em seguida, clique em **executar** para iniciar a tarefa. Para monitorizar esta tarefa, vá para o **tarefas** página no Gestor de dados do StorSimple.

    ![Lista de tarefas e o Estado](./media/storsimple-data-manager-ui/jobs-list-and-status.png)

4. Para além de monitorização no **tarefas** painel, pode também escutar a fila de armazenamento onde uma mensagem é adicionada sempre que um ficheiro é movido do StorSimple para a conta de armazenamento.


## <a name="next-steps"></a>Passos seguintes

[Utilizar o SDK do .NET para iniciar o Gestor de dados do StorSimple tarefas](storsimple-data-manager-dotnet-jobs.md).