---
title: "Sincronização do Azure AD Connect: programador | Microsoft Docs"
description: "Este tópico descreve a funcionalidade de programador incorporada na sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 63f69756b3933fecdec75cc677e1098447e5b94e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-scheduler"></a>Sincronização do Azure AD Connect: programador
Este tópico descreve o programador incorporado na sincronização do Azure AD Connect (a.k.a. motor de sincronização).

Esta funcionalidade foi introduzida com compilação 1.1.105.0 (Fevereiro de 2016 lançada).

## <a name="overview"></a>Descrição geral
Sincronização do Azure AD Connect sincronizar as alterações no seu diretório no local utilizando um programador a ocorrer. Existem dois processos de programador, uma para sincronização de palavra-passe e outra para tarefas de sincronização e a manutenção do objeto/atributos. Este tópico aborda a última opção.

Nas versões anteriores, o programador para objetos e atributos foi externo para o motor de sincronização. Utilizado Programador de tarefas do Windows ou um serviço do Windows separado para acionar o processo de sincronização. O programador está com incorporada 1.1 versões para a sincronização motor e permitir que alguns personalização. A frequência de sincronização do novo predefinido é 30 minutos.

O programador é responsável por duas tarefas:

* **Ciclo de sincronização**. O processo para importar, sincronizar e exportar as alterações.
* **Tarefas de manutenção**. Renove as chaves e certificados para a reposição de palavra-passe e o serviço de registo de dispositivos (DRS). Remover o antigas entradas no registo de operações.

O Programador de si próprio está sempre em execução, mas pode ser configurado para executar apenas um ou nenhuma destas tarefas. Por exemplo, se precisar de ter o seu próprio processo de ciclo de sincronização, pode desativar esta tarefa no programador mas continuar a executar a tarefa de manutenção.

## <a name="scheduler-configuration"></a>Configuração do Programador
Para obter as definições de configuração atual, vá para o PowerShell e execute `Get-ADSyncScheduler`. Mostra um nome semelhante esta imagem:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings2016.png)

Se vir **o comando de sincronização ou o cmdlet não está disponível** quando executar este cmdlet, em seguida, o módulo do PowerShell não está carregado. Este problema pode ocorrer se executar o Azure AD Connect num controlador de domínio ou num servidor com níveis superiores de restrição de PowerShell que as definições predefinidas. Se vir este erro, em seguida, execute `Import-Module ADSync` para disponibilizar o cmdlet.

