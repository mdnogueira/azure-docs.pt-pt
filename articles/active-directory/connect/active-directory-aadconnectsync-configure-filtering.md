---
title: "Sincronização do Azure AD Connect: configurar a filtragem | Microsoft Docs"
description: "Explica como configurar a filtragem de sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 2e1adf5935e7fc01a24db6ada3c4cfe4ac0a4d55
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Sincronização do Azure AD Connect: configurar a filtragem
Ao utilizar a filtragem, pode controlar quais os objetos são apresentados no Azure Active Directory (Azure AD) do seu diretório no local. A configuração predefinida demora todos os objetos em todos os domínios em florestas configurados. Em geral, esta é a configuração recomendada. Os utilizadores com cargas de trabalho do Office 365, como o Exchange Online e Skype para empresas, beneficiam de uma lista completa de endereços Global para que podem enviar correio eletrónico e chamar todas as pessoas. A configuração predefinida, seria têm a mesma experiência que têm com uma implementação no local do Exchange ou o Lync.

Em alguns casos, no entanto, está necessário efetuar algumas alterações para a configuração predefinida. Eis alguns exemplos:

* Planeia utilizar o [topologia de diretório com múltiplos do Azure AD](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Em seguida, terá de aplicar um filtro para controlar quais os objetos que são sincronizados a um determinado diretório do Azure AD.
* Executar um piloto para o Azure ou do Office 365 e pretende apenas um subconjunto de utilizadores no Azure AD. No projeto piloto pequeno, não é importante que tenha uma lista de endereços Global completa para demonstrar a funcionalidade.
* Tem várias contas de serviço e outras contas nonpersonal que não pretende no Azure AD.
* Por motivos de conformidade, não elimine nenhum utilizador contas no local. É apenas desativá-los. Mas no Azure AD, pretende apenas contas do Active Directory estar presente.

Este artigo explica como configurar os diferentes métodos de filtragem.

> [!IMPORTANT]
> Microsoft não suporta a modificação ou a funcionar de sincronização do Azure AD Connect fora as ações que estão documentados formally. Qualquer uma destas ações poderá resultar num estado inconsistente ou não suportado de sincronização do Azure AD Connect. Como resultado, a Microsoft não é possível fornecer o suporte técnico para implementações deste tipo.

## <a name="basics-and-important-notes"></a>Noções básicas e notas importantes
Na sincronização do Azure AD Connect, pode ativar a filtragem em qualquer altura. Se começar com uma configuração predefinida de sincronização de diretórios e, em seguida, configurar a filtragem, os objetos que são filtrados já não estão sincronizados com o Azure AD. Devido a esta alteração, são eliminados quaisquer objetos no Azure AD que foram sincronizados anteriormente mas, em seguida, foram filtrados no Azure AD.

Antes de começar efetuar alterações a filtragem, certifique-se de que a [desativar a tarefa agendada](#disable-scheduled-task) para acidentalmente não exportar as alterações que ainda não tiver verificado corretas.

Porque filtragem, pode remover muitos objetos ao mesmo tempo, pretende assegurar que os novos filtros estão corretos antes de começar a exportar todas as alterações para o Azure AD. Após concluir os passos de configuração, recomendamos vivamente que segue o [passos de verificação](#apply-and-verify-changes) antes de exportar e fazer alterações para o Azure AD.

Para proteção contra eliminação muitos objetos acidentalmente, a funcionalidade "[impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)" está ativada por predefinição. Se eliminar muitos objetos devido a filtragem (500 por predefinição), tem de seguir os passos neste artigo para permitir que as eliminações percorrer para o Azure AD.

Se utilizar uma compilação antes de Novembro de 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)), efetuar uma alteração para uma configuração de filtro e utilizar a sincronização de palavra-passe, em seguida, é necessário acionar uma sincronização completa de todas as palavras-passe após concluir a configuração. Para obter os passos sobre como acionar uma sincronização completa de palavra-passe, consulte [acionar uma sincronização completa de todas as palavras-passe](active-directory-aadconnectsync-troubleshoot-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Se estiver numa compilação 1.0.9125 ou posterior, em seguida, o regular **completa sincronização** ação calcula também indica se devem ser sincronizadas palavras-passe e se este passo adicional já não é necessário.

Se **utilizador** objetos foram eliminados inadvertidamente no Azure AD devido a um erro de filtragem, pode recriar os objetos de utilizador no Azure AD, removendo as configurações de filtragem. Em seguida, pode sincronizar os diretórios novamente. Esta ação restaura os utilizadores a partir da Reciclagem no Azure AD. No entanto, não é possível anular eliminação outros tipos de objeto. Por exemplo, se elimina acidentalmente um grupo de segurança e foi utilizado para a ACL de um recurso, o grupo e a ACL não pode ser recuperado.

O Azure AD Connect elimina apenas objetos que considera-tem uma vez esteja no âmbito. Se não existem objetos no Azure AD que foram criados por outro motor de sincronização e estes objetos não estão no âmbito, a adição de filtragem não remove-los. Por exemplo, se iniciar com um servidor do DirSync que criou uma cópia completa de todo diretório no Azure AD e instalar um novo servidor de sincronização do Azure AD Connect em paralelo com filtragem ativada a partir do início, o Azure AD Connect não remove os objetos adicionais que são criados pelo DirSync.

A configuração de filtragem é mantida ao instalar ou atualizar para uma versão mais recente do Azure AD Connect. É sempre uma melhor prática, certifique-se de que a configuração não foi inadvertidamente alterada após uma atualização para uma versão mais recente antes de executar o ciclo de sincronização primeiro.

Se tiver mais do que uma floresta, tem de aplicar as configurações de filtragem que são descritas neste tópico para cada floresta (partindo do princípio de que pretende que a mesma configuração para todos eles).

### <a name="disable-the-scheduled-task"></a>Desative a tarefa agendada
Para desativar o programador incorporado que aciona um ciclo de sincronização a cada 30 minutos, siga estes passos:

1. Ir para um PowerShell linha de comandos.
2. Executar `Set-ADSyncScheduler -SyncCycleEnabled $False` para desativar o programador.
3. Efetue as alterações que estão documentadas neste artigo.
4. Executar `Set-ADSyncScheduler -SyncCycleEnabled $True` para ativar o Programador de novo.

**Se utilizar uma compilação do Azure AD Connect antes 1.1.105.0**  
Para desativar a tarefa agendada que aciona um ciclo de sincronização a cada três horas, siga estes passos:

1. Iniciar **Programador de tarefas** do **iniciar** menu.
2. Diretamente em **biblioteca do Programador de tarefas**, localize a tarefa com o nome **do Azure AD Sync Scheduler**, rato e selecione **desativar**.  
   ![Programador de tarefas](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. Agora pode efetuar alterações de configuração e executar o motor de sincronização manualmente a partir de **Synchronization Service Manager** consola.

Depois de concluir todas as alterações de filtragem, não se esqueça de voltar atrás e **ativar** novamente a tarefa.

## <a name="filtering-options"></a>Opções de filtragem
Pode aplicar os seguintes tipos de configuração de filtragem para a ferramenta de sincronização de diretórios:

* [**Com base no grupo**](#group-based-filtering): filtragem com base num único grupo só pode ser configurada na instalação inicial, utilizando o Assistente de instalação.
* [**Baseado em domínio**](#domain-based-filtering): ao utilizar esta opção, pode selecionar quais os domínios que sincronizar com o Azure AD. Também pode adicionar e remover domínios a configuração do motor de sincronização ao efetuar alterações à sua infraestrutura no local depois de instalar a sincronização do Azure AD Connect.
* [**Unidade organizacional (UO) – com base**](#organizational-unitbased-filtering): ao utilizar esta opção, pode selecionar qual UOs sincronizar com o Azure AD. Esta opção é para todos os tipos de objeto na UO selecionado.
* [**Baseado em atributos**](#attribute-based-filtering): ao utilizar esta opção, pode filtrar com base nos valores de atributo nos objetos de objetos. Também pode ter diferentes filtros para tipos de objeto diferentes.

Pode utilizar várias opções de filtragem ao mesmo tempo. Por exemplo, pode utilizar a filtragem baseada na UO para incluir apenas os objetos de uma UO. Ao mesmo tempo, pode utilizar a filtragem baseada no atributo para filtrar os objetos mais. Ao utilizar vários métodos de filtragem, os filtros de utilizam um "e" lógico entre os filtros.

## <a name="domain-based-filtering"></a>Filtragem baseada no domínio
Esta secção fornece os passos para configurar o filtro de domínio. Se adicionar ou remover domínios na floresta, depois de instalado o Azure AD Connect, também tem de atualizar a configuração de filtragem.

A forma preferencial para alterar a filtragem baseada em domínio é ao executar o Assistente de instalação e a alteração [domínio filtragem e de UOS](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). O Assistente de instalação automatiza as tarefas que estão documentadas neste tópico.

Só deve seguir estes passos, se não for possível executar o Assistente de instalação por algum motivo.

Configuração de filtragem baseada em domínio é composta por estes passos:

1. [Selecione os domínios](#select-domains-to-be-synchronized) que pretende incluir na sincronização.
2. Para cada adicionados e removidos domínio, ajuste o [perfis de execução](#update-run-profiles).
3. [Aplicar e verificar as alterações](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Selecione os domínios sejam sincronizados
Para definir o filtro de domínio, efetue os seguintes passos:

1. Inicie sessão no servidor que está a executar sincronização do Azure AD Connect, utilizando uma conta que seja um membro a **ADSyncAdmins** grupo de segurança.
2. Iniciar **serviço de sincronização** do **iniciar** menu.
3. Selecione **conectores**e o **conectores** lista, seleccione o conector com o tipo **serviços de domínio do Active Directory**. No **ações**, selecione **propriedades**.  
   ![Propriedades do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Clique em **configurar partições de diretório**.
5. No **selecionar partições de diretório** lista, selecione e anule a seleção de domínios conforme necessário. Certifique-se de que apenas as partições que pretende sincronizar estão selecionadas.  
   ![Partições](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
   Se tiver alterado a sua infraestrutura de Active Directory no local e adicionar ou remover domínios da floresta, em seguida, clique o **atualizar** botão para obter uma lista atualizada. Quando atualizar, está a pedido de credenciais. Forneça as credenciais com acesso de leitura para o Windows Server Active Directory. Não tem de ser o utilizador que é pré-preenchida na caixa de diálogo.  
   ![Atualização necessária](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Quando tiver terminado, feche o **propriedades** diálogo clicando **OK**. Se remover domínios da floresta, uma mensagem de pop-up indica que foi removido um domínio e que a configuração serão limpos.
7. Continuar para ajustar o [perfis de execução](#update-run-profiles).

### <a name="update-the-run-profiles"></a>Os perfis de execução de atualização
Se tiver atualizado o filtro de domínio, terá também de atualizar os perfis de execução.

1. No **conectores** lista, certifique-se de que o conector que alterou no passo anterior está selecionado. No **ações**, selecione **configurar perfis de execução**.  
   ![Conector 1 de perfis de execução](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  
2. Localizar e identificar os seguintes perfis de:
    * Importação completa
    * Sincronização completa
    * Importação delta
    * Sincronização delta
    * Exportar
3. Para cada perfil, ajuste o **adicionado** e **removido** domínios.
    1. Para cada um dos cinco perfis, efetue os seguintes passos para cada **adicionado** domínio:
        1. Selecione o perfil de execução e clique em **novo passo**.
        2. No **passo configurar** na página de **tipo** menu pendente, selecione o passo de tipo com o mesmo nome que o perfil que estiver a configurar. Clique depois em **Seguinte**.  
        ![Conector 2 de perfis de execução](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
        3. No **configuração do conector** na página de **partição** menu pendente, selecione o nome do domínio que tenha adicionado ao seu filtro de domínio.  
        ![Conector 3 de perfis de execução](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
        4. Para fechar o **configurar perfil de execução** caixa de diálogo, clique em **concluir**.
    2. Para cada um dos cinco perfis, efetue os seguintes passos para cada **removido** domínio:
        1. Selecione o perfil de execução.
        2. Se o **valor** do **partição** atributo é um GUID, selecione o passo de execução e clique em **eliminar passo**.  
        ![Conector 4 de perfis de execução](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  
    3. Certifique-se a sua alteração. Cada domínio que pretende sincronizar deve estar listado como um passo para cada perfil de execução.
4. Para fechar o **configurar perfis de execução** caixa de diálogo, clique em **OK**.
5.  Para concluir a configuração, tem de executar um **completa importação** e um **sincronização Delta**. Continuar a ler a secção [aplicar e verificar as alterações](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Baseado na unidade organizacional filtragem
A forma preferencial para alterar a filtragem baseada na UO é ao executar o Assistente de instalação e a alteração [domínio filtragem e de UOS](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). O Assistente de instalação automatiza as tarefas que estão documentadas neste tópico.

Só deve seguir estes passos, se não for possível executar o Assistente de instalação por algum motivo.

Para configurar baseado na unidade organizacional filtragem, efetue os seguintes passos:

1. Inicie sessão no servidor que está a executar sincronização do Azure AD Connect, utilizando uma conta que seja um membro a **ADSyncAdmins** grupo de segurança.
2. Iniciar **serviço de sincronização** do **iniciar** menu.
3. Selecione **conectores**e o **conectores** lista, seleccione o conector com o tipo **serviços de domínio do Active Directory**. No **ações**, selecione **propriedades**.  
   ![Propriedades do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Clique em **configurar partições de diretório**, selecione o domínio que pretende configurar e, em seguida, clique em **contentores**.
5. Quando lhe for pedido, forneça as credenciais com acesso de leitura para o Active Directory no local. Não tem de ser o utilizador que é pré-preenchida na caixa de diálogo.
6. No **selecionar contentores** diálogo caixa, desmarque as UOs que não pretende sincronizar com o diretório em nuvem e, em seguida, clique em **OK**.  
   ![UOs na caixa de diálogo Selecionar contentores](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
   * O **computadores** contentor deve estar selecionado para os computadores Windows 10 sejam sincronizados com êxito para o Azure AD. Se os computadores associados a domínios estão localizados em outros UOs, certifique-se que às estão selecionadas.
   * O contentor **ForeignSecurityPrincipals** deverá ser selecionado, se tiver várias florestas com confianças. Este contentor permite resolver a associação ao grupo de segurança de múltiplas florestas.
   * O **RegisteredDevices** UO deve ser selecionado se estiver ativada a funcionalidade de repetição de escrita do dispositivo. Se utilizar outra funcionalidade de repetição de escrita, tais como a repetição de escrita do grupo, certifique-se que estas localizações estão selecionadas.
   * Selecione qualquer outra UO onde estão localizados os utilizadores, iNetOrgPersons, grupos, contactos e computadores. Na imagem, todas as OUs estes estão localizadas na UO ManagedObjects.
   * Se utilizar a filtragem baseada no grupo, em seguida, na UO onde está localizado o grupo tem de ser incluída.
   * Tenha em atenção que pode configurar se o novo UOs que são adicionados depois de concluída a configuração de filtragem são sincronizados ou não sincronizados. Consulte a secção seguinte para obter mais detalhes.
7. Quando tiver terminado, feche o **propriedades** diálogo clicando **OK**.
8. Para concluir a configuração, tem de executar um **completa importação** e um **sincronização Delta**. Continuar a ler a secção [aplicar e verificar as alterações](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Sincronizar UOs novo
São sincronizados UOs novos que são criados após filtragem foi configurada por predefinição. Este estado é indicado por uma caixa de verificação selecionada. Também pode anular a seleção de algumas sub-UOs. Para obter este comportamento, clique na caixa até que fique branco com uma marca de verificação azul (estado predefinido). Em seguida, anule a seleção qualquer sub-UOs que não pretende sincronizar.

Se a todas as sub-UOs são sincronizados, a caixa está branca com uma marca de verificação azul.  
![UO com todas as caixas de selecionado](./media/active-directory-aadconnectsync-configure-filtering/ousyncnewall.png)

Se algum sub-UOs tem sido não seleccionados, a caixa está cinzento com uma marca de verificação branco.  
![UO com algumas sub-UOs desmarcada](./media/active-directory-aadconnectsync-configure-filtering/ousyncnew.png)

Com esta configuração, uma nova UO que foi criada em ManagedObjects está sincronizada.

O Assistente de instalação do Azure AD Connect cria sempre esta configuração.

### <a name="dont-synchronize-new-ous"></a>Não sincronizar UOs novo
Pode configurar o motor de sincronização para sincronizar não UOs novo após concluir a configuração de filtragem. Este estado é indicado na IU por caixa apresentação cinzento sólido com nenhuma marca de verificação. Para obter este comportamento, clique na caixa até que fique branco com nenhuma marca de verificação. Em seguida, selecione as sub-UOs que pretende sincronizar.

![UO com raiz desmarcada](./media/active-directory-aadconnectsync-configure-filtering/oudonotsyncnew.png)

Com esta configuração, uma nova UO que foi criada em ManagedObjects não está sincronizada.

## <a name="attribute-based-filtering"></a>Filtragem baseada no atributo
Certifique-se de que está a utilizar a Novembro de 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) ou posterior compilar para estes passos para trabalhar.

Filtragem baseada no atributo é a forma mais flexível para objetos de filtro. Pode utilizar a potência de [aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md) para controlar quase todos os aspetos de quando um objeto está sincronizado com o Azure AD.

Pode aplicar [entrada](#inbound-filtering) filtragem do Active Directory para o metaverso e [saída](#outbound-filtering) do metaverso de filtragem para o Azure AD. Recomendamos que aplique a filtragem de entrada uma vez que é o facilitar a manter. Só deve utilizar a filtragem de saída se de que é necessário para associar objetos a partir de mais do que uma floresta antes que pode ocorrer a avaliação.

### <a name="inbound-filtering"></a>Filtragem de entrada
Filtragem de entrada utiliza a configuração predefinida, onde objetos avançar para o Azure AD tem de ter o cloudFiltered de atributo do metaverso não definido para um valor para ser sincronizados. Se o valor deste atributo é definido como **verdadeiro**, em seguida, o objeto não está sincronizado. Não deve ser definido como **falso**, por predefinição. Certifique-se outras regras tem a capacidade para contribuir com um valor, este atributo só deveria para ter os valores **verdadeiro** ou **nulo** (ausente).

Filtragem de entrada, utiliza o poder dos **âmbito** para determinar quais os objetos a sincronizar ou não sincronizar. Este é onde efetuar ajustes para ajustar os requisitos da sua própria organização. O módulo de âmbito tem um **grupo** e um **cláusula** para determinar quando uma regra de sincronização está no âmbito. Um grupo contém um ou vários cláusulas. Não há um "e" lógico entre múltiplas cláusulas e "Ou" entre vários grupos.

Observe informe-num exemplo:  
![Âmbito](./media/active-directory-aadconnectsync-configure-filtering/scope.png)  
Isto deve ser lido como **(departamento = IT) ou (departamento = Vendas e c = US)**.

Os seguintes exemplos e passos, utilize o objeto de utilizador como exemplo, mas pode utilizá-lo para todos os tipos de objeto.

Nos exemplos seguintes, o valor de precedência começa com 50. Isto pode ser qualquer número não utilizado, mas deve ser inferior a 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Filtragem negativo: "não sincronizar estas"
No exemplo seguinte, filtra (não sincronizar) todos os utilizadores onde **extensionAttribute15** tem o valor **NoSync**.

1. Inicie sessão no servidor que está a executar sincronização do Azure AD Connect, utilizando uma conta que seja um membro a **ADSyncAdmins** grupo de segurança.
2. Iniciar **Editor de regras de sincronização** do **iniciar** menu.
3. Certifique-se **entrada** está selecionada e clique em **Adicionar nova regra**.
4. Dê um nome descritivo, a regra, tal como "*do AD – utilizador DoNotSyncFilter*". Selecione uma floresta correta, selecione **utilizador** como o **tipo de objeto CS**e selecione **pessoa** como o **tipo de objeto de MV**. No **tipo de ligação**, selecione **associar**. No **precedência**, escreva um valor que não é atualmente utilizado por outra regra de sincronização (por exemplo, 50) e, em seguida, clique em **seguinte**.  
   ![1 Descrição de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. No **Scoping filtro**, clique em **adicionar grupo**e clique em **Adicionar cláusula**. No **atributo**, selecione **ExtensionAttribute15**. Certifique-se de que **operador** está definido como **igual**e escreva o valor **NoSync** no **valor** caixa. Clique em **Seguinte**.  
   ![2 âmbito de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Deixe o **associar** regras vazio e, em seguida, clique em **seguinte**.
7. Clique em **adicionar transformação**, selecione o **FlowType** como **constante**e selecione **cloudFiltered** como o **destino Atributo**. No **origem** caixa de texto, escreva **verdadeiro**. Clique em **adicionar** para guardar a regra.  
   ![Transformação 3 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Para concluir a configuração, tem de executar um **sincronização completa**. Continuar a ler a secção [aplicar e verificar as alterações](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Filtragem positivo: "apenas sincronizar estas"
Expressa filtragem positivo pode ser mais complicada porque tem também a ter em consideração os objetos que não são óbvios sejam sincronizados, tais como gabinetes de conferência. Também vai substituir o filtro predefinido a regra de out-of-box **do AD - associar utilizador**. Quando criar o seu filtro personalizado, certifique-se por não incluir objetos de sistema cruciais, objetos de conflito de replicação, especiais caixas de correio e as contas de serviço do Azure AD Connect.

A opção de filtragem positiva requer duas regras de sincronização. Precisa de uma regra (ou vários) com o âmbito correto de objetos a sincronizar. Também precisa de uma segunda regra de sincronização de catch-all, que filtra todos os objetos que ainda não tenham sido identificados como um objeto que deve ser sincronizado.

No exemplo seguinte, sincronizar apenas os objetos de utilizador em que o atributo departamento tem o valor **vendas**.

1. Inicie sessão no servidor que está a executar sincronização do Azure AD Connect, utilizando uma conta que seja um membro a **ADSyncAdmins** grupo de segurança.
2. Iniciar **Editor de regras de sincronização** do **iniciar** menu.
3. Certifique-se **entrada** está selecionada e clique em **Adicionar nova regra**.
4. Dê um nome descritivo, a regra, tal como "*do AD – utilizador vendas sincronizar*". Selecione uma floresta correta, selecione **utilizador** como o **tipo de objeto CS**e selecione **pessoa** como o **tipo de objeto de MV**. No **tipo de ligação**, selecione **associar**. No **precedência**, escreva um valor que não é atualmente utilizado por outra regra de sincronização (por exemplo 51) e, em seguida, clique em **seguinte**.  
   ![Descrição de 4 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. No **Scoping filtro**, clique em **adicionar grupo**e clique em **Adicionar cláusula**. No **atributo**, selecione **departamento**. Certifique-se de que o operador está definido como **igual**e escreva o valor **vendas** no **valor** caixa. Clique em **Seguinte**.  
   ![5 âmbito de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Deixe o **associar** regras vazio e, em seguida, clique em **seguinte**.
7. Clique em **adicionar transformação**, selecione **constante** como o **FlowType**e selecione o **cloudFiltered** como o **destino Atributo**. No **origem** caixa, escreva **falso**. Clique em **adicionar** para guardar a regra.  
   ![Transformação 6 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
   Este é um caso especial onde definir explicitamente cloudFiltered para **falso**.
8. Agora que poderemos ter de criar a regra de sincronização de catch-all. Dê um nome descritivo, a regra, tal como "*do AD – filtro de utilizador Catch-all*". Selecione uma floresta correta, selecione **utilizador** como o **tipo de objeto CS**e selecione **pessoa** como o **tipo de objeto de MV**. No **tipo de ligação**, selecione **associar**. No **precedência**, escreva um valor que não é atualmente utilizado por outra regra de sincronização (por exemplo 99). Selecionou um valor de prioridade superior (uma precedência inferior às) a regra de sincronização anterior. Mas também ter deixado algum espaço para que possa adicionar regras de sincronização de filtragem mais mais tarde quando pretender iniciá-la departamentos adicionais. Clique em **Seguinte**.  
   ![Descrição do 7 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. Deixe **Scoping filtro** vazio e clique em **seguinte**. Um filtro em branco indica que a regra é aplicada a todos os objetos.
10. Deixe o **associar** regras vazio e, em seguida, clique em **seguinte**.
11. Clique em **adicionar transformação**, selecione **constante** como o **FlowType**e selecione **cloudFiltered** como o **destino Atributo**. No **origem** caixa, escreva **verdadeiro**. Clique em **adicionar** para guardar a regra.  
    ![Transformação 3 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. Para concluir a configuração, tem de executar um **sincronização completa**. Continuar a ler a secção [aplicar e verificar as alterações](#apply-and-verify-changes).

Se for necessário, pode criar regras mais do primeiro tipo onde inclui mais objetos na sincronização.

### <a name="outbound-filtering"></a>Filtragem de saída
Em alguns casos, é necessário efetuar a filtragem apenas depois de associar os objetos no metaverso. Por exemplo, poderá ser necessário ver o atributo de correio da floresta de recursos e o atributo userPrincipalName da floresta de contas, para determinar se um objeto deve ser sincronizado. Nestes casos, pode criar os filtros na regra de saída.

Neste exemplo, altere a filtragem de modo a que apenas os utilizadores que tenham os correio eletrónico e userPrincipalName que termine em @contoso.com são sincronizadas:

1. Inicie sessão no servidor que está a executar sincronização do Azure AD Connect, utilizando uma conta que seja um membro a **ADSyncAdmins** grupo de segurança.
2. Iniciar **Editor de regras de sincronização** do **iniciar** menu.
3. Em **tipo regras**, clique em **saída**.
4. Dependendo da versão do Connect utilizar, encontrar uma regra com o nome **Out para o AAD – associar utilizador** ou **enviados para o AAD - utilizador aderir ao SOAInAD**e clique em **editar**.
5. No pop-up, responder **Sim** para criar uma cópia da regra.
6. No **Descrição** página, altere **precedência** para um valor de não utilizado, tais como 50.
7. Clique em **Scoping filtro** na navegação esquerda e, em seguida, clique **Adicionar cláusula**. No **atributo**, selecione **correio**. No **operador**, selecione **ENDSWITH**. No **valor**, tipo  **@contoso.com** e, em seguida, clique em **Adicionar cláusula**. No **atributo**, selecione **userPrincipalName**. No **operador**, selecione **ENDSWITH**. No **valor**, tipo  **@contoso.com** .
8. Clique em **Guardar**.
9. Para concluir a configuração, tem de executar um **sincronização completa**. Continuar a ler a secção [aplicar e verificar as alterações](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Aplicar e verificar as alterações
Depois de efetuadas as alterações de configuração, tem de as aplicar aos objetos que já estejam presentes no sistema. Também pode ser que os objetos que não são atualmente no motor de sincronização devem ser processados (e o motor de sincronização tem de ler o sistema de origem novamente para verificar o respetivo conteúdo).

Se alterou a configuração utilizando **domínio** ou **unidade organizacional** filtragem, em seguida, precisa de fazer um **completa importação**, seguido **Delta sincronização**.

Se alterou a configuração utilizando **atributo** filtragem, em seguida, precisa de fazer um **completa sincronização**.

Efetue os seguintes passos:

1. Iniciar **serviço de sincronização** do **iniciar** menu.
2. Selecione **conectores**. No **conectores** lista, seleccione o conector em que efetuou uma configuração alterar anterior. No **ações**, selecione **executar**.  
   ![Conector executar](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. No **perfis de execução**, selecione a operação que foi mencionada na secção anterior. Se precisar de executar duas ações, execute o segundo após a conclusão da primeiro. (O **estado** coluna é **inativo** para o conector selecionado.)

Após a sincronização, todas as alterações são testadas para ser exportada. Antes de efetuar, na verdade, as alterações no Azure AD, que pretende Certifique-se de que todos os estas alterações estão corretas.

1. Inicie uma linha de comandos e aceda a `%Program Files%\Microsoft Azure AD Sync\bin`.
2. Execute `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   O nome do conector está no serviço de sincronização. Tem um nome semelhante a "contoso.com – AAD" para o Azure AD.
3. Execute `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Tem agora um ficheiro no % temp % denominado export.csv que pode ser reduzida no Microsoft Excel. Este ficheiro contém todas as alterações que estão prestes a ser exportado.
5. Efetue as alterações necessárias para os dados ou a configuração e executar estes passos novamente (importar, sincronizar e verifique) até que as alterações que estão prestes a ser exportado o que esperar.

Quando estiver satisfeito, exporte as alterações para o Azure AD.

1. Selecione **conectores**. No **conectores** lista, seleccione o conector do Azure AD. No **ações**, selecione **executar**.
2. No **perfis de execução**, selecione **exportar**.
3. Se as alterações de configuração eliminam muitos objetos, em seguida, verá um erro na exportação quando o número é superior ao limiar configurado (por predefinição 500). Se vir este erro, em seguida, terá de desativar temporariamente o "[impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)" funcionalidade.

Agora está na altura de ativar o Programador de novo.

1. Iniciar **Programador de tarefas** do **iniciar** menu.
2. Diretamente em **biblioteca do Programador de tarefas**, localize a tarefa com o nome **do Azure AD Sync Scheduler**, rato e selecione **ativar**.

## <a name="group-based-filtering"></a>Filtragem baseada no grupo
Pode configurar a com base no grupo de filtragem na primeira vez que instalar o Azure AD Connect utilizando [instalação personalizada](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups). Destina-se para uma implementação piloto onde pretende que apenas um pequeno conjunto de objetos a ser sincronizados. Ao desativar a filtragem baseada no grupo, não pode ser ativada novamente. Tem *não suportado* para utilizar com base no grupo de filtragem de uma configuração personalizada. Só é suportada para configurar esta funcionalidade utilizando o Assistente de instalação. Quando tiver concluído o piloto, em seguida, utilize uma das outras opções de filtragem neste tópico. Quando utilizar a filtragem baseada no UO em conjunto com a filtragem baseada no grupo, tem de ser incluída OU(s) onde sejam encontram o grupo e os seus membros.

Quando efetuar a sincronização de várias florestas do AD, pode configurar com base no grupo de filtragem ao especificar um grupo diferente para cada conector do AD. Se desejar para sincronizar um utilizador no AD de uma floresta e o mesmo utilizador tem um ou mais correspondente objetos noutras florestas do AD, tem de garantir que o objeto de utilizador e todos os respetivos objetos correspondentes são dentro baseado no grupo de filtragem de âmbito. Para obter exemplos:

* Tem um utilizador numa floresta que tenha um objeto FSP (Principal de segurança externa) correspondente noutra floresta. Ambos os objetos tem de ser dentro baseado no grupo de filtragem de âmbito. Caso contrário, o utilizador não será sincronizado para o Azure AD.

* Tem um utilizador numa floresta que tenha uma conta de recurso correspondente (por exemplo, caixa de correio ligada) noutra floresta. Além disso, ter configurado o Azure AD Connect, para associar o utilizador com a conta de recurso. Ambos os objetos tem de ser dentro baseado no grupo de filtragem de âmbito. Caso contrário, o utilizador não será sincronizado para o Azure AD.

* Tem um utilizador numa floresta que tenha um correio correspondente contacto noutra floresta. Além disso, ter configurado o Azure AD Connect, para associar o utilizador com o contacto de correio. Ambos os objetos tem de ser dentro baseado no grupo de filtragem de âmbito. Caso contrário, o utilizador não será sincronizado para o Azure AD.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) configuração.
- Saiba mais sobre [integrar as identidades no local com o Azure AD](active-directory-aadconnect.md).
