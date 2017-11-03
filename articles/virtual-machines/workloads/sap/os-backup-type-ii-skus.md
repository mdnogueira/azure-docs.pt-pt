---
title: "Cópia de segurança do sistema operativo e o restauro de SAP HANA no Azure (instâncias de grande) escreva II SKUs | Microsoft Docs"
description: "Efetuar cópia de segurança do Operatign sistema e restauro de SAP HANA no Azure (instâncias de grande) tipo II SKUs"
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 41349cd7fe3bf39b5b42c44ba47acf980d15ebe7
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Cópia de segurança do SO e restauro para o tipo II SKUs

Este documento descreve os passos para efetuar uma cópia de segurança do sistema operativo e o restauro para o **tipo II SKUs** das instâncias grande HANA. 

>[!NOTE]
>Os scripts de cópia de segurança de SO utiliza o software de ReaR, que é previamente instalado no servidor.  

Após o aprovisionamento estiver concluído, a equipa de gestão do serviço Microsoft, por predefinição, o servidor está configurado com base numa agenda duas cópias de segurança para criar cópias de segurança completa do sistema operativo. Pode verificar o agendamento da tarefa de cópia de segurança utilizando o seguinte comando:
```
#crontab –l
```
Pode alterar a agenda de cópia de segurança qualquer altura utilizando o seguinte comando:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Como efetuar uma cópia de segurança manual?

A cópia de segurança do sistema operativo está agendada utilizando um **cron tarefa** já. No entanto, pode efetuar a cópia de segurança do sistema operativo manualmente, bem como. Para efetuar uma cópia de segurança manual, execute o seguinte comando:

```
#rear -v mkbackup
```
Mostrar de ecrã seguinte mostra a cópia de segurança manual de exemplo:

![Como](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Como restaurar uma cópia de segurança?

Pode restaurar uma cópia de segurança completa ou de um ficheiro individual da cópia de segurança. Para restaurar, utilize o seguinte comando:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Após o restauro, o ficheiro de recuperação no diretório de trabalho atual.

O comando seguinte mostra o restauro de um ficheiro *etc/fstabfrom* o ficheiro de cópia de segurança *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Tem de copiar o ficheiro para a localização pretendida, após o restauro da cópia de segurança.

A seguinte captura de ecrã mostra o restauro de uma cópia de segurança completa:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Como instalar a ferramenta de ReaR e alterar a configuração? 

Os pacotes de Relax-e-recuperar (ReaR) estão **pré-instalado** no **tipo II SKUs** de instâncias de grande HANA e nenhuma ação necessária da. Diretamente pode começar a utilizar o ReaR a cópia de segurança do sistema operativo.
No entanto, as circunstâncias em que tem de instalar os pacotes na sua própria, pode seguir os passos para instalar e configurar a ferramenta de ReaR listados.

Para instalar o **ReaR** pacotes de cópia de segurança, utilize os seguintes comandos:

Para **SLES** sistema operativo, utilize o seguinte comando:
```
#zypper install <rear rpm package>
```
Para **RHEL** sistema operativo, utilize o seguinte comando: 
```
#yum install rear -y
```
Para configurar a ferramenta de ReaR, tem de atualizar parâmetros **OUTPUT_URL** e **BACKUP_URL** no *ficheiro /etc/rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

A seguinte captura de ecrã mostra o restauro de uma cópia de segurança completa: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
