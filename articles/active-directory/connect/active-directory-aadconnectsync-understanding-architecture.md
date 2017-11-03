---
title: "Sincronização do Azure AD Connect: Noções sobre a arquitetura | Microsoft Docs"
description: "Este tópico descreve a arquitetura de sincronização do Azure AD Connect e explica certos termos utilizados."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 51082ad453d53f56f30f814b78578801c00f4827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Sincronização do Azure AD Connect: Noções sobre a arquitetura
Este tópico abrange a arquitetura básica para sincronização do Azure AD Connect. Em muitos aspetos, é semelhante ao seus antecessores MIIS 2003, ILM 2007 e FIM 2010. Sincronização do Azure AD Connect é a evolução destas tecnologias. Se estiver familiarizado com qualquer uma destas tecnologias anterior, o conteúdo deste tópico será familiar bem. Se estiver familiarizado com a sincronização, em seguida, este tópico é que o utilizador. No entanto não é um requisito para saber os detalhes deste tópico, seja bem-sucedida efetuar personalizações para sincronização do Azure AD Connect (designado por motor de sincronização neste tópico).

## <a name="architecture"></a>Arquitetura
O motor de sincronização cria uma vista integrada dos objetos que estão armazenados em várias origens de dados ligada e gere informações de identidade dessas origens de dados. Esta vista integrada é determinada pelo informações de identidade obtidas a partir de origens de dados e um conjunto de regras que determinam como processar estas informações.

### <a name="connected-data-sources-and-connectors"></a>Conetores e origens de dados
O motor de sincronização processa as informações de identidade do repositórios de dados diferentes, tais como o Active Directory ou uma base de dados do SQL Server. Cada repositório de dados que organiza os respetivos dados num formato semelhante a base de dados e que fornece métodos padrão de acesso de dados é um potencial candidato de origem de dados para o motor de sincronização. Os repositórios de dados que estão sincronizados com o motor de sincronização são denominados **ligado a origens de dados** ou **ligado diretórios** (CD).

O motor de sincronização encapsula interação com uma origem de dados ligada dentro de um módulo chamado um **conector**. Cada tipo de origem de dados ligada tem um conector específico. O conector traduz uma operação necessária para o formato de que compreende a origem de dados ligada.

Conectores efetuar chamadas de API para trocar informações de identidade (de leitura e escrita) com uma origem de dados ligada. Também é possível adicionar um Conetor personalizado utilizando a estrutura de conectividade extensível. A ilustração seguinte mostra como um conector liga uma origem de dados ligada para o motor de sincronização.

