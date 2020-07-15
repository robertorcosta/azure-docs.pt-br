---
title: 'Início Rápido: Biblioteca de clientes do Reconhecimento de Formulários para .NET'
description: Neste início rápido, comece a usar a biblioteca de clientes do Reconhecimento de Formulários para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: 2b46e115b6360b161a1b2ad9b176f3afbfaf27d0
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86277827"
---
[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Pacote (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Exemplos](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/).
* Um blob do Armazenamento do Azure contendo um conjunto de dados de treinamento. Confira [Criar um conjunto de dados de treinamento para um modelo personalizado](../../build-training-data-set.md) para obter dicas e opções para compilar o conjunto de dados de treinamento. Para este guia de início rápido, você pode usar os arquivos na pasta **Train** do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451).
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="create-a-form-recognizer-azure-resource"></a>Criar um recurso do Azure do Reconhecimento de Formulários

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Criar variáveis de ambiente

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `formrecognizer-quickstart`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. Em seguida, compile o aplicativo com:

```console
dotnet build
```

A saída de compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

No diretório do projeto, abra o arquivo *Program.cs* no IDE ou no editor de sua preferência. Adicione as seguintes diretivas `using`:

```csharp
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using Azure.AI.FormRecognizer.Training;

using System;
using System.IO;
using System.Threading.Tasks;
```

Em seguida, adicione o código a seguir ao método **Main** do aplicativo. Você definirá essa tarefa assíncrona posteriormente. 

```csharp
static void Main(string[] args)
{
    var t1 = RunFormRecognizerClient();

    Task.WaitAll(t1);
}
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Dentro do diretório do aplicativo, instale a biblioteca de clientes do Reconhecimento de Formulários para .NET com o seguinte comando:

```console
dotnet add package Azure.AI.FormRecognizer --version 1.0.0-preview.3
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de clientes estará disponível como um pacote baixável do NuGet.


<!-- Objet model TBD -->



## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como realizar as seguintes tarefas com a biblioteca de clientes do Reconhecimento de Formulários para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer o conteúdo do formulário](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Treinar um modelo personalizado](#train-a-custom-model)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerenciar seus modelos personalizados](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Autenticar o cliente

Abaixo do método `Main`, defina a tarefa que é referenciada em `Main`. Aqui, você autenticará os objetos de cliente usando as variáveis de assinatura definidas acima. Você usará um objeto **AzureKeyCredential**, para, se necessário, atualizar a chave de API sem criar objetos de cliente.

```csharp
static async Task RunFormRecognizerClient()
{ 
    string endpoint = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_ENDPOINT");
    string apiKey = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_KEY");
    var credential = new AzureKeyCredential(apiKey);
    
    var trainingClient = new FormTrainingClient(new Uri(endpoint), credential);
    var recognizerClient = new FormRecognizerClient(new Uri(endpoint), credential);
```

### <a name="call-client-specific-methods"></a>Chamar métodos específicos do cliente

O próximo bloco de código usa os objetos de cliente para chamar métodos para cada uma das principais tarefas no SDK do Reconhecimento de Formulários. Você definirá esses métodos mais tarde.

Você também precisará adicionar referências às URLs para os dados de treinamento e teste. 
* Para recuperar a URL de SAS para os dados de treinamento do modelo personalizado, abra o Gerenciador de Armazenamento do Microsoft Azure, clique com o botão direito do mouse no contêiner e selecione **Obter Assinatura de Acesso Compartilhado**. Verifique se as permissões de **Leitura** e **Lista** estão marcadas e clique em **Criar**. Em seguida, copie o valor na seção **URL**. Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Para obter uma URL de um formulário para teste, use as etapas acima para obter a URL de SAS de um documento individual no armazenamento de blobs. Ou então, selecione a URL de um documento localizado em outro lugar.
* Use o método acima para obter a URL de uma imagem de recibo também ou use a URL da imagem de exemplo fornecida.

> [!NOTE]
> Os snippets de código deste guia usam formulários remotos acessados por URLs. Caso deseje processar documentos de formulário local, confira os métodos relacionados na [documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre).

