---
title: "Reparação de uma tarefa de exportação de importação/exportação do Azure - v1 | Microsoft Docs"
description: "Saiba como reparar uma tarefa de exportação que foi criada e executada com o serviço importar/exportar do Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 728e2a42-04ce-4be8-9375-e9e2bc6827a5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 57ab58fa1fd8371d0b6f019f94bb162bcc1e0e43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="repairing-an-export-job"></a>Reparação de uma tarefa de exportação
Depois de uma tarefa de exportação foi concluída, pode executar a ferramenta de importação/exportação do Microsoft Azure no local para:  
  
1.  Transferir todos os ficheiros que não foi possível exportar o serviço importar/exportar do Azure.  
  
2.  Confirme que os ficheiros na unidade corretamente foram exportados.  
  
Tem de ter conetividade para o armazenamento do Azure para utilizar esta funcionalidade.  
  
O comando de reparação de uma tarefa de importação é **RepairExport**.

## <a name="repairexport-parameters"></a>Parâmetros de RepairExport

Os parâmetros seguintes podem ser especificados com **RepairExport**:  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|**/r: < RepairFile\>**|Necessário. Caminho para o ficheiro de reparação, que controla o progresso da reparação e permite-lhe retomar uma reparação de interrompida. Cada unidade tem de ter um e apenas um ficheiro de reparação. Quando iniciar uma reparação de uma determinada unidade, irá passar no caminho para um ficheiro de reparação que ainda não existe. Para retomar uma reparação de interrompida, deverá passar nome de um ficheiro de reparação existente. O ficheiro de reparação correspondente para a unidade de destino tem sempre de ser especificado.|  
|**/LOGDIR: < LogDirectory\>**|Opcional. O diretório de registo. Ficheiros de registo verboso serão escritos neste diretório. Se nenhum diretório de registo for especificado, será utilizado como o diretório de registo do diretório atual.|  
|**/d: < TargetDirectory\>**|Necessário. O diretório para validar e reparar. Isto é normalmente o diretório de raiz da unidade de exportação, mas pode também ser uma partilha de ficheiros de rede que contém uma cópia dos ficheiros exportados.|  
|**/BK: < BitLockerKey\>**|Opcional. Deve especificar a chave do BitLocker, se pretender que a ferramenta para desbloquear um encriptados onde estão armazenados os ficheiros exportados.|  
|**/SN: < StorageAccountName\>**|Necessário. O nome da conta de armazenamento para a tarefa de exportação.|  
|**/SK: < StorageAccountKey\>**|**Necessário** se e apenas se não for especificado um contentor SAS. A chave de conta para a conta de armazenamento para a tarefa de exportação.|  
|**/csas: < ContainerSas\>**|**Necessário** se e apenas se a chave de conta de armazenamento não está especificada. O contentor de SAS para aceder aos blobs associadas com a tarefa de exportação.|  
|**/ CopyLogFile: < DriveCopyLogFile\>**|Necessário. O caminho para o ficheiro de registo de cópia de unidade. O ficheiro é gerado pelo serviço de importação/exportação do Windows Azure e pode ser transferido a partir do blob storage do associado ao trabalho. O ficheiro de registo de cópia contém informações sobre os blobs com falhas ou ficheiros que estão a ser reparado.|  
|**/ ManifestFile: < DriveManifestFile\>**|Opcional. O caminho para o ficheiro de manifesto a unidade de exportação. Este ficheiro é gerado pelo serviço de importação/exportação do Windows Azure e armazenado na unidade de exportação e, opcionalmente num blob na conta de armazenamento associado ao trabalho.<br /><br /> O conteúdo dos ficheiros na unidade de exportação será verificado com os hashes de MD5 contidos neste ficheiro. Todos os ficheiros que são determinados estar danificado serão transferidos e rescritos para os diretórios de destino.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Utilizar o modo de RepairExport para corrigir exportações falhadas  
Pode utilizar a ferramenta de importação/exportação do Azure para transferir os ficheiros que não foi possível exportar. O ficheiro de registo de cópia irá conter uma lista de ficheiros que falhou a exportar.  
  
As causas de falhas de exportação incluem as possibilidades seguintes:  
  
-   Unidades danificadas  
  
-   A chave de conta de armazenamento foi alterada durante o processo de transferência  
  
Para executar a ferramenta **RepairExport** modo, primeiro tem de ligar a unidade que contém os ficheiros exportados para o seu computador. Em seguida, execute a ferramenta de importação/exportação do Azure, especificando o caminho para essa unidade com o `/d` parâmetro. Também tem de especificar o caminho do ficheiro de registo de cópia de unidade que transferiu. O seguinte exemplo de linha de comandos abaixo executa a ferramenta para reparar todos os ficheiros que não foi possível exportar:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Segue-se um exemplo de um cópia do ficheiro de registo que mostra um bloco no blob Falha ao exportar:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
O ficheiro de registo de cópia indica que ocorreu uma falha enquanto o serviço de importação/exportação do Windows Azure foi estiverem a transferir um dos blocos do blob para o ficheiro na unidade de exportação. Os outros componentes do ficheiro transferido com êxito e o comprimento do ficheiro foi corretamente definido. Neste caso, a ferramenta irá abrir o ficheiro na unidade, transfira o bloco da conta do storage e escrevem-para o intervalo de ficheiro a partir de deslocamento 65536 com comprimento 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Utilizar RepairExport para validar o conteúdo de unidade  
Também pode utilizar o Azure para importar/exportar com o **RepairExport** opção para validar o conteúdo na unidade estão corretos. O ficheiro de manifesto em cada unidade de exportação contém MD5s para o conteúdo da unidade.  
  
O serviço importar/exportar do Azure pode também guardar os ficheiros de manifesto para uma conta de armazenamento durante o processo de exportação. A localização dos ficheiros de manifesto está disponível através de [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operação quando a tarefa foi concluída. Consulte [serviço importar/exportar formato de ficheiro de manifesto](storage-import-export-file-format-metadata-and-properties.md) para obter mais informações sobre o formato de um ficheiro de manifesto de unidade.  
  
O exemplo seguinte mostra como executar a ferramenta de importação/exportação do Azure com o **/ManifestFile** e **/CopyLogFile** parâmetros:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Segue-se um exemplo de um ficheiro de manifesto:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
Depois de concluir o processo de reparação, a ferramenta irá ler através de cada ficheiro referenciado no ficheiro de manifesto e verificar a integridade do ficheiro com os hashes de MD5. Para o manifesto acima, irá percorrer os seguintes componentes.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Qualquer componente falhar a verificação será transferiu a ferramenta e rescrita para o mesmo ficheiro na unidade.  
  
## <a name="next-steps"></a>Passos seguintes
 
* [Configurar a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup-v1.md)   
* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de uma tarefa de importação](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
