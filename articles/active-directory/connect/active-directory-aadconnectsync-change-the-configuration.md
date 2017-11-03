---
title: "Sincronização do Azure AD Connect: efetuar uma alteração na sincronização do Azure AD Connect de configuração | Microsoft Docs"
description: "Explica como efetuar uma alteração à configuração na sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 63a7ae9d39e1a74294637172efd607ee41b2d69b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Sincronização do Azure AD Connect: como efetuar uma alteração para a configuração predefinida
O objetivo deste tópico é guiá-lo como efetuar alterações à configuração de predefinição na sincronização do Azure AD Connect. Fornece os passos para alguns cenários comuns. Com este conhecimento, deverá conseguir efetuar algumas alterações simples à sua própria configuração com base nas suas regras de negócio.

## <a name="synchronization-rules-editor"></a>Editor de regras de sincronização
O Editor de regras de sincronização é utilizado para ver e alterar a configuração predefinida. Pode encontrá-lo no Menu Iniciar sob o **do Azure AD Connect** grupo.  
![Menu Iniciar com o Editor de regras de sincronização](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Quando abri-lo, pode ver as regras de out-of-box predefinidas.

![Editor de regras de sincronização](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navegar no editor
As listas pendentes na parte superior do editor permitem-lhe localizar rapidamente uma regra específica. Por exemplo, se pretender ver as regras em que está incluído o atributo proxyAddresses, alteraria listas pendentes para o seguinte:  
![Filtragem de SRE](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Para repor a filtragem e carregar uma configuração nova, prima **F5** no teclado.

Para a canto superior direito, tem um botão **Adicionar nova regra**. Este botão é utilizado para criar as suas próprias regras personalizadas.

Na parte inferior, tiver botões para agir de uma regra de sincronização selecionada. **Editar** e **eliminar** fazer o que esperar-lhes. **Exportar** produz um script do PowerShell para recriar a regra de sincronização. Este procedimento permite-lhe mover uma regra de sincronização de um servidor para outro.

## <a name="create-your-first-custom-rule"></a>Criar a primeira regra personalizada
A alteração mais comuns é alterações aos fluxos de atributos. Os dados no seu diretório de origem não podem ser como no Azure AD. O exemplo nesta secção, deve certificar-se de que o nome dado do utilizador é sempre em **maiúsculas e minúsculas adequada**.

### <a name="disable-the-scheduler"></a>Desativar o programador
O [programador](active-directory-aadconnectsync-feature-scheduler.md) executa cada 30 minutos por predefinição. Pretende Certifique-se de que não está a iniciar enquanto estiver a efetuar alterações e resolver problemas relacionados com as regras de novo. Para desativar temporariamente o programador, inicie o PowerShell e execute`Set-ADSyncScheduler -SyncCycleEnabled $false`

![Desativar o programador](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Criar a regra
1. Clique em **Adicionar nova regra**.
2. No **Descrição** página, introduza o seguinte:  
   ![Regra de filtragem de entrada](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * Nome: Atribua a regra, um nome descritivo.
   * Descrição: Alguns esclarecimentos para que outra pessoa possa compreender que a regra destina-se.
   * Ligado sistema: o objeto pode ser encontrado no sistema. Neste caso, selecione o conector do Active Directory.
   * Tipo de objeto de sistema/Metaverso ligado: Selecione **utilizador** e **pessoa** respetivamente.
   * Tipo de ligação: Este valor para alterar o **associar**.
   * Precedência: Forneça um valor que seja exclusivo no sistema. Um valor numérico inferior indica precedência superior.
   * Etiqueta: Deixe em branco. Apenas as regras de out-of-box da Microsoft devem ter esta caixa preenchida com um valor.
3. No **Scoping filtro** página, introduza **givenName ISNOTNULL**.  
   ![Regra de filtro de âmbito de entrada](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   Esta secção é utilizada para definir que objetos que a regra deve ser aplicadas. Se deixado em branco, a regra seria aplicada a todos os objetos de utilizador. Mas, que inclui gabinetes de conferências, contas de serviço e outros objetos de utilizador não pessoas.
4. No **associar regras**, deixe em branco.
5. No **transformações** página, altere o FlowType para **expressão**. Selecione o atributo de destino **givenName**e na origem introduza `PCase([givenName])`.
   ![Transformações de regra de entrada](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   O motor de sincronização é maiúsculas e minúsculas no nome de função e o nome do atributo. Se escrever um problema, ver um aviso quando adicionar a regra. O editor permite-lhe guardar e continuar, por isso terá de reabra a regra e corrija a regra.
6. Clique em **adicionar** para guardar a regra.

A nova regra personalizada deve estar visível com outras regras de sincronização no sistema.

### <a name="verify-the-change"></a>Certifique-se a alteração
Com esta nova alteração, deve certificar-se de que está a funcionar conforme esperado e não está a gerar erros. Dependendo do número de objetos que tiver, existem duas formas diferentes para efetuar este passo.

1. Execute uma sincronização completa em todos os objetos
2. Executar uma pré-visualização e a sincronização completa num único objeto

Iniciar **serviço de sincronização** no menu Iniciar. Os passos nesta secção estão todos nesta ferramenta.

1. **Sincronização completa no todos os objetos**  
   Selecione **conectores** na parte superior. Identifique o conector que efetuou uma alteração na secção anterior, neste caso, os serviços de domínio do Active Directory, e selecione-o. Selecione **executar** de ações e selecione **sincronização completa** e **OK**.
   ![Sincronização completa](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   Agora, os objetos são atualizados no metaverso. Agora pretende ver o objeto no metaverso.
2. **Pré-visualização e a sincronização completa num único objeto**  
   Selecione **conectores** na parte superior. Identifique o conector que efetuou uma alteração na secção anterior, neste caso, os serviços de domínio do Active Directory, e selecione-o. Selecione **procurar espaço de conector**. Utilize o âmbito para localizar um objeto que pretende utilizar para testar a alteração. Selecione o objeto e clique em **pré-visualização**. No novo ecrã, selecione **consolidar pré-visualização**.  
   ![Consolidar pré-visualização](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   A alteração é consolidada agora para o metaverso.

**Observe o objeto de metaverso**  
Agora pretende escolher alguns objetos de exemplo para se certificar de que o valor é esperado e de que a regra é aplicada. Selecione **pesquisa de Metaverso** do nível superior. Adicione qualquer filtro que tem de localizar os objetos relevantes. O resultado da pesquisa, abra um objeto. Observe os valores de atributo e também verificar no **regras de sincronização** coluna que a regra aplicada como esperado.  
![Pesquisa de metaversos](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Ativar o programador
Se tudo está conforme esperado, pode ativar o Programador de novo. A partir do PowerShell, execute `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Outras alterações de fluxo de atributos comuns
A secção anterior descrito como efetuar alterações a um fluxo de atributos. Nesta secção, são fornecidos alguns exemplos adicionais. Os passos sobre como criar a regra de sincronização é abreviada, mas pode encontrar os passos completos na secção anterior.

### <a name="use-another-attribute-than-the-default"></a>Utilize outro atributo que não o predefinido
Na Fabrikam, há uma floresta onde alfabeto local é utilizado para o nome especificado, o apelido e o nome a apresentar. A representação de carateres latinos destes atributos pode ser encontrada em atributos de extensão. Quando criar a lista de endereços global no Azure AD e do Office 365, a organização pretende estes atributos a ser utilizado em vez disso.

Com uma configuração predefinida, um objeto da floresta local este aspeto:  
![Fluxo de atributos 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Para criar uma regra com outros fluxos de atributos, efetue o seguinte:

* Iniciar **Editor de regras de sincronização** no menu Iniciar.
* Com **entrada** ainda selecionada para a esquerda, clique no botão **Adicionar nova regra**.
* Atribua a regra, um nome e descrição. Selecione o Active Directory no local e os tipos de objetos relevantes. No **tipo de ligação**, selecione **associar**. Para precedência, escolha um número que não seja utilizado por outra regra. As regras de out-of-box começar com 100, pelo que o valor de 50 pode ser utilizado neste exemplo.
  ![Fluxo de atributos 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
* Deixe o âmbito em branco (ou seja, deve ser aplicada a todos os objetos de utilizador na floresta).
* Deixe as regras de associação em branco (ou seja, permitem a regra de out-of-box processar todas as associações).
* Transformações, crie os fluxos do seguintes:  
  ![Fluxo de atributos 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
* Clique em **adicionar** para guardar a regra.
* Aceda a **Gestor do serviço de sincronização**. No **conectores**, seleccione o conector em que adicionámos a regra. Selecione **executar**, e **completa sincronização**. Uma sincronização completa recalcula todos os objetos a utilizar as regras atuais.

Este é o resultado para o mesmo objeto com esta regra personalizada:  
![Fluxo de atributos 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Comprimento de atributos
Atributos de cadeia são por predefinição, configurada para ser indexable e o comprimento máximo é de 448 caracteres. Se estiver a trabalhar com atributos de cadeia que podem conter mais, em seguida, certifique-se incluir o seguinte no fluxo de atributos:  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>Alterar o userPrincipalSuffix
O atributo userPrincipalName no Active Directory não é conhecido sempre pelos utilizadores e não pode ser adequado como o ID de início de sessão. O Azure AD Connect Assistente de instalação de sincronização permite escolher um atributo diferente, por exemplo correio. Mas, em alguns casos tem de ser calculado o atributo. Por exemplo, a empresa Contoso tem dois diretórios do Azure AD, uma para produção e outro para testes. Que pretende que os utilizadores no seu inquilino de teste para utilizar outro sufixo o ID de início de sessão.  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

Nesta expressão demorar tudo esquerdo do primeiro @-sign (Word) e concatenate com uma cadeia de caracteres fixa.

### <a name="convert-a-multi-value-to-a-single-value"></a>Converter um valor multi um valor único
Alguns atributos no Active Directory são com múltiplos valores no esquema, apesar de procuram único valor no Active Directory de diretório de utilizadores e computadores. Um exemplo é o atributo de descrição.  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

Nesta expressão no caso do atributo tem um valor, colocar o primeiro item (Item) no atributo, remover espaços (Trim) à esquerda e e, em seguida, mantenha os primeiro 448 caracteres (esquerda) na cadeia de.

### <a name="do-not-flow-an-attribute"></a>Fluxo não de um atributo
Para em segundo plano no cenário nesta secção, consulte [controlar o processo de fluxo de atributos](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Existem duas formas de fluxo não de um atributo. O primeiro está disponível no Assistente de instalação e permite-lhe [remover atributos selecionados](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Esta opção funciona se nunca tiver sincronizado o atributo antes. No entanto, se tiver iniciado para sincronizar este atributo e removê-lo mais tarde com esta funcionalidade, a deixa de motor de sincronização gerir o atributo e os valores existentes é mantida no Azure AD.

Se pretender remover o valor de um atributo e certifique-se de não ser fluxo no futuro, tem de criar uma regra personalizada em vez disso.

Na Fabrikam, ter Observámos que alguns dos atributos que sincronizar com a nuvem não devem ser não existe. Queremos certificar-se de que estes atributos são removidos do Azure AD.  
![Atributos de extensão incorreta](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

* Criar uma nova regra de sincronização de entrada e preencher a descrição ![descrições](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
* Criar fluxos de atributos do tipo **expressão** e com a origem **AuthoritativeNull**. O literal **AuthoritativeNull** indica que o valor deve estar em branco de MV, mesmo se uma regra de sincronização de precedência inferior tenta povoar o valor.
  ![Transformação para atributos de extensão](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
* Guarde a regra de sincronização. Iniciar **serviço de sincronização**, localize o conector, selecione **executar**, e **sincronização completa**. Este passo recalcula todos os fluxos de atributos.
* Certifique-se de que as alterações pretendidas estão prestes a ser exportado ao pesquisar o espaço de conector.
  ![Eliminar faseada](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Criar regras com o PowerShell
Utilizando o editor de regra de sincronização funciona bem quando só tem de fazer algumas alterações. Se precisar de efetuar alterações muitos, PowerShell pode ser uma opção melhor. Algumas funcionalidades avançadas só estão disponíveis com o PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Obter o script do PowerShell para uma regra de out-of-box
Para ver o script que criou uma regra de out-of-box, selecione a regra no editor de regras de sincronização e clique em do PowerShell **exportar**. Esta ação dá-lhe o script do PowerShell que criou a regra.

### <a name="advanced-precedence"></a>Precedência avançada
As regras de sincronização de out-of-box comecem com um valor de precedência de 100. Se tiver várias florestas, e terá de efetuar várias alterações personalizadas, em seguida, as regras de sincronização de 99 poderão não ser suficiente.

Pode instruir o motor de sincronização que pretende que as regras adicionais inseridas antes das regras de out-of-box. Para obter este comportamento, siga estes passos:

1. Marcar a primeira regra de sincronização de out-of-box (esta regra é o **da associação do utilizador do AD**) no editor de regras de sincronização e selecione **exportar**. Copie o valor do identificador de SR.  
![PowerShell antes de alteração](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. Crie a nova regra de sincronização. Pode utilizar o editor de regras de sincronização para criá-la. Exporte a regra para um script do PowerShell.
3. Na propriedade **PrecedenceBefore**, inserir o valor do identificador da regra de out-of-box. Definir o **precedência** para **0**. Certifique-se de que o atributo de identificador é exclusivo e não está a reutilizar um GUID de outra regra. Certifique-se também que o **ImmutableTag** propriedade não está definida; esta propriedade só deve ser definida para uma regra de out-of-box. Guarde o script do PowerShell e executá-la. O resultado é que a regra personalizada é atribuída o valor de precedência de 100 e todas as outras regras de out-of-box são incrementadas.  
![PowerShell após a alteração](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

Pode ter várias regras de sincronização personalizados com o mesmo **PrecedenceBefore** valor quando necessário.


## <a name="enable-synchronization-of-preferreddatalocation"></a>Ativar a sincronização de PreferredDataLocation
Azure AD Connect suporta a sincronização do **PreferredDataLocation** atributo para **utilizador** objetos na versão 1.1.524.0 e depois. Mais especificamente, as seguintes alterações foram introduzidas:

* O esquema do tipo de objeto **utilizador** no conector Azure AD é expandido para incluir o atributo de PreferredDataLocation, o que é do tipo cadeia e é o valor único.

* O esquema do tipo de objeto **pessoa** no Metaverso é expandido para incluir o atributo de PreferredDataLocation, o que é do tipo cadeia e é o valor único.

Por predefinição, o atributo PreferredDataLocation não está ativado para a sincronização porque não existe nenhum atributo PreferredDataLocation correspondente no Active Directory no local. Tem de ativar manualmente sincronização.

> [!IMPORTANT]
> Atualmente, Azure AD permite que o atributo PreferredDataLocation no sincronizados objetos de utilizador e nuvem utilizador objetos diretamente a ser configurado com o Azure AD PowerShell. Assim que tiver ativado a sincronização do atributo PreferredDataLocation, tem de parar a utilizar o Azure AD PowerShell para configurar o atributo em **sincronizados objetos de utilizador** como o Azure AD Connect irá substitui-los com base nos valores de atributo de origem no Active Directory no local.

> [!IMPORTANT]
> Em 1 de Setembro de 2017, do Azure AD já não permitirá o atributo PreferredDataLocation no **sincronizados objetos de utilizador** diretamente seja configurado com o Azure AD PowerShell. Para configurar o atributo PreferredLocation em objetos de utilizador sincronizados, tem de utilizar o Azure AD Connect apenas.

Antes de ativar a sincronização do atributo PreferredDataLocation, tem de:

 * Primeiro, tem de decidir que atributo do Active Directory no local para ser utilizado como o atributo de origem. Deve ser do tipo **cadeia** e é **valor único**.

 * Se configurou anteriormente o atributo PreferredDataLocation no existente sincronizados objetos de utilizador no Azure AD com o Azure AD PowerShell, tem **backport** os valores de atributo para os objetos de utilizador correspondentes no Active Directory no local.
 
    > [!IMPORTANT]
    > Se o fizer não backport os valores de atributo para os objetos de utilizador correspondentes no Active Directory no local, o Azure AD Connect irá remover os valores de atributo existentes no Azure AD quando a sincronização para o atributo PreferredDataLocation está ativada.

 * É recomendável que configure o atributo de origem em, pelo menos, duas no local utilizador AD objetos agora, que pode ser utilizado para a verificar mais tarde.
 
Os passos para ativar a sincronização do atributo PreferredDataLocation podem ser resumidos como:

1. Desativar o agendador de sincronização e certifique-se de que existe, está sem sincronização em curso

2. Adicione o atributo de origem para o local esquema conector AD

3. Adicionar PreferredDataLocation ao esquema do conector do Azure AD

4. Criar uma regra de sincronização de entrada para passar o valor do atributo do Active Directory no local

5. Criar uma regra de sincronização de saída para passar o valor do atributo com o Azure AD

6. Execute o ciclo de sincronização completa

7. Ativar o agendador de sincronização

> [!NOTE]
> O resto desta secção aborda estes passos nos detalhes. Estas são descritas no contexto de uma implementação do Azure AD com topologia de floresta única e sem as regras de sincronização personalizados. Se tiver uma topologia de várias floresta, as regras de sincronização personalizados configurados ou tem um servidor de teste, terá de ajustar os passos em conformidade.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passo 1: Desativar do agendador de sincronização e certifique-se de que existe, está sem sincronização em curso
Certifique-se de que não sincronização ocorre enquanto estiver a atualizar as regras de sincronização para evitar alterações indesejadas que está a ser exportadas para o Azure AD. Para desativar o agendador de sincronização incorporada:

 1. Inicie a sessão do PowerShell no servidor do Azure AD Connect.

 2. Desative a sincronização agendada, executando o cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $false`
 
 3. Iniciar o **Synchronization Service Manager** acedendo ao serviço de sincronização inicial →.
 
 4. Vá para o **operações** separador e confirme que não há nenhuma operação cujo estado é *"em curso."*

![Verificação de Gestor do serviço de sincronização - não existem operações em curso](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step1.png)

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Passo 2: Adicionar o atributo de origem para o local esquema conector AD
Nem todos os atributos de AD são importados para o local espaço de conector do AD. Para adicionar o atributo de origem para a lista dos atributos importados:

 1. Vá para o **conectores** separador o Synchronization Service Manager.
 
 2. Clique com o botão direito no **conector AD no local** e selecione **propriedades**.
 
 3. Na caixa de diálogo de pop-up, vá para o **selecionar atributos** separador.
 
 4. Certifique-se de que o atributo de origem está selecionado na lista de atributos.
 
 5. Clique em **OK** para guardar.

![Adicione o atributo de origem para o local esquema conector AD](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step2.png)

### <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Passo 3: Adicionar PreferredDataLocation ao esquema do conector do Azure AD
Por predefinição, o atributo PreferredDataLocation não é importado para o Azure AD Connect espaço em. Para adicionar o atributo PreferredDataLocation à lista de atributos importados:

 1. Vá para o **conectores** separador o Synchronization Service Manager.

 2. Clique com o botão direito no **conector do Azure AD** e selecione **propriedades**.

 3. Na caixa de diálogo de pop-up, vá para o **selecionar atributos** separador.

 4. Certifique-se de que o atributo PreferredDataLocation está selecionado na lista de atributos.

 5. Clique em **OK** para guardar.

![Adicione o atributo de origem para o esquema do conector do Azure AD](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step3.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Passo 4: Criar uma regra de sincronização de entrada para passar o valor do atributo do Active Directory no local
A regra de sincronização de entrada permite que o valor de atributo para o fluxo a partir do atributo de origem do Active Directory no local para o Metaverso:

1. Iniciar o **Editor de regras de sincronização** acedendo ao Editor de regras de sincronização inicial →.

2. Definir o filtro de pesquisa **direção** ser **entrada**.

3. Clique em **Adicionar nova regra** botão para criar uma regra de entrada novo.

4. Sob o **Descrição** separador, forneça a seguinte configuração:
 
    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | Nome | *Forneça um nome* | Por exemplo, *"do AD – utilizador PreferredDataLocation"* |
    | Descrição | *Forneça uma descrição* |  |
    | Sistema ligado | *Escolha no local conector AD* |  |
    | Tipo de objeto de sistema ligado | **Utilizador** |  |
    | Tipo de objeto de Metaverso | **Pessoa** |  |
    | Tipo de ligação | **Associar** |  |
    | Precedência | *Escolha um número entre 1 a 99* | 1 a 99 está reservado para regras de sincronização personalizados. Não escolha um valor que é utilizado por outra regra de sincronização. |

5. Vá para o **Scoping filtro** separador e adicione um **único grupo de filtro de âmbito com a cláusula seguinte**:
 
    | Atributo | operador | Valor |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Utilizador\_ | 
 
    Filtro de âmbito determina qual no local AD objetos que esta regra de sincronização de entrada é aplicada a. Neste exemplo, utilizamos o mesmo filtro de âmbito utilizado como *"do AD – comum do utilizador"* regra de sincronização de OOB, o que impede que a regra de sincronização que está a ser aplicadas a objetos de utilizador criados através da funcionalidade de repetição de escrita de utilizador do Azure AD. Poderá ter de otimizar o filtro de âmbito, de acordo com a implementação do Azure AD Connect.

6. Vá para o **separador de transformação** e implementar a seguinte regra de transformação:
 
    | Tipo de fluxo | Atributo de destino | Origem | Aplicar uma vez | Tipo de intercalação |
    | --- | --- | --- | --- | --- |
    | Direto | PreferredDataLocation | Escolha o atributo de origem | Desmarcada | Atualizar |

7. Clique em **adicionar** para criar a regra de entrada.

![Criar regra de sincronização de entrada](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step4.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Passo 5: Criar uma regra de sincronização de saída para passar o valor do atributo com o Azure AD
A regra de sincronização de saída permite que o valor de atributo para o fluxo do Metaverso ao atributo PreferredDataLocation no Azure AD:

1. Vá para o **as regras de sincronização** Editor.

2. Definir o filtro de pesquisa **direção** ser **saída**.

3. Clique em **Adicionar nova regra** botão.

4. Sob o **Descrição** separador, forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | Nome | *Forneça um nome* | Por exemplo, "enviados ao AAD – utilizador PreferredDataLocation" |
    | Descrição | *Forneça uma descrição* |
    | Sistema ligado | *Seleccione o conector AAD* |
    | Tipo de objeto de sistema ligado | Utilizador ||
    | Tipo de objeto de Metaverso | **Pessoa** ||
    | Tipo de ligação | **Associar** ||
    | Precedência | *Escolha um número entre 1 a 99* | 1 a 99 está reservado para regras de sincronização personalizados. YDo não escolha um valor que é utilizado por outra regra de sincronização. |

5. Vá para o **Scoping filtro** separador e adicione um **único grupo de filtro de âmbito com dois cláusulas**:
 
    | Atributo | operador | Valor |
    | --- | --- | --- |
    | sourceObjectType | IGUAL A | Utilizador |
    | cloudMastered | NOTEQUAL | Verdadeiro |

    Filtro de âmbito determina qual é aplicada esta regra de sincronização de saída para os objetos do Azure AD. Neste exemplo, utilizamos o mesmo filtro de âmbito de "Out AD – a identidade do utilizador" regra de sincronização de OOB. Impede que a regra de sincronização que está a ser aplicadas a objetos de utilizador que não estão sincronizados do Active Directory no local. Poderá ter de otimizar o filtro de âmbito, de acordo com a implementação do Azure AD Connect.
    
6. Vá para o **transformação** separador e implementar a seguinte regra de transformação:

    | Tipo de fluxo | Atributo de destino | Origem | Aplicar uma vez | Tipo de intercalação |
    | --- | --- | --- | --- | --- |
    | Direto | PreferredDataLocation | PreferredDataLocation | Desmarcada | Atualizar |

7. Fechar **adicionar** para criar a regra de saída.

![Criar regra de sincronização de saída](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step5.png)

### <a name="step-6-run-full-synchronization-cycle"></a>Passo 6: Ciclo de sincronização completa de execução
Em geral, o ciclo de sincronização completa é necessário uma vez que, foram adicionados novos atributos para ambos os AD e esquema de conector do Azure AD e as regras de sincronização personalizados introduzidos. Recomenda-se que verificar as alterações antes de exportá-los para o Azure AD. Pode utilizar os seguintes passos para verificar as alterações ao executar manualmente os passos que compõem um ciclo de sincronização completa. 

1. Executar **completa importação** passo no **conector AD no local**:

   1. Vá para o **operações** separador o Synchronization Service Manager.

   2. Clique com o botão direito no **conector AD no local** e selecione **executar...**

   3. Na caixa de diálogo de pop-up, selecione **importação completa** e clique em **OK**.
    
   4. Aguarde pela conclusão da operação.

    > [!NOTE]
    > Pode ignorar a importação completa no no local conector AD Se o atributo de origem já está incluído na lista de importadas atributos. Por outras palavras, não tinha que fazer qualquer alteração durante [passo 2: adicionar o atributo de origem para o local esquema conector AD](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Executar **completa importação** passo no **conector do Azure AD**:

   1. Clique com o botão direito no **conector do Azure AD** e selecione **executar...**

   2. Na caixa de diálogo de pop-up, selecione **importação completa** e clique em **OK**.
   
   3. Aguarde pela conclusão da operação.

3. Verificar as alterações de regra de sincronização de um objeto de utilizador existente:

O atributo de origem no local do Active Directory e PreferredDataLocation do Azure AD foram importados para o respetivo espaço de conector. Antes de continuar com o passo de sincronização completa, recomenda-se que efetue uma **pré-visualização** num utilizador existente objeto no local espaço de conector do AD. O objeto selecionado que deve ter o atributo de origem preenchido. Um bem-sucedida **pré-visualização** com PreferredDataLocation preenchido no Metaverso é um bom indicador de que configurou a sincronização regras corretamente. Para obter informações sobre como fazer uma **pré-visualização**, consulte a secção [verificar a alteração](#verify-the-change).

4. Executar **sincronização completa** passo no **conector AD no local**:

   1. Clique com o botão direito no **conector AD no local** e selecione **executar...**
  
   2. Na caixa de diálogo de pop-up, selecione **sincronização completa** e clique em **OK**.
   
   3. Aguarde pela conclusão da operação.

5. Certifique-se **exportações pendentes** para o Azure AD:

   1. Clique com o botão direito no **conector do Azure AD** e selecione **espaço de conector de pesquisa**.

   2. Na caixa de diálogo de pop-up da espaço de conector de pesquisa:

      1. Definir **âmbito** para **pendentes exportação**.
      
      2. Verifique todas as três caixas de verificação, incluindo **adicionar, modificar e eliminar**.
      
      3. Clique em de **pesquisa** botão para obter a lista de objetos com as alterações para ser exportada. Para examinar as alterações de determinado objecto, faça duplo clique o objeto.
      
      4. Certifique-se de que as alterações são esperadas.

6. Executar **exportar** passo no **conector do Azure AD**
      
   1. Clique com botão direito do **conector do Azure AD** e selecione **executar...**
   
   2. Na caixa de diálogo de pop-up a executar o conector, selecione **exportar** e clique em **OK**.
   
   3. Aguarde que a exportação para o Azure AD para concluir.

> [!NOTE]
> Poderá reparar que os passos não incluem o passo de sincronização completa e o passo de exportação no conector Azure AD. Os passos não são necessários, uma vez que os valores de atributo fluem do Active Directory no local ao Azure AD apenas.

### <a name="step-7-re-enable-sync-scheduler"></a>Passo 7: Volte a ativar do agendador de sincronização
Volte a ativar o agendador de sincronização incorporada:

1. Inicie a sessão do PowerShell.

2. Volte a ativar a sincronização agendada, executando o cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`



## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o modelo de configuração [compreender de aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Saiba mais sobre o idioma de expressão no [expressões compreender de aprovisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Noções e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
