---
title: "Conector do SQL Server genérico | Microsoft Docs"
description: "Este artigo descreve como configurar o conector do SQL Server genérico da Microsoft."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: fd8ccef3-6605-47ba-9219-e0c74ffc0ec9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: billmath
ms.openlocfilehash: 81bacc39d974dfbd1b2aa8dce9e629c508203811
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="generic-sql-connector-technical-reference"></a>Referência técnica de conector do SQL Server genérica
Este artigo descreve o conector do SQL Server genérico. O artigo aplica-se os seguintes produtos:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Tem de utilizar correção 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2, o conector está disponível como uma transferência a partir do [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

Para ver este conector em ação, consulte o [genérico conector do SQL Server passo a passo](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) artigo.

## <a name="overview-of-the-generic-sql-connector"></a>Descrição geral do conector do SQL Server genérico
O conector do SQL Server genérico permite-lhe integrar o serviço de sincronização com um sistema de base de dados que oferece a conectividade ODBC.  

Numa perspetiva de alto nível, as seguintes funcionalidades são suportadas pela versão atual do conector:

| Funcionalidade | Suporte |
| --- | --- |
| Origem de dados ligada |O conector é suportado com todos os controladores ODBC de 64 bits. Foi testado com o seguinte: <li>Microsoft SQL Server e SQL Azure</li><li>IBM DB2 10</li><li>IBM DB2 9.x</li><li>G Oracle 10 e 11</li><li>MySQL 5. x</li> |
| Cenários |<li>Gestão de ciclo de vida do objeto</li><li>Gestão de palavra-passe</li> |
| Operações |<li>Importação completa e a importação Delta, exportação</li><li>Para exportar: Adicionar, eliminar, atualização e substituir</li><li>Definir a palavra-passe, altere a palavra-passe</li> |
| Esquema |<li>Deteção dinâmica de objetos e atributos</li> |

### <a name="prerequisites"></a>Pré-requisitos
Antes de utilizar o conector, certifique-se de que tem o seguinte no servidor de sincronização:

* Arquitetura do Microsoft .NET 4.5.2 ou posterior
* controladores de cliente ODBC de 64 bits

### <a name="permissions-in-connected-data-source"></a>Permissões na origem de dados ligada
Para criar ou Efetue qualquer uma das tarefas suportadas no conector do SQL Server genérico, tem de ter:

* db_datareader
* db_datawriter

### <a name="ports-and-protocols"></a>Portas e protocolos
Para as portas necessárias para o controlador ODBC funcionar, consulte a documentação do fornecedor de base de dados.

## <a name="create-a-new-connector"></a>Criar um novo conector
Para criar um conector do SQL Server genérico, no **serviço de sincronização** selecione **agente de gestão** e **criar**. Selecione o **SQL genérico (Microsoft)** conector.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>Conectividade
O conector utiliza um ficheiro de ODBC DSN conectividade. Criar o ficheiro de DSN utilizando **origens de dados de ODBC** encontrado no menu Iniciar em **ferramentas administrativas**. A ferramenta administrativa, criar um **ficheiro DSN** pelo que pode ser fornecido para o conector.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

O ecrã de conectividade é o primeiro ao criar um novo conector genérico do SQL Server. Primeiro tem de fornecer as seguintes informações:

* Caminho do ficheiro DSN
* Autenticação
  * Nome de Utilizador
  * Palavra-passe

A base de dados deve suportar um dos seguintes métodos de autenticação:

* **Autenticação do Windows**: A base de dados de autenticação utiliza as credenciais do Windows para verificar o utilizador. O utilizador nome/palavra-passe especificada é utilizado para autenticar com a base de dados. Esta conta necessita de permissões para a base de dados.
* **Autenticação do SQL Server**: as utilizações de base de dados de autenticação de nome de utilizador/palavra-passe definido um ecrã de conectividade para ligar à base de dados. Se armazenar o nome de utilizador/pasword no ficheiro DSN, as credenciais fornecidas no ecrã de conectividade têm precedência.
* **Autenticação de base de dados SQL do Azure**: para obter mais informações, consulte [ligar ao SQL Server da base de dados ao utilizar Azure autenticação do Active Directory](../../sql-database/sql-database-aad-authentication.md).

**DN é âncora**: Se selecionar esta opção, o DN também é utilizado como o atributo âncora. Que pode ser utilizado para uma implementação simple, mas também tem a limitação seguinte:

* Conector suporta o tipo de apenas um objeto. Por conseguinte, qualquer atributos de referência só podem referenciar o mesmo tipo de objeto.

**Tipo de exportação: Substituição de objetos**: durante a exportação, quando foram alterados apenas alguns atributos, todo o objeto com todos os atributos é exportado e substitui o objeto existente.

### <a name="schema-1-detect-object-types"></a>Esquema 1 (detetar os tipos de objeto)
Nesta página, vai configurar a forma como o conector vai localizar os tipos de objeto diferentes na base de dados.

Cada tipo de objeto é apresentado como uma partição e configurado adicional no **configurar partições e hierarquias**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Método de deteção de tipo de objeto**: O conector suporta estes métodos de deteção de tipo de objeto.

* **Valor fixo**: fornecer a lista de tipos de objeto com uma lista separada por vírgulas. Por exemplo: `User,Group,Department`.  
  ![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
* **Tabela/vista/armazenado procedimento**: forneça o nome do procedimento de tabela/vista/armazenados e, em seguida, o nome da coluna que fornece a lista de tipos de objeto. Se utilizar um procedimento armazenado, em seguida, também fornecer parâmetros para o mesmo no formato **[nome]: [direção]: [Value]**. Forneça cada parâmetro numa linha separada (utilize Ctrl + Enter para obter uma nova linha).  
  ![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
* **Consulta SQL**: esta opção permite-lhe fornecer uma consulta SQL que devolva uma única coluna com os tipos de objeto, por exemplo `SELECT [Column Name] FROM TABLENAME`. A coluna devolvida tem de ser do tipo cadeia (varchar).

### <a name="schema-2-detect-attribute-types"></a>Esquema 2 (detetar tipos de atributo)
Nesta página, vai configurar como os nomes de atributo e os tipos que vão ser detetado. As opções de configuração são listadas para cada tipo de objeto detetado na página anterior.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Método de deteção de tipo de atributo**: O conector suporta estes métodos de deteção de tipo de atributo com cada tipo de objeto detetadas no ecrã de 1 de esquema.

* **Tabela/vista/armazenado procedimento**: forneça o nome do procedimento de tabela/vista/armazenados de mensagens em fila que deve ser utilizado para localizar os nomes de atributo. Se utilizar um procedimento armazenado, em seguida, também fornecer parâmetros para o mesmo no formato **[nome]: [direção]: [Value]**. Forneça cada parâmetro numa linha separada (utilize Ctrl + Enter para obter uma nova linha). Para detetar os nomes de atributo num atributo com múltiplos valor, forneça uma lista separada por vírgulas de tabelas ou vistas. Cenários com múltiplos valores não são suportados quando o tabela principais e subordinados têm os mesmos nomes de coluna.
* **Consulta SQL**: esta opção permite-lhe fornecer uma consulta SQL que devolva uma única coluna com os nomes de atributos, por exemplo `SELECT [Column Name] FROM TABLENAME`. A coluna devolvida tem de ser do tipo cadeia (varchar).

### <a name="schema-3-define-anchor-and-dn"></a>Esquema 3 (definir âncora e DN)
Esta página permite-lhe configurar âncora e atributo DN para cada tipo de objeto detetado. Pode selecionar vários atributos para tornar a âncora exclusivo.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

* Atributos com múltiplos valores e booleanos não estão listados.
* Mesmo atributo não pode utilizar para DN e âncora, a menos que **DN é âncora** está selecionado na página de Conetividade.
* Se **DN é âncora** está selecionado na página de Conetividade, esta página requer apenas o atributo de DN. Este atributo também poderia ser utilizado como o atributo âncora.

  ![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>Esquema 4 (tipo de atributo definir, referência e direção)
Esta página permite-lhe configurar o tipo de atributo, tal como número inteiro, binary, ou Booleano e direção para cada atributo. Todos os atributos a partir da página **esquema 2** estão listados, incluindo atributos com múltiplos valores.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

* **Tipo de dados**: utilizado para mapear o tipo de atributo para os tipos conhecidos por motor de sincronização. A predefinição é utilizar o mesmo tipo detetado no esquema do SQL Server, mas DateTime e de referência não sejam facilmente Detetáveis. Os, tem de especificar **DateTime** ou **referência**.
* **Direção**: pode definir a direção de atributo para importação e exportação ou ImportExport. ImportExport é a predefinição.

![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Notas:

* Se um tipo de atributo não é detetável pelo conector, utiliza o tipo de dados de cadeia.
* **Aninhada de tabelas** podem ser consideradas tabelas de base de dados de uma coluna. Oracle armazena as linhas de uma tabela aninhada não ordem específica. No entanto, quando é possível obter a tabela aninhada numa variável LP/SQL, as linhas são indicadas índices consecutivos começando em 1. Que dá-lhe acesso semelhantes a matrizes às linhas individuais.
* **VARRYS** não são suportadas no conector.

### <a name="schema-5-define-partition-for-reference-attributes"></a>Esquema 5 (Definir partição para atributos de referência)
Nesta página, configurar para todos os atributos de referência que (tipo de objeto) da partição faz referência a um atributo.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Se utilizar **DN é âncora**, em seguida, tem de utilizar o mesmo tipo de objeto que estão a referência do. Não é possível referenciar outro tipo de objeto.

>[!NOTE]
A partir da atualização de Março de 2017 agora é uma opção para "*" quando esta opção é escolhido, em seguida, todos os tipos de membro possíveis serão importados.

![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/any-option.png)

>[!IMPORTANT]
 A partir de Maio de 2017 o "*" aka **qualquer opção** foi alterado para suportar a importar e exportar fluxo. Se quiser utilizar esta opção com múltiplos valor tabela/vista deve ter um atributo que contém o tipo de objeto.

![](./media/active-directory-aadconnectsync-connector-genericsql/any-02.png)

 </br> Se "*" for selecionado, em seguida, também tem de ser especificado o nome da coluna com o tipo de objeto.</br> ![](./media/active-directory-aadconnectsync-connector-genericsql/any-03.png)

Após a importação, verá algo semelhante para a imagem abaixo:

  ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/after-import.png)



### <a name="global-parameters"></a>Parâmetros globais
A página de parâmetros globais é utilizada para configurar a importação Delta, o formato de data/hora e o método de palavra-passe.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)



O conector do SQL Server genérico suporta os seguintes métodos para a importação Delta:

* **Acionador**: consulte [gerar vistas de diferenças utilizando Acionadores](https://technet.microsoft.com/library/cc708665.aspx).
* **Marca de água**: uma abordagem genérica que pode ser utilizada com qualquer base de dados. A consulta de marca de água está pré-preenchidos com base no fornecedor de base de dados. Uma coluna de marca de água deve estar presente em cada tabela/vista utilizada. Esta coluna tem de controlar inserções e atualizações para as tabelas como e o respetivo dependentes (com múltiplos valores ou subordinado) tabelas. Os relógios entre o serviço de sincronização e o servidor de base de dados têm de ser sincronizados. Caso contrário, poderão ser omitidas algumas entradas na importação delta.  
  Limitação:
  * Estratégia de marca de água não não os objetos de suporte eliminado.
* **Instantâneo**: (funciona apenas com o Microsoft SQL Server) [gerar vistas de diferenças através de instantâneos](https://technet.microsoft.com/library/cc720640.aspx)
* **Registo de alterações**: (funciona apenas com o Microsoft SQL Server) [sobre o registo de alterações](https://msdn.microsoft.com/library/bb933875.aspx)  
  Limitações:
  * Âncora & atributo DN devem fazer parte da chave primária para o objeto selecionado na tabela.
  * Consulta SQL não é suportada durante a importação e exportação com controlo de alterações.

**Os parâmetros adicionais**: Especifique o servidor da base de dados fuso horário que indica onde está localizado o servidor de base de dados. Este valor é utilizado para suportar os vários formatos de atributos de data e hora.

O conector armazena sempre data e a data / hora em formato UTC. Para conseguir corretamente converter a data e vezes, o fuso horário do servidor da base de dados e o formato utilizado deve ser especificado. O formato deve ser expressa no formato de .net.

Durante a exportação cada atributo de tempo de data tem de ser fornecido para o conector no formato de hora UTC.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Configuração de palavra-passe**: O conector fornece capacidades de sincronização de palavra-passe e suporta define e alterar palavra-passe.

O conector fornece dois métodos para suportar a sincronização de palavra-passe:

* **Procedimento armazenado**: Este método requer dois procedimentos armazenados para suportar o conjunto de & Alterar palavra-passe. Escreva todos os parâmetros para adicionar e alterar a operação de palavra-passe em **SP de palavra-passe definida** e **SP de alteração de palavra-passe** parâmetros respetivamente como por exemplo abaixo.
  ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
* **Extensão de palavra-passe**: Este método requer a DLL de extensão de palavra-passe (tem de fornecer o nome da DLL de extensão que está a implementar o [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) interface). Assemblagem da extensão de palavra-passe tem de ser colocada na pasta de extensão para que o conector pode carregar a DLL no tempo de execução.
  ![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Também tem de ativar a gestão de palavra-passe no **configurar extensão** página.
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurar partições e hierarquias
Na página de partições e hierarquias, selecione todos os tipos de objeto. Cada tipo de objeto é a sua própria partição.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

Também pode substituir os valores definidos no **conectividade** ou **parâmetros globais** página.

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>Configurar âncoras
Esta página é só de leitura, uma vez que a âncora já foi definida. O atributo âncora selecionado é sempre acrescentado com o tipo de objeto para garantir que continua a ser exclusivo em tipos de objeto.

![âncoras](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>Configurar parâmetros de passo de execução
Estes passos são configurados nos perfis de execução no conector. Estas configurações fazem o trabalho real de importar e exportar dados.

### <a name="full-and-delta-import"></a>Completo e a importação Delta
Genérico suporte completo de conector do SQL Server e a importação Delta utilizando estes métodos:

* Tabela
* Vista
* Procedimento Armazenado
* Consulta SQL

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Tabela/vista**  
Para importar atributos com múltiplos valores para um objeto, tem de fornecer o nome da tabela/vista separada por vírgulas no **com nome de vários valores/vistas de tabelas** e condições da respetiva associação no **associar condição** com a tabela principal.

Exemplo: Que pretende importar o objeto do empregado e todos os atributos com múltiplos valores. Existem duas tabelas, com o nome de empregado (tabela principal) e o departamento (com múltiplos valor).
Faça o seguinte:

* Tipo **empregado** no **tabela/vista/SP**.
* Tipo de departamento numa **com nome de vários valores/vistas de tabelas**.
* Escreva a condição de associação entre o empregado & departamento numa **condição de associação**, por exemplo `Employee.DEPTID=Department.DepartmentID`.
  ![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Procedimentos armazenados**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

* Se tiver a quantidade de dados, recomenda-se para implementar a paginação com os procedimentos armazenados.
* Para obter o procedimento armazenado suporta paginação, terá de fornecer o índice de início e fim índice. Consulte: [paginação de forma eficiente através de grandes quantidades de dados](https://msdn.microsoft.com/library/bb445504.aspx).
* @StartIndexe @EndIndex são substituídos durante a execução com o valor de tamanho de página respetivos configurado no **passo configurar** página. Por exemplo, quando o conector obtém a primeira página e o tamanho da página está definida 500, essa situação @StartIndex seria 1 e @EndIndex 500. Estes valores aumentam quando o conector obtém as páginas subsequentes e altere o @StartIndex & @EndIndex valor.
* Para executar o procedimento armazenado parametrizada, fornecer os parâmetros `[Name]:[Direction]:[Value]` formato. Introduza cada um dos parâmetros numa linha separada (utilize Ctrl + Enter para obter uma nova linha).
* Conector do SQL Server genérico também suporta a operação de importação de servidores ligados no Microsoft SQL Server. Se a informações devem ser obtidas a partir de uma tabela no servidor ligado, tabela deve ser fornecida no formato:`[ServerName].[Database].[Schema].[TableName]`
* Conector genérico do SQL Server suporta apenas os objetos que tenham estrutura semelhante à (os alias nome e tipo de dados) entre executar passos de deteção de informações e o esquema. Se o objeto selecionado do esquema e as informações fornecidas no passo de execução for diferente, não é possível suportar este tipo de cenários de conector do SQL Server.

**Consulta SQL**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

* Resultado vários define consultas não suportadas.
* Consulta de SQL Server suporta a paginação e fornecer início índice e o índice de fim como uma variável para suportar a paginação.

### <a name="delta-import"></a>Importação delta
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Configuração da importação delta necessita de configuração mais comparada a importação completa.

* Se escolher a abordagem de Acionador ou um instantâneo para controlar as alterações de delta, em seguida, forneça a tabela de histórico ou o instantâneo da base de dados no **nome de base de dados de tabela de histórico ou instantâneo** caixa.
* Terá também de fornecer a condição de associação entre a tabela de histórico e a tabela principal, por exemplo`Employee.ID=History.EmployeeID`
* Para controlar a transação na tabela principal da tabela de histórico, tem de fornecer o nome da coluna que contém as informações da operação (adicionar/atualizar/eliminar).
* Se escolher a marca d'água para controlar as alterações de delta, em seguida, forneça o nome da coluna que contém as informações de operação em **nome da coluna marcar máximo**.
* O **alterar o atributo de tipo** coluna é necessária para o tipo de alteração. Esta coluna mapeia uma alteração que ocorre na tabela primária ou de tabela de valor múltiplos para um tipo de alteração na vista de diferenças. Esta coluna pode conter o tipo de alteração de Modify_Attribute para alteração do nível de atributo ou um adicionar, modificar, ou eliminar alterar tipo para um tipo de alteração ao nível do objeto. Se for algo que o valor predefinido de adicionar, modificar, ou eliminar, em seguida, pode definir esses valores a utilizar esta opção.

### <a name="export"></a>Exportar
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Conector do SQL Server genérico suportam exportação utilizando quatro métodos suportados, tais como:

* Tabela
* Vista
* Procedimento Armazenado
* Consulta SQL

**Tabela/vista**  
Se escolher a opção de tabela/vista, o conector gera as respetivas consultas para fazer a exportação.

**Procedimentos armazenados**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Se escolher a opção de procedimento armazenado, exportação requer três os procedimentos armazenados diferentes para efetuar operações de inserir/atualizar/eliminar.

* **Adicionar nome SP**: SP este é executado se qualquer objeto é fornecido para o conector para inserção na respetiva tabela.
* **Atualizar o nome de SP**: SP este é executado se qualquer objeto é fornecido para o conector para atualização na respetiva tabela.
* **Eliminar o nome de SP**: SP este é executado se qualquer objeto é fornecido para o conector para eliminação na tabela correspondentes.
* Atributo selecionado o esquema utilizado como um valor de parâmetro para o procedimento armazenado. Por exemplo, `@EmployeeName: INPUT: EmployeeName` (EmployeeName está selecionado no esquema do conector e o conector substitui o respetivo valor durante a exportação)
* Para executar o procedimento armazenado parametrizado, fornecer os parâmetros no `[Name]:[Direction]:[Value]` formato. Introduza cada um dos parâmetros numa linha separada (utilize Ctrl + Enter para obter uma nova linha).

**Consulta SQL**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Se escolher a opção de consulta SQL, exportação requer três diferentes consultas para executar operações de inserir/atualizar/eliminar.

* **Inserir consulta**: esta consulta executa se qualquer objeto é fornecido para o conector para inserção na respetiva tabela.
* **Atualizar a consulta**: esta consulta executa se qualquer objeto é fornecido para o conector para atualização na respetiva tabela.
* **Eliminar consulta**: esta consulta executa se qualquer objeto é fornecido para o conector para eliminação na tabela correspondentes.
* Atributo selecionado o esquema utilizado como um valor de parâmetro para a consulta, por exemplo`Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>Resolução de problemas
* Para obter informações sobre como ativar o registo resolver o conector, consulte o [como ativar o rastreio do ETW para conectores](http://go.microsoft.com/fwlink/?LinkId=335731).
