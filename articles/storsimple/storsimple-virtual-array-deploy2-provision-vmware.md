---
title: Aprovisionar a matriz de Virtual StorSimple no VMware | Microsoft Docs
description: "Este tutorial segundo na série de implementação de matriz Virtual StorSimple envolve o aprovisionamento de um dispositivo virtual do VMware."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 495ef6a93ee06423495269306ad06e76dda13e10
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Implementar StorSimple Virtual matriz - aprovisionar no VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Descrição geral
Este tutorial descreve como aprovisionar e ligar a uma matriz de Virtual StorSimple num sistema anfitrião com o VMware ESXi 5.0, 5.5 ou 6.0. Este artigo aplica-se para a implementação de matrizes Virtual StorSimple no portal do Azure e a nuvem do Microsoft Azure Government.

Necessita de privilégios de administrador para aprovisionar e ligar a um dispositivo virtual. A aprovisionamento e inicial configuração pode demorar cerca de 10 minutos a concluir.

## <a name="provisioning-prerequisites"></a>Pré-requisitos de aprovisionamento
As pré-requisitos para aprovisionar um dispositivo virtual num sistema anfitrião com o VMware ESXi 5.0, 5.5 ou 6.0, são os seguintes.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple
Antes de começar, certifique-se de que:

* Concluiu todos os passos [preparar o portal para a matriz Virtual StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Transferir a imagem do dispositivo virtual para VMware do portal do Azure. Para obter mais informações, consulte **passo 3: Transferir a imagem do dispositivo virtual** de [preparar o portal do guia de matriz Virtual StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Para o dispositivo virtual StorSimple
Antes de implementar um dispositivo virtual, certifique-se de que:

* Tem acesso a um sistema anfitrião com Hyper-V (2008 R2 ou posterior) que pode ser utilizado para um aprovisionar um dispositivo.
* O sistema anfitrião é capaz de dedicar os seguintes recursos para aprovisionar o dispositivo virtual:

  * Um mínimo de 4 núcleos.
  * Pelo menos 8 GB de RAM. Se pretender configurar a matriz virtual como servidor de ficheiros, 8 GB suporta inferior a 2 milhões de ficheiros. Terá de 16 GB de RAM para 2-4 milhões de ficheiros de suporte.
  * Uma interface de rede.
  * Um disco virtual 500 GB para os dados de sistema.

### <a name="for-the-network-in-datacenter"></a>Para a rede no datacenter
Antes de começar, certifique-se de que:

* Consultou os requisitos de rede para implementar um dispositivo virtual StorSimple e configurado a rede de centro de dados de acordo com os requisitos. 

## <a name="step-by-step-provisioning"></a>Passo a passo de aprovisionamento
Para aprovisionar e ligar a um dispositivo virtual, terá de efetuar os seguintes passos:

1. Certifique-se de que o sistema anfitrião tem recursos suficientes para cumprir os requisitos de mínima do dispositivo virtual.
2. Aprovisione um dispositivo virtual no seu hipervisor.
3. Iniciar o dispositivo virtual e obter o endereço IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Passo 1: Certifique-se de sistema anfitrião cumpre os requisitos de mínima do dispositivo virtual
Para criar um dispositivo virtual, terá de:

* Acesso a um sistema anfitrião com o VMware ESXi servidor 5.0, 5.5 ou 6.0.
* VMware vSphere cliente no seu sistema para gerir o anfitrião ESXi.

  * Um mínimo de 4 núcleos.
  * Pelo menos 8 GB de RAM. Se pretender configurar a matriz virtual como servidor de ficheiros, 8 GB suporta inferior a 2 milhões de ficheiros. Terá de 16 GB de RAM para 2-4 milhões de ficheiros de suporte.
  * Uma interface de rede ligado à rede com capacidade de encaminhamento de tráfego de Internet. A largura de banda de Internet mínimo deve ser 5 Mbps para permitir o de trabalho ideal do dispositivo.
  * Um disco virtual 500 GB de dados.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Passo 2: Aprovisionar um dispositivo virtual no hipervisor
Execute os seguintes passos para aprovisionar um dispositivo virtual no seu hipervisor.

1. Copie a imagem do dispositivo virtual no seu sistema. Transferiu a esta imagem de virtual através do portal do Azure.

   1. Certifique-se de que transferiu o ficheiro de imagem mais recente. Se tiver transferido anteriormente a imagem, transfira-o novamente para garantir que têm a imagem mais recente. A imagem mais recente tem dois ficheiros (em vez de um).
   2. Tome nota da localização onde copiar a imagem que estiver a utilizar esta imagem posteriormente no procedimento.

2. Inicie sessão no servidor com o cliente do vSphere ESXi. Tem de ter privilégios de administrador para criar uma máquina virtual.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. No cliente do vSphere, na secção de inventário no painel esquerdo, selecione o servidor de ESXi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Carrega o VMDK para o servidor de ESXi. Navegue para o **configuração** separador no painel direito. Em **Hardware**, selecione **armazenamento**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. No painel direito, em **Datastores**, selecione o arquivo de dados onde pretende carregar o VMDK. O arquivo de dados tem de ter espaço livre suficiente para os SO e discos de dados.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Clique com botão direito e selecione **procurar o arquivo de dados**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. A **Browser de arquivo de dados** surge a janela.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na barra de ferramentas, clique em ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ícone para criar uma nova pasta. Especifique o nome de pasta e tome nota do mesmo. Irá utilizar este nome de pasta mais tarde, quando criar uma máquina virtual (melhor prática recomendada). Clique em **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. A nova pasta aparece no painel esquerdo do **Browser de arquivo de dados**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Clique no ícone de carregamento ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) e selecione **carregar o ficheiro**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Procurar e aponte para os ficheiros VMDK que transferiu. Existem dois ficheiros. Selecione um ficheiro a carregar.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Clique em **abra**. Inicia o carregamento do ficheiro VMDK para o arquivo de dados especificado. Pode demorar alguns minutos para o ficheiro a carregar.
13. Depois do carregamento estiver concluído, consulte o ficheiro no arquivo de dados na pasta que criou.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Agora, carregue o ficheiro VMDK segundo ao mesmo arquivo de dados.
14. Voltar para a janela de cliente vSphere. Com o servidor de ESXi selecionado, clique com botão direito e selecione **Nova Máquina Virtual**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. A **Criar Nova Máquina Virtual** será apresentada a janela. No **configuração** página, selecione o **personalizada** opção. Clique em **Seguinte**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. No **nome e localização** página, especifique o nome da sua máquina virtual. Este nome deve corresponder ao nome de pasta (recomendável) que especificou anteriormente no passo 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. No **armazenamento** página, selecione um arquivo de dados que pretende utilizar para aprovisionar a VM.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. No **versão da Máquina Virtual** página, selecione **versão da Máquina Virtual: 8**. Versões 8 a 11 estão todas suportadas.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. No **sistema operativo convidado** página, selecione o **sistema operativo convidado** como **Windows**. Para **versão**, na lista pendente, selecione **Microsoft Windows Server 2012 (64-bit)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. No **CPUs** página, ajuste o **número de sockets virtuais** e **número de núcleos por virtual socket** para que o **número Total de núcleos** é 4 (ou mais). Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. No **memória** página, especifique 8 GB (ou mais) de RAM. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. No **rede** página, especifique o número de interfaces de rede. O requisito mínimo é uma interface de rede.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. No **controlador SCSI** página, aceite a predefinição **controlador LSI Logic SAS**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. No **selecione um disco** página, escolha **utilizar um disco virtual existente**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. No **selecionar disco existente** página **caminho do ficheiro de disco**, clique em **procurar**. Esta ação abre um **procurar Datastores** caixa de diálogo. Navegue para a localização onde que carregou o VMDK. Agora verá apenas um ficheiro no arquivo de dados como os dois ficheiros, que inicialmente carregado foi intercalados. Selecione o ficheiro e clique em **OK**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. No **opções avançadas** página, aceite a predefinição e clique em **seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. No **pronto para concluir** , reveja a todas as definições associadas a nova máquina virtual. Verifique **editar as definições de máquina virtual antes da conclusão**. Clique em **continuar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. No **propriedades de máquinas virtuais** na página de **Hardware** separador, localize o hardware do dispositivo. Selecione **novo disco rígido**. Clique em **Adicionar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Verá um **Adicionar Hardware** janela. No **tipo de dispositivo** página **escolha o tipo de dispositivo que pretende adicionar**, selecione **disco rígido**e clique em **seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. No **selecione um disco** página, escolha **criar um novo disco virtual**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. No **criar um disco** página, altere o **tamanho do disco** 500 GB (ou mais). Enquanto 500 GB é o requisito mínimo, pode aprovisionar sempre um disco maior. Tenha em atenção que não é possível expandir ou reduzir o disco aprovisionado a vez. Para mais informações sobre o tamanho do disco para aprovisionar, reveja a secção de dimensionamento no [documento do melhores práticas](storsimple-ova-best-practices.md). Em **disco aprovisionamento**, selecione **aprovisionamento**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. No **opções avançadas** página, aceite a predefinição.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. No **pronto para concluir** , reveja as opções de disco. Clique em **Concluir**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Regressar à página de propriedades da Máquina Virtual. Um novo disco rígido é adicionado à máquina virtual. Clique em **Concluir**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Com a máquina virtual selecionada no painel direito, navegue para o **resumo** separador. Reveja as definições para a máquina virtual.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

A máquina virtual está agora aprovisionada. O passo seguinte é energia neste computador e obter o endereço IP.

> [!NOTE]
> Recomendamos que não instale as ferramentas do VMware na sua matriz virtual (como aprovisionado acima). Instalação das ferramentas do VMware irá resultar numa configuração não suportada.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Passo 3: Iniciar o dispositivo virtual e obter o IP
Execute os seguintes passos para iniciar o dispositivo virtual e ligar ao mesmo.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual
1. Inicie o dispositivo virtual. O vSphere Configuration Manager, no painel esquerdo, selecione o seu dispositivo e faça duplo clique para trazer o menu de contexto. Selecione **energia** e, em seguida, selecione **ligar**. Isto deve ligar a máquina virtual. Pode ver o estado na parte inferior **tarefas recentes** painel do cliente vSphere.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. As tarefas de configuração irão demorar alguns minutos a concluir. Depois do dispositivo estiver em execução, navegue para o **consola** separador. Envie Ctrl + Alt + Delete, inicie sessão no dispositivo. Em alternativa, pode apontar o cursor na janela de consola e prima Ctrl + Alt + Insert. O utilizador predefinido é *StorSimpleAdmin* e a palavra-passe predefinida é *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Por motivos de segurança, a palavra-passe de administrador do dispositivo expira no primeiro início de sessão. Lhe for pedido para alterar a palavra-passe.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Introduza uma palavra-passe com, pelo menos, 8 carateres. A palavra-passe tem de conter 3 fora de 4 destes requisitos: carateres em maiúsculas, minúsculas, numérico e especiais. Reintroduza a palavra-passe para confirmá-la. Será notificado de que a palavra-passe foi alterada.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Depois da palavra-passe é alterada com êxito, pode reiniciar o dispositivo virtual. Aguarde o reinício concluir. Consola do Windows PowerShell do dispositivo pode ser apresentada juntamente com uma barra de progresso.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Os passos 6-8 só se aplicam ao arrancar segurança num ambiente não DHCP. Se tiver um ambiente de DHCP, ignorar estes passos e avance para o passo 9. Se arrancado se o dispositivo no ambiente de não DHCP, verá o ecrã seguinte.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Em seguida, configure a rede.
7. Utilize o `Get-HcsIpAddress` comando para listar as interfaces de rede ativadas no seu dispositivo virtual. Se o dispositivo tem uma única interface de rede ativada, o nome predefinido atribuído a esta interface é `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Utilize o `Set-HcsIpAddress` cmdlet para configurar a rede. Um exemplo é mostrado abaixo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Depois da configuração inicial está concluída e o dispositivo ter arrancado cópias de segurança, verá o texto da faixa de dispositivo. Anote o endereço IP e o URL apresentado no texto da faixa para gerir o dispositivo. Utilizará este endereço IP para ligar para o web da IU do seu dispositivo virtual e concluir a configuração local e registo.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Opcional) Execute este passo apenas se estiver a implementar o seu dispositivo na nuvem pública. Agora irá ativar o modo de Estados Unidos Federal Information processamento Standard (FIPS) no seu dispositivo. A norma FIPS 140 define os algoritmos criptográficos aprovados para utilização pelos sistemas de computador government Federal dos EUA para a proteção de dados confidenciais.

    1. Para ativar o modo FIPS, execute o seguinte cmdlet:

        `Enable-HcsFIPSMode`
    2. Reinicie o seu dispositivo depois de ter ativado o modo FIPS para que as validações criptográficas entrem em vigor.

       > [!NOTE]
       > Pode ativar ou desativar o modo FIPS no seu dispositivo. Alternar o dispositivo entre o modo FIPS e não FIPS não é suportada.
       >
       >

Se o dispositivo não cumpre os requisitos mínimos de configuração, verá um erro no texto da faixa (mostrado abaixo). Terá de modificar a configuração de dispositivo, de modo a que tem recursos adequados para satisfazer os requisitos mínimos. Pode, em seguida, reiniciar e ligar ao dispositivo. Consulte os requisitos mínimos de configuração no [passo 1: Certifique-se de que o sistema anfitrião cumpre os requisitos de mínima do dispositivo virtual](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Se enfrentam qualquer outro erro durante a configuração inicial através da IU da web local, consulte os fluxos de trabalho seguintes:

* Executar testes de diagnóstico para [resolver problemas de configuração de IU da web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Gerar o pacote de registo e ver ficheiros de registo](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Passos seguintes
* [Configurar a matriz de Virtual StorSimple como um servidor de ficheiros](storsimple-virtual-array-deploy3-fs-setup.md)
* [Configurar a matriz de Virtual StorSimple como um servidor de iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
