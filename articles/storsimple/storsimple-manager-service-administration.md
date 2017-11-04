---
title: "Administração do service Manager do StorSimple | Microsoft Docs"
description: "Saiba como gerir o dispositivo StorSimple ao utilizar o serviço StorSimple Manager no portal clássico do Azure."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2586582e-d85c-42e1-afb3-be734c1c0461
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: 22924da07434a06f4c822d97a2afd02ea982e0e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>Utilizar o serviço StorSimple Manager para administrar o dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Este artigo descreve a interface de serviço StorSimple Manager, incluindo como ligar a, as várias opções disponíveis e ligações de saída para os fluxos de trabalho específicas que podem ser efetuadas através deste IU. Esta orientação é aplicável a ambos; físico StorSimple e o dispositivo virtual.

Depois de ler este artigo, irá aprender a:

* Ligar ao serviço StorSimple Manager
* Navegue até a IU do StorSimple Manager
* Administrar o dispositivo StorSimple através do serviço StorSimple Manager

## <a name="connect-to-storsimple-manager-service"></a>Ligar ao serviço StorSimple Manager
O serviço StorSimple Manager é executado no Microsoft Azure e se liga a diversos dispositivos StorSimple. Utilize um central portal clássico de Microsoft Azure em execução num browser para gerir estes dispositivos. Para ligar ao serviço StorSimple Manager, execute o seguinte:

