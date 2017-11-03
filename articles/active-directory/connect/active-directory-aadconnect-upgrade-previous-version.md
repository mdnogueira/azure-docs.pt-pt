---
title: "O Azure AD Connect: Atualizar de uma versão anterior | Microsoft Docs"
description: "Explica os diferentes métodos para atualizar para a versão mais recente do Azure Active Directory Connect, incluindo uma atualização no local e uma migração swing."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 52fd9375c71c42feaf87f4a0f4220e1cb3889e63
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>O Azure AD Connect: Atualização de uma versão anterior para a versão mais recente
Este tópico descreve os diferentes métodos que pode utilizar para atualizar a instalação do Connect do Azure Active Directory (Azure AD) para a versão mais recente. Recomendamos que mantenha sozinho atual com as versões do Azure AD Connect. Também é utilizar os passos a [Swing migração](#swing-migration) secção quando fizer uma alteração de configuração significativas.

Se pretender atualizar do DirSync, consulte [atualizar da ferramenta de sincronização do Azure AD (DirSync) do](active-directory-aadconnect-dirsync-upgrade-get-started.md) em vez disso.

Existem algumas estratégias diferentes que pode utilizar a atualização do Azure AD Connect.

| Método | Descrição |
| --- | --- |
| [Atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) |Este é o método mais fácil para os clientes com uma instalação rápida. |
| [Atualização no local](#in-place-upgrade) |Se tiver um único servidor, pode atualizar a instalação direta no mesmo servidor. |
| [Migração de swing](#swing-migration) |Com dois servidores, pode preparar um dos servidores com a nova versão ou a configuração e alterar o servidor de Active Directory quando estiver pronto. |

Para informações de permissões, consulte o [as permissões necessárias para uma atualização](active-directory-aadconnect-accounts-permissions.md#upgrade).

> [!NOTE]
> Depois de ativar o novo servidor do Azure AD Connect iniciar a sincronizar alterações para o Azure AD, é necessário não reverter utilizando DirSync ou o Azure AD Sync. Desatualização do Azure AD Connect para clientes legados, incluindo o DirSync e Azure AD Sync, não é suportada e pode provocar problemas tais como a perda de dados no Azure AD.

## <a name="in-place-upgrade"></a>Atualização no local
Uma atualização no local funciona para mover do Azure AD Sync ou do Azure AD Connect. Não funciona para mover a partir do DirSync ou para uma solução com o Forefront Identity Manager (FIM) + conector Azure AD.

Este método é preferencial quando tiver um servidor único e inferior a prestes até 100.000 objetos. Se existirem quaisquer alterações às regras de sincronização de out-of-box, uma importação completa e uma sincronização completa ocorrerem após a atualização. Este método garante que a nova configuração é aplicada a todos os objetos existentes no sistema. Esta execução poderá demorar algumas horas, consoante o número de objetos que estão no âmbito do motor de sincronização. O agendador de sincronização de diferenças normal (que sincroniza a cada 30 minutos por predefinição) está suspenso, mas continua a sincronização de palavra-passe. Pode considerar efetuar a atualização no local durante um fim de semana. Se existirem que sem alterações à configuração de out-of-box com o Azure AD Connect com a nova versão, em seguida, uma importação/sincronização normal delta inicia em vez disso.  
![Atualização no local](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Se tiver efetuado alterações para as regras de sincronização de out-of-box, em seguida, estas regras estiverem definidas novamente para a configuração predefinida a actualização. Para se certificar de que a configuração é mantida entre as atualizações, certifique-se de que efetua alterações como estão descritos [melhores práticas para alterar a configuração predefinida](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

Durante a atualização no local, poderão existir alterações introduzidas que necessitam de atividades de sincronização específica (incluindo o passo de importação completa e o passo de sincronização completa) para ser executada após a conclusão da atualização. Para diferir dessas atividades, consulte a secção [como diferir sincronização completa após a atualização](#how-to-defer-full-synchronization-after-upgrade).

## <a name="swing-migration"></a>Migração rotativa
Se tiver uma implementação de complexa ou muitos objetos, poderá ser impractical para efetuar uma atualização no local no sistema em direto. Para alguns clientes, este processo poderá demorar vários dias – e durante este período, são processadas sem alterações de delta. Também pode utilizar este método quando pretender efetuar alterações substanciais à sua configuração e pretende experimentá-los antes da enviadas por push para a nuvem.

O método recomendado para estes cenários é a utilização de uma migração swing. É necessário (pelo menos) dois servidores - um servidor de Active Directory e um servidor de teste. Servidor do Active Directory (mostrado com linhas azuis sólidas na imagem seguinte) é responsável pela carga de produção Active Directory. O servidor de transição (mostrado com linhas roxa tracejadas) está preparado com a nova versão ou a configuração. Quando estiver pronto completamente, este servidor é efetuado Active Directory. O Active Directory servidor anterior, que agora tem a versão antiga ou configuração instalado, é efetuado para o servidor de teste e é atualizado.

Os dois servidores, podem utilizar uma versão diferente. Por exemplo, o servidor do Active Directory que planeia desativar pode utilizar o Azure AD Sync e o novo servidor de teste, pode utilizar o Azure AD Connect. Se utilizar a migração de swing para desenvolver uma nova configuração, é boa ideia ter as mesmas versões em dois servidores.  
![Servidor de teste](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Alguns clientes preferem ter três ou quatro servidores para este cenário. Quando o servidor de transição é atualizado, não tem um servidor de cópia de segurança para [recuperação após desastre](active-directory-aadconnectsync-operations.md#disaster-recovery). Com três ou quatro servidores, pode preparar um conjunto de servidores de modo de espera/principal com a nova versão, que garante que sempre há um servidor de teste que está pronto para assumir o controlo.

Estes passos também funcionam para mover do Azure AD Sync ou uma solução com o FIM + conector Azure AD. Estes passos não funcionam para DirSync, mas o mesmo swing método de migração (também denominado implementação paralela) com os passos para o DirSync em [sincronização de atualização do Azure Active Directory (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Utilize uma migração swing para atualizar
1. Se utilizar o Azure AD Connect em ambos os servidores e planeie efetuar apenas uma configuração alterar, certifique-se de que o servidor de Active Directory e o servidor de transição estão ambos a utilizar a mesma versão. Que torna mais fácil comparar as diferenças mais tarde. Se estiver a atualizar a partir do Azure AD Sync, estes servidores têm versões diferentes. Se estiver a atualizar a partir de uma versão mais antiga do Azure AD Connect, é boa ideia começar com os dois servidores que estão a utilizar a mesma versão, mas não é necessário.
2. Se tiver tomado uma configuração personalizada e o servidor de teste não o tiver, siga os passos em [mover uma configuração personalizada do servidor ativo para o servidor de teste](#move-custom-configuration-from-active-to-staging-server).
3. Se estiver a atualizar a partir de uma versão anterior do Azure AD Connect, atualize o servidor de teste para a versão mais recente. Se estiver a mover do Azure AD Sync, em seguida, instale o Azure AD Connect no seu servidor de teste.
4. Permitir que o motor de sincronização executar importação completa e sincronização completa no seu servidor de teste.
5. Certifique-se que a nova configuração não fazer com que quaisquer alterações inesperadas, utilizando os passos em "Verificar" na [verificar a configuração de um servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Se algo não for conforme esperado, corrigi-lo, execute a importação e sincronização e verificar os dados até que a procura boa, seguindo os passos.
6. Mude o servidor de transição para o servidor de Active Directory. Este é o último passo "Servidor de Active Directory de comutador" em [verificar a configuração de um servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Se estiver a atualizar o Azure AD Connect, atualize o servidor que está agora em modo de teste para a versão mais recente. Siga os mesmos passos para obter os dados e a configuração atualizada. Se tiver efetuado a atualização a partir do Azure AD Sync, pode agora desligar e desativar o servidor antigo.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Mover uma configuração personalizada do servidor ativo para o servidor de teste
Se tiver tomado alterações de configuração para o servidor do Active Directory, tem de certificar-se de que as mesmas alterações são aplicadas ao servidor de teste. Para ajudar a movimentação, pode utilizar o [documenter de configuração do Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Pode mover que a sincronização personalizada regras que criou utilizando o PowerShell. Tem de aplicar outras alterações da mesma forma em ambos os sistemas e não é possível migrar as alterações. O [documenter configuração](https://github.com/Microsoft/AADConnectConfigDocumenter) podem ajudar a comparar os dois sistemas para se certificar de que o se são idênticos. A ferramenta também pode ajudar a automatizar passos encontrados nesta secção.

Terá de configurar as seguintes ações da mesma forma em ambos os servidores:

* Ligação para as mesmas todas as florestas
* Qualquer domínio filtragem e de UOS
* As mesmas as funcionalidades opcionais, tais como a sincronização de palavra-passe e a repetição de escrita de palavras-passe

**Mover as regras de sincronização personalizados**  
Para mover as regras de sincronização personalizados, efetue o seguinte:

1. Abra **Editor de regras de sincronização** no seu servidor do Active Directory.
2. Selecione uma regra personalizada. Clique em **exportar**. Aparece uma janela do bloco de notas. Guarde o ficheiro temporário com uma extensão de PS1. Isto faz com que um script do PowerShell. Copie o ficheiro PS1 para o servidor de teste.  
   ![Exportação de regra de sincronização](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. O GUID do conector é diferente no servidor de teste e deve alterá-la. Para obter o GUID, iniciar **Editor de regras de sincronização**, selecione uma das regras de out-of-box que representam o mesmo sistema ligado e clique em **exportar**. Substitua o GUID no seu ficheiro PS1 com o GUID do servidor de teste.
4. Na linha de comandos do PowerShell, execute o ficheiro PS1. Esta ação cria a regra de sincronização personalizados no servidor de teste.
5. Repita esta para todas as suas regras personalizadas.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Como diferir sincronização completa após a atualização
Durante a atualização no local, poderão existir alterações introduzidas que necessitam de atividades de sincronização específica (incluindo o passo de importação completa e o passo de sincronização completa) para ser executada. Por exemplo, exigem alterações do esquema de conector **completa importação** requerem alterações de regra de sincronização do passo e out-of-box **completa sincronização** passo para ser executado num conectores afetados. Durante a atualização, o Azure AD Connect determina quais as atividades de sincronização são necessárias e regista-los como *substitui*. Num ciclo de sincronização seguinte, o agendador de sincronização escolherá estas substituições e executa-los. Depois de uma substituição é executada com êxito, é removido.

Poderão existir situações em que pretenda estas substituições para ocorrer imediatamente após a atualização. Por exemplo, tiver vários objetos sincronizados e pretender que estes passos de sincronização para ocorrer após o horário de expediente. Para remover estas substituições:

1. Durante a atualização, **desmarque** a opção **iniciar o processo de sincronização ao concluir a configuração**. Isto desativa o agendador de sincronização e impede o ciclo de sincronização ocorridos automaticamente antes das substituições são removidas.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync01.png)

2. Após a conclusão da atualização, execute o seguinte cmdlet para saber que substituições foram adicionadas:`Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > As substituições são específicas do conector. No exemplo seguinte, o passo de importação completa e de que o passo de sincronização completa foram adicionadas para no local conector AD e do conector do Azure AD.

   ![DisableFullSyncAfterUpgrade](./media/active-directory-aadconnect-upgrade-previous-version/disablefullsync02.png)

3. Tome nota dos existente substituições que foram adicionados.
   
4. Para remover as substituições para importação completa e uma sincronização completa num conector arbitrário, execute o seguinte cmdlet:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Para remover as substituições em todos os conectores, execute o seguinte script do PowerShell:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Para retomar o programador, execute o seguinte cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Lembre-se ao executar os passos de sincronização necessário convier mais antigo. Manualmente pode executar estes passos a utilizar o Synchronization Service Manager ou adicionar que as substituições cópia utilizando o cmdlet Set-ADSyncSchedulerConnectorOverride.

Para adicionar as substituições para importação completa e sincronização completa de um conector arbitrário, execute o seguinte cmdlet:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
