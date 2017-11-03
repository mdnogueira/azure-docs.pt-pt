---
title: Configurar StorSimple matriz Virtual como servidor de ficheiros | Microsoft Docs
description: "Este tutorial terceiro na implementação de matriz Virtual StorSimple dá instruções ao configurar um dispositivo virtual como servidor de ficheiros."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bf507fb21b314a6811db1c1e45a4356381caada1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Implementar StorSimple Virtual matriz - conjunto de cópias de segurança como servidor de ficheiros através do portal do Azure
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introdução
Este artigo descreve como efetuar a configuração inicial, registar o servidor de ficheiros do StorSimple, concluir a configuração de dispositivo e criar e ligar a partilhas SMB. Este é o último artigo da série de tutoriais de implementação necessários para implementar completamente a matriz de virtual como um servidor de ficheiros ou um servidor de iSCSI.

O processo de instalação e configuração pode demorar cerca de 10 minutos a concluir. As informações neste artigo aplica-se apenas a implementação da matriz Virtual StorSimple. Para a implementação de dispositivos de série 8000 do StorSimple, aceda a: [implementar o dispositivo da série 8000 do StorSimple com a atualização 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Pré-requisitos do programa de configuração
Antes de configurar e configurar a matriz de Virtual StorSimple, certifique-se de que:

* Ter aprovisionado uma matriz de virtual e ligados à mesma, conforme detalhado no [aprovisionar uma matriz de Virtual StorSimple no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou [aprovisionar uma matriz de Virtual StorSimple no VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Tem a chave de registo do serviço do serviço do Gestor de dispositivos do StorSimple que criou para gerir as matrizes de Virtual StorSimple. Para obter mais informações, consulte [passo 2: obter a chave de registo do serviço](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) matriz Virtual StorSimple.
* Se esta for a segunda virtual matriz com um serviço StorSimple Manager de dispositivo que está a registar, deve ter a chave de encriptação de dados do serviço. Esta chave foi gerada quando o primeiro dispositivo foi registado com êxito com este serviço. Se tiver perdido a esta chave, consulte [obter a chave de encriptação de dados do serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) para a matriz de Virtual StorSimple.

## <a name="step-by-step-setup"></a>Programa de configuração passo a passo
Utilize as seguintes instruções passo a passo para configurar e a matriz de Virtual StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passo 1: Concluir a configuração de IU da web locais e registar o seu dispositivo
#### <a name="to-complete-the-setup-and-register-the-device"></a>Para concluir a configuração e registar o dispositivo
1. Abra uma janela do browser e estabelecer ligação com a IU da web do local. Escreva:
   
   `https://<ip-address of network interface>`
   
   Utilize o URL de ligação que anotou no passo anterior. Verá um erro que indica que não há um problema com o certificado de segurança do Web site. Clique em **continuar para esta página Web**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Inicie sessão no web IU da sua matriz virtual como **StorSimpleAdmin**. Introduza a palavra-passe de administrador dispositivo que alterou no passo 3: iniciar a matriz virtual [aprovisionar uma matriz de Virtual StorSimple no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) ou no [aprovisionar uma matriz de Virtual StorSimple no VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. É direcionado para o **home page** página. Esta página descreve as várias definições necessárias para configurar e registar a matriz de virtual com o serviço do Gestor de dispositivos do StorSimple. O **as definições de rede**, **as definições de proxy de Web**, e **tempo definições** são opcionais. As definições necessárias apenas são **definições do dispositivo** e **definições da Cloud**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. No **as definições de rede** página em **interfaces de rede**, dados 0 serão configurados automaticamente para si. Cada interface de rede está definido por predefinição, para obter automaticamente endereço IP (DHCP). Por conseguinte, um endereço IP, uma sub-rede e gateway são automaticamente atribuídos (para IPv4 e IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Se tiver adicionado mais de uma interface de rede durante o aprovisionamento do dispositivo, pode configurá-las aqui. Tenha em atenção de que pode configurar a sua interface de rede como IPv4 apenas ou como IPv4 e IPv6. Configurações de apenas de IPv6 não são suportadas.
5. Servidores DNS são necessários porque são utilizados quando o dispositivo tenta comunicar com os fornecedores de serviços de armazenamento na nuvem ou para resolver o seu dispositivo por nome quando configurado como um servidor de ficheiros. No **as definições de rede** página sob o **servidores DNS**:
   
   1. Um servidor DNS primário e secundário são configuradas automaticamente. Se optar por configurar endereços IP estáticos, pode especificar servidores DNS. Para elevada disponibilidade, recomendamos que configure um site primário e um servidor DNS secundário.
   2. Clique em **aplicar** para aplicar e Valide as definições de rede.
6. No **definições do dispositivo** página:
   
   1. Atribuir um único **nome** para o seu dispositivo. Este nome pode ter 1-15 carateres e pode conter letra, números e hífenes.
   2. Clique em de **servidor de ficheiros** ícone ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) para o **tipo** de dispositivo que está a criar. Um servidor de ficheiros irá permitir-lhe criar as pastas partilhadas.
   3. Como o seu dispositivo é um servidor de ficheiros, terá de associar o dispositivo a um domínio. Introduza um **nome de domínio**.
   4. Clique em **Aplicar**.
7. Será apresentada uma caixa de diálogo. Introduza as credenciais de domínio no formato especificado. Clique no ícone de verificação. As credenciais de domínio são verificadas. Verá uma mensagem de erro se as credenciais estão incorretas.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Clique em **Aplicar**. Este procedimento irá aplicar e validar as definições do dispositivo.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Certifique-se de que a matriz de virtual destina-se no seu próprio unidade organizacional (UO) do Active Directory e não existem objetos de política de grupo (GPO) são aplicados à mesma ou herdados. Política de grupo pode instalar aplicações como o software de antivírus na matriz Virtual StorSimple. Instalar o software adicional não é suportada e pode levar a danos nos dados. 
   > 
   > 
9. (Opcionalmente)-configure o servidor proxy web. Apesar da configuração de proxy web é opcional, lembre-se de que se utilizar um proxy web, só pode configurá-lo aqui.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   No **Web proxy** página:
   
   1. Forneça o **URL de proxy Web** neste formato: *http://&lt;endereço IP do anfitrião ou FDQN&gt;: número de porta*. Tenha em atenção que não são suportados HTTPS URLs.
   2. Especifique **autenticação** como **básico** ou **nenhum**.
   3. Se utilizar a autenticação, também terá de fornecer um **Username** e **palavra-passe**.
   4. Clique em **Aplicar**. Isto irá validar e aplicar as definições de proxy web configurada.
10. (Opcionalmente) configure as definições de tempo para o seu dispositivo, como o fuso horário e os servidores NTP primário e secundário. Os servidores NTP são necessários porque o seu dispositivo deve sincronizar a hora para se poder autenticar com os fornecedores de serviços em nuvem.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    No **tempo definições** página:
    
    1. Na lista pendente, selecione o **fuso horário** com base na localização geográfica na qual o dispositivo está a ser implementado. O fuso horário predefinido para o dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.
    2. Especifique um **servidor NTP primário** para o seu dispositivo ou aceite o valor predefinido de time.windows.com. Verifique se a sua rede permite que o tráfego NTP passe do datacenter para a Internet.
    3. Opcionalmente, especifique um **servidor NTP secundário** para o seu dispositivo.
    4. Clique em **Aplicar**. Isto irá validar e aplicar as definições de tempo configurado.
11. Configure as definições da nuvem para o seu dispositivo. Neste passo, irá concluir a configuração de dispositivo local e, em seguida, registar o dispositivo com o serviço do Gestor de dispositivos do StorSimple.
    
    1. Introduza o **chave de registo do serviço** que obteve [passo 2: obter a chave de registo do serviço](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) matriz Virtual StorSimple.
    2. Se este for o primeiro dispositivo de registar este serviço, será apresentada com o **chave de encriptação de dados do serviço**. Copie esta chave e guarde-a numa localização segura. Esta chave é necessária com a chave de registo do serviço para registar dispositivos adicionais com o serviço do Gestor de dispositivos do StorSimple. 
       
       Se não for o primeiro dispositivo de que está a registar com este serviço, terá de fornecer a chave de encriptação de dados do serviço. Para obter mais informações, consulte para obter o [chave de encriptação de dados do serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) no seu local de IU da web.
    3. Clique em **registar**. Esta ação irá reiniciar o dispositivo. Poderá ter de aguardar 2 a 3 minutos antes do dispositivo está registado com êxito. Depois do dispositivo tem de ser reiniciado, será conduzido para o página de início de sessão.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Regresse ao portal do Azure. Aceda a **todos os recursos**, procure o seu serviço do Gestor de dispositivos do StorSimple.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Na lista filtrada, selecione o seu serviço do Gestor de dispositivos do StorSimple e, em seguida, navegue para **gestão > dispositivos**. No **dispositivos** painel, certifique-se de que o dispositivo foi ligado com êxito para o serviço e tem o estado **preparado para configurar**.
    
    ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Passo 2: Configurar o dispositivo como servidor de ficheiros
Execute os seguintes passos no [portal do Azure](https://portal.azure.com/) para concluir a configuração de dispositivos necessários.

#### <a name="to-configure-the-device-as-file-server"></a>Para configurar o dispositivo como servidor de ficheiros
1. Aceda ao seu serviço do Gestor de dispositivos do StorSimple e, em seguida, aceda a **gestão > dispositivos**. No **dispositivos** painel, selecione o dispositivo que acabou de criar. Este dispositivo seria apresentados como **preparado para configurar**.
   
   ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Clique no dispositivo e verá uma mensagem de faixa que indica se o dispositivo está pronto para a configuração.
   
    ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Clique em **configurar** na barra de comandos. Isto abre-se a **configurar** painel. No **configurar** painel, efetue o seguinte procedimento:
   
    1. O nome do servidor de ficheiros é preenchido automaticamente.
    
    2. Certifique-se a encriptação de armazenamento na nuvem está definida como **ativado**. Isto irá encriptar todos os dados que são enviados para a nuvem. 
    
    3. Uma chave AES 256 bits é utilizada com a chave definida pelo utilizador para a encriptação. Especifique uma chave de 32 carateres e, em seguida, reintroduza a chave para confirmá-la. Registe a chave de uma aplicação de gestão de chaves para consulta futura.
    
    4. Clique em **configurar definições necessárias** para especificar as credenciais da conta de armazenamento a ser utilizado com o seu dispositivo. Clique em **adicionar novo** se existem não existem credenciais de conta de armazenamento configuradas. **Certifique-se de que a conta de armazenamento que utiliza suporta blobs de blocos. Os blobs de página não são suportados.** Obter mais informações sobre [bloqueia os blobs e blobs de páginas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
    ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. No **adicionar um credenciais da conta do storage** painel, efetue o seguinte procedimento: 

    1. Escolha subscrição atual, se a conta do storage na mesma subscrição que o serviço. Especifique outro é o armazenamento conta está fora da subscrição do serviço. 
    
    2. Na lista pendente, escolha uma conta de armazenamento existente. 
    
    3. A localização será automaticamente preenchida com base na conta de armazenamento especificada. 
    
    4. Ative o SSL, e certifique-se de um canal de comunicação de rede segura entre o dispositivo e a nuvem.
    
    5. Clique em **adicionar** para adicionar esta credencial de conta de armazenamento. 
   
        ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Assim que a credencial da conta de armazenamento é criada com êxito, o **configurar** painel será atualizado para apresentar as credenciais da conta de armazenamento especificada. Clique em **Configurar**.
   
   ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Verá um ficheiro de um servidor está a ser criado. Depois do servidor de ficheiros é criado com êxito, será notificado.
   
   ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   O estado do dispositivo também será alterado para **Online**.
   
   ![Configurar um servidor de ficheiros](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Pode avançar para adicionar uma partilha.

## <a name="step-3-add-a-share"></a>Passo 3: Adicionar uma partilha
Execute os seguintes passos no [portal do Azure](https://portal.azure.com/) para criar uma partilha.

#### <a name="to-create-a-share"></a>Para criar uma partilha
1. Selecione o dispositivo de servidor de ficheiros que configurou no passo anterior e clique em **...**  (ou clique com botão direito). No menu de contexto, selecione **Adicionar partilha**. Em alternativa, pode clicar em **+ Adicionar partilha** na barra de comando de dispositivo.
   
   ![Adicione uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Especifique as seguintes definições de partilha:

    1. Um nome exclusivo para a partilha. O nome tem de ser uma cadeia que contém 3 e 127 carateres.
    
    2. Opcional **Descrição** para a partilha. A descrição irão ajudá-lo a identificar os proprietários de partilha.
    
    3. A **tipo** para a partilha. O tipo pode ser **em camadas** ou **afixado localmente**, com camadas que está a ser a predefinição. Para cargas de trabalho que necessitem de garantias locais, latências baixas e desempenho superior, selecione um **afixado localmente** partilhar. Para todos os outros dados, selecione um **em camadas** partilhar.
    Uma partilha localmente afixada é fortemente aprovisionada e assegura que os dados primários na partilha de permanecem locais no dispositivo e não transbordam para a nuvem. Uma partilha em camadas por outro lado é fracamente aprovisionada. Quando cria uma partilha em camadas, 10% do espaço é aprovisionado no escalão local e 90% do espaço é aprovisionado na nuvem. Por exemplo, se aprovisionar um volume de 1 TB, 100 GB deverá residir no espaço local e 900 GB seria utilizada na nuvem quando as camadas de dados. Por sua vez Isto implica que, se executar a partir do espaço local no dispositivo, não é possível aprovisionar uma partilha em camadas.
   
    4. No **predefinir permissões totais** campo, atribua as permissões para o utilizador ou grupo que está a aceder a esta partilha. Especifique o nome de utilizador ou o grupo de utilizadores no  *john@contoso.com*  formato. Recomendamos que utilize um grupo de utilizadores (em vez de um utilizador único) para permitir que os privilégios de administrador para aceder a estas partilhas. Depois de ter de atribuir as permissões aqui, em seguida, pode utilizar o Explorador de ficheiros para modificar estas permissões.
   
    5. Clique em **adicionar** para criar a partilha. 
    
        ![Adicione uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
        São notificado de que a criação de partilha está em curso.
   
        ![Adicione uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
    Depois da partilha for criada com as definições especificadas, o **partilhas** painel será atualizado para refletir a nova partilha. Por predefinição, a monitorização e de cópia de segurança estão ativadas para a partilha.
   
    ![Adicione uma partilha](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Passo 4: Ligar à partilha de
Agora precisa de ligar a uma ou mais partilhas que criou no passo anterior. Execute estes passos no anfitrião do Windows Server ligado para a matriz de Virtual StorSimple.

#### <a name="to-connect-to-the-share"></a>Para ligar à partilha de
1. Prima ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. Na janela executar, especifique o *&#92; &#92;&lt; nome do servidor de ficheiros&gt;*  como o caminho, substituindo *nome do servidor de ficheiros* com o nome do dispositivo atribuído ao seu servidor de ficheiros. Clique em **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Esta ação abre o Explorador de ficheiros de cópia de segurança. Agora deverá conseguir ver as partilhas que criou como pastas. Selecione e faça duplo clique uma partilha (pasta) para ver o conteúdo.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Agora pode adicionar ficheiros a estas partilhas e efetuar uma cópia de segurança.

## <a name="next-steps"></a>Passos seguintes
Saiba como utilizar local web da IU para [administrar a matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).

