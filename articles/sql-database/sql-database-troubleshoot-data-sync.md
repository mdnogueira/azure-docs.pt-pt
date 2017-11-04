---
title: "Resolver problemas de sincronização de dados SQL do Azure | Microsoft Docs"
description: "Saiba como resolver problemas comuns com a sincronização de dados SQL do Azure"
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: d0cbe02e5a99fc62869256ed86e774aa2729f7e6
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-issues-with-azure-sql-data-sync"></a>Resolver problemas com a sincronização de dados SQL do Azure

Este artigo descreve como resolver problemas atuais que são conhecidos para a equipa de sincronização de dados do SQL Server (pré-visualização). Se existir uma solução para um problema, é fornecido aqui.
                                                           
## <a name="my-client-agent-doesnt-work"></a>Agente do cliente não funciona

### <a name="description-and-symptoms"></a>A descrição e sintomas

Obter as seguintes mensagens de erro quando tenta utilizar o agente do cliente.

"Falha na sincronização com a exceção Ocorreu um erro ao tentar anular a serialização do parâmetro www.microsoft.com/.../05:GetBatchInfoResult. Consulte a InnerException para obter mais detalhes.

"Mensagem de exceção interna: o tipo 'Microsoft.Synchronization.ChangeBatch' é um tipo de coleção inválido desde que não tem um construtor predefinido."


### <a name="cause"></a>Causa

Este erro é um problema com a sincronização de dados do SQL Server (pré-visualização).

A causa mais provável deste problema é:

-   Está a executar o Windows 8 Developer Preview ou

-   Ter o .NET 4.5 instalados.

### <a name="solution-or-workaround"></a>Solução ou com outra solução

Certifique-se que instale o agente de cliente num computador que não esteja a executar o Windows 8 Developer Preview e que o .NET Framework 4.5 não está instalado.

## <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>Agente do cliente não funciona após cancelar a desinstalação

### <a name="description-and-symptoms"></a>A descrição e sintomas

O agente do cliente não funciona, apesar de cancelada respetiva desinstalação.

### <a name="cause"></a>Causa

Este problema ocorre porque o agente de cliente de sincronização de dados do SQL Server (pré-visualização) não armazena as credenciais.

### <a name="solution-or-workaround"></a>Solução ou com outra solução

Existem duas soluções para experimentar:

-   Em primeiro lugar, utilize o services.msc para reintroduzir as suas credenciais para o agente de cliente.

