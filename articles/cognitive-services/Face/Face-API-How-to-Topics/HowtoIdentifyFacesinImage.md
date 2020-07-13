---
title: 'Exemplo: Identificar rostos em imagens – Detecção Facial'
titleSuffix: Azure Cognitive Services
description: Este guia demonstra como identificar faces desconhecidas, usando objetos PersonGroup que são criados com base em pessoas conhecidas anteriormente.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 07/02/2020
ms.author: sbowles
ms.openlocfilehash: 607f67258c5d069590f934891c09ccada780c977
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85918751"
---
# <a name="example-identify-faces-in-images"></a>Exemplo: Identificar faces em imagens

Este guia demonstra como identificar faces desconhecidas, usando objetos PersonGroup que são criados com base em pessoas conhecidas anteriormente. As amostras foram escritas em C# com a biblioteca de clientes da Detecção Facial dos Serviços Cognitivos do Azure.

Este exemplo demonstra:

- Como criar um PersonGroup. Esse PersonGroup contém uma lista de pessoas conhecidas.
- Como atribuir faces a cada pessoa. Essas faces são usadas como uma linha de base para identificar pessoas. É recomendável que você use exibições frontais e claras das faces. Um exemplo seria uma ID de foto. Um bom conjunto de fotos inclui faces da mesma pessoa em poses, cores de roupa ou estilos de cabelo diferentes.

## <a name="prerequisites"></a>Pré-requisitos
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Crie um recurso da Detecção Facial"  target="_blank">crie um recurso da Detecção Facial <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso** e copie a chave.
* Algumas fotos com os rostos de pessoas identificadas. [Baixar fotos de exemplo](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) para Ana, Guilherme e Clara.
* Uma série de fotos de teste. As fotos podem ou não conter os rostos das pessoas identificadas. Use algumas imagens do link de fotos de exemplo.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Crie um novo aplicativo .NET Core em seu IDE ou editor preferido. 

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `face-identify`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```dotnetcli
dotnet build
```

A saída de compilação não deve conter nenhum aviso ou erro. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="step-1-authorize-the-api-call"></a>Etapa 1: Autorizar a chamada à API

Cada chamada à API de Detecção Facial exige uma chave de assinatura. Essa chave pode ser passada por um parâmetro de cadeia de caracteres de consulta ou especificada no cabeçalho da solicitação. Quando você usar uma biblioteca de clientes, a chave de assinatura será passada através do construtor da classe **FaceClient**. Adicione o código a seguir ao método **Main** do arquivo *Program.cs*.
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<subscription key>"),
    new System.Net.Http.DelegatingHandler[] { });
