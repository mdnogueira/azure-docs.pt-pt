## <a name="repeatability-during-copy"></a>Repetibilidade durante a cópia
Quando copiar dados de origem e de lojas relacionais, terá de manter a repetibilidade em mente para evitar resultados inesperados. 

Um setor pode ser executada novamente automaticamente no Azure Data Factory de acordo com a política de repetição especificada. Recomendamos que defina uma política de repetição para proteger contra falhas transitórias. Por conseguinte, repetibilidade é um aspeto importante a asseguramos durante o movimento de dados. 

**Como uma origem:**

> [!NOTE]
> Os exemplos seguintes são para o Azure SQL, mas são aplicáveis a qualquer arquivo de dados que suporta conjuntos de dados retangular. Terá de ajustar o **tipo** de origem e o **consulta** propriedade (por exemplo: consulta em vez de sqlReaderQuery) para os dados de arquivo.   
> 
> 

Normalmente, ao ler do relacionais arquivos, iria querer ler apenas os dados correspondente a essa setor. Uma forma de fazê-lo seria utilizando as variáveis WindowStart e WindowEnd disponíveis no Azure Data Factory. Leia sobre as variáveis e funções no Azure Data Factory aqui no [agendamento e execução](../articles/data-factory/v1/data-factory-scheduling-and-execution.md) artigo. Exemplo: 

```json
"source": {
"type": "SqlSource",
"sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Esta consulta lê os dados de 'MyTable', que se situe no intervalo de duração do setor. Volte a executar este setor seria também Certifique-se sempre este comportamento. 

Noutros casos, pode pretender ler a tabela inteira (suponha para uma vez mover apenas) e pode definir o sqlReaderQuery da seguinte forma:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```
