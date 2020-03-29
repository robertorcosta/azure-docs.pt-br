---
title: Migre seus dados faciais através de assinaturas - Face
titleSuffix: Azure Cognitive Services
description: Este guia mostra como migrar seus dados faciais armazenados de uma assinatura Face para outra.
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: lewlu
ms.openlocfilehash: e5ca51da7322e4eab4ea364ec5da086a1068fa9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169803"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrar seus dados de detecção facial para uma assinatura diferente de Detecção Facial

Este guia mostra como mover dados faciais, como um objeto PersonGroup salvo com rostos, para uma assinatura diferente do Azure Cognitive Services Face. Para mover os dados, você usa o recurso Snapshot. Desta forma, você evita ter que construir e treinar repetidamente um objeto PersonGroup ou FaceList quando você move ou expande suas operações. Por exemplo, talvez você tenha criado um objeto PersonGroup usando uma assinatura de avaliação gratuita e agora queira migrar para sua assinatura paga. Ou você pode precisar sincronizar dados faciais através de assinaturas em diferentes regiões para uma grande operação corporativa.

Essa mesma estratégia de migração também se aplica aos objetos LargePersonGroup e LargeFaceList. Se você não está familiarizado com os conceitos deste guia, veja suas definições no guia [de conceitos de reconhecimento facial.](../concepts/face-recognition.md) Este guia usa a biblioteca de clientes Face .NET com C#.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes itens:

- Duas chaves de assinatura face, uma com os dados existentes e uma para migrar. Para assinar o serviço Face e obter sua chave, siga as instruções em [Criar uma conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- A seqüência de ID de assinatura do Face que corresponde à assinatura de destino. Para encontrá-lo, selecione **Visão geral** no portal Azure. 
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

Este guia usa um aplicativo de console simples para executar a migração de dados faciais. Para obter uma implementação completa, consulte a [amostra de instantâneo face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) no GitHub.

1. No Visual Studio, crie um novo projeto de console .NET Framework. Nomeie-o **FaceApiSnapshotSample**.
1. Obtenha os pacotes necessários do NuGet. Clique com o botão direito do mouse no projeto no Solution Explorer e selecione **Gerenciar pacotes NuGet**. Selecione a guia **Procurar** e selecione **Incluir pré-lançamento**. Encontrar e instalar o seguinte pacote:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Criar clientes de detecção facial

No método **Main** em *Program.cs*, crie duas instâncias [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) para suas assinaturas de origem e de destino. Este exemplo usa uma assinatura Face na região da Ásia Oriental como fonte e uma assinatura dos EUA ocidentais como alvo. Este exemplo demonstra como migrar dados de uma região do Azure para outra. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Preencha os valores-chave da assinatura e urls de ponto final para sua origem e assinaturas de destino.


## <a name="prepare-a-persongroup-for-migration"></a>Preparar um PersonGroup para migração

Você precisa da ID do PersonGroup na sua assinatura de origem para migrá-la para a assinatura de destino. Use o método [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) para recuperar uma lista de objetos do PersonGroup. Em seguida, obtenha a propriedade [PersonGroup.PersonGroupId.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) Esse processo parece diferente com base nos objetos do PersonGroup que você tem. Neste guia, o ID persongroup `personGroupId`de origem é armazenado em .

> [!NOTE]
> O [código de exemplo](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) cria e treina um novo PersonGroup para migrar. Na maioria dos casos, você já deve ter um PersonGroup para usar.

## <a name="take-a-snapshot-of-a-persongroup"></a>Tire uma foto de um PersonGroup

Um instantâneo é o armazenamento remoto temporário para certos tipos de dados face. Ele funciona como um tipo de área de transferência para copiar dados de uma assinatura para outra. Primeiro, você tira um instantâneo dos dados na assinatura de origem. Em seguida, você aplica-o a um novo objeto de dados na assinatura de destino.

Use a instância FaceClient da assinatura de origem para tirar um instantâneo do PersonGroup. Use [o TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) com o PersonGroup ID e o ID da assinatura de destino. Se você tiver várias assinaturas de destino, adicione-as como entradas de matriz no terceiro parâmetro.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> O processo de tirar e aplicar instantâneos não interrompe nenhuma chamada regular para a fonte ou segmentar PersonGroups ou FaceLists. Não faça chamadas simultâneas que alterem o objeto de origem, como [chamadas de gerenciamento do FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) ou a chamada Do [PersonGroup Train,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) por exemplo. A operação de instantâneo pode ser executada antes ou depois dessas operações ou pode encontrar erros.

## <a name="retrieve-the-snapshot-id"></a>Recupere o ID de instantâneo

O método usado para tirar instantâneos é assíncrono, então você deve aguardar sua conclusão. As operações de instantâneonão podem ser canceladas. Neste código, `WaitForOperation` o método monitora a chamada assíncrona. Verifica o status a cada 100 ms. Após o término da operação, recupere um `OperationLocation` ID da operação analisando o campo. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Um `OperationLocation` valor típico se parece com isso:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

O método auxiliar `WaitForOperation` está aqui:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

Depois que o `Succeeded`status da operação aparecer, obtenha `ResourceLocation` o ID de instantâneo analisando o campo da instância OperaçãoStatus retornada.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Um `resourceLocation` valor típico se parece com isso:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Aplique um instantâneo a uma assinatura de destino

Em seguida, crie o novo PersonGroup na assinatura de destino usando um ID gerado aleatoriamente. Em seguida, use a instância FaceClient da assinatura de destino para aplicar o instantâneo a este PersonGroup. Passe no ID de instantâneo e no novo PersonGroup ID.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Um objeto Snapshot é válido por apenas 48 horas. Só tire um instantâneo se você pretende usá-lo para migração de dados logo depois.

Uma solicitação de aplicação de instantâneo retorna outro ID de operação. Para obter este ID, `OperationLocation` analise o campo da instância de snapshotresultado retornado. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

O processo de aplicação de instantâneos também `WaitForOperation` é assíncrono, então novamente use para esperar que ele termine.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testar a migração de dados

Depois de aplicar o instantâneo, o novo PersonGroup na assinatura de destino preenche com os dados faciais originais. Por padrão, os resultados do treinamento também são copiados. O novo PersonGroup está pronto para chamadas de identificação facial sem precisar de retreinamento.

Para testar a migração de dados, execute as seguintes operações e compare os resultados que eles imprimem com o console:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Use os seguintes métodos auxiliares:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Agora você pode usar o novo PersonGroup na assinatura de destino. 

Para atualizar o PersonGroup de destino novamente no futuro, crie um novo PersonGroup para receber o instantâneo. Para isso, siga os passos deste guia. Um único objeto PersonGroup pode ter um instantâneo aplicado a ele apenas uma vez.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar de migrar dados faciais, exclua manualmente o objeto de instantâneo.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Próximas etapas

Em seguida, consulte a documentação de referência da API relevante, explore um aplicativo de exemplo que usa o recurso Snapshot ou siga um guia de como começar a usar as outras operações de API mencionadas aqui:

- [Documentação de referência do instantâneo (SDK do .NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Amostra de instantâneo de face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Adicionar faces](how-to-add-faces.md)
- [Detectar rostos em uma imagem](HowtoDetectFacesinImage.md)
- [Identificar faces em imagens](HowtoIdentifyFacesinImage.md)
