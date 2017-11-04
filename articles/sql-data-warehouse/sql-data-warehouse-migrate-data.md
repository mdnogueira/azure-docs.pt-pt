---
title: Migrar os dados ao SQL Data Warehouse | Microsoft Docs
description: "Sugestões para migrar os dados para o Azure SQL Data Warehouse para desenvolver soluções."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: d78f954a-f54c-4aa4-9040-919bc6414887
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/29/2017
ms.author: joeyong;barbkess
ms.openlocfilehash: 0d156bc2eecf8220bd5ff4eb811d91482f216837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-data"></a>Migrar os dados
Podem ser mover os dados de diferentes origens para o SQL Data Warehouse com ferramentas de várias.  ADF cópia, SSIS e bcp podem todos ser utilizadas para atingir este objetivo. No entanto, como a quantidade de dados aumenta necessário pensar sobre interrompendo para baixo o processo de migração de dados para os passos. Isto affords a oportunidade para otimizar a cada passo para o desempenho e resiliência garantir uma migração de dados uniforme.

Este artigo aborda primeiro os cenários de migração simples de cópia de ADF, SSIS e bcp. Em seguida, procure um pouco mais profundo na forma como a migração pode ser otimizada.

## <a name="azure-data-factory-adf-copy"></a>Cópia de fábrica de dados (ADF) do Azure
[Cópia de ADF] [ ADF Copy] faz parte de [do Azure Data Factory][Azure Data Factory]. Pode utilizar a cópia de ADF para exportar os dados para ficheiros simples que reside no armazenamento local, para ficheiros simples remotos contido no armazenamento de Blobs do Azure ou diretamente para o SQL Data Warehouse.

Se os dados é iniciada no ficheiros simples, em seguida, primeiro terá de transferi-lo para o blob storage do Azure antes de iniciar uma carga-lo para o SQL Data Warehouse. Depois dos dados são transferidos para o armazenamento de Blobs do Azure pode optar por utilizar [ADF cópia] [ ADF Copy] novamente para enviar os dados para o SQL Data Warehouse.

O PolyBase fornece também uma opção de elevado desempenho para carregar os dados. No entanto, que significa utilizar duas ferramentas em vez de um. Se precisar de obter o melhor desempenho e utilize o PolyBase. Se pretender uma experiência única ferramenta (e os dados não estão em grande escala) ADF é a sua resposta.


> 
> 

Vá ao longo para o seguinte artigo para algumas ótimo [amostras ADF][ADF samples].

## <a name="integration-services"></a>Serviços de Integração
Integration Services (SSIS) é um poderosa e flexível extrair transformação e carregamento (ETL) ferramenta suporta fluxos de trabalho complexos, transformação de dados e várias opções de carregamento de dados. Utilize SSIS simplesmente transferir dados para o Azure ou como parte de uma migração de mais ampla.

> [!NOTE]
> SSIS pode exportar para UTF-8 sem a marca de ordem de bytes no ficheiro. Para configurar este, primeiro tem de utilizar o componente de coluna derivada converter os dados de caráter no fluxo de dados para utilizar a página de códigos do UTF-8 65001. Depois de tem sido convertidas as colunas, escreva os dados para o adaptador de destino com ficheiros simples, garantindo que 65001 também foi selecionada como a página de código para o ficheiro.
> 
> 

SSIS estabelece ligação ao SQL Data Warehouse, tal como que iria a estabelecer ligação a uma implementação do SQL Server. No entanto, as suas ligações terá de estar a utilizar um Gestor de ligação do ADO.NET. Deve também ter cuidado para configurar a "utilização inserção em massa quando disponível" definição para maximizar o débito. Consulte o [adaptador de destino do ADO.NET] [ ADO.NET destination adapter] artigo para obter mais informações sobre esta propriedade

> [!NOTE]
> Ligar ao Azure SQL Data Warehouse utilizando OLEDB não é suportada.
> 
> 

Além disso, não há sempre a possibilidade de um pacote poderão falhar devido a problemas de rede ou de limitação. Estrutura de pacotes, para que podem ser retomados at the point of falha, sem refazer o de trabalho que foram concluídos antes da falha.

Para obter mais informações, consulte o [documentação SSIS][SSIS documentation].

## <a name="bcp"></a>bcp
BCP é um utilitário da linha de comandos que foi concebido para a importação de dados de ficheiro simples e exportação. Alguns transformação pode ocorrer durante a exportação de dados. Para efetuar transformações simples utilizam uma consulta para selecionar e transformar os dados. Assim que exportado, os ficheiros simples podem, em seguida, ser carregados diretamente para o destino da base de dados do armazém de dados do SQL Server.

