---
title: "Instalação automática do servidor de cópia de segurança do Azure v2 | Microsoft Docs"
description: "Utilize um script do PowerShell para instalar automaticamente o servidor de cópia de segurança do Azure v2. Este tipo de instalação também é denominado uma instalação autónoma."
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/30/2017
ms.author: markgal;masaran
ms.openlocfilehash: 91778a67f9ef523aa87b7918197e0d0ded0f5702
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="run-an-unattended-installation-of-azure-backup-server-v2"></a>Execute uma instalação autónoma do servidor de cópia de segurança do Azure v2

Saiba como executar uma instalação autónoma do servidor de cópia de segurança do Azure v2. 

Estes passos não se aplicam se estiver a instalar o servidor de cópia de segurança do Azure v1.

## <a name="install-backup-server-v2"></a>Instalar o servidor de cópia de segurança v2

1. No servidor que aloja o servidor de cópia de segurança do Azure v2, crie um ficheiro de texto. (Pode criar o ficheiro no bloco de notas ou noutro editor de texto.) Guarde o ficheiro como MABSSetup.ini. 

2. Cole o seguinte código no ficheiro MABSSetup.ini. Substitua o texto dentro dos parênteses Retos (\< \>) com valores do seu ambiente. O texto seguinte é um exemplo:

  ```
  [OPTIONS]
  UserName=administrator
  CompanyName=<Microsoft Corporation>
  SQLMachineName=localhost
  SQLInstanceName=<SQL instance name>
  SQLMachineUserName=administrator
  SQLMachinePassword=<admin password>
  SQLMachineDomainName=<machine domain>
  ReportingMachineName=localhost
  ReportingInstanceName=<reporting instance name>
  SqlAccountPassword=<admin password>
  ReportingMachineUserName=<username>
  ReportingMachinePassword=<reporting admin password>
  ReportingMachineDomainName=<domain>
  VaultCredentialFilePath=<vault credential full path and complete name>
  SecurityPassphrase=<passphrase>
  PassphraseSaveLocation=<passphrase save location>
  UseExistingSQL=<1/0 use or do not use existing SQL>
  ```

3. Guarde o ficheiro. Em seguida, na linha de comandos elevada no servidor de instalação, introduza este comando:

  ```
  start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
  ```

Pode utilizar estes sinalizadores para a instalação:</br>
**/f**: caminho do ficheiro. ini</br>
**/l**: caminho do registo</br>
**i**: caminho de instalação</br>
**/x**: desinstalar caminho</br>

## <a name="next-steps"></a>Passos seguintes
Depois de instalar o servidor de cópia de segurança, saiba como preparar o servidor ou começar a proteger uma carga de trabalho.

- [Preparar as cargas de trabalho do servidor de cópia de segurança](backup-azure-microsoft-azure-backup.md)
- [Utilizar o servidor de cópia de segurança para fazer cópias de segurança de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Utilizar o servidor de cópia de segurança para criar cópias de segurança do SQL Server](backup-azure-sql-mabs.md)
- [Adicionar armazenamento de cópia de segurança moderno para o servidor de cópia de segurança](backup-mabs-add-storage.md)
