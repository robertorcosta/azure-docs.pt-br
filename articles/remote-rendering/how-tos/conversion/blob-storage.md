---
title: Use o Armazenamento Azure Blob para conversão de modelos
description: Descreve passos comuns para configurar e usar o armazenamento blob para conversão do modelo.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681643"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Use o Armazenamento Azure Blob para conversão de modelos

O serviço [de conversão de modelos](model-conversion.md) requer acesso ao armazenamento blob do Azure para que ele possa recuperar dados de entrada e armazenar dados de saída. Este artigo descreve como fazer os passos mais comuns.

## <a name="prepare-azure-storage-accounts"></a>Prepare as contas de armazenamento do Azure

- Criar uma conta de armazenamento (StorageV2)
- Criar um recipiente de bolha de entrada na conta de armazenamento (por exemplo, chamado "arrinput")
- Criar um recipiente blob de saída na conta de armazenamento (por exemplo, chamado "arroutput")

> [!TIP]
> Para obter instruções passo a passo de como configurar sua conta de armazenamento, dê uma olhada no [Quickstart: Converta um modelo para renderização](../../quickstarts/convert-model.md)

A criação da conta de armazenamento e dos recipientes blob pode ser feita com uma das seguintes ferramentas:

- [Portal do Azure](https://portal.azure.com)
- [linha de comando az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)
- SDKs (C#, Python ... )

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Certifique-se de que a renderização remota do Azure pode acessar sua conta de armazenamento

O Azure Remote Rending precisa recuperar dados do modelo da sua conta de armazenamento e gravar dados de volta para ele.

Você pode conceder acesso à renderização remota do Azure à sua conta de armazenamento das duas maneiras a seguir:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Conecte sua conta de armazenamento do Azure com sua conta de renderização remota do Azure

Siga os passos dados na seção [Criar uma conta.](../create-an-account.md#link-storage-accounts)

### <a name="retrieve-sas-for-the-storage-containers"></a>Recupere SAS para os recipientes de armazenamento

As assinaturas de acesso armazenadas (SAS) são usadas para conceder acesso à leitura para entrada e gravar acesso para saída. Recomendamos a geração de novas URIs cada vez que um modelo é convertido. Uma vez que os URIs expiram após algum tempo, permaneços por mais tempo pode correr o risco de quebrar seu aplicativo inesperadamente.

Os detalhes sobre o SAS podem ser encontrados na [documentação do SAS.](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

Um Uri SAS pode ser gerado usando um dos:

- módulo AZ PowerShell
  - veja o [exemplo de scripts PowerShell](../../samples/powershell-example-scripts.md)
- [linha de comando az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)
  - clique com o botão direito do mouse no contêiner "Obter assinatura de acesso compartilhado" (leia, liste o acesso para o contêiner de entrada, escreva acesso para o contêiner de saída)
- SDKs (C#, Python ... )

Um exemplo de uso de Assinaturas de Acesso Compartilhado na conversão de ativos é mostrado em Conversion.ps1 dos [Scripts de exemplo do Powershell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Carregar um modelo de entrada

Para começar a converter um modelo, você precisa carregá-lo, usando uma das seguintes opções:

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) - uma ui conveniente para carregar/baixar/gerenciar arquivos no armazenamento azure blob
- [Linha de comando Azure](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - veja os [scripts do Exemplo PowerShell](../../samples/powershell-example-scripts.md)
- [Usando um SDK de armazenamento (Python, C# ... )](https://docs.microsoft.com/azure/storage/)
- [Usando as APIs de descanso de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

Para um exemplo de como fazer upload de dados para conversão, consulte Conversion.ps1 dos [Scripts de exemplo do Powershell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="get-a-sas-uri-for-the-converted-model"></a>Obtenha um URI SAS para o modelo convertido

Esta etapa é semelhante à [recuperação de SAS para os recipientes de armazenamento](#retrieve-sas-for-the-storage-containers). No entanto, desta vez você precisa recuperar um URI SAS para o arquivo modelo, que foi escrito no recipiente de saída.

Por exemplo, para recuperar um Uri SAS através do [Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)clique com o botão direito do mouse no arquivo do modelo e selecione "Obter assinatura de acesso compartilhado".

Uma Assinatura de Acesso Compartilhado (SAS) para carregar modelos é necessária se você não tiver conectado sua conta de armazenamento à sua conta de renderização remota Do Zure. Você pode aprender como conectar sua conta em [Criar uma conta](../create-an-account.md#link-storage-accounts).

## <a name="next-steps"></a>Próximas etapas

- [Configuração da conversão do modelo](configure-model-conversion.md)
- [A API de conversão do modelo REST](conversion-rest-api.md)