-   Segundo, desinstalar este agente de cliente e instalar um novo. Transfira e instale o agente de cliente mais recente do [Centro de transferências](http://go.microsoft.com/fwlink/?linkid=221479).

## <a name="my-database-isnt-listed-in-the-agent-dropdown"></a>A minha base de dados não está listado na lista pendente de agente

### <a name="description-and-symptoms"></a>A descrição e sintomas

Quando tentar adicionar uma base de dados existente do SQL Server a um grupo de sincronização, a base de dados não está listado na lista pendente.

### <a name="cause"></a>Causa

Existem várias causas possíveis para este problema:

-   O grupo de agente e sincronização de cliente são nos dados de diferentes centros.

-   Lista do agente de cliente das bases de dados não está atualizada.

### <a name="solution"></a>Solução

A solução depende a causa.

#### <a name="the-client-agent-and-sync-group-are-in-different-data-centers"></a>O grupo de agente e sincronização de cliente são nos dados de diferentes centros

Tem de ter o agente do cliente e o grupo de sincronização no mesmo centro de dados. Pode configurar esta configuração através de uma das seguintes ações:

-   Crie um novo agente no mesmo centro de dados como o grupo de sincronização. Em seguida, registe a base de dados com que o agente. Consulte [como: instalar um agente de cliente de sincronização de dados do SQL Server (pré-visualização)](#install-a-sql-data-sync-client-agent) para obter mais informações.

-   Elimine o grupo de sincronização atual. Em seguida, recriá-lo no mesmo centro de dados como o agente.

#### <a name="the-client-agents-list-of-databases-is-not-current"></a>Lista do agente de cliente das bases de dados não está atualizada

Pare e reinicie o serviço de agente do cliente.
O agente local transfere a lista de bases de dados associadas apenas a submissão primeiro da chave do agente, não em submissões de chave de agente subsequentes. Assim, as bases de dados registados durante uma mudança de agente não aparecer na instância original do agente.

## <a name="client-agent-doesnt-start-error-1069"></a>Agente de cliente não comece (erro 1069)

### <a name="description-and-symptoms"></a>A descrição e sintomas

Detetar a que o agente não está em execução num computador que aloja o SQL Server. Ao tentar iniciar manualmente o agente, obtém uma caixa de diálogo de erro com a mensagem de erro, "erro 1069: O serviço não foram iniciados devido a uma falha de início de sessão."

![Caixa de diálogo de erro 1069 de sincronização de dados](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

### <a name="cause"></a>Causa

Uma causa provável deste erro é que a palavra-passe no servidor local mudou desde que criou o agente e atribuiu uma palavra-passe de início de sessão.

### <a name="solution-or-workaround"></a>Solução ou com outra solução

Atualize palavra-passe o agente para a sua palavra-passe atual do servidor.

1. Localize o agente de cliente de sincronização de dados do SQL Server (pré-visualização) serviço de pré-visualização.

    a. Clique em **iniciar**.

    b. Escreva "services.msc" na caixa de pesquisa.

    c. Nos resultados da pesquisa, clique em "Serviços".

    d. No **serviços** janela, desloque-se para a entrada para **pré-visualização de agente de sincronização de dados do SQL Server (pré-visualização)**.

2. A entrada com o botão direito e selecione **parar**.

3. Faça duplo clique na entrada e, em seguida, clique em **propriedades**.

4. No **propriedades de pré-visualização do agente de sincronização de dados do SQL Server (pré-visualização)** janela, clique em de **início de sessão** separador.

5. Introduza a palavra-passe na caixa de texto de palavra-passe.

6. Confirme a palavra-passe na caixa de texto Confirmar palavra-passe.

7. Clique em **Aplicar** e, em seguida, em **OK**.

8. No **serviços** janela, clique com botão direito a **pré-visualização de agente de sincronização de dados do SQL Server (pré-visualização)** de serviço, em seguida, clique em **iniciar**.

9. Fechar o **serviços** janela.

## <a name="i-get-a-disk-out-of-space-message"></a>É apresentada uma mensagem de "disco ficou sem espaço em"

### <a name="cause"></a>Causa

A mensagem de "disco ficou sem espaço em" pode aparecer quando os ficheiros que devem ser eliminados permanecem atrás. Esta condição pode ocorrer devido a software antivírus, ou porque os ficheiros estão abrir quando são tentadas as operações de eliminação.

### <a name="solution"></a>Solução

A solução é eliminar manualmente os ficheiros de sincronização em `%temp%` (`del \*sync\* /s`) e, em seguida, remover, bem como os subdiretórios.

> [!IMPORTANT]
> Aguarde até que a sincronização seja concluída antes de eliminar todos os ficheiros.

## <a name="i-cannot-delete-my-sync-group"></a>Não é possível eliminar o meu grupo de sincronização

### <a name="description-and-symptoms"></a>A descrição e sintomas

Falha na tentativa de eliminar um grupo de sincronização.

### <a name="causes"></a>Causas

Qualquer uma das seguintes ações podem resultar numa falha ao eliminar um grupo de sincronização.

-   O agente do cliente está offline.

-   O agente de cliente é desinstalada ou em falta. 

-   Uma base de dados está offline. 

-   Grupo de sincronização de aprovisionamento ou a sincronizar. 

### <a name="solutions"></a>Soluções

Para resolver a falha para eliminar um grupo de sincronização, verifique os seguintes procedimentos:

-   Certifique-se de que o agente do cliente está online e tente novamente.

-   Se o agente de cliente desinstalada ou de outra forma de em falta:

    a. Remova o ficheiro XML de agente da pasta de instalação de sincronização de dados do SQL Server (pré-visualização) se o ficheiro existe.

    b. Instale o agente no mesmo/outro no computador local, submeta a chave do agente do portal gerado para o agente que está a ser mostrada offline.

-   **O serviço de sincronização de dados do SQL Server (pré-visualização) está parado.**

    a. No **iniciar** menu, procure os serviços.

    b. Clique em serviços nos resultados da pesquisa.

    c. Localizar o **pré-visualização de sincronização de dados do SQL Server (pré-visualização)** serviço.

    d. Se o estado do serviço **parado**, o nome do serviço com o botão direito e selecione **iniciar** no menu de lista pendente.

-   Verifique as bases de dados de bases de dados SQL e SQL Server para Lembre-se de que estão todos os online.

-   Aguarde pela conclusão do processo de aprovisionamento ou sincronização. Em seguida, volte a tentar eliminar o grupo de sincronização.

## <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-associated-with-the-client-agent"></a>Falha de sincronização no portal de IU para bases de dados no local associadas com o agente do cliente

### <a name="description-and-symptoms"></a>A descrição e sintomas

Falha de sincronização no portal de sincronização de dados do SQL Server (pré-visualização) da IU para bases de dados no local associados com o agente. No computador local que executa o agente, consulte os erros de System.IO.IOException no registo de eventos, indicando que o disco tem espaço suficiente.

### <a name="solution-or-workaround"></a>Solução ou com outra solução

Crie mais espaço na unidade em que reside o diretório % TEMP %.

## <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>Posso não é possível anular o registo de uma base de dados do SQL Server no local

### <a name="cause"></a>Causa

Provavelmente, está a tentar anular o registo da base de dados que já tenha sido eliminada.

### <a name="solution-or-workaround"></a>Solução ou com outra solução

Para anular o registo de uma base de dados do SQL Server no local, selecione a base de dados e clique em **Force eliminar**.

Se esta operação não consegue remover a base de dados do grupo de sincronização, efetue os seguintes procedimentos:

1. Pare e reinicie o serviço de anfitrião de agente do cliente.

    a. Clique no menu de início.

    b. Introduza *services.msc* na caixa de pesquisa.

    c. Nos programas secção do painel de resultados faça duplo clique em **serviços**.

    d. Localize e faça duplo clique o serviço **sincronização de dados do SQL Server (pré-visualização)**.

    e. Se o serviço está em execução, pare-o.

    f. Clique com botão direito e selecione **iniciar**.

    g. Verifique se a base de dados já não está registado. Se já não estiver registado, terminar. Caso contrário, continuar com o passo seguinte.

2. Abra a aplicação de agente do cliente (SqlAzureDataSyncAgent).

3. Clique em **editar credenciais de** e fornecer as credenciais para a base de dados para que seja acessível.

4. Continuar com a anulação do registo.

## <a name="i-cannot-submit-the-agent-key"></a>Posso não é possível enviar a chave do agente

### <a name="description-and-symptoms"></a>A descrição e sintomas

Depois de criar ou recriar uma chave para um agente, tente ao submeter a essa chave através da aplicação SqlAzureDataSyncAgent, mas a submissão não consegue concluir.

![Sincronizar a caixa de diálogo de erro - não é possível submeter a chave do agente](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Antes de continuar, certifique-se de que uma falha de uma das seguintes condições não é a causa do seu problema.

-   O serviço do Windows de sincronização de dados do SQL Server (pré-visualização) está em execução.

-   A conta de serviço do serviço do Windows de pré-visualização de sincronização de dados do SQL Server (pré-visualização) tem acesso à rede.

-   O agente do cliente é capaz de contactar o serviço de localizador. Verifique se a seguinte chave de registo tem o valor "https://locator.sync.azure.com/LocatorServiceApi.svc"

    -   Num x86 computador:`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

    -   Num x64 computador:`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

### <a name="cause"></a>Causa

A chave do agente identifica exclusivamente cada agente local. A chave tem de cumprir duas condições para que esta funcione:

-   A chave do agente de cliente no servidor de sincronização de dados do SQL Server (pré-visualização) e o computador local deve ser idêntica.

-   A chave do agente de cliente pode ser utilizada apenas uma vez.

### <a name="solution-or-workaround"></a>Solução ou com outra solução

Se o agente não está a funcionar, é porque uma ou ambas estas condições não são cumpridas. Para obter o agente para funcionar novamente:

1. Gere uma nova chave.

2. Aplica a nova chave para o agente.

Para aplicar a nova chave para o agente, efetue os seguintes procedimentos:

1. Utilize o Explorador de ficheiros para navegar para o diretório de instalação do agente. O diretório de instalação predefinido é `c:\\program files (x86)\\microsoft sql data sync`.

2. Faça duplo clique o `bin` subdiretório.

3. Inicie o `SqlAzureDataSyncAgent` aplicação.

4. Clique em **submeter chave do agente**.

5. Cole a chave da área de transferência no espaço fornecido.

6. Clique em **OK**.

7. Feche o programa.

## <a name="i-do-not-have-sufficient-privileges-to-start-system-services"></a>Não tiver privilégios suficientes para iniciar serviços do sistema

### <a name="cause"></a>Causa

Este erro ocorre em duas situações:

-   O nome de utilizador e/ou a palavra-passe está incorreta.

-   A conta de utilizador especificado não tem privilégios suficientes para iniciar sessão como um serviço.

### <a name="solution-or-workaround"></a>Solução ou com outra solução

Conceda credenciais de registo-em-como-um-serviço para a conta de utilizador.

1. Navegue para **iniciar | Painel de controlo | Ferramentas administrativas |  Política de segurança local | Política local | Gestão de direitos de utilizador**.

2. Localize e clique o **iniciar sessão como um serviço** entrada.

3. Adicione a conta de utilizador no **iniciar sessão como um serviço propriedades** caixa de diálogo.

4. Clique em **aplicar** , em seguida, **OK**.

5. Feche as janelas.

## <a name="local-sync-agent-app-is-unable-to-connect-to-the-local-sync-service"></a>Aplicação local do agente de sincronização não é possível ligar ao serviço de sincronização local

### <a name="solution-or-workaround"></a>Solução ou com outra solução

Experimente os passos seguintes:

1. Sair da aplicação.

2. Abra o painel de serviços de componentes.

    a. Na caixa de pesquisa na barra de tarefas, escreva "services.msc".

    b. Faça duplo clique em "Serviços" nos resultados da pesquisa.

3. Pare e reinicie o serviço de "Pré-visualização de sincronização (pré-visualização) de dados de SQL Server".

4. Reinicie a aplicação.

## <a name="install-uninstall-or-repair-fails"></a>Instalar, desinstalar ou repare a falha

### <a name="cause"></a>Causa

Existem muitas causas possíveis da falha. Para determinar a causa desta falha específica, tem de observar os registos.

### <a name="solution-or-workaround"></a>Solução ou com outra solução

Para descobrir a causa específica para a falha que teve, terá de gerar e consulte os registos do Windows Installer. Pode ativar o registo na linha de comandos. Por exemplo, suponha que o ficheiro transferido do AgentServiceSetup.msi é LocalAgentHost.msi. Gerar e examinar os ficheiros de registo utilizando as seguintes linhas de comando:

-   Para instalações:`msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`

-   Para desinstala:`msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Também pode ativar o registo para todas as instalações efetuadas pelo programa de instalação do Windows. O artigo da Base de dados de Conhecimento Microsoft [como ativar o registo do Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornece uma solução de um clique para ativar o registo para o Windows Installer. Também fornece a localização estes registos.

## <a name="a-database-has-an-out-of-date-status"></a>Uma base de dados tem um Estado "Desatualizado"

### <a name="cause"></a>Causa

Sincronização de dados do SQL Server (pré-visualização) remove as bases de dados que tenham sido offline de 45 dias ou mais (como contados desde o momento a base de dados ficou offline) do serviço. Se uma base de dados está offline para 45 dias ou mais e, em seguida, volta a ficar online, o estado estiver definido como "Desatualizadas."

### <a name="solution-or-workaround"></a>Solução ou com outra solução

Pode evitar um Estado "Desatualizado", certificando-se de que nenhuma das suas bases de dados aceda offline de 45 dias ou mais.

Se o estado de uma base de dados é "Desatualizadas", precisa de executar os seguintes procedimentos:

1. Remova a base de dados "Desatualizada" do grupo de sincronização.

2. Adicionar a base de dados de volta para o grupo de sincronização.

> [!WARNING]
> Perderá todas as alterações efetuadas a esta base de dados enquanto estava offline.

## <a name="a-sync-group-has-an-out-of-date-status"></a>Um grupo de sincronização tem um Estado "Desatualizado"

### <a name="cause"></a>Causa

Se falharem um ou mais alterações aplicar durante o período de retenção todo de 45 dias, um grupo de sincronização pode tornar-se desatualizados.

### <a name="solution-or-workaround"></a>Solução ou com outra solução

Para evitar um Estado "Desatualizado", examine os resultados das suas tarefas de sincronização no Visualizador do histórico regularmente, investigar e resolver quaisquer alterações que não obedeçam a aplicar.

Se o estado de um grupo de sincronização é "Desatualizadas", terá de eliminar o grupo de sincronização e recriá-la.

## <a name="i-see-erroneous-data-in-my-tables"></a>Posso ver dados errados nas minhas tabelas

### <a name="description-and-symptoms"></a>A descrição e sintomas

Se as tabelas com o mesmo nome mas a partir de diferentes esquemas numa base de dados estão envolvidas na sincronização, consulte errados dados nestas tabelas após a sincronização.

### <a name="cause-and-fix"></a>Causa e corrija

A sincronização de dados do SQL Server (pré-visualização) processo de aprovisionamento utiliza as mesmo tabelas de controlo para tabelas com o mesmo nome mas em esquemas diferentes. Como resultado, as alterações de ambas as tabelas são refletidas na mesma tabela de controlo e este comportamento, fazendo com que dados errados é alterado durante a sincronização.

### <a name="resolution-or-workaround"></a>Resolução ou com outra solução

Certifique-se de que os nomes das tabelas envolvidos na sincronização são diferentes, mesmo que pertencem a diferentes esquemas.

## <a name="i-see-inconsistent-primary-key-data-after-a-successful-synchronization"></a>Posso ver dados de chaves primários inconsistentes após uma sincronização efetuada com êxito

### <a name="description-and-symptoms"></a>A descrição e sintomas

Depois de uma sincronização que é comunicado como concluída com êxito e o registo mostra sem linhas ignoradas ou falhadas, que observar o que são inconsistentes entre as bases de dados no grupo de sincronização de dados de chaves primária.

### <a name="cause"></a>Causa

Este comportamento é propositado. Alterações em qualquer resultado de coluna de chave primária em dados inconsistentes nas linhas em que a chave primária foi alterada.

### <a name="resolution-or-workaround"></a>Resolução ou com outra solução

Para evitar este problema, certifique-se de que não existem dados na coluna chave primária são alterados.

Para corrigir este problema após tiver ocorrido, tem de remover a linha afetada de todos os pontos finais no grupo de sincronização e, em seguida, reinsert a linha.

## <a name="i-see-a-significant-degradation-in-performance"></a>Vejo uma degradação significativa do desempenho

### <a name="description-and-symptoms"></a>A descrição e sintomas

O desempenho degrada significativamente, possivelmente para o ponto em que ainda não é possível iniciar a IU de sincronização de dados.

### <a name="cause"></a>Causa

A causa mais provável é um ciclo de sincronização. Um ciclo de sincronização ocorre quando uma sincronização por acionadores de grupo A sincronização a sincronização por grupo de sincronização B, que por sua vez aciona uma sincronização por grupo de sincronização A. A situação real pode ser mais complexa, que envolve mais do que dois grupos de sincronização no ciclo, mas o fator significativo é que haja um acionar circular de sincronizações causadas por grupos de sincronização a sobreposição de outro.

### <a name="resolution-or-workaround"></a>Resolução ou com outra solução

A correção melhor é prevenção. Certifique-se de que não têm as referências circulares nos seus grupos de sincronização. Não é possível sincronizar a qualquer linha que é sincronizada a um grupo de sincronização por outro grupo de sincronização.

## <a name="client-agent-cannot-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>Agente de cliente não é possível eliminar a partir do portal se a sua base de dados associado no local não está acessível

### <a name="description-and-symptoms"></a>A descrição e sintomas

Se um ponto final local (ou seja, uma base de dados) está registado com um agente de cliente de sincronização de dados do SQL Server (pré-visualização) ficar inacessível, não é possível eliminar o agente do cliente.

### <a name="cause"></a>Causa

Não é possível eliminar o agente local porque a base de dados inacessível ainda está registado com o agente. Ao tentar eliminar o agente, o processo de eliminação tenta contactar a base de dados, falha.

### <a name="resolution-or-workaround"></a>Resolução ou com outra solução

Utilize "forçar a eliminação" para eliminar a base de dados inacessível.

> [!NOTE]
> Se depois de tabelas de metadados de sincronização um "eliminar force" permanecem deprovisioningutil.exe utilização para limpá-los.

## <a name="a-sync-group-cannot-be-deleted-within-three-minutes-of-uninstallingstopping-the-agent"></a>Não é possível eliminar um grupo de sincronização dentro de desinstalar/parar o agente de três minutos

### <a name="description-and-symptoms"></a>A descrição e sintomas

Não é possível eliminar um grupo de sincronização dentro de três minutos de desinstalar/parar o agente de cliente de sincronização de dados do SQL Server (pré-visualização) associado.

### <a name="resolution-or-workaround"></a>Resolução ou com outra solução

1. Remover um grupo de sincronização, enquanto que os agentes de sincronização associados são online (recomendado).

2. Se o agente está instalado mas offline, colocá-la online no computador local. Em seguida, aguarde que o estado do agente a aparecer como online no portal de sincronização de dados do SQL Server (pré-visualização) e remova o grupo de sincronização.

3. Se o agente estiver offline, porque foi desinstalado, efetue os seguintes procedimentos. Em seguida, tente eliminar o grupo de sincronização.

    a.  Remova o ficheiro XML de agente a pasta de instalação de sincronização de dados do SQL Server (pré-visualização) se o ficheiro existe.

    b.  Instale o agente no mesmo ou outro num computador local, submeta a chave do agente do portal gerado para o agente que está a ser mostrada offline.

## <a name="my-sync-group-is-stuck-in-the-processing-state"></a>O meu grupo de sincronização está bloqueado no estado de processamento

### <a name="description-and-symptoms"></a>A descrição e sintomas

Um grupo de sincronização na sincronização de dados do SQL Server (pré-visualização) foi no estado de processamento durante um longo período de tempo, não responder ao comando de paragem e os registos mostram não novas entradas.

### <a name="causes"></a>Causas

Qualquer uma das seguintes condições pode resultar num grupo de sincronização que está a ser bloqueado no estado de processamento.

-   **O agente do cliente está offline.** Certifique-se de que o agente do cliente está online e tente novamente.

-   **O agente de cliente é desinstalada ou em falta.** Se o agente de cliente desinstalada ou de outra forma de em falta:

    1. Remova o ficheiro XML de agente da pasta de instalação de sincronização de dados do SQL Server (pré-visualização) se o ficheiro existe.

    2. Instale o agente no mesmo/outro no computador local. Em seguida, submeta a chave do agente do portal gerado para o agente que está a ser mostrado como offline.

-   **O serviço de sincronização de dados do SQL Server (pré-visualização) está parado.**

    1. No **iniciar** menu, procure os serviços.

    2. Clique em serviços nos resultados da pesquisa.

    3. Localizar o **sincronização de dados do SQL Server (pré-visualização)** serviço.

    4. Se o estado do serviço **parado**, o nome do serviço com o botão direito e selecione **iniciar** no menu de lista pendente.

### <a name="solution-or-workaround"></a>Solução ou com outra solução

Se não for possível corrigir o problema, é possível repor o estado do seu grupo de sincronização pelo suporte da Microsoft. Para ter o estado da reposição, crie uma mensagem num fórum no [fórum da SQL Database do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)e inclui o ID de subscrição e o ID do grupo de sincronização para o grupo que tem de ser reposto. Um engenheiro de suporte da Microsoft irá responder a sua publicação e informá-lo quando o estado foi reposto.

