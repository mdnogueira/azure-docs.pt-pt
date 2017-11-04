## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Especificar a definição de estrutura para conjuntos de dados retangular
A secção de estrutura nos conjuntos de dados JSON é uma **opcional** secção para tabelas retangular (com linhas e colunas) e contém uma coleção de colunas da tabela. Irá utilizar a secção de estrutura para o fornecer informações de tipo para conversões de tipo ou fazer mapeamentos da coluna. As secções seguintes descrevem estas funcionalidades em detalhe. 

Cada coluna contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da coluna. |Sim |
| tipo |Tipo de dados da coluna. Consulte a secção de conversões de tipo abaixo para mais detalhes sobre quando deve especificar as informações de tipo |Não |
| Cultura |.NET com base idioma a ser utilizado quando o tipo for especificado, não sendo tipo .NET Datetime ou Datetimeoffset. Predefinição é "en-us". |Não |
| formato |Cadeia de formato a utilizar quando o tipo for especificado, não sendo .NET tipo Datetime ou Datetimeoffset. |Não |

O exemplo seguinte mostra a secção de estrutura JSON para uma tabela que tem três colunas userid, o nome e lastlogindate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Utilize as diretrizes seguintes para quando incluir informações "estrutura" e o que pretende incluir no **estrutura** secção.

* **Para origens de dados estruturados** que o arquivo de esquema e o tipo de informações de dados, juntamente com dados propriamente ditos (origens, como a tabela do Azure do SQL Server, Oracle, etc.), deve especificar a secção "estrutura" se quiser fazer mapeamento de colunas de origem específico colunas a colunas específicas no sink e os respetivos nomes não são da mesma (consulte os detalhes na secção de mapeamento de coluna abaixo). 
  
    Tal como mencionado acima, as informações de tipo são opcionais na secção "estrutura". Para origens de structured, informações de tipo já se encontra disponíveis como parte da definição de conjunto de dados no arquivo de dados, por isso, não deve incluir informações sobre o tipo ao incluir a secção "estrutura".
* **Para o esquema em origens de dados de leitura (especificamente BLOBs do Azure)** pode optar por armazenar os dados sem armazenar quaisquer informações de esquema ou tipo com os dados. Para estes tipos de origens de dados deve incluir "estrutura" nos seguintes 2 casos:
  * Que pretende efetuar o mapeamento de colunas.
  * Quando o conjunto de dados é uma origem de uma atividade de cópia, pode fornecer informações sobre o tipo de "estrutura" e fábrica de dados utilizará estas informações de tipo para a conversão para tipos de nativos para o sink. Consulte [mover dados para e de Blob do Azure](../articles/data-factory/v1/data-factory-azure-blob-connector.md) artigo para obter mais informações.

### <a name="supported-net-based-types"></a>Suportado. Tipos de rede
Fábrica de dados suporta os seguintes CLS compatíveis .NET com base em tipo valores para fornecer informações sobre o tipo de "estrutura" para o esquema em origens de dados de leitura, como o blob do Azure.

* Int16
* Int32 
* Int64
* Único
* duplo
* Decimal
* Byte]
* bool
* Cadeia 
* GUID
* DateTime
* Datetimeoffset
* Timespan 

Para Datetime & Datetimeoffset, também pode especificar opcionalmente cadeia "Cultura" & "format" para facilitar a analisar a cadeia de Datetime personalizada. Consulte o exemplo para a conversão de tipo abaixo.

