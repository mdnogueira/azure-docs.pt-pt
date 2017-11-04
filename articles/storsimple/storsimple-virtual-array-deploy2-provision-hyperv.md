---
title: Aprovisionar StorSimple matriz Virtual no Hyper-V | Microsoft Docs
description: "Este tutorial segundo na implementação de matriz Virtual StorSimple envolve o aprovisionamento de uma matriz virtual no Hyper-V."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/15/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bad431c8958f7d381bb9c0410caa3a57c6e75c19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Implementar StorSimple Virtual matriz - aprovisionar no Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Descrição geral
Este tutorial descreve como aprovisionar uma matriz de Virtual StorSimple num sistema anfitrião com Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. Este artigo aplica-se para a implementação de matrizes Virtual StorSimple no portal do Azure e na nuvem do Microsoft Azure Government.

Necessita de privilégios de administrador para aprovisionar e configurar uma matriz de virtual. A aprovisionamento e inicial configuração pode demorar cerca de 10 minutos a concluir.

## <a name="provisioning-prerequisites"></a>Pré-requisitos de aprovisionamento
Aqui, encontrará as pré-requisitos para Aprovisionar uma matriz virtual num sistema anfitrião com Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple
Antes de começar, certifique-se de que:

* Concluiu todos os passos [preparar o portal para a matriz Virtual StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Transferir a imagem de matriz virtual do Hyper-V do portal do Azure. Para obter mais informações, consulte **passo 3: Transferir a imagem de matriz virtual** de [preparar o portal do guia de matriz Virtual StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > O software em execução na matriz Virtual StorSimple só pode ser utilizado com o serviço do Gestor de dispositivos do StorSimple.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Para a matriz de Virtual StorSimple
Antes de implementar uma matriz de virtual, certifique-se de que:

* Tem acesso a um sistema anfitrião com Hyper-V no Windows Server 2008 R2 ou posterior que pode ser utilizados para um aprovisionar um dispositivo.
* O sistema anfitrião é capaz de dedicar os seguintes recursos para aprovisionar a matriz de virtual:

  * Um mínimo de 4 núcleos.
  * Pelo menos 8 GB de RAM. Se pretender configurar a matriz virtual como servidor de ficheiros, 8 GB suporta inferior a 2 milhões de ficheiros. Terá de 16 GB de RAM para 2-4 milhões de ficheiros de suporte.
  * Uma interface de rede.
  * Um disco virtual 500 GB de dados.

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter
Antes de começar, reveja os requisitos de rede para implementar uma matriz de Virtual StorSimple e configurar a rede de datacenter adequadamente. Para obter mais informações, consulte [matriz Virtual StorSimple requisitos de rede](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Passo a passo de aprovisionamento
Para aprovisionar e ligar a uma matriz de virtual, terá de efetuar os seguintes passos:

1. Certifique-se de que o sistema anfitrião tem recursos suficientes para cumprir os requisitos mínimos de matriz virtual.
2. Aprovisione uma matriz virtual no seu hipervisor.
3. Inicie a matriz virtual e obter o endereço IP.

Cada um destes passos é explicada nas secções seguintes.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Passo 1: Certifique-se de que o sistema anfitrião cumpre os requisitos de matriz virtual mínima
Para criar uma matriz de virtual, tem de:

* A função Hyper-V instalada no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 SP1.
* Gestor de Hyper-V do Microsoft num cliente Microsoft Windows ligado ao anfitrião.

Certifique-se de que o hardware subjacente (sistema de anfitrião) no qual está a criar a matriz virtual é capaz de dedicar os seguintes recursos para a matriz de virtual:

* Um mínimo de 4 núcleos.
* Pelo menos 8 GB de RAM. Se pretender configurar a matriz virtual como servidor de ficheiros, 8 GB suporta inferior a 2 milhões de ficheiros. Terá de 16 GB de RAM para 2-4 milhões de ficheiros de suporte.
* Uma interface de rede.
* Um disco virtual 500 GB para os dados de sistema.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Passo 2: Aprovisionar uma matriz virtual no hipervisor
Execute os seguintes passos para aprovisionar um dispositivo no seu hipervisor.

#### <a name="to-provision-a-virtual-array"></a>Para Aprovisionar uma matriz de virtual
1. No anfitrião do Windows Server, copie a imagem de matriz virtual para uma unidade local. Esta imagem (VHD ou VHDX) que transferiu através do portal do Azure. Tome nota da localização onde copiar a imagem que estiver a utilizar esta imagem posteriormente no procedimento.
2. Abra **Gestor de servidor**. No canto superior direito, clique em **ferramentas** e selecione **Gestor de Hyper-V**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Se estiver a executar o Windows Server 2008 R2, abra o Gestor de Hyper-V. No Gestor de servidor, clique em **funções > Hyper-V > Gestor de Hyper-V**.
3. No **Gestor de Hyper-V**, no painel de âmbito, clique com botão direito do nó de sistema para abrir o menu de contexto e, em seguida, clique em **novo** > **Máquina Virtual**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. No **antes de começar** página do Assistente de Nova Máquina Virtual, clique em **seguinte**.
5. No **Especificar nome e localização** , indique uma **nome** para a matriz de virtual. Clique em **Seguinte**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. No **especificar geração** página, escolha o tipo de imagem do dispositivo e, em seguida, clique em **seguinte**. Esta página não aparece se estiver a utilizar o Windows Server 2008 R2.

   * Escolha **geração 2** se tiver transferido uma imagem. vhdx para o Windows Server 2012 ou posterior.
   * Escolha **geração 1** se tiver transferido uma imagem VHD do Windows Server 2008 R2 ou posterior.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. No **atribuir memória** página, especifique um **memória de arranque** de, pelo menos, **8192 MB**, não ative a memória dinâmica e, em seguida, clique em **seguinte**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. No **configurar redes** página, especifique o comutador virtual que está ligado à Internet e, em seguida, clique em **seguinte**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. No **ligar disco rígido virtual** página, escolha **utilizar um disco rígido virtual existente**, especifique a localização da imagem de matriz virtual (. vhdx ou. vhd) e, em seguida, clique em **seguinte**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Reveja o **resumo** e, em seguida, clique em **concluir** para criar a máquina virtual.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Para cumprir os requisitos mínimos, terá de 4 núcleos. Para adicionar 4 processadores virtuais, selecione o sistema anfitrião de **Gestor de Hyper-V** janela. No painel à direita em lista de **máquinas virtuais**, localizar a máquina virtual que acabou de criar. Selecione e o nome do computador com o botão direito e selecione **definições**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. No **definições** página, no painel esquerdo, clique em **processador**. No painel direito, defina **número de processadores virtuais** 4 (ou mais). Clique em **Aplicar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Para cumprir os requisitos mínimos, também terá de adicionar um disco de dados virtual 500 GB. No **definições** página:

    1. No painel esquerdo, selecione **controlador SCSI**.
    2. No painel direito, selecione **rígido,** e clique em **adicionar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. No **unidade de disco rígido** página, selecione o **disco rígido Virtual** opção e clique em **novo**. O **Assistente de novo disco rígido Virtual** é iniciado.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. No **antes de começar** página do assistente disco rígido Virtual novo, clique em **seguinte**.
16. No **página escolher formato de disco**, aceite a opção predefinida **VHDX** formato. Clique em **Seguinte**. Este ecrã não é apresentado se o Windows Server 2008 R2.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. No **página escolher tipo de disco**, definir o tipo de disco rígido virtual como **expansão dinâmica** (recomendado). **Um tamanho fixo** disco funciona, mas poderá ter de aguardar algum tempo. Recomendamos que não utilize o **Differencing** opção. Clique em **Seguinte**. No Windows Server 2012 R2 e Windows Server 2012, **expansão dinâmica** é a opção predefinida, enquanto no Windows Server 2008 R2, a predefinição é **um tamanho fixo**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. No **Especificar nome e localização** , indique uma **nome** , bem como **localização** (pode navegar para um) para o disco de dados. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. No **configurar disco** página, selecione a opção **criar um novo disco de rígido virtual em branco** e especifique o tamanho como **500 GB** (ou mais). Enquanto 500 GB é o requisito mínimo, pode aprovisionar sempre um disco maior. Tenha em atenção que não é possível expandir ou reduzir o disco aprovisionado a vez. Para mais informações sobre o tamanho do disco para aprovisionar, reveja a secção de dimensionamento no [documento do melhores práticas](storsimple-ova-best-practices.md). Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. No **resumo** página, consulte os detalhes do disco virtual de dados e se forem satisfeitos, clique em **concluir** para criar o disco. O assistente fechar e um disco rígido virtual é adicionado ao seu computador.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Volte à **definições** página. Clique em **OK** para fechar o **definições** página e regresse à janela do Gestor de Hyper-V.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Passo 3: Iniciar a matriz virtual e obter o IP
Execute os seguintes passos para iniciar a matriz de virtual e ligar ao mesmo.

#### <a name="to-start-the-virtual-array"></a>Para iniciar a matriz virtual
1. Inicie a matriz virtual.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Depois do dispositivo estiver em execução, selecione o dispositivo, clique com o botão direito e selecione **Connect**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Poderá ter de aguardar 5-10 minutos para que o dispositivo estar pronto. É apresentada uma mensagem de estado na consola para indicar o progresso. Depois do dispositivo estiver pronto, avance para **ação**. Prima `Ctrl + Alt + Delete` para iniciar sessão para a matriz virtual. O utilizador predefinido é *StorSimpleAdmin* e a palavra-passe predefinida é *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Por motivos de segurança, a palavra-passe de administrador do dispositivo expira no primeiro início de sessão. Lhe for pedido para alterar a palavra-passe.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Introduza uma palavra-passe com, pelo menos, 8 carateres. A palavra-passe tem de satisfazer, pelo menos, 3 fora os seguintes requisitos de 4: carateres em maiúsculas, minúsculas, numérico e especiais. Reintroduza a palavra-passe para confirmá-la. São notificados de que a palavra-passe foi alterada.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Depois da palavra-passe é alterada com êxito, pode reiniciar a matriz virtual. Aguarde que o dispositivo iniciar.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Consola do Windows PowerShell do dispositivo é apresentada juntamente com uma barra de progresso.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Os passos 6-8 só se aplicam ao arrancar segurança num ambiente não DHCP. Se tiver um ambiente de DHCP, ignorar estes passos e avance para o passo 9. Se arrancado se o dispositivo no ambiente de não DHCP, verá o ecrã seguinte.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Em seguida, configure a rede.
7. Utilize o `Get-HcsIpAddress` comando para listar as interfaces de rede ativadas na sua matriz virtual. Se o dispositivo tem uma única interface de rede ativada, o nome predefinido atribuído a esta interface é `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Utilize o `Set-HcsIpAddress` cmdlet para configurar a rede. Consulte o exemplo seguinte:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Depois da configuração inicial está concluída e o dispositivo ter arrancado cópias de segurança, verá o texto da faixa de dispositivo. Anote o endereço IP e o URL apresentado no texto da faixa para gerir o dispositivo. Utilize este endereço IP para ligar para o web IU da sua matriz virtual e concluir a configuração local e registo.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Opcional) Execute este passo apenas se estiver a implementar o seu dispositivo na nuvem pública. Agora irá ativar o modo de Estados Unidos Federal Information processamento Standard (FIPS) no seu dispositivo. A norma FIPS 140 define os algoritmos criptográficos aprovados para utilização pelos sistemas de computador government Federal dos EUA para a proteção de dados confidenciais.

    1. Para ativar o modo FIPS, execute o seguinte cmdlet:

        `Enable-HcsFIPSMode`
    2. Reinicie o seu dispositivo depois de ter ativado o modo FIPS para que as validações criptográficas entrem em vigor.

       > [!NOTE]
       > Pode ativar ou desativar o modo FIPS no seu dispositivo. Alternar o dispositivo entre o modo FIPS e não FIPS não é suportada.
       >
       >

Se o dispositivo não cumpre os requisitos mínimos de configuração, consulte o erro seguinte no texto da faixa (mostrado abaixo). Modificar a configuração de dispositivo para que a máquina tem recursos adequados para satisfazer os requisitos mínimos. Pode, em seguida, reiniciar e ligar ao dispositivo. Consulte os requisitos mínimos de configuração no [passo 1: garantir que o sistema anfitrião cumpre os requisitos mínimos de matriz virtual](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Se enfrentam qualquer outro erro durante a configuração inicial através da IU da web local, consulte os fluxos de trabalho seguintes:

* Executar testes de diagnóstico para [resolver problemas de configuração de IU da web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Gerar o pacote de registo e ver ficheiros de registo](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Passos seguintes
* [Configurar a matriz de Virtual StorSimple como um servidor de ficheiros](storsimple-virtual-array-deploy3-fs-setup.md)
* [Configurar a matriz de Virtual StorSimple como um servidor de iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
