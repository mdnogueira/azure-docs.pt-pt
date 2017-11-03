---
title: "Administração de Snapshot Manager do StorSimple | Microsoft Docs"
description: "Fornece uma descrição geral e ligações para mais informações sobre tarefas de administração de solução do Snapshot Manager do StorSimple e fluxos de trabalho."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: v-sharos
ms.openlocfilehash: a99b3d7336c3dc1a1f249915d6971a49f4b69be6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Utilize o Gestor de instantâneo StorSimple para administrar a sua solução StorSimple

## <a name="overview"></a>Descrição geral
Snapshot Manager do StorSimple é um snap-in Consola de gestão da Microsoft (MMC) que simplifica a gestão de cópia de segurança num ambiente do Microsoft Azure StorSimple e proteção de dados. Com o Snapshot Manager do StorSimple, pode gerir dados do Microsoft Azure StorSimple no Centro de dados e na nuvem como uma solução de armazenamento integrada único, assim simplificar processos de cópia de segurança e reduzindo os custos.

A consola de gestão central Snapshot Manager do StorSimple permite-lhe criar cópias de segurança consistentes e, no momento da local e da nuvem. Por exemplo, pode utilizar a consola para:

* Configurar, fazer cópias de segurança e eliminar os volumes.
* Configurar grupos de volume para se certificar de que a cópia de segurança de dados é consistente com aplicações.
* Gerir políticas de cópia de segurança para que os dados de cópias de segurança com base numa agenda predeterminada.
* Crie cópias independentes de dados, que podem ser armazenados na nuvem e utilizados para recuperação após desastre.

Este artigo fornece ligações para tutoriais que descrevem o Snapshot Manager do StorSimple e como utilizá-la para concluir as tarefas de administração do sistema e fluxos de trabalho.

* Para obter mais informações sobre a arquitetura e componentes Snapshot Manager do StorSimple, consulte [Novidades Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md) 
* Para transferir o Snapshot Manager do StorSimple, visite [a página de transferência do Snapshot Manager do StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).
* Para obter procedimentos de implementação do Snapshot Manager do StorSimple, aceda a [implementar Snapshot Manager do StorSimple](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Não é possível utilizar o Snapshot Manager do StorSimple para gerir o Microsoft Azure StorSimple Virtual matrizes (também conhecido como StorSimple no local dispositivos virtuais).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>Tarefas do Snapshot Manager do StorSimple e fluxos de trabalho
Pode utilizar o Snapshot Manager do StorSimple para monitorizar e gerir tarefas de cópia de segurança atuais, agendadas e concluídas. Além disso, o Snapshot Manager do StorSimple fornece um catálogo de até 64 cópias de segurança foi concluídas. Pode utilizar o catálogo para localizar e restaurar volumes ou ficheiros individuais. 

| SE PRETENDER EFETUAR ESTE PROCEDIMENTO... | UTILIZE ESTE TUTORIAL... |
|:--- |:--- |
| Saiba mais sobre o Snapshot Manager do StorSimple |[O que é o Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md) |
| Instalar o Snapshot Manager do StorSimple<br>Reinstalar o Snapshot Manager do StorSimple<br>Remova o Snapshot Manager do StorSimple |[Implementar o Snapshot Manager do StorSimple](storsimple-snapshot-manager-deployment.md) |
| Snapshot Manager do StorSimple Utilize menus e funcionalidades:<ul><li>Barra de menus</li><li>Barra de ferramentas</li><li>Painel de âmbito</li><li>Painel de resultados</li><li>Painel de ações</li><li>Navegação do teclado e atalhos</li></ul> |[Interface de utilizador do Snapshot Manager do StorSimple](storsimple-use-snapshot-manager.md) |
| Utilize as funcionalidades MMC comuns incluídas no Snapshot Manager do StorSimple:<ul><li>Vista</li><li>Nova janela a partir daqui</li><li>Atualizar</li><li>Lista de exportação</li><li>Ajuda</li></ul> |[Utilizar as ações do menu MMC no Snapshot Manager do StorSimple](storsimple-snapshot-manager-mmc-menu.md) |
| Adicionar ou substituir um dispositivo<br>Ligar um dispositivo<br>Certifique-se grupos de volume importados<br>Atualizar dispositivos ligados<br>Autenticar um dispositivo<br>Ver detalhes do dispositivo<br>Eliminar uma configuração de dispositivo<br>Alterar uma palavra-passe do dispositivo<br>Substituir um dispositivo com falhas<br> |[Utilize o Snapshot Manager do StorSimple para ligar e gerir dispositivos StorSimple](storsimple-snapshot-manager-manage-devices.md) |
| Montar os volumes<br>Ver informações sobre volumes<br>Eliminar um volume<br>Reanalisar volumes<br>Configurar e efetuar cópias de segurança de um volume básico<br>Configurar e a cópia de segurança de um volume espelhado dinâmico |[Utilize o Snapshot Manager do StorSimple para ver e gerir volumes](storsimple-snapshot-manager-manage-volumes.md) |
| Grupos de volume de vista<br>Criar um grupo de volume<br>Cópia de segurança de um grupo de volume<br>Editar um grupo de volume<br>Eliminar um grupo de volume |[Utilize o Snapshot Manager do StorSimple para criar e gerir grupos de volume](storsimple-snapshot-manager-manage-volume-groups.md) |
| Criar uma política de cópia de segurança <br>Editar uma política de cópia de segurança<br>Eliminar uma política de cópia de segurança |[Utilize o Snapshot Manager do StorSimple para criar e gerir políticas de cópia de segurança](storsimple-snapshot-manager-manage-backup-policies.md) |
| Ver e gerir tarefas de cópia de segurança agendadas<br>Ver e gerir tarefas de cópia de segurança recentes<br>Ver e gerir tarefas de cópia de segurança atualmente em execução |[Utilize o Snapshot Manager do StorSimple para ver e gerir tarefas de cópia de segurança](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Restaurar um volume<br>Clonar um volume ou o grupo de volume<br>Eliminar uma cópia de segurança<br>Recuperar um ficheiro<br>Restaurar a base de dados do Snapshot Manager do StorSimple |[Utilize o Gestor de instantâneo StorSimple para gerir o catálogo de cópias de segurança](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Passos seguintes
[Transferir o Snapshot Manager do StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).