```csharp
    string trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    string formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    string receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    Console.WriteLine("Get form content...");
    await GetContent(recognizerClient, formUrl);

    Console.WriteLine("Analyze receipt...");
    await AnalyzeReceipt(recognizerClient, receiptUrl);

    Console.WriteLine("Train Model with training data...");
    Guid modelId = await TrainModel(trainingClient, trainingDataUrl);

    Console.WriteLine("Analyze PDF form...");
    await AnalyzePdfForm(recognizerClient, modelId, formUrl);

    Console.WriteLine("Manage models...");
    await ManageModels(trainingClient, trainingDataUrl) ;
}
```

## <a name="recognize-form-content"></a>Reconhecer o conteúdo do formulário

Use o Reconhecimento de Formulários para reconhecer tabelas, linhas e palavras em documentos, sem a necessidade de treinar um modelo.

Para reconhecer o conteúdo de um arquivo em um URI especificado, use o método **StartRecognizeContentFromUri**.

```csharp
private static async Task<Guid> GetContent(
    FormRecognizerClient recognizerClient, string invoiceUri)
{
    Response<FormPageCollection> formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(invoiceUri))
        .WaitForCompletionAsync();
```

O valor retornado é uma coleção de objetos **FormPage**: um para cada página no documento enviado. O código a seguir itera nesses objetos e imprime os pares de chave/valor extraídos e os dados da tabela.

