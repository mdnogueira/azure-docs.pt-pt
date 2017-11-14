---
title: "Notas de versão de atualização 5 do StorSimple 8000 série | Microsoft Docs"
description: "Descreve as novas funcionalidades, problemas e soluções para atualização 5 do StorSimple 8000 série."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 672757e82bcf645b705f46a9975e09c9dc5eef92
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Notas de versão de atualização 5 do StorSimple 8000 série

## <a name="overview"></a>Descrição geral

As notas de versão seguintes descrevem as novas funcionalidades e identificam os problemas abertos críticos para a atualização 5 do StorSimple 8000 série. Também contêm uma lista das atualizações de software StorSimple incluído nesta versão.

Atualização 5 pode ser aplicada a todos os dispositivos StorSimple executar a atualização 0.1 a atualização 4. A versão de dispositivo associada a atualização 5 é 6.3.9600.17845.

Reveja as informações contidas nas notas de versão antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * Atualização 5 é uma atualização obrigatória e tem de ser instalada imediatamente. Para obter mais informações, consulte como [aplicar Update 5](storsimple-8000-install-update-5.md).
> * Atualização 5 tem o software de dispositivos, de firmware de disco, segurança de SO e outras atualizações do SO. Demora cerca de 4 horas para instalar esta atualização. Atualização de firmware do disco é uma atualização acontece e resulta num tempo de inatividade para o seu dispositivo. Recomendamos que aplique a atualização 5 para manter o seu dispositivo atualizados.
> * Para os novos lançamentos, poderá não ver atualizações imediatamente porque fazemos de uma implementação faseada de atualizações. Aguarde alguns dias e, em seguida, análise de atualizações novamente como estas atualizações deixará de estar disponível em breve.

## <a name="whats-new-in-update-5"></a>Novidades na atualização 5

Foram efetuadas as seguintes melhorias de chaves e correções de erros na atualização 5.

* **Utilização do Azure Active Directory (AAD) para autenticar com o serviço do Gestor de dispositivos do StorSimple** – da atualização 5 em diante, Azure Active Directory é utilizado para autenticar com o serviço do Gestor de dispositivos do StorSimple. O mecanismo de autenticação antigo será preterido Dezembro de 2017. Todos os utilizadores têm de incluir novos URLs de autenticação nas regras de firewall. Para obter mais informações, aceda a [autenticação os URLs listados nos requisitos de rede para o dispositivo StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Se o URL de autenticação não está incluído nas regras da firewall, os utilizadores verão um alerta crítico que o dispositivo StorSimple não foi possível autenticar com o serviço. Se os utilizadores veem este alerta, tem de incluir o novo URL de autenticação. Para obter mais informações, aceda a [StorSimple redes alertas](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nova versão do Snapshot Manager do StorSimple** -uma nova versão do Snapshot Manager do StorSimple é libertada com Update 5 e é compatível com todos os dispositivos StorSimple que estejam a executar a atualização 4 ou posterior. Recomendamos que Atualize para esta versão. A versão anterior do Snapshot Manager do StorSimple é utilizado para dispositivos StorSimple que executam a atualização 3 ou anterior. [Transfira a versão adequada do Snapshot Manager do StorSimple](https://www.microsoft.com/en-us/download/details.aspx?id=44220) e consulte [implementar Snapshot Manager do StorSimple](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problemas fixos em Update 5

A tabela seguinte fornece um resumo dos problemas que foram corrigidos na atualização 5.

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Comunicação remota do Windows PowerShell |Na versão anterior, um utilizador deverá receber um erro ao tentar estabelecer uma ligação remota para a aplicação de nuvem do StorSimple através do Windows PowerShell. Este problema foi causada de raiz e fixo nesta versão. |Não |Sim |
| 2 |Modelos de largura de banda |Na versão anterior, Ocorreu um problema com os modelos de largura de banda que resultaram numa menor largura de banda de que o dispositivo foi configurado para. Este problema é resolvido nesta versão. |Sim |Sim |
| 3 |Ativação pós-falha |Na versão anterior, quando um dispositivo com um grande número de volumes foi a ativação pós-falha para outro dispositivo com a Update 4, o processo falhará ao tentar aplicar os registos de controlo de acesso. Este problema ser corrigido nesta versão. |Sim |Sim |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Problemas conhecidos no 5 de atualização de versões anteriores

Existem não existem problemas conhecidos novo na atualização 5. Para obter uma lista dos problemas transportados através da atualização 5 a partir de versões anteriores, aceda a [notas de versão da atualização 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Série-attached SCSI (SAS) controlador e atualizações de firmware na atualização 5

Esta versão tem controlador SAS e atualizações de controladores e firmware LSI. Para obter mais informações sobre como instalar estas atualizações, consulte [instalar a atualização 5](storsimple-8000-install-update-5.md) no dispositivo StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Atualizações de aplicação de nuvem do StorSimple no Update 5

Não é possível aplicar esta atualização para a aplicação de nuvem do StorSimple (também conhecido como o dispositivo virtual). Novo aparelhos de nuvem tem de ser criada utilizando a imagem de atualização 5. Para informações sobre como criar uma aplicação de nuvem do StorSimple, aceda a [implementar e gerir um dispositivo de nuvem do StorSimple](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Passo seguinte

Saiba como [instalar a atualização 5](storsimple-8000-install-update-5.md) no dispositivo StorSimple.