* **AllowedSyncCycleInterval**. O intervalo de tempo mais curto entre ciclos de sincronização permitido pelo Azure AD. Não é possível sincronizar mais frequentemente do que esta definição e ainda ser suportado.
* **CurrentlyEffectiveSyncCycleInterval**. A agenda está a ser atualmente em vigor. Tem o mesmo valor que CustomizedSyncInterval (se definir) se não for mais frequente do que AllowedSyncInterval. Se utilizar uma compilação antes 1.1.281 e alterar CustomizedSyncCycleInterval, esta alteração entra em vigor após o próximo ciclo de sincronização. De compilação 1.1.281 a alteração entra em vigor imediatamente.
* **CustomizedSyncCycleInterval**. Se pretender que o programador para executar quaisquer outros frequência que não o predefinido de 30 minutos, em seguida, configura esta definição. Na imagem acima, o programador já foi definido para ser executada a cada hora. Se definir esta definição para um valor inferior ao AllowedSyncInterval, em seguida, a última opção é utilizada.
* **NextSyncCyclePolicyType**. O Delta ou inicial. Define se a próxima execução deve apenas as alterações de delta processo, ou se a próxima execução deve fazer um completo importar e sincronizar. A última opção também seria Reprocessar quaisquer regras novas ou alteradas.
* **NextSyncCycleStartTimeInUTC**. Próxima vez que o Programador de inicia o próximo ciclo de sincronização.
* **PurgeRunHistoryInterval**. O tempo devem ficar os registos de operações. Estes registos podem ser revistos no Gestor do serviço de sincronização. A predefinição é manter estes registos para 7 dias.
* **SyncCycleEnabled**. Indica se o programador está a executar a importação, sincronização e processos de exportação como parte da sua operação.
* **MaintenanceEnabled**. Mostra se o processo de manutenção está ativado. -Certificados/chaves de atualizações e elimina o registo de operações.
* **StagingModeEnabled**. Mostra se [modo de teste](active-directory-aadconnectsync-operations.md#staging-mode) está ativada. Se esta definição estiver ativada, em seguida,-suprime o exporta a execução mas continuar a executar importação e sincronização.
* **SchedulerSuspended**. Definidas por ligar durante uma atualização para bloquear temporariamente o Programador de execução.

Pode alterar algumas destas definições com `Set-ADSyncScheduler`. Podem ser modificados os seguintes parâmetros:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

No compilações anteriores do Azure AD Connect, **isStagingModeEnabled** foi exposta na ADSyncScheduler de conjunto. É **não suportado** para definir esta propriedade. A propriedade **SchedulerSuspended** só deve ser modificado por ligar. É **não suportado** para configurar esta diretamente com o PowerShell.

A configuração do programador é armazenada no Azure AD. Se tiver um servidor de teste, qualquer alteração no servidor primário também afeta o servidor de transição (exceto IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Sintaxe:`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dias, HH - horas, segundos mm - minutos, ss-

Exemplo:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Altera o programador para ser executada a cada 3 horas.

Exemplo:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Alterações de alterar o programador para ser executada diariamente.

### <a name="disable-the-scheduler"></a>Desativar o programador  
Se precisar de efetuar alterações de configuração, em seguida, que pretende desativar o programador. Por exemplo, quando lhe [configurar a filtragem](active-directory-aadconnectsync-configure-filtering.md) ou [efetuar alterações às regras de sincronização](active-directory-aadconnectsync-change-the-configuration.md).

Para desativar o programador, execute `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Desativar o programador](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

Quando tiver tomado as suas alterações, não se esqueça de ativar o programador com `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>Iniciar o programador
O programador está por predefinição executar cada 30 minutos. Em alguns casos, poderá querer executar um ciclo de sincronização entre os ciclos agendados ou tem de executar um tipo diferente.

**Ciclo de sincronização de diferenças**  
Um ciclo de sincronização de diferenças inclui os seguintes passos:

* Importação delta em todos os conectores
* Sincronização delta em todos os conectores
* Exportar em todos os conectores

É possível que tenha uma alteração urgente que têm de ser sincronizada imediatamente, o que é por isso terá de executar manualmente um ciclo. Se precisar de executar manualmente um ciclo, em seguida, da execução do PowerShell `Start-ADSyncSyncCycle -PolicyType Delta`.

**Ciclo de sincronização completa**  
Se tiver efetuado uma das seguintes alterações de configuração, tem de executar um ciclo de sincronização completa (a.k.a. Inicial):

* Adicionou mais objetos ou atributos a serem importados a partir de um diretório de origem
* Efetuou alterações para as regras de sincronização
* Alterar [filtragem](active-directory-aadconnectsync-configure-filtering.md) para um número diferente de objetos deve ser incluído

Se tiver efetuado uma destas alterações, em seguida, terá de executar um ciclo de sincronização completa para que o motor de sincronização tem a oportunidade de reconsolidate os espaços de conector. Um ciclo de sincronização completo inclui os seguintes passos:

* Importação completa em todos os conectores
* Sincronização completa no todos os conectores
* Exportar em todos os conectores

Para iniciar um ciclo de sincronização completa, execute `Start-ADSyncSyncCycle -PolicyType Initial` de uma linha de comandos do PowerShell. Este comando inicia um ciclo de sincronização completa.

## <a name="stop-the-scheduler"></a>Parar o programador
Se o programador está atualmente em execução um ciclo de sincronização, poderá ter de pará-lo. Por exemplo, se iniciar o Assistente de instalação e obter este erro:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Quando um ciclo de sincronização está em execução, não é possível efetuar alterações de configuração. Foi possível aguardar até que o programador concluiu o processo, mas também podem interromper, pelo que pode efetuar as alterações imediatamente. A parar o ciclo atual não é prejudicial e as alterações pendentes são processados de próxima execução.

1. Comece por informar o programador para parar o respetivo ciclo atual com o cmdlet do PowerShell `Stop-ADSyncSyncCycle`.
2. Se utilizar uma compilação antes 1.1.281, em seguida, parar o Programador de não a interromperá se o conector atual da respetiva tarefa atual. Para forçar o conector para parar, efetuar as seguintes ações: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
   * Iniciar **serviço de sincronização** no menu Iniciar. Aceda a **conectores**, realce o conector com o estado **executar**e selecione **parar** das ações.

O agendador ainda está ativo e inicia novamente na próxima oportunidade.

## <a name="custom-scheduler"></a>Programador personalizado
Os cmdlets documentados nesta secção só estão disponíveis na compilação [1.1.130.0](active-directory-aadconnect-version-history.md#111300) e mais tarde.

Se o programador incorporado não satisfaz os requisitos de, em seguida, pode agendar os conectores com o PowerShell.

### <a name="invoke-adsyncrunprofile"></a>ADSyncRunProfile invocar
Pode iniciar um perfil para um conector desta forma:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Os nomes a utilizar para [os nomes dos conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md) e [nomes de perfil de executar](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) pode ser encontrado no [da IU do Gestor do serviço de sincronização](active-directory-aadconnectsync-service-manager-ui.md).

![Invocar o perfil de execução](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

O `Invoke-ADSyncRunProfile` cmdlet é síncrona, ou seja, não regressa controlo até o conector foi concluída a operação com êxito ou com um erro.

Quando agendar os conectores, a recomendação é agendá-los pela seguinte ordem:

1. (Completo/Delta) Importar de diretórios no local, como o Active Directory
2. (Completo/Delta) Importação do Azure AD
3. (Completo/Delta) Sincronização de diretórios no local, como o Active Directory
4. (Completo/Delta) Sincronização do Azure AD
5. Exportar para o Azure AD
6. Exportar para diretórios no local, como o Active Directory

Esta ordem é como o programador incorporado executa os conectores.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Também pode monitorizar o motor de sincronização para ver se está ocupado ou inativo. Este cmdlet devolve um resultado vazio se o motor de sincronização está inativo e não está em execução de um conector. Se estiver a executar um conector, devolve o nome do conector.

```
Get-ADSyncConnectorRunStatus
```

![Estado de execução do conector](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
Na imagem acima, a primeira linha é de um Estado em que o motor de sincronização está inativo. A segunda linha de quando está a executar o conector do Azure AD.

## <a name="scheduler-and-installation-wizard"></a>Assistente de instalação e do agendador
Se iniciar o Assistente de instalação, o programador está temporariamente suspensa. Este comportamento é porque é suposto efetuar alterações de configuração e não não possível aplicar estas definições se o motor de sincronização está ativamente em execução. Por este motivo, não deixe o Assistente de instalação aberta, uma vez que interrompe o motor de sincronização de efetuar quaisquer ações de sincronização.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
