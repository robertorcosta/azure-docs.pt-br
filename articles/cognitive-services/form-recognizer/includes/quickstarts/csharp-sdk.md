---
title: 'Início Rápido: Biblioteca de clientes do Reconhecimento de Formulários para .NET'
description: Use a biblioteca de clientes para .NET do Reconhecimento de Formulários para criar um aplicativo de processamento de formulários que extraia pares de chave/valor e dados de tabela de seus documentos personalizados.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.author: pafarley
ms.openlocfilehash: fc7b435d3abdd2e04f8beabf35b7ed337c5ff68b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91318868"
---
> [!IMPORTANT]
> * No momento, o SDK do Reconhecimento de Formulários direciona a v2.0 do serviço de Reconhecimento de Formulários.
> * O código neste artigo usa métodos síncronos e armazenamento de credenciais não seguras por motivos de simplicidade. Confira a documentação de referência abaixo. 

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Pacote (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Exemplos](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services).
* Um blob do Armazenamento do Azure contendo um conjunto de dados de treinamento. Confira [Criar um conjunto de dados de treinamento para um modelo personalizado](../../build-training-data-set.md) para obter dicas e opções para compilar o conjunto de dados de treinamento. Para este guia de início rápido, você pode usar os arquivos na pasta **Train** do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451).
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Depois de ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Criar um recurso do Reconhecimento de Formulários"  target="_blank">crie um Reconhecimento de Formulários <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API do Reconhecimento de Formulários. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

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

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Dentro do diretório do aplicativo, instale a biblioteca de clientes do Reconhecimento de Formulários para .NET com o seguinte comando:

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

> [!TIP]
> Se você estiver usando o IDE do Visual Studio, a biblioteca de clientes estará disponível como um pacote baixável do NuGet.

No diretório do projeto, abra o arquivo *Program.cs* no IDE ou no editor de sua preferência. Adicione as seguintes diretivas `using`:

```csharp
using Azure;
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using Azure.AI.FormRecognizer.Training;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
```

## <a name="object-model"></a>Modelo de objeto 

Com o Reconhecimento de Formulários, você pode criar dois tipos diferentes de cliente. O primeiro, `FormRecognizerClient`, é usado para consultar o serviço em campos e conteúdo do formulário reconhecidos. O segundo, `FormTrainingClient`, é usado para criar e gerenciar modelos personalizados que você pode usar para aprimorar o reconhecimento. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` fornece operações para:

 - Reconhecer campos e conteúdo do formulário usando modelos personalizados treinados para reconhecer seus formulários personalizados.  Esses valores são retornados em uma coleção de objetos `RecognizedForm`. Confira o exemplo [Analisar formulários personalizados](#analyze-forms-with-a-custom-model).
 - Reconhecer o conteúdo do formulário, incluindo tabelas, linhas e palavras, sem a necessidade de treinar um modelo.  O conteúdo do formulário é retornado em uma coleção de objetos `FormPage`. Confira o exemplo [Reconhecer o conteúdo do formulário](#recognize-form-content).
 - Reconhecer campos comuns de recibos dos EUA, usando um modelo de recibo pré-treinado no serviço do Reconhecimento de Formulários.  Esses campos e metadados são retornados em uma coleção de objetos `RecognizedForm`. Confira o exemplo [Reconhecer recibos](#recognize-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` fornece operações para:

- Treinar modelos personalizados para reconhecer todos os campos e valores encontrados em seus formulários personalizados.  Um `CustomFormModel` é retornado indicando os tipos de formulário que o modelo reconhecerá e os campos que serão extraídos para cada tipo de formulário.
- Treinar modelos personalizados para reconhecer campos e valores específicos que você determina rotulando seus formulários personalizados.  Um `CustomFormModel` é retornado indicando os campos que o modelo extrairá, bem como a precisão estimada de cada campo.
- Gerenciar modelos criados em sua conta.
- Copiar um modelo personalizado de um recurso de Reconhecimento de Formulários para outro.

