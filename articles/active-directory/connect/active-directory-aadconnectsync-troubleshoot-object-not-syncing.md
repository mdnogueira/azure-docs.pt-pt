---
title: "Resolver problemas de um objeto que não está a sincronizar com o Azure AD | Microsoft Docs"
description: "Resolver problemas relacionados com a razão pela qual um objeto não está a sincronizar com o Azure AD."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 491a920ceeaac62dd37b1def3f02234056aebfb0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Resolver problemas de um objeto que não está a sincronizar com o Azure AD

Se um objeto não está a sincronizar conforme esperado para o Azure AD, em seguida, pode ser devido a várias razões. Se recebeu um e-mail de erro do Azure AD ou consulte o erro no Azure AD Connect Health, em seguida, lidos [resolver erros de exportação](active-directory-aadconnect-troubleshoot-sync-errors.md) em vez disso. Mas, se estiver a resolver um problema em que o objeto não está no Azure AD, em seguida, este tópico é que o utilizador. Descreve como localizar erros na sincronização do Azure AD Connect de componente no local.

Para localizar os erros, vai observe alguns locais diferentes pela seguinte ordem:

1. O [os registos de operações](#operations) para encontrar erros identificados pelo motor de sincronização durante a importação e sincronização.
2. O [espaço de conector](#connector-space-object-properties) para localizar objetos em falta e erros de sincronização.
3. O [metaverso](#metaverse-object-properties) para encontrar problemas relacionados com a dados.

Iniciar [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) antes de iniciar estes passos.

## <a name="operations"></a>Operações
No separador operações o Synchronization Service Manager é onde deve começar a resolução de problemas. No separador operações mostra os resultados das operações mais recentes.  
![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/operations.png)  

A metade superior mostra todas as execuções chronic ordem. Por predefinição, as operações de registo mantém informações sobre os últimos sete dias, mas esta definição pode ser alterada com o [programador](active-directory-aadconnectsync-feature-scheduler.md). Pretende procurar qualquer execução que mostram um Estado de êxito. Pode alterar a ordenação clicando os cabeçalhos.

O **estado** coluna são as informações mais importantes e mostra o problema mais grave para execução. Eis um resumo rápido dos Estados mais comuns por ordem de prioridade para investigar (onde * indicar vários cadeias de erro possíveis).

| Estado | Comentário |
| --- | --- |
| parado-* |Não foi possível concluir a execução. Por exemplo, se o sistema remoto não está disponível e não pode ser contactado. |
| parado-erro-limite |Existem mais de 5.000 erros. A executar automaticamente foi parada devido ao elevado número de erros. |
| concluída -\*-erros |A execução foi concluída, mas existem erros (menos de 5000) que devem ser investigados. |
| concluída -\*-avisos |A execução foi concluída, mas alguns dados não está no estado esperado. Se tiver de erros, em seguida, esta mensagem é, normalmente, apenas um sintoma. Até ter resolvidas erros, não deve investigar avisos. |
| exito |Não existem problemas. |

Quando seleciona uma linha, na parte inferior atualizações para mostrar os detalhes do que são executados. A extremidade esquerda da parte inferior, poderá ter uma indicação de lista **passo n. º**. Esta lista aparece apenas se tiver vários domínios na floresta onde cada domínio é representado por um passo. O nome de domínio pode ser encontrado no cabeçalho **partição**. Em **estatísticas de sincronização**, pode encontrar mais informações sobre o número de alterações que foram processados. Pode clicar nas hiperligações para obter uma lista dos objetos alterados. Se tiver de objetos com erros, esses surgem erros em **erros de sincronização**.

### <a name="troubleshoot-errors-in-operations-tab"></a>Resolver erros no separador de operações
![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorsync.png)  
Quando tiver erros, tanto o objeto erro e o erro próprio são ligações fornecem mais informações.

Iniciar clicando a cadeia de erro (**sincronização regra-erro-função-acionada** na imagem). É apresentada primeiro com uma descrição geral do objeto. Para ver o erro real, clique no botão **rastreio da pilha**. Este rastreio fornece informações de nível de depuração para o erro.

Pode faça duplo clique no **chamar informações de pilha** caixa, escolha **Selecionar tudo**, e **cópia**. Em seguida, pode copiar a pilha e observe o erro no seu editor favorito, tais como o bloco de notas.

* Se o erro é partir **SyncRulesEngine**, em seguida, as informações de pilha de chamada tem primeiro uma lista de todos os atributos do objeto. Desloque para baixo até verá no cabeçalho **InnerException = >**.  
  ![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorinnerexception.png)  
  A linha depois mostra o erro. Na imagem acima, o erro é de um Fabrikam de regra de sincronização personalizados criados.

Se o erro propriamente dito não fornecer informações suficientes, em seguida, está na altura para ver dados propriamente ditos. Pode clicar na ligação com o identificador de objeto e continuar a resolução de problemas de [objeto importado de espaço de conector](#cs-import).

## <a name="connector-space-object-properties"></a>Propriedades do objeto de espaço de conector
Se não tiver quaisquer erros encontrados no [operações](#operations) separador, em seguida, o passo seguinte consiste em seguir o objeto de espaço de conector do Active Directory, para o metaverso e, para o Azure AD. Este caminho, deverá considerar o onde está o problema.

### <a name="search-for-an-object-in-the-cs"></a>Pesquisar um objeto no CS

No **Synchronization Service Manager**, clique em **conectores**, selecione o conector do Active Directory, e **espaço de conector de pesquisa**.

No **âmbito**, selecione **RDN** (em que pretende procurar o atributo CN) ou **DN ou âncora** (em que pretende procurar o atributo distinguishedName). Introduza um valor e clique em **pesquisa**.  
![Pesquisa de espaço conector](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearch.png)  

Se não encontrar o objeto que procura, então poderá ter sido filtrado com [filtragem baseada em domínio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) ou [filtragem baseada na UO](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering). Leia o [configurar a filtragem](active-directory-aadconnectsync-configure-filtering.md) tópico para verificar se a filtragem é configurada como esperado.

Outra pesquisa útil consiste em Selecionar o conector do Azure AD, no **âmbito** selecione **importação pendente**e selecione o **adicionar** caixa de verificação. Esta pesquisa dá-lhe todos os objetos sincronizados no Azure AD que não pode ser associado um objeto no local.  
![Orphan de pesquisa de espaço de conector](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearchorphan.png)  
Os objetos foram criados por outro motor de sincronização ou um motor de sincronização com uma configuração de filtragem diferente. Esta vista está uma lista de **orphan** objetos já não é geridos. Deve rever esta lista e considere remover estes objetos utilizando o [Azure AD PowerShell](http://aka.ms/aadposh) cmdlets.

### <a name="cs-import"></a>Importação de CS
Quando abre um objeto de cs, existem vários separadores na parte superior. O **importar** separador mostra os dados que são testados após uma importação.  
![Objeto de CS](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/csobject.png)    
O **valor antigo** mostra que atualmente armazenado em ligar e o **novo valor** que recebeu do sistema de origem e ainda não foi aplicado. Se existir um erro no objeto, as alterações não são processadas.

**Erro**  
![Objeto de CS](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssyncerror.png)  
O **erro de sincronização** separador só está visível se houver um problema com o objeto. Para obter mais informações, consulte [resolver erros de sincronização](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>CS linhagem
O separador de linhagem mostra como o objeto de espaço de conector está relacionado com o objeto de metaverso. Pode ver quando o conector importada pela última vez uma alteração do sistema ligado e as regras que foram aplicados para preencher dados no metaverso.  
![Linhagem CS](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineage.png)  
No **ação** coluna, pode ver há um **entrada** regra de sincronização com a ação **aprovisionar**. Que indica que este objeto de espaço de conector estiver presente, desde que o objeto de metaverso permanece. Se a lista de regras de sincronização em vez disso, mostra uma regra de sincronização com direção **saída** e **aprovisionar**, ele indica que este objeto é eliminado quando o objeto de metaverso é eliminado.  
![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineageout.png)  
Também pode ver no **PasswordSync** coluna que o espaço de conector de entrada pode contribuir muda para a palavra-passe, uma vez que uma regra de sincronização tem o valor **verdadeiro**. Esta palavra-passe é enviada para o Azure AD através da regra de saída.

No separador linhagem, pode obter para o metaverso clicando [as propriedades do objeto de Metaverso](#mv-attributes).

Na parte inferior de todos os separadores são dois botões: **pré-visualização** e **registo**.

### <a name="preview"></a>Pré-visualização
A página de pré-visualização é utilizada para sincronizar um único objeto. Isto é útil se são algumas regras de sincronização personalizados de resolução de problemas e pretende ver o efeito de uma alteração num único objeto. Pode selecionar entre **sincronização completa** e **sincronização Delta**. Também pode selecionar entre **gerar pré-visualização**, apenas que mantém a alteração na memória, e **consolidar pré-visualização**, que atualizou o metaverso e prepara a todas as alterações aos espaços de conector de destino.  
![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/preview.png)  
Pode inspecionar o objeto e que a regra aplicada para um fluxo de atributo específico.  
![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/previewresult.png)

### <a name="log"></a>Registar
A página de registo é utilizada para ver o estado de sincronização de palavra-passe e histórico. Para obter mais informações, consulte [resolver problemas de sincronização de palavra-passe](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).

## <a name="metaverse-object-properties"></a>Propriedades do objeto de Metaverso
É, normalmente, é melhor iniciar a pesquisa da origem do Active Directory [espaço de conector](#connector-space). Mas também pode iniciar a pesquisa de metaverso.

### <a name="search-for-an-object-in-the-mv"></a>Procure um objeto de MV
No **Synchronization Service Manager**, clique em **pesquisa de Metaverso**. Crie uma consulta que sabe que localiza o utilizador. Pode procurar atributos comuns, tais como accountName (sAMAccountName) e userPrincipalName. Para obter mais informações, consulte [pesquisa de Metaverso](active-directory-aadconnectsync-service-manager-ui-mvsearch.md).
![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvsearch.png)  

No **os resultados da pesquisa** janela, clique o objeto.

Se não foi possível localizar o objeto, em seguida, tem não ainda atingiu o metaverso. Continuar a pesquisar para o objeto no Active Directory [espaço de conector](#connector-space-object-properties). Pode haver um erro de sincronização que está a bloquear o objeto de proveniente para o metaverso ou pode existir um filtro aplicado.

### <a name="mv-attributes"></a>Atributos de MV
No separador atributos, pode ver os valores e o conector contribuído-lo.  
![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvobject.png)  

Se um objeto não está a sincronizar, observe, em seguida, os seguintes atributos no metaverso:
- É o atributo **cloudFiltered** presente e definido como **verdadeiro**? Se é, em seguida, que foi filtrado, de acordo com os passos em [atributo baseado em filtragem](active-directory-aadconnectsync-configure-filtering.md#attribute-based-filtering).
- É o atributo **sourceAnchor** presente? Se não o tiver feito, tem uma topologia de floresta de recursos de conta? Se um objeto é identificado como uma caixa de correio ligada (o atributo **msExchRecipientTypeDetails** tem o valor 2), em seguida, o sourceAnchor é contribuído por floresta com uma conta do Active Directory ativada. Certifique-se a conta do principal foi importada e sincronizada corretamente. A conta de principal tem de estar listada no [conectores](#mv-connectors) para o objeto.

### <a name="mv-connectors"></a>Conectores de MV
O separador de conectores mostra todos os espaços de conector que tem uma representação do objeto.  
![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvconnectors.png)  
Deve ter um conector para:

- Cada floresta do Active Directory do utilizador é representada na. Este representação pode incluir foreignSecurityPrincipals e objetos de contacto.
- Um conetor no Azure AD.

Se estão em falta o conector para o Azure AD, em seguida, lidos [atributos de MV](#MV-attributes) para verificar os critérios de que está a ser aprovisionado para o Azure AD.

Este separador também permite-lhe navegar para o [objeto de espaço de conector](#connector-space-object-properties). Selecionar uma linha e clique em **propriedades**.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
