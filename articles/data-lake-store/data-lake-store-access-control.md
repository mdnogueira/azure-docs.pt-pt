---
title: "Descrição geral do controlo de acesso no Data Lake Store | Microsoft Docs"
description: Compreender o funcionamento do controlo de acesso no Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: nitinme
ms.openlocfilehash: 43582ae805d560e72fb8d03ab8e0abee4c4325b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="access-control-in-azure-data-lake-store"></a>Controlo de acesso no Azure Data Lake Store

O Azure Data Lake Store implementa um modelo de controlo de acesso que deriva do HDFS, que, por sua vez, deriva do modelo de controlo de acesso POSIX. Este artigo resume as noções básicas do modelo de controlo de acesso para o Data Lake Store. Para saber mais sobre o modelo de controlo de acesso do HDFS, veja [HDFS Permissions Guide (Guia de Permissões do HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="access-control-lists-on-files-and-folders"></a>Listas de controlo de acesso em ficheiros e pastas

Existem dois tipos de listas de controlo de acesso (ACLs) – **ACLs de Acesso** e **ACLs Predefinidas**.

* **ACLs de Acesso**: controlam o acesso a um objeto. Os ficheiros e as pastas têm ACLs de Acesso.

* **ACLs Predefinidas**: um "modelo" de ACLs associado a uma pasta que determinam as ACLs de Acesso para todos os itens subordinados que são criados nessa pasta. Os ficheiros não possuem ACLs Predefinidas.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

Tanto as ACLs de Acesso como as ACLs Predefinidas têm a mesma estrutura.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-2.png)



> [!NOTE]
> Alterar a ACL Predefinida num componente principal não afeta a ACL de Acesso ou a ACL Predefinida de itens subordinados que já existem.
>
>

## <a name="users-and-identities"></a>Utilizadores e identidades

Todos os ficheiros e pastas têm permissões diferentes para estas identidades:

* O utilizador proprietário do ficheiro
* O grupo proprietário
* Utilizadores nomeados
* Grupos nomeados
* Todos os outros utilizadores

As identidades dos utilizadores e grupos são identidades do Azure Active Directory (Azure AD). Assim, salvo indicado em contrário, um “utilizador”, no contexto do Data Lake Store, pode significar um utilizador ou um grupo de segurança do Azure AD.

## <a name="permissions"></a>Permissões

As permissões num objeto do sistema de ficheiros são **Leitura**, **Escrita** e **Execução** e podem ser utilizadas em ficheiros e pastas conforme mostrado na tabela seguinte:

|            |    Ficheiro     |   Pasta |
|------------|-------------|----------|
| **Leitura (R)** | Pode editar o conteúdo de um ficheiro | Requer **Leitura** e **Execução** para listar os conteúdos da pasta|
| **Escrita (W)** | Pode escrever ou acrescentar a um ficheiro | Requer **Escrita** e **Execução** para criar itens subordinados numa pasta |
| **Execução (X)** | Não tem qualquer significado no contexto do Data Lake Store | É necessário para atravessar os itens subordinados de uma pasta |

### <a name="short-forms-for-permissions"></a>Formatos curtos para as permissões

O **RWX** é utilizado para indicar **Leitura + Escrita + Execução**. Existe um formato numérico mais condensado, em que **Leitura=4**, **Escrita=2** e **Execução=1**, cuja respetiva soma representa as permissões. Abaixo, encontram-se alguns exemplos.

| Formato numérico | Formato curto |      O que representa     |
|--------------|------------|------------------------|
| 7            | RWX        | Leitura + Escrita + Execução |
| 5            | R-X        | Leitura + Execução         |
| 4            | R--        | Leitura                   |
| 0            | ---        | Sem permissões         |


### <a name="permissions-do-not-inherit"></a>As permissões não são herdadas

No modelo de estilo POSIX utilizado pelo Data Lake Store, as permissões para um item são armazenadas no próprio item. Por outras palavras, as permissões para um item não podem ser herdadas dos itens principais.

## <a name="common-scenarios-related-to-permissions"></a>Cenários comuns relacionados com as permissões

Seguem-se alguns cenários comuns para o ajudar a compreender que permissões são necessárias para executar determinadas operações numa conta do Data Lake Store.

### <a name="permissions-needed-to-read-a-file"></a>Permissões necessárias para ler um ficheiro

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Para o ficheiro ser lido, o autor da chamada precisa de permissões de **Leitura**.
* Para todas as pastas da estrutura de pastas que contêm o ficheiro, o autor da chamada precisa de permissões de **Execução**.

### <a name="permissions-needed-to-append-to-a-file"></a>Permissões necessárias para acrescentar a um ficheiro

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* Para o ficheiro ser acrescentado, o autor da chamada precisa de permissões de **Escrita**.
* Para todas as pastas que contêm o ficheiro, o autor da chamada precisa de permissões de **Execução**.

### <a name="permissions-needed-to-delete-a-file"></a>Permissões necessárias para eliminar um ficheiro

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* Para a pasta principal, o autor da chamada precisa de permissões de **Escrita + Execução**.
* Para todas as outras pastas no caminho do ficheiro, o autor da chamada precisa de permissões de **Execução**.



> [!NOTE]
> As permissões de Escrita no ficheiro não são necessárias para o eliminar, desde que as duas condições anteriores sejam verdadeiras.
>
>

### <a name="permissions-needed-to-enumerate-a-folder"></a>Permissões necessárias para enumerar uma pasta

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* Para a pasta enumerar, o autor da chamada precisa de permissões de **Leitura + Execução**.
* Para todas as pastas predecessoras, o autor da chamada precisa de permissões de **Execução**.

## <a name="viewing-permissions-in-the-azure-portal"></a>Permissões de visualização no portal do Azure

A partir do painel **Data Explorer** da conta do Data Lake Store, clique em **Acesso**, para ver as ACLs de um ficheiro ou pasta. Clique em **Acesso** para ver as ACLs da pasta **catalog**, na conta **mydatastore**.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

Neste painel, a secção superior mostra uma descrição geral das permissões que tem. (Na captura de ecrã, o utilizador é Bob.) Depois disso, são apresentadas as permissões de acesso. Depois disso, a partir do painel **Acesso**, clique em **Vista Simples** para ver a vista mais simples.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Clique em **Vista Avançada** para ver a vista mais avançada, onde são mostrados os conceitos de ACLs Predefinidas, de máscara e de superutilizador.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## <a name="the-super-user"></a>O superutilizador

O superutilizador é o que tem mais direitos entre todos os utilizadores no Data Lake Store. O superutilizador:

* Tem permissões de RWX para **todos** os ficheiros e pastas.
* Pode alterar as permissões em qualquer ficheiro ou pasta.
* Pode alterar o utilizador proprietário ou grupo proprietário de qualquer ficheiro ou pasta.

No Azure, as conta do Data Lake Store têm várias funções do Azure, incluindo:

* Proprietários
* Contribuintes
* Leitores

Qualquer pessoa na função de **Proprietários** para uma conta do Data Lake Store é automaticamente um superutilizador relativamente a essa conta. Para saber mais, veja [Controlo de acesso baseado em funções](../active-directory/role-based-access-control-configure.md).
Se quiser criar uma função de controlo de acesso baseado em funções (RBAC) com permissões de superutilizador, esta tem de ter as permissões seguintes:
- Microsoft.DataLakeStore/accounts/Superuser/action
- Microsoft.Authorization/roleAssignments/write


## <a name="the-owning-user"></a>O utilizador proprietário

O utilizador que criou o item é automaticamente o utilizador proprietário do item. Um utilizador proprietário pode:

* Alterar as permissões de um ficheiro que é propriedade.
* Alterar o grupo proprietário de um ficheiro que é propriedade, desde que o utilizador proprietário também seja membro do grupo de destino.

> [!NOTE]
> O utilizador proprietário *não pode* alterar o utilizador proprietário de outro ficheiro que é propriedade. Apenas os superutilizadores podem alterar o utilizador proprietário de um ficheiro ou pasta.
>
>

## <a name="the-owning-group"></a>O grupo proprietário

Nas ACLs POSIX, cada utilizador está associado um "grupo principal". Por exemplo, o utilizador "alice" poderá pertencer ao grupo "finanças". A Alice poderá, também, pertencer a vários grupos, mas um dos grupos será sempre o grupo principal dela. No POSIX, quando a Alice cria um ficheiro, o grupo proprietário do mesmo está definido como o grupo principal dela, que, neste caso, é "finanças".

Quando é criado um novo item do sistema de ficheiros, o Data Lake Store atribui um valor ao grupo proprietário.

* **Caso 1**: a pasta raiz "/". Esta pasta é criada quando é criada uma conta do Data Lake Store. Neste caso, o grupo proprietário está definido como o utilizador que criou a conta.
* **Caso 2** (todos os outros casos): quando é criado um item novo, o grupo proprietário é copiado da pasta principal.

O grupo proprietário pode ser alterado por:
* Qualquer superutilizador.
* Pelo utilizador proprietário, se o utilizador proprietário também for membro do grupo de destino.

## <a name="access-check-algorithm"></a>Algoritmo de verificação de acesso

A ilustração seguinte representa o algoritmo de verificação de acesso para contas do Data Lake Store.

![Algoritmo de ALCs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)


## <a name="the-mask-and-effective-permissions"></a>A máscara (mask) e as "permissões efetivas"

**Mask** é um valor RWX que é utilizado para limitar o acesso a **utilizadores nomeados**, ao **grupo proprietário** e aos **grupos nomeados**, quando estiver a efetuar o algoritmo de verificação de acesso. Eis os conceitos-chave da mask.

* A máscara cria "permissões efetivas". Isto significa que modifica as permissões no momento da verificação de acesso.
* A máscara pode ser editada diretamente pelo proprietário do ficheiro e por qualquer superutilizador.
* A máscara pode remover permissões para criar a permissão efetiva. A máscara *não pode* adicionar permissões à permissão efetiva.

Vejamos alguns exemplos. No exemplo seguinte, a máscara está definida como **RWX**, o que significa que não remove nenhuma permissão. As permissões efetivas do utilizador nomeado, do grupo proprietário e do grupo nomeado não são alteradas durante a verificação de acesso.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

No exemplo seguinte, a máscara está definida como **R-X**. Isto significa que **desativa as permissões de Escrita** do **utilizador nomeado**, do **grupo proprietário** e do **grupo nomeado** no momento da verificação de acesso.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Para referência, é aqui que é apresentada a máscara de um ficheiro ou pasta no portal do Azure.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

> [!NOTE]
> Nas contas novas do Data Lake Store, a máscara da ACL de Acesso e da ACL Predefinida da pasta raiz ("/") está predefinida como RWX.
>
>

## <a name="permissions-on-new-files-and-folders"></a>Permissões em novos ficheiros e pastas

Quando um novo ficheiro ou pasta são criados numa pasta existente, a ACL Predefinida na pasta principal determina:

- A ACL Predefinida e a ACL de Acesso da pasta subordinada.
- A ACL de Acesso do ficheiro subordinado (os ficheiros não têm ACLs Predefinidas)

### <a name="the-access-acl-of-a-child-file-or-folder"></a>A ACL de Acesso de um ficheiro ou pasta subordinado

Quando é criado um ficheiro ou pasta subordinado, a ACL Predefinida do item principal é copiada como a ACL de Acesso do ficheiro ou pasta subordinado. Além disso, se **outro** utilizador tiver permissões de RWX na ACL Predefinida do item principal, essas permissões são completamente removidas da ACL de Acesso do item subordinado.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

Na maioria dos cenários, as informações anteriores são tudo o que precisa de saber sobre como é determinada a ACL de Acesso dos itens subordinados. No entanto, se estiver familiarizado com os sistemas POSIX e pretender compreender detalhadamente como esta transformação é alcançada, consulte a secção [Função umask na criação de ACL de Acesso para novos ficheiros e pastas](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) posteriormente neste artigo.


### <a name="a-child-folders-default-acl"></a>Uma ACL Predefinida de uma pasta subordinada

Quando é criada uma pasta subordinada numa pasta principal, a ACL Predefinida da pasta principal é copiada, tal como está, para a ACL Predefinida da pasta subordinada.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-store"></a>Tópicos avançados para compreender as ACLs no Data Lake Store

Seguem-se alguns tópicos avançados para ajudar a compreender como as ACLs são determinadas para os ficheiros ou pastas do Data Lake Store.

### <a name="umasks-role-in-creating-the-access-acl-for-new-files-and-folders"></a>Função da umask na criação de ACL de Acesso para novos ficheiros e pastas

Num sistema compatível com POSIX, o conceito geral é que umask é um valor de 9 bits na pasta principal utilizado para transformar a permissão de **utilizador proprietário**, de **grupo proprietário** e de **outros** na ACL de Acesso de um ficheiro ou pasta subordinado novo. Os bits de uma umask identificam que bits desligar na ACL de Acesso do item subordinado. Deste modo, é utilizado para impedir seletivamente a propagação de permissões para **utilizador proprietário**, **grupo proprietário** e **outro**.

Num sistema do HDFS, a umask é, normalmente, uma opção de configuração ao nível do site que é controlada pelos administradores. O Data Lake Store utiliza uma **umask ao nível da conta** que não pode ser alterada. A tabela seguinte mostra a umask do Data Lake Store.

| Grupo de utilizadores  | Definição | Efeito na ACL de Acesso de novo item subordinado |
|------------ |---------|---------------------------------------|
| Utilizador proprietário | ---     | Nenhum efeito                             |
| Grupo proprietário| ---     | Nenhum efeito                             |
| Outros       | RWX     | Remover Leitura + Escrita + Execução         |

A ilustração seguinte mostra esta umask em ação. O efeito prático é remover a **Leitura + Escrita + Execução** para **outro** utilizador. Uma vez que a umask não especifica os bits para o **utilizador proprietário** e para o **grupo proprietário**, essas permissões não são transformadas.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-umask.png)