```

## <a name="step-2-create-the-persongroup"></a>Etapa 2: Criar o PersonGroup

Nessa etapa, um **PersonGroup** denominado "MyFriends" contém Ana, Guilherme e Clara. Cada pessoa tem várias faces registradas. As faces devem ser detectadas entre as imagens. Depois de todas essas etapas, você tem um **PersonGroup** como o desta imagem:

![MyFriends](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Etapa 2.1: Definir pessoas para o PersonGroup
Uma pessoa é uma unidade básica de identificação. Uma pessoa pode ter uma ou mais faces conhecidas registradas. Um **PersonGroup** é uma coleção de pessoas. Cada pessoa é definida dentro de um **PersonGroup** específico. A identificação é feita com relação a um **PersonGroup**. A tarefa é criar um **PersonGroup** e criar as pessoas nele, como Ana, Guilherme e Clara.

Primeiro, crie um **PersonGroup**, usando a API [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244). A API da biblioteca de clientes correspondente é o método **CreatePersonGroupAsync** para a classe **FaceClient**. A ID do grupo que é especificada para criar o grupo é exclusiva para cada assinatura. Você também pode obter, atualizar ou excluir **PersonGroups**, usando outras APIs **PersonGroup**. 

Após um grupo ser definido, você poderá definir as pessoas dentro delem, usando a API [Pessoa do PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c). O método da biblioteca de clientes é **CreatePersonAsync**. Você pode adicionar uma face a cada pessoa depois que elas tiverem sido criadas.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a> Etapa 2.2: Detectar faces e registrá-las para a pessoa correta
A detecção é feita enviando uma solicitação da Web "POST" para A API [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) com o arquivo de imagem no corpo da solicitação HTTP. Quando você usa a biblioteca de clientes, a detecção facial é feita por um dos métodos Detect..Async da classe FaceClient.

Para cada face detectada, chame [Pessoa do PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) para adicioná-la à pessoa correta.

O código a seguir demonstra o processo de como detectar uma face de uma imagem e adicioná-la a uma pessoa:

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Se a imagem tiver mais de uma face, apenas a face maior será adicionada. Você pode adicionar outras faces à pessoa. Passar uma cadeia de caracteres no formato "targetFace = esquerda, superior, largura, altura" para o parâmetro de consulta da API [Pessoa do PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b). Você também pode usar o parâmetro opcional targetFace no método AddPersonFaceAsync para adicionar outras faces. Cada face adicionada à pessoa receberá uma ID facial persistente exclusiva. Você pode usar essa ID em [Pessoa do PersonGroup – Excluir Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) e [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="step-3-train-the-persongroup"></a>Etapa 3: Treinar o PersonGroup

O **PersonGroup** precisa ser treinado antes que se possa utilizá-lo para realizar uma identificação. O **PersonGroup** precisará ser treinado novamente após uma pessoa ser adicionada ou removida ou se você editar o rosto de uma pessoa registrada. O treinamento é feito com A API [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). Quando você usa a biblioteca de clientes, isso é uma chamada ao método **TrainPersonGroupAsync**:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
O treinamento é um processo assíncrono. Ele poderá não estar concluído mesmo depois que o método **TrainPersonGroupAsync** tiver retornado. Talvez seja necessário consultar o status do treinamento. Use a API [PersonGroup – Obter Status do Treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) ou o método **GetPersonGroupTrainingStatusAsync** da biblioteca de clientes. O seguinte código demonstra uma lógica simples de esperar o fim do treinamento do **PersonGroup**:
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Etapa 4: Identificar um rosto com relação a um PersonGroup definido

Quando o serviço de Detecção Facial faz identificações, ele calcula a semelhança de um rosto de teste entre todos os rostos em um grupo. Ela retorna a pessoa mais comparável à face de teste. Esse processo é feito por meio da API [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) ou pelo método IdentifyAsync da biblioteca de clientes.

A face de teste deve ser detectada por meio das etapas anteriores. Em seguida, a ID facial é passada para a API de identificação como um segundo argumento. Várias IDs faciais podem ser identificadas ao mesmo tempo. O resultado contém todos os resultados identificados. Por padrão, o processo de identificação retornará somente uma pessoa que melhor corresponda à face de teste. Se preferir, especifique o parâmetro opcional _maxNumOfCandidatesReturned_ para permitir que o processo de identificação retorne mais candidatos.

O código a seguir demonstra o processo de identificação:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectWithStreamAsync(s);
    var faceIds = faces.Select(face => face.FaceId.Value).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Depois de concluir as etapas, tente identificar faces diferentes. Veja se as faces de Ana, Guilherme ou Clara podem ser identificadas corretamente, de acordo com as imagens carregadas para detecção facial. Veja os exemplos a seguir:

![Identificar faces diferentes](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Etapa 5: Solicitar grande escala

Um **PersonGroup** pode conter até 10 mil pessoas com base na limitação de design anterior. Para obter mais informações sobre cenários em escala de até milhões, veja [Como usar o recurso de larga escala](how-to-use-large-scale.md).

## <a name="summary"></a>Resumo

Neste guia, você aprendeu o processo de criar um **PersonGroup** e identificar uma pessoa. Os seguintes recursos foram explicados e demonstrados:

- Detecte faces usando a API [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d).
- Crie PersonGroups usando a API [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244).
- Crie pessoas usando a API [Pessoa do PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c).
- Treine um PersonGroup usando a API [PersonGroup – Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).
- Identifique faces desconhecidas no PersonGroup usando a API [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="related-topics"></a>Tópicos relacionados

- [Conceitos de reconhecimento facial](../concepts/face-recognition.md)
- [Detectar faces em uma imagem](HowtoDetectFacesinImage.md)
- [Adicionar faces](how-to-add-faces.md)
- [Usar o recurso de larga escala](how-to-use-large-scale.md)
