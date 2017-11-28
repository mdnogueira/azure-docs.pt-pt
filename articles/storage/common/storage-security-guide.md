---
title: "Guia de segurança de armazenamento do Azure | Microsoft Docs"
description: "Fornece detalhes sobre os vários métodos de proteger o Storage do Azure, incluindo mas não limitado a RBAC, encriptação do serviço de armazenamento, encriptação do lado do cliente, SMB 3.0 e Azure Disk Encryption."
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 6f931d94-ef5a-44c6-b1d9-8a3c9c327fb2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: c3973c7e529cd1d0ecd98ae17d4d979d0d458ef3
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="azure-storage-security-guide"></a>Guia de segurança de armazenamento do Azure
## <a name="overview"></a>Descrição geral
Storage do Azure fornece um conjunto completo de capacidades de segurança que em conjunto permitem aos programadores criarem aplicações seguras. A conta de armazenamento em si pode ser protegida utilizando o controlo de acesso baseado em funções e o Azure Active Directory. Dados podem ser protegidos em trânsito entre uma aplicação e o Azure utilizando [encriptação do lado do cliente](../storage-client-side-encryption.md), HTTPS ou SMB 3.0. Conjunto de dados sejam encriptados automaticamente quando escrito para utilizar o armazenamento do Azure [encriptação de serviço de armazenamento (SSE)](storage-service-encryption.md). SO e discos de dados utilizados por máquinas virtuais podem ser definidos para ser encriptado utilizando [Azure Disk Encryption](../../security/azure-security-disk-encryption.md). Pode ser concedido acesso delegado aos objetos de dados no armazenamento do Azure utilizando [assinaturas de acesso partilhado](../storage-dotnet-shared-access-signature-part-1.md).

Este artigo irá fornecer uma descrição geral de cada uma destas funcionalidades de segurança que podem ser utilizadas com o Storage do Azure. As ligações são fornecidos para artigos que irão dar-detalhes de cada funcionalidade, pode facilmente fazê-lo mais investigação em cada tópico.

Seguem-se os tópicos para ser abordadas neste artigo:

