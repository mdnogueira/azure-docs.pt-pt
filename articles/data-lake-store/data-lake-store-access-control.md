<properties
   pageTitle="Descrição geral de Controlo de Acesso no Data Lake Store | Microsoft Azure"
   description="Compreender o controlo de acesso no Azure Data Lake Store"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/06/2016"
   ms.author="nitinme"/>

# Controlo de acesso no Azure Data Lake Store

O Data Lake Store implementa um modelo de controlo de acesso que deriva do HDFS e, por sua vez, do modelo de controlo de acesso POSIX. Este artigo resume as noções básicas do modelo de controlo de acesso para o Data Lake Store. Para saber mais sobre o modelo de controlo de acesso HDFS consulte [Guia de Permissões HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## Listas de controlo de acesso em ficheiros e pastas

Existem dois tipos de listas de Controlo de acesso (ACLs) -  **ACLs de Acesso** e **ACLs Predefinidas**.

* **ACLs de Acesso** – Controlam o acesso a um objeto. Tanto os Ficheiros como as Pastas têm ACLs de Acesso.

* **ACLs Predefinidas** – Um "modelo" de ACLs associado a uma pasta que determina as ACLs de Acesso para todos os itens subordinados criados nessa pasta. Os ficheiros não possuem ACLs Predefinidas.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

Tanto as ACLs de Acesso como as ACLs Predefinidas têm a mesma estrutura.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-2.png)

>[AZURE.NOTE] Alterar a ACL Predefinida num componente principal não afeta a ACL de Acesso ou a ACL Predefinida de itens subordinados que já existem.

## Utilizadores e identidades

Todos os ficheiros e pastas têm permissões diferentes para estas identidades:

* O utilizador proprietário do ficheiro
* O grupo proprietário
* Utilizadores nomeados
* Grupos nomeados
* Todos os outros utilizadores

As identidades dos utilizadores e dos grupos são identidades do Azure Active Directory (AAD), portanto, salvo indicação em contrário, um "utilizador", no contexto do Data Lake Store, pode significar um utilizador do AAD ou um grupo de segurança do AAD.

## Permissões

As permissões num objeto do sistema de ficheiros são **Leitura**, **Escrita** e **Execução** e podem ser utilizadas em ficheiros e pastas conforme mostrado na tabela abaixo.

|            |    Ficheiro     |   Pasta |
|------------|-------------|----------|
| **Leitura (R)** | Pode editar o conteúdo de um ficheiro | Requer **Leitura** e **Execução** para listar os conteúdos da pasta.|
| **Escrita (W)** | Pode escrever ou acrescentar a um ficheiro | Requer **Escrita e Execução** para criar itens subordinados numa pasta. |
| **Execução (X)** | Não tem qualquer significado no contexto do Data Lake Store | É necessário para atravessar os itens subordinados de uma pasta. |

### Formatos curtos para as permissões

O **RWX**é utilizado para indicar **Leitura + Escrita + Execução**. Existe um formato numérico condensado em que **Leitura=4**, **Escrita=2** e **=1** e a respetiva soma representa as permissões. Abaixo encontram-se alguns exemplos.

| Formato numérico | Formato curto |      O que representa     |
|--------------|------------|------------------------|
| 7            | RWX        | Leitura + Escrita + Execução |
| 5            | R-X        | Leitura + Execução         |
| 4            | R--        | Leitura                   |
| 0            | ---        | Sem permissões         |


### As permissões não são herdadas

No modelo de estilo POSIX utilizado pelo Data Lake Store, as permissões para um item são armazenadas no próprio item. Por outras palavras, as permissões para um item não podem ser herdadas dos itens principais.

## Cenários comuns relacionados com as permissões

Seguem-se alguns cenários comuns para compreender que permissões são necessárias para executar determinadas operações numa conta do Data Lake Store.

### Permissões necessárias para ler um ficheiro

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Para o ficheiro ser lido - o autor da chamada necessita de permissões de **Leitura**
* Para todas as pastas da estrutura de pastas que contêm o ficheiro - o autor da chamada necessita de permissões de **Execução**