> [!NOTE]
> Muitas vezes, é boa ideia encapsulam as transformações utilizadas durante a exportação de dados numa vista no sistema de origem. Isto garante que é mantida a lógica e o processo é repetido.
> 
> 

Vantagens do bcp são:

* Simplicidade. comandos do BCP são simples compilar e executar
* Processo de carregamento iniciável novamente. Uma vez exportado a carga pode ser executado qualquer número de vezes

Limitações do bcp são:

* BCP funciona com apresentadas simples apenas ficheiros. Não funciona com ficheiros, tais como xml ou JSON
* Capacidades de transformação de dados estão limitadas apenas a fase de exportação e são simples natureza
* BCP não foi adaptado para ser robusto ao carregar os dados através da internet. Qualquer instabilidade de rede pode causar um erro de carregamento.
* o esquema estar presente na base de dados de destino antes da carga depende do BCP

Para obter mais informações, consulte [utilizar o bcp para carregar dados para o SQL Data Warehouse][Use bcp to load data into SQL Data Warehouse].

## <a name="optimizing-data-migration"></a>Otimizar a migração de dados
Um processo de migração de dados SQLDW pode ser dividido efetivamente em três passos discretos:

1. Exportação de dados de origem
2. Transferência de dados para o Azure
3. Carregar para o destino SQLDW da base de dados

Cada passo pode ser otimizado individualmente para criar um processo de migração novamente iniciável, resilientes e robusto que maximiza o desempenho em cada passo.

## <a name="optimizing-data-load"></a>Otimizar o carregamento de dados
Observar estas na ordem inversa para um momento; é a forma mais rápida para carregar dados através do PolyBase. Otimizar um processo de carregamento do PolyBase coloca pré-requisitos os passos anteriores para que seja melhor compreender isto compromisso. São:

1. Codificação dos ficheiros de dados
2. Formato de ficheiros de dados
3. Localização dos ficheiros de dados

### <a name="encoding"></a>Encoding
O PolyBase requer ficheiros de dados ser UTF-8 ou UTF-16FE. 



### <a name="format-of-data-files"></a>Formato de ficheiros de dados
O PolyBase exige de um terminador de linha fixo de tabulação ou \n. Os ficheiros de dados tem de estar em conformidade com este padrão. Não sabemos de quaisquer restrições terminadores de cadeia ou coluna.

Terá de definir cada coluna no ficheiro como parte da sua tabela externa em PolyBase. Certifique-se de que todas as colunas exportadas são necessárias e que os tipos de estar em conformidade com as normas necessárias.

Consulte novamente a [migrar o esquema] artigo para detalhes sobre os tipos de dados suportada.

### <a name="location-of-data-files"></a>Localização dos ficheiros de dados
Armazém de dados do SQL Server utiliza o PolyBase para carregar dados do Blob Storage do Azure exclusivamente. Por conseguinte, os dados tem de ter foi primeiro transferidos para o armazenamento de Blobs.

## <a name="optimizing-data-transfer"></a>Otimizar a transferência de dados
Uma das partes mais lentas da migração de dados é a transferência dos dados para o Azure. Não só pode ser de largura de banda de rede um problema, mas também fiabilidade de rede é importante que pode hamper progresso. Por predefinição, a migração de dados para o Azure é através da internet para que as possibilidades de erros de transferência ocorrer estejam predispostas razoável. No entanto, estes erros podem exigir dados novamente sejam enviados todo ou em parte.

Felizmente tem várias opções para melhorar a velocidade e resiliência deste processo:

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
Poderá pretender considerar a utilização de [ExpressRoute] [ ExpressRoute] para acelerar a transferência. [ExpressRoute] [ ExpressRoute] fornece-lhe uma ligação privada estabelecida para o Azure para a ligação não passa através da internet pública. Isto não é de um passo obrigatório. No entanto, este irá melhorar o débito quando enviar por push dados para o Azure no local ou localização conjunta.

As vantagens de utilizar [ExpressRoute] [ ExpressRoute] são:

1. Uma maior fiabilidade
2. Velocidade de rede mais rápida
3. Menor latência de rede
4. uma maior segurança de rede

[ExpressRoute] [ ExpressRoute] é vantajoso para um número de cenários; não apenas a migração.

Interessado? Para obter mais informações e preços, visite o [documentação do ExpressRoute][ExpressRoute documentation].