### <a name="the-sticky-bit"></a>O sticky bit

O sticky bit é uma funcionalidade mais avançada de um sistema de ficheiros POSIX. No contexto do Data Lake Store, é improvável que o sticky bit seja necessário.

A tabela seguinte mostra o funcionamento do sticky bit no Data Lake Store.

| Grupo de utilizadores         | Ficheiro    | Pasta |
|--------------------|---------|-------------------------|
| Sticky bit **DESLIGADO** | Nenhum efeito   | Nenhum efeito.           |
| Bit temporário **LIGADO**  | Nenhum efeito   | Impede qualquer pessoa, exceto os **superutilizadores** e o **utilizador proprietário** de um item subordinado, de eliminar ou alterar o nome desse item subordinado.               |

O sticky bit não é apresentado no portal do Azure.

## <a name="common-questions-about-acls-in-data-lake-store"></a>Perguntas comuns sobre as ACLs no Data Lake Store

Eis algumas questões que surgem com frequência sobre as ACLs no Data Lake Store.

### <a name="do-i-have-to-enable-support-for-acls"></a>É necessário ativar o suporte para as ACLs?

Não. O controlo de acesso através das ACLs está sempre ativado para uma conta do Data Lake Store.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Que permissões são necessárias para eliminar recursivamente uma pasta e o respetivo conteúdo?

