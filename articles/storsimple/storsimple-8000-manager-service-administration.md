---
title: "Administração de serviço do Gestor de dispositivos do StorSimple | Microsoft Docs"
description: "Saiba como gerir o dispositivo StorSimple ao utilizar o serviço do Gestor de dispositivos do StorSimple no portal do Azure."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: 0e7d7f44a70278a7777ba6c32c8e546074953fdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve a interface de serviço do Gestor de dispositivos do StorSimple, incluindo como ligar, as várias opções disponíveis e ligações de saída para os fluxos de trabalho específicas que podem ser efetuadas através deste IU. Esta orientação é aplicável a ambos; o dispositivo físico StorSimple e a aplicação de nuvem.

Depois de ler este artigo, irá aprender a:

* Ligar ao serviço do Gestor de dispositivos do StorSimple
* Administrar o dispositivo StorSimple através do serviço do Gestor de dispositivos do StorSimple

## <a name="connect-to-storsimple-device-manager-service"></a>Ligar ao serviço do Gestor de dispositivos do StorSimple

O serviço do Gestor de dispositivos do StorSimple é executado no Microsoft Azure e se liga a diversos dispositivos StorSimple. Utilizar um portal central do Microsoft Azure em execução num browser para gerir estes dispositivos. Para ligar ao serviço do Gestor de dispositivos do StorSimple, efetue o seguinte.

