---
title: 'Início Rápido: Ler dados capturados do aplicativo Python – Hubs de Eventos do Azure'
description: 'Início Rápido: Scripts que usam o SDK do Python do Azure para demonstrar o recurso de Captura dos Hubs de Eventos.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18, tracking-python
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: c726b0d11759d30730046e635c701cf23d130dfc
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561558"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>Início Rápido: Passo a passo da Captura de Hubs de Eventos: Python (azure-eventhub versão 1)

A Captura é um recurso dos Hubs de Eventos do Azure. É possível usar a Captura para fornecer automaticamente os dados de streaming em seu hub de eventos para uma conta de armazenamento de Blobs do Azure de sua escolha. Essa funcionalidade facilita o processamento de lotes em dados de streaming em tempo real. Este artigo descreve como usar a Captura de Hubs de Eventos com o Python. Para obter mais informações sobre a Captura dos Hubs de Eventos, consulte [Captura de eventos por meio dos Hubs de Eventos do Azure][Overview of Event Hubs Capture].

Este tutorial usa o [SDK do Python do Azure](https://azure.microsoft.com/develop/python/) para demonstrar o recurso de Captura. O programa *sender.py* envia telemetria de ambiente simulado para os Hubs de Eventos no formato JSON. O hub de eventos usa o recurso de Captura para gravar esses dados no armazenamento de Blobs em lotes. O aplicativo *capturereader.py* lê esses blobs, cria um arquivo de acréscimo para cada um dos dispositivos e grava os dados em arquivos *.csv* em cada dispositivo.

> [!WARNING]
> Este início rápido se destina à versão 1 do SDK do Python dos Hubs de Eventos do Azure. É recomendável que você [migre](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) seu código para a [versão 5 do SDK do Python](get-started-capture-python-v2.md).

Neste passo a passo, você vai: 

> [!div class="checklist"]
> * Criar uma conta de armazenamento de Blobs do Azure e um contêiner no portal do Azure.
> * Habilitar a Captura de Hubs de Eventos e direcioná-los para sua conta de armazenamento.
> * Enviar dados para o hub de eventos usando um script Python.
> * Ler e processar arquivos da Captura dos Hubs de Eventos usando outro script Python.

## <a name="prerequisites"></a>Pré-requisitos

- Python 3.4 ou posterior, com `pip` instalado e atualizado.
  
- Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
  
- Um namespace de Hubs de Eventos ativo e um hub de eventos, criados seguindo as instruções no [Início Rápido: Criar um hub de eventos usando o portal do Azure](event-hubs-create.md). Anote o namespace e os nomes do hub de eventos para usar posteriormente neste tutorial. 
  
  > [!NOTE]
  > Caso já tenha um contêiner de armazenamento para usar, você poderá habilitar a Captura e selecionar o contêiner de armazenamento ao criar o Hub de Eventos. 
  > 
  
- O nome da chave de acesso compartilhado dos Hubs de Eventos e o valor da chave primária. Localize ou crie esses valores nas **Políticas de acesso compartilhado** em sua página de Hubs de Eventos. O nome da chave de acesso padrão é **RootManageSharedAccessKey**. Copie o nome da chave de acesso e o valor da chave primária para usar posteriormente neste tutorial. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Criar uma conta de armazenamento de Blobs do Azure e um contêiner

Crie uma conta de armazenamento e um contêiner para usar na captura. 

1. Entre no [portal do Azure][Azure portal].
2. No painel de navegação esquerdo, selecione **Contas de armazenamento** e na tela **Contas de armazenamento** selecione **Adicionar**.
3. Na tela de criação da conta de armazenamento, selecione uma assinatura e um grupo de recursos e nomeie a conta de armazenamento. É possível manter as outras seleções no padrão. Selecione **Examinar + criar**, examine as configurações e, em seguida, selecione **Criar**. 
   
   ![Criar Conta de Armazenamento][1]
   
4. Quando a implantação for concluída, selecione **Ir para o recurso** e, na tela **Visão geral** da conta de armazenamento, selecione **Contêineres**.
5. Na tela **Contêineres**, selecione **Adicionar contêiner**. 
6. Na tela **Novo contêiner**, nomeie o contêiner e, em seguida, selecione **OK**. Anote o nome do contêiner a ser usado posteriormente no tutorial. 
7. No painel de navegação à esquerda da tela **Contêineres**, selecione **Chaves de acesso**. Copie o **Nome da conta de armazenamento** e o valor da **Chave** em **key1** para usar posteriormente no tutorial.
 
## <a name="enable-event-hubs-capture"></a>Habilitar a Captura de Hubs de Eventos

1. No portal do Azure, navegue até o hub de eventos selecionando o namespace dos Hubs de Eventos em **Todos os recursos**, selecione os **Hubs de eventos** no painel de navegação esquerdo e, em seguida, selecione o hub de eventos. 
2. Na tela **Visão geral** do hub de eventos, selecione **Capturar eventos**.
3. Na tela **Capturar**, selecione **Ativar**. Em seguida, em **Contêiner de Armazenamento do Azure**, escolha **Selecionar Contêiner**. 
4. Na tela **Contêineres**, selecione o contêiner de armazenamento que deseja usar e, em seguida, escolha **Selecionar**. 
5. Na tela **Capturar**, selecione **Salvar alterações**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Criar um script Python para enviar os eventos ao Hub de Eventos
Esse script envia 200 eventos para seu hub de eventos. Os eventos são leituras ambientais simples enviadas em JSON.

1. Abra seu editor favorito do Python, como o [Visual Studio Code][Visual Studio Code].
2. Crie um novo arquivo chamado *sender.py*. 
3. Cole o seguinte código em *sender.py*. Substitua os próprios valores para os Hubs de Eventos \<namespace>, \<AccessKeyName>, \<primary key value> e \<eventhub>.
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. Salve o arquivo.

## <a name="create-a-python-script-to-read-capture-files"></a>Criar um script Python para ler arquivos de Captura

Esse script lê os arquivos capturados e cria um arquivo para cada um de seus dispositivos para gravar os dados somente para esse dispositivo.

1. No editor do Python, crie um novo arquivo chamado *capturereader.py*. 
2. Cole o seguinte código em *capturereader.py*. Substitua os valores salvos para seu \<storageaccount>, \<storage account access key> e \<storagecontainer>.
   
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
           if not parsed_json['id'] in dict:
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
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>Executar o script do Python

1. Abra um prompt de comando que tenha o Python em seu caminho e execute estes comandos para instalar os pacotes de pré-requisito do Python:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Caso tenha uma versão anterior do `azure-storage` ou do `azure`, talvez seja necessário usar a opção `--upgrade`.
   
   Talvez você também precise executar o comando a seguir. A execução desse comando não é necessária na maioria dos sistemas. 
   
   ```cmd
   pip install cryptography
   ```
   
2. No diretório em que salvou *sender.py* e *capturereader.py*, execute este comando:
   
   ```cmd
   start python sender.py
   ```
   
   O comando inicia um novo processo do Python para executar o remetente.
   
3. Quando a captura terminar de ser executada, execute este comando:
   
   ```cmd
   python capturereader.py
   ```

   O processador de captura baixa todos os blobs não vazios do contêiner da conta de armazenamento e grava os resultados como arquivos *.csv* no diretório local. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os Hubs de Eventos, consulte: 

* [Visão Geral da Captura dos Hubs de Eventos][Overview of Event Hubs Capture]
* [Aplicativos de exemplo que usam Hub de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Visão geral de Hubs de Eventos][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