### <a name="azure-import-and-export-service"></a>Serviço de exportação e importação do Azure
A importação do Azure e o serviço de exportação é um processo de transferência de dados foi concebido para grandes (GB + +) para em grande escala (TB + Resp +) as transferências de dados no Azure. Envolve escrever os dados em discos e envio-los para um centro de dados do Azure. O conteúdo do disco, em seguida, será carregado para o Blobs Storage do Azure em seu nome.

Uma vista detalhada do processo de exportação de importação é o seguinte:

1. Configurar um contentor de Blob Storage do Azure para receber os dados
2. Exportar os dados para o armazenamento local
3. Copiar os dados polegada 3.5 SATA II/III rígido em unidades de disco com a [ferramenta de importação/exportação do Azure]
4. Criar uma tarefa de importação utilizando a importação do Azure e o serviço de exportar a fornecer os ficheiros do diário de alterações produzidos pela [ferramenta de importação/exportação do Azure]
5. São enviados os discos seu centro de dados do Azure indicado
6. Os dados são transferidos para o contentor do Blob Storage do Azure
7. Carregar os dados para SQLDW utilizando o PolyBase

### <a name="azcopyazcopy-utility"></a>[AZCopy][AZCopy] utilitário
O [AZCopy][AZCopy] utilitário é uma ótima ferramenta para obter os dados transferidos para o Blobs Storage do Azure. Concebida para pequenas (MB + Resp +) para muito grandes (GB + Resp +) as transferências de dados. [AZCopy] tem também foi concebido para fornecer a bom resiliente débito ao transferir dados para o Azure e por isso é uma escolha ideal para o passo de transferência de dados. Uma vez transferidos pode carregar os dados através do PolyBase no SQL Data Warehouse. Também pode incorporar AZCopy seus pacotes SSIS utilizando uma tarefa "A executar o processo".

Para utilizar o AZCopy primeiro terá de transferir e instalá-lo. Não existe um [versão de produção] [ production version] e um [versão de pré-visualização] [ preview version] disponíveis.

Para carregar um ficheiro do sistema de ficheiros, terá de um comando como o abaixo:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Pode ser um resumo do processo de alto nível:

1. Configurar um contentor do blob storage do Azure para receber os dados
2. Exportar os dados para o armazenamento local
3. AZCopy os dados no contentor do Blob Storage do Azure
4. Carregar os dados para o SQL Data Warehouse, utilizando o PolyBase

Total disponível de documentação: [AZCopy][AZCopy].

## <a name="optimizing-data-export"></a>Otimizar a exportação de dados
Além de garantir que a exportação está em conformidade com os requisitos apresentados pelo PolyBase podem também de procura otimizar a exportação dos dados para melhorar ainda mais o processo.



### <a name="data-compression"></a>Compressão de dados
O PolyBase pode ler os dados de gzip comprimido. Se conseguir comprimir os dados aos ficheiros de gzip será minimiza a quantidade de dados que está a ser enviados através da rede.

### <a name="multiple-files"></a>Vários ficheiros
Dividi tabelas grandes em vários ficheiros não só ajuda a melhorar a velocidade de exportação, também ajuda a com re-startability de transferência e a capacidade de gestão geral dos dados uma vez no blob storage do Azure. Uma das muitas funcionalidades nice do PolyBase é irá ler todos os ficheiros para uma pasta e processá-la como uma tabela. Consequentemente, é boa ideia isolar os ficheiros para cada tabela para a sua própria pasta.

O PolyBase também suporta uma funcionalidade conhecida como "transversal da pasta de recursiva". Pode utilizar esta funcionalidade para melhorar ainda mais a organização dos seus dados exportados para melhorar a gestão de dados.

Para obter mais informações sobre como carregar dados com o PolyBase, consulte [PolyBase de utilização para carregar dados para o SQL Data Warehouse][Use PolyBase to load data into SQL Data Warehouse].

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a migração, consulte [migrar a sua solução ao SQL Data Warehouse][Migrate your solution to SQL Data Warehouse].
Para mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento][development overview].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/v1/data-factory-data-movement-activities.md 
[ADF samples]: ../data-factory/v1/data-factory-samples.md
[ADF Copy examples]: ../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md
[development overview]: sql-data-warehouse-overview-develop.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: sql-data-warehouse-load-with-bcp.md
[Use PolyBase to load data into SQL Data Warehouse]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: http://azure.microsoft.com/documentation/services/expressroute/

[production version]: http://aka.ms/downloadazcopy/
[preview version]: http://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx
