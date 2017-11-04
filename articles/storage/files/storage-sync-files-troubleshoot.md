---
title: "Resolver problemas de sincronização de ficheiros do Azure (pré-visualização) | Microsoft Docs"
description: "Resolver problemas comuns com a sincronização de ficheiros do Azure"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 6bc5f2da2b8628671037b9257db746e73cd3afad
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Resolver problemas de sincronização de ficheiros do Azure (pré-visualização)
O Azure File Sync (pré-visualização) permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Isto é feito ao transformar os Servidores do Windows numa cache rápida da partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

Este artigo foi concebido para ajudar a resolver problemas encontrados com a implementação de sincronização de ficheiros do Azure. A falhar, este guia ilustra como recolher registos importantes do sistema para ajudar a uma investigação mais aprofundada dos problemas. Se não vir a resposta à sua pergunta aqui, não hesitam em entrar-nos através dos canais seguintes (em constante crescendo ordem):

1. Na secção de comentários deste artigo.
2. [Fórum de armazenamento do Azure](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [Ficheiros do Azure UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Support da Microsoft: Para criar um novo incidente de suporte, navegue até à "ajuda + suporte" separador no portal do Azure e clique em "Novo suporte de pedido".

## <a name="agent-installation-and-server-registration"></a>Registo de servidor e a instalação do agente
<a id="agent-installation-failures"></a>**Como resolver problemas de falhas de instalação do agente**  
Se a instalação do agente de sincronização de ficheiros do Azure está a falhar, execute o seguinte comando numa linha de comandos elevada para ativar o registo durante a instalação do agente:

```
StorageSyncAgent.msi /l*v Installer.log
```

Depois da instalação falhar, reveja o installer.log para determinar a causa. 

> [!Note]  
> A instalação do agente falhará se optar por utilizar o Microsoft Update e o serviço Windows Update não está em execução.

<a id="server-registration-missing"></a>**Servidor não está listado em servidores registados no portal do Azure**  
Se um servidor não está listado em servidores registados para um serviço de sincronização de armazenamento, execute os seguintes passos:
1. Início de sessão para o servidor que pretende registar.
2. Abra o Explorador de ficheiros e navegue para o diretório de instalação do agente de sincronização de armazenamento (localização predefinida é `C:\Program Files\Azure\StorageSyncAgent`). 
3. Execute ServerRegistration.exe e siga o Assistente para registar o servidor com um serviço de sincronização de armazenamento.

<a id="server-already-registered"></a>**Registar o servidor apresenta a seguinte mensagem depois de instalar o agente de sincronização de ficheiros do Azure: "este servidor já está registado"**  
![Uma captura de ecrã da caixa de diálogo de registo do servidor com o erro "o servidor já está registado" mensagem](media/storage-sync-files-troubleshoot/server-registration-1.png)

Esta mensagem é apresentada se o servidor foi anteriormente registado com um serviço de sincronização de armazenamento. Para anular o registo do servidor com o serviço de sincronização atual do armazenamento e o registo com um novo serviço de sincronização de armazenamento, siga os passos para [anular o registo de um servidor com sincronização de ficheiros do Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Se o servidor não está listado em servidores registados no serviço de sincronização de armazenamento, execute os seguintes comandos do PowerShell no servidor que pretende anular o registo:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Se o servidor fizer parte de um cluster, é opcional `Reset-StorageSyncServer -CleanClusterRegistration` parâmetro removerá também o registo de cluster.

<a id="web-site-not-trusted"></a>**Ao registar um servidor de receber vários "web site não fidedignos" respostas, por que motivo?**  
Este erro ocorre porque o **segurança avançada do Internet Explorer** política for ativada durante o registo de servidor. Para mais informações sobre como desativar corretamente o **segurança avançada do Internet Explorer** política, consulte [preparar Windows Servers para utilização com sincronização de ficheiros do Azure](storage-sync-files-deployment-guide.md#prepare-windows-servers-for-use-with-azure-file-sync) e [como implementar o ficheiro do Azure Sincronização (pré-visualização)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Gestão de grupo de sincronização
<a id="cloud-endpoint-using-share"></a>**Falha de criação de ponto final da nuvem com o seguinte erro: "Partilha de ficheiros do Azure especificada já está a ser utilizado por um CloudEndpoint diferentes"**  
Este erro ocorre se a partilha de ficheiros do Azure já está a ser utilizado por outro ponto final da nuvem. 

Se estiver a receber este erro e a partilha de ficheiros do Azure não está a ser utilizada por um ponto final da nuvem, execute os seguintes passos abaixo para limpar os metadados de sincronização de ficheiros do Azure na partilha de ficheiros do Azure:

> [!Warning]  
> A eliminar os metadados de uma partilha de ficheiros do Azure que está a ser utilizada por um ponto final da nuvem fará com que as operações de sincronização de ficheiros do Azure para falhar. 

1. Navegue para a partilha de ficheiros do Azure no Portal do Azure.  
2. Clique com o botão direito do rato em partilha de ficheiros do Azure e selecione **editar os metadados**
3. Clique com o botão direito do rato em SyncService e selecione **eliminar**.

<a id="cloud-endpoint-authfailed"></a>**Falha de criação de ponto final da nuvem com o erro: AuthorizationFailed**  
Este problema ocorre se a sua conta de utilizador não tem direitos suficientes para criar um ponto final da nuvem. 

Para criar um ponto final da nuvem, a conta de utilizador tem de ter as seguintes permissões de Authorization Microsoft:  
* Leitura: Obter a definição de função
* Escrita: Criar ou atualizar a definição de função personalizada
* Leitura: Obter a atribuição de função
* Escrita: Criar a atribuição de função

As seguintes funções incorporadas tem as permissões adequadas do Microsoft Authorization:  
* Proprietário
* Administrador de Acesso de Utilizador

Para determinar se a sua função de utilizador da conta tem as permissões adequadas, efetue o seguinte:  
* Clique em **grupos de recursos** no portal do Azure
* Selecione o grupo de recursos onde está localizada a conta de armazenamento e clique em **(IAM) do controlo de acesso**
* Clique em de **função** (por exemplo, o proprietário, contribuinte) para a sua conta de utilizador.
* No **fornecedor de recursos** lista, selecione **Authorization Microsoft** 
* **Atribuição de função** deve ter **leitura** e **permissões de escrita**
* **Definição de função** deve ter **leitura** e **permissões de escrita**

<a id="cloud-endpoint-deleteinternalerror"></a>**Falha de eliminação de ponto final com o erro: MgmtInternalError**  
Este problema pode ocorrer se a conta de armazenamento ou de partilha de ficheiros do Azure foi eliminada antes de eliminar o ponto final da nuvem. Este problema será resolvido numa atualização futura e o ponto final da nuvem pode ser eliminado.

Para impedir que este problema ocorrer, elimine o ponto final da nuvem antes de eliminar a conta de armazenamento ou de partilha de ficheiros do Azure.

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Criar um ficheiro diretamente na minha partilha de ficheiros do Azure através de SMB ou através do portal. Quanto tempo até que o ficheiro está sincronizado com os servidores no grupo de sincronização?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**Como resolver problemas de sincronização não funcionar num servidor**  
Se a sincronização está a falhar num servidor, execute o seguinte:
- Certifique-se de que existe um ponto final do servidor no portal do Azure para o diretório que quer sincronizar a uma partilha de ficheiros do Azure:
    
    ![Captura de ecrã de um grupo de sincronização com uma nuvem e de ponto final do servidor no portal do Azure](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- Reveja os registos de eventos operacionais e de diagnóstico com o Visualizador de eventos, localizado em `Applications and Services\Microsoft\FileSync\Agent`.
- Confirme que o servidor tem conectividade internet.
- Certifique-se a sincronização de ficheiros do Azure service está a ser executado no servidor de abrir o snap-in MMC de serviços e certifique-se de que o agente de sincronização de armazenamento (FileSyncSvc) está em execução.

<a id="replica-not-ready"></a>**Falha de sincronização com o erro: 0x80c8300f - a réplica não está pronta para efetuar a operação necessária**  
Este erro é esperado se criar um ponto final da nuvem e utilize uma partilha de ficheiros do Azure que contém dados. Depois de concluída a deteção de alteração na partilha de ficheiros do Azure (pode demorar até 24 horas), a sincronização deve começar a funcionar corretamente.

<a id="broken-sync-files"></a>**Como resolver problemas de ficheiros individuais ao sincronizar a falhar**  
Se a sincronização falham ficheiros individuais, efetue o seguinte:
- Reveja os registos de eventos operacionais e diagnóstico em `Applications and Services\Microsoft\FileSync\Agent` no Visualizador de eventos
- Certifique-se de que existem não alças abertas no ficheiro
    - Nota: Sincronização de ficheiros do Azure periodicamente entrarão instantâneos VSS para sincronizar os ficheiros com alças abertas

## <a name="cloud-tiering"></a>Disposição em camadas na cloud 
<a id="files-fail-tiering"></a>**Como resolver problemas de ficheiros que não obedeçam a camada**  
Se ficheiros falharem a camada para ficheiros do Azure, execute os seguintes passos:

- Verifique se que existem os ficheiros na partilha de ficheiros do Azure
    - Nota: Um ficheiro tem ser sincronizado com uma partilha de ficheiros do Azure antes de pode em camadas
- Reveja os registos de eventos operacionais e de diagnóstico, localizados em `Applications and Services\Microsoft\FileSync\Agent` no Visualizador de eventos
- Confirme que o servidor tem conectividade internet 
- Certifique-se de que os controladores de filtro de sincronização de ficheiros do Azure (StorageSync.sys & StorageSyncGuard.sys) estão em execução
    - Abra uma linha de comandos elevada, execute `fltmc` e certifique-se de que os controladores de filtro do sistema do ficheiro StorageSync.sys e StorageSyncGuard.sys estão listados

<a id="files-fail-recall"></a>**Como resolver problemas de ficheiros que não obedeçam a possível resgatar os**  
Se os ficheiros falharem a possível resgatar os, execute os seguintes passos:
- Reveja os registos de eventos operacionais e de diagnóstico, localizados em `Applications and Services\Microsoft\FileSync\Agent` no Visualizador de eventos
- Certifique-se de que existe os ficheiros na partilha de ficheiros do Azure
- Confirme que o servidor tem conectividade internet 
- Certifique-se de que os controladores de filtro de sincronização de ficheiros do Azure (StorageSync.sys & StorageSyncGuard.sys) estão em execução
    - Abra uma linha de comandos elevada, execute `fltmc` e certifique-se de que os controladores de filtro do sistema do ficheiro StorageSync.sys e StorageSyncGuard.sys estão listados

<a id="files-unexpectedly-recalled"></a>**Como resolver problemas que está a ser inesperadamente resgatar os num servidor de ficheiros**  
Software antivírus, cópia de segurança e outras aplicações que ler grande número de ficheiros irão fazer com que recalls indesejáveis, a menos que respeitem o atributo offline e ignorar ao ler o conteúdo desses ficheiros. A ignorar os ficheiros offline para os produtos que suportam ajuda a evitar recalls indesejáveis quando executar operações, tais como análises de software antivírus ou as tarefas de cópia de segurança.

Consulte o seu fornecedor de software sobre como configurar a solução para ignorar a leitura de ficheiros offline.

Mais recalls indesejados poderão acontecer noutros cenários, como a navegação nos ficheiros no Explorador de ficheiros. Abrir uma pasta com camadas de nuvem ficheiros no Explorador de ficheiros no servidor poderá resultar em recalls indesejados, mais, por isso, se o software antivírus está ativada no servidor.

## <a name="general-troubleshooting"></a>Resolução de problemas genéricos
Se ocorrerem problemas com sincronização de ficheiros do Azure num servidor, comece por efetuar o seguinte:
- Reveja os registos de eventos de diagnóstico e operacional no Visualizador de eventos
    - Sincronizar, criação de camadas e problemas de devolução de chamada são registados nos registos de eventos de diagnóstico e operacionais em`Applications and Services\Microsoft\FileSync\Agent`
    - Problemas de gerir um servidor (por exemplo, as definições de configuração) são registados nos registos de eventos de diagnóstico e operacionais em`Applications and Services\Microsoft\FileSync\Management`
- Certifique-se de que o serviço de sincronização de ficheiros do Azure está em execução no servidor
    - Abra o snap-in MMC de serviços e certifique-se de que o agente de sincronização de armazenamento (FileSyncSvc) está em execução
- Certifique-se de que os controladores de filtro de sincronização de ficheiros do Azure (StorageSync.sys & StorageSyncGuard.sys) estão em execução
    - Abra uma linha de comandos elevada, execute fltmc e certifique-se de que os controladores de filtro do sistema do ficheiro StorageSync.sys e StorageSyncGuard.sys estão listados

Se o problema não for resolvido depois de efetuar os passos acima, execute a ferramenta de AFSDiag efetuando os seguintes passos:
1. Crie um diretório que será utilizado para guardar a saída de AFSDiag (por exemplo, c:\output).
2. Abra uma janela elevada do PowerShell e execute os seguintes comandos (prima enter após cada comando):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: use the path created in step 1
    ```

3. Para o nível de rastreio de modo do kernel de sincronização de ficheiros do Azure, introduza 1 (a menos que especificado para criar mais verbosos rastreios) e clique em enter.
4. Para o nível de rastreio de modo do utilizador de sincronização de ficheiros do Azure, introduza 1 (a menos que especificado para criar mais verbosos rastreios) e clique em enter.
5. Reproduza o problema e prima D quando terminar.
6. Um ficheiro. zip que contém os registos e ficheiros de rastreio será no diretório de saída especificado.

## <a name="see-also"></a>Consultar também
- [Perguntas mais frequentes sobre os ficheiros do Azure](storage-files-faq.md)
- [Resolução de problemas de ficheiros do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Resolução de problemas de ficheiros do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
