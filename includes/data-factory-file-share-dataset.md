## <a name="fileshare-dataset-type-properties"></a>Propriedades do tipo de conjunto de dados de partilha de ficheiros
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](../articles/data-factory/v1/data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

O **typeProperties** secção é diferente para cada tipo de conjunto de dados. Fornece informações que são específicas para o tipo de conjunto de dados. Os typeProperties secção para um conjunto de dados do tipo **FileShare** dataset tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho de subchaves para a pasta. Utilize o caráter de escape ' \ ' para carateres especiais na cadeia. Consulte [exemplo ligado definições de serviço e o conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Pode combinar esta propriedade com **partitionBy** ter pasta caminhos com base no setor início/fim tempos de data. |Sim |
| fileName |Especifique o nome do ficheiro no **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não for especificado qualquer valor para esta propriedade, a tabela de pontos a todos os ficheiros na pasta.<br/><br/>Quando não for especificado o nome de ficheiro para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: <br/><br/>Dados. <Guid>. txt (exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| fileFilter |Especifique um filtro para ser utilizado para selecionar um subconjunto de ficheiros em folderPath em vez de todos os ficheiros.<br/><br/>Valores permitidos são: `*` (vários carateres) e `?` (único caráter).<br/><br/>Exemplos 1:`"fileFilter": "*.log"`<br/>Exemplo 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter se aplica a um conjunto de dados de partilha de ficheiros de entrada. Esta propriedade não é suportada com HDFS. |Não |
| partitionedBy |partitionedBy pode ser utilizado para especificar um folderPath dinâmica, o nome de ficheiro de dados de séries de tempo. Por exemplo, folderPath parametrizada para cada hora dos dados. |Não |
| formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade de formato para um destes valores. Para obter mais informações, consulte [formato de texto](#specifying-textformat), [formato Json](#specifying-jsonformat), [formato Avro](#specifying-avroformat), [Orc formato](#specifying-orcformat), e [Parquet formato](#specifying-parquetformat) secções. <br><br> Se pretender **copiar ficheiros como-é** entre arquivos baseados em ficheiros (cópia binário), ignorar a secção de formato em ambas as definições do conjunto de dados de entrada e de saída. |Não |
| Compressão | Especifique o tipo e o nível de compressão de dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**; e níveis suportados são: **Optimal** e **mais rápido**. Para obter mais informações, consulte [especificando compressão](#specifying-compression) secção. |Não |
| useBinaryTransfer |Especifique se utilizar o modo de transferência de binários. Verdadeiro para o modo binário e ASCII FALSO. Valor predefinido: True. Esta propriedade só pode ser utilizada quando o tipo de serviço ligado associado é do tipo: FtpServer. |Não |

> [!NOTE]
> nome de ficheiro e fileFilter não podem ser utilizados em simultâneo.
>
>

### <a name="using-partionedby-property"></a>Utilizar a propriedade partionedBy
Tal como mencionado na secção anterior, pode especificar um folderPath dinâmica, o nome de ficheiro de dados de séries de tempo com partitionedBy. Pode fazê-lo com as macros fábrica de dados e a variável de sistema SliceStart, SliceEnd que indicam o período de tempo de lógica de um setor de dados indicado.

Para mais informações sobre conjuntos de dados de séries de tempo, agendar e setores, consulte o artigo [criar conjuntos de dados](../articles/data-factory/v1/data-factory-create-datasets.md), [agendamento e execução](../articles/data-factory/v1/data-factory-scheduling-and-execution.md), e [criar Pipelines](../articles/data-factory/v1/data-factory-create-pipelines.md) artigos.

#### <a name="sample-1"></a>Exemplo 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Neste exemplo é substituído {setor} com o valor da variável de sistema do Data Factory SliceStart no formato (YYYYMMDDHH) especificado. SliceStart refere-se a hora do setor. FolderPath é diferente para cada setor. Exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemplo 2:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em separado variáveis que são utilizadas pelas propriedades folderPath e nome de ficheiro.
