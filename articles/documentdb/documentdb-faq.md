<properties 
    pageTitle="Perguntas sobre a Base de Dados DocumentDB – Perguntas Mais Frequentes | Microsoft Azure" 
    description="Obtenha respostas às perguntas mais frequentes sobre o Azure DocumentDB, um serviço de bases de dados de documentos NoSQL para JSON. Responda a perguntas de bases de dados sobre a capacidade, níveis de desempenho e dimensionamento." 
    keywords="Database questions, frequently asked questions, documentdb, azure, Microsoft azure"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="03/30/2016" 
    ms.author="mimig"/>


#Perguntas mais frequentes acerca do DocumentDB

## Perguntas de base de dados sobre noções básicas do Microsoft Azure DocumentDB

### O que é o Microsoft Azure DocumentDB? 
O Microsoft Azure DocumentDB é uma base de dados como serviço de documentos NoSQL altamente dimensionáveis que oferece consultas avançadas sobre os dados sem esquema, ajuda a proporcionar um desempenho configurável e fiável e permite um desenvolvimento rápido, tudo através de uma plataforma gerida pela potência e alcance do Microsoft Azure. O DocumentDB é a solução ideal para aplicações Web, móveis, de jogos e de IoT quando o débito previsível, a latência baixa e um modelo de dados sem esquema são requisitos essenciais. O DocumentDB proporciona flexibilidade de esquema e indexação avançada através de um modelo de dados JSON nativo e inclui suporte de transacional com vários documentos com JavaScript integrado.  
  