* A pasta principal tem de ter as permissões de **Escrita + Execução**.
* A pasta a eliminar, e cada pasta dentro da mesma, requer as permissões de **Leitura + Escrita + Execução**.

> [!NOTE]
> Não precisa das permissões de Escrita para eliminar ficheiros em pastas. Além disso, a pasta raiz "/" **nunca** pode ser eliminada.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>Quem é o proprietário de um ficheiro ou pasta?

O criador de um ficheiro ou de uma pasta torna-se o proprietário.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Que grupo é definido como proprietário de um ficheiro ou pasta durante a criação?

O grupo proprietário é copiado do grupo proprietário da pasta principal sob a qual o novo ficheiro ou pasta é criado.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Sou o utilizador proprietário de um ficheiro, mas não tenho as permissões de RWX necessárias. O que posso fazer?

O utilizador proprietário pode alterar as permissões do ficheiro para atribuir as permissões de RWX necessárias a ele próprio.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Quando vejo as ACLs no portal do Azure, vejo os nomes de utilizador, mas pelas APIs vejo GUIDs. Por que motivo é que isto acontece?

As entradas nas ACLs são armazenadas como GUIDs que correspondem aos utilizadores no Azure AD. As APIs devolvem os GUIDs conforme estão. O portal do Azure tenta tornar as ACLs mais fáceis de utilizar, ao traduzir os GUIDs para nomes amigáveis sempre que possível.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Porque é que, por vezes, vejo GUIDs nas ACLs quando utilizo o portal do Azure?

