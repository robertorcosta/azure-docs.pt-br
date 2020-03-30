---
title: Alterar o feed no Azure Blob Storage (Preview) | Microsoft Docs
description: Saiba mais sobre os logs de feed de alterações no Azure Blob Storage e como usá-los.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: ac111b06d578a0e9af8581ef2e8caeccfc4a291e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536880"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Alterar o suporte ao feed no Azure Blob Storage (Preview)

O objetivo do feed de alterações é fornecer registros de transações de todas as alterações que ocorrem nas bolhas e nos metadados blob em sua conta de armazenamento. O feed de alterações fornece registro **ordenado,** **garantido,** **durável,** **imutável,** **somente leitura** dessas alterações. Os aplicativos clientes podem ler esses logs a qualquer momento, seja no streaming ou no modo em lote. O feed de alterações permite que você crie soluções eficientes e escaláveis que processam eventos de alteração que ocorrem em sua conta blob Storage a um baixo custo.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

O feed de alterações é armazenado como [blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) em um recipiente especial em sua conta de armazenamento a um custo padrão [de preço blob.](https://azure.microsoft.com/pricing/details/storage/blobs/) Você pode controlar o período de retenção desses arquivos com base em seus requisitos (Veja as [condições](#conditions) da versão atual). Os eventos de alteração são anexados ao feed de alterações como registros na especificação do formato [Apache Avro:](https://avro.apache.org/docs/1.8.2/spec.html) um formato compacto, rápido e binário que fornece estruturas de dados ricas com esquema inline. Esse formato é amplamente usado no ecossistema do Hadoop, pelo Stream Analytics e pelo Azure Data Factory.

Você pode processar esses registros de forma assíncrona, incremental ou completa. Qualquer número de aplicativos clientes pode ler independentemente o feed de alterações, em paralelo e em seu próprio ritmo. Aplicativos de análise como [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) ou [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) podem consumir logs diretamente como arquivos Avro, que permitem processá-los a um custo baixo, com alta largura de banda, e sem ter que escrever um aplicativo personalizado.

O suporte ao feed de alterações é adequado para cenários que processam dados com base em objetos que mudaram. Por exemplo, os aplicativos podem:

  - Atualize um índice secundário, sincronize com um cache, mecanismo de pesquisa ou qualquer outro cenário de gerenciamento de conteúdo.
  
  - Extrair insights e métricas de análise de negócios, com base em alterações que ocorrem em seus objetos, seja de forma streaming ou em modo de loteamento.
  
  - Armazene, audite e analise alterações em seus objetos, durante qualquer período de tempo, para segurança, conformidade ou inteligência para gerenciamento de dados corporativos.

  - Crie soluções para backup, espelhamento ou replicação do estado do objeto em sua conta para gerenciamento ou conformidade de desastres.

  - Crie pipelines de aplicativos conectados que reajam para alterar eventos ou agendar execuções com base em objetos criados ou alterados.

> [!NOTE]
> A alimentação de alterações fornece um modelo de log ordenado e durável das alterações que ocorrem a uma bolha. As alterações são escritas e disponibilizadas no seu registro de feed de alterações dentro de uma ordem de poucos minutos da alteração. Se o seu aplicativo tiver que reagir a eventos muito mais rápido do que isso, considere usar [eventos de armazenamento Blob](storage-blob-event-overview.md) em vez disso. [O Blob Storage Events](storage-blob-event-overview.md) fornece eventos únicos em tempo real que permitem que suas funções ou aplicativos do Azure reajam rapidamente às alterações que ocorrem em uma bolha. 

## <a name="enable-and-disable-the-change-feed"></a>Habilite e desative o feed de alteração

Você deve habilitar o feed de alterações em sua conta de armazenamento para começar a capturar e gravar alterações. Desabilite a alimentação de alteração para impedir a captura de alterações. Você pode habilitar e desativar alterações usando modelos do Azure Resource Manager no Portal ou no Powershell.

Aqui estão algumas coisas para ter em mente quando você habilitar a alimentação de mudança.

- Há apenas um feed de mudança para o serviço blob em cada conta de armazenamento e é armazenado no **$blobchangefeed** recipiente.

- As alterações Criar, Atualizar e Excluir são capturadas apenas no nível de serviço blob.

- O feed de alterações captura *todas as* alterações para todos os eventos disponíveis que ocorrem na conta. Os aplicativos clientes podem filtrar os tipos de eventos conforme necessário. (Veja as [condições](#conditions) da versão atual).

- Apenas contas de armazenamento GPv2 e Blob podem habilitar o feed de alterações. As contas premium BlockBlobStorage e as contas hierárquicas habilitadas pelo namespace não são suportadas no momento. As contas de armazenamento GPv1 não são suportadas, mas podem ser atualizadas para o GPv2 sem tempo de inatividade, consulte [Upgrade para uma conta de armazenamento GPv2](../common/storage-account-upgrade.md) para obter mais informações.

> [!IMPORTANT]
> O feed de mudança está em pré-visualização pública, e está disponível nas **regiões oeste** e **oeste2.** Veja a seção de [condições](#conditions) deste artigo. Para se inscrever na pré-visualização, consulte a seção [Registrar sua assinatura](#register) deste artigo. Você deve registrar sua assinatura antes de habilitar o feed de alterações em suas contas de armazenamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Habilite o feed de alterações em sua conta de armazenamento usando o portal Azure:

1. No [portal Azure,](https://portal.azure.com/)selecione sua conta de armazenamento. 

2. Navegue até a opção **de proteção de dados** em **Blob Service**.

3. Clique **em Ativado** no feed de alteração **blob**

4. Escolha o botão **Salvar** para confirmar suas configurações de Proteção de Dados

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Habilite a alimentação de alterações usando o PowerShell:

1. Instale o PowershellGet mais recente.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Feche e, em seguida, reabra o console Powershell.

3. Instale o módulo de visualização **Az.Storage.**

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Entre em sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela para fazer a autenticação.

   ```powershell
   Connect-AzAccount
   ```

5. Habilite o feed de alterações para sua conta de armazenamento.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Modelo](#tab/template)
Use um modelo do Azure Resource Manager para habilitar o feed de alterações na sua conta de armazenamento existente através do portal Azure:

1. No portal Azure, escolha **Criar um recurso**.

2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e pressione **ENTER**.

3. Escolha **[Implantar um modelo personalizado](https://portal.azure.com/#create/Microsoft.Template)** e, em seguida, escolha Construir seu próprio modelo no **editor**.

4. No editor modelo, cole no json a seguir. Substitua o espaço reservado `<accountName>` pelo nome da sua conta de armazenamento.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Escolha o botão **Salvar,** especifique o grupo de recursos da conta e escolha o botão **Comprar** para implantar o modelo e ativar o feed de alterações.

---

## <a name="consume-the-change-feed"></a>Consumir a ração de mudança

O feed de alterações produz vários metadados e arquivos de registro. Esses arquivos estão localizados no contêiner **$blobchangefeed** da conta de armazenamento. 

> [!NOTE]
> Na versão atual, **o** $blobchangefeed contêiner não é visível no Azure Storage Explorer ou no portal Azure. No momento, você não pode ver o $blobchangefeed contêiner quando você chamar API ListContainers, mas você é capaz de chamar a API ListBlobs diretamente no contêiner para ver as bolhas.

Seus aplicativos clientes podem consumir o feed de alterações usando a biblioteca do processador de feed de alteração de bolha que é fornecida com o SDK do processador de alimentação Change. 

Consulte Os logs de feed de alteração de [processo no Azure Blob Storage](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Entenda a organização do feed de mudanças

<a id="segment-index"></a>

### <a name="segments"></a>Segmentos

O feed de alterações é um registro de alterações que são *organizadas* em segmentos **de hora em hora,** mas anexadas e atualizadas a cada poucos minutos. Esses segmentos são criados somente quando há eventos de mudança de bolha que ocorrem nessa hora. Isso permite que seu aplicativo cliente consuma alterações que ocorrem em intervalos específicos de tempo sem ter que pesquisar por todo o registro. Para saber mais, consulte as [Especificações](#specifications).

Um segmento de hora disponível do feed de alteração é descrito em um arquivo manifesto que especifica os caminhos para os arquivos de feed de alteração para esse segmento. A listagem `$blobchangefeed/idx/segments/` do diretório virtual mostra esses segmentos ordenados pelo tempo. O caminho do segmento descreve o início do intervalo de tempo por hora que o segmento representa. Você pode usar essa lista para filtrar os segmentos de logs que lhe interessam.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> O `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` é criado automaticamente quando você habilita a alimentação de alteração. Você pode ignorar este arquivo com segurança. É um arquivo de inicialização sempre vazio. 

O arquivo manifesto`meta.json`do segmento ( ) mostra o caminho `chunkFilePaths` dos arquivos de feed de alteração para esse segmento na propriedade. Aqui está um exemplo de um arquivo manifesto de segmento.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> O `$blobchangefeed` contêiner só aparece depois de habilitar o recurso de feed de alteração em sua conta. Você terá que esperar alguns minutos depois de ativar a alimentação de alteração antes de poder listar as bolhas no recipiente. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Alterar registros de eventos

Os arquivos de feed de alteração contêm uma série de registros de eventos de alteração. Cada registro de evento de alteração corresponde a uma mudança para uma bolha individual. Os registros são serializados e gravados no arquivo usando a especificação do formato [Apache Avro.](https://avro.apache.org/docs/1.8.2/spec.html) Os registros podem ser lidos usando a especificação do formato do arquivo Avro. Existem várias bibliotecas disponíveis para processar arquivos nesse formato.

Os arquivos de feed `$blobchangefeed/log/` de alteração são armazenados no diretório virtual como [bolhas de apêndice](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). O primeiro arquivo de feed `00000` de alteração em cada `00000.avro`caminho terá no nome do arquivo (Por exemplo ). O nome de cada arquivo de log subseqüente adicionado `00001.avro`a esse caminho será incrementado por 1 (Por exemplo: ).

Aqui está um exemplo de registro de evento de alteração do arquivo de feed de alteração convertido em Json.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Para obter uma descrição de cada propriedade, consulte o esquema de [eventos azure Event Grid para Blob Storage](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties).

> [!NOTE]
> Os arquivos de feed de alteração para um segmento não aparecem imediatamente após a criação de um segmento. O tempo de atraso está dentro do intervalo normal de latência de publicação da alimentação de alteração que está dentro de alguns minutos da alteração.

<a id="specifications"></a>

## <a name="specifications"></a>Especificações

- Os registros de eventos de alteração são anexados apenas ao feed de alterações. Uma vez que esses registros são anexados, eles são imutáveis e a posição de registro é estável. Os aplicativos clientes podem manter seu próprio ponto de verificação na posição de leitura do feed de alterações.

- Os registros de eventos de alteração são anexados dentro de uma ordem de poucos minutos da alteração. Os aplicativos clientes podem optar por consumir registros à medida que são anexados para acesso ao streaming ou em massa a qualquer outro momento.

- Os registros de eventos de alteração são ordenados por ordem de modificação **por bolha**. A ordem das alterações entre blobs é indefinida no Azure Blob Storage. Todas as alterações em um segmento anterior são anteriores a quaisquer alterações nos segmentos subsequentes.

- Os registros de eventos de alteração são serializados no arquivo de log usando a especificação do formato [Apache Avro 1.8.2.](https://avro.apache.org/docs/1.8.2/spec.html)

- Alterar registros de `eventType` eventos onde `Control` o tem um valor de são registros internos do sistema e não reflete uma alteração em objetos em sua conta. Você pode ignorar esses registros com segurança.

- Os valores na `storageDiagnonstics` bolsa de propriedades são apenas para uso interno e não projetados para uso pela sua aplicação. Seus aplicativos não devem ter uma dependência contratual sobre esses dados. Você pode ignorar essas propriedades com segurança.

- O tempo representado pelo segmento é **aproximado** com limites de 15 minutos. Assim, para garantir o consumo de todos os registros dentro de um tempo especificado, consumir o segmento de horas anteriores e próximas consecutivas.

- Cada segmento pode ter `chunkFilePaths` um número diferente devido ao particionamento interno do fluxo de log para gerenciar a publicação throughput. Os arquivos de `chunkFilePath` log em cada um são garantidos para conter bolhas mutuamente exclusivas, e podem ser consumidos e processados em paralelo sem violar a ordem de modificações por bolha durante a iteração.

- Os Segmentos `Publishing` começam em status. Uma vez que a anexação dos registros para `Finalized`o segmento estiver completa, será . Registre arquivos em qualquer segmento datado `LastConsumable` após a `$blobchangefeed/meta/Segments.json` data da propriedade no arquivo, não deve ser consumido pelo seu aplicativo. Aqui está um exemplo `LastConsumable`da `$blobchangefeed/meta/Segments.json` propriedade em um arquivo:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>Registre sua assinatura (Preview)

Como o feed de alterações está apenas na visualização pública, você precisará registrar sua assinatura para usar o recurso.

### <a name="register-by-using-powershell"></a>Registre-se usando powershell

Em um console PowerShell, execute estes comandos:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Registre-se usando o Azure CLI

No Azure Cloud Shell, execute estes comandos:

```azurecli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Condições e problemas conhecidos (Visualização)

Esta seção descreve problemas e condições conhecidos na visualização pública atual do feed de alterações. 
- Para visualização, você deve primeiro [registrar sua assinatura](#register) antes de poder habilitar o feed de alterações para sua conta de armazenamento nas regiões westcentralus ou westus2. 
- O feed de alterações captura apenas as operações de criação, atualização, exclusão e cópia. As atualizações de metadados não são capturadas no momento na pré-visualização.
- Alterar registros de eventos para qualquer alteração pode aparecer mais de uma vez no feed de alterações.
- Você ainda não pode gerenciar a vida útil dos arquivos de log de feed de alteração definindo a política de retenção baseada no tempo sobre eles e você não pode excluir as bolhas 
- A `url` propriedade do arquivo de log está atualmente sempre vazia.
- A `LastConsumable` propriedade do arquivo segments.json não lista o primeiro segmento que o feed de alteração finaliza. Esse problema só ocorre após a finalização do primeiro segmento. Todos os segmentos subseqüentes após a `LastConsumable` primeira hora são capturados com precisão na propriedade.
- No momento, você não pode ver o contêiner **$blobchangefeed** quando você chama API listContainers e o contêiner não aparece no portal do Azure ou no Storage Explorer
- As contas de armazenamento que iniciaram anteriormente um [failover da conta](../common/storage-disaster-recovery-guidance.md) podem ter problemas com o arquivo log não aparecendo. Qualquer failover de conta futura também pode impactar o arquivo de log durante a visualização.

## <a name="faq"></a>Perguntas frequentes

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Qual é a diferença entre o registro de feed de alterações e o armazenamento de armazenamento?
Os registros de análise têm registros de todas as operações de leitura, gravação, lista e exclusão com solicitações bem-sucedidas e com falha em todas as operações. Os registros de análise são o melhor esforço e nenhum pedido é garantido.

O feed de alterações é uma solução que fornece registro transacional de mutações ou alterações bem-sucedidas em sua conta, como criação de bolhas, modificação e exclusões. O feed de alterações garante que todos os eventos sejam gravados e exibidos na ordem de alterações bem sucedidas por bolha, portanto, você não precisa filtrar o ruído de um grande volume de operações de leitura ou solicitações com falha. O feed de mudança é fundamentalmente projetado e otimizado para o desenvolvimento de aplicativos que requerem certas garantias.

### <a name="should-i-use-change-feed-or-storage-events"></a>Devo usar eventos de feed ou armazenamento de alterações?
Você pode aproveitar ambos os recursos como feed de alterações e eventos de [armazenamento Blob](storage-blob-event-overview.md) fornecem as mesmas informações com a mesma garantia de confiabilidade de entrega, com a principal diferença sendo a latência, o pedido e o armazenamento de registros de eventos. O feed de alterações publica registros no registro em poucos minutos após a alteração e também garante a ordem das operações de alteração por bolha. Os eventos de armazenamento são empurrados em tempo real e podem não ser ordenados. Os eventos de feed de alterações são armazenados duramente dentro de sua conta de armazenamento como registros estáveis somente leitura com sua própria retenção definida, enquanto os eventos de armazenamento são transitórios para serem consumidos pelo manipulador de eventos, a menos que você os armazene explicitamente. Com o feed Change, qualquer número de seus aplicativos pode consumir os logs à sua própria conveniência usando APIs ou SDKs blob. 

## <a name="next-steps"></a>Próximas etapas

- Veja um exemplo de como ler o feed de alterações usando um aplicativo cliente .NET. Consulte Os logs de feed de alteração de [processo no Azure Blob Storage](storage-blob-change-feed-how-to.md).
- Saiba como reagir aos eventos em tempo real. Veja [reagindo a eventos de armazenamento blob](storage-blob-event-overview.md)
- Saiba mais sobre informações detalhadas de registro para operações bem-sucedidas e com falha para todas as solicitações. Consulte [o registro de análises de armazenamento do Azure](../common/storage-analytics-logging.md)