#### <a name="to-connect-to-the-service"></a>Para ligar ao serviço
1. Navegue para [https://manage.windowsazure.com/](https://manage.windowsazure.com/).
2. Utilizar as credenciais da conta Microsoft, inicie sessão no portal clássico do Microsoft Azure (localizado na parte superior direita do painel).
3. Desloque para baixo do painel de navegação à esquerda para aceder ao serviço StorSimple Manager.

## <a name="navigate-storsimple-manager-service-ui"></a>Navegue até o serviço StorSimple Manager IU
A hierarquia de navegação para o serviço StorSimple Manager IU é mostrada na seguinte tabela.

* **StorSimple Manager** página de destino leva-o para as páginas de nível de serviço de IU aplicáveis a todos os dispositivos dentro de um serviço.
* **Dispositivos** página leva-o para as páginas de IU de nível do dispositivo – aplicáveis a um dispositivo específico.
* **Os contentores de volume** página leva-o para a página de volume que mostra todos os volumes associados a um dispositivo.

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Hierarquia de navegação de serviço do StorSimple Manager
| Página de destino | Páginas de nível de serviço | Páginas ao nível do dispositivo | Páginas ao nível do dispositivo |
| --- | --- | --- | --- |
| Serviço StorSimple Manager |Dashboard de serviço |Dashboard de dispositivo | |
| Dispositivos → |Monitorizar | | |
| Catálogo de cópias de segurança |Containers→ de volume |Volumes | |
| Configurar (serviço) |Políticas de cópia de segurança | | |
| Tarefas |Configurar (dispositivos) | | |
| Alertas |Manutenção | | |

![Vídeo disponível](./media/storsimple-manager-service-administration/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que o orienta através da interface de utilizador do serviço StorSimple Manager, clique em [aqui](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/).

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>Administrar o dispositivo StorSimple com o serviço StorSimple Manager
A tabela seguinte mostra um resumo de todas as tarefas de gestão comuns e fluxos de trabalho complexos que podem ser executados no âmbito do serviço StorSimple Manager IU. Estas tarefas são organizadas com base nas páginas da IU em que são iniciadas.

Para obter mais informações sobre cada fluxo de trabalho, clique o procedimento adequado na tabela.

#### <a name="storsimple-manager-workflows"></a>Fluxos de trabalho do StorSimple Manager
| Se pretender efetuar este procedimento... | Visite esta página IU... | Utilize este procedimento. |
| --- | --- | --- |
| Criar um serviço</br>Eliminar um serviço</br>Obter a chave de registo do serviço</br>Regenerar a chave de registo do serviço |Serviço StorSimple Manager |[Implementar um serviço StorSimple Manager](storsimple-manage-service.md) |
| Alterar a chave de encriptação de dados do serviço</br>Consulte os registos de operação |Dashboard do StorSimple Manager serviço → |[Utilize o dashboard do serviço StorSimple Manager](storsimple-service-dashboard.md) |
| Desativar um dispositivo</br>Eliminar um dispositivo |Dispositivos do StorSimple Manager serviço → |[Desativar ou eliminar um dispositivo](storsimple-deactivate-and-delete-device.md) |
| Saiba mais sobre a ativação pós-falha de dispositivo e de recuperação de desastre</br>Ativação pós-falha para um dispositivo físico</br>Ativação pós-falha para um dispositivo virtual</br>Recuperação de desastre de continuidade de negócio (BCDR) |Dispositivos do StorSimple Manager serviço → |[Ativação pós-falha e recuperação após desastre para o dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md) |
| Lista as cópias de segurança para um volume</br>Selecione um conjunto de cópia de segurança</br>Eliminar um conjunto de cópia de segurança |Catálogo de cópia de segurança do StorSimple Manager serviço → |[Gerir cópias de segurança](storsimple-manage-backup-catalog.md) |
| Clonar um volume |Catálogo de cópia de segurança do StorSimple Manager serviço → |[Clonar um volume](storsimple-clone-volume.md) |
| Restaurar um conjunto de cópia de segurança |Catálogo de cópia de segurança do StorSimple Manager serviço → |[Restaurar um conjunto de cópia de segurança](storsimple-restore-from-backup-set.md) |
| Sobre as contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Eliminar uma conta do Storage</br>Rotação da chave de contas do storage |Configurar o StorSimple Manager serviço → |[Gerir contas de armazenamento](storsimple-manage-storage-accounts.md) |
| Sobre os modelos de largura de banda</br>Adicionar um modelo de largura de banda</br>Editar um modelo de largura de banda</br>Eliminar um modelo de largura de banda</br>Utilizar um modelo de largura de banda predefinido</br>Criar um modelo de largura de banda all-day que inicia no tempo especificado |Configurar o StorSimple Manager serviço → |[Gerir modelos de largura de banda](storsimple-manage-bandwidth-templates.md) |
| Sobre os registos de controlo de acesso</br>Criar um registo de controlo de acesso</br>Editar um registo de controlo de acesso</br>Elimine um registo de controlo de acesso |Configurar o StorSimple Manager serviço → |[Gerir registos de controlo de acesso](storsimple-manage-acrs.md) |
| Ver detalhes da tarefa</br>Cancelar uma tarefa |Tarefas do StorSimple Manager serviço → |[Gerir tarefas](storsimple-manage-jobs.md) |
| Receber notificações de alerta</br>Gerir alertas</br>Rever os alertas |Alertas do StorSimple Manager serviço → |[Ver e gerir alertas do StorSimple](storsimple-manage-alerts.md) |
| Ver iniciadores ligados</br>Determinar o número de série do dispositivo</br>Localizar o IQN de destino |Dispositivos de serviço → do StorSimple Manager → Dashboard |[Utilize o dashboard do dispositivo StorSimple](storsimple-device-dashboard.md) |
| Criar gráficos de monitorização |Dispositivos de serviço → do StorSimple Manager monitorizar → |[Monitorizar o dispositivo StorSimple](storsimple-monitor-device.md) |
| Adicionar um contentor de volume</br>Modificar um contentor de volume</br>Eliminar um contentor de volume |Contentores de Volume do StorSimple Manager serviço → dispositivos → |[Gerir contentores de volumes](storsimple-manage-volume-containers.md) |
| Adicionar um volume</br>Modificar um volume</br>Colocar offline um volume</br>Eliminar um volume</br>Monitorizar um volume |Volumes do StorSimple Manager serviço → dispositivos → contentores de Volume → |[Gerir volumes](storsimple-manage-volumes.md) |
| Modificar as definições do dispositivo</br>Modificar as definições de hora</br>Modificar as definições de DNS.md</br>Configurar as interfaces de rede |Dispositivos de serviço → do StorSimple Manager configurar → |[Modificar configuração de dispositivo para o dispositivo StorSimple](storsimple-modify-device-config.md) |
| Ver definições de proxy web |Dispositivos de serviço → do StorSimple Manager configurar → |[Configurar o proxy web para o seu dispositivo](storsimple-configure-web-proxy.md) |
| Modifique a palavra-passe de administrador do dispositivo</br>Modifique a palavra-passe do Snapshot Manager do StorSimple |Dispositivos de serviço → do StorSimple Manager configurar → |[Alterar as palavras-passe do StorSimple](storsimple-change-passwords.md) |
| Configurar a gestão remota |Dispositivos de serviço → do StorSimple Manager configurar → |[Ligar remotamente ao dispositivo StorSimple](storsimple-remote-connect.md) |
| Configurar definições de alerta |Dispositivos de serviço → do StorSimple Manager configurar → |[Ver e gerir alertas do StorSimple](storsimple-manage-alerts.md) |
| Configurar o CHAP para o dispositivo StorSimple |Dispositivos de serviço → do StorSimple Manager configurar → |[Configurar o CHAP para o dispositivo StorSimple](storsimple-configure-chap.md) |
| Adicionar uma política de cópias de segurança</br>Adicionar ou modificar uma agenda</br>Eliminar uma política de cópia de segurança</br>Efetuar uma cópia de segurança manual</br>Criar uma política de cópia de segurança personalizada com vários volumes e agendas |Políticas de cópia de segurança do StorSimple Manager serviço → dispositivos → |[Gerir políticas de cópia de segurança](storsimple-manage-backup-policies.md) |
| Parar os controladores de dispositivo</br>Reinicie os controladores de dispositivo</br>Encerrar os controladores de dispositivo</br>Repor o seu dispositivo para as predefinições de fábrica</br>(Acima destinam-se apenas a dispositivos no local) |Dispositivos de serviço → do StorSimple Manager → manutenção |[Gerir o controlador de dispositivo do StorSimple](storsimple-manage-device-controller.md) |
| Saiba mais sobre os componentes de hardware do StorSimple</br>Monitorizar o estado do hardware</br>(Acima destinam-se apenas a dispositivos no local) |Dispositivos de serviço → do StorSimple Manager → manutenção |[Componentes de hardware do monitor](storsimple-monitor-hardware-status.md) |
| Criar um pacote de suporte |Dispositivos de serviço → do StorSimple Manager → manutenção |[Criar e gerir um pacote de suporte](storsimple-create-manage-support-package.md) |
| Instalar atualizações de software |Dispositivos de serviço → do StorSimple Manager → manutenção |[Atualizar o seu dispositivo](storsimple-update-device.md) |

## <a name="next-steps"></a>Passos seguintes
Se surgirem problemas com a operação diária do dispositivo StorSimple ou com qualquer um dos respetivos componentes de hardware, consulte:

* [Resolver problemas de um dispositivo operacional](storsimple-troubleshoot-operational-device.md)
* [Utilizar monitorização LEDs indicador do StorSimple](storsimple-monitoring-indicators.md)

Se não é possível resolver os problemas e tem de criar um pedido de serviço, consulte [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md).

