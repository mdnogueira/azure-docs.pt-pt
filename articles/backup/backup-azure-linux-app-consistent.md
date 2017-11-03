---
title: "Cópia de segurança do Azure: consistentes com aplicações as cópias de segurança de VMs do Linux | Microsoft Docs"
description: "Utilize scripts para garantir cópias de segurança consistentes com aplicações para o Azure, para as máquinas virtuais do Linux. Os scripts são aplicadas apenas a VMs com Linux numa implementação do Resource Manager; os scripts não aplicável a implementações de Gestor de serviço ou VMs do Windows. Este artigo leva-o através dos passos para configurar os scripts, incluindo a resolução de problemas."
services: backup
documentationcenter: dev-center-name
author: anuragmehrotra
manager: shivamg
keywords: "cópia de segurança consistentes da aplicação; cópia de segurança de VM do Azure com consistência de aplicações; Cópia de segurança de VM com Linux; Cópia de segurança do Azure"
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 4/12/2017
ms.author: anuragm;markgal
ms.openlocfilehash: 378c65bec8fd1f880ed459e76f5e4b5d85e49d2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Cópia de segurança consistentes com aplicações das VMs do Linux do Azure (pré-visualização)

Este talks artigo sobre o Linux do script pré-cópia e executar o script pós-cópia framework e como pode ser utilizado para fazer cópias de segurança consistentes com aplicações das VMs do Linux do Azure.

> [!Note]
> A estrutura de pré- script de e script pós-implementação só é suportada para máquinas virtuais do Linux implementadas no Azure Resource Manager. Scripts de consistência de aplicações não são suportados para máquinas de virtuais implementadas no Service Manager ou máquinas virtuais do Windows.
>

## <a name="how-the-framework-works"></a>Como funciona o framework

A estrutura fornece uma opção para executar pré-scripts de personalizados e scripts pós-cópia enquanto estiver a ser instantâneos VM. Pré-scripts são executados imediatamente antes de tirar o instantâneo da VM e scripts pós-implementação são executadas imediatamente após a tirar o instantâneo VM. Isto dá-lhe a flexibilidade de controlar a sua aplicação e o ambiente enquanto estiver a ser instantâneos VM.

Neste cenário, é importante certificar-se a cópia de segurança consistentes com aplicações. O pré-script de pode invocar as APIs de aplicação nativa ao Silenciar os IOs e descarregar conteúdo dentro da memória para o disco. Isto assegura que o instantâneo é consistente com aplicações (ou seja, que inclui a aplicação de cópia de segurança quando a VM é iniciada de pós-restauro). Script de pós-implementação pode ser utilizado para thaw IOs. Fazê-lo ao utilizando APIs de aplicação nativa para que a aplicação pode retomar o instantâneo de post VM operações normais.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Passos para configurar o pré- script de e scripts pós-implementação

1. Inicie sessão como utilizador raiz para a VM com Linux que pretende criar cópias de segurança.

2. Transferir **VMSnapshotScriptPluginConfig.json** de [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)e, em seguida, copie-o para o **etc/azure** pasta em todas as VMs que vai fazer cópias de segurança. Criar o **etc/azure** diretório se não existir já.

3. Copie o pré- script de e scripts pós-implementação para a sua aplicação em todas as VMs que pretende criar cópias de segurança. Pode copiar os scripts para qualquer local na VM. Não se esqueça de atualizar o caminho completo dos ficheiros de script no **VMSnapshotScriptPluginConfig.json** ficheiro.

4. Certifique-se as seguintes permissões para estes ficheiros:

   - **VMSnapshotScriptPluginConfig.json**: permissão "600." Por exemplo, apenas os utilizadores "raiz" devem ter permissões "leitura" e "escrita" para este ficheiro e nenhum utilizador deve ter permissões de "executar".

   - **Ficheiro de pré-script de**: permissão "700."  Por exemplo, o utilizador de "raiz" só deve ter "ler", "escrita" e "executar" permissões para este ficheiro.
  
   - **Script de pós-implementação** permissão "700." Por exemplo, o utilizador de "raiz" só deve ter "ler", "escrita" e "executar" permissões para este ficheiro.

   > [!Important]
   > A estrutura fornece aos utilizadores uma grande quantidade de energia. É importante que é seguro e esse utilizador de "raiz" apenas tem acesso aos ficheiros críticos de JSON e o script.
   > Se não são cumpridos os requisitos anteriores, o script não é executado. Isto resulta numa cópia de segurança consistentes do sistema/falhas de ficheiros.
   >

