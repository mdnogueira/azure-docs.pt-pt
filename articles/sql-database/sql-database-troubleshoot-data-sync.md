---
title: "Resolver problemas de sincronização de dados SQL do Azure (pré-visualização) | Microsoft Docs"
description: "Saiba como resolver problemas comuns com a sincronização de dados de SQL do Azure (pré-visualização)."
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 50cabbaa584671e52c1ea7efbd2ad990b8438272
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="troubleshoot-issues-with-sql-data-sync-preview"></a>Resolver problemas com a sincronização de dados do SQL Server (pré-visualização)

Este artigo descreve como resolver problemas conhecidos com a sincronização de dados de SQL do Azure (pré-visualização). Se existir uma resolução para um problema, é fornecido aqui.

Para obter uma descrição geral da sincronização de dados do SQL Server (pré-visualização), consulte [sincronizar os dados em várias bases de dados na nuvem e no local com sincronização de dados de SQL do Azure (pré-visualização)](sql-database-sync-data.md).

## <a name="sync-issues"></a>Problemas de sincronização

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a>Falha de sincronização no portal de IU para bases de dados no local que estão associados com o agente do cliente

#### <a name="description-and-symptoms"></a>A descrição e sintomas

Sincronização falha em sincronização de dados do SQL Server (pré-visualização) IU do portal para bases de dados no local que estão associados com o agente. No computador local que está a executar o agente, consulte os erros de System.IO.IOException no registo de eventos. Os erros indicar que o disco tem espaço suficiente.

#### <a name="resolution"></a>Resolução

Crie mais espaço na unidade em que está localizado o diretório % TEMP %.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>O meu grupo de sincronização está bloqueado no estado de processamento

#### <a name="description-and-symptoms"></a>A descrição e sintomas

Um grupo de sincronização na sincronização de dados do SQL Server (pré-visualização) está a ser o estado de processamento durante muito tempo. Não responder a **parar** comando e os registos de mostram não novas entradas.

#### <a name="cause"></a>Causa

Qualquer uma das seguintes condições poderá resultar num grupo de sincronização que está a ser bloqueado no estado de processamento de:

-   **O agente do cliente está offline**. Certifique-se de que o agente do cliente está online e, em seguida, tente novamente.

-   **O agente do cliente está em falta ou desinstalada**. Se o agente de cliente desinstalada ou de outra forma de em falta:

    1. Remova o ficheiro XML de agente a pasta de instalação de sincronização de dados do SQL Server (pré-visualização), se o ficheiro existe.
    2. Instale o agente num computador local (pode ser o mesmo ou outro computador). Em seguida, submeta a chave do agente que é gerada no portal para o agente que está a ser mostrado como offline.

-   **O serviço de sincronização de dados do SQL Server está parado**.

    1. No **iniciar** menu, procure **serviços**.
    2. Nos resultados da pesquisa, selecione **serviços**.
    3. Localizar o **sincronização de dados do SQL Server (pré-visualização)** serviço.
    4. Se o estado do serviço **parado**, faça duplo clique o nome do serviço e, em seguida, selecione **iniciar**.

#### <a name="resolution"></a>Resolução

Se as informações não mover o grupo de sincronização fora do Estado de processamento, Support da Microsoft pode repor o estado do seu grupo de sincronização. Ter o estado do grupo de sincronização repor, além de [fórum da SQL Database do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), criar um pedido post. No post, incluem o ID de subscrição e o ID do grupo de sincronização para o grupo que tem de ser reposto. Um engenheiro de Microsoft Support responderá ao seu post e irá informá-lo quando o estado foi reposto.

### <a name="i-see-erroneous-data-in-my-tables"></a>Posso ver dados errados nas minhas tabelas

#### <a name="description-and-symptoms"></a>A descrição e sintomas

No caso de tabelas que têm o mesmo nome mas que são de esquemas de base de dados diferente uma sincronização, consulte errados dados nas tabelas após a sincronização.

#### <a name="cause"></a>Causa

A sincronização de dados do SQL Server (pré-visualização) processo de aprovisionamento utiliza as mesmo tabelas de controlo para tabelas que têm o mesmo nome mas que estão em esquemas diferentes. Por este motivo, as alterações de ambas as tabelas são refletidas na mesma tabela de controlo. Isto faz com que as alterações de dados errada durante a sincronização.

#### <a name="resolution"></a>Resolução

