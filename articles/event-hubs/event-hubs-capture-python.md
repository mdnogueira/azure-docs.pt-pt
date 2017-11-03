---
title: "Instruções de captura do Event Hubs do Azure | Microsoft Docs"
description: Exemplo que utiliza o SDK do Python para demonstrar a utilizar a funcionalidade de captura de Hubs de eventos.
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 5fb691ec53fed20e5df4f581da10b964c07e09b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-capture-walkthrough-python"></a>Event Hubs capturar explicação passo a passo: Python

Captura de Hubs de eventos é uma funcionalidade dos Event Hubs que lhe permite fornecer automaticamente os dados de transmissão em fluxo no seu hub de eventos para uma conta de armazenamento de Blobs do Azure à sua escolha. Esta capacidade torna mais fácil efetuar o processamento nos dados de transmissão em fluxo em tempo real em lote. Este artigo descreve como utilizar os Hubs de eventos captura com o Python. Para obter mais informações sobre como capturar os Hubs de eventos, consulte o [artigo de descrição geral](event-hubs-capture-overview.md).

Este exemplo utiliza o [SDK Python do Azure](https://azure.microsoft.com/develop/python/) para demonstrar a funcionalidade de captura. O programa de sender.py envia telemetria ambiental simulada para os Event Hubs no formato JSON. O hub de eventos está configurado para utilizar a funcionalidade de captura para escrever dados para o blob storage em lotes. A aplicação de capturereader.py, em seguida, lê estes blobs e cria um ficheiro de acréscimo por dispositivo, em seguida, escreve os dados em ficheiros. csv.

## <a name="what-will-be-accomplished"></a>O que será efetuado

1. Crie uma conta do Blob Storage do Azure e um contentor do blob dentro da mesma, utilizando o portal do Azure.
2. Crie um espaço de nomes do Hub de eventos, utilizando o portal do Azure.
3. Crie um hub de eventos com a funcionalidade de captura ativada, no portal do Azure.
4. Envie dados para o hub de eventos com um script de Python.
5. Ler os ficheiros da captura e processá-los com outro script de Python.

## <a name="prerequisites"></a>Pré-requisitos

- Python 2.7.x
- Uma subscrição do Azure.
- Um Active Directory [hub de espaço de nomes e eventos de Event Hubs.](event-hubs-create.md)

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure
1. Inicie sessão no [Portal do Azure][Azure portal].
2. No painel de navegação esquerdo do portal, clique em **novo**, em seguida, clique em **armazenamento**e, em seguida, clique em **conta de armazenamento**.
3. Preencha os campos no painel de conta de armazenamento e, em seguida, clique em **criar**.
   
   ![][1]
4. Depois de confirmar a **implementações com êxito** da mensagem, clique no nome da nova conta de armazenamento e no **Essentials** painel, clique em **Blobs**. Quando o **serviço Blob** abre painel, clique em **+ contentor** na parte superior. Nome do contentor **capturar**, em seguida, feche o **serviço Blob** painel.
5. Clique em **chaves de acesso** no painel esquerdo e copie o nome da conta de armazenamento e o valor da **chave1**. Guarde estes valores para o bloco de notas ou alguns outra localização temporária.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Criar um script do Python para enviar eventos para o hub de eventos
1. Abra o seu editor favorito de Python, tais como [Visual Studio Code][Visual Studio Code].
2. Criar um script designado **sender.py**. Este script envia 200 eventos para o hub de eventos. São simples ambientais as leituras dos enviadas em JSON.
3. Cole o seguinte código sender.py:
   
  ```python
  import uuid
  import datetime
  import random
  import json
  from azure.servicebus import ServiceBusService
   
  sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
  devices = []
  for x in range(0, 10):
      devices.append(str(uuid.uuid4()))
   
  for y in range(0,20):
      for dev in devices:
          reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
          s = json.dumps(reading)
          sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
      print y
  ```
4. Atualize o código anterior para utilizar o seu nome de espaço de nomes, o valor da chave e o nome do hub de eventos que obteve quando criou o espaço de nomes de Event Hubs.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Criar um script do Python para ler os ficheiros de captura

1. Preencha o painel e clique em **criar**.
2. Criar um script designado **capturereader.py**. Este script lê os ficheiros capturados e cria um ficheiro por dispositivo para escrever os dados apenas para esse dispositivo.
3. Cole o seguinte código capturereader.py:
   
  ```python
  import os
  import string
  import json
  import avro.schema
  from avro.datafile import DataFileReader, DataFileWriter
  from avro.io import DatumReader, DatumWriter
  from azure.storage.blob import BlockBlobService
   
  def processBlob(filename):
      reader = DataFileReader(open(filename, 'rb'), DatumReader())
      dict = {}
      for reading in reader:
          parsed_json = json.loads(reading["Body"])
          if not 'id' in parsed_json:
              return
          if not dict.has_key(parsed_json['id']):
              list = []
              dict[parsed_json['id']] = list
          else:
              list = dict[parsed_json['id']]
              list.append(parsed_json)
      reader.close()
      for device in dict.keys():
          deviceFile = open(device + '.csv', "a")
          for r in dict[device]:
              deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
  def startProcessing(accountName, key, container):
      print 'Processor started using path: ' + os.getcwd()
      block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
      generator = block_blob_service.list_blobs(container)
      for blob in generator:
          if blob.properties.content_length != 0:
              print('Downloaded a non empty blob: ' + blob.name)
              cleanName = string.replace(blob.name, '/', '_')
              block_blob_service.get_blob_to_path(container, blob.name, cleanName)
              processBlob(cleanName)
              os.remove(cleanName)
          block_blob_service.delete_blob(container, blob.name)
  startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
  ```
4. Certifique-se ao colar os valores adequados para o nome da conta de armazenamento e a chave na chamada para `startProcessing`.

## <a name="run-the-scripts"></a>Executar os scripts
1. Abra uma linha de comandos que tenha o Python no respetivo caminho e, em seguida, execute estes comandos para instalar pacotes de Python pré-requisitos:
   
  ```
  pip install azure-storage
  pip install azure-servicebus
  pip install avro
  ```
   
  Se tiver uma versão anterior do storage do azure ou do azure, poderá ter de utilizar o **– atualizar** opção
   
  Também poderá ter de executar o seguinte (não é necessário na maioria dos sistemas):
   
  ```
  pip install cryptography
  ```
2. Altere o diretório para onde quer que guardou sender.py e capturereader.py e execute este comando:
   
  ```
  start python sender.py
  ```
   
  Este comando inicia um novo processo de Python para executar o remetente.
3. Agora, aguarde alguns minutos para que a captura ser executada. Em seguida, escreva o seguinte comando numa janela de comando do original:
   
   ```
   python capturereader.py
   ```

   Este processador de captura utiliza o diretório local para transferir todos os blobs no contentor/conta de armazenamento. Processa os que não estão vazias e escreve os resultados como ficheiros. csv para o diretório local.

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Captura de descrição geral dos Event Hubs][Overview of Event Hubs Capture]
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Descrição geral dos Hubs de Eventos][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