### Permissões necessárias para acrescentar a um ficheiro

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* Para o ficheiro ser acrescentado - o autor da chamada necessita de permissões de **Escrita**
* Para todas as pastas que contêm o ficheiro - o autor da chamada necessita de permissões de **Execução**

### Permissões necessárias para eliminar um ficheiro

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* Para a pasta principal - o autor da chamada necessita de permissões de **Escrita + Execução**
* Para todas as outras pastas no caminho do ficheiro - o autor da chamada necessita de permissões de **Execução**

>[AZURE.NOTE] As permissões de Escrita no ficheiro não são necessárias para eliminar o ficheiro desde que as duas condições acima sejam verdadeiras.

### Permissões necessárias para enumerar uma pasta

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* Para a pasta enumerar - o autor da chamada necessita de permissões de **Leitura + Execução**
* Para todas as pastas predecessoras - o autor da chamada necessita de permissões de **Execução**

## Permissões de visualização no portal do Azure

A partir do painel **Data Explorer** da conta do Data Lake Store, clique em **Acesso** para ver as ACLs para um ficheiro ou para uma pasta. Na captura de ecrã abaixo, clique em Aceder para ver as ACLs para a pasta **Catálogo** na conta **mydatastore**.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

Depois disso, a partir do painel **Acesso**, clique em **Vista Simples** para ver a vista mais simples.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Clique em **Vista Avançada** para ver a vista mais avançada.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## O superutilizador

Um superutilizador é o que tem mais direitos entre todos os utilizadores no Data Lake Store. Um utilizador super:

* tem permissões de RWX para **todos** os ficheiros e pastas
* pode alterar as permissões em qualquer pasta ou ficheiro
* pode alterar o utilizador proprietário ou grupo proprietário de qualquer pasta ou ficheiro.

No Azure, uma conta do Data Lake Store tem várias funções do Azure:

* Proprietários
* Contribuintes
* Leitores
* Etc.

Qualquer pessoa na função de **Proprietários** para uma conta do Data Lake Store é automaticamente um superutilizador relativamente a essa conta. Para saber mais sobre o Controlo de Acesso Baseado em Funções (RBAC), consulte [Controlo de acesso baseado em funções](../active-directory/role-based-access-control-configure.md).

## O utilizador proprietário

O utilizador que criou o item é automaticamente o utilizador proprietário do item. Um utilizador proprietário pode:

* Alterar as permissões de um ficheiro que é propriedade
* Alterar o grupo proprietário de um ficheiro que é propriedade, desde que o utilizador proprietário também seja membro do grupo de destino.

>[AZURE.NOTE] O utilizador proprietário **não pode** alterar o utilizador proprietário de outro ficheiro que é propriedade Apenas os superutilizadores podem alterar o utilizador proprietário de um ficheiro ou pasta.

## O grupo proprietário

Nas ACLs POSIX, cada utilizador está associado um "grupo principal". Por exemplo, o utilizador "alice" poderá pertencer ao grupo "finanças". Alice poderá pertencer a vários grupos, mas um dos grupos será sempre o seu grupo principal. No POSIX, quando a Alice cria um ficheiro, o grupo proprietário do mesmo está definido para o grupo principal dela, que neste caso é "finanças".
 
Quando é criado um novo item do sistema de ficheiros, o Data Lake Store atribui um valor ao grupo proprietário. 

* **Caso 1** -A pasta raiz "/". Esta pasta é criada quando é criada uma conta do Data Lake Store. Neste caso, o grupo proprietário está definido para o utilizador que criou a conta.
* **Caso 2** (todos os outros casos) - Quando é criado um novo item, o grupo proprietário é copiado da pasta principal.

O grupo proprietário pode ser alterado por:
* Qualquer superutilizador
* Pelo utilizador proprietário, se o utilizador proprietário também for membro do grupo de destino.

## Algoritmo de verificação de acesso

A ilustração seguinte representa o algoritmo de verificação de acesso para contas do Data Lake Store.

![Algoritmo de ALCs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)


## A mask e as "permissões efetivas"

A **mask** é um valor RWX que é utilizado para limitar o acesso para **utilizadores nomeados**, para o **grupo proprietário** e para os **grupos nomeados** ao efetuar o algoritmo de Verificação de Acesso. Eis os conceitos chave da mask. 

