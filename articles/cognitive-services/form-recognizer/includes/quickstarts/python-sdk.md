---
title: 'Início Rápido: Biblioteca de clientes do Reconhecimento de Formulários para Python'
description: Neste início rápido, comece a usar a biblioteca de clientes do Reconhecimento de Formulários para Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/15/2020
ms.author: pafarley
ms.openlocfilehash: 3300bd53be36e34ff32267605352d6f80923724e
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86277714"
---
[Documentação de referência](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [Pacote (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* Um blob do Armazenamento do Azure contendo um conjunto de dados de treinamento. Confira [Criar um conjunto de dados de treinamento para um modelo personalizado](../../build-training-data-set.md) para obter dicas e opções para compilar o conjunto de dados de treinamento. Para este guia de início rápido, você pode usar os arquivos na pasta **Train** do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451).
* [Python 2.7, 3.5 ou posterior](https://www.python.org/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-form-recognizer-azure-resource"></a>Criar um recurso do Azure do Reconhecimento de Formulários

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Criar variáveis de ambiente

[!INCLUDE [environment-variables](../environment-variables.md)]


### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um novo aplicativo Python em seu IDE ou editor preferido. Depois, importe as bibliotecas a seguir.

```python
import os
import azure.ai.formrecognizer
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import ResourceNotFoundError
```

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso. Se você tiver criado a variável de ambiente depois de iniciar o aplicativo, precisará fechar e abrir novamente o editor, o IDE ou o shell para acessar a variável.

```python
endpoint = os.environ["FORM_RECOGNIZER_ENDPOINT"]
key = os.environ["FORM_RECOGNIZER_KEY"]
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Depois de instalar o Python, você pode instalar a biblioteca de clientes com:

```console
pip install azure-ai-formrecognizer
```

<!-- 
tbd object model
-->

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como executar as seguintes tarefas com a biblioteca de clientes do Reconhecimento de Formulários para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer o conteúdo do formulário](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Treinar um modelo personalizado](#train-a-custom-model)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerenciar seus modelos personalizados](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Autenticar o cliente

Aqui, você autenticará os objetos de cliente usando as variáveis de assinatura definidas acima. Você usará um objeto **AzureKeyCredential**, para, se necessário, atualizar a chave de API sem criar objetos de cliente.

```python
form_recognizer_client = FormRecognizerClient(endpoint=endpoint, credential=AzureKeyCredential(key))

form_training_client = FormTrainingClient(endpoint, AzureKeyCredential(key))
```

## <a name="define-variables"></a>Definir variáveis

> [!NOTE]
> Os snippets de código deste guia usam formulários remotos acessados por URLs. Caso deseje processar documentos de formulário local, confira os métodos relacionados na [documentação de referência](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) e nas [amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

Você também precisará adicionar referências às URLs para os dados de treinamento e teste.
* Para recuperar a URL de SAS para os dados de treinamento do modelo personalizado, abra o Gerenciador de Armazenamento do Microsoft Azure, clique com o botão direito do mouse no contêiner e selecione **Obter Assinatura de Acesso Compartilhado**. Verifique se as permissões de **Leitura** e **Lista** estão marcadas e clique em **Criar**. Em seguida, copie o valor na seção **URL**. Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Para obter uma URL de um formulário para teste, use as etapas acima para obter a URL de SAS de um documento individual no armazenamento de blobs. Ou então, selecione a URL de um documento localizado em outro lugar.
* Use o método acima para obter a URL de uma imagem de recibo também ou use a URL da imagem de exemplo fornecida.

```python
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"
formUrl = "<SAS-URL-of-a-form-in-blob-storage>"
receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png"
```

## <a name="recognize-form-content"></a>Reconhecer o conteúdo do formulário

Use o Reconhecimento de Formulários para reconhecer tabelas, linhas e palavras em documentos, sem a necessidade de treinar um modelo.

Para reconhecer o conteúdo de um arquivo em uma URL especificada, use o método **begin_recognize_content**.

```Python
poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
contents = poller.result()
```

O valor retornado é uma coleção de objetos **FormPage**: um para cada página no documento enviado. O código a seguir itera nesses objetos e imprime os pares de chave/valor extraídos e os dados da tabela.

```python
for idx, content in enumerate(contents):
    print("----Recognizing content from page #{}----".format(idx))
    print("Has width: {} and height: {}, measured with unit: {}".format(
        content.width,
        content.height,
        content.unit
    ))
    for table_idx, table in enumerate(content.tables):
        print("Table # {} has {} rows and {} columns".format(table_idx, table.row_count, table.column_count))
        for cell in table.cells:
            print("...Cell[{}][{}] has text '{}' within bounding box '{}'".format(
                cell.row_index,
                cell.column_index,
                cell.text,
                format_bounding_box(cell.bounding_box)
            ))
    for line_idx, line in enumerate(content.lines):
        print("Line # {} has word count '{}' and text '{}' within bounding box '{}'".format(
            line_idx,
            len(line.words),
            line.text,
            format_bounding_box(line.bounding_box)
        ))
    print("----------------------------------------")
```

O código acima usa uma função auxiliar `format_bounding_box` para simplificar as coordenadas de uma caixa delimitadora. Defina-o separadamente:

```python
def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])
```

## <a name="recognize-receipts"></a>Reconhecer recibos

Esta seção demonstra como reconhecer e extrair campos comuns de recibos dos EUA usando um modelo de recibo pré-treinado. Para reconhecer recibos de uma URL, use o método **begin_recognize_receipts_from_url**. 

```python
poller = form_recognizer_client.begin_recognize_receipts_from_url(receiptUrl)
receipts = poller.result()
```

O valor retornado é uma coleção de objetos **RecognizedReceipt**: um para cada página no documento enviado. O bloco de código a seguir imprime informações básicas do recibo no console.

```python
for idx, receipt in enumerate(receipts):
    print("--------Recognizing receipt #{}--------".format(idx))
    receipt_type = receipt.fields.get("ReceiptType")
    if receipt_type:
        print("Receipt Type: {} has confidence: {}".format(receipt_type.value, receipt_type.confidence))
    merchant_name = receipt.fields.get("MerchantName")
    if merchant_name:
        print("Merchant Name: {} has confidence: {}".format(merchant_name.value, merchant_name.confidence))
    transaction_date = receipt.fields.get("TransactionDate")
    if transaction_date:
        print("Transaction Date: {} has confidence: {}".format(transaction_date.value, transaction_date.confidence))
```

O próximo bloco de código itera em itens individuais detectados no recibo e imprime os detalhes no console.


```python
    print("Receipt items:")
    for idx, item in enumerate(receipt.fields.get("Items").value):
        print("...Item #{}".format(idx))
        item_name = item.value.get("Name")
        if item_name:
            print("......Item Name: {} has confidence: {}".format(item_name.value, item_name.confidence))
        item_quantity = item.value.get("Quantity")
        if item_quantity:
            print("......Item Quantity: {} has confidence: {}".format(item_quantity.value, item_quantity.confidence))
        item_price = item.value.get("Price")
        if item_price:
            print("......Individual Item Price: {} has confidence: {}".format(item_price.value, item_price.confidence))
        item_total_price = item.value.get("TotalPrice")
        if item_total_price:
            print("......Total Item Price: {} has confidence: {}".format(item_total_price.value, item_total_price.confidence))
```

Por fim, o último bloco de código imprime o restante dos principais detalhes do recibo.

```python
    subtotal = receipt.fields.get("Subtotal")
    if subtotal:
        print("Subtotal: {} has confidence: {}".format(subtotal.value, subtotal.confidence))
    tax = receipt.fields.get("Tax")
    if tax:
        print("Tax: {} has confidence: {}".format(tax.value, tax.confidence))
    tip = receipt.fields.get("Tip")
    if tip:
        print("Tip: {} has confidence: {}".format(tip.value, tip.confidence))
    total = receipt.fields.get("Total")
    if total:
        print("Total: {} has confidence: {}".format(total.value, total.confidence))
    print("--------------------------------------")
```


## <a name="train-a-custom-model"></a>Treinar um modelo personalizado

Esta seção demonstra como treinar um modelo com os próprios dados. Um modelo treinado pode gerar dados estruturados que incluem as relações de chave/valor no documento de formulário original. Depois de treinar no modelo, você pode testá-lo, treinar novamente e, por fim, usá-lo para extrair, de forma confiável, dados de mais formulários de acordo com suas necessidades.

> [!NOTE]
> Você também pode treinar modelos com uma interface gráfica do usuário, como a [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Treinar um modelo sem rótulos

Treine modelos personalizados para reconhecer todos os campos e valores encontrados em seus formulários personalizados sem rotular manualmente os documentos de treinamento.

O código a seguir usa o cliente de treinamento com a função **begin_training** para treinar um modelo em um conjunto especificado de documentos.

```python
poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=False)
model = poller.result()
```

O objeto **CustomFormSubmodel** retornado contém informações sobre os tipos de formulários que o modelo pode reconhecer e os campos que ele pode extrair de cada tipo de formulário. O bloco de código a seguir imprime essas informações no console.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.requested_on))
print("Last modified: {}".format(model.completed_on))

print("Recognized fields:")
# Looping through the submodels, which contains the fields they were trained on
for submodel in model.submodels:
    print("...The submodel has form type '{}'".format(submodel.form_type))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have label '{}'".format(
            name, field.label
        ))
```