#### <a name="to-connect-to-the-service"></a>Para ligar ao serviço
1. Navegue para [https://portal.azure.com/](https://portal.azure.com/).
2. Utilizar as credenciais da conta Microsoft, inicie sessão no portal do Microsoft Azure (localizado na parte superior direita do painel).
3. Percorra o painel de navegação à esquerda para aceder ao serviço do Gestor de dispositivos do StorSimple.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administrar o dispositivo StorSimple com o serviço do Gestor de dispositivos do StorSimple

A tabela seguinte mostra um resumo de todas as tarefas de gestão comuns e fluxos de trabalho complexos que podem ser executados no âmbito do serviço do Gestor de dispositivos do StorSimple da IU. Estas tarefas são organizadas com base nos painéis de IU em que são iniciadas.

Para obter mais informações sobre cada fluxo de trabalho, clique o procedimento adequado na tabela.

#### <a name="storsimple-device-manager-workflows"></a>Fluxos de trabalho do Gestor de dispositivos do StorSimple

| Se pretender efetuar este procedimento... | Utilize este procedimento. |
| --- | --- |
| Criar um serviço</br>Eliminar um serviço</br>Obter a chave de registo do serviço</br>Regenerar a chave de registo do serviço |[Implementar um serviço do Gestor de dispositivos do StorSimple](storsimple-8000-manage-service.md) |
| Consulte os registos de atividade |[Utilize o resumo de serviço do Gestor de dispositivos do StorSimple](storsimple-8000-service-dashboard.md) |
| Alterar a chave de encriptação de dados do serviço</br>Consulte os registos de operação |[Utilize o dashboard de serviço do Gestor de dispositivos do StorSimple](storsimple-8000-service-dashboard.md) |
| Desativar um dispositivo</br>Eliminar um dispositivo |[Desativar ou eliminar um dispositivo](storsimple-8000-deactivate-and-delete-device.md) |
| Saiba mais sobre a ativação pós-falha de dispositivo e de recuperação de desastre</br>Ativação pós-falha para um dispositivo físico</br>Ativação pós-falha para um dispositivo virtual</br>Recuperação de desastre de continuidade de negócio (BCDR) |[Ativação pós-falha e recuperação após desastre para o dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Lista as cópias de segurança para um volume</br>Selecione um conjunto de cópia de segurança</br>Eliminar um conjunto de cópia de segurança |[Gerir cópias de segurança](storsimple-8000-manage-backup-catalog.md) |
| Clonar um volume |[Clonar um volume](storsimple-8000-clone-volume-u2.md) |
| Restaurar um conjunto de cópia de segurança |[Restaurar um conjunto de cópia de segurança](storsimple-8000-restore-from-backup-set-u2.md) |
| Sobre as contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Eliminar uma conta do Storage</br>Rotação da chave de contas do storage |[Gerir contas de armazenamento](storsimple-8000-manage-storage-accounts.md) |
| Sobre os modelos de largura de banda</br>Adicionar um modelo de largura de banda</br>Editar um modelo de largura de banda</br>Eliminar um modelo de largura de banda</br>Utilizar um modelo de largura de banda predefinido</br>Criar um modelo de largura de banda all-day que inicia no tempo especificado |[Gerir modelos de largura de banda](storsimple-8000-manage-bandwidth-templates.md) |
| Sobre os registos de controlo de acesso</br>Criar um registo de controlo de acesso</br>Editar um registo de controlo de acesso</br>Elimine um registo de controlo de acesso |[Gerir registos de controlo de acesso](storsimple-8000-manage-acrs.md) |
| Ver detalhes da tarefa</br>Cancelar uma tarefa |[Gerir tarefas](storsimple-8000-manage-jobs-u2.md) |
| Receber notificações de alerta</br>Gerir alertas</br>Rever os alertas |[Ver e gerir alertas do StorSimple](storsimple-8000-manage-alerts.md) |
| Criar gráficos de monitorização |[Monitorizar o dispositivo StorSimple](storsimple-monitor-device.md) |
| Adicionar um contentor de volume</br>Modificar um contentor de volume</br>Eliminar um contentor de volume |[Gerir contentores de volumes](storsimple-8000-manage-volume-containers.md) |
| Adicionar um volume</br>Modificar um volume</br>Colocar offline um volume</br>Eliminar um volume</br>Monitorizar um volume |[Gerir volumes](storsimple-8000-manage-volumes-u2.md) |
| Modificar as definições do dispositivo</br>Modificar as definições de hora</br>Modificar as definições de DNS.md</br>Configurar as interfaces de rede |[Modificar configuração de dispositivo para o dispositivo StorSimple](storsimple-8000-modify-device-config.md) |
| Ver definições de proxy web |[Configurar o proxy web para o seu dispositivo](storsimple-8000-configure-web-proxy.md) |
| Modifique a palavra-passe de administrador do dispositivo</br>Modifique a palavra-passe do Snapshot Manager do StorSimple |[Alterar as palavras-passe do StorSimple](storsimple-8000-change-passwords.md) |
| Configurar a gestão remota |[Ligar remotamente ao dispositivo StorSimple](storsimple-8000-remote-connect.md) |
| Configurar definições de alerta |[Ver e gerir alertas do StorSimple](storsimple-8000-manage-alerts.md) |
| Configurar o CHAP para o dispositivo StorSimple |[Configurar o CHAP para o dispositivo StorSimple](storsimple-configure-chap.md) |
| Adicionar uma política de cópias de segurança</br>Adicionar ou modificar uma agenda</br>Eliminar uma política de cópia de segurança</br>Efetuar uma cópia de segurança manual</br>Criar uma política de cópia de segurança personalizada com vários volumes e agendas |[Gerir políticas de cópia de segurança](storsimple-8000-manage-backup-policies-u2.md) |
| Parar os controladores de dispositivo</br>Reinicie os controladores de dispositivo</br>Encerrar os controladores de dispositivo</br>Repor o seu dispositivo para as predefinições de fábrica</br>(Acima destinam-se apenas a dispositivos no local) |[Gerir o controlador de dispositivo do StorSimple](storsimple-8000-manage-device-controller.md) |
| Saiba mais sobre os componentes de hardware do StorSimple</br>Monitorizar o estado do hardware</br>(Acima destinam-se apenas a dispositivos no local) |[Componentes de hardware do monitor](storsimple-8000-monitor-hardware-status.md) |
| Criar um pacote de suporte |[Criar e gerir um pacote de suporte](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Instalar atualizações de software |[Atualizar o seu dispositivo](storsimple-update-device.md) |

## <a name="next-steps"></a>Passos seguintes

Se surgirem problemas com a operação diária do dispositivo StorSimple ou com qualquer um dos respetivos componentes de hardware, consulte:

* [Resolver problemas com a ferramenta de diagnóstico](storsimple-8000-diagnostics.md)
* [Utilizar monitorização LEDs indicador do StorSimple](storsimple-monitoring-indicators.md)

Se não é possível resolver os problemas e tem de criar um pedido de serviço, consulte [contactar o suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