* [Gestão Plane segurança](#management-plane-security) – proteger a sua conta de armazenamento

  O plane de gestão é constituído pelos recursos utilizados para gerir a sua conta de armazenamento. Nesta secção, iremos falar sobre o modelo de implementação Azure Resource Manager e como utilizar o controlo de acesso baseado em funções (RBAC) para controlar o acesso às suas contas de armazenamento. Também irá abordadas gerir as chaves de conta de armazenamento e como voltar a gerá-los.
* [Dados Plane segurança](#data-plane-security) – proteger o acesso aos seus dados

  Nesta secção, vamos abordar permitir o acesso os objetos de dados real na sua conta de armazenamento, tais como blobs, ficheiros, filas e tabelas, utilizando as assinaturas de acesso partilhado e políticas de acesso armazenada. Abordaremos SAS de nível de serviço e o nível de conta SAS. Podemos também verá como limitar o acesso a um endereço IP específico (ou intervalo de endereços IP), como limitar o protocolo utilizado para HTTPS e como revogar uma assinatura de acesso partilhado sem aguardar que expire.
* [Encriptação em Trânsito](#encryption-in-transit)

  Esta secção descreve como proteger dados quando a transferência ou a sair do armazenamento do Azure. Iremos falar sobre a utilização recomendada de HTTPS e a encriptação utilizado pelo SMB 3.0 para partilhas de ficheiros do Azure. Iremos irá também observe encriptação do lado do cliente, que permite-lhe para encriptar os dados antes de é transferido para o armazenamento numa aplicação de cliente e para desencriptar os dados depois de serem transferidos fora do armazenamento.
* [Encriptação Inativa](#encryption-at-rest)

  Serão abordadas encriptação de serviço de armazenamento (SSE), e como pode ativá-la para uma conta de armazenamento, resultando nos blobs de blocos, blobs de páginas a ser encriptados automaticamente quando escrita ao Storage do Azure de blobs de acréscimo. Também veremos como pode utilizar o Azure Disk Encryption e explorar as diferenças básicas e casos de encriptação de disco versus SSE versus encriptação do lado do cliente. Iremos brevemente abordar a conformidade FIPS para E.U.A. Computadores Government.
* Utilizar [análise de armazenamento](#storage-analytics) para auditar o acesso do Storage do Azure

  Esta secção descreve como localizar as informações nos registos de análise de armazenamento para um pedido. Vamos observe análise de armazenamento real dados de registo e ver como discernir se é efetuado um pedido com a chave de conta de armazenamento, com uma assinatura de acesso partilhado, ou anonimamente e se foi concluída com êxito ou falha.
* [A ativação baseada no Browser clientes utilizando a CORS](#Cross-Origin-Resource-Sharing-CORS)

  Esta secção aborda como permitir que os recursos de várias origens (CORS) de partilha. Iremos falar sobre acesso entre domínios e como processá-lo com as capacidades CORS incorporadas no armazenamento do Azure.

## <a name="management-plane-security"></a>Gestão Plane segurança
O plane de gestão é constituído por operações que afetam a conta do storage. Por exemplo, pode criar ou eliminar uma conta de armazenamento, obter uma lista de contas de armazenamento numa subscrição, obter as chaves de conta de armazenamento ou voltar a gerar as chaves de conta de armazenamento.

Quando cria uma nova conta de armazenamento, selecione um modelo de implementação de clássico ou do Resource Manager. O modelo clássico de criação de recursos no Azure só permite all-or-nothing acesso à subscrição e, por sua vez, a conta de armazenamento.

Este guia centra-se no modelo do Resource Manager que é o meio recomendado para criar contas de armazenamento. Com as contas de armazenamento do Resource Manager, em vez de conceder acesso para a subscrição completa, pode controlar o acesso num nível mais finito para o plane de gestão utilizando o controlo de acesso baseado em funções (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Como proteger a sua conta de armazenamento com controlo de acesso baseado em funções (RBAC)
Vamos falar sobre o que é o RBAC e como pode utilizá-lo. Cada subscrição do Azure tem um Azure Active Directory. Os utilizadores, grupos e aplicações a partir desse diretório podem ser concedidas acesso para gerir os recursos na subscrição do Azure que utilizam o modelo de implementação Resource Manager. Isto é referido como o controlo de acesso baseado em funções (RBAC). Para gerir este acesso, pode utilizar o [portal do Azure](https://portal.azure.com/), a [ferramentas da CLI do Azure](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), ou o [APIs de REST de fornecedor de recursos de armazenamento de Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Com o modelo do Resource Manager, coloque a conta de armazenamento no acesso de grupo e o controlo de recursos para o plane gestão dessa conta de armazenamento específico utilizando o Azure Active Directory do. Por exemplo, pode dar aos utilizadores específicos a capacidade de aceder as chaves de conta de armazenamento, enquanto outros utilizadores podem ver informações sobre a conta de armazenamento, mas não é possível aceder as chaves de conta de armazenamento.

#### <a name="granting-access"></a>Conceder acesso
É concedido acesso ao atribuir a função RBAC adequada aos utilizadores, grupos e aplicações, no âmbito correto. Para conceder acesso para a subscrição completa, atribua uma função ao nível da subscrição. Pode conceder acesso a todos os recursos num grupo de recursos ao conceder permissões para o grupo de recursos em si. Também pode atribuir funções específicas a recursos específicos, tais como contas de armazenamento.

Seguem-se os pontos principais que terá de saber sobre como utilizar o RBAC para aceder as operações de gestão de uma conta de armazenamento do Azure:

* Quando atribui acesso, basicamente atribuir uma função para a conta que pretende ter acesso. Pode controlar o acesso para as operações utilizadas para gerir essa conta de armazenamento, mas não para os objetos de dados na conta. Por exemplo, pode conceder permissão para obter as propriedades da conta de armazenamento (por exemplo, redundância), mas não para um contentor ou os dados dentro de um contentor no Blob Storage.
* Alguém tem permissão para aceder a dados de objetos na conta de armazenamento, pode conceder-lhes permissão para ler as chaves de conta de armazenamento e esse utilizador pode, em seguida, utilizar essas chaves para aceder a blobs, filas, tabelas e os ficheiros.
* Funções podem ser atribuídas a uma conta de utilizador específico, um grupo de utilizadores ou para uma aplicação específica.
* Cada função tem uma lista de ações e não as ações. Por exemplo, a função de contribuinte de Máquina Virtual tem uma ação de "listKeys" que permite que as chaves de conta de armazenamento a ser lido. O contribuinte tem "Não ações" como atualizar o acesso de utilizadores do Active Directory.
* As funções de armazenamento incluem (mas não estão limitadas a) o seguinte:

  * Proprietário – podem gerir tudo, incluindo o acesso.
  * Contribuidor – que podem fazer nada, o proprietário pode exceto atribuir acesso. Alguém com esta função pode ver e voltar a gerar as chaves de conta de armazenamento. Com as chaves de conta de armazenamento, poderem aceder aos objetos de dados.
  * Leitor – podem visualizar informações sobre a conta de armazenamento, exceto os segredos. Por exemplo, se atribuir uma função com permissões de leitor na conta de armazenamento para alguém, podem visualizar as propriedades da conta de armazenamento, mas não é possível efetuar quaisquer alterações às propriedades ou ver as chaves de conta de armazenamento.
  * Contribuinte da conta de armazenamento – pode gerir a conta de armazenamento – podem ler os grupos de recursos e recursos, a subscrição e criar e gerir implementações do grupo de recursos de subscrição. Também pode aceder às chaves de conta de armazenamento, que por sua vez, significa que podem aceder a plane de dados.
  * Administrador de acesso do utilizador – podem gerir o acesso de utilizador para a conta de armazenamento. Por exemplo, estes podem conceder acesso de leitor para um utilizador específico.
  * Contribuinte de máquina virtual – podem gerir máquinas virtuais, mas não a conta de armazenamento ao qual estão ligados. Esta função pode listar as chaves de conta de armazenamento, o que significa que o utilizador a quem atribuir esta função pode atualizar o plane de dados.

    Para um utilizador criar uma máquina virtual, têm de ser capaz de criar o ficheiro VHD correspondente numa conta do storage. Para tal, têm de ser capaz de obter a chave de conta do storage e transmita-a para a API de criação de VM. Por conseguinte, têm de ter esta permissão, de modo que podem listar as chaves de conta de armazenamento.
* A capacidade de definir funções personalizadas é uma funcionalidade que permite-lhe compor um conjunto de ações a partir de uma lista de ações disponíveis que podem ser executadas nos recursos do Azure.
* O utilizador tem de ser configurado no Azure Active Directory antes de pode atribuir-lhes uma função.
* Pode criar um relatório de quem concedido/revogado que tipo de acesso para/de quem e no qual âmbito utilizando o PowerShell ou a CLI do Azure.

#### <a name="resources"></a>Recursos
* [Controlo de Acesso Baseado em Funções do Azure Active Directory](../../active-directory/role-based-access-control-configure.md)

  Este artigo explica o Controlo de Acesso Baseado em Funções do Azure Active Directory e como funciona.
* [RBAC: Funções Incorporadas](../../active-directory/role-based-access-built-in-roles.md)

  Este artigo fornece detalhes sobre todas as funções incorporadas disponíveis no RBAC.
* [Compreender a implementação do Resource Manager e a implementação clássica](../../azure-resource-manager/resource-manager-deployment-model.md)

  Este artigo explica a implementação do Resource Manager e modelos de implementação clássica e explica as vantagens de utilizar os grupos de recursos e do Resource Manager. Explica como funcionam os computação do Azure, rede e fornecedores de armazenamento sob o modelo Resource Manager.
* [Gerir o Controlo de Acesso Baseado em Funções com a API REST](../../active-directory/role-based-access-control-manage-access-rest.md)

  Este artigo mostra como utilizar a API REST para gerir o RBAC.
* [Referência de API de REST de fornecedor de recursos de armazenamento do Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Esta é a referência para as APIs que pode utilizar para gerir a sua conta de armazenamento através de programação.
* [Guia para programadores para autenticação com a API do Azure Resource Manager](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

  Este artigo mostra como efetuar a autenticação utilizando as APIs do Gestor de recursos.
* [Controlo de Acesso Baseado em Funções para o Microsoft Azure no Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Esta é uma ligação para um vídeo do Channel 9 da conferência Ignite 2015 MS. Nesta sessão, fala-se sobre as capacidades de gestão de acesso e de criação de relatórios no Azure e são exploradas as melhores práticas relativamente à proteção do acesso às subscrições do Azure com o Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Gerir as chaves de conta de armazenamento
Chaves de conta de armazenamento são cadeias de 512 bits criadas pelo Azure que, juntamente com o nome da conta de armazenamento, pode ser utilizado para aceder os objetos de dados armazenados na conta de armazenamento, por exemplo, blobs, entidades dentro de uma tabela, fila de mensagens e ficheiros numa partilha de ficheiros do Azure. Controlar o acesso para o armazenamento conta chaves controla o acesso a plane os dados para essa conta de armazenamento.

Cada conta de armazenamento tem duas chaves referidas como "Chave 1" e «Chave 2» no [portal do Azure](http://portal.azure.com/) e nos cmdlets do PowerShell. Estes podem ser regenerados manualmente utilizando um dos vários métodos, incluindo, mas não limitado a utilizar o [portal do Azure](https://portal.azure.com/), PowerShell, a CLI do Azure, ou através de programação utilizando a biblioteca de clientes de armazenamento do .NET ou a API de REST de serviços de armazenamento do Azure.

Existem diversas razões para voltar a gerar as chaves de conta de armazenamento.

* Pode voltar a gerá-los de forma regular por motivos de segurança.
* Pretende voltar a gerar as chaves de conta de armazenamento se alguém gerido hack numa aplicação e obter a chave que foi codificado ou guardada no ficheiro de configuração, concedendo-lhes acesso total à sua conta de armazenamento.
* Outro cenário para regeneração da chave é se a equipa está a utilizar um Explorador de armazenamento de aplicação que mantém a chave de conta de armazenamento, e sai de um dos membros do agrupamento. A aplicação iria continuar a funcionar, conceder acesso à sua conta do storage depois forem removidos. Isto é, na verdade, a principal razão criaram assinaturas de acesso partilhado de nível de conta – pode utilizar um SAS de nível de conta em vez de armazenar as chaves de acesso num ficheiro de configuração.

#### <a name="key-regeneration-plan"></a>Plano de regeneração da chave
Não pretende apenas regenerar a chave que estiver a utilizar sem algum planeamento. Se, fazê-lo, foi possível cortar desativar todo o acesso a essa conta de armazenamento, o que pode provocar a interrupção principais. É por isso existem duas chaves. Deve voltar a gerar uma chave de cada vez.

Antes de voltar a gerar as chaves, lembre-se de que tem uma lista de todas as aplicações que dependem da conta de armazenamento, bem como quaisquer outros serviços que está a utilizar no Azure. Por exemplo, se estiver a utilizar o Azure Media Services dependentes da sua conta do storage, deve sincronizar novamente as chaves de acesso com o serviço de multimédia depois de voltar a gerar a chave. Se estiver a utilizar quaisquer aplicações, tais como o Explorador de armazenamento, terá de fornecer as chaves para essas aplicações, bem como novo. Tenha em atenção que se tiver VMs cujos ficheiros VHD são armazenados na conta de armazenamento, este será não ser afetado por regenerar as chaves de conta de armazenamento.

Pode voltar a gerar as chaves no portal do Azure. Depois das chaves são regeneradas poderem demorar até 10 minutos a ser sincronizados em todos os serviços de armazenamento.

Quando estiver pronto, eis o processo geral com detalhes sobre como deve alterar a chave. Neste caso, pressuposto é que estiver a utilizar atualmente chave 1 e pretender alterar tudo para utilizar em vez disso, a chave 2.

1. Regenere a chave 2 para se certificar de que é seguro. Pode fazê-lo no portal do Azure.
2. Em todas as aplicações onde está armazenada a chave de armazenamento, altere a chave de armazenamento a utilizar o valor da chave 2 de novo. Testar e publicar a aplicação.
3. Após todos os das aplicações e serviços estão operacionais e em execução com êxito, voltar a gerar chave 1. Isto garante que qualquer pessoa a quem que não expressamente atribuiu a nova chave deixará de ter acesso à conta de armazenamento.

Se estiver a utilizar a chave 2, pode utilizar o mesmo processo, mas inverter os nomes de chaves.

Pode migrar através de alguns dias, a alteração de cada aplicação para utilizar a nova chave e publicar. Depois de terminar, deve, em seguida, volte atrás e regenerar a chave antiga, pelo que já não funciona.

Outra opção consiste em colocar a chave de conta do storage num [Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/) como um segredo e tem as suas aplicações obter a chave a partir daí. Em seguida, quando regenerar a chave e atualizar o Cofre de chaves do Azure, as aplicações não terá de voltar a implementar porque estes selecionará a nova chave de Cofre de chaves do Azure automaticamente. Tenha em atenção que pode fazer com que a aplicação de ler a chave de cada vez que o ficheiro necessário ou -lo em cache na memória e se não conseguir ao utilizá-la, obter a chave novamente partir do Cofre de chaves do Azure.

Utilizar o Cofre de chaves do Azure também adiciona a outro nível de segurança para as chaves de armazenamento. Se utilizar este método, nunca terá o codificado de chave de armazenamento num ficheiro de configuração, que elimina essa compromissos alguém obter acesso às chaves sem permissão específico.

Outra vantagem de utilizar o Cofre de chaves do Azure é que também pode controlar o acesso às suas chaves utilizando o Azure Active Directory. Isto significa que pode conceder acesso a alguns das aplicações que necessitam para obter as chaves a partir do Cofre de chaves do Azure e saiba que outras aplicações não conseguir as chaves de acesso sem conceder-lhes permissão especificamente.

Nota: se recomenda utilizar apenas uma das chaves em todas as suas aplicações, ao mesmo tempo. Se utilizar a chave 1 em alguns locais e a chave 2 em outros, não será possível rodar as chaves sem alguma perda de acesso de aplicação.

#### <a name="resources"></a>Recursos
* [Sobre as contas de armazenamento do Azure](storage-create-storage-account.md#regenerate-storage-access-keys)

  Este artigo fornece uma descrição geral de contas do storage e descreve ver, copiar e regenerar chaves de acesso de armazenamento.
* [Referência de API de REST de fornecedor de recursos de armazenamento do Azure](https://msdn.microsoft.com/library/mt163683.aspx)

  Este artigo contém ligações para artigos específicos sobre como obter as chaves de conta de armazenamento e voltar a gerar as chaves de conta de armazenamento para uma conta do Azure utilizando a API REST. Nota: Isto é para contas de armazenamento do Resource Manager.
* [Operações em contas de armazenamento](https://msdn.microsoft.com/library/ee460790.aspx)

  Este artigo de referência de API de REST de Gestor do serviço de armazenamento contém ligações para artigos específicas sobre a obtenção e voltar a gerar as chaves de conta de armazenamento utilizando a API REST. Nota: Isto é para as contas de armazenamento clássico.
* [Diga goodbye para gestão de chaves – gerir o acesso aos dados de armazenamento do Azure com o Azure AD](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

  Este artigo mostra como utilizar o Active Directory para controlar o acesso para as chaves de armazenamento do Azure no Cofre de chaves do Azure. Também mostra como utilizar uma tarefa de automatização do Azure para voltar a gerar as chaves de cada hora.

## <a name="data-plane-security"></a>Segurança dos dados Plane
Segurança dos dados Plane refere-se os métodos utilizados para proteger os objetos de dados armazenados no Storage do Azure – os blobs, filas, tabelas e ficheiros. Iremos viu métodos para encriptar os dados e segurança durante trânsito dos dados, mas como deve proceder controlar o acesso aos objetos?

Existem dois métodos para autorizar o acesso aos objetos dados próprios. Estes incluem-se ao controlar o acesso às chaves de conta de armazenamento e utilizar assinaturas de acesso partilhado para conceder acesso a objetos de dados específico durante um período de tempo específico.

Além disso, para o Blob Storage, pode permitir acesso público para os blobs ao definir o nível de acesso para o contentor que retém os blobs em conformidade. Se definir acesso para um contentor de Blob ou contentor, permitirá o acesso de leitura público para os blobs no contentor. Isto significa que qualquer pessoa com um URL a apontar para um blob no contentor pode abrir num browser sem utilizar uma assinatura de acesso partilhado ou ter as chaves de conta de armazenamento.

Para além de limitar o acesso através de autorização, também pode utilizar [Firewalls e redes virtuais](storage-network-security.md) para limitar o acesso à conta de armazenamento com base nas regras de rede.  Permite esta abordagem negar o acesso ao tráfego de internet públicas e para conceder acesso apenas a determinados redes virtuais do Azure ou da internet pública intervalos de endereços IP.


### <a name="storage-account-keys"></a>Chaves de Contas de Armazenamento
Chaves de conta de armazenamento são criadas pelo Azure que, juntamente com o nome da conta de armazenamento, pode ser utilizado para aceder os objetos de dados armazenados na conta de armazenamento de cadeias de 512 bits.

Por exemplo, pode de leitura de blobs, escrever em filas, criar tabelas e modifique os ficheiros. Muitas destas ações podem ser efetuadas através do portal do Azure, ou utilizar uma das muitas aplicações de Explorador de armazenamento. Também pode escrever código para utilizar a API REST ou uma das bibliotecas de cliente de armazenamento para efetuar estas operações.

Tal como explicado na secção no [gestão Plane segurança](#management-plane-security), acesso às chaves de armazenamento para uma conta de armazenamento clássicas pode ser concedida, concedendo acesso total à subscrição do Azure. Acesso às chaves de armazenamento para uma conta de armazenamento utilizando o modelo Azure Resource Manager pode ser controlado através de controlo de acesso baseado em funções (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Como delegar o acesso a objetos na sua conta utilizar assinaturas de acesso partilhado e armazenados políticas de acesso
Uma assinatura de acesso partilhado é uma cadeia que contém um token de segurança que pode ser anexado a um URI que lhe permite delegar o acesso a objetos de armazenamento e especificar as restrições, como as permissões e o intervalo de data/hora de acesso.

Pode conceder acesso a contentores, mensagens de filas, ficheiros, tabelas e blobs. Com tabelas, na realidade, pode conceder permissão para aceder a um intervalo de entidades na tabela, especificando os intervalos de chaves partição e da fila a que pretende que o utilizador tenha acesso. Por exemplo, se tiver dados armazenados com uma chave de partição do Estado geográfica, poderia conceder alguém acesso para apenas os dados para Califórnia.

Noutro exemplo, pode dar um token SAS, que lhe permite escrever entradas de uma fila de uma aplicação web e atribuir uma função de trabalho a aplicação de função um token SAS para receber mensagens da fila e processá-los. Ou, pode dar um cliente um token SAS, que podem utilizar para carregar imagens para um contentor no Blob Storage e atribuir uma permissão de aplicação web para ler as imagens. Em ambos os casos, há uma separação das preocupações – cada aplicação pode ser especificada apenas o acesso que necessitam para efetuar as respetivas tarefas. Isto é possível através da utilização de assinaturas de acesso partilhado.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Por que motivo que pretende utilizar assinaturas de acesso partilhado
Por que motivo seria que pretende utilizar um SAS em vez de apenas dar a sua chave de conta do storage, o que é por isso, muito mais fácil? Dar a sua chave de conta do storage é como as chaves do seu Unido de armazenamento de partilha. -Concede acesso completo. Alguém foi utilizar as chaves e carregar a sua biblioteca de música toda a sua conta do storage. Que podem substituir os ficheiros infetados de vírus versões ou roubar os seus dados. Fornecer ausente acesso ilimitado à sua conta do storage é algo que não devem ser levados ligeiramente.

Com assinaturas de acesso partilhado, pode dar um cliente apenas as permissões necessárias para um período limitado de tempo. Por exemplo, se alguém está a carregar um blob para a sua conta, pode conceder-lhes acesso de escrita para o tempo apenas suficiente para carregar o blob (consoante o tamanho do blob, obviamente). E se mudar de ideias, pode revogar esse acesso.

Além disso, pode especificar que os pedidos efetuados com uma SAS estão limitados a um determinado endereço IP ou intervalo de endereços IP externo para o Azure. Também pode exigir que são efetuados os pedidos de utilização de um protocolo específico (HTTPS ou HTTP/HTTPS). Isto significa que se pretende permitir o tráfego HTTPS, que pode definir apenas o protocolo necessário para HTTPS e o tráfego HTTP será bloqueado.

#### <a name="definition-of-a-shared-access-signature"></a>Definição de uma assinatura de acesso partilhado
Uma assinatura de acesso partilhado é um conjunto de parâmetros de consulta anexado ao URL apontar o recurso

que fornece informações sobre o acesso permitido e o período de tempo para o qual o acesso é permitido. Eis um exemplo; Este URI fornece acesso de leitura para um blob para cinco minutos. Tenha em atenção que SAS os parâmetros de consulta tem de ser codificados de URL, como % 3A para vírgula (:) ou % 20 para um espaço.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>Como a assinatura de acesso partilhado é autenticada pelo serviço de armazenamento do Azure
Quando o serviço de armazenamento recebe o pedido, aceita os parâmetros de consulta de entrada e cria uma assinatura utilizando o mesmo método que o programa de chamada. Em seguida, compara as assinaturas de dois. Se estes concordarem, o serviço de armazenamento pode verificar a versão do serviço de armazenamento certificar-se de que é válido, certifique-se de que a data e hora atuais dentro da janela especificada, certifique-se o acesso solicitado corresponde de pedidos efetuados, etc.

Por exemplo, com os nosso URL acima, se o URL foi apontar para um ficheiro em vez de um blob, este pedido iria falhar porque Especifica que a assinatura de acesso partilhado é para um blob. Se o comando REST que está a ser chamado atualizar um blob, irão falhar porque a assinatura de acesso partilhado Especifica que o acesso de leitura apenas é permitido.

#### <a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de acesso partilhado
* Uma SAS de nível de serviço pode ser utilizada para aceder a recursos específicos numa conta do storage. Alguns exemplos deste estão a obter uma lista de blobs num contentor, transferir um blob, atualizar uma entidade numa tabela, adicionando as mensagens a uma fila ou carregar um ficheiro para uma partilha de ficheiros.
* Uma SAS de nível de conta pode ser utilizado para aceder a tudo o que pode ser utilizado um SAS de nível de serviço para. Além disso, deu opções para recursos que não são permitidas com uma SAS de nível de serviço, tais como a capacidade de criar contentores, tabelas, filas e partilhas de ficheiros. Também pode especificar o acesso a vários serviços de uma só vez. Por exemplo, pode dar alguém aceder aos blobs e os ficheiros na sua conta de armazenamento.

#### <a name="creating-an-sas-uri"></a>Criar um URI de SAS
1. Pode criar um URI ad hoc a pedido, definir todos os parâmetros de consulta de cada vez.

   Este é realmente flexível, mas se tiver um conjunto lógico de parâmetros que são semelhantes a cada hora, através de uma política de acesso armazenada uma ideia mais precisa.
2. Pode criar uma política de acesso armazenada para um contentor inteiro, a partilha de ficheiros, a tabela ou fila. Em seguida, pode utilizar isto como base para o URI de SAS que cria. As permissões com base nas políticas de acesso armazenada podem ser facilmente revogadas. Pode ter até 5 políticas definidas em cada contentor, fila, tabela ou partilha de ficheiros.

   Por exemplo, se que foram tem muitas pessoas leiam os blobs num contentor específico, pode criar uma política de acesso armazenada que diz "conceder acesso de leitura" e outras definições que serão os mesmos cada vez. Em seguida, pode criar um URI de SAS utilizando as definições da política de acesso armazenada e especificando a data/hora de expiração. A vantagem é que não tem de especificar todos os parâmetros de consulta sempre.

#### <a name="revocation"></a>Revogação
Suponha que o SAS tiver sido comprometido ou se quiser alterá-lo devido a requisitos de conformidade de regulamentação ou a segurança da empresa. Como a revogar o acesso a um recurso utilizando esse SAS? Depende de como que criou o URI de SAS.

Se estiver a utilizar o ad hoc URI, tem três opções. Pode emitir tokens SAS com políticas de expiração curto e aguarde simplesmente a SAS expirar. Pode mudar o nome ou eliminar o recurso (assumindo que o token foi incluído um único objeto). Pode alterar as chaves de conta de armazenamento. Esta última opção pode ter um grande impacto, dependendo de quantos serviços utilizar essa conta de armazenamento e provavelmente não é algo que pretende fazer sem algum planeamento.

Se estiver a utilizar um SAS derivada de uma política de acesso armazenada, pode remover o acesso ao revogar a política de acesso armazenada – apenas pode alterá-la, pelo que já expirou ou pode removê-la completamente. Isto entra em vigor imediatamente e invalida todos os SAS criadas com essa política de acesso armazenada. Atualizar ou remover a política de acesso armazenada poderão pessoas impacto ao aceder ao contentor específico, partilha de ficheiros, tabela ou fila através de SAS, mas se os clientes são escritos pelo solicitarem um SAS novo quando o antigo um fica inválido, isto irá funcionar ajustar.

Uma vez com uma SAS derivada de uma política de acesso armazenada dá-lhe a capacidade para revogar esse SAS imediatamente, é recomendável utilizar sempre armazenadas as políticas de acesso sempre que possível.

#### <a name="resources"></a>Recursos
Para obter informações mais detalhadas sobre como utilizar assinaturas de acesso partilhado e armazenadas as políticas de acesso, completa com exemplos, consulte os artigos seguintes:

* Estes são os artigos de referência.

  * [Serviço SAS](https://msdn.microsoft.com/library/dn140256.aspx)

    Este artigo fornece exemplos de como utilizar um SAS de nível de serviço com ficheiros, intervalos de tabela, fila de mensagens e blobs.
  * [Construir um serviço SAS](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Construir uma conta SAS](https://msdn.microsoft.com/library/mt584140.aspx)
* Estes são os tutoriais para criar assinaturas de acesso partilhado e políticas de acesso armazenada utilizando a biblioteca de clientes .NET.

  * [Utilizar assinaturas de acesso partilhado (SAS)](../storage-dotnet-shared-access-signature-part-1.md)
  * [Partilhado assinaturas de acesso, parte 2: Criar e utilizar um SAS com o serviço Blob](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Este artigo inclui uma explicação sobre o modelo SAS, exemplos de assinaturas de acesso partilhado, e recomendações para a melhor prática de utilização de SAS. Também abordadas é a revogação de permissão concedida.

* Autenticação

  * [Autenticação para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Tutorial de introdução de assinaturas de acesso partilhado

  * [Tutorial de introdução de SAS](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Encriptação em trânsito
### <a name="transport-level-encryption--using-https"></a>Encriptação de nível de transporte – através de HTTPS
Outro passo que deve tomar para garantir a segurança dos seus dados de armazenamento do Azure é para encriptar os dados entre o cliente e o armazenamento do Azure. A recomendação primeiro consiste em utilizar sempre o [HTTPS](https://en.wikipedia.org/wiki/HTTPS) protocolo, que garante uma comunicação segura através da Internet pública.

Para que um canal de comunicação segura, deve sempre utilizar HTTPS quando chamar as APIs REST ou aceder aos objetos no armazenamento. Além disso, **assinaturas de acesso partilhado**, que pode ser utilizado para delegar o acesso a objetos de armazenamento do Azure, que incluem uma opção para especificar que apenas o protocolo HTTPS pode ser utilizado quando utilizar assinaturas de acesso partilhado, garantindo que qualquer pessoa enviar ligações com SAS tokens irá utilizar o protocolo correto.

Pode impor a utilização de HTTPS quando chamar as APIs REST para aceder aos objetos em contas de armazenamento, permitindo [Secure transferência necessária](../storage-require-secure-transfer.md) para a conta de armazenamento. Ligações utilizando HTTP irão recusou-se quando esta estiver ativada.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Utilizar a encriptação durante trânsito com partilhas de ficheiros do Azure
Ficheiros do Azure suporta HTTPS ao utilizar a API REST, mas é mais frequentemente utilizado como uma partilha de ficheiros SMB ligado a uma VM. SMB 2.1 não suporta a encriptação, pelo que as ligações só são permitidas na mesma região no Azure. No entanto, SMB 3.0 suporta a encriptação e está disponível no Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10, permitindo por várias regiões aceder e até mesmo acesso no ambiente de trabalho.

Tenha em atenção que enquanto partilhas de ficheiros do Azure podem ser utilizadas com o Unix, o cliente Linux SMB ainda não suporta encriptação, para que acesso só é permitido dentro de uma região do Azure. Suporte de encriptação de Linux está no plano de programadores de Linux responsáveis pela funcionalidade SMB. Quando adicionarem estes encriptação, terá a mesma capacidade para aceder a uma partilha de ficheiros do Azure no Linux, tal como para o Windows.

Pode impor a utilização da encriptação com o serviço de ficheiros do Azure, permitindo [Secure transferência necessária](../storage-require-secure-transfer.md) para a conta de armazenamento. Se utilizar as APIs REST, HTTPs é necessário. Para SMB, apenas as ligações de SMB que suportem encriptação que irão estabelecer ligação com êxito.

#### <a name="resources"></a>Recursos
* [Introdução de ficheiros do Azure](../files/storage-files-introduction.md)
* [Introdução ao Azure ficheiros no Windows](../files/storage-how-to-use-files-windows.md)

  Este artigo fornece uma descrição geral das partilhas de ficheiros do Azure e como montar e utilizá-los no Windows.

* [How to use Azure Files with Linux (Como utilizar os Ficheiros do Azure com o Linux)](../files/storage-how-to-use-files-linux.md)

  Este artigo mostra como montar uma partilha de ficheiros do Azure nos ficheiros de sistema e de carregamento/transferência de Linux.

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Utilizar a encriptação do lado do cliente para proteger os dados que envie para o armazenamento
Outra opção que o ajuda a garantir que os dados estão seguros durante a transferência entre uma aplicação de cliente e o armazenamento é a encriptação do lado do cliente. Os dados são encriptados antes de serem transferidos para o armazenamento do Azure. Quando a obtenção de dados do Storage do Azure, os dados são desencriptados depois recebido do lado do cliente. Apesar dos dados são encriptados vai através da transmissão, é recomendável que também utilizar HTTPS, porque tem verificações de integridade de dados incorporadas que ajudam a mitigar os erros de rede que afetam a integridade dos dados.

A encriptação do lado do cliente também é um método para encriptar os dados inativos, como os dados são armazenados na sua forma encriptada. Iremos falar sobre esta mais detalhadamente na secção no [encriptação de Inativos](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Encriptação de Inativos
Existem três funcionalidades do Azure que fornecem encriptação de inativos. Encriptação de disco do Azure é utilizada para encriptar os SO e discos de dados em máquinas de virtuais do IaaS. Os outros dois – SSE – e a encriptação do lado do cliente estão ambos utilizada para encriptar dados no armazenamento do Azure. Vamos examinar cada uma destas e, em seguida, não uma comparação e ver quando cada um deles pode ser utilizado.

Apesar de poder utilizar a encriptação do lado do cliente para encriptar os dados em trânsito (que também é armazenado no respetivo formato encriptado no armazenamento), poderá preferir simplesmente utilizar HTTPS durante a transferência e ter alguma forma dos dados sejam encriptados automaticamente quando esta está armazenada. Existem duas formas de fazê-lo – Azure Disk Encryption e SSE. Um é utilizado para encriptar diretamente os dados no SO e discos de dados utilizados por VMs e o outro é utilizado para encriptar os dados escritos para armazenamento de Blobs do Azure.

### <a name="storage-service-encryption-sse"></a>Encriptação do serviço de armazenamento (SSE)
SSE permite-lhe pedido que o serviço de armazenamento encriptar automaticamente os dados ao escrever no armazenamento do Azure. Ao ler os dados do armazenamento do Azure, vai ser desencriptado pelo serviço de armazenamento antes de a ser devolvido. Isto permite-lhe proteger os dados sem ter de modificar o código ou adicione o código para todas as aplicações.

Esta é uma definição aplica-se à conta de armazenamento de todo. Pode ativar e desativar esta funcionalidade alterando o valor da definição. Para tal, pode utilizar o portal do Azure, PowerShell, a CLI do Azure, a API de REST de fornecedor de recursos de armazenamento ou a biblioteca de clientes de armazenamento do .NET. Por predefinição, o SSE está desativada.

Neste momento, as chaves utilizadas para a encriptação são geridas pela Microsoft. Vamos gerar as chaves originalmente e gerir o armazenamento seguro das chaves, bem como a rotação regular, tal como definido pela política interna do Microsoft. No futuro, irá obter a capacidade de gerir as suas próprias chaves de encriptação e fornecer um caminho de migração a partir das chaves gerida pela Microsoft para chaves gerida pelo cliente.

Esta funcionalidade está disponível para Standard e Premium Storage contas criadas utilizando o modelo de implementação Resource Manager. SSE aplica-se apenas aos blobs, os blobs de páginas de blocos e blobs de acréscimo. Outros tipos de dados, incluindo tabelas, filas e ficheiros, não serão encriptados.

Dados apenas são encriptados quando SSE está ativada e os dados são escritos para o Blob Storage. Ativar ou desativar SSE não afeta a dados existentes. Por outras palavras, quando ativar esta encriptação, possa não voltar atrás e encriptar os dados que já existe nem será-desencriptar os dados que já existe ao desativar SSE.

Se pretender utilizar esta funcionalidade com uma conta de armazenamento do clássico, pode criar uma nova conta de armazenamento do Resource Manager e utilizar o AzCopy para copiar os dados para a nova conta.

### <a name="client-side-encryption"></a>Encriptação do lado do cliente
Encriptação do lado do cliente foi mencionado quando debater a encriptação dos dados em trânsito. Esta funcionalidade permite-lhe programaticamente encriptar os dados numa aplicação de cliente antes de a enviar através da transmissão escrita ao Storage do Azure e através de programação desencriptar os dados após a obtenção-lo a partir do armazenamento do Azure.

Isto fornece encriptação em trânsito, mas também fornece a funcionalidade de encriptação de inativos. Tenha em atenção que apesar dos dados são encriptados em trânsito, ainda Recomendamos que utilize HTTPS para tirar partido das verificações de integridade dos dados incorporados que ajudam a mitigar os erros de rede que afetam a integridade dos dados.

É um exemplo de onde pode utilizar esta opção se tiver uma aplicação web que armazena os blobs e obtém os blobs e pretende que a aplicação e os dados como seguro quanto possível. Nesse caso, utilizaria encriptação do lado do cliente. O tráfego entre o cliente e o serviço de Blob do Azure contém o recurso encriptado e ninguém pode interpretar os dados em trânsito e reconstitute-lo para os blobs privados.

Encriptação do lado do cliente está incorporada no Java e as bibliotecas de cliente de armazenamento do .NET, que por sua vez, utilize as APIs de cofre da chave do Azure, facilitando pretty para que possa implementar. O processo de encriptação e desencriptação de dados utiliza a técnica envelope e armazena os metadados utilizados pela encriptação em cada objeto de armazenamento. Por exemplo, para blobs, armazena-lo nos metadados do blob, enquanto para filas, adiciona-la para cada mensagem de fila.

Para a encriptação de si próprio, pode gerar e gerir as suas próprias chaves de encriptação. Também pode utilizar as chaves geradas pelo biblioteca de cliente do Storage do Azure ou pode fazer com que o Cofre de chaves do Azure gerar as chaves. Pode armazenar as chaves de encriptação no seu armazenamento de chaves no local ou, pode armazená-las num cofre de chaves do Azure. O Cofre de chaves do Azure permite-lhe conceder acesso para os segredos no Cofre de chaves do Azure para utilizadores específicos, com o Azure Active Directory. Isto significa que não apenas a qualquer pessoa pode ler o Cofre de chaves do Azure e obter as chaves que está a utilizar para a encriptação do lado do cliente.

#### <a name="resources"></a>Recursos
* [Encriptar e desencriptar blobs no armazenamento do Microsoft Azure com o Cofre de chaves do Azure](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Este artigo mostra como utilizar a encriptação do lado do cliente com o Cofre de chaves do Azure, incluindo como criar a KEK e armazene-o num cofre com o PowerShell.
* [Encriptação do lado do cliente e o Azure Cofre de chaves para o armazenamento do Microsoft Azure](../storage-client-side-encryption.md)

  Este artigo fornece uma explicação de encriptação do lado do cliente e fornece exemplos de como utilizar a biblioteca de clientes de armazenamento para encriptar e desencriptar recursos os quatro dos serviços de armazenamento. Também aborda o Cofre de chaves do Azure.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Utilizar o Azure Disk Encryption para encriptar os discos utilizados pela suas máquinas virtuais
Encriptação de disco do Azure é uma funcionalidade nova. Esta funcionalidade permite-lhe encriptar os discos de SO e discos de dados utilizados pela máquina Virtual IaaS. Para o Windows, as unidades estão encriptadas com tecnologia de encriptação do BitLocker de norma da indústria. Para Linux, os discos estão encriptados com a tecnologia de DM-Crypt. Isto é integrado com o Cofre de chaves do Azure para permitem controlar e gerir as chaves de encriptação de disco.

A solução suporta os seguintes cenários para VMs de IaaS quando são ativados no Microsoft Azure:

* Integração com o Cofre de chaves do Azure
* O escalão Standard VMs: [A, D, DS, G, GS e série Sim forth VMs do IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Ativar a encriptação em Windows e as VMs de IaaS Linux
* A desativação da encriptação nos dados e SO unidades para as VMs de IaaS Windows
* A desativação da encriptação em unidades de dados para as VMs de IaaS Linux
* Ativar a encriptação em VMs de IaaS que estão a executar SO de cliente do Windows
* Ativar a encriptação em volumes com caminhos de montagem
* Ativar a encriptação em VMs do Linux que estão configurados com o disco striping (RAID) utilizando mdadm
* Ativar a encriptação em VMs do Linux utilizando LVM para discos de dados
* Ativar a encriptação em VMs do Windows que são configuradas através da utilização de espaços de armazenamento
* Todas as regiões públicas do Azure são suportadas

A solução não suporta os seguintes cenários, funcionalidades e tecnologias na versão:

* Escalão básico VMs do IaaS
* A desativação da encriptação numa unidade do SO para as VMs de IaaS Linux
* VMs de IaaS que são criados utilizando o método de criação de VM clássico
* Integração com o serviço de gestão de chaves no local
* Ficheiros do Azure (sistema de ficheiros partilhados), o sistema de ficheiros de rede (NFS), volumes dinâmicos e VMs do Windows que estão configurados com sistemas RAID baseados em software


> [!NOTE]
> Encriptação de disco de SO Linux é atualmente suportada nas seguintes distribuições de Linux: RHEL 7.2, CentOS 7.2n e Ubuntu 16.04.
>
>

Esta funcionalidade garante que todos os dados nos seus discos da máquina virtual são encriptados em pausa no armazenamento do Azure.

#### <a name="resources"></a>Recursos
* [Encriptação de disco do Azure para o Windows e as VMs de Linux IaaS](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Comparação de encriptação de disco do Azure, SSE e encriptação do lado do cliente
#### <a name="iaas-vms-and-their-vhd-files"></a>VMs de IaaS e os respetivos ficheiros VHD
Para discos utilizados por VMs de IaaS, recomendamos a utilização do Azure Disk Encryption. Pode ativar o SSE para encriptar os ficheiros VHD que são utilizados para fazer uma cópia destes discos no armazenamento do Azure, mas, encripta apenas dados recentemente gravados. Isto significa que se criar uma VM e, em seguida, ativar SSE na conta de armazenamento que contém o ficheiro VHD, apenas as alterações serão encriptadas, não o ficheiro VHD original.

Se criar uma VM utilizando uma imagem do Azure Marketplace, o Azure executa um [shallow cópia](https://en.wikipedia.org/wiki/Object_copying) da imagem para o armazenamento de conta de armazenamento do Azure e não está encriptada, mesmo se tiver SSE ativada. Depois da VM de cria e inicia a atualização da imagem, SSE começará a encriptar os dados. Por este motivo, é melhor utilizar o Azure Disk Encryption em VMs criadas a partir de imagens no Azure Marketplace se de que quer que eles totalmente encriptados.

Se trouxer uma VM previamente encriptada no Azure no local, será possível carregar as chaves de encriptação para o Cofre de chaves do Azure e continuar a utilizar a encriptação para essa VM que estava a utilizar no local. Encriptação de disco do Azure está ativada para processar este cenário.

Se tiver o VHD não encriptadas no local, pode carregá-lo para a galeria como uma imagem personalizada e aprovisionar uma VM a partir do mesmo. Se a fazê-lo utilizando os modelos do Resource Manager, pode colocá-la para ativar o Azure Disk Encryption quando arrancarem a VM.

Quando adicionar um disco de dados e montá-la na VM, pode ativar o Azure Disk Encryption esse disco de dados. Esta encriptará primeiro esse disco de dados localmente e, em seguida, a camada de gestão de serviço executará uma operação de escrita em Diferido com o armazenamento para que o conteúdo do armazenamento é encriptado.

#### <a name="client-side-encryption"></a>Encriptação do lado do cliente
A encriptação do lado do cliente é o método mais seguro de encriptar os dados, dado que encripta-a antes de trânsito e encripta os dados inativos. No entanto, requerem que adicione código às suas aplicações a utilizar o armazenamento, o que poderá pretender fazer. Nesses casos, pode utilizar o HTTPs para os seus dados em trânsito e SSE para encriptar os dados inativos.

Com a encriptação do lado do cliente, pode encriptar as entidades da tabela, fila de mensagens e os blobs. Com SSE, apenas pode encriptar blobs. Se precisar de tabela e fila dados sejam encriptados, deve utilizar a encriptação do lado do cliente.

Encriptação do lado do cliente é gerida inteiramente pela aplicação. Esta é a abordagem mais segura, mas tem de efetuar alterações programáticas à sua aplicação e colocar os processos de gestão de chaves no local. Pretende utilizar este quando pretender que a segurança adicional durante trânsito e pretende que os dados armazenados sejam encriptados.

A encriptação do lado do cliente é mais carga no cliente e tem de ser uma conta para isto no seu plano de escalabilidade, especialmente se estiver a encriptação e a transferência de uma grande quantidade de dados.

#### <a name="storage-service-encryption-sse"></a>Encriptação do serviço de armazenamento (SSE)
SSE é gerida pelo armazenamento do Azure. Utilização de SSE não fornece para garantir a segurança dos dados em trânsito, mas encriptar os dados que que é escritos no armazenamento do Azure. Não há nenhum impacto no desempenho quando utilizar esta funcionalidade.

Apenas pode encriptar blobs de blocos, blobs de acréscimo e utilizar SSE de blobs de páginas. Se precisar de encriptar dados da tabela ou fila, deve considerar a utilização de encriptação do lado do cliente.

Se tiver um arquivo ou biblioteca de arquivos VHD que utilizar como base para a criação de novas máquinas virtuais, pode criar uma nova conta de armazenamento, ativar SSE e, em seguida, carregue ficheiros VHD a essa conta. Esses ficheiros VHD serão encriptados pelo armazenamento do Azure.

Se tiver ativado para os discos no SSE ativada na conta de armazenamento que contém os ficheiros VHD e VM do Azure Disk Encryption, que funcionará ajustar; irá resultar num quaisquer dados escritos recentemente a ser encriptados duas vezes.

## <a name="storage-analytics"></a>Análise de Armazenamento
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Análise de armazenamento a utilizar para monitorizar o tipo de autorização
Para cada conta de armazenamento, pode ativar a análise de armazenamento do Azure efetuar o registo e armazenar dados de métricas. Esta é uma ótima ferramenta para utilizar quando pretende verificar as métricas de desempenho de uma conta de armazenamento ou precisam de resolver uma conta de armazenamento porque estão a ter problemas de desempenho.

Outro conjunto de dados, pode ver nos registos de análise do armazenamento é o método de autenticação utilizado por outra quando acedem ao armazenamento. Por exemplo, com o Blob Storage, pode ver se utilizaram uma assinatura de acesso partilhado ou as chaves de conta de armazenamento ou se o blob acedido foi público.

Isto pode ser realmente útil se totalmente são guarding acesso ao armazenamento. Por exemplo, no Blob Storage pode definir todos os contentores para private e implementar a utilização de um serviço SAS em toda as suas aplicações. Em seguida, pode verificar os registos regularmente para ver se os blobs são acedidos utilizando as chaves de conta de armazenamento, o que podem indicar uma violação de segurança, ou se os blobs são públicos, mas não deve ser.

#### <a name="what-do-the-logs-look-like"></a>Os registos de aspeto?
Depois de ativar as métricas de conta de armazenamento e dados de análise de registo através do portal do Azure, irão começar a acumular rapidamente. O registo e as métricas para cada serviço está separada; o registo é escrito apenas quando existe atividade nessa conta de armazenamento, enquanto as métricas serão registadas a cada minuto, a cada hora ou todos os dias, dependendo de como configurar.

Os registos são armazenados em blobs de blocos num contentor com o nome $logs na conta de armazenamento. Este contentor é criado automaticamente quando a análise de armazenamento está ativada. Uma vez criado neste contentor, que não é possível eliminá-lo, apesar de poder eliminar o respetivo conteúdo.

No recipiente do $logs, existe uma pasta para cada serviço, e, em seguida, existem subpastas para a ano/mês/dia/hora. Na hora, os registos são simplesmente numerados. Esta é a estrutura de diretórios será aspeto:

![Vista de ficheiros de registo](./media/storage-security-guide/image1.png)

Todos os pedidos para o armazenamento do Azure é registado. Eis um instantâneo de um ficheiro de registo, que mostra os primeiros campos alguns.

![Instantâneo de um ficheiro de registo](./media/storage-security-guide/image2.png)

Pode ver o que pode utilizar os registos para controlar a qualquer tipo de chamadas para uma conta de armazenamento.

#### <a name="what-are-all-of-those-fields-for"></a>Quais são todas essas campos para?
Não há um artigo listado nos recursos abaixo que fornece a lista das muitos dos campos nos registos e o que são utilizadas. Eis a lista de campos por ordem:

![Instantâneo de campos num ficheiro de registo](./media/storage-security-guide/image3.png)

Estamos interessados em entradas para GetBlob, e a forma como os que são autenticados, por isso, é necessário procure entradas com "Get-BLOBs" de tipo de operação e verifique o estado do pedido (4<sup>ésimo</sup> coluna) e o tipo de autorização (8<sup>ésimo</sup> coluna).

Por exemplo, as primeiras linhas na listagem acima, o estado do pedido é "Êxito" e o tipo de autorização é "autenticar". Isto significa que o pedido foi validado com a chave de conta de armazenamento.

#### <a name="how-are-my-blobs-being-authenticated"></a>Como são os meus blobs que está a ser autenticados?
Temos três cenários que, se estiver interessados em.

1. O blob é público e é acedido através de um URL sem uma assinatura de acesso partilhado. Neste caso, o estado do pedido é "AnonymousSuccess" e o tipo de autorização é "anónimo".

   1.0; 2015-11-17T02:01:29.0488963Z; GetBlob; **AnonymousSuccess**200; 124; 37; **anónimo**; mystorage...
2. O blob é privado e foi utilizado com uma assinatura de acesso partilhado. Neste caso, o estado do pedido é "SASSuccess" e o tipo de autorização é "sas".

   1.0; 2015-11-16T18:30:05.6556115Z; GetBlob; **SASSuccess**; 200; 416 64; **SAs**; mystorage...
3. O blob é privado e a chave de armazenamento foi utilizada para aceder ao mesmo. Neste caso, o estado do pedido é "**êxito**"e o tipo de autorização é"**autenticado**".

   1.0; 2015-11-16T18:32:24.3174537Z; GetBlob; **Êxito**; 206; 59 22; **autenticado**; mystorage...

Pode utilizar o Microsoft Message Analyzer para ver e analisar estes registos. Inclui capacidades de pesquisa e filtrar. Por exemplo, pode querer pesquisar instâncias de GetBlob para ver se a utilização é o que esperar, ou seja, para se certificar de que alguém está não aceder à sua conta do storage inadequada.

#### <a name="resources"></a>Recursos
* [Análise de Armazenamento](../storage-analytics.md)

  Este artigo é uma descrição geral da análise de armazenamento e como ativá-los.
* [Formato de registo de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Este artigo ilustra o formato de registo de análise de armazenamento e fornece detalhes sobre os campos disponíveis nas mesmas, incluindo tipo de autenticação, que indica o tipo de autenticação utilizado para o pedido.
* [Monitorizar uma conta de armazenamento no portal do Azure](../storage-monitor-storage-account.md)

  Este artigo mostra como configurar a monitorização de métricas e registo de uma conta de armazenamento.
* [Resolução de problemas de ponto-a-ponto utilizando as métricas do Storage do Azure e o registo, o AzCopy e o Message Analyzer](../storage-e2e-troubleshooting.md)

  Este artigo aborda a resolução de problemas com a análise de armazenamento e mostra como utilizar o Microsoft Message Analyzer.
* [Guia de funcionamento de analisador de mensagens da Microsoft](https://technet.microsoft.com/library/jj649776.aspx)

  Este artigo é uma referência para o Microsoft Message Analyzer e inclui ligações para um tutorial, o início rápido e o resumo da funcionalidade.

## <a name="cross-origin-resource-sharing-cors"></a>Partilha de Recursos Transversais à Origem (CORS)
### <a name="cross-domain-access-of-resources"></a>Acesso entre domínios de recursos
Quando um browser em execução num domínio faz um pedido HTTP para um recurso de outro domínio, isto denomina-se um pedido HTTP de várias origens. Por exemplo, uma página HTML servida a partir contoso.com faz um pedido para um jpeg alojado no fabrikam.blob.core.windows.net. Por motivos de segurança, os browsers restrinja os pedidos HTTP de várias origens iniciados a partir de scripts, como JavaScript. Isto significa que quando esse jpeg no fabrikam.blob.core.windows.net os pedidos de código JavaScript numa página web em contoso.com, o browser não permitem o pedido.

O que este tem de fazer com o Storage do Azure? Se estiver a armazenar recursos estáticos, tais como ficheiros de dados JSON ou XML no Blob Storage através de uma conta de armazenamento denominado Fabrikam, o domínio para os recursos fabrikam.blob.core.windows.net, e a aplicação de web contoso.com não conseguirá aceder aos mesmos utilizando JavaScript porque os domínios são diferentes. Isto também se aplica se está a tentar chamar um dos serviços de armazenamento do Azure – por exemplo, o Table Storage – que devolver dados JSON a serem processados pelo cliente de JavaScript.

#### <a name="possible-solutions"></a>Possíveis soluções
Uma forma para resolver este problema é a atribuição um domínio personalizado, como "storage.contoso.com" a fabrikam.blob.core.windows.net. O problema é que só é possível atribuir esse domínio personalizado para uma conta de armazenamento. E se os recursos são armazenados em várias contas de armazenamento?

Outra forma de resolver este problema é ter a aplicação web atuar como um proxy para as chamadas de armazenamento. Isto significa que o se estiver a carregar um ficheiro para o Blob Storage, a aplicação web teria gravá-lo localmente e, em seguida, copie-o para o Blob Storage ou seria possível ler todos os-la na memória e, em seguida, gravá-lo para o Blob Storage. Em alternativa, pode escrever uma aplicação web dedicado (por exemplo, uma API Web) que carrega os ficheiros localmente e escreve-los para o Blob Storage. Qualquer forma, tem em conta que funcionam quando precisa de determinar a escalabilidade.

#### <a name="how-can-cors-help"></a>Como pode ajudar a CORS?
Armazenamento do Azure permite-lhe ativar o CORS – cruzam a partilha de recursos de origem. Para cada conta de armazenamento, pode especificar os domínios que podem aceder aos recursos na conta do storage. Por exemplo, no nosso caso descrito acima, podemos pode ativar a CORS na conta de armazenamento fabrikam.blob.core.windows.net e configurá-la para permitir o acesso a contoso.com. Em seguida, contoso.com de aplicação web pode aceder diretamente ao recursos fabrikam.blob.core.windows.net.

Um aspeto a ter em atenção é o que permite o acesso CORS, mas não fornece autenticação, o que é necessária para todos os não-acesso público de recursos de armazenamento. Isto significa que só pode aceder blobs se forem públicos ou incluir uma assinatura de acesso partilhado que lhe confere a permissão adequada. As tabelas, filas e os ficheiros têm sem acesso de público e exigem uma SAS.

Por predefinição, a CORS está desativada em todos os serviços. Pode ativar a CORS utilizando a API REST ou biblioteca de clientes do storage para chamar um dos métodos para definir as políticas de serviço. Quando, fazê-lo, incluir uma regra CORS, que está a ser XML. Eis um exemplo de uma regra CORS que foi definido com a operação definir propriedades de serviço para o serviço Blob para uma conta de armazenamento. Pode efetuar essa operação utilizando a biblioteca de clientes de armazenamento ou as APIs REST para o Storage do Azure.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Eis o que significa que cada linha:

* **AllowedOrigins** Isto indica quais os domínios que não correspondente podem pedir e receber dados do serviço de armazenamento. Isto indica que contoso.com e fabrikam.com podem pedir a dados de armazenamento de BLOBs para uma conta de armazenamento específico. Também pode definir esta opção para um caráter universal (\*) para permitir que todos os domínios para pedidos de acesso.
* **AllowedMethods** esta é a lista de métodos (verbos de pedido HTTP) que podem ser utilizados quando efetua o pedido. Neste exemplo, são permitidas apenas PUT e GET. Pode definir esta opção para um caráter universal (\*) para permitir que todos os métodos ser utilizado.
* **AllowedHeaders** trata os cabeçalhos de pedido que o domínio de origem pode especificar quando efetua o pedido. Neste exemplo, todos os cabeçalhos de metadados a partir x-ms-meta-data, x-ms-meta-destino e x-ms-meta-abc permitidos. O caráter universal (\*) indica que o início qualquer cabeçalho com o prefixo especificado é permitido.
* **ExposedHeaders** Isto indica que os cabeçalhos de resposta devem ser expostos pelo browser para o emissor de pedido. Neste exemplo, qualquer cabeçalho começados por "x-ms - meta-" irá ser exposto.
* **MaxAgeInSeconds** esta é a quantidade máxima de tempo que um browser serão colocados em cache o pedido de opções de verificação prévia. (Para obter mais informações sobre o pedido de verificação prévia, consulte o artigo primeiro abaixo).

#### <a name="resources"></a>Recursos
Para obter mais informações sobre CORS e como ativá-la, consulte estes recursos.

* [Recursos de várias origens (CORS) suporte para os serviços de armazenamento do Azure no Azure.com de partilha](../storage-cors-support.md)

  Este artigo fornece uma descrição geral de CORS e como configurar as regras para os serviços de armazenamento diferente.
* [Recursos de várias origens (CORS) suporte para os serviços de armazenamento do Azure no MSDN de partilha](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Esta é a documentação de referência para o suporte CORS para os serviços de armazenamento do Azure. Isto tem ligações para artigos de cada serviço de armazenamento e mostra um exemplo e explica cada elemento no ficheiro de CORS.
* [Storage do Microsoft Azure: CORS de introdução](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Esta é uma ligação para o artigo de blogue inicial anunciar CORS e como utilizá-lo.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Perguntas mais frequentes sobre a segurança de armazenamento do Azure
1. **Como verificar a integridade dos blobs que posso estou transferir ou a sair do armazenamento do Azure se posso não é possível utilizar o protocolo HTTPS?**

   Se por qualquer motivo, que terá de utilizar HTTP em vez de HTTPS e estiver a trabalhar com blobs de blocos, pode utilizar a verificar o MD5 para ajudar a validar a integridade dos blobs a serem transferidos. Isto irá ajudar com proteção de erros de camada de transporte/rede, mas não necessariamente com ataques intermediário.

   Se é possível utilizar HTTPS, que fornece segurança ao nível do transporte, em seguida, a utilização de MD5 a verificação é redundante e desnecessários.

   Para obter mais informações, consulte o [descrição geral do Azure Blob MD5](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **O que sobre a conformidade FIPS para EUA Governo dos EUA?**

   O Federal informações processamento padrão FIPS (United States) define os algoritmos criptográficos aprovados para utilização pelos E.U.A. Governo Federal dos sistemas de para a proteção de dados confidenciais. Ativar FIPS modo num ambiente de trabalho do Windows server ou indica o SO que devem ser utilizados apenas validados FIPS algoritmos criptográficos. Se uma aplicação utiliza algoritmos não compatíveis, as aplicações irão interromper. Versões de With.NET Framework 4.5.2 ou superior, a aplicação muda automaticamente os algoritmos de criptografia para utilizar algoritmos compatíveis com FIPS quando o computador está no modo FIPS.

   Microsoft mantém até cada cliente para decidir se pretende ativar o modo FIPS. Acreditamos que não é não existe nenhuma razão apelativa para os clientes que não são sujeitos a normas government para ativar o modo FIPS por predefinição.

   **Recursos**

* [Por que motivo está a não recomendamos "Modo FIPS" já](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Este artigo de blogue fornece uma descrição geral do FIPS e explica por que motivo não ativar modo FIPS por predefinição.
* [O FIPS 140 validação](https://technet.microsoft.com/library/cc750357.aspx)

  Este artigo fornece informações sobre como produtos da Microsoft e os módulos criptográficos está em conformidade com a norma FIPS para EUA Governo Federal dos EUA.
* ["Criptografia de sistema: Utilize FIPS algoritmos compatíveis com para a encriptação, hashing e iniciar sessão" efeitos de definições de segurança no Windows XP e em versões posteriores do Windows](https://support.microsoft.com/kb/811833)

  Este artigo aborda a utilização do modo FIPS no computadores mais antigas do Windows.