### <a name="train-a-model-with-labels"></a>Treinar um modelo com rótulos

Você também pode treinar modelos personalizados rotulando manualmente os documentos de treinamento. O treinamento com rótulos leva a um melhor desempenho em alguns cenários. 

> [!IMPORTANT]
> Para o treinamento com rótulos, você precisará ter arquivos de informações de rótulo especiais ( *\<filename\>.pdf.labels.json*) no contêiner de armazenamento de blobs junto com os documentos de treinamento. A [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md) fornece uma interface do usuário para ajudar você a criar esses arquivos de rótulo. Depois de obtê-los, chame a função **begin_training** com o parâmetro *use_training_labels* definido como `true`.

```python
poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=True)
model = poller.result()
```

O **CustomFormSubmodel** retornado indica os campos que o modelo pode extrair, juntamente com a precisão estimada em cada campo. O bloco de código a seguir imprime essas informações no console.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.created_on))
print("Last modified: {}".format(model.last_modified))

print("Recognized fields:")
# looping through the submodels, which contains the fields they were trained on
# The labels are based on the ones you gave the training document.
for submodel in model.submodels:
    print("...The submodel with form type {} has accuracy '{}'".format(submodel.form_type, submodel.accuracy))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have name '{}' with an accuracy of {}".format(
            name, field.name, field.accuracy
        ))
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Esta seção demonstra como extrair informações de chave/valor e outro conteúdo dos tipos de formulário personalizados, usando modelos treinados com os próprios formulários.

