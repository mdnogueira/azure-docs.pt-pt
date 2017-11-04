---
title: Lotus Domino Connector | Microsoft Docs
description: Este artigo descreve como configurar Lotus Domino Connector da Microsoft.
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: e07fd469-d862-470f-a3c6-3ed2a8d745bf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/119/2017
ms.author: barclayn
ms.openlocfilehash: 15155fd9e1ab2dd6d58bcaf85a465c0585d3bc41
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="lotus-domino-connector-technical-reference"></a>Referência técnica do Lotus Domino Connector
Este artigo descreve o conector do Lotus Domino. O artigo aplica-se os seguintes produtos:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Tem de utilizar correção 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2, o conector está disponível como uma transferência a partir do [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Descrição geral do Lotus Domino Connector
O conector do Lotus Domino permite-lhe integrar o serviço de sincronização com o servidor de Lotus Domino do IBM.

Numa perspetiva de alto nível, as seguintes funcionalidades são suportadas pela versão atual do conector:

| Funcionalidade | Suporte |
| --- | --- |
| Origem de dados ligada |Servidor: <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>Cliente:<li>Lotus Domino 8.5.x</li><li>Lotus Notes 9.x</li> |
| Cenários |<li>Gestão de ciclo de vida do objeto</li><li>Gestão de grupo</li><li>Gestão de palavra-passe</li> |
| Operações |<li>Completo e a importação Delta</li><li>Exportar</li><li>Definir e alterar palavra-passe na palavra-passe HTTP</li> |
| Esquema |<li>Pessoa (utilizador de Roaming, contacte (pessoas com nenhum certificado))</li><li>Grupo</li><li>Recursos (Reunião Online de recursos, espaço)</li><li>No correio de base de dados</li><li>Deteção dinâmica de atributos de objetos suportados</li><li>Suporta até 250 certifiers personalizados com uma organização & unidades organizacionais (UO)</li> |

O conector do Lotus Domino utiliza o cliente Lotus notas para comunicar com Lotus Domino servidor. Como consequência, esta dependência de um cliente de notas do Lotus suportado tem de estar instalado no servidor de sincronização. A comunicação entre o cliente e o servidor é implementada através da interface do Lotus notas .NET Interop (Interop.domino.dll). Esta interface facilita a comunicação entre o cliente Lotus notas e Microsoft.NET plataforma e suporta o acesso a documentos Lotus Domino e vistas. Importação delta, também é possível que a interface nativa C++ é utilizada (consoante o método de importação delta selecionado).

### <a name="prerequisites"></a>Pré-requisitos
Antes de utilizar o conector, certifique-se de que tem os seguintes pré-requisitos no servidor de sincronização:

* Arquitetura do Microsoft .NET 4.5.2 ou posterior
* O cliente Lotus notas tem de estar instalado no seu servidor de sincronização
* O conector do Lotus Domino requer a predefinição Lotus Domino LDAP esquema da base de dados (schema.nsf) esteja presente no servidor de diretório Domino. Se não estiver presente, pode instalá-lo ao executar ou ao reiniciar o serviço LDAP no servidor de Domino.

### <a name="connected-data-source-permissions"></a>Permissões de origem de dados ligadas
Para executar qualquer uma das tarefas suportadas no conector Lotus Domino, tem de ser um membro dos grupos seguintes:

* Administradores do acesso completos
* Administradores
* Administradores de base de dados

A tabela seguinte lista as permissões que são necessárias para cada operação:

| Operação | Direitos de acesso |
| --- | --- |
| Importar |<li>Leia os documentos públicos</li><li> Administrador de acesso total (quando é membro do grupo de administradores do acesso total, automaticamente tem o acesso efetivo na ACL.)</li> |
| Exportar e definir a palavra-passe |Acesso Efetivo: <li>Criar documentos</li><li>Eliminar documentos</li><li>Leia os documentos públicos</li><li>Escrever documentos públicos</li><li>Replicar ou cópia documentos</li>Para operações de exportação, também terá das seguintes funções: <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li> |

### <a name="direct-operations-and-adminp"></a>Operações diretas e AdminP
Operações ou vá diretamente para o diretório de Domino ou através do processo de AdminP. As tabelas seguintes objetos de lista de todos os suportado, operações e, se aplicável, o método de implementação relacionados:

**Livro de endereços primário**

| Objeto | Criar | Atualizar | Eliminar |
| --- | --- | --- | --- |
| Pessoa |AdminP |Direto |AdminP |
| Grupo |AdminP |Direto |AdminP |
| MailInDB |Direto |Direto |Direto |
| Recurso |AdminP |Direto |AdminP |

**Livro de endereços secundário**

| Objeto | Criar | Atualizar | Eliminar |
| --- | --- | --- | --- |
| Pessoa |N/D |Direto |Direto |
| Grupo |Direto |Direto |Direto |
| MailInDB |Direto |Direto |Direto |
| Recurso |N/D |N/D |N/D |

Quando um recurso é criado, é criado um documento de notas. Da mesma forma, quando um recurso é eliminado, o documento de notas é eliminado.

### <a name="ports-and-protocols"></a>Portas e protocolos
IBM Lotus notas cliente e os servidores de Domino comunicam utilizando notas remoto procedimento chamar (NRPC) onde NRPC deve utilizar TCP/IP. O número de porta predefinido é 1352, mas pode ser alterado pelo administrador Domino.

### <a name="not-supported"></a>Não suportado
As seguintes operações não são suportadas pela versão atual do conector Lotus Domino:

* Mova a caixa de correio entre servidores.

## <a name="create-a-new-connector"></a>Criar um novo conector
### <a name="client-software-installation-and-configuration"></a>Configuração e instalação do Software de cliente
Notas de Lotus tem de estar instaladas no servidor **antes** o conector está instalado.

Quando instalar, certifique-se que efetue uma **único utilizador instalar**. A predefinição **multiutilizador instalar** não funciona.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

Na página de funcionalidades, instale apenas as funcionalidades necessárias do Lotus notas e **início de sessão único de cliente**. É necessário para o conector conseguir iniciar sessão servidor Domino o início de sessão único.  
![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Nota:** conta de serviço de início Lotus notas depois com um utilizador que está localizado no mesmo servidor que a conta utiliza como o conector. Certifique-se também fechar o cliente Lotus notas no servidor. Não pode estar em execução ao mesmo tempo que o conector tenta ligar ao servidor Domino.

### <a name="create-connector"></a>Criar conector
Para criar um conector de Lotus Domino no **serviço de sincronização** selecione **agente de gestão** e **criar**. Selecione o **Lotus Domino (Microsoft)** conector.  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Se a versão do serviço de sincronização oferece a capacidade de configurar **arquitetura**, certifique-se de que o conector está definido como o valor predefinido para ser executada no **processo**.

### <a name="connectivity"></a>Conectividade
Na página de conectividade, tem de especificar o nome do servidor Lotus Domino e introduza as credenciais de início de sessão.  
![Conectividade](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

A propriedade do servidor de Domino suporta dois formatos para o nome do servidor:

* serverName
* ServerName/DirectoryName

O **ServerName/DirectoryName** formato é o formato preferencial para este atributo, porque fornece resposta mais rápida quando o conector contacta o servidor de Domino.

O ficheiro de UserID fornecido será armazenado na base de dados de configuração do serviço de sincronização.

Para **importação Delta** dispõe das seguintes opções:

* **Nenhuma**. O conector que não as importações de diferenças.
* **Adicionar/atualizar**. A importação de diferenças do conector does adicione e operações de atualização. Para eliminar um **importação completa** operação é necessária. Esta operação está a utilizar a interoperabilidade de .net.
* **Adicionar/atualizar/eliminar**. A importação de diferenças do conector does adicionar, atualizar e eliminar operações. Esta operação está a utilizar as interfaces de C++ nativas.

No **opções de esquema** tem as seguintes opções:

* **Predefinição esquema**. O conector Deteta o esquema do servidor Domino. Esta seleção é a opção predefinida.
* **Esquema de DSML**. Só é utilizada se o servidor de Domino não expõe o esquema. Em seguida, pode criar um ficheiro DSML com o esquema e importá-lo em vez disso. Para obter mais informações sobre DSML, consulte [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Quando clicar em seguinte, os parâmetros de configuração do ID de utilizador e palavra-passe são verificados.

### <a name="global-parameters"></a>Parâmetros globais
Na página de parâmetros globais, configure o fuso horário e a importar e exportar a opção de operação.  
![Parâmetros globais](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

O **fuso horário do servidor Domino** parâmetro define a localização do seu servidor Domino.

Esta opção de configuração é necessário para suportar **importação delta** operações porque permitem que o serviço de sincronização determinar as alterações entre as dois últimos importações.

>[!Note]
A partir da atualização de Março de 2017 o ecrã de parâmetros globais inclui a opção para eliminar a base de dados de correio do utilizador durante a eliminação do utilizador.

![Eliminar a caixa de correio do utilizador](./media/active-directory-aadconnectsync-connector-domino/AdminP.png)

#### <a name="import-settings-method"></a>Importar definições de método
O **efetuar a importação completa pelo** tem estas opções:

* Pesquisa
* Vista (recomendada)

**Pesquisa** é a utilização de indexação no Domino mas é comum que os índices não são atualizados em tempo real e os dados devolvidos do servidor não estão sempre corretos. Para um sistema com demasiadas alterações, esta opção normalmente não funciona e fornece false elimina em algumas situações. No entanto, **pesquisa** é mais rápida do que **vista**.

**Vista** é a opção recomendada, uma vez que fornece o estado correto de dados. É ligeiramente mais lenta do que a pesquisa.

#### <a name="creation-of-virtual-contact-objects"></a>Criação dos objetos de contactos Virtual
O **permitir a criação de \_objeto contacte** tem estas opções:

* Nenhuma
* Valores de referência não
* Valores de referência e não seja de referência

No Domino, atributos de referência podem conter vários formatos diferentes para referenciar outros objetos. Para poder representar variações diferentes, implementa o conector \_contacte objetos, também conhecido como **contactos Virtual** (VC). Estes objetos são criados para que possam juntar-se em objetos de MV existentes ou projetados com novos objetos. Desta forma, podem ser preservadas referências de atributo.

Ao ativar esta definição e se o conteúdo de um atributo de referência não é um formato de DN, um \_contacte objeto é criado. Por exemplo, um atributo de membro de um grupo pode conter endereços SMTP. Também é possível ter shortName e outros atributos presentes nos atributos de referência. Para este cenário, selecione **valores de referência não**. Esta configuração é a definição mais comuns para implementações de Domino.

Quando Lotus Domino está configurado para ter livros de endereços separado com diferentes nomes único que representa o mesmo objeto, é possível criar também \_contacte objetos para todos os valores de referência que se encontram no livro de endereços. Para este cenário, selecione o **referência e valores de referência não** opção.

Se tiver vários valores no atributo **FullName** no Domino, em seguida, também pretender ativar a criação dos contactos Virtual para que as referências podem ser resolvidas. Por exemplo, este atributo pode ter vários valores após um marriage ou divorce. Selecione a caixa de verificação **ativar... FullName tem vários valores** para este cenário.

Ao associar nos atributos de corretos, o \_objetos de contacto deverá ser associados ao objeto de MV.

Estes objetos têm VC =\_contacte adicionado à respetiva DN.

#### <a name="import-settings-conflict-object"></a>Importar as definições, entrar em conflito objeto
**Excluir objetos de conflito**

Numa implementação Domino grande, é possível que vários objetos têm o mesmo DN devido a problemas de replicação. Nestes casos, o conector seria ver dois objetos com UniversalIDs diferentes, mas DN do mesmo. Este conflito faria com que um objeto transitório a ser criado no espaço de conector. O conector pode ignorar os objetos que foram selecionados na Domino como victims de replicação. A recomendação é manter esta caixa de verificação selecionada.

#### <a name="export-settings"></a>Exportar definições
Se a opção **utilizar AdminP para atualizar referências** é não seleccionado, exportação dos atributos de referência, tais como o membro, uma chamada direta e não utiliza o processo de AdminP. Utilize esta opção apenas quando não tiver sido configurado AdminP para manter a integridade referencial.

#### <a name="routing-information"></a>Informações de encaminhamento
Domino, é possível que um atributo de referência tem informações de encaminhamento incorporadas como um sufixo para o DN. Por exemplo, o atributo de membro de um grupo pode conter **CN =example/organization@ABC**. O sufixo @ABC é as informações de encaminhamento. As informações de encaminhamento são utilizadas pelo Domino para enviar e-mails para o sistema Domino correto, que pode ser um sistema numa organização diferente. No campo de informações de encaminhamento, pode especificar os sufixos de encaminhamento utilizados dentro da organização no âmbito do conector. Se um dos seguintes valores encontra-se como um sufixo num atributo de referência, as informações de encaminhamento são removidas de referência. Se o sufixo de encaminhamento com um valor de referência não pode ser correspondido a um desses valores especificados, um \_contacte objeto é criado. Estes \_objetos de contacto são criados com **RO = @<RoutingSuffix>**  inserido o DN. Para estes \_contacte objetos os seguintes atributos são também adicionados para permitir a associar a um objecto real, se necessário: \_routingName, \_contactName, \_displayName e UniversalID.

#### <a name="additional-address-books"></a>Livros de endereços adicionais
Se não tiver **assistência de diretório** instalados, que fornece o nome de livros de endereços secundária, em seguida, pode introduzir manualmente estes livros de endereços.

#### <a name="multivalued-transformation"></a>Transformação com múltiplos valores
Demasiados atributos no Lotus Domino são com múltiplos valores. Os atributos de metaverso correspondentes são normalmente único importantes. Ao configurar a importação e a opção de operação de exportação, ative o conector para o ajudar com a tradução necessária dos atributos afetados.

**Exportar**  
A opção de operação de exportação suporta dois modos:

* Acrescentar item
* Substituir o item

**Substituir o Item** -se selecionar esta opção, o conector sempre remover os valores atuais do atributo no Domino e substitua-as com os valores fornecidos. Fornecido valor pode ser um valor único ou com múltiplos valores.

Exemplo: O atributo de Assistente de um objeto de pessoa tenha os seguintes valores:

* CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN = João Smith/OU=Contoso/O=Americas,NAB=names.nsf

Se um Assistente de nova denominada **David Alexander** está atribuído a este objeto de pessoa, o resultado é:

* CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**Acrescentar Item** -se selecionar esta opção, o conector mantém os valores existentes no atributo na Domino e inserir novos valores na parte superior da lista de dados.

Exemplo: O atributo de Assistente de um objeto de pessoa tenha os seguintes valores:

* CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN = João Smith/OU=Contoso/O=Americas,NAB=names.nsf

Se um Assistente de nova denominada **David Alexander** está atribuído a este objeto de pessoa, o resultado é:

* CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN = João Smith/OU=Contoso/O=Americas,NAB=names.nsf

**Importar**  
A opção de operação de importação suporta dois modos:

* Predefinição
* Valor com múltiplos valores a uma única

**Predefinição** -se selecionar a opção predefinida, todos os valores de todos os atributos são importados.

**Com múltiplos valores para o valor único** -se selecionar esta opção, um atributo com múltiplos valor é convertido para um atributo de valor único. Se existir mais do que um valor, é utilizado o valor na parte superior (este valor é normalmente também a versão mais recente).

Exemplo: O atributo de Assistente de um objeto de pessoa tenha os seguintes valores:

* CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN = João Smith/OU=Contoso/O=Americas,NAB=names.nsf

A atualização mais recente para este atributo é **David Alexander**. Porque a opção de operação de importação estiver definida como Multivalued valor único, conector importa apenas **David Alexander** para o espaço conetor.

Não é aplicável a lógica de converter os atributos com múltiplos valores em atributos de valor único para o atributo de membro do grupo e o atributo de fullname pessoa.

-Lo também possível configurar a importar e exportar regras de transformação para atributos com múltiplos valores por atributo, como uma exceção para a regra global. Para configurar esta opção, introduza [objecttype]. [attributename] no **importar a lista de atributos de exclusão** e **exportar a lista de atributos de exclusão** caixas de texto. Por exemplo, se introduzir Person.Assistant e o sinalizador global está definido para importar todos os valores, apenas o primeiro valor é importado para o assistente.

#### <a name="certifiers"></a>Certifiers
Todas as unidades de organização/Organizational são listadas pelo conector. Para poder exportar os objetos de pessoas no livro de endereços primário, é necessário um certifier com a respetiva palavra-passe.

Se todos os certifiers tem a mesma palavra-passe, o **palavra-passe para todos os Certifers** pode ser utilizado. Em seguida, pode introduzir a palavra-passe aqui e especifique apenas o ficheiro de certifier.

Se apenas importar, não é necessário especificar quaisquer certifiers.

### <a name="configure-provisioning-hierarchy"></a>Configurar hierarquia de aprovisionamento
Quando configurar o conector do Lotus Domino, ignore esta página de caixa de diálogo. O conector do Lotus Domino não suporta a hierarquia de aprovisionamento.  
![Hierarquia de aprovisionamento](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurar partições e hierarquias
Quando configurar partições e hierarquias, tem de selecionar o livro de endereços primário denominado NAB=names.nsf. Para além do livro de endereços primário, pode selecionar livros de endereços secundária caso existam.  
![Partições](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Selecionar atributos
Quando configura os atributos, tem de selecionar todos os atributos que têm o prefixo  **\_MMS\_**. Estes atributos são necessários quando aprovisionar novos objetos para Lotus Domino

![Atributos](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Gestão de ciclo de vida do objeto
Esta secção fornece uma descrição geral dos diversos objetos no Domino.

### <a name="person-objects"></a>Objetos de pessoa
O objeto da pessoa que representa os utilizadores na organização e unidades organizacionais. Para além dos atributos predefinidos, o administrador de Domino pode adicionar atributos personalizados para um objeto de pessoa. No mínimo, um objeto de pessoa tem de incluir todos os atributos obrigatórios. Para obter uma lista completa dos atributos obrigatórios, consulte [Lotus notas propriedades](#lotus-notes-properties). Para registar um objeto de pessoa, devem ser cumpridos os seguintes pré-requisitos:

* O livro de endereços (names.nsf) tem foram definido e deve ser o livro de endereços primário.
* Tem de ter o certifier Nã/UO Id e a palavra-passe para registar um determinado utilizador na organização / unidade organizacional.
* Tem de definir um conjunto específico de notas do Lotus as propriedades de um objeto de pessoa. Estas propriedades são utilizadas para aprovisionar o objeto da pessoa. Para obter mais informações, consulte a secção denominada [Lotus notas propriedades](#lotus-notes-properties) mais à frente neste documento.
* O HTTP palavra-passe inicial para uma pessoa é um atributo e o conjunto durante o aprovisionamento.
* O objeto de pessoa tem de ser um dos seguintes três tipos suportados:
  1. Utilizador normal, que tem um ficheiro de correio e um ficheiro de id de utilizador
  2. (Um utilizador Normal que inclui todos os ficheiros de base de dados de roaming) de utilizador itinerantes
  3. Contactos (com nenhum ficheiro de id de utilizador)

Pessoas (exceto os contactos) ainda podem ser agrupadas nos utilizadores e de utilizadores internacionais conforme definido pelo valor o \_MMS\_IDRegType propriedade. Utilize estas pessoas cliente notas para aceder aos servidores Lotus Domino, ter um Id de notas e um documento de pessoa. Se estiverem a utilizar o correio de notas, em seguida, também têm um ficheiro de correio. O utilizador tem de ser registado para ficar ativo. Para obter mais informações, consulte:

* [A configurar utilizadores de notas](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
* [Registo de utilizador](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
* [Gestão de utilizadores](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
* [Mudar o nome de utilizadores](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Todas estas operações são efetuadas no Lotus Domino e, em seguida, importar para o serviço de sincronização.

### <a name="resources-and-rooms"></a>Recursos e gabinetes
Um recurso é outro tipo de uma base de dados no Lotus Domino. Recursos podem ser gabinetes conferência com vários tipos de equipamento, por exemplo, projetores. Existem subtipos de recursos suportados pelo conector do Lotus Domino que são definidos pelo atributo de tipo de recurso:

| Tipo de recurso | Atributo de tipo de recurso |
| --- | --- |
| Sala |1 |
| Recursos (Other) |2 |
| Reunião online |3 |

Para o tipo de objetos de recurso funcione, é necessário o seguinte:

* Base de dados de reserva de recursos já deve existir no servidor de Domino ligado
* O site já está definido para o recurso

A base de dados de reserva de recursos contém três tipos de documentos:

* Perfil de site
* Recurso
* Reserva

Para obter mais informações sobre a configuração da base de dados de reserva de recursos, consulte [como configurar a base de dados do recurso reservas](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html).

**Criar, atualizar e eliminar recursos**  
As operações criar, atualizar e eliminar são efetuadas pelo conector do Lotus Domino na base de dados de reserva de recursos. Recursos são criados como documentos no Names.nsf (ou seja, o principal no livro de endereços). Para obter mais informações sobre como editar e eliminar recursos, consulte [editar e eliminar documentos recursos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html).

**Importar e exportar operação dos recursos**  
Os recursos podem ser importados para e podem ser exportados a partir do serviço de sincronização, tal como qualquer outro tipo de objeto. Selecione o tipo de objeto como recurso durante a configuração. Para a operação de exportação com êxito, deve ter os detalhes para o tipo de recurso, base de dados de conferência e nome do Site.

### <a name="mail-in-databases"></a>Correio nas bases de dados
Uma base de dados no correio é uma base de dados que foi concebido para receber mensagens de correio eletrónico. É uma caixa de correio do Lotus Domino que não está associada a qualquer conta de utilizador do Lotus Domino específica (ou seja, não tem os seus próprios ficheiros de ID e a palavra-passe). Uma base de dados no correio tem um UserID exclusivo ("nome abreviado") associado e o seu próprio endereço de correio eletrónico.

Se for necessário para uma caixa de correio separado com o seu próprio endereço de correio eletrónico pode ser partilhada entre diferentes utilizadores (por exemplo, group@contoso.com), é criada uma base de dados no correio. O acesso a esta caixa de correio é controlado através do seu controlo lista acesso (ACL), que contém os nomes dos utilizadores notas que têm permissão para abrir a caixa de correio.

Para obter uma lista dos atributos necessários, consulte a secção denominada [os atributos obrigatórios](#mandatory-attributes) posteriormente neste artigo.

Quando uma base de dados foi concebido para receber uma mensagem, é criado um documento no correio de base de dados no Lotus Domino. Este documento tem de existir no diretório de Domino de cada servidor que armazena uma cópia da base de dados. Para obter uma descrição detalhada sobre como criar um documento no correio de base de dados, consulte [criar um documento no correio de base de dados](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html).

Antes de criar uma base de dados no correio, já deve existir na base de dados (deveria ter sido criado pelo administrador Lotus) no servidor Domino.

### <a name="group-management"></a>Gestão de grupo
Pode obter uma descrição geral detalhada da gestão de grupo de Lotus Domino partir os seguintes recursos:

* [Utilizar grupos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
* [Criar um grupo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
* [Criar e modificar grupos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
* [Gerir grupos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
* [Mudar o nome de um grupo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Gestão de palavra-passe
Para um utilizador Lotus Domino registado, existem dois tipos de palavras-passe:

1. Palavra-passe de utilizador (armazenado no ficheiro User.id)
2. Internet / palavra-passe HTTP

O conector do Lotus Domino suporta apenas as operações com palavra-passe HTTP.

Para efetuar a gestão de palavra-passe, deverá ativar a gestão de palavras-passe para o conector no estruturador de agente de gestão. Para ativar a gestão de palavra-passe, selecione **ativar a gestão de palavra-passe** no **configurar extensões** página da caixa de diálogo.  
![Configurar extensões](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

O suporte de conector Lotus Domino seguintes operações na palavra-passe de Internet:

* Definir palavra-passe: Palavra-passe do conjunto define uma nova palavra-passe HTTP/Internet no utilizador no Domino. Por predefinição, a conta também está desbloqueada. O sinalizador de desbloqueio está exposto na interface do motor de sincronização de WMI.
* Alterar palavra-passe: Neste cenário, um utilizador pode pretender alterar a palavra-passe ou é-lhe pedido para alterar a palavra-passe após um período de tempo especificado. Para esta operação seja coloque, (o antigo tanto a nova palavra-passe) são obrigatórios. Assim que tiver sido alterada, a nova palavra-passe é atualizado no Lotus Domino.

Para obter mais informações, consulte:

* [Utilizar a funcionalidade de bloqueio de Internet](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
* [Gerir palavras-passe de Internet](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Informações de referência
Esta secção lista como descrições de atributo e requisitos de atributo para o conector do Lotus Domino.

### <a name="lotus-notes-properties"></a>Lotus Notes propriedades
Quando aprovisiona objetos de pessoa ao seu diretório Lotus Domino, os objetos tem de ter um conjunto específico de propriedades com valores específicos preenchidos. Estes valores apenas são necessárias para criar operações.

A tabela seguinte apresenta estas propriedades e fornece uma descrição dos mesmos.

| Propriedade | Descrição |
| --- | --- |
| \_MMS_AltFullName |O nome completo alternativo do utilizador. |
| \_MMS_AltFullNameLanguage |O idioma a ser utilizado para especificar o nome completo alternativo do utilizador. |
| \_MMS_CertDaysToExpire |O número de dias a contar da data atual antes do certificado expira. Se não for especificado, a data de predefinido é dois anos a contar da data atual. |
| \_MMS_Certifier |Propriedade que contém o nome de hierarquia organizacional do certifier. Por exemplo: UO = OrganizationUnit, O = Org, C = país. |
| \_MMS_IDPath |Se a propriedade está vazia, nenhum ficheiro de identificação do utilizador é criado localmente no servidor de sincronização. Se a propriedade contém um nome de ficheiro, é criado um ficheiro de ID de utilizador na pasta madata. A propriedade também pode conter um caminho completo. |
| \_MMS_IDRegType |Pessoas que pode ser classificados como contactos, nos utilizadores e os utilizadores internacionais. A tabela seguinte lista os valores possíveis: <li>0 - contacte</li><li>1 - utilizador de US</li><li>2 - utilizador internacional</li> |
| \_MMS_IDStoreType |Propriedade necessária para E.U.A. e utilizadores internacionais. A propriedade contém um valor inteiro que especifica se a identificação de utilizador é armazenada como um anexo no livro de endereços de notas ou no ficheiro de correio da pessoa. Se o ficheiro de ID de utilizador for um anexo no livro de endereços,, opcionalmente, pode ser criado como um ficheiro com \_MMS_IDPath. <li>Vazio - ficheiro de arquivo ID no ID de cofre, nenhum ficheiro de identificação (utilizado para contactos).</li><li> 1 - anexo no livro de endereços de notas. O \_MMS_Password propriedade tem de ser definida para os ficheiros de identificação de utilizador que são anexos</li><li>2 - ID de arquivo no ficheiro de correio da pessoa. O \_MMS_UseAdminP tem de ser definido como falso para permitir que o ficheiro de correio ser criado durante o registo da pessoa. O \_MMS_Password propriedade tem de ser definida para os ficheiros de identificação do utilizador.</li> |
| \_MMS_MailQuotaSizeLimit |O número de megabytes que são permitidos para a base de dados de ficheiro de mensagem de correio eletrónico. |
| \_MMS_MailQuotaWarningThreshold |O número de megabytes que são permitidos para a base de dados de ficheiro de mensagem de correio eletrónico antes de que é emitido um aviso. |
| \_MMS_MailTemplateName |O ficheiro de modelo de correio eletrónico que é utilizado para criar o ficheiro de mensagem de correio eletrónico do utilizador. Se não for especificado um modelo, o ficheiro de correio é criado utilizando o modelo especificado. Não se for especificado nenhum modelo, o ficheiro de modelo predefinido é utilizado para criar o ficheiro. |
| \_MMS_OU |Propriedade opcional que é o nome de UO sob o certifier. Esta propriedade deve estar vazia para contactos. |
| \_MMS_Password |Propriedade necessária para os utilizadores. A propriedade contém a palavra-passe para o ficheiro de identificação do objeto. |
| \_MMS_UseAdminP |Propriedade deve ser definida como VERDADEIRO se o ficheiro de correio deve ser criado pelo processo de AdminP no servidor de Domino (assíncrono para o processo de exportação). Se for definida como false, o ficheiro de correio é criado com o utilizador Domino (síncrono no processo de exportação). |

Para um utilizador com um ficheiro de identificação, o \_MMS_Password propriedade tem de conter um valor. Para acesso ao e-mail através do cliente Lotus notas, as propriedades de MailServer e MailFile de um utilizador tem de conter um valor.

Para aceder ao e-mail através de um browser Web, as propriedades seguintes têm de conter valores:

* MailFile - propriedade necessária que contém o caminho no servidor do Lotus Domino onde está armazenado o ficheiro de correio.
* MailServer - propriedade necessária que contém o nome do servidor Lotus Domino. Este valor é o nome a utilizar quando criou o ficheiro de correio Lotus no servidor de Domino.
* HTTPPassword - propriedade opcional que contém a palavra-passe de acesso Web para o objeto.

Para aceder ao servidor de Domino sem capacidade de correio, a propriedade HTTPPassword tem de conter um valor. A propriedade MailFile e a propriedade MailServer podem estar vazios.

Com \_MMS_ IDStoreType = 2 (id de arquivo no ficheiro de correio), a propriedade MailSystem NotesRegistrationclass está definida como REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Atributos obrigatórios
O conector do Lotus Domino suporta principalmente estes tipos de objetos (tipos de documentos):

* Grupo
* Correio na base de dados
* Pessoa
* Contacte (pessoa com nenhuma certifier)
* Recurso

Esta secção lista os atributos que são obrigatórios para cada objeto a exportar para um servidor de Domino suportado.

| Tipo de objeto | Atributos obrigatórios |
| --- | --- |
| Grupo |<li>ListName necessário</li> |
| Principal na base de dados |<li>FullName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |
| Pessoa |<li>Apelido</li><li>MailFile</li><li>ShortName</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li> |
| Contacte (pessoa com nenhuma certifier) |<li>\_MMS_IDRegType</li> |
| Recurso |<li>FullName</li><li>ResourceType</li><li>ConfDB</li><li>ResourceCapacity</li><li>site</li><li>displayName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |

## <a name="common-issues-and-questions"></a>Problemas e questões comuns
### <a name="schema-detection-does-not-work"></a>Deteção de esquema não funciona
Ser capaz de detetar o esquema, é necessário que o ficheiro de schema.nsf está presente no servidor Domino. Este ficheiro é apresentado apenas se LDAP estiver instalado no servidor. Se o esquema não é detetável, verifique o seguinte:

* O ficheiro schema.nsf está presente na pasta raiz do servidor Domino
* O utilizador tem permissões para ver o ficheiro schema.nsf.
* Força um reinício do servidor LDAP. Abra **Lotus Domino consola** e utilizar **dizer ReloadSchema de LDAP** comando ou recarregue o esquema.

### <a name="not-all-secondary-address-books-are-visible"></a>Nem todos os livros de endereços secundária estão visíveis
O conector Domino baseia-se a funcionalidade **diretório assistência** conseguir localizar os livros de endereços secundário. Se os livros de endereços secundária estão em falta, verifique se [diretório assistência](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) foi ativado e configurado no servidor de Domino.

### <a name="custom-attributes-in-domino"></a>Atributos personalizados no Domino
Existem várias formas Domino para expandir o esquema, pelo que é apresentada como um atributo personalizado consumíveis pelo conector.

**Abordagem 1: Expandir o esquema de Lotus Domino**

1. Criar uma cópia do modelo de diretório Domino {PUBNAMES. NTF} seguindo [estes passos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (não deve personalizar o diretório de IBM Lotus Domino predefinido modelo):
2. Abra o modelo de diretório de cópia de Domino {CONTOSO. Modelo NTF} que foi criado no estruturador de Domino e siga estes passos:
   * Clique em elementos partilhados e expanda Subforms
   * Faça duplo clique em subform de InheritableSchema ${ObjectName} (onde {ObjectName} é o nome da classe de objeto estruturais predefinido, por exemplo: pessoa).
   * Nome do atributo que pretende adicionar o esquema {MyPersonAtrribute} e as correspondentes para esse atributo. Criar um campo, selecione o **criar** Menu e, em seguida, selecione **campo** a partir do menu.
   * No campo adicionado, defina as respetivas propriedades selecionando o respetivo tipo, estilo, tamanho, tipo de letra e outros parâmetros relacionados na janela de propriedades de campo.
   * Mantenha o mesmo valor predefinido como o nome indicado para esse atributo de atributo (por exemplo, se o nome do atributo MyPersonAttribute, mantenha o valor predefinido com o mesmo nome).
   * Guarde o subform de InheritableSchema ${ObjectName} com valores atualizados.
3. Substituir o modelo de diretório Domino {PUBNAMES. NTF} com o novo modelo personalizado {CONTOSO. NTF} seguindo [estes passos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Feche Domino Admin e abra a consola Domino para reiniciar o serviço LDAP e recarregar o esquema de LDAP:
   * Na consola de Domino, introduza o comando em **Domino comando** texto registado para reiniciar o serviço LDAP - [LDAP de tarefas reiniciar](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
   * Recarregar LDAP esquema utilizar comandos de LDAP dizer - dizer ReloadSchema de LDAP
5. Abra Domino Admin e selecione o separador de pessoas e grupos para ver o atributo adicionado será refletido na domino adicionam pessoa.
6. Abrir Schema.nsf de **ficheiros** separador e ver o atributo adicionado é refletido na classe de objeto dominoPerson LDAP.

**Abordagem 2: Criar um auxClass com o atributo personalizado e associar a classe de objeto**

1. Criar uma cópia do modelo de diretório Domino {PUBNAMES. NTF} seguindo [estes passos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (nunca personalizar o diretório de IBM Lotus Domino predefinido modelo):
2. Abra o modelo de diretório de cópia de Domino {CONTOSO. Modelo NTF} que foi criado, no Domino Designer.
3. No painel esquerdo, selecione o código partilhado e, em seguida, Subforms.
4. Clique em novo Subform
5. Efetue o seguinte procedimento para especificar as propriedades para o novo subform:
   * Com o novo subform abra, escolha o Design - Subform propriedades
   * Junto a propriedade de nome, introduza um nome para a classe de objeto de auxiliar – por exemplo, TestSubform.
   * Manter a propriedade de opções "Incluir Insert Subform... caixa de diálogo" selecionado
   * Anular seleção de opções propriedade "compor pass-through HTML nas notas."
   * Deixe as outras propriedades da mesma e fechar a caixa Subform propriedades.
   * Guarde e feche o subform de novo.
6. Efetue o seguinte procedimento para adicionar um campo para definir a classe de objeto de auxiliar:
   * Abra o subform que criou.
   * Escolha criar - campo.
   * Junto ao nome no separador Noções básicas da caixa de diálogo de campo, especifique um nome, por exemplo: {MyPersonTestAttribute}.
   * No campo adicionado, defina as respetivas propriedades selecionando o respetivo tipo, estilo, tamanho, tipo de letra e as propriedades relacionadas.
   * Mantenha o mesmo valor predefinido como o nome indicado para esse atributo de atributo (por exemplo, se o nome do atributo MyPersonTestAttribute, mantenha o valor predefinido com o mesmo nome).
   * Guarde o subform com valores atualizados e efetue o seguinte:
     * No painel esquerdo, selecione o código partilhado e, em seguida, Subforms
     * Selecione o novo subform e escolha estrutura - propriedades de criação.
     * Clique no separador terceiro da esquerda e selecione **propagar este prohibition de alteração da estrutura**.
7. Abra ${ObjectName} ExtensibleSchema subform, (em que {ObjectName} é o nome da classe de objeto estruturais predefinido, por exemplo – a pessoa).
8. Inserir recursos e selecione Subform (que criou, por exemplo – TestSubform) e guarde o subform de ExtensibleSchema ${ObjectName}.
9. Substituir o modelo de diretório Domino {PUBNAMES. NTF} com o novo modelo personalizado {CONTOSO. NTF} seguindo [estes passos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Feche Domino Admin e abra a consola Domino para reiniciar o serviço LDAP e recarregar o esquema de LDAP:
    * Na consola de Domino, introduza o comando em **Domino comando** texto registado para reiniciar o serviço LDAP - [LDAP de tarefas reiniciar](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    * Recarregar LDAP esquema utilize o comando de LDAP dizer **dizer ReloadSchema de LDAP**.
11. Abra o Domino Admin e selecione o separador pessoas e grupos para ver o atributo adicionado será refletido na domino Adicionar pessoa (em outros separador).
12. Abrir Schema.nsf de **ficheiros** separador e ver o atributo adicionado é refletido no TestSubform LDAP auxiliar classe de objeto.

**Abordagem 3: Adicionar o atributo personalizado para a classe de ExtensibleObject**

1. Ficheiro {Schema.nsf} aberto colocado no diretório de raiz
2. Selecione as Classes de objetos de LDAP no menu esquerdo em **todos os documentos de esquema** e clique em **classe de objeto adicionar** botão:
3. Forneça o nome de LDAP no formato {zzzExtensibleSchema} (em que zzz é o nome da classe de objeto estruturais predefinido, por exemplo pessoa). Por exemplo, para expandir o esquema para a pessoa classe de objeto, forneça o nome LDAP {PersonExtensibleSchema}.
4. Forneça o nome de classe de objeto Superior, para que pretende expandir o esquema. Por exemplo, para expandir o esquema da classe de objeto de pessoa, forneça o nome de classe de objeto Superior {dominoPerson}:
5. Forneça um OID válido correspondente para a classe de objeto.
6. Selecione os atributos de expandida/personalizados em campos obrigatório ou tipos de atributo opcional de acordo com os requisitos:
7. Depois de adicionar atributos necessários para o ExtensibleObjectClass, clique em **guardar e fechar**.
8. Um ExtensibleObjectClass é criado para a classe de objeto do respetivo predefinido com atributos expandidos.

## <a name="troubleshooting"></a>Resolução de problemas
* Para obter informações sobre como ativar o registo resolver o conector, consulte o [como ativar o rastreio do ETW para conectores](http://go.microsoft.com/fwlink/?LinkId=335731).
