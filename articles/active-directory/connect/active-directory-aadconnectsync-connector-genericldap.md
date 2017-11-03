---
title: "Conector LDAP genérico | Microsoft Docs"
description: "Este artigo descreve como configurar o conector de LDAP genérico da Microsoft."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 984beeb0-4d91-4908-ad81-c19797c4891b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: adb174526bc377f484be5fb0a71b28e8daaaa6fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="generic-ldap-connector-technical-reference"></a>Referência técnica de conector LDAP genérica
Este artigo descreve o conector LDAP genérico. O artigo aplica-se os seguintes produtos:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Tem de utilizar correção 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2, o conector está disponível como uma transferência a partir do [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

Ao fazer referência ao IETF RFCs, este documento está a utilizar o formato (RFC [número RFC] / [secção documento RFC]), por exemplo (RFC 4512/4.3).
Pode encontrar mais informações em http://tools.ietf.org/html/rfc4500 (é necessário substituir 4500 com o número correto de RFC).

## <a name="overview-of-the-generic-ldap-connector"></a>Descrição geral do conector LDAP genérico
O conector LDAP genérico permite-lhe integrar o serviço de sincronização com um servidor LDAP v3.

Determinadas operações e os elementos de esquema, tais como as necessárias para efetuar a importação delta, não estão especificados nos RFCs IETF. Para estas operações são suportados apenas diretórios LDAP ou não explicitamente especificados.

Numa perspetiva de alto nível, as seguintes funcionalidades são suportadas pela versão atual do conector:

| Funcionalidade | Suporte |
| --- | --- |
| Origem de dados ligada |O conector é suportado com todos os servidores de v3 LDAP (com RFC 4510). Foi testado com o seguinte: <li>Microsoft Active Directory Serviços LDS do Directory (AD LDS)</li><li>Diretório do Microsoft Active Directory Catálogo Global (AD GC)</li><li>Servidor de diretório 389</li><li>Servidor de diretório do Apache</li><li>IBM Tivoli DS</li><li>Diretório de Isode</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Abra DJ</li><li>Abra DS</li><li>Abra LDAP (openldap.org)</li><li>Oracle (anteriormente Sun) diretório Server Enterprise Edition</li><li>Servidor de diretório Virtual RadiantOne (VDS)</li><li>Sun um servidor de diretório</li>**Diretórios acentuados não suportados:** <li>Microsoft Active Directory Domain Services (AD DS) [utilizar em vez disso, o conector do Active Directory incorporados]</li><li>Diretório de Internet Oracle (OID)</li> |
| Cenários |<li>Gestão de ciclo de vida do objeto</li><li>Gestão de grupo</li><li>Gestão de palavra-passe</li> |
| Operações |As seguintes operações são suportadas em todos os diretórios LDAP: <li>Importação completa</li><li>Exportar</li>As seguintes operações só são suportadas em diretórios especificados:<li>Importação delta</li><li>Definir a palavra-passe, altere a palavra-passe</li> |
| Esquema |<li>Foi detetado o esquema do esquema LDAP (RFC3673 e RFC4512/4.2)</li><li>Suporta estruturais classes, classes de aux e classe de objeto extensibleObject (RFC4512/4.3)</li> |

### <a name="delta-import-and-password-management-support"></a>Suporte de gestão de palavra-passe e a importação delta
Suportado diretórios para a importação Delta e gestão de palavra-passe:

* Microsoft Active Directory Serviços LDS do Directory (AD LDS)
  * Suporta todas as operações para a importação delta
  * Suporta define palavra-passe
* Diretório do Microsoft Active Directory Catálogo Global (AD GC)
  * Suporta todas as operações para a importação delta
  * Suporta define palavra-passe
* Servidor de diretório 389
  * Suporta todas as operações para a importação delta
  * Suporta definir palavra-passe e alterar palavra-passe
* Servidor de diretório do Apache
  * Não suporta a importação delta, uma vez que este diretório não tem um registo de alterações persistente
  * Suporta define palavra-passe
* IBM Tivoli DS
  * Suporta todas as operações para a importação delta
  * Suporta definir palavra-passe e alterar palavra-passe
* Diretório de Isode
  * Suporta todas as operações para a importação delta
  * Suporta definir palavra-passe e alterar palavra-passe
* Novell eDirectory e NetIQ eDirectory
  * Suporta operações de adicionar, atualizar e mudar o nome para a importação delta
  * Não suporta operações de eliminação para a importação delta
  * Suporta definir palavra-passe e alterar palavra-passe
* Abra DJ
  * Suporta todas as operações para a importação delta
  * Suporta definir palavra-passe e alterar palavra-passe
* Abra DS
  * Suporta todas as operações para a importação delta
  * Suporta definir palavra-passe e alterar palavra-passe
* Abra LDAP (openldap.org)
  * Suporta todas as operações para a importação delta
  * Suporta define palavra-passe
  * Não suporta a alterar palavra-passe
* Oracle (anteriormente Sun) diretório Server Enterprise Edition
  * Suporta todas as operações para a importação delta
  * Suporta definir palavra-passe e alterar palavra-passe
* Servidor de diretório Virtual RadiantOne (VDS)
  * Tem de utilizar a versão 7.1.1 ou superior
  * Suporta todas as operações para a importação delta
  * Suporta definir palavra-passe e alterar palavra-passe
* Sun um servidor de diretório
  * Suporta todas as operações para a importação delta
  * Suporta definir palavra-passe e alterar palavra-passe

### <a name="prerequisites"></a>Pré-requisitos
Antes de utilizar o conector, certifique-se de que tem o seguinte no servidor de sincronização:

* Arquitetura do Microsoft .NET 4.5.2 ou posterior

### <a name="detecting-the-ldap-server"></a>Detetar o servidor LDAP
O conector baseia-se na várias técnicas para detetar e identificar o servidor LDAP. O conector utiliza da DSE de raiz, o nome/versão do fornecedor e inspeciona o esquema para localizar objetos exclusivos e atributos conhecidos existir em determinados servidores de LDAP. Estes dados, se encontrar, é utilizado para preencher previamente as opções de configuração no conector.

### <a name="connected-data-source-permissions"></a>Permissões de origem de dados ligadas
Para efetuar a importação e exportação operações sobre os objetos no diretório ligado, a conta do conector tem de ter permissões suficientes. O conector tem de escrever as permissões para poder exportar e permissões de leitura para poder importar. Configuração de permissões é efetuada nas experiências de gestão do diretório de destino em si.

### <a name="ports-and-protocols"></a>Portas e protocolos
O conector utiliza o número de porta especificado na configuração, que, por predefinição, é a 389 para o LDAP e 636 para LDAPS.

Para LDAPS, tem de utilizar o SSL 3.0 ou TLS. SSL 2.0 não é suportada e não pode ser ativado.

### <a name="required-controls-and-features"></a>Controlos necessários e funcionalidades
As seguintes controlos LDAP/funcionalidades têm de estar disponíveis no servidor de LDAP para o conector funcionar corretamente:  
`1.3.6.1.4.1.4203.1.5.3`Filtros de True/False

O filtro de True/False não é frequentemente reportado como suportada pelo diretórios LDAP e poderá ser mostrada no **página Global** em **obrigatório funcionalidades não foi encontrado**. É utilizado para criar **ou** filtros nas consultas LDAP, por exemplo, ao importar vários tipos de objeto. Se pode importar mais do que um tipo de objeto, o servidor LDAP suporte esta funcionalidade.

Se utilizar um diretório em que um identificador exclusivo é a âncora de seguintes também tem de estar disponíveis (para obter mais informações, consulte o [configurar âncoras](#configure-anchors) secção):  
`1.3.6.1.4.1.4203.1.5.1`Todos os atributos operacionais

Se o diretório tiver mais objetos que o se podem ajustar na chamada para o diretório, em seguida, é recomendado utilizar a paginação. Para paginação funcione, precisa de uma das seguintes opções:

**Opção 1:**  
`1.2.840.113556.1.4.319`pagedResultsControl

**Opção 2:**  
`2.16.840.1.113730.3.4.9`VLVControl  
`1.2.840.113556.1.4.473`SortControl

Se ambas as opções são ativadas na configuração do conector, pagedResultsControl é utilizada.

`1.2.840.113556.1.4.417`ShowDeletedControl

ShowDeletedControl só é utilizado com o método de importação delta USNChanged para conseguir ver objetos eliminados.

O conector tenta detetar as opções presentes no servidor. Se não não possível detetar as opções, um aviso se encontra presente na página Global nas propriedades do conector. Nem todos os servidores LDAP presentes todos os controlos/funcionalidades que suportam e o mesmo se este aviso se encontra presente, o conector pode funcionar sem problemas.

### <a name="delta-import"></a>Importação delta
Importação delta só está disponível quando foi detetado um diretório de suporte. Atualmente são utilizados os seguintes métodos:

* Accesslog LDAP. Consulte [http://www.openldap.org/doc/admin24/overlays.html#Access registo](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
* Changelog LDAP. Consulte [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
* TimeStamp. Para Novell/NetIQ eDirectory, o conector utiliza a última data/hora para obter criado e atualizar objetos. Novell/NetIQ eDirectory não fornece que um equivalente significa obter os objetos eliminados. Esta opção também pode ser utilizada se nenhum outro método de importação delta está ativo no servidor LDAP. Esta opção não é possível importar eliminada objetos.
* USNChanged. Consulte: [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>Não suportado
Não são suportadas as seguintes funcionalidades LDAP:

* Referências de LDAP entre servidores (RFC 4511/4.1.10)

## <a name="create-a-new-connector"></a>Criar um novo conector
Para criar um conector LDAP genérico, no **serviço de sincronização** selecione **agente de gestão** e **criar**. Selecione o **LDAP genérico (Microsoft)** conector.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>Conectividade
Na página de conectividade, tem de especificar as informações do anfitrião, a porta e o enlace. Dependendo do que o enlace é selecionado, adicionais poderão indicar informações nas secções seguintes.

![Conectividade](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

* A definição de tempo limite da ligação é utilizada apenas para a primeira ligação ao servidor ao detetar o esquema.
* Se o enlace é anónimo, em seguida, um nome de utilizador / palavra-passe nem certificado são utilizadas.
* Para outros enlaces, introduza informações ou no nome de utilizador / palavra-passe ou selecione um certificado.
* Se estiver a utilizar o Kerberos para autenticar, em seguida, fornece também o Realm domínio do utilizador.

O **aliases de atributos** caixa de texto é utilizada para atributos definidos no esquema com a sintaxe de RFC4522. Estes atributos não não possível detetar durante a deteção de esquema e o conector tem de obter ajuda para identificar os atributos. Por exemplo seguintes têm de ser introduzidos na caixa de aliases de atributo para identificar corretamente o atributo userCertificate como um atributo binário:

`userCertificate;binary`

Segue-se um exemplo de como esta configuração pode ter o seguinte aspeto:

![Conectividade](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Selecione o **inclui os atributos de operacionais no esquema** caixa de verificação para também inclui os atributos criados pelo servidor. Estes incluem atributos, tais como quando o objeto foi criado e a hora da última atualização.

Selecione **inclui os atributos de extensíveis no esquema** se objectos extensíveis (RFC4512/4.3) são utilizados e a ativação desta opção permite que cada atributo a ser utilizado em todos os objetos. Selecionar esta opção faz com que o esquema muito grande, de modo a menos que o diretório ligado está a utilizar esta funcionalidade a recomendação é manter a opção desmarcada.

### <a name="global-parameters"></a>Parâmetros globais
Na página de parâmetros globais, configure o DN no registo de alterações de delta e funcionalidades adicionais de LDAP. A página é pré-preenchidos com as informações fornecidas pelo servidor LDAP.

![Conectividade](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

A secção superior mostra as informações que forneceu o próprio servidor, tal como o nome do servidor. O conector também verifica que os controlos obrigatórios estão presentes da DSE de raiz. Se estes controlos não estiver listados, é apresentado um aviso. Alguns diretórios LDAP não listar todas as funcionalidades da DSE de raiz e é possível que o conector funciona sem problemas, mesmo que esteja presente um aviso.

O **suportado controlos** caixas de verificação controlam o comportamento de determinadas operações:

* Com a eliminação de árvore selecionada, é eliminada uma hierarquia com uma chamada LDAP. Com a eliminação de árvore desmarcada, o conector efetua uma eliminação recursiva se for necessário.
* Com os resultados paginados selecionados, o conector efetua uma importação paginada com o tamanho especificado nos passos de execução.
* O VLVControl e SortControl é uma alternativa à pagedResultsControl ler dados a partir do diretório LDAP.
* Se as três opções (pagedResultsControl VLVControl e SortControl) são não seleccionadas o conector importa todos os objetos numa única operação, poderá falhar se for um diretório de grandes dimensões.
* ShowDeletedControl só é utilizada quando o método de importação Delta é USNChanged.

O registo de alterações DN é o contexto de nomenclatura utilizado pelo registo de alterações de delta, por exemplo **cn = changelog**. Este valor deve ser especificado para efetuar a importação delta.

Segue-se uma lista de registo de alterações de predefinição DNs:

| Diretório | Registo de alterações de delta |
| --- | --- |
| Microsoft AD LDS e AD GC |Detetadas automaticamente. USNChanged. |
| Servidor de diretório do Apache |Não está disponível. |
| Diretório 389 |Registo de alterações. Valor a utilizar predefinido: **cn = changelog** |
| IBM Tivoli DS |Registo de alterações. Valor a utilizar predefinido: **cn = changelog** |
| Diretório de Isode |Registo de alterações. Valor a utilizar predefinido: **cn = changelog** |
| Novell/NetIQ eDirectory |Não está disponível. TimeStamp. Utiliza o conector atualizado pela última vez data/hora para obter adicionados e atualizados registos. |
| Abra DJ/DS |Registo de alterações.  Valor a utilizar predefinido: **cn = changelog** |
| Abra LDAP |Registo de acesso. Valor a utilizar predefinido: **cn = accesslog** |
| Oracle DSEE |Registo de alterações. Valor a utilizar predefinido: **cn = changelog** |
| RadiantOne VDS |Diretório virtual. Depende do diretório ligado ao VDS. |
| Sun um servidor de diretório |Registo de alterações. Valor a utilizar predefinido: **cn = changelog** |

O atributo de palavra-passe é o nome do atributo que deve utilizar o conector para definir a palavra-passe na alteração da palavra-passe e a palavra-passe definida operações.
Este valor é predefinido como **userPassword** mas pode ser alterada quando for necessário para um sistema LDAP específico.

Na lista de partições adicionais, é possível adicionar os espaços de nomes adicionais detetados automaticamente. Por exemplo, esta definição pode ser utilizada se vários servidores, certifique-se um cluster lógico, o que deve todos ser importado ao mesmo tempo. Tal como o Active Directory pode ter vários domínios numa floresta, mas todos os domínios partilham um esquema, o mesmo pode simulated, introduzindo os espaços de nomes adicionais nesta caixa. Cada espaço de nomes pode importar a partir de diferentes servidores e é configurado adicionalmente na página Configurar partições e hierarquias. Utilize Ctrl + Enter para obter uma nova linha.

### <a name="configure-provisioning-hierarchy"></a>Configurar hierarquia de aprovisionamento
Esta página é utilizada para mapear o componente de DN, por exemplo UO, para o tipo de objeto que deve ser aprovisionado, por exemplo organizationalUnit.

![Hierarquia de aprovisionamento](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

Ao configurar a hierarquia de aprovisionamento, pode configurar o conector para criar automaticamente uma estrutura quando necessário. Por exemplo, se existir um espaço de nomes dc = contoso, dc = com e um novo cn de objeto = Joe, UO = Seattle, c = US, dc = contoso, dc = com é aprovisionado, em seguida, o conector pode criar um objeto de país de tipo para E.U.A. e um organizationalUnit para Seattle, se os ainda não estiverem presentes no diretório.

### <a name="configure-partitions-and-hierarchies"></a>Configurar partições e hierarquias
Na página de partições e hierarquias, selecione todos os espaços de nomes com objetos que pretende importar e exportar.

![Partições](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Para cada espaço de nomes, também é possível configurar as definições de conectividade que podem substituir os valores especificados no ecrã de conectividade. Se estes valores forem deixados no respectivo valor em branco predefinido, é utilizada a informação a partir do ecrã de conectividade.

Também é possível selecionar os contentores e UOs o conector deve importar do e exportação.

Quando efetuar uma procura isto é feito através de todos os contentores na partição. Nos casos em que existem grande número de contentores este comportamento leva a degradação do desempenho.

>[!NOTE]
A partir da atualização de Março de 2017 para o LDAP genérico pesquisas do conector podem ser limitadas na âmbito para apenas os contentores selecionados. Isto pode ser feito ao selecionar a caixa de verificação 'Pesquisa apenas em contentores selecionados', conforme mostrado na imagem abaixo.

![Pesquisa só selecionar contentores](./media/active-directory-aadconnectsync-connector-genericldap/partitions-only-selected-containers.png)

### <a name="configure-anchors"></a>Configurar âncoras
Esta página têm sempre um valor pré-configurada e não pode ser alterada. Se o fornecedor do servidor foi identificado, em seguida, a âncora de poderá ser preenchida com um atributo imutável, por exemplo o GUID para um objeto. Se este não foi detetada ou é conhecido por não ter um atributo imutável, o conector utiliza dn (nome distinto) como a âncora.

![âncoras](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)


Segue-se uma lista de servidores LDAP e a âncora de que está a ser utilizado:

| Diretório | Atributo âncora |
| --- | --- |
| Microsoft AD LDS e AD GC |objectGUID |
| Servidor de diretório 389 |DN |
| Apache diretório |DN |
| IBM Tivoli DS |DN |
| Diretório de Isode |DN |
| Novell/NetIQ eDirectory |GUID |
| Abra DJ/DS |DN |
| Abra LDAP |DN |
| Oracle ODSEE |DN |
| RadiantOne VDS |DN |
| Sun um servidor de diretório |DN |

## <a name="other-notes"></a>Outras notas
Esta secção fornece informações de aspetos específicos para este conector ou por outros motivos importante saber.

### <a name="delta-import"></a>Importação delta
Marca de água delta no LDAP abrir é UTC data/hora. Por este motivo, os relógios entre o serviço de sincronização do FIM e o LDAP abra têm de ser sincronizados. Caso contrário, poderão ser omitidas algumas entradas no registo de alterações de delta.

Para Novell eDirectory, a importação delta não é detetar as eliminações de objeto. Por este motivo, é necessário executar uma importação completa periodicamente para localizar todos os objetos eliminados.

Para diretórios com um registo de alterações de delta baseia-se na data/hora, recomenda vivamente para executar uma importação completa em alturas periódicas. Este processo permite que o motor de sincronização para localizar e dissimilarities entre o servidor LDAP e o que está atualmente no espaço de conector.

## <a name="troubleshooting"></a>Resolução de problemas
* Para obter informações sobre como ativar o registo resolver o conector, consulte o [como ativar o rastreio do ETW para conectores](http://go.microsoft.com/fwlink/?LinkId=335731).