```csharp
    foreach (FormPage page in formPages.Value)
    {
        Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count}" + 
            $" lines.");
    
        for (int i = 0; i < page.Lines.Count; i++)
        {
            FormLine line = page.Lines[i];
            Console.WriteLine($"    Line {i} has {line.Words.Count}" + 
                $" word{(line.Words.Count > 1 ? "s" : "")}," +
                $" and text: '{line.Text}'.");
        }
    
        for (int i = 0; i < page.Tables.Count; i++)
        {
            FormTable table = page.Tables[i];
            Console.WriteLine($"Table {i} has {table.RowCount} rows and" +
                $" {table.ColumnCount} columns.");
            foreach (FormTableCell cell in table.Cells)
            {
                Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex})" +
                    $" contains text: '{cell.Text}'.");
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Reconhecer recibos

Esta seção demonstra como reconhecer e extrair campos comuns de recibos dos EUA usando um modelo de recibo pré-treinado.

Para reconhecer recibos de um URI, use o método **StartRecognizeReceiptsFromUri**. O valor retornado é uma coleção de objetos **RecognizedReceipt**: um para cada página no documento enviado. O código a seguir processa um recibo no URI fornecido e imprime os valores e os campos principais no console.

```csharp
private static async Task<Guid> AnalyzeReceipt(
    FormRecognizerClient recognizerClient, string receiptUri)
{
    RecognizedReceiptCollection receipts = await recognizerClient.StartRecognizeReceiptsFromUri(new Uri(receiptUri))
    .WaitForCompletionAsync();

    foreach (RecognizedReceipt receipt in receipts)
    {
    FormField merchantNameField;
    if (receipt.RecognizedForm.Fields.TryGetValue("MerchantName", out merchantNameField))
    {
        if (merchantNameField.Value.Type == FieldValueType.String)
        {
            string merchantName = merchantNameField.Value.AsString();

            Console.WriteLine($"Merchant Name: '{merchantName}', with confidence {merchantNameField.Confidence}");
        }
    }

    FormField transactionDateField;
    if (receipt.RecognizedForm.Fields.TryGetValue("TransactionDate", out transactionDateField))
    {
        if (transactionDateField.Value.Type == FieldValueType.Date)
        {
            DateTime transactionDate = transactionDateField.Value.AsDate();

            Console.WriteLine($"Transaction Date: '{transactionDate}', with confidence {transactionDateField.Confidence}");
        }
    }
```

O próximo bloco de código itera em itens individuais detectados no recibo e imprime os detalhes no console.

```csharp
    FormField itemsField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Items", out itemsField))
    {
        if (itemsField.Value.Type == FieldValueType.List)
        {
            foreach (FormField itemField in itemsField.Value.AsList())
            {
                Console.WriteLine("Item:");

                if (itemField.Value.Type == FieldValueType.Dictionary)
                {
                    IReadOnlyDictionary<string, FormField> itemFields = itemField.Value.AsDictionary();

                    FormField itemNameField;
                    if (itemFields.TryGetValue("Name", out itemNameField))
                    {
                        if (itemNameField.Value.Type == FieldValueType.String)
                        {
                            string itemName = itemNameField.Value.AsString();

                            Console.WriteLine($"    Name: '{itemName}', with confidence {itemNameField.Confidence}");
                        }
                    }

                    FormField itemTotalPriceField;
                    if (itemFields.TryGetValue("TotalPrice", out itemTotalPriceField))
                    {
                        if (itemTotalPriceField.Value.Type == FieldValueType.Float)
                        {
                            float itemTotalPrice = itemTotalPriceField.Value.AsFloat();

                            Console.WriteLine($"    Total Price: '{itemTotalPrice}', with confidence {itemTotalPriceField.Confidence}");
                        }
                    }
                }
            }
        }
    }
```

Por fim, o último bloco de código imprime o valor total no recibo.

```csharp
    FormField totalField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Total", out totalField))
    {
        if (totalField.Value.Type == FieldValueType.Float)
        {
            float total = totalField.Value.AsFloat();

            Console.WriteLine($"Total: '{total}', with confidence '{totalField.Confidence}'");
        }
    }
}
```

## <a name="train-a-custom-model"></a>Treinar um modelo personalizado

Esta seção demonstra como treinar um modelo com os próprios dados. Um modelo treinado pode gerar dados estruturados que incluem as relações de chave/valor no documento de formulário original. Depois de treinar no modelo, você pode testá-lo, treinar novamente e, por fim, usá-lo para extrair, de forma confiável, dados de mais formulários de acordo com suas necessidades.

> [!NOTE]
> Você também pode treinar modelos com uma interface gráfica do usuário, como a [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Treinar um modelo sem rótulos

Treine modelos personalizados para reconhecer todos os campos e valores encontrados nos formulários personalizados sem rotular manualmente os documentos de treinamento.

O método a seguir treina um modelo em um especificado conjunto de documentos e imprime o status do modelo no console. 

```csharp
private static async Task<Guid> TrainModel(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: false).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

O objeto **CustomFormModel** retornado contém informações sobre os tipos de formulários que o modelo pode reconhecer e os campos que ele pode extrair de cada tipo de formulário. O bloco de código a seguir imprime essas informações no console.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

Por fim, esse método retorna a ID exclusiva do modelo.

```csharp
    return model.ModelId;
}
```

### <a name="train-a-model-with-labels"></a>Treinar um modelo com rótulos

Você também pode treinar modelos personalizados rotulando manualmente os documentos de treinamento. O treinamento com rótulos leva a um melhor desempenho em alguns cenários. Para o treinamento com rótulos, você precisará ter arquivos de informações de rótulo especiais ( *\<filename\>.pdf.labels.json*) no contêiner de armazenamento de blobs junto com os documentos de treinamento. A [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md) fornece uma interface do usuário para ajudar você a criar esses arquivos de rótulo. Depois de obtê-los, chame o método **StartTrainingAsync** com o parâmetro *uselabels* definido como `true`.

```csharp
private static async Task<Guid> TrainModelWithLabelsAsync(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
    .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: true).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

O **CustomFormModel** retornado indica os campos que o modelo pode extrair, juntamente com a precisão estimada em cada campo. O bloco de código a seguir imprime essas informações no console.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Accuracy != null)
            {
                Console.Write($", Accuracy: {field.Accuracy}");
            }
            Console.WriteLine("");
        }
    }
    return model.ModelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Esta seção demonstra como extrair informações de chave/valor e outro conteúdo dos tipos de formulário personalizados, usando modelos treinados com os próprios formulários.

> [!IMPORTANT]
> Para implementar esse cenário, você já precisará ter treinado um modelo para passar a ID para o método abaixo. Confira a seção [Treinar um modelo](#train-a-model-without-labels).

Você usará o método **StartRecognizeCustomFormsFromUri**. O valor retornado é uma coleção de objetos **RecognizedForm**: um para cada página no documento enviado.

```csharp
// Analyze PDF form data
private static async Task AnalyzePdfForm(
    FormRecognizerClient formClient, Guid modelId, string pdfFormFile)
{    
    Response<IReadOnlyList<RecognizedForm>> forms = await recognizerClient
        .StartRecognizeCustomFormsFromUri(modelId, new Uri(formUri))
        .WaitForCompletionAsync();
```

O código a seguir imprime os resultados da análise no console. Ele imprime cada campo reconhecido e o valor correspondente, juntamente com uma pontuação de confiança.

```csharp
    foreach (RecognizedForm form in forms.Value)
    {
        Console.WriteLine($"Form of type: {form.FormType}");
        foreach (FormField field in form.Fields.Values)
        {
            Console.WriteLine($"Field '{field.Name}: ");
    
            if (field.LabelText != null)
            {
                Console.WriteLine($"    Label: '{field.LabelText.Text}");
            }
    
            Console.WriteLine($"    Value: '{field.ValueText.Text}");
            Console.WriteLine($"    Confidence: '{field.Confidence}");
        }
    }
}
```

## <a name="manage-your-custom-models"></a>Gerenciar seus modelos personalizados

Esta seção demonstra como gerenciar os modelos personalizados armazenados na sua conta. O código a seguir executa todas as tarefas de gerenciamento de modelos em um só método, como um exemplo. Comece copiando a assinatura de método abaixo:

```csharp
private static async Task ManageModels(
    FormRecognizerClient trainingClient, string trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verificar o número de modelos na conta do recurso do FormRecognizer

O bloco de código a seguir verifica quantos modelos você salvou na sua conta do Reconhecimento de Formulários e os compara com o limite da conta.

```csharp
    // Check number of models in the FormRecognizer account, 
    // and the maximum number of models that can be stored.
    AccountProperties accountProperties = trainingClient.GetAccountProperties();
    Console.WriteLine($"Account has {accountProperties.CustomModelCount} models.");
    Console.WriteLine($"It can have at most {accountProperties.CustomModelLimit}" +
        $" models.");
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Listar os modelos atualmente armazenados na conta do recurso

O bloco de código a seguir lista os modelos atuais na sua conta e imprime os detalhes no console.

```csharp
    // List the first ten or fewer models currently stored in the account.
    Pageable<CustomFormModelInfo> models = trainingClient.GetModelInfos();
    
    foreach (CustomFormModelInfo modelInfo in models.Take(10))
    {
        Console.WriteLine($"Custom Model Info:");
        Console.WriteLine($"    Model Id: {modelInfo.ModelId}");
        Console.WriteLine($"    Model Status: {modelInfo.Status}");
        Console.WriteLine($"    Created On: {modelInfo.CreatedOn}");
        Console.WriteLine($"    Last Modified: {modelInfo.LastModified}");
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>Obter um modelo específico usando a ID do modelo

O bloco de código a seguir treina um novo modelo (assim como na seção [Treinar um modelo](#train-a-model-without-labels)) e recupera uma segunda referência a ele usando a respectiva ID.

```csharp
    // Create a new model to store in the account
    CustomFormModel model = await trainingClient.StartTrainingAsync(
        new Uri(trainingFileUrl)).WaitForCompletionAsync();
    
    // Get the model that was just created
    CustomFormModel modelCopy = trainingClient.GetCustomModel(model.ModelId);
    
    Console.WriteLine($"Custom Model {modelCopy.ModelId} recognizes the following" +
        " form types:");
    
    foreach (CustomFormSubModel subModel in modelCopy.Models)
    {
        Console.WriteLine($"SubModel Form Type: {subModel.FormType}");
        foreach (CustomFormModelField field in subModel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

### <a name="delete-a-model-from-the-resource-account"></a>Excluir um modelo da conta do recurso

Exclua também um modelo da sua conta referenciando a ID.

```csharp
    // Delete the model from the account.
    trainingClient.DeleteModel(model.ModelId);
}
```

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Solução de problemas

Quando você interagir com a biblioteca de clientes do Reconhecimento de Formulários de Serviços Cognitivos usando o SDK do .NET, os erros retornados pelo serviço resultarão em uma `RequestFailedException`. Eles incluirão o mesmo código de status HTTP que teria sido retornado por uma solicitação da API REST.

Por exemplo, se você enviar uma imagem de recibo com um URI inválido, um erro `400` será retornado, indicando "Solicitação inválida".

```csharp Snippet:FormRecognizerBadRequest
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();

}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

Você observará que as informações adicionais, como a ID de solicitação do cliente da operação, são registradas em log.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou a biblioteca de clientes .NET do Reconhecimento de Formulários para treinar modelos personalizado e analisar formulários de diferentes maneiras. Em seguida, aprenda dicas para criar um conjunto de dados de treinamento melhor e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de treinamento](../../build-training-data-set.md)

* [O que é o Reconhecimento de Formulários?](../../overview.md)
* Encontre o código de exemplo deste guia (e outros) no [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).