Confira exemplos para [Treinar um modelo](#train-a-custom-model) e [Gerenciar modelos personalizados](#manage-custom-models).

> [!NOTE]
> Os modelos também podem ser treinados usando uma interface gráfica do usuário, como a [Ferramenta de Rotulagem do Reconhecimento de Formulários](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool).

## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como realizar as seguintes tarefas com a biblioteca de clientes do Reconhecimento de Formulários para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer o conteúdo do formulário](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Treinar um modelo personalizado](#train-a-custom-model)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerenciar seus modelos personalizados](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Autenticar o cliente

Embaixo de `Main()`, crie um método chamado `AuthenticateClient`. Você usará isso em tarefas futuras para autenticar suas solicitações para o serviço de Reconhecimento de Formulários. Esse método usa o objeto `AzureKeyCredential`, portanto, se necessário, você pode atualizar a chave de API sem criar objetos de cliente.

> [!IMPORTANT]
> Obtenha a chave e o ponto de extremidade no portal do Azure. Se o recurso do Reconhecimento de Formulários que você criou na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Ir para o Recurso** em **Próximas etapas**. Encontre a chave e o ponto de extremidade na página da **chave e do ponto de extremidade** do recurso, em **gerenciamento de recursos**. 
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

```csharp
static private FormRecognizerClient AuthenticateClient(){
    string endpoint = "<replace-with-your-form-recognizer-endpoint-here>";
    string apiKey = "<replace-with-your-form-recognizer-key-here>";
    var credential = new AzureKeyCredential(apiKey);
    var client = new FormRecognizerClient(new Uri(endpoint), credential);
    return client;
}
```

## <a name="get-assets-for-testing"></a>Obter ativos para teste 

Os snippets de código deste guia usam formulários remotos acessados por URLs. Caso deseje processar documentos de formulário local, confira os métodos relacionados na [documentação de referência](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) e nas [amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

Você também precisará adicionar referências às URLs para os dados de treinamento e teste.

* Para recuperar a URL de SAS para os dados de treinamento do modelo personalizado, abra o Gerenciador de Armazenamento do Microsoft Azure, clique com o botão direito do mouse no contêiner e selecione **Obter Assinatura de Acesso Compartilhado**. Verifique se as permissões de **Leitura** e **Lista** estão marcadas e clique em **Criar**. Em seguida, copie o valor na seção **URL**. Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Use as imagens de exemplo e de recibo incluídas nos exemplos abaixo (também disponíveis no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) ou use as etapas acima para obter a URL SAS de um documento individual no Armazenamento de Blobs. 

> [!NOTE]
> Os snippets de código deste guia usam formulários remotos acessados por URLs. Caso deseje processar documentos de formulário local, confira os métodos relacionados na [documentação de referência](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

## <a name="recognize-form-content"></a>Reconhecer o conteúdo do formulário

Use o Reconhecimento de Formulários para reconhecer tabelas, linhas e palavras em documentos, sem a necessidade de treinar um modelo. O valor retornado é uma coleção de objetos **FormPage**: um para cada página no documento enviado. 

Para reconhecer o conteúdo de um arquivo em um URI especificado, use o método `StartRecognizeContentFromUri`.

```csharp
static async Task RecognizeContent(){
    var invoiceUri = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";
    var recognizeClient =  AuthenticateClient();
    FormPageCollection formPages = await recognizeClient
        .StartRecognizeContentFromUri(new Uri(invoiceUri))
        .WaitForCompletionAsync();
    foreach (FormPage page in formPages)
    {
        Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count} lines.");

        for (int i = 0; i < page.Lines.Count; i++)
        {
            FormLine line = page.Lines[i];
            Console.WriteLine($"    Line {i} has {line.Words.Count} word{(line.Words.Count > 1 ? "s" : "")}, and text: '{line.Text}'.");
        }

        for (int i = 0; i < page.Tables.Count; i++)
        {
            FormTable table = page.Tables[i];
            Console.WriteLine($"Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");
            foreach (FormTableCell cell in table.Cells)
            {
                Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) contains text: '{cell.Text}'.");
            }
        }
    }
}
```

Para executar esse método, você precisará chamá-lo de `Main`. 

```csharp
static void Main(string[] args)
{
    var analyzeForm = RecognizeContent();
    Task.WaitAll(analyzeForm);
}
```

### <a name="output"></a>Saída

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
```

## <a name="recognize-receipts"></a>Reconhecer recibos

Esta seção demonstra como reconhecer e extrair campos comuns de recibos dos EUA usando um modelo de recibo pré-treinado.

Para reconhecer recibos de um URI, use o método `StartRecognizeReceiptsFromUri`. O valor retornado é uma coleção de objetos `RecognizedReceipt`: um para cada página no documento enviado. O código a seguir processa um recibo no URI fornecido e imprime os valores e os campos principais no console.

```csharp
static async Task RecognizeReceipts(){
    var receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png";
    var recognizeClient = AuthenticateClient();
    RecognizedFormCollection receipts = await recognizeClient.StartRecognizeReceiptsFromUri(new Uri(receiptUrl)).WaitForCompletionAsync();

    // To see the list of the supported fields returned by service and its corresponding types, consult:
    // https://aka.ms/formrecognizer/receiptfields

    foreach (RecognizedForm receipt in receipts)
    {
        FormField merchantNameField;
        if (receipt.Fields.TryGetValue("MerchantName", out merchantNameField))
        {
            if (merchantNameField.Value.ValueType == FieldValueType.String)
            {
                string merchantName = merchantNameField.Value.AsString();

                Console.WriteLine($"Merchant Name: '{merchantName}', with confidence {merchantNameField.Confidence}");
            }
        }

        FormField transactionDateField;
        if (receipt.Fields.TryGetValue("TransactionDate", out transactionDateField))
        {
            if (transactionDateField.Value.ValueType == FieldValueType.Date)
            {
                DateTime transactionDate = transactionDateField.Value.AsDate();

                Console.WriteLine($"Transaction Date: '{transactionDate}', with confidence {transactionDateField.Confidence}");
            }
        }

        FormField itemsField;
        if (receipt.Fields.TryGetValue("Items", out itemsField))
        {
            if (itemsField.Value.ValueType == FieldValueType.List)
            {
                foreach (FormField itemField in itemsField.Value.AsList())
                {
                    Console.WriteLine("Item:");

                    if (itemField.Value.ValueType == FieldValueType.Dictionary)
                    {
                        IReadOnlyDictionary<string, FormField> itemFields = itemField.Value.AsDictionary();

                        FormField itemNameField;
                        if (itemFields.TryGetValue("Name", out itemNameField))
                        {
                            if (itemNameField.Value.ValueType == FieldValueType.String)
                            {
                                string itemName = itemNameField.Value.AsString();

                                Console.WriteLine($"    Name: '{itemName}', with confidence {itemNameField.Confidence}");
                            }
                        }

                        FormField itemTotalPriceField;
                        if (itemFields.TryGetValue("TotalPrice", out itemTotalPriceField))
                        {
                            if (itemTotalPriceField.Value.ValueType == FieldValueType.Float)
                            {
                                float itemTotalPrice = itemTotalPriceField.Value.AsFloat();

                                Console.WriteLine($"    Total Price: '{itemTotalPrice}', with confidence {itemTotalPriceField.Confidence}");
                            }
                        }
                    }
                }
            }
        }

        FormField totalField;
        if (receipt.Fields.TryGetValue("Total", out totalField))
        {
            if (totalField.Value.ValueType == FieldValueType.Float)
            {
                float total = totalField.Value.AsFloat();

                Console.WriteLine($"Total: '{total}', with confidence '{totalField.Confidence}'");
            }
        }
    }
}
```

Para executar esse método, você precisará chamá-lo de `Main`. 

```csharp
static void Main(string[] args)
{
    var analyzeReceipts = RecognizeReceipts();
    Task.WaitAll(analyzeReceipts);
}
```

### <a name="output"></a>Saída 

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
```

## <a name="train-a-custom-model"></a>Treinar um modelo personalizado

Esta seção demonstra como treinar um modelo com os próprios dados. Um modelo treinado pode gerar dados estruturados que incluem as relações de chave/valor no documento de formulário original. Depois de treinar no modelo, você pode testá-lo, treinar novamente e, por fim, usá-lo para extrair, de forma confiável, dados de mais formulários de acordo com suas necessidades.

> [!NOTE]
> Você também pode treinar modelos com uma interface gráfica do usuário, como a [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md).

### <a name="authenticate-the-training-client"></a>Autenticar o cliente de treinamento

Embaixo de `AuthenticateClient`, crie um método chamado `AuthenticateTrainingClient`. Você usará isso em tarefas futuras para treinar modelos personalizados. Esse método usa o objeto `AzureKeyCredential` (como `AuthenticateClient`), portanto, se necessário, você pode atualizar a chave de API sem criar objetos de cliente.

```csharp
static private FormTrainingClient AuthenticateTrainingClient()
{
    string endpoint = "https://formre-ga-sdk-testing.cognitiveservices.azure.com/";
    string apiKey = "<replace-with-your-form-recognizer-key-here>";
    var credential = new AzureKeyCredential(apiKey);
    var trainingClient = new FormTrainingClient(new Uri(endpoint), credential);
    return trainingClient;
}
```

### <a name="train-a-model-without-labels"></a>Treinar um modelo sem rótulos

Treine modelos personalizados para reconhecer todos os campos e valores encontrados nos formulários personalizados sem rotular manualmente os documentos de treinamento. O método a seguir treina um modelo em um especificado conjunto de documentos e imprime o status do modelo no console. O objeto `CustomFormModel` retornado contém informações sobre os tipos de formulários que o modelo pode reconhecer e os campos que ele pode extrair de cada tipo de formulário. O bloco de código a seguir imprime essas informações no console.

```csharp
static async Task TrainCustomModelNoLabels()
{
    var trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    var trainingClient = AuthenticateTrainingClient();
    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingDataUrl), useTrainingLabels: false)
        .WaitForCompletionAsync();
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Training model started on: {model.TrainingStartedOn}");
    Console.WriteLine($"    Training model completed on: {model.TrainingCompletedOn}");

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
}
```

Para executar esse método, você precisará chamá-lo de `Main`. 

```csharp
static void Main(string[] args)
{
    var trainCustomModel = TrainCustomModelNoLabels();
    Task.WaitAll(trainCustomModel);
}
```

### <a name="output"></a>Saída

Essa reposta foi truncada para facilitar a leitura.

```console
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    ... 
Custom Model Info:
    Model Id: 95035721-f19d-40eb-8820-0c806b42798b
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:50 PM +00:00
Submodel Form Type: form-95035721-f19d-40eb-8820-0c806b42798b
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ... 
Custom Model Info:
    Model Id: e7a1181b-1fb7-40be-bfbe-1ee154183633
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:52 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    FieldName: field-5, FieldLabel: Details
    FieldName: field-6, FieldLabel: Email:
    FieldName: field-7, FieldLabel: Hero Limited
    FieldName: field-8, FieldLabel: Name:
    FieldName: field-9, FieldLabel: Phone:
    ...
```

### <a name="train-a-model-with-labels"></a>Treinar um modelo com rótulos

Você também pode treinar modelos personalizados rotulando manualmente os documentos de treinamento. O treinamento com rótulos leva a um melhor desempenho em alguns cenários. Para o treinamento com rótulos, você precisará ter arquivos de informações de rótulo especiais (`\<filename\>.pdf.labels.json`) no contêiner do Armazenamento de Blobs junto com os documentos de treinamento. A [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md) fornece uma interface do usuário para ajudar você a criar esses arquivos de rótulo. Depois de obtê-los, chame o método `StartTrainingAsync` com o parâmetro `uselabels` definido como `true`. O `CustomFormModel` retornado indica os campos que o modelo pode extrair, juntamente com a precisão estimada em cada campo. O bloco de código a seguir imprime essas informações no console.

```csharp
static async Task TrainCustomModelWithLabels()
{
    var trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    var trainingClient = AuthenticateTrainingClient();

    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingDataUrl), useTrainingLabels: true)
        .WaitForCompletionAsync();
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Training model started on: {model.TrainingStartedOn}");
    Console.WriteLine($"    Training model completed on: {model.TrainingCompletedOn}");

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
}
```

Para executar esse método, você precisará chamá-lo de `Main`. 

```csharp
static void Main(string[] args)
{
    var trainCustomModel = TrainCustomModelWithLabels();
    Task.WaitAll(trainCustomModel);
}
```

### <a name="output"></a>Saída

Essa reposta foi truncada para facilitar a leitura.

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ... 
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: 63c013e3-1cab-43eb-84b0-f4b20cb9214c
    Model Status: Ready
    Training model started on: 8/24/2020 6:42:54 PM +00:00
    Training model completed on: 8/24/2020 6:43:01 PM +00:00
Submodel Form Type: form-63c013e3-1cab-43eb-84b0-f4b20cb9214c
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    ... 
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Esta seção demonstra como extrair informações de chave/valor e outro conteúdo dos tipos de formulário personalizados, usando modelos treinados com os próprios formulários.

> [!IMPORTANT]
> Para implementar esse cenário, você já precisará ter treinado um modelo para passar a ID para o método abaixo.

Você usará o método `StartRecognizeCustomFormsFromUri`. O valor retornado é uma coleção de objetos `RecognizedForm`: um para cada página no documento enviado. O código a seguir imprime os resultados da análise no console. Ele imprime cada campo reconhecido e o valor correspondente, juntamente com uma pontuação de confiança.

```csharp
static async Task RecognizeContentCustomModel()
{
    // Use the custom model ID returned in the previous example.
    string modelId = "<modelId>";
    var invoiceUri = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";
    var recognizeClient = AuthenticateClient();

    RecognizedFormCollection forms = await recognizeClient
    .StartRecognizeCustomFormsFromUri(modelId, new Uri(invoiceUri))
    .WaitForCompletionAsync();
    foreach (RecognizedForm form in forms)
    {
        Console.WriteLine($"Form of type: {form.FormType}");
        foreach (FormField field in form.Fields.Values)
        {
            Console.WriteLine($"Field '{field.Name}: ");

            if (field.LabelData != null)
            {
                Console.WriteLine($"    Label: '{field.LabelData.Text}");
            }

            Console.WriteLine($"    Value: '{field.ValueData.Text}");
            Console.WriteLine($"    Confidence: '{field.Confidence}");
        }
        Console.WriteLine("Table data:");
        foreach (FormPage page in form.Pages.Values)
        {
            for (int i = 0; i < page.Tables.Count; i++)
            {
                FormTable table = page.Tables[i];
                Console.WriteLine($"Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");
                foreach (FormTableCell cell in table.Cells)
                {
                    Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) contains {(cell.IsHeader ? "header" : "text")}: '{cell.Text}'");
                }
            }
        }
    }
}
```

Para executar esse método, você precisará chamá-lo de `Main`. 

```csharp
static void Main(string[] args)
{
    var recognizeContentCustomModel = RecognizeContentCustomModel();
    Task.WaitAll(recognizeContentCustomModel);
}
```

### <a name="output"></a>Saída

Essa reposta foi truncada para facilitar a leitura.

```console
Custom Model Info:
    Model Id: 9b0108ee-65c8-450e-b527-bb309d054fc4
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:32 PM +00:00
Submodel Form Type: form-9b0108ee-65c8-450e-b527-bb309d054fc4
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ...
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    ...

Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ...
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: dc115156-ce0e-4202-bbe4-7426e7bee756
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:41 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    ... 
Form of type: custom:form
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '$56,651.49
    Confidence: '0.249
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: 'PT
    Confidence: '0.245
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '99243
    Confidence: '0.114
   ...
```

## <a name="manage-custom-models"></a>Gerenciar modelos personalizados

Esta seção demonstra como gerenciar os modelos personalizados armazenados na sua conta. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verificar o número de modelos na conta do recurso do FormRecognizer

O bloco de código a seguir verifica quantos modelos você salvou na sua conta do Reconhecimento de Formulários e os compara com o limite da conta.

```csharp
static void CheckNumberOfModels()
{
    var trainingClient = AuthenticateTrainingClient();
    AccountProperties accountProperties = trainingClient.GetAccountProperties();
    Console.WriteLine($"Account has {accountProperties.CustomModelCount} models.");
    Console.WriteLine($"It can have at most {accountProperties.CustomModelLimit} models.");
}
```

Para executar esse método, você precisará chamá-lo de `Main`. 

```csharp
static void Main(string[] args)
{
    CheckNumberOfModels();
}
```

### <a name="output"></a>Saída 

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Listar os modelos atualmente armazenados na conta do recurso

O bloco de código a seguir lista os modelos atuais na sua conta e imprime os detalhes no console.

```csharp
static void ListAllModels()
{
    var trainingClient = AuthenticateTrainingClient();

    Pageable<CustomFormModelInfo> models = trainingClient.GetCustomModels();

    foreach (CustomFormModelInfo modelInfo in models)
    {
        Console.WriteLine($"Custom Model Info:");
        Console.WriteLine($"    Model Id: {modelInfo.ModelId}");
        Console.WriteLine($"    Model Status: {modelInfo.Status}");
        Console.WriteLine($"    Training model started on: {modelInfo.TrainingStartedOn}");
        Console.WriteLine($"    Training model completed on: {modelInfo.TrainingCompletedOn}");
    }   
}
```

Para executar esse método, você precisará chamá-lo de `Main`. 

```csharp
static void Main(string[] args)
{
    ListAllModels();
}
```

### <a name="output"></a>Saída 

Essa reposta foi truncada para facilitar a leitura.

```console
Custom Model Info:
    Model Id: 05932d5a-a2f8-4030-a2ef-4e5ed7112515
    Model Status: Creating
    Training model started on: 8/24/2020 7:35:02 PM +00:00
    Training model completed on: 8/24/2020 7:35:02 PM +00:00
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Custom Model Info:
    Model Id: 3303e9de-6cec-4dfb-9e68-36510a6ecbb2
    Model Status: Ready
    Training model started on: 8/24/2020 7:29:27 PM +00:00
    Training model completed on: 8/24/2020 7:29:36 PM +00:00
```

### <a name="get-a-specific-model-using-the-models-id"></a>Obter um modelo específico usando a ID do modelo

O bloco de código a seguir treina um novo modelo (assim como na seção [Treinar um modelo](#train-a-model-without-labels)) e recupera uma segunda referência a ele usando a respectiva ID.

```csharp
static void GetModelById()
{
    // Use the custom model ID returned in the previous example.
    string modelId = "<modelId>";
    var trainingClient = AuthenticateTrainingClient();
    CustomFormModel modelCopy = trainingClient.GetCustomModel(modelId);

    Console.WriteLine($"Custom Model {modelCopy.ModelId} recognizes the following form types:");

    foreach (CustomFormSubmodel submodel in modelCopy.Submodels)
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
}    
```

Para executar esse método, você precisará chamá-lo de `Main`. 

```csharp
static void Main(string[] args)
{
    GetModelById();
}
```

### <a name="output"></a>Saída 

Essa reposta foi truncada para facilitar a leitura.

```console
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Submodel Form Type: form-150828c4-2eb2-487e-a728-60d5d504bd16
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    FieldName: PhoneNumber
    FieldName: PurchaseOrderNumber
    FieldName: Quantity
    FieldName: Signature
    FieldName: Subtotal
    FieldName: Tax
    FieldName: Total
    FieldName: VendorName
    FieldName: Website
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Excluir um modelo da conta do recurso

Exclua também um modelo da sua conta referenciando a ID.

```csharp
static void DeleteModel()
{
    // Use the custom model ID returned in the previous example.
    string modelId = "<modelId>";
    var trainingClient = AuthenticateTrainingClient();
    trainingClient.DeleteModel(modelId);
} 
```

## <a name="run-the-application"></a>Executar o aplicativo

Você pode executar o aplicativo a qualquer momento com várias funções sobre as quais você leu neste início rápido com este comando:

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

```csharp
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