5. Configurar **VMSnapshotScriptPluginConfig.json** conforme descrito aqui:
    - **pluginName**: deixe este campo como está ou os scripts poderão não funcionar conforme esperado.

    - **preScriptLocation**: forneça o caminho completo do pré-script na VM que vai fazer cópias de segurança.

    - **postScriptLocation**: forneça o caminho completo do script de pós-implementação na VM que vai fazer cópias de segurança.

    - **preScriptParams**: forneça os parâmetros opcionais que precisam de ser transmitidas ao pré script de. Todos os parâmetros devem ser aspas e devem ser separados por vírgulas se existirem vários parâmetros.

    - **postScriptParams**: forneça os parâmetros opcionais que precisam de ser transmitidas ao script de pós-implementação. Todos os parâmetros devem ser aspas e devem ser separados por vírgulas se existirem vários parâmetros.

    - **preScriptNoOfRetries**: definir o número de vezes que o pré-script de deve ser repetido se não houver qualquer erro antes de terminar. Experimente de apenas um zero significa e sem repetição se ocorrer uma falha.

    - **postScriptNoOfRetries**: definir o número de vezes que o script de pós-implementação deve ser repetido se não houver qualquer erro antes de terminar. Experimente de apenas um zero significa e sem repetição se ocorrer uma falha.
    
    - **timeoutInSeconds**: especificar os tempos limite individuais para o pré- script de e o script de pós-implementação.

    - **continueBackupOnFailure**: defina este valor como **verdadeiro** se pretende que o Backup do Azure para reverter para uma cópia de segurança ficheiros do consistente/falhas consistentes do sistema se o pré- script de ou pós-script falha. Definir este como **falso** falhar a cópia de segurança em caso de falha de script (exceto se tiver de VM de disco único que retrocede para cópia de segurança consistentes com falhas independentemente desta definição).

    - **fsFreezeEnabled**: Especifique se Linux fsfreeze deve ser chamado enquanto estiver a criar o instantâneo de VM para garantir a consistência do sistema de ficheiros. É recomendável manter esta definição definida como **verdadeiro** , a menos que a aplicação tem uma dependência na desativação fsfreeze.

6. A estrutura de script está agora configurada. Se a cópia de segurança da VM já está configurada, a cópia de segurança seguinte invoca os scripts e aciona uma cópia de segurança consistentes com aplicações. Se a cópia de segurança VM não está configurada, configure-a utilizando [cópia de segurança do Azure que as máquinas virtuais cofres dos serviços de recuperação.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Resolução de problemas

Certifique-se de adicionar o registo adequado ao escrever o pré- script de e scripts pós-implementação e reveja os registos de script para corrigir quaisquer problemas de script. Se ainda tiver problemas a execução de scripts, consulte a tabela seguinte para obter mais informações.

| Erro | Mensagem de erro | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Pré-ScriptExecutionFailed |O pré-script de devolveu um erro, pelo que a cópia de segurança poderá não ser consistentes com aplicações.   | Consulte os registos de falha para o script corrigir o problema.|  
|   Post ScriptExecutionFailed |    O script de pós-implementação devolveu um erro que pode afetar o estado da aplicação. |    Consulte os registos de falha para o script corrigir o problema e verifique o estado da aplicação. |
| Pré-ScriptNotFound |  O pré-script de não foi encontrado na localização especificada no **VMSnapshotScriptPluginConfig.json** ficheiro de configuração. |   Certifique-se que ao script de disponibilizar está presente no caminho especificado no ficheiro de configuração para garantir que a cópia de segurança consistentes com aplicações.|
| Post ScriptNotFound | O pós-script de não foi encontrado na localização especificada no **VMSnapshotScriptPluginConfig.json** ficheiro de configuração. |   Certifique-se que posterior ao script de disponibilizar está presente no caminho especificado no ficheiro de configuração para garantir que a cópia de segurança consistentes com aplicações.|
| IncorrectPluginhostFile | O **Pluginhost** ficheiro, que é fornecido com a extensão de VmSnapshotLinux, está danificado, pelo que não é possível executar pré- scripts de e scripts pós-implementação e a cópia de segurança, não será consistentes com aplicações. | Desinstalar o **VmSnapshotLinux** extensão e serão automaticamente reinstalado com a cópia de segurança seguinte para corrigir o problema. |
| IncorrectJSONConfigFile | O **VMSnapshotScriptPluginConfig.json** ficheiro incorreto, por isso, a pré-script e não é possível executar o script de pós-implementação e a cópia de segurança, não será consistentes com aplicações. | Transferir a cópia de [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) e configurá-la novamente. |
| Script de InsufficientPermissionforPre | Para executar scripts, utilizador de "raiz" deve ser o proprietário do ficheiro e o ficheiro deve ter permissões "700" (ou seja, deve ter apenas "proprietário" "ler", "escrita" e "executar" permissões). | Certifique-se o utilizador de "raiz" é o "proprietário" do ficheiro de script e que apenas "proprietário" tem "ler", "escrita" e "executar" permissões. |
| Script de InsufficientPermissionforPost | Para executar scripts, o utilizador raiz deve ser o proprietário do ficheiro e o ficheiro deve ter permissões "700" (ou seja, deve ter apenas "proprietário" "ler", "escrita" e "executar" permissões). | Certifique-se o utilizador de "raiz" é o "proprietário" do ficheiro de script e que apenas "proprietário" tem "ler", "escrita" e "executar" permissões. |
| Pré-ScriptTimeout | A execução do pré-script cópia de segurança consistentes com aplicações excedido. | Verifique o script e aumentar o tempo limite no **VMSnapshotScriptPluginConfig.json** ficheiro que está localizado em **etc/azure**. |
| Post ScriptTimeout | A execução do pós-script de cópia de segurança consistentes com aplicações excedeu o tempo limite. | Verifique o script e aumentar o tempo limite no **VMSnapshotScriptPluginConfig.json** ficheiro que está localizado em **etc/azure**. |

## <a name="next-steps"></a>Passos seguintes
[Configurar cópia de segurança VM para um cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
