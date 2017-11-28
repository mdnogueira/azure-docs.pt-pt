---
title: "Migrar os dados no StorSimple série de 5000 7000 dispositivo da 8000 série | Microsoft Docs"
description: "Fornece uma descrição geral e os pré-requisitos da funcionalidade de migração."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: b5c84dbea574afceba93d978425a70ce33271e36
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrar dados de série do StorSimple 5000 7000 dispositivo da 8000 série

> [!IMPORTANT]
> - Migração atualmente é uma operação assistida. Se pretender migrar dados do dispositivo StorSimple 5000 7000 série para um dispositivo de série 8000, tem de agendar a migração com Support da Microsoft. Support da Microsoft, em seguida, irá ativar a sua subscrição para a migração. Para obter mais informações, consulte como [abrir um pedido de suporte](storsimple-8000-contact-microsoft-support.md).
> - Depois de o ficheiro de pedido de serviço, poderá demorar algumas semanas para executar o plano de migração e, na verdade, iniciar a migração.
> - Antes de contactar o Support da Microsoft, ser concluída e confirme que leia o [pré-requisitos de migração](#migration-prerequisites) indicados no artigo.

## <a name="overview"></a>Descrição geral

Este artigo apresenta a funcionalidade de migração que permite que os clientes de séries 5000 7000 StorSimple migrar os dados para o dispositivo físico de série 8000 do StorSimple ou os dispositivos 8010/8020 nuvem. Este artigo também contém ligações para instruções passo a passo transferível dos passos necessários para migrar dados a partir de um 5000 7000 série legado do dispositivo uma série 8000 físico ou dispositivo de nuvem.

Este artigo se aplica tanto o dispositivo de série 8000 no local, bem como a aplicação de nuvem do StorSimple.


## <a name="migration-feature-versus-host-side-migration"></a>Funcionalidade de migração em comparação com a migração de lado do anfitrião

Pode mover dados utilizando a funcionalidade de migração ou através de uma migração de lado do anfitrião. Esta secção descreve as especificações de cada método, incluindo as profissionais de TI e contras. Utilize estas informações para descobrir qual o método que pretende pursue para migrar os dados.

A funcionalidade de migração simula um processo de recuperação (DR) após desastre de séries 7000/5000 a 8000 série. Esta funcionalidade permite-lhe migrar os dados de formato de séries 5000/7000 para o formato de série 8000 no Azure. O processo de migração é iniciado utilizando a ferramenta de migração do StorSimple. A ferramenta inicia a transferência e a conversão de metadados de cópia de segurança no dispositivo de série 8000 e, em seguida, utiliza a cópia de segurança mais recente para expor os volumes no dispositivo.

|      | Profissionais de TI                                                                                                                                     |Contras                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | O processo de migração preserva o histórico de cópias de segurança que foram executadas nas séries 5000/7000.                                               | Quando os utilizadores tentam aceder aos dados, esta migração irá transferir os dados a partir do Azure, por conseguinte, incorrer em custos de transferência de dados.                                     |
| 2.   | Não existem dados migrados no lado do anfitrião.                                                                                                     | O processo necessita de período de indisponibilidade entre o início da cópia de segurança e mais recente cópia de segurança que está a ser anexados da 8000 série (podem ser estimadas utilizando a ferramenta de migração). |
| 3.   | Este processo preserva todas as políticas, modelos de largura de banda, encriptação e outras definições em dispositivos de 8000 série.                      | Acesso de utilizador irá recuperar apenas os dados acedidos pelos utilizadores e não será rehydrate todo o conjunto de dados.                                                  |
| 4.   | Este processo necessita de mais tempo para converter todas as cópias de segurança mais antigas no Azure que é feita de forma assíncrona, sem afetar a produção | Só pode ser efetuada a migração de um nível de configuração de nuvem.  Volumes individuais numa configuração de nuvem não podem ser migradas separadamente                       |

Uma migração de lado do anfitrião permite configurar da 8000 série de forma independente e copiar os dados dos dispositivos de série de 5000/7000 dispositivo da 8000 série. Isto é equivalente ao migrar dados a partir de um dispositivo de armazenamento para outro. Uma variedade de ferramentas, tais como Diskboss, robocopy são utilizados para copiar os dados.

|      | Profissionais de TI                                                                                                                      |Contras                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Migração pode ser approached de forma faseada numa base de volume por volume.                                               | Cópias de segurança anteriores (taken nas séries 5000/7000) não estará disponíveis no dispositivo 8000 série.                                                                                                       |
| 2.   | Permite a consolidação de dados para uma conta de armazenamento no Azure.                                                       | Primeira cópia de segurança para a nuvem em 8000 série irá demorar mais tempo como todos os dados nas necessidades da 8000 série a cópia de segurança para o Azure.                                                                     |
| 3.   | Os seguintes uma migração com êxito, é local no dispositivo de todos os dados. Não existem nenhum latências de acesso aos dados. | Consumo de armazenamento do Azure irá aumentar até que os dados são eliminados do dispositivo 5000/7000.                                                                                                        |
| 4.   |                                                                                                                           | Se o dispositivo de séries 7000/5000 tiver uma grande quantidade de dados, durante a migração estes dados tem de ser transferido a partir do azure que irá implicar custos e latências relacionadas com a transferência de dados do Azure |

Este artigo foca-se apenas na funcionalidade de migração de 5000/7000 para dispositivos de série 8000. Para obter mais informações sobre a migração de lado do anfitrião, aceda a [migração a partir de outros dispositivos de armazenamento](http://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating Data to StorSimple Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

Seguem-se os pré-requisitos de migração para o dispositivo de séries 5000 ou 7000 legado e o dispositivo do StorSimple 8000 série.

> [!IMPORTANT]
> Reveja e conclua os pré-requisitos de migração antes de o ficheiro um pedido de serviço com Support da Microsoft.

### <a name="for-the-50007000-series-device-source"></a>Para o dispositivo de séries 5000/7000 (origem)

Antes de começar a migração, certifique-se de que:

* Tem a 5000 ou 7000 séries de dispositivo de origem o dispositivo pode estar em direto ou descendente.

    > [!IMPORTANT]
    > Recomendamos que tenha acesso de série para este dispositivo ao longo do processo de migração. Acesso de série devem existir problemas de dispositivo, pode ajudar na resolução de problemas.

* O dispositivo de origem das séries 5000 ou 7000 está em execução v2.1.1.518 de versão do software. Versões anteriores não são suportadas.
* Para verificar a versão que está a executar o seu séries 5000 ou 7000, observe o canto superior direito da IU do Web. Isto deve apresentar a versão do software que está a executar o seu dispositivo. Para a migração, as séries 5000 ou 7000 devem executar v2.1.1.518.

    ![Verificar a versão de software no dispositivo legado](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Se o dispositivo em direto não está em execução v2.1.1.518, atualize o sistema para a versão mínima necessária. Para obter instruções detalhadas, consulte [actualizar o sistema para v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Se estiver a executar v2.1.1.518, aceda ao web da IU para ver se existem quaisquer notificações para falhas de restauro do registo. Se tinha falha no restauro do registo, execute o restauro do registo. Para obter mais informações, aceda a como [executar restauro do registo](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Se tiver um dispositivo baixo que não estava a ser executado v2.1.1.518, execute uma ativação pós-falha para um dispositivo de substituição que está a executar v2.1.1.518. Para obter instruções detalhadas, consulte a DR do dispositivo StorSimple série de 5000/7000.
    * Fazer uma cópia de segurança de dados para o seu dispositivo um instantâneo de nuvem.
    * Verifique a existência de quaisquer outras cópias de segurança tarefas ativas que estão em execução no dispositivo de origem. Isto inclui as tarefas no anfitrião da consola de proteção de dados do StorSimple. Aguarde que as tarefas atuais concluir.


### <a name="for-the-8000-series-physical-device-target"></a>Para o dispositivo físico da 8000 série (destino)

Antes de começar a migração, certifique-se de que:

* O dispositivo de série 8000 do destino está registado e em execução. Para obter mais informações, consulte como [implementar o dispositivo StorSimple com o serviço StorSimple Manager](storsimple-8000-deployment-walkthrough-u2.md).
* O dispositivo da 8000 série tem o mais recente StorSimple 8000 série Update 5 instalado e em execução 6.3.9600.17845 ou versão posterior. Se o dispositivo não tem as atualizações mais recentes instaladas, terá de instalar as atualizações mais recentes antes de poder continuar com a migração. Para obter mais informações, consulte como [instalar a atualização mais recente no seu dispositivo da 8000 série](storsimple-8000-install-update-5.md).
* A subscrição do Azure está ativada para a migração. Se não estiver ativada a sua subscrição, contacte Support da Microsoft para ativar a sua subscrição para a migração.

### <a name="for-the-80108020-cloud-appliance-target"></a>Para a aplicação de nuvem 8010/8020 (destino)

Antes de começar a migração, certifique-se:

* A aplicação de nuvem de destino está registado e em execução. Para obter mais informações, consulte como [implementar e gerir o dispositivo de nuvem do StorSimple](storsimple-8000-cloud-appliance-u2.md).
* A aplicação de nuvem está a executar o mais recente StorSimple 8000 série Update 5 6.3.9600.17845 de versão do software. Se a sua aplicação na nuvem não está em execução Update 5, crie um novo dispositivo de nuvem de Update 5 antes de continuar com a migração. Para obter mais informações, consulte como [criar uma aplicação de nuvem 8010/8020](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Para o computador que executa a ferramenta de migração do StorSimple

Ferramenta de migração do StorSimple é uma ferramenta de baseadas na IU de mensagens em fila que lhe permite migrar dados do StorSimple série de 5000 7000 para um dispositivo de série 8000. Para instalar a ferramenta de migração do StorSimple, utilize um computador que cumpra os seguintes requisitos.

O computador tem conectividade à Internet e:

* Está a executar o sistema operativo seguinte
    * Windows 10.
    * Windows Server 2012 R2 (ou superior) para instalar a ferramenta de migração do StorSimple.
* Instalou a .NET 4.5.2.
* Tem um mínimo de 5 GB de espaço livre para instalar e utilizar a ferramenta.

> [!TIP]
> Se o dispositivo StorSimple está ligado a um anfitrião Windows Server, pode instalar a ferramenta de migração no computador anfitrião Windows Server.

#### <a name="to-install-storsimple-migration-tool"></a>Para instalar a ferramenta de migração do StorSimple

Execute os seguintes passos para instalar a ferramenta de migração do StorSimple no seu computador.

1. Copie a pasta _StorSimple8000SeriesMigrationTool_ no seu computador Windows. Certifique-se de que a unidade em que o software é copiado tem espaço suficiente.

    Abra o ficheiro de configuração da ferramenta _StorSimple8000SeriesMigrationTool.exe.config_ na pasta. Eis o fragmento do ficheiro.
    
    ```
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Edite os valores correspondentes para as chaves e substitua por:

    * `UserName`– Nome o utilizador iniciar sessão no portal do Azure.
    * `SubscriptionName and SubscriptionId`– O nome e o ID da sua subscrição do Azure. No seu serviço do Gestor de dispositivos do StorSimple destino página, em **geral**, clique em **propriedades**. Copie o nome da subscrição e o ID de subscrição associado ao seu serviço.
    * `ResourceName`– Nome do seu dispositivo do serviço StorSimple Manager no portal do Azure. Também é apresentado em Propriedades do serviço.
    * `ResourceGroup`– O nome do grupo de recursos associado com o seu dispositivo do serviço StorSimple Manager no portal do Azure. Também é apresentado em Propriedades do serviço.
    ![Verifique as propriedades do serviço para o dispositivo de destino](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`– O azure Active Directory ID do inquilino no portal do Azure. Inicie sessão no Microsoft Azure como administrador. No portal do Microsoft Azure, clique em **do Azure Active Directory**. Em **gerir**, clique em **propriedades**. O inquilino ID é apresentado no **ID de diretório** caixa.
    ![Verifique o ID de inquilino do Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Guarde as alterações feitas no ficheiro de configuração.
4.  Execute o _StorSimple8000SeriesMigrationTool.exe_ para iniciar a ferramenta. Quando lhe forem pedidas as credenciais, forneça as credenciais associadas com a sua subscrição no portal do Azure. 
5.  É apresentada a ferramenta de migração do StorSimple da IU.
  

## <a name="next-steps"></a>Passos seguintes
Transferir as instruções passo a passo sobre como [migrar dados de um StorSimple série de 5000 7000 num dispositivo da 8000 série](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).
