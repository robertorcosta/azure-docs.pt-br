---
title: 'Início Rápido: Detectar anomalias em dados de série temporal usando a biblioteca de clientes do Detector de Anomalias para .NET'
titleSuffix: Azure Cognitive Services
description: Este início rápido mostra como usar a API do Detector de Anomalias para detectar anormalidades em sua série de dados como um lote ou em dados de streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: fdb35edc35e07ed4ee718281942565a8f1d061d4
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402697"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Início Rápido: biblioteca de clientes do Detector de Anomalias para .NET

Introdução à biblioteca de clientes Detector de Anomalias para .NET. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. O serviço Detector de Anomalias permite localizar anormalidades em seus dados de série temporal usando automaticamente os modelos de melhor ajuste, independentemente do setor, cenário ou volume de dados.

Use a biblioteca de cliente do Detector de Anomalias para .NET para:

* Detectar anomalias em todo o seu conjunto de dados de série temporal, como uma solicitação em lote
* Detectar o status de anomalias do ponto de dados mais recente em sua série temporal

[Documentação de referência da biblioteca](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [Localize o código no GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* Versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Um ponto de extremidade e uma chave do Detector de Anomalias

## <a name="setting-up"></a>Configurando

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso do Detector de Anomalias

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-net-core-application"></a>Criar um aplicativo .NET Core

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `anomaly-detector-quickstart`. Este comando cria um projeto "Olá, Mundo" simples com um único arquivo de origem C#: *Program.cs*. 

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
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

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Dentro do diretório do aplicativo, instale a biblioteca de clientes do Detector de Anomalias para .NET com o seguinte comando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

No diretório do projeto, abra o arquivo *program.cs* e adicione o seguinte usando `directives`:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

No método `main()` do aplicativo, crie variáveis para o local do Azure do seu recurso e a chave como uma variável de ambiente. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Modelo de objeto

O cliente do Detector de Anomalias é um objeto [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) que se autentica no Azure usando [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), que contém sua chave. O cliente fornece dois métodos de detecção de anomalias: Em um conjunto de dados inteiro usando [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)e no ponto de dados mais recente usando [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Os dados de série temporal são enviados como uma série de [Pontos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) em um objeto [Solicitação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request). O objeto `Request` contém propriedades para descrever os dados ([Granularidade](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity), por exemplo) e os parâmetros para a detecção de anomalias. 

A resposta do Detector de Anomalias é um objeto [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) ou [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse), dependendo do método usado. 

## <a name="code-examples"></a>Exemplos de código

Esses snippets de códigos mostram como fazer o seguinte com a biblioteca de clientes do Detector de Anomalias para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Carregar um conjunto de dados de série temporal de um arquivo](#load-time-series-data-from-a-file)
* [Detectar anomalias no conjunto de dados inteiro](#detect-anomalies-in-the-entire-data-set) 
* [Detectar o status de anomalias do último ponto de dados](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Em um novo método, instancie um cliente com o ponto de extremidade e a chave. Crie um objeto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) com a sua chave e use-o com o ponto de extremidade para criar um objeto [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview). 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
## <a name="load-time-series-data-from-a-file"></a>Carregar dados de série temporal de um arquivo

Baixe os dados de exemplo deste guia de início rápido no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Em seu navegador, clique com o botão direito do mouse em **Raw**.
2. Clique em **Salvar link como**.
3. Salve o arquivo no diretório do aplicativo como um arquivo .csv.

Esses dados de série temporal são formatados como um arquivo .csv e enviados à API do Detector de Anomalias.

Crie um novo método para ler os dados da série temporal e adicione-o a um objeto [Solicitação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview). Chame `File.ReadAllLines()` com o caminho do arquivo, crie uma lista de objetos [Ponto](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) e retire os caracteres de nova linha. Extraia os valores, separe o carimbo de data do seu valor numérico e adicione-os a um novo objeto `Point`. 

Faça um objeto `Request` com a série de pontos e faça `Granularity.Daily` para a [Granularidade](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (ou periodicidade) dos pontos de dados.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detectar anomalias no conjunto de dados inteiro 

Crie um método para chamar o método [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) do cliente com o objeto `Request` e aguarde a resposta como um objeto [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview). Se a série temporal contiver quaisquer anomalias, itere por meio dos valores [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) da resposta e imprima os que forem `true`. Esses valores correspondem ao índice de pontos de dados anômalos, caso algum seja encontrado.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do último ponto de dados

Crie um método para chamar o método [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) do cliente com o objeto `Request` e aguarde a resposta como um objeto [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview). Verifique o atributo [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) da resposta para determinar se o último ponto de dados enviado foi uma anomalia ou não. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `dotnet run` do seu próprio diretório de aplicativo.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