Quando um utilizador deixa de existir no Azure AD, é apresentado um GUID. Normalmente, isto acontece quando o utilizador já não está na empresa ou se a conta dele tiver sido eliminada no Azure AD.

### <a name="does-data-lake-store-support-inheritance-of-acls"></a>O Data Lake Store suporta a herança de ACLs?

Não.

### <a name="what-is-the-difference-between-mask-and-umask"></a>Qual é a diferença entre mask e umask?

| mask | umask|
|------|------|
| A propriedade **mask** está disponível em todos os ficheiros e pastas. | A propriedade **umask** é uma propriedade da conta do Data Lake Store. Portanto, existe apenas uma única umask no Data Lake Store.    |
| A propriedade mask num ficheiro ou pasta pode ser alterada pelo utilizador proprietário ou grupo proprietário de um ficheiro ou por um superutilizador. | A propriedade umask não pode ser modificada por nenhum utilizador, nem mesmo por superutilizadores. É um valor constante, inalterável.|
| A propriedade mask é utilizada durante o algoritmo de verificação de acesso em runtime para determinar se um utilizador tem o direito de realizar uma operação num ficheiro ou pasta. A função da mask é criar "permissões efetivas" no momento da verificação de acesso. | A umask não é utilizada durante a verificação de acesso. A umask é utilizada para determinar a ACL de Acesso de novos itens subordinados de uma pasta. |
| A mask é um valor RWX de 3 bits que se aplica ao utilizador nomeado, ao grupo nomeado e ao utilizador proprietário no momento da verificação de acesso.| A umask é um valor de 9 bits que se aplica ao utilizador proprietário, ao grupo proprietário e a **outros** de um novo item subordinado.|

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Onde posso obter mais informações sobre o modelo de controlo de acesso POSIX?

* [POSIX Access Control Lists on Linux (Listas de Controlo de Acesso POSIX no Linux)](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html)

* [HDFS permission guide (Guia de permissões do HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

* [FAQ DO POSIX](http://www.opengroup.org/austin/papers/posix_faq.html)

* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)

* [POSIX 1003.1 2013](http://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)

* [POSIX 1003.1 2016](http://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)

* [POSIX ACL no Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)

* [ACL: using access control lists on Linux (ACL: utilizar listas de controlo de acesso no Linux)](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Consultar também

* [Descrição geral do Azure Data Lake Store](data-lake-store-overview.md)