* A mask cria "permissões efetivas", ou seja, modifica as permissões no momento da Verificação de Acesso.
* Uma mask pode ser editada diretamente pelo proprietário do ficheiro e por qualquer superutilizador.
* Uma mask tem a capacidade de remover permissões para criar a permissão efetiva. Uma mask **não pode** adicionar permissões à permissão efetiva. 

Vejamos alguns exemplos. Abaixo, a mask está definida como **RWX**, o que significa que a mask não remove quaisquer permissões. Tenha em atenção que as permissões efetivas para um utilizador nomeado, para um grupo proprietário e para um grupo nomeado não são alteradas durante a verificação de acesso.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

No exemplo abaixo, a mask está definida como **R-X**. Portanto, **desliga a permissão de Escrita** para o **utilizador nomeado**, para o **grupo proprietário** e para o **grupo nomeado** no momento da verificação de acesso.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Para referência, eis onde a mask para uma pasta ou um ficheiro é apresentada no Portal do Azure.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

>[AZURE.NOTE] Para uma nova conta do Data Lake Store, a mask para a ACL de Acesso e para a ACL Predefinida da pasta raiz ("/") está predefinida como RWX.

## Permissões em novos ficheiros e pastas

Quando um novo ficheiro ou pasta são criados numa pasta existente, a ACL Predefinida na pasta principal determina:

* Uma ACL Predefinida e uma ACL de Acesso da pasta subordinada
* Uma ACL de Acesso de um ficheiro subordinado (os ficheiros não possuem uma ACL Predefinida)

### Uma ACL de Acesso de uma pasta ou ficheiro subordinados

Quando é criada uma pasta ou ficheiro subordinados, a ACL Predefinida da principal é copiada como ACL de Acesso da pasta ou ficheiro subordinados. Além disso, se **outro** utilizador teve permissões de RWX na ACL Predefinida da principal, é completamente removido da ACL de Acesso do item subordinado.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

Na maioria dos cenários, as informações acima são tudo o que necessita de saber sobre a forma como a ACL de Acesso de um item subordinado é determinada. No entanto, se estiver familiarizado com os sistemas POSIX e pretender compreender detalhadamente como esta transformação é alcançada, consulte a secção [Função umask na criação de ACL de Acesso para novos ficheiros e pastas](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) posteriormente neste artigo.
 

### Uma ACL Predefinida de uma pasta subordinada

Quando uma pasta subordinada é criada numa pasta principal, a ACL Predefinida da pasta principal é copiada, tal como está, para a ACL Predefinida da pasta subordinada.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## Tópicos avançados para compreender as ACLs no Data Lake Store

Seguem-se alguns dos tópicos avançados para ajudar a compreender como as ACLs são determinadas para os ficheiros ou pastas do Data Lake Store.

### Função da umask na criação de ACL de Acesso para novos ficheiros e pastas

Num sistema compatível com POSIX, o conceito geral é que a umask é um valor de 9 bits na pasta principal utilizada para transformar a permissão para **utilizador proprietário**, **grupo proprietário** e **outros** numa nova ACL de Acesso de uma pasta ou ficheiro subordinados. Os bits de uma umask identificam que bits desligar na ACL de Acesso do item subordinado. Deste modo, isto é utilizado para seletivamente impedir a propagação de permissões para o utilizador proprietário, para o grupo proprietário e para outros.
  
Num sistema de HDFS, a umask é normalmente uma opção de configuração ao nível do site que é controlada por administradores. O Data Lake Store utiliza uma **umask ao nível da conta** que não pode ser alterada. A tabela seguinte mostra a umask do Data Lake Store.

| Grupo de utilizadores  | Definição | Efeito na ACL de Acesso de novo item subordinado |
|------------ |---------|---------------------------------------|
| Utilizador proprietário | ---     | Nenhum efeito                             |
| Grupo proprietário| ---     | Nenhum efeito                             |
| Outros       | RWX     | Remover Leitura + Escrita + Execução         | 

A ilustração seguinte mostra esta umask em ação. O efeito prático é remover a **Leitura + Escrita + Execução** para **outro** utilizador. Uma vez que a umask não especifica os bits para o **utilizador proprietário** e para o **grupo proprietário**, essas permissões não são transformadas.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-umask.png) 