![Arch1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

Podem do fluxo de dados em qualquer direção, mas não é possível flua em ambas as direções em simultâneo. Por outras palavras, um conector pode ser configurado para permitir que os dados para o fluxo de origem de dados ligada para o motor de sincronização ou de motor de sincronização para a origem de dados ligada, mas apenas um dessas operações pode ocorrer num dado momento para um objeto e um atributo. A direção pode ser diferente para diferentes objetos e atributos diferentes.

Para configurar um conector, especifique os tipos de objeto que pretende sincronizar. Especificar os tipos de objeto define o âmbito dos objetos que estão incluídos no processo de sincronização. O passo seguinte consiste em Selecionar atributos a sincronizar, que é conhecido como uma lista de inclusão de atributo. Estas definições podem ser alteradas sempre em resposta a alterações para as regras de negócio. Quando utiliza o Assistente de instalação do Azure AD Connect, estas definições são configuradas por si.

Para exportar os objetos a uma origem de dados ligada, a lista de inclusão de atributo tem de incluir, pelo menos, os atributos mínimo necessários para criar um tipo de objeto específico numa origem de dados ligada. Por exemplo, o **sAMAccountName** atributo tem de ser incluído na lista de inclusão de atributos para exportar um objeto de utilizador no Active Directory, porque todos os objetos de utilizador no Active Directory tem de ter um **sAMAccountName** atributo definido. Novamente, o Assistente de instalação efetuar esta configuração para si.

Se a origem de dados ligada utiliza estruturais componentes, tais como partições ou contentores para organizar os objetos, pode limitar as áreas da origem de dados ligada, que são utilizadas para uma determinada solução.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Estrutura interna do espaço de nomes de motor de sincronização
O espaço de nomes do motor de sincronização completa é constituído por dois espaços de nomes que armazenam informações de identidade. Os dois espaços de nomes são:

* O espaço de conector (CS)
* Metaverso (MV)

O **espaço de conector** é uma área de transição que contém representações designado objetos de uma origem de dados ligada e os atributos especificados na lista de inclusão de atributos. O motor de sincronização utiliza o espaço de conector para determinar o que mudou na origem de dados ligada e para testar as alterações de entrada. O motor de sincronização também utiliza o espaço de conector para testar as alterações de saída de exportação para a origem de dados ligada. O motor de sincronização mantém um espaço de conector distintos como uma área de transição para cada conector.

Ao utilizar uma área de transição, o motor de sincronização permanece independente das origens de dados ligada e não é afetado por disponibilidade e acessibilidade. Como resultado, pode processar as informações de identidade em qualquer altura utilizando os dados na área de transição. O motor de sincronização pode pedir apenas as alterações efetuadas no interior da origem de dados ligada desde a última sessão comunicação terminada ou push enviados apenas as alterações às informações de identidade que a origem de dados ligada ainda não tiver recebido, que reduz a rede tráfego entre o motor de sincronização e a origem de dados ligada.

Além disso, o motor de sincronização armazena informações de estado sobre todos os objetos que o se prepara no espaço de conector. Quando são recebidos novos dados, o motor de sincronização avalia sempre se os dados já foram sincronizados.

O **metaverso** é uma área de armazenamento que contém as informações de identidade agregados de várias origens de dados ligada, criando uma vista única global, integrada de todos os objetos combinadas. Objetos de Metaverso são criados com base nas informações de identidade que são obtidas a partir de origens de dados e um conjunto de regras que permitem-lhe personalizar o processo de sincronização.

A ilustração seguinte mostra o espaço de nomes do espaço de conector e o espaço de nomes de metaverso dentro do motor de sincronização.

![Arch2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Objetos de identidade do motor de sincronização
Os objetos no motor de sincronização são representações de qualquer um dos objetos da origem de dados ligada ou a vista integrada que o motor de sincronização tem desses objetos. Todos os objetos do motor de sincronização tem de ter um identificador exclusivo global (GUID). GUIDs fornecem integridade dos dados e rápidas relações entre objetos.

### <a name="connector-space-objects"></a>Objetos de espaço de conector
Quando o motor de sincronização comunica com uma origem de dados ligada, lê as informações de identidade na origem de dados ligada e utiliza essa informação para criar uma representação do objeto de identidade no espaço de conector. Não é possível criar ou eliminar estes objetos individualmente. No entanto, pode eliminar manualmente todos os objetos num espaço de conector.

Todos os objetos no espaço de conector tem dois atributos:

* Um identificador exclusivo global (GUID)
* Um nome exclusivo (também conhecido como DN)

Se a origem de dados ligada atribui um atributo exclusivo para o objeto, em seguida, objetos no espaço de conector também podem ter um atributo âncora. O atributo âncora identifica exclusivamente um objeto da origem de dados ligada. O motor de sincronização utiliza a âncora de localizar a representação deste objeto correspondente na origem de dados ligada. Motor de sincronização parte do princípio de que a âncora de um objeto nunca é alterado durante a duração do objeto.

Muitos dos conectores utilizam um identificador exclusivo conhecido para gerar uma âncora automaticamente para cada objeto quando for importado. Por exemplo, o conector do Active Directory utiliza o **objectGUID** atributo para uma âncora. Para origens de dados que não fornecem um identificador exclusivo claramente definido, pode especificar a geração de âncora como parte da configuração do conector.

Nesse caso, a âncora baseia-se de um ou mais atributos exclusivos de um objeto de tipo, nem do que as alterações e exclusivamente que identifica o objeto no espaço de conector (por exemplo, um número de empregado ou um ID de utilizador).

Um objeto de espaço de conector pode ser um dos seguintes:

* Um objeto de teste
* Um marcador de posição

### <a name="staging-objects"></a>Objetos de transição
Um objeto de transição representa uma instância dos tipos de objeto designado da origem de dados ligada. Para além de GUID e o nome único, um objeto de teste tem sempre um valor que indica o tipo de objeto.

Objetos de teste que tenham sido importados sempre de ter um valor para o atributo âncora. Objetos de teste que foram recentemente aprovisionados pelo motor de sincronização e estão a ser criada na origem de dados ligada não tem um valor para o atributo âncora.

Objetos de transição também transportem os valores atuais de atributos de negócio e as informações operacionais necessários pelo motor de sincronização para efetuar o processo de sincronização. Informações operacionais incluem sinalizadores que indicam o tipo de atualizações que são configurados no objeto de teste. Se um objeto de teste tiver recebido novas informações de identidade da origem de dados ligados que ainda não foram processada, o objeto está assinalado como **importação pendente**. Se um objeto de teste tiver novas informações de identidade que ainda não foram exportadas para a origem de dados ligada, está assinalado como **pendentes exportação**.

Um objeto de teste pode ser um objeto de importação ou um objeto de exportação. O motor de sincronização cria um objeto de importação utilizando as informações do objeto recebidas a partir da origem de dados ligada. Quando o motor de sincronização recebe informações sobre a existência de um novo objeto a que corresponda a um dos tipos de objeto selecionados no conector, cria um objeto de importação no espaço de conector como uma representação do objeto da origem de dados ligada.

A ilustração seguinte mostra um objeto de importação que representa um objecto da origem de dados ligada.

![Arch3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

O motor de sincronização cria um objeto de exportação utilizando as informações de objetos no metaverso. Objetos de exportação são exportados para a origem de dados ligada durante a próxima sessão de comunicação. Perspetiva do motor de sincronização de exportação não existem objetos na origem de dados ligada ainda. Por conseguinte, o atributo âncora para um objeto de exportação não está disponível. Depois de receber o objeto do motor de sincronização, a origem de dados ligada cria um valor exclusivo para o atributo âncora do objeto.

A ilustração seguinte mostra como é criado um objecto de exportação utilizando as informações de identidade no metaverso.

![Arch4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

O motor de sincronização confirma que a exportação do objeto ao voltar a importar o objeto da origem de dados ligada. Exporte os objetos de importação de objetos tornarem quando o motor de sincronização recebe-los durante a importação seguinte do que a origem de dados ligada.

### <a name="placeholders"></a>Marcadores de posição
O motor de sincronização utiliza um espaço de nomes simples para armazenar objetos. No entanto, algumas origens de dados como o Active Directory utilizam um espaço de nomes hierárquico. Para transformar informações a partir de um espaço de nomes hierárquica para um espaço de nomes simples, o motor de sincronização utiliza os marcadores de posição para preservar a hierarquia.

Cada marcador de posição representa um componente (por exemplo, uma unidade organizacional), do nome de hierárquica de um objeto que não tenha sido importado para o motor de sincronização, mas é necessário para construir o nome hierárquico. Estes preencher lacunas criadas pelo referências da origem de dados ligadas a objetos que não são de teste objetos no espaço de conector.

O motor de sincronização também utiliza marcadores de posição para armazenar os objetos referenciados que ainda não tiverem sido importados. Por exemplo, se a sincronização está configurada para incluir o atributo manager para o *Abbie Spencer* objeto e o valor recebido é um objeto que não foi importado ainda, tal como *CN = Nogueira Sperry, CN = utilizadores, DC = fabrikam, DC = com* , as informações de manager são armazenadas como marcadores de posição no espaço de conector. Se o objecto do Gestor for importado mais tarde, o objeto de marcador de posição é substituído pelo objeto transição que representa o gestor.

### <a name="metaverse-objects"></a>Objetos de Metaverso
Um objeto de metaverso contém a vista agregada tem desse motor de sincronização dos objetos de testes no espaço de conector. Motor de sincronização cria objetos de metaverso utilizando as informações nos objetos de importação. Vários objetos de espaço de conector podem ser associados a um objeto de metaverso único, mas um objeto de espaço de conector não pode ser associado a mais do que um objeto de metaverso.

Objetos de Metaverso não podem ser manualmente criados ou eliminados. O motor de sincronização elimina automaticamente os objetos de metaverso que não tenham uma ligação a qualquer objeto de espaço de conector no espaço de conector.

Para mapear objetos dentro de uma origem de dados ligada para um tipo de objeto correspondente no metaverso, o motor de sincronização fornece um esquema extensível com um conjunto predefinido de tipos de objetos e atributos associados. Pode criar novos tipos de objeto e os atributos de objetos de metaverso. Os atributos podem ser único ou com múltiplos valores e os tipos de atributo podem ser cadeias, referências, números e os valores booleanos.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relações entre objetos de transição e objetos de metaverso
Dentro do espaço de nomes do motor de sincronização, o fluxo de dados está ativado pela relação de ligação entre objetos de transição e objetos de metaverso. Um objeto de teste que está ligado a um objeto de metaverso é chamado um **associado ao objeto** (ou **objecto do conector**). Um objeto de teste que não está ligado a um objeto de metaverso é chamado um **desagregado objeto** (ou **objetos de seccionador**). Os termos de licenciamento associados e desassociada são preferenciais para não confunda com os conectores responsáveis por importar e exportar dados a partir de um diretório ligado.

Marcadores de posição nunca estão ligados a um objeto de metaverso

Um objeto associado a um é composto por um objeto de transição e a respetiva relação ligada a um objeto de metaverso único. Objetos associados são utilizados para sincronizar os valores de atributos entre um objeto de espaço de conector e um objeto de metaverso.

Quando um objeto de transição torna-se um objeto associado ao durante a sincronização, os atributos possam circular entre o objeto de transição e o objeto de metaverso. Fluxo de atributos é bidirecional e é configurado utilizando regras de atributo de importação e exportação atributo regras.

Um objeto de espaço de conector única pode ser ligado a apenas um objeto de metaverso. No entanto, cada objeto de metaverso pode ser associado a vários objetos de espaço de conector da mesma ou nos espaços de conector diferentes, conforme mostrado na ilustração seguinte.

![Arch5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

A relação entre o objeto de transição e um objeto de metaverso ligada é persistente e pode ser removida apenas por regras que especificar.

Um objeto disjoined é um objeto de teste que não está ligado a qualquer objeto de metaverso. O atributo de valores de um objeto disjoined não são processados qualquer adicional no metaverso. Os valores de atributo do objeto correspondente na origem de dados ligados não são atualizados pelo motor de sincronização.

Ao utilizar objetos desagregados, pode armazenar as informações de identidade no motor de sincronização e processá-la mais tarde. Manter um objeto de teste como um objeto disjoined no espaço de conector tem muitas vantagens. Porque o sistema já tiver testado as informações necessárias sobre este objeto, não é necessário criar uma representação deste objeto novamente durante a importação seguinte a partir da origem de dados ligada. Desta forma, o motor de sincronização tem sempre um instantâneo completo da origem de dados ligada, mesmo se não houver nenhuma ligação à origem de dados ligada atual. Objetos desagregados podem ser convertidos em objetos associados e vice versa, consoante as regras que especificar.

É criado um objeto de importação como um objeto disjoined. Um objeto de exportação tem de ser um objeto associado. A lógica de sistema impõe a esta regra e elimina todos os objetos de exportação não é um objeto associado.

## <a name="sync-engine-identity-management-process"></a>Processo de gestão de identidade de motor de sincronização
O processo de gestão de identidade controla a forma como as informações de identidade são atualizadas entre origens de dados diferentes. Gestão de identidades ocorre em três processos:

* Importar
* Sincronização
* Exportar

Durante o processo de importação, o motor de sincronização avalia as informações de identidade recebido de uma origem de dados ligada. Quando são detetadas alterações, este cria novos objetos de transição ou objetos transição existentes no espaço de conector para a sincronização de atualizações.

Durante o processo de sincronização, o motor de sincronização de atualizações de metaverso para refletir as alterações ocorridas no espaço de conector e atualiza o espaço de conector para refletir as alterações ocorridas no metaverso.

Durante o processo de exportação, o motor de sincronização envia as alterações que são testados nos objetos de teste e que estão sinalizadas como pendente de exportação.

Mostra o seguinte ilustração onde cada um dos processos ocorre como fluxos de informações de identidade de uma origem de dados ligada para outro.

![Arch6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>Processo de importação
Durante o processo de importação, o motor de sincronização avalia atualizações para as informações de identidade. Motor de Sincronização compara as informações de identidade recebidas a partir da origem de dados ligada com as informações de identidade de um objeto de transição e determina se o objeto de transição necessita de atualizações. Se for necessário atualizar o objeto de teste com novos dados, o objeto de transição está assinalado como pendente importar.

Por objetos no espaço de conector antes da sincronização de teste, o motor de sincronização pode processar apenas informações de identidade que foi alterado. Este processo fornece as seguintes vantagens:

* **Sincronização eficiente**. A quantidade de dados processados durante a sincronização é minimizada.
* **A ressincronização eficiente**. Pode alterar como o motor de sincronização processa as informações de identidade sem restabelecer a ligação do motor de sincronização para a origem de dados.
* **Oportunidade de pré-visualizar sincronização**. Pode pré-visualizar a sincronização para verificar se a sua pressupostos sobre o processo de gestão de identidade estão corretos.

Para cada objeto especificado no conector, o motor de sincronização primeiro tenta localizar uma representação do objeto no espaço de conector do conector. Motor de sincronização analisa todos os objetos de testes no espaço de conector e tenta localizar um objeto de transição correspondente que tem um atributo âncora correspondente. Se nenhum objeto de teste existente tem um atributo âncora correspondente, tenta localizar um objeto de transição correspondente com o mesmo nome único motor de sincronização.

Quando o motor de sincronização encontra um objeto de teste que corresponde ao nome único, mas não por âncora, ocorre o seguinte comportamento especial:

* Se o objeto localizado no espaço de conector não tem nenhuma âncora, então o motor de sincronização remove este objeto a partir do espaço de conector e marca o objeto de metaverso está associada como **novamente executada a próxima sincronização de aprovisionamento**. Em seguida, cria o novo objeto de importação.
* Se o objeto localizado no espaço de conector tem uma âncora de, em seguida, o motor de sincronização parte do princípio de que este objeto foi mudado ou eliminado no diretório ligado. Atribui um nome distinto temporário, os novo para o objeto de espaço de conector para que pode testar o objeto de entrada. O objeto antigo de torna-se então **transitório**, aguardar que o conector para importar a mudança de nome ou eliminação para resolver a situação.

Se o motor de sincronização localiza um objeto de transição correspondente para o objeto especificado no conector, determina o tipo de alterações para aplicar. Por exemplo, o motor de sincronização pode mudar o nome ou eliminar o objeto da origem de dados ligada ou que apenas pode atualizar os valores de atributo do objeto.

Objetos de teste com dados atualizados são marcados como pendente importar. Estão disponíveis diferentes tipos de pendentes importações. Consoante o resultado do processo de importação, um objeto de teste no espaço de conector tem um dos seguintes pendentes tipos de importação:

* **Nenhuma**. Não existem alterações a qualquer um dos atributos do objeto transição estão disponíveis. Motor de sincronização não sinalizador este tipo como pendente importar.
* **Adicionar**. O objeto de transição é um novo objeto de importação no espaço de conector. Motor de sincronização sinalizadores este tipo como pendente importar para processamento adicional no metaverso.
* **Atualização**. Motor de sincronização localiza um objeto de transição correspondente no espaço de conector e sinaliza este tipo como importação pendente para que as atualizações para os atributos podem ser processadas no metaverso. As atualizações incluem mudar o nome do objeto.
* **Eliminar**. Motor de sincronização localiza um objeto de transição correspondente no espaço de conector e sinaliza este tipo como importação pendente para que o associados a um objeto pode ser eliminado.
* **Eliminar/adicionar**. Motor de sincronização localiza um objeto de transição correspondente no espaço de conector, mas os tipos de objeto não correspondem. Neste caso, um eliminar-adicionar modificação é testada. Um eliminar-adicionar modificação indica ao motor de sincronização que tem de ocorrer uma ressincronização completa deste objeto porque a diferentes conjuntos de regras de aplicam a este objeto, quando o tipo de objeto alterações.

Ao definir o estado de importação pendente de um objeto de teste, é possível reduzir significativamente a quantidade de dados processadas durante a sincronização porque fazê-lo, por isso, permite que o sistema processar apenas os objetos que tenham dados atualizados.

### <a name="synchronization-process"></a>Processo de sincronização
Sincronização é composto por dois processos relacionados:

* Sincronização de entrada, quando o conteúdo do metaverso é atualizado utilizando os dados no espaço de conector.
* Sincronização de saída, quando o conteúdo do espaço de conector é atualizado através da utilização de dados no metaverso.

Ao utilizar as informações de teste no espaço de conector, o processo de sincronização de entrada cria no metaverso a vista integrada dos dados que são armazenados nas origens de dados ligada. Todos os objetos de transição ou apenas as informações de importação pendente estão agregadas, dependendo de como as regras estão configuradas.

As atualizações do processo de sincronização de saída exportar objetos quando forem efetuadas alterações de objetos de metaverso.

Sincronização de entrada cria a vista integrada no metaverso de informações de identidade que é recebidos a partir de origens de dados. Motor de sincronização pode processar as informações de identidade em qualquer altura utilizando as últimas informações de identidade que tem da origem de dados ligada.

**Sincronização de entrada**

Sincronização de entrada inclui os seguintes processos:

* **Aprovisionar** (também denominado **projecção** se é importante distinguir este processo de aprovisionamento de sincronização de saída). O motor de sincronização cria um novo objeto de metaverso com base num objeto de transição e liga-los. Aprovisionar é uma operação de ao nível do objeto.
* **Associar**. O motor de sincronização liga um objeto de transição para um objeto de metaverso existente. Uma associação é uma operação de ao nível do objeto.
* **Importar o fluxo de atributos**. Motor de sincronização de atualizações os valores de atributo, denominados de fluxo de atributos do objeto no metaverso. Fluxo de atributos de importação é uma operação de nível de atributo que requer uma ligação entre um objeto de transição e um objeto de metaverso.

Aprovisionar é o único processo que cria os objetos no metaverso. Aprovisionar afeta apenas os objetos de importação que estão desagregados objetos. Durante o aprovisionamento, o motor de sincronização cria um objeto de metaverso que corresponda ao tipo de objeto do objeto de importação e estabelece uma ligação entre os dois objetos, assim, criar um objeto associado a um.

O processo de associação também estabelece uma ligação entre objetos de importação e um objeto de metaverso. A diferença entre a associação e aprovisionar é que o processo de associação requer que o objeto de importação estão ligados a um objeto de metaverso existente, onde o processo de aprovisionamento cria um novo objeto de metaverso.

Motor de sincronização tenta associar um objeto de importação a um objeto de metaverso utilizando critérios especificados na configuração da regra de sincronização.

Durante os processos de aprovisionamento e a associação, o motor de sincronização liga um objeto disjoined para um objeto de metaverso torná-los associado. Depois de concluídos estes operações ao nível do objeto, o motor de sincronização pode atualizar os valores de atributo do objeto de metaverso associado. Este processo é denominado o fluxo de atributos de importação.

Fluxo de atributos de importação ocorre em todos os objetos de importação que transportem novos dados e estão ligados a um objeto de metaverso.

**Sincronização de saída**

Atualizações de sincronização de saída exportar objetos quando um objeto de metaverso alterar, mas não é eliminado. O objetivo da sincronização de saída é avaliar se as alterações a objetos de metaverso exigem atualizações a transição de objetos nos espaços de conector. Em alguns casos, as alterações podem exigir que teste objetos em todos os espaços de conector ser atualizado. Objetos de teste que são alterados são sinalizados como pendente de exportação, torná-los exportar objetos. Estes objetos são mais tarde enviados para a origem de dados ligados durante o processo de exportação de exportação.

Sincronização de saída tem três processos:

* **Aprovisionamento**
* **Desaprovisionamento**
* **Fluxo de atributos de exportação**

Aprovisionamento e desaprovisionamento são ambas as operações ao nível do objeto. Desaprovisionamento depende do aprovisionamento porque apenas aprovisionamento pode iniciá-lo. Desaprovisionamento é acionado quando o aprovisionamento remove a ligação entre um objeto de metaverso e um objeto de exportação.

Aprovisionamento sempre é acionado quando as alterações sejam aplicadas a objetos no metaverso. Quando são efetuadas alterações a objetos de metaverso, o motor de sincronização pode efetuar qualquer uma das seguintes tarefas como parte do processo de aprovisionamento:

* Crie objetos associados, onde um objeto de metaverso está ligado a um objeto de exportação criado recentemente.
* Mudar o nome de um objeto associado.
* Disjoin ligações entre um objeto de metaverso e objetos, criação de um objeto disjoined de teste.

Se o aprovisionamento requer o motor de sincronização para criar um novo objeto de conector, o objeto de transição para o qual está ligado o objeto de metaverso é sempre um objeto de exportação, porque o objeto ainda não existe na origem de dados ligada.

Se o aprovisionamento requer o motor de sincronização para disjoin um associado a um objeto, criação de um objeto disjoined, desaprovisionamento é acionado. O processo de desaprovisionamento elimina o objeto.

Durante o desaprovisionamento, eliminar um objeto de exportação não fisicamente eliminar o objeto. O objeto está assinalado como **eliminado**, o que significa que a operação delete é preparada no objeto.

Fluxo de atributos de exportação também ocorre durante o processo de sincronização de saída, forma semelhante ao que o fluxo de atributos de importação ocorre durante a sincronização de entrada. Fluxo de atributos de exportação ocorre apenas entre objetos de metaverso e exportação que estão associados.

### <a name="export-process"></a>Processo de exportação
Durante o processo de exportação, o motor de sincronização examina todos os objetos de exportação que estão sinalizados como pendente exportação no espaço de conector e, em seguida, envia as atualizações para a origem de dados ligada.

O motor de sincronização pode determinar o êxito de uma exportação mas suficientemente não consegue determinar que o processo de gestão de identidade está concluído. Objetos da origem de dados ligada podem sempre ser alterados por outros processos. Porque o motor de sincronização não tem uma ligação persistente para a origem de dados ligada, não é suficiente para efetuar pressupostos sobre as propriedades de um objecto da origem de dados ligada em apenas uma notificação de exportação com êxito.

Por exemplo, um processo na origem de dados ligada foi possível alterar os atributos o objeto para os valores originais (ou seja, a origem de dados ligada foi substituir os valores imediatamente depois dos dados são enviados pelo motor de sincronização e aplicados com êxito em a origem de dados ligada).

Os arquivos de motor de sincronização exportar e importar informações de estado sobre cada objeto de teste. Se os valores dos atributos que são especificados na lista de inclusão de atributos foram alterados desde a última exportação, o armazenamento de importar e exportar o motor de sincronização do Estado ativa para reagir de forma adequada. Motor de sincronização utiliza o processo de importação para confirmar a valores de atributo que foram exportados para a origem de dados ligada. Mostra uma comparação entre as informações de importados e exportadas, conforme mostrado na ilustração seguinte, permite que o motor de sincronização determinar se a exportação foi concluída com êxito ou se precisa de ser repetido.

![Arch7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

Por exemplo, se o motor de sincronização exporta atributo C, que tem um valor de 5, a uma origem de dados ligada, armazena C = 5 na respetiva memória de estado de exportação. Cada exportação adicional neste objecto resulta numa tentativa para exportar C = 5 para a origem de dados ligada novamente porque o motor de sincronização parte do princípio de que este valor não foi forma permanente aplicado para o objeto (ou seja, a menos que um valor diferente importado recentemente a partir de origem de dados ligada). A memória de exportação será eliminada quando C = 5 é recebido durante uma operação de importação do objeto.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