Para obter mais perguntas de bases de dados, respostas e instruções sobre como implementar e utilizar este serviço, consulte a [página de documentação do DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

### Que tipo de base de dados é o DocumentDB?
O DocumentDB é uma base de dados orientada para documentos NoSQL que armazena os dados no formato JSON.  O DocumentDB suporta estruturas de dados autónomas aninhadas que podem ser consultadas através de uma [gramática de consulta SQL](documentdb-sql-query.md) de DocumentDB avançada. O DocumentDB proporciona um processamento transacional de elevado desempenho de JavaScript do lado do servidor através de [procedimentos armazenados, acionadores e funções definidas pelo utilizador](documentdb-programming.md). A base de dados também suporta níveis de consistência ajustáveis pelo programador com [níveis de desempenho](documentdb-performance-levels.md) associados.
 
### As bases de dados do DocumentDB têm tabelas como uma base de dados relacional (RDBMS)?
Não, o DocumentDB armazena os dados em coleções de documentos JSON.  Para obter informações sobre recursos do DocumentDB, consulte [Modelo e conceitos de recursos do DocumentDB](documentdb-resources.md). 

### As bases de dados do DocumentDB suportam dados sem esquema?
Sim, o DocumentDB permite que as aplicações armazenem documentos JSON arbitrários sem definição de esquema ou de sugestões. Os dados estão imediatamente disponíveis para consulta através da interface de consulta de SQL do DocumentDB.   

### O DocumentDB suporta transações ACID?
Sim, o DocumentDB suporta transações entre documentos expressas como procedimentos e acionadores armazenados no JavaScript . As transações estão confinadas a uma única partição dentro de cada coleção e executadas com semântica ACID como totalmente ou nada isoladas a partir de outros pedidos de código e de utilizador em execução simultânea.  Se as exceções forem emitidas através da execução do lado do servidor do código da aplicação JavaScript, toda a transação é revertida. 

### Quais são os casos de utilização típica para o DocumentDB?  
O DocumentDB é uma boa escolha para novas aplicações Web, de jogos, móveis e de IoT onde o dimensionamento automático, o desempenho previsível, a ordem rápida de tempos de resposta de milissegundos e a capacidade de consultar sobre os dados sem esquema é importante. O DocumentDB presta-se a um desenvolvimento rápido e suportar a iteração contínua de modelos de dados de aplicações. As aplicações que gerem conteúdos e dados gerados pelo utilizador são [casos de utilização comuns para o DocumentDB](documentdb-use-cases.md).  

### Como é que o DocumentDB proporciona um desempenho previsível?
Uma Unidade de Pedido (RU) é a medida de débito no DocumentDB. 1 RU corresponde ao débito do GET de um documento de 1 KB. Cada operação no DocumentDB, incluindo leituras, escritas, consultas SQL e execuções de procedimentos armazenados tem um valor de Unidade de Pedido de determinista baseado no débito necessário para concluir a operação. Em vez de pensar na CPU, na E/S e na memória e em como estas afetam o débito da sua aplicação, pode pensar em termos de uma medida de Pedido de Unidade única.

Cada coleção do DocumentDB pode ser reservada com débito aprovisionado em termos de Unidades de Pedido de débito por segundo. Para aplicações de qualquer escala, pode referenciar pedidos individuais para medir os respetivos valores de unidade de pedido e aprovisionar coleções para processar o total da soma de unidades de pedido em todos os pedidos. Também pode aumentar verticalmente ou reduzir verticalmente o débito da sua coleção à medida que as necessidades da sua aplicação evoluem. Para mais informações sobre unidades de pedido e para ajudar a determinar as necessidades da sua coleção, leia [Gerir o Desempenho e a Capacidade](documentdb-manage.md).

### O DocumentDB está em conformidade com a HIPAA?
Sim, o DocumentDB está em conformidade com a HIPAA. A HIPAA estabelece requisitos para a utilização, divulgação e salvaguarda das informações médicas pessoalmente identificáveis. Para obter mais informações, consulte o [Centro de Fidedignidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### Quais são os limites de armazenamento do DocumentDB? 
Não existe nenhum limite teórico para a quantidade total de dados que uma coleção pode armazenar no DocumentDB. Se pretende armazenar mais de 250 GB de dados dentro de uma única coleção, [contacte o suporte](documentdb-increase-limits.md) para aumentar a quota da sua conta. 

### Quais são os limites de débito do DocumentDB? 
Não existe nenhum limite teórico para a quantidade total de débito que uma coleção pode suportar no DocumentDB, se a sua carga de trabalho pode ser distribuída aproximadamente de modo uniforme entre um número de chaves de partição suficientemente grande. Se pretende exceder 250 000 unidades de pedido/segundo por coleção ou conta, [contacte o suporte](documentdb-increase-limits.md) para aumentar a quota da sua conta. 

### Quanto custa o Microsoft Azure DocumentDB?
Consulte a página [Detalhes de preços do DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/) para obter detalhes. Os custos de utilização do DocumentDB são determinados pelo número de coleções em utilização, o número de horas que as coleções estiveram online e o armazenamento consumido e débito aprovisionado para cada coleção. 

### Existe uma conta gratuita disponível?
Se nunca tiver tido uma conta do Azure, pode inscrever-se numa [conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/), que lhe oferece 30 dias e 200 $ para experimentar todos os serviços do Azure. Em alternativa, se tiver uma subscrição do Visual Studio, poderá beneficiar de [150 $ em créditos mensais gratuitos do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para utilizar em qualquer serviço Azure.  

### Como posso obter ajuda adicional com o DocumentDB?
Se precisar de qualquer ajuda, contacte-nos em [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb), nos [fóruns para programadores do Azure DocumentDB MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) ou agende uma [conversa de individual com a equipa de engenharia do DocumentDB](http://www.askdocdb.com/). Para se manter atualizado sobre as últimas novidades e funcionalidades do DocumentDB, siga-nos no [Twitter](https://twitter.com/DocumentDB).

## Configurar o Microsoft Azure DocumentDB

### Como posso inscrever-me no Microsoft Azure DocumentDB?
O Microsoft Azure DocumentDB está disponível no [Portal do Azure][azure-portal].  Primeiro, deve inscrever-se numa subscrição do Microsoft Azure.  Depois de se inscrever numa subscrição do Microsoft Azure, pode adicionar uma conta do DocumentDB à sua subscrição do Azure. Para obter instruções sobre como adicionar uma conta do DocumentDB, consulte [Criar uma conta de base de dados DocumentDB](documentdb-create-account.md).   

### O que é uma chave mestra?
Uma chave mestra é um token de segurança para aceder a todos os recursos de uma conta. Os indivíduos com a chave têm acesso de leitura e escrita em todos os recursos na conta de base de dados. Tenha cuidado ao distribuir chaves mestras. A chave mestra principal e a chave mestra secundária estão disponíveis no painel **Chaves ** do [Portal do Azure][azure-portal]. Para mais informações sobre chaves, consulte [Ver, copiar e voltar a gerar chaves de acesso](documentdb-manage-account.md#keys).

### Como crio uma base de dados?
Pode criar bases de dados ao utilizar o [Portal do Azure]() conforme descrito em [Criar uma base de dados DocumentDB](documentdb-create-database.md), um do [SDKs do DocumentDB](documentdb-sdk-dotnet.md) ou através de [APIs REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### O que é uma coleção?
Uma coleção é um contentor de documentos JSON e a lógica da aplicação associada JavaScript. Uma coleção é uma entidade faturável, onde o [custo](documentdb-performance-levels.md) é determinado pelo nível de desempenho associado à coleção. As coleções podem abranger uma ou mais partições/servidores e podem dimensionar para processar volumes praticamente ilimitados de armazenamento ou débito.

As coleções também são as entidades de faturação para o DocumentDB. Cada coleção é faturada de hora em hora com base no débito aprovisionado e o espaço de armazenamento utilizado. Para obter mais informações, consulte [Preços do DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/).  

### Como posso configurar utilizadores e permissões?
Pode criar utilizadores e permissões ao utilizar um dos [SDKs do DocumentDB](documentdb-sdk-dotnet.md) ou através de [APIs REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

## Perguntas de base de dados sobre programação com o Microsoft Azure DocumentDB

### Como faço para iniciar a programação com o DocumentDB?
Existem [SDKs](documentdb-sdk-dotnet.md) disponíveis para .NET, Python, Node.js, JavaScript e Java.  Os programadores também podem tirar partido de [APIs HTTP RESTful](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interagir com recursos do DocumentDB a partir de uma variedade de plataformas e idiomas. 

Estão disponíveis no GitHub exemplos para os SDKs [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) e [Python](documentdb-python-samples.md) do DocumentDB.

### O DocumentDB suporta SQL?
A linguagem de consulta de SQL do DocumentDB é um subconjunto avançado da funcionalidade de consulta suportada pelo SQL. A linguagem de consulta de SQL do DocumentDB fornece operadores avançados hierárquicos e relacionais e extensibilidade através de funções definidas pelo utilizador (UDFs) com base em JavaScript. A gramática JSON permite-lhe modelar documentos JSON como árvores com etiquetas como nós de árvores, que são utilizados pelas técnicas de indexação automática do DocumentDB, bem como pelo dialeto de consulta SQL do DocumentDB.  Para obter detalhes sobre como utilizar a gramática do SQL, consulte o artigo [DocumentDB de Consulta][consulta].

### Quais são os tipos de dados suportados pelo DocumentDB?
Os tipos de dados primitivos suportados no DocumentDB são os mesmos que no JSON. O JSON tem um sistema de tipo simples que consiste em Cadeias, Números (precisão dupla IEEE754) e em Booleanos – verdadeiros, falsos e Nulos.  Os tipos de dados mais complexos como Data/Hora, Guid, Int64 e Geometria podem ser representados tanto no JSON como no DocumentDB através da criação de objetos aninhados ao utilizar o operador {} e matrizes ao utilizar o operador []. 

### Como é que o DocumentDB fornece simultaneidade?
O DocumentDB suporta o controlo de simultaneidade otimista (OCC) através de etiquetas de entidade HTTP ou ETags. Cada recurso DocumentDB tem uma ETag e os clientes do DocumentDB incluem a versão mais recente de leitura nos pedidos de escrita. Se a ETag for atual, a alteração é consolidada. Se o valor tiver sido alterado externamente, o servidor rejeita a escrita com um código de resposta “Falha de pré-condição HTTP 412”. Os clientes têm de ler a versão mais recente do recurso e repetir o pedido. 

### Como posso efetuar transações no DocumentDB?
O DocumentDB suporta transações de linguagem integrada através de acionadores e procedimentos armazenados do JavaScript. Todas as operações de bases de dados no interior de scripts são executadas em isolamento de instantâneo no âmbito da coleção se esta for uma coleção de partição única ou documentos com o mesmo valor de chave de partição dentro de uma coleção, se a coleção estiver particionada. Um instantâneo das versões do documento (ETags) é tirado no início da transação e consolidado apenas se o script for concluído com êxito. Se o JavaScript emitir um erro, a transação é revertida. Consulte [Programação do lado do servidor do DocumentDB](documentdb-programming.md) para ver mais detalhes.

### Como posso inserir em massa documentos para o DocumentDB? 
Existem três formas de inserir em massa documentos para o DocumentDB:

- A ferramenta de migração de dados, conforme descrito em [Importar dados para o DocumentDB](documentdb-import-data.md).
- Explorador de Documentos no Portal do Azure, conforme descrito em [Adicionar documentos em massa com o Explorador de Documentos](documentdb-view-json-document-explorer.md#BulkAdd).
- Procedimentos armazenados, conforme descrito em [Programação do lado do servidor do DocumentDB](documentdb-programming.md).

### O DocumentDB suporta a colocação em cache de ligações de recursos?
Sim, porque o DocumentDB é um serviço RESTful, as ligações de recursos são imutáveis e podem ser colocadas em cache. Os clientes do DocumentDB podem especificar um cabeçalho de “Se-Nenhuma-Correspondência” para leituras em relação a quaisquer recursos, como o documento ou coleção e atualizar as respetivas cópias locais apenas quando a versão do servidor tiver sido alterada. 




[azure-portal]: https://portal.azure.com
[consulta]: documentdb-sql-query.md
 


<!--HONumber=Jun16_HO2-->