Certifique-se de que os nomes das tabelas envolvidos numa sincronização são diferentes, mesmo se as tabelas pertencem a diferentes esquemas numa base de dados.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a>Posso ver dados de chaves primários inconsistentes após uma sincronização com êxito

#### <a name="description-and-symptoms"></a>A descrição e sintomas

Uma sincronização é reportada como concluída com êxito e o registo mostra sem linhas ignoradas ou falhadas, mas observar a que dados de chaves principal são consistente entre as bases de dados no grupo de sincronização.

#### <a name="cause"></a>Causa

Este resultado é propositado. Alterações em qualquer resultado de coluna de chave primária em dados inconsistentes nas linhas em que a chave primária foi alterada.

#### <a name="resolution"></a>Resolução

Para evitar este problema, certifique-se de que não existem dados na coluna chave primária são alterados.

Para corrigir este problema após tiver ocorrido, elimine a linha que tenha dados inconsistentes de todos os pontos finais no grupo de sincronização. Em seguida, reinsert a linha.

### <a name="i-see-a-significant-degradation-in-performance"></a>Vejo uma degradação significativa do desempenho

#### <a name="description-and-symptoms"></a>A descrição e sintomas

O desempenho degrada significativamente, possivelmente para o ponto em que ainda não é possível abrir a IU de sincronização de dados.

#### <a name="cause"></a>Causa

A causa mais provável é um ciclo de sincronização. Um ciclo de sincronização ocorre quando um grupo de sincronização ao sincronizar A aciona um grupo de sincronização ao sincronizar B, que, em seguida, aciona uma grupo de sincronização ao sincronizar A. A situação real pode estar mais complexa, que poderão envolver mais de dois grupos de sincronização no ciclo. O problema é que há um acionar circular de sincronização que é causado por grupos de sincronização sobreposição entre si.

#### <a name="resolution"></a>Resolução

A correção melhor é prevenção. Certifique-se de que não tem referências circulares nos seus grupos de sincronização. Não é possível sincronizar a qualquer linha que está sincronizada por um grupo de sincronização por outro grupo de sincronização.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a>Posso ver esta mensagem: "não é possível inserir o valor NULL na coluna \<coluna\>. Coluna não permite nulls." O que significa e como pode corrigir? 
Esta mensagem de erro indica que tenha ocorrido um dos dois seguintes problemas:
-  Uma tabela não tem uma chave primária. Para corrigir este problema, adicione uma chave primária para todas as tabelas que estiver a sincronizar.
-  Não há uma cláusula WHERE da instrução CREATE INDEX. Sincronização de dados (pré-visualização) não processa esta condição. Para corrigir este problema, remova a cláusula WHERE ou manualmente, efetue as alterações a todas as bases de dados. 
 
### <a name="how-does-data-sync-preview-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>Como a sincronização de dados (pré-visualização) processar referências circulares? Ou seja, quando os mesmos dados são sincronizados em vários grupos de sincronização e mantém a alteração assim?
Sincronização de dados (pré-visualização) não processa as referências circulares. Lembre-se de que os evitar. 

## <a name="client-agent-issues"></a>Problemas de agente do cliente

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a>O agente do cliente instalar, desinstalar ou repare a falha

#### <a name="cause"></a>Causa

Muitos cenários poderão causar esta falha. Para determinar a causa desta falha específica, consulte os registos.

#### <a name="resolution"></a>Resolução

Para determinar a causa da falha específica, gerar e consulte os registos do Windows Installer. Pode ativar o registo de uma linha de comandos. Por exemplo, se o ficheiro transferido do AgentServiceSetup.msi LocalAgentHost.msi, gerar e examinar os ficheiros de registo utilizando as seguintes linhas de comando:

