---
title: Configurar o MPIO para o dispositivo StorSimple | Microsoft Docs
description: "Descreve como configurar o Multipath i / o (MPIO) para o dispositivo StorSimple ligado a um anfitrião com o Windows Server 2012 R2."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 9fe3fa3a2df63d111de742ecb48b1469aad543cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Configurar o Multipath i / o para o dispositivo StorSimple

Este tutorial descreve os passos que deverá seguir para instalar e utilizar a funcionalidade Multipath i / o (MPIO) num anfitrião com o Windows Server 2012 R2 e ligado a um dispositivo físico StorSimple. As orientações neste artigo aplica-se a série 8000 do StorSimple apenas a dispositivos físicos. O MPIO não é suportado num dispositivo StorSimple em nuvem.

Microsoft incorporada suporte para a funcionalidade Multipath i / o (MPIO) no Windows Server para ajuda compilação altamente disponíveis, com tolerância a falhas SAN configurações. MPIO utiliza os componentes de caminho físico redundantes, placas, cabos e comutadores — para criar caminhos lógicos entre o servidor e o dispositivo de armazenamento. Se existir uma falha de componente, fazendo com que um caminho lógico falhe, a lógica de multipathing utiliza um caminho alternativo de e/s para que as aplicações ainda podem aceder aos respetivos dados. Além disso, consoante a configuração, MPIO pode também melhorar o desempenho ao reequilíbrio a carga em todos os estes caminhos. Para obter mais informações, consulte [descrição geral do MPIO](https://technet.microsoft.com/library/cc725907.aspx "MPIO descrição geral e funcionalidades").

Para elevada disponibilidade da solução StorSimple, MPIO deve ser configurado no dispositivo StorSimple. Quando é instalado MPIO nos servidores de anfitrião com o Windows Server 2012 R2, os servidores, em seguida, podem tolerar uma ligação, a rede ou a falha de interface.

## <a name="mpio-configuration-summary"></a>Resumo da configuração do MPIO

MPIO é uma funcionalidade opcional no Windows Server e não está instalado por predefinição. Deve ser instalada como uma funcionalidade através do Server Manager.

Siga estes passos para configurar o MPIO no dispositivo StorSimple:

* Passo 1: Instalar a MPIO no anfitrião do servidor do Windows
* Passo 2: Configurar o MPIO para volumes do StorSimple
* Passo 3: Volumes do StorSimple de montagem no anfitrião
* Passo 4: Configurar o MPIO para elevada disponibilidade e o balanceamento de carga

Cada um dos passos anteriores é abordada nas secções seguintes.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Passo 1: Instalar a MPIO no anfitrião do servidor do Windows

Para instalar esta funcionalidade no anfitrião do Windows Server, conclua o procedimento seguinte.

#### <a name="to-install-mpio-on-the-host"></a>A instalação do MPIO no anfitrião

1. Abra o Gestor de servidor no anfitrião do Windows Server. Por predefinição, o Gestor de servidores inicia quando um membro do grupo Administradores inicia sessão num computador que está a executar o Windows Server 2012 R2 ou Windows Server 2012. Se o Gestor de servidor já não estiver aberto, clique em **Iniciar > Gestor de servidor**.
   
   ![Gestor de servidor](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Clique em **Gestor de servidor > Dashboard > Adicionar funções e funcionalidades**. Esta ação inicia o **para adicionar funções e funcionalidades** assistente.
   
   ![Assistente para adicionar funções e funcionalidades 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. No **para adicionar funções e funcionalidades** assistente, execute os seguintes passos:
   
   1. No **antes de começar** página, clique em **seguinte**.
   2. No **selecionar tipo de instalação** página, aceite a predefinição de **baseada em funções ou baseada em funcionalidade** instalação. Clique em **Seguinte**.
   
       ![Assistente para adicionar funções e funcionalidades 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. No **servidor de destino selecione** página, escolha **selecione um servidor no agrupamento de servidores**. O servidor de anfitrião deve ser detetado automaticamente. Clique em **Seguinte**.
   4. No **selecionar funções de servidor** página, clique em **seguinte**.
   5. No **selecionar funcionalidades** página, selecione **Multipath i / o**e clique em **seguinte**.
   
       ![Assistente para adicionar funções e funcionalidades 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. No **confirmar seleções de instalação** página, confirme a seleção e, em seguida, selecione **reinicie o servidor de destino automaticamente se necessário**, conforme mostrado abaixo. Clique em **Instalar**.
   
       ![Assistente para adicionar funções e funcionalidades 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Será notificado quando a instalação estiver concluída. Clique em **Fechar** para fechar o assistente.
   
       ![Assistente para adicionar funções e funcionalidades 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Passo 2: Configurar o MPIO para volumes do StorSimple

MPIO tem de ser configurado para identificar os volumes do StorSimple. Para configurar o MPIO para reconhecer volumes do StorSimple, execute os seguintes passos.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Configurar o MPIO para volumes do StorSimple

1. Abra o **configuração do MPIO**. Clique em **Gestor de servidor > Dashboard > Ferramentas > MPIO**.
2. No **MPIO propriedades** caixa de diálogo, selecione o **detetar vários caminhos** separador.
3. Selecione **adicionar suporte para dispositivos de iSCSI**e, em seguida, clique em **adicionar**.  
   ![Propriedades do MPIO detetar múltiplos caminhos](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Reinicie o servidor quando lhe for pedido.
5. No **MPIO propriedades** caixa de diálogo, clique em de **MPIO dispositivos** separador. Clique em **Adicionar**.
    </br>![Dispositivos MPIO de propriedades do MPIO](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. No **adicionar suporte de MPIO** caixa de diálogo em **ID de Hardware do dispositivo**, introduza o seu número de série do dispositivo. Para obter o número de série do dispositivo, aceder ao serviço StorSimple Manager do dispositivo. Navegue para **dispositivos > Dashboard**. O número de série do dispositivo é apresentado no lado direito **relance rápida** painel do dashboard do dispositivo.
    </br>
    ![Adicionar suporte do MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Reinicie o servidor quando lhe for pedido.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Passo 3: Volumes do StorSimple de montagem no anfitrião

Depois do MPIO está configurado no Windows Server, volumes criados no dispositivo StorSimple podem ser montados em, em seguida, podem tirar partido MPIO para redundância. Para montar um volume, execute os seguintes passos.

#### <a name="to-mount-volumes-on-the-host"></a>Montar os volumes no anfitrião

1. Abra o **propriedades do iniciador iSCSI** janela no anfitrião do servidor do Windows. Clique em **Gestor de servidor > Dashboard > Ferramentas > iniciador iSCSI**.
2. No **propriedades do iniciador iSCSI** caixa de diálogo, clique no separador de deteção e, em seguida, clique em **detetar Portal de destino**.
3. No **detetar Portal de destino** diálogo caixa, execute os seguintes passos:
   
   1. Introduza o endereço IP da porta de dados do dispositivo StorSimple (por exemplo, introduza dados 0).
   2. Clique em **OK** para voltar para o **propriedades do iniciador iSCSI** caixa de diálogo.
     
     > [!IMPORTANT]
     > **Se estiver a utilizar uma rede privada para ligações de iSCSI, introduza o endereço IP da porta de dados que está ligado à rede privada.**
    
4. Repita os passos 2-3 um segundo interface de rede (por exemplo, dados 1) no seu dispositivo. Tenha em atenção que estas interfaces devem ser ativadas para iSCSI. Para obter mais informações, consulte [modificar interfaces de rede](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Selecione o **destinos** separador o **propriedades do iniciador iSCSI** caixa de diálogo. Deverá ver o dispositivo StorSimple destino IQN em **destinos detetados**.

   ![Separador de destinos de propriedades do Iniciador do iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Clique em **Connect** para estabelecer uma sessão iSCSI com o dispositivo StorSimple. A **ligar ao destino** é apresentada a caixa de diálogo.
7. No **ligar ao destino** caixa de diálogo, selecione o **multi-path ativar** caixa de verificação. Clique em **avançadas**.
8. No **definições avançadas** diálogo caixa, execute os seguintes passos:
   
   1. No **adaptador Local** na lista pendente, selecione **Iniciador do Microsoft iSCSI**.
   2. No **iniciador IP** pendente lista, selecione o endereço IP do anfitrião.
   3. No **Portal de destino** pendente IP lista, selecione o IP da interface do dispositivo.
   4. Clique em **OK** para voltar para o **propriedades do iniciador iSCSI** caixa de diálogo.
9. Clique em **Propriedades**. No **propriedades** caixa de diálogo, clique em **adicionar sessão**.
10. No **ligar ao destino** caixa de diálogo, selecione o **multi-path ativar** caixa de verificação. Clique em **avançadas**.
11. No **definições avançadas** caixa de diálogo:

    1. No **adaptador Local** na lista pendente, selecione n Iniciador do Microsoft iSCSI.
    2. No **iniciador IP** pendente lista, selecione o endereço IP correspondente no anfitrião. Neste caso, está a ligar duas interfaces de rede no dispositivo para uma única interface de rede no anfitrião. Por conseguinte, esta interface é igual à que é fornecido para a primeira sessão.
    3. No **IP do Portal de destino** na lista pendente, selecione o endereço IP para a interface de dados segundo ativada no dispositivo.
    4. Clique em **OK** para regressar à caixa de diálogo de propriedades do iniciador iSCSI. Adicionou uma segunda sessão para o destino.
12. Abra **gestão de computadores** navegando para **Gestor de servidor > Dashboard > Gestão de computadores**. No painel esquerdo, clique em **armazenamento > Gestão de discos**. O volume criado no dispositivo StorSimple que estão visíveis para este anfitrião é apresentado em **gestão de discos** como discos de novo.
13. Inicialize o disco e crie um novo volume. Durante o processo de formato, selecione um tamanho de bloco de 64 KB.
    
    ![Gestão de discos](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Em **gestão de discos**, clique com botão direito do **disco** e selecione **propriedades**.
15. No modelo de StorSimple # # # **propriedades do dispositivo de disco de multi-path** caixa de diálogo, clique em de **MPIO** separador.
    
    ![O StorSimple 8100 multi-path disco DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. No **DSM nome** secção, clique em **detalhes** e certifique-se de que os parâmetros estão definidos para os parâmetros de predefinição. Os parâmetros de predefinição são:
    
    * Certifique-se de caminho período = 30
    * Contagem de tentativas = 3
    * PDO remover período = 20
    * Intervalo de repetição = 1
    * Certifique-se de caminho ativado = desmarcada.

> [!NOTE]
> **Não modifique os parâmetros de predefinição.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Passo 4: Configurar o MPIO para elevada disponibilidade e o balanceamento de carga

Para multi-path com base no balanceamento de carga e de elevada disponibilidade, os múltiplas sessões têm de ser adicionadas manualmente para declarar os caminhos de diferentes disponíveis. Por exemplo, se o anfitrião tem duas interfaces de rede ligados à SAN e o dispositivo tem duas interfaces de rede ligadas à SAN, em seguida, terá de quatro sessões configuradas com (apenas duas sessões será necessário se cada interface de dados e a interface de anfitrião estiver numa sub-rede IP diferentes e não for encaminhável) de permutações de caminho adequado.

**Recomendamos que tenha, pelo menos, 8 paralelas as sessões ativas entre o dispositivo e o anfitrião de aplicação.** Isto pode ser alcançado Ativando 4 interfaces de rede no seu sistema do Windows Server. Utilize interfaces de rede física ou virtuais interfaces através de tecnologias de Virtualização de rede no nível do sistema operativo ou de hardware no anfitrião do Windows Server. Com duas interfaces de rede no dispositivo, esta configuração resultaria em 8 sessões ativas. Esta configuração ajuda a otimizar o débito de dispositivo e da nuvem.

> [!IMPORTANT]
> **Recomendamos que não misture de 1 GbE e interfaces de rede 10 GbE. Se utilizar duas interfaces de rede, ambas as interfaces devem ser do tipo idêntico.**

O procedimento seguinte descreve como adicionar sessões quando um dispositivo StorSimple com duas interfaces de rede está ligado a um anfitrião com duas interfaces de rede. Isto dá-lhe apenas 4 sessões. Utilize este procedimento mesmo com um dispositivo StorSimple com duas interfaces de rede ligadas a um anfitrião com quatro interfaces de rede. Terá de configurar 8 em vez das 4 sessões descritas aqui.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Para configurar o MPIO para elevada disponibilidade e o balanceamento de carga

1. Executar uma deteção de destino: no **propriedades do iniciador iSCSI** caixa de diálogo a **deteção** separador, clique em **detetar Portal**.
2. No **ligar ao destino** caixa de diálogo, introduza o endereço IP de uma das interfaces de rede do dispositivo.
3. Clique em **OK** para voltar para o **propriedades do iniciador iSCSI** caixa de diálogo.
4. No **propriedades do iniciador iSCSI** caixa de diálogo, selecione o **destinos** separador, realce o destino detetado e, em seguida, clique em **Connect**. O **ligar ao destino** é apresentada a caixa de diálogo.
5. No **ligar ao destino** caixa de diálogo:
   
   1. Deixe a predefinição selecionada a definição de destino para **adicionar esta ligação** à lista de destinos favoritos. Isto faz com que o dispositivo tentar reiniciar a ligação, sempre que este computador é reiniciado automaticamente.
   2. Selecione o **multi-path ativar** caixa de verificação.
   3. Clique em **avançadas**.
6. No **definições avançadas** caixa de diálogo:
   
   1. No **adaptador Local** na lista pendente, selecione **Iniciador do Microsoft iSCSI**.
   2. No **iniciador IP** pendente lista, selecione o endereço IP do anfitrião.
   3. No **IP do Portal de destino** na lista pendente, selecione o endereço IP da interface de dados ativado no dispositivo.
   4. Clique em **OK** para regressar à caixa de diálogo de propriedades do iniciador iSCSI.
7. Clique em **propriedades**e, no **propriedades** caixa de diálogo, clique em **adicionar sessão**.
8. No **ligar ao destino** caixa de diálogo, selecione o **multi-path ativar** caixa de verificação e, em seguida, clique em **avançadas**.
9. No **definições avançadas** caixa de diálogo:
   
   1. No **adaptador Local** na lista pendente, selecione **Iniciador do Microsoft iSCSI**.
   2. No **iniciador IP** pendente lista, selecione o endereço IP correspondente à interface segundo no anfitrião.
   3. No **IP do Portal de destino** na lista pendente, selecione o endereço IP para a interface de dados segundo ativada no dispositivo.
   4. Clique em **OK** para voltar para o **propriedades do iniciador iSCSI** caixa de diálogo. Agora ter adicionado uma segunda sessão para o destino.
10. Repita os passos 8 a 10 para adicionar sessões adicionais (caminhos) para o destino. Com duas interfaces de rede no anfitrião e duas no dispositivo, pode adicionar um total de sessões de quatro.
11. Depois de adicionar as sessões pretendidas (caminhos), o **propriedades do iniciador iSCSI** diálogo caixa, selecione o destino e clique em **propriedades**. No separador de sessões do **propriedades** caixa de diálogo, tenha em atenção a sessão de quatro identificadores que correspondem às combinações possíveis de caminho. Para cancelar uma sessão, selecione a caixa de verificação junto de um identificador de sessão e, em seguida, clique em **desligar**.
12. Para ver os dispositivos apresentados dentro de sessões, selecione o **dispositivos** separador. Para configurar a política MPIO para um dispositivo selecionado, clique em **MPIO**. O **detalhes do dispositivo** é apresentada a caixa de diálogo. No **MPIO** separador, pode selecionar as adequadas **política de balanceamento de carga** definições. Também pode ver o **Active Directory** ou **Standby** tipo de caminho.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [utilizando o serviço do Gestor de dispositivos do StorSimple para modificar a configuração do dispositivo StorSimple](storsimple-8000-modify-device-config.md).

