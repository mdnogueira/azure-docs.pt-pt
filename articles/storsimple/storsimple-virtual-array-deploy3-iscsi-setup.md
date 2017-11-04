---
title: "Configuração do servidor do Microsoft Azure StorSimple Virtual matriz iSCSI | Microsoft Docs"
description: "Descreve como efetuar a configuração inicial, registar o servidor de iSCSI StorSimple e concluir a configuração do dispositivo."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 076df176d7cd40c009aea27004fe0f4415999c80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Implementar StorSimple Virtual matriz – conjunto de cópias de segurança como um servidor de iSCSI através do portal do Azure

![fluxo de processo de configuração do iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Descrição geral

Este tutorial de implementação aplicam-se para a matriz Virtual do Microsoft Azure StorSimple. Este tutorial descreve como efetuar a configuração inicial, registar o servidor de iSCSI do StorSimple, concluir a configuração de dispositivo e, em seguida, criar, montar, inicializar e formatar volumes na sua matriz de Virtual StorSimple configurado como um servidor de iSCSI. 

Os procedimentos descritos aqui demorar cerca de 30 minutos para 1 hora para concluir. As informações publicadas neste artigo aplica-se apenas às matrizes de Virtual StorSimple.

## <a name="setup-prerequisites"></a>Pré-requisitos do programa de configuração

Antes de configurar e configurar a matriz de Virtual StorSimple, certifique-se de que:

* Ter aprovisionado uma matriz de virtual e ligados à mesma, conforme descrito em [implementar StorSimple Virtual matriz - aprovisionar uma matriz virtual no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [implementar StorSimple Virtual matriz - aprovisionar uma matriz virtual do VMware ](storsimple-virtual-array-deploy2-provision-vmware.md).
* Tem a chave de registo do serviço do serviço do Gestor de dispositivos do StorSimple que criou para gerir as matrizes de Virtual StorSimple. Para obter mais informações, consulte **passo 2: obter a chave de registo do serviço** no [implementar StorSimple Virtual matriz - preparar o portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Se esta for a segunda virtual matriz com um serviço StorSimple Manager de dispositivo que está a registar, deve ter a chave de encriptação de dados do serviço. Esta chave foi gerada quando o primeiro dispositivo foi registado com êxito com este serviço. Se tiver perdido a esta chave, consulte **obter a chave de encriptação de dados do serviço** no [utilizar a IU da Web para administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Programa de configuração passo a passo

Utilize as seguintes instruções passo a passo para configurar e a matriz de Virtual StorSimple:

* [Passo 1: Concluir a configuração de IU da web locais e registar o seu dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* [Passo 2: Concluir a configuração de dispositivos necessários](#step-2-complete-the-required-device-setup)
* [Passo 3: Adicionar um volume](#step-3-add-a-volume)
* [Passo 4: Montar, inicializar e formatar um volume](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passo 1: Concluir a configuração de IU da web locais e registar o seu dispositivo

#### <a name="to-complete-the-setup-and-register-the-device"></a>Para concluir a configuração e registar o dispositivo

1. Abra uma janela do browser. Para estabelecer ligação com a tipo de IU da web:
   
    `https://<ip-address of network interface>`
   
    Utilize o URL de ligação que anotou no passo anterior. Verá um erro notificam que há um problema com o certificado de segurança do Web site. Clique em **continuar para esta página web**.
   
    ![Erro de certificado de segurança](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Iniciar sessão para a IU da web do seu dispositivo virtual como **StorSimpleAdmin**. Introduza a palavra-passe de administrador dispositivo que alterou no passo 3: iniciar o dispositivo virtual [implementar StorSimple Virtual matriz - aprovisionar um dispositivo virtual no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou [implementar StorSimple Virtual matriz - aprovisionar um dispositivo virtual do VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Página de início de sessão](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Será conduzido para a **home page** página. Esta página descreve as várias definições necessárias para configurar e registar o dispositivo virtual com o serviço do Gestor de dispositivos do StorSimple. Tenha em atenção que o **as definições de rede**, **as definições de proxy de Web**, e **tempo definições** são opcionais. As definições necessárias apenas são **definições do dispositivo** e **definições da Cloud**.
   
    ![página inicial](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. No **as definições de rede** página em **interfaces de rede**, dados 0 serão configurados automaticamente para si. Cada interface de rede está definido por predefinição, para obter um endereço IP automaticamente (DHCP). Por conseguinte, um endereço IP, a sub-rede e o gateway serão automaticamente atribuídos (para IPv4 e IPv6).
   
    Como planeia implementar o dispositivo como um servidor de iSCSI (para armazenamento de blocos de aprovisionar), recomendamos que desative o **obter automaticamente endereço IP** opção e configurar endereços IP estáticos.
   
    ![Página de definições de rede](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Se tiver adicionado mais de uma interface de rede durante o aprovisionamento do dispositivo, pode configurá-las aqui. Tenha em atenção de que pode configurar a sua interface de rede como IPv4 apenas ou como IPv4 e IPv6. Configurações de apenas de IPv6 não são suportadas.
5. Servidores DNS são necessários porque são utilizados quando o dispositivo tenta comunicar com os fornecedores de serviços de armazenamento na nuvem ou para resolver o seu dispositivo por nome se estiver configurado como servidor de ficheiros. No **as definições de rede** página sob o **servidores DNS**:
   
   1. Um servidor DNS primário e secundário será configurado automaticamente. Se optar por configurar endereços IP estáticos, pode especificar servidores DNS. Para elevada disponibilidade, recomendamos que configure um site primário e um servidor DNS secundário.
   2. Clique em **Aplicar**. Este procedimento irá aplicar e Valide as definições de rede.
6. No **definições do dispositivo** página:
   
   1. Atribuir um único **nome** para o seu dispositivo. Este nome pode ter 1-15 carateres e pode conter letra, números e hífenes.
   2. Clique em de **servidor iSCSI** ícone ![ícone de iSCSI do servidor](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) para o **tipo** de dispositivo que está a criar. Um servidor de iSCSI será permitem-lhe aprovisionar armazenamento de blocos.
   3. Especifique se pretende que este dispositivo ser associado a um domínio. Se o seu dispositivo é um servidor de iSCSI, em seguida, aderir ao domínio é opcional. Se optar por não aderir ao seu servidor de iSCSI para um domínio, clique em **aplicar**, aguarde que as definições para ser aplicada e, em seguida, avance para o passo seguinte.
      
       Se pretender associar o dispositivo a um domínio. Introduza um **nome de domínio**e, em seguida, clique em **aplicar**.
      
      > [!NOTE]
      > Se associar o servidor de iSCSI a um domínio, certifique-se de que a matriz de virtual está no seu próprio unidade organizacional (UO) do Microsoft Azure Active Directory e não existem objetos de política de grupo (GPO) são aplicados ao mesmo.
      > 
      > 
   4. Será apresentada uma caixa de diálogo. Introduza as credenciais de domínio no formato especificado. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). As credenciais de domínio serão verificadas. Verá uma mensagem de erro se as credenciais estão incorretas.
      
       ![credenciais](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Clique em **Aplicar**. Este procedimento irá aplicar e validar as definições do dispositivo.
7. (Opcionalmente)-configure o servidor proxy web. Apesar da configuração de proxy web é opcional, lembre-se de que se utilizar um proxy web, só pode configurá-lo aqui.
   
    ![configurar o web proxy](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    No **Web proxy** página:
   
   1. Forneça o **URL de proxy Web** neste formato: *http://host-IP endereço* ou *FDQN:Port número*. Tenha em atenção que não são suportados HTTPS URLs.
   2. Especifique **autenticação** como **básico** ou **nenhum**.
   3. Se estiver a utilizar a autenticação, também terá de fornecer um **Username** e **palavra-passe**.
   4. Clique em **Aplicar**. Isto irá validar e aplicar as definições de proxy web configurada.
8. (Opcionalmente) configure as definições de tempo para o seu dispositivo, como o fuso horário e os servidores NTP primário e secundário. Os servidores NTP são necessários porque o seu dispositivo deve sincronizar a hora para se poder autenticar com os fornecedores de serviços em nuvem.
   
    ![Definições de hora](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    No **tempo definições** página:
   
   1. Na lista pendente, selecione o **fuso horário** com base na localização geográfica na qual o dispositivo está a ser implementado. O fuso horário predefinido para o dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.
   2. Especifique um **servidor NTP primário** para o seu dispositivo ou aceite o valor predefinido de time.windows.com. Verifique se a sua rede permite que o tráfego NTP passe do datacenter para a Internet.
   3. Opcionalmente, especifique um **servidor NTP secundário** para o seu dispositivo.
   4. Clique em **Aplicar**. Isto irá validar e aplicar as definições de tempo configurado.
9. Configure as definições da nuvem para o seu dispositivo. Neste passo, irá concluir a configuração de dispositivo local e, em seguida, registar o dispositivo com o serviço do Gestor de dispositivos do StorSimple.
   
   1. Introduza o **chave de registo do serviço** que obteve **passo 2: obter a chave de registo do serviço** no [implementar StorSimple Virtual matriz - preparar o Portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Se não for o primeiro dispositivo de que está a registar com este serviço, terá de fornecer o **chave de encriptação de dados do serviço**. Esta chave é necessária com a chave de registo do serviço para registar dispositivos adicionais com o serviço do Gestor de dispositivos do StorSimple. Para obter mais informações, consulte [obter a chave de encriptação de dados do serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) no seu local de IU da web.
   3. Clique em **registar**. Esta ação irá reiniciar o dispositivo. Poderá ter de aguardar 2 a 3 minutos antes do dispositivo está registado com êxito. Depois do dispositivo tem de ser reiniciado, será conduzido para o página de início de sessão.
      
      ![Registar o dispositivo](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Regresse ao portal do Azure.
11. Navegue para o **dispositivos** painel do seu serviço. Se tiver uma grande quantidade de recursos, clique em **todos os recursos**, clique no nome do serviço (procure-la, se necessário) e, em seguida, clique em **dispositivos**.
12. No **dispositivos** painel, certifique-se de que o dispositivo foi ligado com êxito para o serviço ao observar o estado. O estado do dispositivo deve ser **Pronto para configurar**.
    
    ![Registar o dispositivo](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Passo 2: Configurar o dispositivo como servidor de iSCSI

Execute os seguintes passos no portal do Azure para concluir a configuração de dispositivos necessários.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Para configurar o dispositivo como servidor de iSCSI

1. Aceda ao seu serviço do Gestor de dispositivos do StorSimple e, em seguida, aceda a **gestão > dispositivos**. No **dispositivos** painel, selecione o dispositivo que acabou de criar. Este dispositivo seria apresentados como **preparado para configurar**.
   
    ![Configurar o dispositivo como servidor de iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Clique no dispositivo e verá uma mensagem de faixa que indica se o dispositivo está pronto para a configuração.
   
    ![Configurar o dispositivo como servidor de iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Clique em **configurar** na barra de comando de dispositivo. Isto abre-se a **configurar** painel. No **configurar** painel, efetue o seguinte procedimento:
   
   * O nome do servidor de iSCSI é preenchido automaticamente.
   * Certifique-se a encriptação de armazenamento na nuvem está definida como **ativado**. Isto garante que os dados enviados do dispositivo para a nuvem são encriptados.
   * Especifique uma chave de encriptação de 32 carateres e registe-la numa aplicação Gestão de chaves para consulta futura.
   * Selecione uma conta de armazenamento a ser utilizado com o seu dispositivo. Esta subscrição, pode selecionar uma conta de armazenamento existente ou pode clicar em **adicionar** para escolher uma conta de uma subscrição diferente.
     
     ![Configurar o dispositivo como servidor de iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Clique em **configurar** para concluir ao configurar o servidor de iSCSI.
   
    ![Configurar o dispositivo como servidor de iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Será notificado de que a criação de servidor de iSCSI está em curso. Depois do servidor de iSCSI é criado com êxito, o **dispositivos** painel está atualizado e o estado de dispositivo correspondente é **Online**.
   
    ![Configurar o dispositivo como servidor de iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Passo 3: Adicionar um volume

1. No **dispositivos** painel, selecione o dispositivo que acabou configurado como um servidor de iSCSI. Clique em **...**  (em alternativa com o botão direito nesta linha) e no menu de contexto, selecione **adicionar volume**. Também pode clicar em **+ adicionar volume** na barra de comando. Isto abre-se a **adicionar volume** painel.
   
    ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. No **adicionar volume** painel, efetue o seguinte procedimento:
   
   * No **nome do Volume** campo, introduza um nome exclusivo para o volume. O nome tem de ser uma cadeia que contém 3 e 127 carateres.
   * No **tipo** pendente lista, especifique se pretende criar um **em camadas** ou **afixado localmente** volume. Para cargas de trabalho que necessitem de garantias locais, latências baixas e desempenho superior, selecione **afixado localmente** **volume**. Para todos os outros dados, selecione **em camadas** **volume**.
   * No **capacidade** campo, especifique o tamanho do volume. Um volume em camadas tem de estar entre 500 GB e 5 TB e um volume localmente afixado tem de estar entre 50 GB e 500 GB.
     
     Um volume localmente afixado é fortemente aprovisionado e assegura que os dados primários no volume permanecem no dispositivo e não transbordam para a nuvem.
     
     Um volume em camadas por outro lado é fracamente aprovisionado. Quando cria um volume em camadas, aproximadamente, 10% do espaço de é aprovisionado no escalão local e 90% do espaço é aprovisionado na nuvem. Por exemplo, se aprovisionar um volume de 1 TB, 100 GB deverá residir no espaço local e 900 GB seria utilizada na nuvem quando as camadas de dados. Isto implica por sua vez é que o se executar a partir do espaço local no dispositivo, não é possível aprovisionar uma partilha em camadas (porque a 10% não estará disponível).
     
     ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Clique em **ligados a anfitriões**, selecione um registo de controlo de acesso (ACR) correspondente para o iniciador iSCSI que pretende ligar a este volume e, em seguida, clique em **selecione**. <br><br> 
3. Para adicionar um novo anfitrião ligado, clique em **adicionar novo**, introduza um nome para o anfitrião e o iSCSI nome qualificado (IQN) e, em seguida, clique em **adicionar**. Se não tiver o IQN, aceda a [apêndice a: obter o IQN de um anfitrião Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Quando tiver terminado de configurar o volume, clique em **OK**. Será criado um volume com as definições especificadas e verá uma notificação. Por predefinição, monitorização e de cópia de segurança serão ativadas para o volume.
   
     ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Para confirmar que o volume foi criado com êxito, vá para o **Volumes** painel. Deverá ver o volume listado.
   
   ![Adicionar um volume](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Passo 4: Montar, inicializar e formatar um volume

Execute os seguintes passos para montar, inicializar e formatar os volumes do StorSimple num anfitrião Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Para montar, inicializar e formatar um volume

1. Abra o **iniciador iSCSI** aplicação no servidor adequado.
2. Na janela **Propriedades do Iniciador iSCSI**, no separador **Deteção**, clique em **Detetar Portal**.
   
    ![detetar portal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. Na caixa de diálogo **Detetar Portal de Destino**, forneça o endereço IP da sua interface de rede ativada com permissão para iSCSI e clique em **OK**.
   
    ![Endereço IP](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. Na janela **Propriedades do Iniciador iSCSI**, no separador **Destinos**, localize os **Destinos Detetados**. (Cada volume será um destino detetado.). O estado do dispositivo deve ser apresentado como **Inativo**.
   
    ![destinos detetados](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Selecione um dispositivo de destino e, em seguida, clique em **Connect**. Quando o dispositivo estiver ligado, o estado deverá mudar para **Ligado**. (Para obter mais informações sobre como utilizar o Iniciador do Microsoft iSCSI, consulte [iniciador iSCSI instalar e configurar o Microsoft][1].
   
    ![Selecione o dispositivo de destino](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. No anfitrião do Windows, prima a tecla do Logótipo do Windows + X e clique em **Executar**.
7. Na caixa de diálogo **Executar**, escreva **Diskmgmt.msc**. Clique em **OK**. Será apresentada a caixa de diálogo **Gestão de Discos**. O painel da direita apresentará os volumes no anfitrião.
8. Na janela **Gestão de Discos**, os volumes montados serão apresentados como mostrado na ilustração seguinte. Clique com o botão direito do rato no volume detetado (clique no nome do disco) e, em seguida, clique em **Online**.
   
    ![gestão de discos](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Clique com botão direito e selecione **Inicializar disco**.
   
    ![Inicialize o disco 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. Na caixa de diálogo, selecione os discos inicializar e, em seguida, clique em **OK**.
    
    ![Inicialize o disco 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Inicia o Assistente de novo Volume simples. Selecione um tamanho de disco e, em seguida, clique em **seguinte**.
    
    ![Assistente de novo volume 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Atribuir uma letra de unidade ao volume e, em seguida, clique em **seguinte**.
    
    ![Assistente de novo volume 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Introduza os parâmetros para formatar o volume. **No Windows Server, NTFS só é suportada.** Defina o tamanho da unidade de alocação para 64K. Forneça uma etiqueta para o volume. É uma melhor prática recomendada para este nome de ser idêntico ao nome do volume fornecido na sua matriz de Virtual StorSimple. Clique em **Seguinte**.
    
    ![Assistente de novo volume 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Verifique os valores para o volume e, em seguida, clique em **concluir**.
    
    ![Assistente de novo volume 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Os volumes serão apresentados como **Online** no **gestão de discos** página.
    
    ![volumes online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar local web da IU para [administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Apêndice a: obter o IQN de um anfitrião Windows Server

Execute os passos seguintes para obter o Nome Qualificado do iSCSI (IQN) de um anfitrião do Windows que está a executar o Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Para obter o IQN de um anfitrião do Windows

1. Inicie o iniciador do Microsoft iSCSI no anfitrião do Windows.
2. Na janela **Propriedades do Iniciador iSCSI**, no separador **Configuração**, selecione e copie a cadeia do campo **Nome do Iniciador**.
   
    ![Propriedades do iniciador iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Guarde esta cadeia.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



