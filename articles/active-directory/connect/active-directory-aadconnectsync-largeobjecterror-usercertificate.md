---
title: "Sincronização do Azure AD Connect: erros de processamento LargeObject causados por userCertificate atributo | Microsoft Docs"
description: "Este tópico fornece os passos de remediação de erros de LargeObject causados por userCertificate atributo."
services: active-directory
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 2a5418ff61e07793fceca5a8207c1c5aa18847b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Sincronização do Azure AD Connect: erros de processamento LargeObject causados por userCertificate atributo

Azure AD impõe um limite máximo de **15** valores de certificado no **userCertificate** atributo. Se o Azure AD Connect exporta um objeto com mais de 15 valores para o Azure AD, o Azure AD devolve um **LargeObject** erro com a mensagem:

>*"O objeto aprovisionado é demasiado grande. Compacte o número de valores de atributo neste objeto. A operação será repetida no próximo ciclo de sincronização..."*

O erro LargeObject pode ser causado por outros atributos do AD. Para confirmar que realmente é causado pelo atributo userCertificate, tem de validar contra o objeto no AD no local ou no [pesquisa de Metaverso Synchronization Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Para obter a lista de objetos no seu inquilino com LargeObject erros, utilize um dos seguintes métodos:

 * Se o seu inquilino está ativado para o Azure AD Connect Health para sincronização, pode consultar o [relatório de erros de sincronização](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync#object-level-synchronization-error-report-preview) fornecido.
 
 * O e-mail de notificação para erros de sincronização de diretórios que é enviado no final de cada ciclo de sincronização tem a lista de objetos com LargeObject erros. 
 * O [separador Synchronization Service Manager Operations](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) mostra uma lista de objetos com erros LargeObject se clicar na exportação mais recente para a operação do Azure AD.
 
## <a name="mitigation-options"></a>Opções de mitigação
Até que o erro LargeObject for resolvido, as outras alterações de atributos para o mesmo objeto não não possível exportar para o Azure AD. Para resolver o erro, pode considerar as seguintes opções:

 * Atualizar o Azure AD Connect, para criar 1.1.524.0 ou após. No Azure AD Connect crie 1.1.524.0, a sincronização de out-of-box regras foram atualizadas para exportar não userCertificate de atributos e userSMIMECertificate se os atributos têm mais de 15 valores. Para obter detalhes sobre como atualizar do Azure AD Connect, consulte o artigo [do Azure AD Connect: atualização a partir de uma versão anterior para a versão mais recente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Implementar um **regra de sincronização de saída** no Azure AD Connect que exporta um **valor em vez dos valores reais para objetos com mais de 15 valores de certificado nulo**. Esta opção é adequada se não necessitar de qualquer um dos valores de certificado seja exportado para o Azure AD para objetos com mais de 15 valores. Para obter mais informações sobre como implementar esta regra de sincronização, consulte a secção seguinte [regra de sincronização de implementar para limitar a exportação do atributo userCertificate](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Reduza o número de valores de certificado no local de objeto do AD (15 ou menos) ao remover valores que já não estão em utilização pela sua organização. Isto é adequado se o aumento do tamanho atributo é causado por expiraram ou que não utilizados. Pode utilizar o [aqui disponível do script do PowerShell](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) para ajudar a localizar, cópia de segurança e eliminar certificados expirados no seu local AD. Antes de eliminar os certificados, recomenda-se que verifique com os administradores da infraestrutura de chave pública na sua organização.

 * Configure o Azure AD Connect para excluir o atributo userCertificate da que está a ser exportados para o Azure AD. Em geral, recomendamos esta opção, uma vez que o atributo pode ser utilizado por Serviços Online da Microsoft para ativar cenários específicos. Em particular:
    * O atributo userCertificate no objeto User é utilizado pelo Exchange Online e clientes do Outlook para mensagem de assinatura e encriptação. Para obter mais informações sobre esta funcionalidade, consulte o artigo [S/MIME para a mensagem de assinatura e encriptação](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * O atributo userCertificate no objeto de computador é utilizado pelo Azure AD para permitir que o Windows 10 no local dispositivos associados a um domínio ligar ao Azure AD. Para obter mais informações sobre esta funcionalidade, consulte artigo [ligar dispositivos associados a um domínio ao Azure AD para experiências do Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementar a regra de sincronização para limitar a exportação do atributo userCertificate
Para resolver o erro de LargeObject causado pelo atributo userCertificate, pode implementar uma regra de sincronização de saída no Azure AD Connect que exporta um **valor em vez dos valores reais para objetos com mais de 15 valores de certificadonulo**. Esta secção descreve os passos necessários para implementar a regra de sincronização para **utilizador** objetos. Os passos podem ser adaptados para **contacte** e **computador** objetos.

> [!IMPORTANT]
> Exportar um valor nulo remove o certificado valores sido anteriormente exportado com êxito para o Azure AD.

Os passos podem ser resumidos como:

1. Desativar o agendador de sincronização e certifique-se de que existe, está sem sincronização em curso.
3. Localize a regra de sincronização de saída existentes para o atributo userCertificate.
4. Crie a regra de sincronização de saída obrigada.
5. Certifique-se a nova regra de sincronização de um objeto com o erro de LargeObject existente.
6. Aplica a nova regra de sincronização aos restantes objetos com o erro de LargeObject.
7. Certifique-se de que não foram efetuadas alterações inesperadas a aguardar para ser exportados para o Azure AD.
8. Exporte as alterações para o Azure AD.
9. Volte a ativar do agendador de sincronização.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passo 1. Desativar o agendador de sincronização e certifique-se de que existe, está sem sincronização em curso
Certifique-se de que não sincronização ocorre enquanto estiver a implementar uma nova regra de sincronização para evitar alterações indesejadas que está a ser exportadas para o Azure AD. Para desativar o agendador de sincronização incorporada:
1. Inicie a sessão do PowerShell no servidor do Azure AD Connect.

2. Desative a sincronização agendada, executando o cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Os passos anteriores só são aplicáveis a versões mais recentes (1.1.xxx.x) do Azure AD Connect com o programador incorporado. Se estiver a utilizar versões anteriores (1.0.xxx.x) do Azure AD Connect que utiliza o Programador de tarefas do Windows ou estiver a utilizar o seu próprio programador personalizado (não comum) para acionar a sincronização periódica, tem de desativá-las em conformidade.

3. Iniciar o **Synchronization Service Manager** acedendo ao serviço de sincronização inicial →.

4. Vá para o **operações** separador e confirme que não há nenhuma operação cujo estado é *"em curso."*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Passo 2. Localizar a regra de sincronização de saída existentes para o atributo userCertificate
Deve existir uma regra de sincronização existente que está ativada e configurada para exportar o atributo de userCertificate para objetos de utilizador para o Azure AD. Localize esta regra de sincronização para descobrir o **precedência** e **filtro de âmbito** configuração:

1. Iniciar o **Editor de regras de sincronização** acedendo ao Editor de regras de sincronização inicial →.

2. Configure os filtros de pesquisa com os seguintes valores:

    | Atributo | Valor |
    | --- | --- |
    | Direção |**Saída** |
    | Tipo de objeto de MV |**Pessoa** |
    | conector |*nome do seu conector do Azure AD* |
    | Tipo de objeto do conector |**utilizador** |
    | Atributo de MV |**userCertificate** |

3. Se estiver a utilizar regras de sincronização (out-of-box) de OOB para o conector do Azure AD para exportar o atributo de userCertficiate para objetos de utilizador, deve regressar a *"Enviados ao AAD – utilizador ExchangeOnline"* regra.
4. Tome nota do **precedência** valor desta regra de sincronização.
5. Selecione a regra de sincronização e clique em **editar**.
6. No *"Editar reservado confirmação regra"* caixa de diálogo de pop-up, clique em **não**. (Não se preocupe, que não vamos fazer qualquer alteração para esta regra de sincronização).
7. No ecrã de edição, selecione o **Scoping filtro** separador.
8. Tenha em atenção a configuração de filtro de âmbito. Se estiver a utilizar a regra de sincronização OOB, deverá exatamente existir **um grupo de filtro âmbito que contém dois cláusulas**, incluindo:

    | Atributo | operador | Valor |
    | --- | --- | --- |
    | sourceObjectType | IGUAL A | Utilizador |
    | cloudMastered | NOTEQUAL | Verdadeiro |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Passo 3. Criar a regra de sincronização de saída obrigada
A nova regra de sincronização tem de ter o mesmo **filtro âmbito** e **precedência superior** que a regra de sincronização existente. Isto garante que a nova regra de sincronização se aplica o mesmo conjunto de objetos, como a regra de sincronização existente e substitui a regra de sincronização existente para o atributo userCertificate. Para criar a regra de sincronização:
1. No Editor de regras de sincronização, clique em de **Adicionar nova regra** botão.
2. Sob o **separador de descrição**, fornecer a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | Nome | *Forneça um nome* | Por exemplo, *"Enviados para o AAD – personalizado de substituição para userCertificate"* |
    | Descrição | *Forneça uma descrição* | Por exemplo, *"Se userCertificate atributo tem mais do que 15 valores, exporte nulo".* |
    | Sistema ligado | *Selecione o conector Azure AD* |
    | Tipo de objeto de sistema ligado | **utilizador** | |
    | Tipo de objeto de Metaverso | **pessoa** | |
    | Tipo de ligação | **Associar** | |
    | Precedência | *Optou por um número entre 1 e 99* | A número escolhida não podem ser utilizadas por nenhuma regra de sincronização existente e tem um valor inferior (e, consequentemente, precedência superior) que a regra de sincronização existente. |

3. Vá para o **Scoping filtro** separador e implementa o filtro de âmbito mesmo que a regra de sincronização existente está a utilizar.
4. Ignorar o **associar regras** separador.
5. Vá para o **transformações** separador para adicionar uma novo transformação utilizando a seguinte configuração:

    | Atributo | Valor |
    | --- | --- |
    | Tipo de fluxo |**Expressão** |
    | Atributo de destino |**userCertificate** |
    | Atributo de origem |*Utilize a seguinte expressão*:`IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Clique em de **adicionar** botão para criar a regra de sincronização.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Passo 4. Certifique-se a nova regra de sincronização de um objeto existente com o erro de LargeObject
Isto serve para confirmar que a regra de sincronização criada está a funcionar corretamente num objeto AD existente com o erro de LargeObject antes de aplicá-la para outros objetos:
1. Vá para o **operações** separador o Synchronization Service Manager.
2. Selecione a exportação mais recente para a operação do Azure AD e clique dos objetos com LargeObject erros.
3.  No ecrã de pop-up de propriedades do objeto de espaço de conector, clique em de **pré-visualização** botão.
4. No ecrã de pop-up de pré-visualização, selecione **completa sincronização** e clique em **consolidar pré-visualização**.
5. Feche o ecrã de pré-visualização e o ecrã de propriedades do objeto de espaço de conector.
6. Vá para o **conectores** separador o Synchronization Service Manager.
7. Clique com o botão direito no **do Azure AD** conector e seleccione **executar...**
8. No pop-up executar o conector, selecione **exportar** passo e clique em **OK**.
9. Aguarde que a exportação para o Azure AD para concluir e confirme que não há nenhum erro LargeObject mais neste objeto específico.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Passo 5. Aplicar a nova regra de sincronização aos restantes objetos com o erro de LargeObject
Assim que a regra de sincronização foi adicionada, terá de executar um passo de sincronização completa no conector AD:
1. Vá para o **conectores** separador o Synchronization Service Manager.
2. Clique com o botão direito no **AD** conector e seleccione **executar...**
3. No pop-up executar o conector, selecione **sincronização completa** passo e clique em **OK**.
4. Aguarde que o passo de sincronização completa concluir.
5. Repita os passos acima para os conectores restantes do AD, se tiver mais do que um conectores do AD. Normalmente, vários conectores são necessários se tiver vários diretórios no local.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Passo 6. Certifique-se de que não foram efetuadas alterações inesperadas a aguardar para ser exportados para o Azure AD
1. Vá para o **conectores** separador o Synchronization Service Manager.
2. Clique com o botão direito no **do Azure AD** conector e seleccione **espaço de conector de pesquisa**.
3. No pop-up do espaço de conector de pesquisa:
    1. Definir o âmbito **pendentes exportação**.
    2. Verifique todos os 3 caixas de verificação, incluindo **adicionar**, **modificar** e **eliminar**.
    3. Clique em **pesquisa** botão para devolver todos os objetos com as alterações à espera que seja exportado para o Azure AD.
    4. Certifique-se de que não foram efetuadas alterações inesperadas. Para examinar as alterações de determinado objecto, faça duplo clique no objeto.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Passo 7. Exportar as alterações para o Azure AD
Para exportar as alterações para o Azure AD:
1. Vá para o **conectores** separador o Synchronization Service Manager.
2. Clique com o botão direito no **do Azure AD** conector e seleccione **executar...**
4. No pop-up executar o conector, selecione **exportar** passo e clique em **OK**.
5. Aguarde que a exportação para o Azure AD para concluir e confirmar que existirem erros LargeObject mais.

### <a name="step-8-re-enable-sync-scheduler"></a>Passo 8. Volte a ativar do agendador de sincronização
Agora que o problema foi resolvido, volte a ativar o agendador de sincronização incorporada:
1. Inicie a sessão do PowerShell.
2. Volte a ativar a sincronização agendada, executando o cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Os passos anteriores só são aplicáveis a versões mais recentes (1.1.xxx.x) do Azure AD Connect com o programador incorporado. Se estiver a utilizar versões anteriores (1.0.xxx.x) do Azure AD Connect que utiliza o Programador de tarefas do Windows ou estiver a utilizar o seu próprio programador personalizado (não comum) para acionar a sincronização periódica, tem de desativá-las em conformidade.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

