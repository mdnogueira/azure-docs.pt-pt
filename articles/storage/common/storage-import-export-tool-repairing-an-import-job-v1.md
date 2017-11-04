---
title: "Reparação de uma tarefa de importação do Azure para importar/exportar - v1 | Microsoft Docs"
description: "Saiba como reparar uma tarefa de importação que foi criada e executada com o serviço importar/exportar do Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 38cc16bd-ad55-4625-9a85-e1726c35fd1b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: c837713fd9e7d03287ae5a3644fd6bb47714c9d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="repairing-an-import-job"></a>Reparação de uma tarefa de importação
O serviço de importação/exportação do Microsoft Azure poderá não conseguir copiar alguns dos seus ficheiros ou partes de um ficheiro para o serviço Blob do Windows Azure. Algumas razões para falhas incluem:  
  
-   Ficheiros danificados  
  
-   Unidades danificadas  
  
-   A chave de conta de armazenamento alterada enquanto o ficheiro estava a ser transferido.  
  
Pode executar a ferramenta de importação/exportação do Microsoft Azure com a importação de ficheiros de registo de cópia da tarefa e a ferramenta de carregamentos de ficheiros em falta (ou partes de um ficheiro) à sua conta de armazenamento do Windows Azure para concluir a tarefa de importação.  
  
## <a name="repairimport-parameters"></a>Parâmetros de RepairImport

Os parâmetros seguintes podem ser especificados com **RepairImport**: 
  
|||  
|-|-|  
|**/r:**< RepairFile\>|**Necessário.** Caminho para o ficheiro de reparação, que controla o progresso da reparação e permite-lhe retomar uma reparação de interrompida. Cada unidade tem de ter um e apenas um ficheiro de reparação. Quando iniciar uma reparação de uma determinada unidade, passa o caminho para um ficheiro de reparação, que ainda não existe. Para retomar uma reparação de interrompida, deverá passar nome de um ficheiro de reparação existente. O ficheiro de reparação correspondente para a unidade de destino tem sempre de ser especificado.|  
|**/LOGDIR:**< LogDirectory\>|**Opcional.** O diretório de registo. Ficheiros de registo verboso são escritos para este diretório. Não se for especificado nenhum diretório de registo, o atual diretório é utilizado como o diretório de registo.|  
|**/d:**< TargetDirectories\>|**Necessário.** Um ou mais valores separados por ponto e vírgula diretórios que contêm os ficheiros originais que foram importados. A unidade de importação também pode ser utilizada, mas não é necessária se alternativo localizações dos ficheiros originais não estiverem disponíveis.|  
|**/BK:**< BitLockerKey\>|**Opcional.** Deve especificar a chave do BitLocker, se pretender que a ferramenta para desbloquear uma unidade encriptada onde os ficheiros originais estão disponíveis.|  
|**/SN:**< StorageAccountName\>|**Necessário.** O nome da conta de armazenamento para a tarefa de importação.|  
|**/SK:**< StorageAccountKey\>|**Necessário** se e apenas se não for especificado um contentor SAS. A chave de conta para a conta de armazenamento para a tarefa de importação.|  
|**/csas:**< ContainerSas\>|**Necessário** se e apenas se a chave de conta de armazenamento não está especificada. O contentor de SAS para aceder aos blobs associadas com a tarefa de importação.|  
|**/ CopyLogFile:**< DriveCopyLogFile\>|**Necessário.** Caminho para o ficheiro de registo de cópia de unidade (o verboso registo ou erro de registo). O ficheiro é gerado pelo serviço de importação/exportação do Windows Azure e pode ser transferido a partir do blob storage do associado ao trabalho. O ficheiro de registo de cópia contém informações sobre os blobs com falhas ou ficheiros, que estão a ser reparado.|  
|**/ PathMapFile:**< DrivePathMapFile\>|**Opcional.** Caminho para um ficheiro de texto que pode ser utilizado para resolver ambiguities se tiver vários ficheiros com o mesmo nome que foram importar na mesma tarefa. Na primeira vez que a ferramenta é executada, pode preencher este ficheiro com todos os nomes ambíguos. As execuções subsequentes da ferramenta de utilizam este ficheiro para resolver os ambiguities.|  
  
## <a name="using-the-repairimport-command"></a>Utilizando o comando RepairImport  
Para reparar a importar dados por transmissão em fluxo de dados através da rede, tem de especificar os diretórios que contêm os ficheiros originais foram importar utilizando o `/d` parâmetro. Também tem de especificar o ficheiro de registo de cópia que transferiu a partir da sua conta de armazenamento. Uma linha de comandos típica para reparar uma tarefa de importação com falhas parciais tem o seguinte:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
No exemplo a seguir um cópia do ficheiro de registo, uma peça de 64K de um ficheiro foi danificada na unidade que foi enviada para a tarefa de importação. Uma vez que esta é a única falha indicada, o resto dos blobs na tarefa foram importadas com êxito.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Quando este registo de cópia é passado para a ferramenta de importação/exportação do Azure, a ferramenta tenta concluir a importação para este ficheiro ao copiar o conteúdo em falta através da rede. Os seguintes exemplo acima, a ferramenta de procura o ficheiro original `\animals\koala.jpg` dentro os dois diretórios `C:\Users\bob\Pictures` e `X:\BobBackup\photos`. Se o ficheiro `C:\Users\bob\Pictures\animals\koala.jpg` existir, a ferramenta de importação/exportação do Azure copia o intervalo de dados em falta para o blob correspondente `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>A resolução de conflitos quando utilizar RepairImport  
Em algumas situações, a ferramenta poderá não conseguir encontrar ou abrir o ficheiro necessário para uma das seguintes razões: não foi possível encontrar o ficheiro, o ficheiro não está acessível, o nome de ficheiro é ambíguo ou o conteúdo do ficheiro já não está correto.  
  
Um erro ambíguo pode ocorrer se a ferramenta está a tentar localizar `\animals\koala.jpg` e não existe um ficheiro com esse nome sob ambos `C:\Users\bob\pictures` e `X:\BobBackup\photos`. Ou seja, ambos `C:\Users\bob\pictures\animals\koala.jpg` e `X:\BobBackup\photos\animals\koala.jpg` existe nas unidades de tarefa de importação.  
  
O `/PathMapFile` opção permite-lhe resolva estes erros. Pode especificar o nome do ficheiro, que contém a lista de ficheiros que a ferramenta não conseguiu identificar corretamente. O seguinte exemplo de linha de comandos preenche `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
A ferramenta irá, em seguida, escrever os caminhos de ficheiro problemático para `9WM35C2V_pathmap.txt`, um em cada linha. Por exemplo, o ficheiro pode conter as seguintes entradas depois de executar o comando:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Para cada ficheiro na lista, deve tentar localizar e abrir o ficheiro para garantir que está disponível para a ferramenta. Se pretender saber a ferramenta explicitamente onde encontrar um ficheiro, pode modificar o ficheiro de mapa de caminho e adicione o caminho para cada ficheiro na mesma linha, separado por um caráter de separador:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Depois de disponibilizar os ficheiros necessários para a ferramenta, ou atualizar o ficheiro de mapa de caminho, pode voltar a executar a ferramenta para concluir o processo de importação.  
  
## <a name="next-steps"></a>Passos seguintes
 
* [Configurar a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup-v1.md)   
* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
