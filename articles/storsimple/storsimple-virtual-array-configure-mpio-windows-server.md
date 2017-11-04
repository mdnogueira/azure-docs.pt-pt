---
title: "Configurar o MPIO no anfitrião ligado à matriz Virtual StorSimple | Microsoft Docs"
description: "Descreve como configurar o Multipath i / o (MPIO) para a matriz de Virtual StorSimple ligado a um anfitrião com o Windows Server 2012 R2."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5b7a7f99-ee5b-4b7d-ab32-483a5a1fa504
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/01/2017
ms.author: alkohli
ms.openlocfilehash: c75c6ed40754aee964e2b68f4f569dc1422507f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Configurar o Multipath i / o no anfitrião do Windows Server para a matriz de Virtual StorSimple
## <a name="overview"></a>Descrição geral
Este artigo descreve como instalar a funcionalidade Multipath i / (o MPIO) no anfitrião do Windows Server, aplicar definições de configuração específicos para volumes de só de StorSimple e, em seguida, verifique o MPIO para volumes do StorSimple. O procedimento parte do princípio de que a matriz de Virtual StorSimple 1200 com duas interfaces de rede está ligada a um anfitrião Windows Server com duas interfaces de rede. As informações contidas neste artigo aplica-se apenas à matriz de virtual. Para obter informações sobre dispositivos de série 8000 do StorSimple, aceda a [configurar o MPIO para o anfitrião do StorSimple](storsimple-configure-mpio-windows-server.md). 

A funcionalidade MPIO em configurações de armazenamento de elevada disponibilidade, com tolerância a falhas de compilação do Windows Server ajuda-o. MPIO utiliza os componentes de caminho físico redundantes, placas, cabos e comutadores — para criar caminhos lógicos entre o servidor e o dispositivo de armazenamento. Se existir uma falha de componente, fazendo com que um caminho lógico falhe, a lógica de multipathing utiliza um caminho alternativo de e/s para que as aplicações ainda podem aceder aos respetivos dados. Além disso, dependendo da configuração, MPIO pode também melhorar o desempenho ao novamente balanceamento de carga em todos os estes caminhos. Para obter mais informações, consulte [descrição geral do MPIO](https://technet.microsoft.com/library/cc725907.aspx "MPIO descrição geral e funcionalidades").

Para elevada disponibilidade da solução StorSimple, configure o MPIO nos anfitriões do Windows Server ligados para a matriz de Virtual StorSimple (modelo 1200). Os servidores de anfitrião, em seguida, podem tolerar uma ligação, a rede ou a falha de interface. 

Terá de seguir estes passos para configurar o MPIO: 

* Pré-requisitos da configuração
* Passo 1: Instalar a MPIO no anfitrião do servidor do Windows
* Passo 2: Configurar o MPIO para volumes do StorSimple
* Passo 3: Volumes do StorSimple de montagem no anfitrião

Todos os passos acima é abordados nas secções seguintes.

## <a name="prerequisites"></a>Pré-requisitos
Esta secção fornece detalhes sobre os pré-requisitos de configuração para o anfitrião do Windows Server e a matriz de virtual.

### <a name="on-windows-server-host"></a>No anfitrião do Windows Server
* Certifique-se de que o anfitrião do Windows Server tem 2 interfaces de rede ativadas.

### <a name="on-storsimple-virtual-array"></a>Na matriz Virtual StorSimple
* A matriz virtual deve ser configurada como um servidor de iSCSI. Para obter mais informações, consulte [configurar matriz virtual como um servidor de iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md). Uma ou mais interfaces de rede devem ser ativadas na matriz.   
* As interfaces de rede na sua matriz virtual devem ser acessíveis a partir do anfitrião do servidor do Windows.
* Um ou mais volumes devem ser criados na sua matriz de Virtual StorSimple. Para obter mais informações, consulte [adicionar um volume](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume) na sua matriz de Virtual StorSimple. Neste procedimento, criámos 3 volumes (1 localmente afixado e 2 volumes em camadas como mostrado abaixo) na matriz de virtual.
  
    ![mpio0](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>Configuração de hardware para a matriz Virtual StorSimple
A figura abaixo mostra a configuração de hardware para elevada disponibilidade e multipathing balanceamento de carga para o anfitrião do Windows Server e a matriz Virtual StorSimple utilizado neste procedimento.

![configuração de hardware do MPIO](./media/storsimple-virtual-array-configure-mpio-windows-server/1200hardwareconfig.png)

Conforme mostrado na figura anterior:

* A matriz de Virtual StorSimple aprovisionado no Hyper-V é um dispositivo de Active Directory de nó único configurado como um servidor de iSCSI.
* Duas interfaces de rede virtual são ativadas na sua matriz. Na web local IU da sua matriz virtual, certifique-se de que as duas interfaces de rede estão ativadas, navegando até **as definições de rede** conforme mostrado abaixo:
  
    ![Interfaces de rede ativadas no 1200](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio9.png)
  
    Tenha em atenção os endereços IPv4 das interfaces de rede ativadas (Ethernet, Ethernet 2 por predefinição) e guardar para utilização posterior no anfitrião.
* Duas interfaces de rede estão ativadas no anfitrião do Windows Server. Se as interfaces ligadas para o anfitrião e a matriz estiverem na mesma sub-rede, em seguida, será 4 caminhos disponíveis. Isto foi as maiúsculas e minúsculas neste procedimento. No entanto, se cada interface de rede na interface de matriz e o anfitrião estiver numa sub-rede IP diferentes (e não encaminhável), em seguida, apenas 2 caminhos estará disponíveis.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Passo 1: Instalar a MPIO no anfitrião do servidor do Windows
MPIO é uma funcionalidade opcional no Windows Server e não está instalado por predefinição. Deve ser instalada como uma funcionalidade através do Server Manager. Para instalar esta funcionalidade no anfitrião do Windows Server, conclua o procedimento seguinte.

[!INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Passo 2: Configurar o MPIO para volumes do StorSimple
MPIO tem de ser configurado para identificar os volumes do StorSimple. Para configurar o MPIO para reconhecer volumes do StorSimple, execute os seguintes passos.

[!INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Passo 3: Volumes do StorSimple de montagem no anfitrião
Depois do MPIO está configurado no Windows Server, volumes criados na matriz de StorSimple podem ser montados em, em seguida, podem tirar partido MPIO para redundância. Para montar um volume, execute os seguintes passos.

#### <a name="to-mount-volumes-on-the-host"></a>Montar os volumes no anfitrião
1. Abra o **propriedades do iniciador iSCSI** janela no anfitrião do servidor do Windows. Aceda a **Gestor de servidor > Dashboard > Ferramentas > iniciador iSCSI**.
2. No **propriedades do iniciador iSCSI** caixa de diálogo, clique em **deteção**e, em seguida, clique em **detetar Portal de destino**.
3. No **detetar Portal de destino** diálogo caixa, efetue o seguinte:
   
    1. Introduza o endereço IP da interface de rede ativada primeiro na sua matriz de Virtual StorSimple. Por predefinição, isto seria **Ethernet**. 
    2. Clique em **OK** para voltar para o **propriedades do iniciador iSCSI** caixa de diálogo.
     
    > [!IMPORTANT]
    > Se estiver a utilizar uma rede privada para ligações de iSCSI, introduza o endereço IP da porta de dados que está ligado à rede privada.
     
4. Repita os passos 2-3 um segundo interface de rede (por exemplo, Ethernet 2) na sua matriz. 
5. Selecione o **destinos** separador o **propriedades do iniciador iSCSI** caixa de diálogo. Para a matriz de virtual, deverá ver a superfície de cada volume como um destino em **destinos detetados**. Neste caso, seriam possível detetar três destinos (correspondente aos três volumes).
   
    ![mpio1](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio1.png)
6. Clique em **Connect** para estabelecer uma sessão de iSCSI com a matriz de Virtual StorSimple. A **ligar ao destino** será apresentada a caixa de diálogo. Selecione o **multi-path ativar** caixa de verificação. Clique em **avançadas**.
   
    ![mpio2](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio2.png)
7. No **definições avançadas** diálogo caixa, efetue o seguinte:                                        
   
    1. No **adaptador Local** na lista pendente, selecione **Iniciador do Microsoft iSCSI**.
    2. No **iniciador IP** pendente lista, selecione o endereço IP do anfitrião.
    3. No **Portal de destino** pendente IP lista, selecione o IP da interface de matriz.
    4. Clique em **OK** para voltar para o **propriedades do iniciador iSCSI** caixa de diálogo.
     
        ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

8. Clique em **Propriedades**. 
   
    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)

9. No **propriedades** caixa de diálogo, clique em **adicionar sessão**.
   
   ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)
10. No **ligar ao destino** caixa de diálogo, selecione o **multi-path ativar** caixa de verificação. Clique em **avançadas**.
11. No **definições avançadas** caixa de diálogo:                                        
    
    1. No **adaptador Local** na lista pendente, selecione n Iniciador do Microsoft iSCSI.

    2. No **iniciador IP** pendente lista, selecione o endereço IP correspondente no anfitrião. Neste caso, que está a ligar duas interfaces de rede na matriz de uma única interface de rede no anfitrião. Por conseguinte, esta interface é igual à que é fornecido para a primeira sessão.

    3. No **IP do Portal de destino** na lista pendente, selecione o endereço IP para a interface de dados segundo ativada na matriz.

    4. Clique em **OK** para regressar à caixa de diálogo de propriedades do iniciador iSCSI. Adicionou uma segunda sessão para o destino.
      
       ![mpio11](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio11.png)
    
    5. Depois de adicionar as sessões pretendidas (caminhos), o **propriedades do iniciador iSCSI** diálogo caixa, selecione o destino e clique em **propriedades**. No separador de sessões do **propriedades** caixa de diálogo, tenha em atenção a sessão de quatro identificadores que correspondem às combinações possíveis de caminho. Para cancelar uma sessão, selecione a caixa de verificação junto de um identificador de sessão e, em seguida, clique em **desligar**.

    6. Para ver os dispositivos apresentados dentro de sessões, selecione o **dispositivos** separador. Para configurar a política MPIO para um dispositivo selecionado, clique em **MPIO**.

    7. O **detalhes** será apresentada a caixa de diálogo. No **MPIO** separador, pode selecionar as adequadas **política de balanceamento de carga** definições. Também pode ver o **Active Directory** ou **Standby** tipo de caminho.
12. Repita os passos 8-11 para adicionar sessões adicionais (caminhos) para o destino. Com duas interfaces de rede no anfitrião e duas na matriz de virtual, pode adicionar um total de sessões de quatro para cada destino. 
    
    ![mpio14](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio14.png)
13. Terá de repetir estes passos para cada volume (analisa como um destino).
    
    ![mpio15](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio15.png)
14. Abra **gestão de computadores** navegando para **Gestor de servidor > Dashboard > Gestão de computadores**. No painel esquerdo, clique em **armazenamento > Gestão de discos**. Os volumes criados na matriz virtuais StorSimple que estão visíveis para este anfitrião serão apresentado no **gestão de discos** como discos de novo.
15. Inicialize o disco e crie um novo volume. Durante o processo de formato, selecione um tamanho de unidade de alocação (AUS) de 64 KB. Repita o processo para todos os volumes disponíveis.
    
    ![Gestão de discos](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio20.png)
16. Em **gestão de discos**, clique com botão direito do **disco** e selecione **propriedades**.
17. No **propriedades do dispositivo de disco de multi-path** caixa de diálogo, clique em de **MPIO** separador.
    
    ![Propriedades do disco](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio21.png)
18. No **DSM nome** secção, clique em **detalhes** e certifique-se de que os parâmetros estão definidos para os parâmetros de predefinição. Os parâmetros de predefinição são:
    
    * Certifique-se de caminho período = 30
    * Contagem de tentativas = 3
    * PDO remover período = 20
    * Intervalo de repetição = 1
    * Certifique-se de caminho ativado = desmarcada.
    
    > [!NOTE]
    > **Não modifique os parâmetros de predefinição.**
   
## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [utilizando o serviço do Gestor de dispositivos do StorSimple para administrar a matriz de Virtual StorSimple](storsimple-virtual-array-manager-service-administration.md).