### O sticky bit

O sticky bit é uma funcionalidade mais avançada de um sistema de ficheiros POSIX. No contexto do Data Lake Store, é improvável que o sticky bit seja necessário.

A tabela abaixo mostra como o sticky bit funciona no Data Lake Store.

| Grupo de utilizadores         | Ficheiro    | Pasta |
|--------------------|---------|-------------------------|
| Sticky bit **DESLIGADO** | Nenhum efeito   | Nenhum efeito           |
| Bit temporário **LIGADO**  | Nenhum efeito   | Impede qualquer pessoa, exceto os **superutilizadores** e o **utilizador proprietário** de um item subordinado, de eliminar ou alterar o nome desse item subordinado.               |

O sticky bit não é apresentado no Portal do Azure.

## Perguntas comuns sobre as ACLs no Data Lake Store

Eis algumas questões que surgem com frequência no que respeita às ACLs no Data Lake Store.

### É necessário ativar o suporte para as ACLs?

Não. O controlo de acesso através das ACLs está sempre ativado para uma conta do Data Lake Store.

### Que permissões são necessárias para eliminar recursivamente uma pasta e o respetivo conteúdo?

* A pasta principal tem de ter **Escrita + Execução**.
* A pasta a eliminar e cada pasta dentro da mesma, requer **Leitura + Escrita + Execução**.
>[AZURE.NOTE] Eliminar os ficheiros em pastas não requer Escrita nesses ficheiros. Além disso, a pasta raiz "/" **nunca** pode ser eliminada.

### Quem é definido como o proprietário de um ficheiro ou pasta?

O criador de um ficheiro ou de uma pasta torna-se o proprietário.

### Quem é definido como o grupo proprietário de um ficheiro ou de uma pasta durante a criação?

É copiado do grupo proprietário da pasta principal sob a qual o novo ficheiro ou pasta são criados.

### Sou o utilizador proprietário de um ficheiro, mas não tenho as permissões de RWX necessárias. O que posso fazer?

O utilizador proprietário pode simplesmente alterar as permissões do ficheiro para atribuir as permissões RWX necessárias.

### O Data Lake Store suporta a herança de ACLs?

Não.

### Qual é a diferença entre mask e umask?

| mask | umask|
|------|------|
| A propriedade **mask** está disponível em todos os ficheiros e pastas. | A propriedade **umask** é uma propriedade da conta do Data Lake Store. Portanto, existe apenas uma única umask no Data Lake Store.    |
| A propriedade mask num ficheiro ou pasta pode ser alterada pelo utilizador proprietário ou grupo proprietário de um ficheiro ou por um superutilizador. | A propriedade umask não pode ser modificada por nenhum utilizador, nem por um superutilizador. É um valor constante, inalterável.|
| A propriedade mask é utilizada durante o algoritmo de Verificação de Acesso em runtime para determinar se um utilizador tem o direito de realizar uma operação num ficheiro ou pasta. A função da mask é criar "permissões efetivas" no momento da verificação de acesso. | A umask não é utilizada durante a Verificação de Acesso. A umask é utilizada para determinar a ACL de Acesso de novos itens subordinados de uma pasta. |
| A mask é um valor RWX de 3 bits que se aplica ao utilizador nomeado, ao grupo nomeado e ao utilizador proprietário no momento da verificação de acesso.| A umask é um valor de 9 bits que se aplica ao utilizador proprietário, ao grupo proprietário e a outros de um novo subordinado.| 

### Onde posso obter mais informações sobre o modelo de controlo de acesso POSIX?

* [http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html)

* [Guia de Permissões do HDFS](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) 

* [FAQ DO POSIX](http://www.opengroup.org/austin/papers/posix_faq.html)

* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)

* [POSIX 1003.1e 1997](http://users.suse.com/~agruen/acl/posix/Posix_1003.1e-990310.pdf)

* [ACL POSIX no Linux](http://users.suse.com/~agruen/acl/linux-acls/online/)

* [ACL a utilizar Listas de Controlo de Acesso no Linux](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## Consultar também

* [Descrição geral do Azure Data Lake Store](data-lake-store-overview.md)

* [Introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)








<!--HONumber=sep16_HO2-->