-   Para instalações:`msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
-   Para desinstala:`msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Também pode ativar o registo para todas as instalações de que são efetuadas pelo Windows Installer. O artigo da Base de dados de Conhecimento Microsoft [como ativar o registo do Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornece uma solução de um clique para ativar o registo para o Windows Installer. Também fornece a localização dos registos.

### <a name="my-client-agent-doesnt-work"></a>Agente do cliente não funciona

#### <a name="description-and-symptoms"></a>A descrição e sintomas

Obter mensagens seguintes quando tenta utilizar o agente do cliente:

"Falha na sincronização com a exceção Ocorreu um erro ao tentar anular a serialização do parâmetro www.microsoft.com/.../05:GetBatchInfoResult. Consulte a InnerException para obter mais detalhes."

"Mensagem de exceção interna: o tipo 'Microsoft.Synchronization.ChangeBatch' é um tipo de coleção inválido desde que não tem um construtor predefinido."

#### <a name="cause"></a>Causa

Este é um problema conhecido com a instalação de sincronização de dados do SQL Server (pré-visualização). A causa mais provável desta mensagem é um dos seguintes:

-   Está a executar o Windows 8 Developer Preview.
-   Tem de instalar o .NET Framework 4.5.

#### <a name="resolution"></a>Resolução

Certifique-se de que instala o agente do cliente num computador que não está a executar o Windows 8 Developer Preview e que o .NET Framework 4.5 não está instalado.

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>Agente do cliente não funciona após cancelar a desinstalação

#### <a name="description-and-symptoms"></a>A descrição e sintomas

O agente do cliente não funciona, mesmo depois de cancelar a respetiva desinstalação.

#### <a name="cause"></a>Causa

Isto ocorre porque o agente de cliente de sincronização de dados do SQL Server (pré-visualização) não armazena as credenciais.

#### <a name="resolution"></a>Resolução

Pode tentar estas duas soluções:

-   Utilize o services.msc para reintroduzir as credenciais para o agente de cliente.
-   Desinstalar este agente de cliente e, em seguida, instale um novo. Transfira e instale o agente de cliente mais recente do [Centro de transferências](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="my-database-isnt-listed-in-the-agent-list"></a>A minha base de dados não está listado na lista de agente

#### <a name="description-and-symptoms"></a>A descrição e sintomas

Quando tentar adicionar uma base de dados existente do SQL Server a um grupo de sincronização, a base de dados não aparece na lista de agentes.

#### <a name="cause"></a>Causa

Estes cenários poderão causar este problema:

-   O grupo de agente e sincronização de cliente estiverem em datacenters diferentes.
-   Lista do agente de cliente das bases de dados não é atual.

#### <a name="resolution"></a>Resolução

A resolução depende a causa.

- **O grupo de agente e sincronização de cliente estiverem em datacenters diferentes**

    O agente do cliente e o grupo de sincronização devem estar no mesmo centro de dados. Para configurar esta opção, tem duas opções:

    -   Crie um novo agente no Centro de dados onde está localizado o grupo de sincronização. Em seguida, registe a base de dados com que o agente.
    -   Elimine o grupo de sincronização atual. Em seguida, recrie o grupo de sincronização no Centro de dados onde o agente está localizado.

- **Lista do agente de cliente das bases de dados não é atual**

    Pare e reinicie o serviço de agente do cliente.

    O agente local transfere a lista de bases de dados associadas apenas na submissão primeiro da chave do agente. -Não é transferido a lista de bases de dados associadas em submissões de chave de agente subsequentes. Bases de dados que estão registados durante uma mudança de agente não apareçam na instância original do agente.

### <a name="client-agent-doesnt-start-error-1069"></a>Agente de cliente não comece (erro 1069)

#### <a name="description-and-symptoms"></a>A descrição e sintomas

Detetar a que o agente não está em execução num computador que aloja o SQL Server. Ao tentar iniciar manualmente o agente, verá uma caixa de diálogo que é apresentada a mensagem "erro 1069: O serviço não foram iniciados devido a uma falha de início de sessão."

![Caixa de diálogo de erro 1069 de sincronização de dados](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>Causa

Uma causa provável deste erro é que a palavra-passe no servidor local foi alterado desde que o agente e a palavra-passe do agente.

#### <a name="resolution"></a>Resolução

Atualize palavra-passe o agente para a sua palavra-passe atual do servidor:

1. Localize o agente de cliente de sincronização de dados do SQL Server (pré-visualização) serviço de pré-visualização.  
    a. Selecione **iniciar**.  
    b. Na caixa de pesquisa, introduza **services.msc**.  
    c. Nos resultados da pesquisa, selecione **serviços**.  
    d. No **serviços** janela, desloque-se para a entrada para **pré-visualização de agente de sincronização de dados do SQL Server (pré-visualização)**.  
2. Clique com botão direito **pré-visualização de agente de sincronização de dados do SQL Server (pré-visualização)**e, em seguida, selecione **parar**.
3. Clique com botão direito **pré-visualização de agente de sincronização de dados do SQL Server (pré-visualização)**e, em seguida, selecione **propriedades**.
4. No **propriedades de pré-visualização do agente de sincronização de dados do SQL Server (pré-visualização)**, selecione o **início de sessão** separador.
5. No **palavra-passe** box, introduza a palavra-passe.
6. No **Confirmar palavra-passe** caixa, reintroduza a palavra-passe.
7. Selecione **aplicar**e, em seguida, selecione **OK**.
8. No **serviços** janela, clique com botão direito a **pré-visualização de agente de sincronização de dados do SQL Server (pré-visualização)** de serviço e, em seguida, clique em **iniciar**.
9. Fechar o **serviços** janela.

### <a name="i-cant-submit-the-agent-key"></a>Posso não é possível enviar a chave do agente

#### <a name="description-and-symptoms"></a>A descrição e sintomas

Depois de criar ou voltar a criar uma chave para um agente, tentar submeter a chave através da aplicação SqlAzureDataSyncAgent. A submissão não consegue concluir.

![Sincronizar a caixa de diálogo de erro - não é possível submeter a chave do agente](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Antes de continuar, verifique as seguintes condições:

-   O serviço do Windows de sincronização de dados do SQL Server (pré-visualização) está em execução.  
-   A conta de serviço do serviço do Windows de pré-visualização de sincronização de dados do SQL Server (pré-visualização) tem acesso à rede.    
-   O agente do cliente pode contactar o serviço de localizador. Certifique-se de que a seguinte chave de registo tem o valor https://locator.sync.azure.com/LocatorServiceApi.svc:  
    -   Num x86 computador:`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`  
    -   Num x64 computador:`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

#### <a name="cause"></a>Causa

A chave do agente identifica exclusivamente cada agente local. A chave tem de cumprir duas condições:

-   A chave do agente de cliente no servidor de sincronização de dados do SQL Server (pré-visualização) e o computador local deve ser idêntica.
-   A chave do agente de cliente pode ser utilizada apenas uma vez.

#### <a name="resolution"></a>Resolução

Se o agente não está a funcionar, é porque uma ou ambas estas condições não são cumpridas. Para obter o agente para funcionar novamente:

1. Gere uma nova chave.
2. Aplica a nova chave para o agente.

Para aplicar a nova chave para o agente:

1. No Explorador de ficheiros, aceda ao seu diretório de instalação do agente. O diretório de instalação predefinida é c:\\os ficheiros de programa (x86)\\sincronização de dados do Microsoft SQL Server.
2. Faça duplo clique o subdiretório bin.
3. Abra a aplicação de SqlAzureDataSyncAgent.
4. Selecione **submeter chave do agente**.
5. No espaço fornecido, cole a chave da área de transferência.
6. Selecione **OK**.
7. Feche o programa.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>Não é possível eliminar o agente do cliente no portal se a sua base de dados associado no local está inacessível

#### <a name="description-and-symptoms"></a>A descrição e sintomas

Se um ponto final local (ou seja, uma base de dados) que está registado com um agente de cliente de sincronização de dados do SQL Server (pré-visualização) ficar inacessível, não é possível eliminar o agente do cliente.

#### <a name="cause"></a>Causa

Não é possível eliminar o agente local porque a base de dados inacessível ainda está registado com o agente. Ao tentar eliminar o agente, o processo de eliminação tenta contactar a base de dados, falha.

#### <a name="resolution"></a>Resolução

Utilize "forçar a eliminação" para eliminar a base de dados inacessível.

> [!NOTE]
> Se as tabelas de metadados de sincronização permanecerem depois de uma "eliminação forçar", utilize deprovisioningutil.exe para limpá-los.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a>Aplicação local do agente de sincronização não é possível ligar ao serviço de sincronização local

#### <a name="resolution"></a>Resolução

Experimente os passos seguintes:

1. Sair da aplicação.  
2. Abra o painel de serviços de componentes.  
    a. Na caixa de pesquisa na barra de tarefas, introduza **services.msc**.  
    b. Nos resultados da pesquisa, faça duplo clique em **serviços**.  
3. Parar o **pré-visualização de sincronização de dados do SQL Server (pré-visualização)** serviço.
4. Reinicie o **pré-visualização de sincronização de dados do SQL Server (pré-visualização)** serviço.  
5. Reabra a aplicação.

## <a name="setup-and-maintenance-issues"></a>Problemas de configuração e manutenção

### <a name="i-get-a-disk-out-of-space-message"></a>É apresentada uma mensagem de "disco ficou sem espaço em"

#### <a name="cause"></a>Causa

A mensagem de "disco ficou sem espaço em" poderá aparecer se representada ficheiros têm de ser eliminado. Isto pode ser causado por software antivírus ou ficheiros abertos quando eliminar operações são tentadas.

#### <a name="resolution"></a>Resolução

Elimine manualmente os ficheiros de sincronização que estão na pasta % temp % (`del \*sync\* /s`). Em seguida, elimine os subdiretórios na pasta % temp %.

> [!IMPORTANT]
> Não elimine quaisquer ficheiros com sincronização em curso.

### <a name="i-cant-delete-my-sync-group"></a>Não é possível eliminar o meu grupo de sincronização

#### <a name="description-and-symptoms"></a>A descrição e sintomas

A tentativa de eliminar um grupo de sincronização irá falhar.

#### <a name="causes"></a>Causas

Qualquer um dos seguintes cenários poderá resultar numa falha ao eliminar um grupo de sincronização:

-   O agente do cliente está offline.
-   O agente de cliente é desinstalada ou em falta. 
-   Uma base de dados está offline. 
-   Grupo de sincronização de aprovisionamento ou a sincronizar. 

#### <a name="resolution"></a>Resolução

Para resolver a falha para eliminar um grupo de sincronização:

-   Certifique-se de que o agente do cliente está online e, em seguida, tente novamente.
-   Se o agente de cliente desinstalada ou de outra forma de em falta:  
    a. Remova o ficheiro XML de agente a pasta de instalação de sincronização de dados do SQL Server (pré-visualização), se o ficheiro existe.  
    b. Instale o agente num computador local (pode ser o mesmo ou outro computador). Em seguida, submeta a chave do agente que é gerada no portal para o agente que está a ser mostrado como offline.
-   Certifique-se de que o serviço de sincronização de dados do SQL Server (pré-visualização) está em execução:  
    a. No **iniciar** menu, procure **serviços**.  
    b. Nos resultados da pesquisa, selecione **serviços**.  
    c. Localizar o **pré-visualização de sincronização de dados do SQL Server (pré-visualização)** serviço.  
    d. Se o estado do serviço **parado**, faça duplo clique o nome do serviço e, em seguida, selecione **iniciar**.
-   Certifique-se de que o SQL Server e bases de dados do SQL Server estão todos os online.
-   Aguarde pela conclusão do processo de aprovisionamento ou sincronização e, em seguida, repita a eliminar o grupo de sincronização.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>Posso não é possível anular o registo de uma base de dados do SQL Server no local

#### <a name="cause"></a>Causa

Provavelmente, está a tentar anular o registo da base de dados que já tenha sido eliminada.

#### <a name="resolution"></a>Resolução

Ao anular o registo de uma base de dados do SQL Server no local, selecione a base de dados e, em seguida, selecione **Force eliminar**.

Se esta operação não consegue remover a base de dados do grupo de sincronização:

1. Pare e reinicie o serviço de anfitrião de agente do cliente:  
    a. Selecione o **iniciar** menu.  
    b. Na caixa de pesquisa, introduza **services.msc**.  
    c. No **programas** secção da pesquisa resulta painel, faça duplo clique em **serviços**.  
    d. Clique com botão direito do **sincronização de dados do SQL Server (pré-visualização)** serviço.  
    e. Se o serviço está em execução, pare-o.  
    f. O serviço com o botão direito e, em seguida, selecione **iniciar**.  
    g. Verifique se a base de dados ainda está registado. Se já não estiver registado, terminar. Caso contrário, continuar com o passo seguinte.
2. Abra a aplicação de agente do cliente (SqlAzureDataSyncAgent).
3. Selecione **editar credenciais de**e, em seguida, introduza as credenciais para a base de dados.
4. Continuar com a anulação do registo.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a>Eu não tiver privilégios suficientes para iniciar serviços do sistema

#### <a name="cause"></a>Causa

Este erro ocorre em duas situações:
-   O nome de utilizador e/ou a palavra-passe está incorreta.
-   A conta de utilizador especificado não tem privilégios suficientes para iniciar sessão como um serviço.

#### <a name="resolution"></a>Resolução

Conceda credenciais de registo-em-como-um-serviço para a conta de utilizador:

1. Aceda a **iniciar** > **painel de controlo** > **ferramentas administrativas** > **política de segurança Local**  >  **Política local** > **gestão de direitos de utilizador**.
2. Selecione **iniciar sessão como um serviço**.
3. No **propriedades** diálogo caixa, adicione a conta de utilizador.
4. Selecione **aplicar**e, em seguida, selecione **OK**.
5. Feche todas as janelas.

### <a name="a-database-has-an-out-of-date-status"></a>Uma base de dados tem um Estado "Desatualizado"

#### <a name="cause"></a>Causa

Sincronização de dados do SQL Server (pré-visualização) remove as bases de dados que foram offline do serviço de 45 dias ou mais (como contados desde o momento que a base de dados ficou offline). Se uma base de dados está offline para 45 dias ou mais e, em seguida, volta a ficar online, o respetivo estado é **Desatualizadas**.

#### <a name="resolution"></a>Resolução

Pode evitar um **Desatualizadas** Estado, garantindo que nenhuma das suas bases de dados fique offline de 45 dias ou mais.

Se o estado de uma base de dados é **Desatualizadas**:

1. Remover a base de dados que tenha um **Desatualizadas** estado do grupo de sincronização.
2. Adicionar a base de dados de volta para o grupo de sincronização.

> [!WARNING]
> Perderá todas as alterações efetuadas a esta base de dados enquanto estava offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a>Um grupo de sincronização tem um Estado "Desatualizado"

#### <a name="cause"></a>Causa

Se falharem um ou mais alterações aplicar durante o período de retenção todo de 45 dias, um grupo de sincronização pode tornar-se desatualizados.

#### <a name="resolution"></a>Resolução

Para evitar um **Desatualizadas** estado para um grupo de sincronização, examine os resultados das suas tarefas de sincronização no Visualizador do histórico regularmente. Investigar e resolver quaisquer alterações que não obedeçam a aplicar.

Se o estado de um grupo de sincronização é **Desatualizadas**, elimine o grupo de sincronização e, em seguida, volte a criá-la.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a>Não é possível eliminar um grupo de sincronização dentro de desinstalar ou parar o agente de três minutos

#### <a name="description-and-symptoms"></a>A descrição e sintomas

Não é possível eliminar um grupo de sincronização dentro de três minutos de desinstalar ou parar o agente de cliente de sincronização de dados do SQL Server (pré-visualização) associado.

#### <a name="resolution"></a>Resolução

1. Remover um grupo de sincronização, enquanto que os agentes de sincronização associados são online (recomendado).
2. Se o agente estiver offline, mas está instalado, colocá-la online no computador local. Aguarde que o estado do agente a aparecer como **Online** no portal de sincronização de dados do SQL Server (pré-visualização). Em seguida, remova o grupo de sincronização.
3. Se o agente estiver offline, porque foi desinstalado:  
    a.  Remova o ficheiro XML de agente a pasta de instalação de sincronização de dados do SQL Server (pré-visualização), se o ficheiro existe.  
    b.  Instale o agente num computador local (pode ser o mesmo ou outro computador). Em seguida, submeta a chave do agente que é gerada no portal para o agente que está a ser mostrado como offline.  
    c. Tente eliminar o grupo de sincronização.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>O que acontece quando restaurar a uma base de dados danificado ou perdido?

Se restaurar uma base de dados perdido ou danificado a partir de uma cópia de segurança, poderá haver um nonconvergence dos dados nos grupos de sincronização a que pertence a base de dados.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a sincronização de dados do SQL Server (pré-visualização), consulte:

-   [Sincronizar os dados em várias bases de dados na nuvem e no local com sincronização de dados de SQL do Azure (pré-visualização)](sql-database-sync-data.md)  
-   [Configurar a sincronização de dados de SQL do Azure (pré-visualização)](sql-database-get-started-sql-data-sync.md)  
-   [Melhores práticas para a sincronização de dados de SQL do Azure (pré-visualização)](sql-database-best-practices-data-sync.md)  
-   [Monitor SQL do Azure de sincronização de dados (pré-visualização) com a análise de registos do OMS](sql-database-sync-monitor-oms.md)  
-   Conclua os exemplos do PowerShell que mostram como configurar a sincronização de dados do SQL Server (pré-visualização):  
    -   [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Utilizar o PowerShell para sincronizar entre uma base de dados do SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Transferir a documentação da API de REST de sincronização de dados do SQL Server (pré-visualização)](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para obter mais informações sobre a base de dados SQL, consulte:

-   [Descrição geral da base de dados do SQL Server](sql-database-technical-overview.md)
-   [Gestão de ciclo de vida de base de dados](https://msdn.microsoft.com/library/jj907294.aspx)