> [!IMPORTANT]
> Para implementar esse cenário, você já precisará ter treinado um modelo para passar a ID para o método abaixo. Confira a seção [Treinar um modelo](#train-a-model-without-labels).

Você usará o método **begin_recognize_custom_forms_from_url**. O valor retornado é uma coleção de objetos **RecognizedForm**: um para cada página no documento enviado.

```python
# Make sure your form's type is included in the list of form types the custom model can recognize
poller = form_recognizer_client.begin_recognize_custom_forms_from_url(
    model_id=model.model_id, form_url=formUrl)
forms = poller.result()
```

O código a seguir imprime os resultados da análise no console. Ele imprime cada campo reconhecido e o valor correspondente, juntamente com uma pontuação de confiança.

```python
for idx, form in enumerate(forms):
    print("--------Recognizing Form #{}--------".format(idx))
    print("Form {} has type {}".format(idx, form.form_type))
    for name, field in form.fields.items():
        # each field is of type FormField
        # The value of the field can also be a FormField, or a list of FormFields
        # In our sample, it is just a FormField.
        print("...Field '{}' has value '{}' with a confidence score of {}".format(
            name, field.value, field.confidence
        ))
        # label data is populated if you are using a model trained with unlabeled data, since the service needs to make predictions for
        # labels if not explicitly given to it.
        if field.label_data:
            print("...Field '{}' has label '{}' with a confidence score of {}".format(
                name,
                field.label_data.text,
                field.confidence
            ))
    print("-----------------------------------")
```

## <a name="manage-your-custom-models"></a>Gerenciar seus modelos personalizados

Esta seção demonstra como gerenciar os modelos personalizados armazenados na sua conta. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verificar o número de modelos na conta do recurso do FormRecognizer

O bloco de código a seguir verifica quantos modelos você salvou na sua conta do Reconhecimento de Formulários e os compara com o limite da conta.

```python
# First, we see how many custom models we have, and what our limit is
account_properties = form_training_client.get_account_properties()
print("Our account has {} custom models, and we can have at most {} custom models".format(
    account_properties.custom_model_count, account_properties.custom_model_limit
))
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Listar os modelos atualmente armazenados na conta do recurso

O bloco de código a seguir lista os modelos atuais na sua conta e imprime os detalhes no console. Ele também salva uma referência ao primeiro modelo.

```python
# Next, we get a paged list of all of our custom models
custom_models = form_training_client.list_custom_models()

print("We have models with the following ids:")

# Let's pull out the first model
first_model = next(custom_models)
print(first_model.model_id)
for model in custom_models:
    print(model.model_id)
```

### <a name="get-a-specific-model-using-the-models-id"></a>Obter um modelo específico usando a ID do modelo

O bloco de código a seguir usa a ID do modelo salva na seção anterior e a usa para recuperar detalhes sobre o modelo.

```python
# Now we'll get the first custom model in the paged list
custom_model = form_training_client.get_custom_model(model_id=first_model.model_id)
print("Model ID: {}".format(custom_model.model_id))
print("Status: {}".format(custom_model.status))
print("Created on: {}".format(custom_model.requested_on))
print("Last modified: {}".format(custom_model.completed_on))
```

### <a name="delete-a-model-from-the-resource-account"></a>Excluir um modelo da conta do recurso

Exclua também um modelo da sua conta referenciando a ID. Esse código exclui o modelo usado na seção anterior.

```python
form_training_client.delete_model(model_id=custom_model.model_id)

# Confirm deletion:
try:
    form_training_client.get_custom_model(model_id=custom_model.model_id)
except ResourceNotFoundError:
    print("Successfully deleted model with id {}".format(custom_model.model_id))
}
```

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `python` no seu arquivo de início rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Solução de problemas

### <a name="general"></a>Geral

A biblioteca de clientes do Reconhecimento de Formulários vai gerar as exceções definidas no [Azure Core](https://aka.ms/azsdk-python-azure-core).

## <a name="logging"></a>Registro em log

Essa biblioteca usa a [biblioteca de log padrão](https://docs.python.org/3/library/logging.html) para o log. As informações básicas sobre as sessões HTTP (URLs, cabeçalhos etc.) são registradas em log no nível de INFO.

O log detalhado no nível de DEBUG, incluindo corpos de solicitação/resposta e cabeçalhos não redigidos, pode ser habilitado em um cliente com o argumento de palavra-chave `logging_enable`:

```python
import sys
import logging
from azure.ai.formrecognizer import FormRecognizerClient
from azure.core.credentials import AzureKeyCredential

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "https://<my-custom-subdomain>.cognitiveservices.azure.com/"
credential = AzureKeyCredential("<api_key>")

# This client will log detailed information about its HTTP sessions, at DEBUG level
form_recognizer_client = FormRecognizerClient(endpoint, credential, logging_enable=True)
```

Da mesma forma, `logging_enable` pode habilitar o log detalhado para uma operação individual, mesmo quando ela não está habilitada para o cliente:

```python
poller = form_recognizer_client.begin_recognize_receipts(receipt, logging_enable=True)
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou a biblioteca de clientes Python do Reconhecimento de Formulários para treinar modelos personalizado e analisar formulários de diferentes maneiras. Em seguida, aprenda dicas para criar um conjunto de dados de treinamento melhor e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de treinamento](../../build-training-data-set.md)

* [O que é o Reconhecimento de Formulários?](../../overview.md)
* Encontre o código de exemplo deste guia (e outros) no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).
