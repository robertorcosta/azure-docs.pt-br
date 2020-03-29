---
title: Usar o Docker Compose para implantar vários contêineres
titleSuffix: Azure Cognitive Services
description: Aprenda a implantar vários contêineres de Serviços Cognitivos. Este artigo mostra como orquestrar várias imagens de contêiner Docker usando docker compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: bfbaa03469ee04ff900a215aadd8c814efcba761
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037528"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Usar o Docker Compose para implantar vários contêineres

Este artigo mostra como implantar vários contêineres do Azure Cognitive Services. Especificamente, você aprenderá a usar o Docker Compose para orquestrar várias imagens de contêiner Docker.

> [Docker Compose](https://docs.docker.com/compose/) é uma ferramenta para definir e executar aplicativos Docker de vários contêineres. Em Compose, você usa um arquivo YAML para configurar os serviços do seu aplicativo. Em seguida, você cria e inicia todos os serviços a partir de sua configuração executando um único comando.

Pode ser útil para orquestrar várias imagens de contêiner em um único computador host. Neste artigo, reuniremos os recipientes Read and Form Recognizer.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que devem ser instaladas e executadas localmente:

* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Docker Engine](https://www.docker.com/products/docker-engine). Confirme se o Cli Docker funciona em uma janela de console.
* Um recurso do Azure com o tipo de preço correto. Apenas os seguintes níveis de preços funcionam com este contêiner:
  * **Recurso de Visão computacional** apenas com nível de preços F0 ou Standard.
  * **Recurso De reconhecimento** de formulário apenas com nível de preços F0 ou Standard.
  * O recurso **Serviços Cognitivos** com o tipo de preço S0.

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao Registro de contêiner

Preencha e envie o [formulário de solicitação de solicitação de contêineres de fala de serviços cognitivos](https://aka.ms/speechcontainerspreview/). 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Arquivo Docker Compose

O arquivo YAML define todos os serviços a serem implantados. Esses serviços dependem `DockerFile` de uma imagem de contêiner existente ou de um contêiner existente. Neste caso, usaremos duas imagens de pré-visualização. Copie e cole o seguinte arquivo YAML e *salve-o como docker-compose.yaml*. Forneça os valores **apropriados de apikey,** **faturamento**e **EndpointUri** no arquivo.

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-read"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Crie os diretórios na máquina host especificadas o nó **volumes.** Essa abordagem é necessária porque os diretórios devem existir antes de tentar montar uma imagem usando amarras de volume.

## <a name="start-the-configured-docker-compose-services"></a>Inicie os serviços de Composição do Docker configurados

Um arquivo Docker Compose permite o gerenciamento de todas as etapas do ciclo de vida de um serviço definido: serviços de início, parada e reconstrução; visualização do status do serviço; e log streaming. Abra uma interface de linha de comando a partir do diretório do projeto (onde o arquivo docker-compose.yaml está localizado).

> [!NOTE]
> Para evitar erros, certifique-se de que a máquina host compartilhe corretamente as unidades com o Docker Engine. Por exemplo, se *E:\publicpreview* for usado como um diretório no arquivo *docker-compose.yaml,* compartilhe a unidade **E** com o Docker.

A partir da interface de linha de comando, execute o seguinte comando para iniciar (ou reiniciar) todos os serviços definidos no arquivo *docker-compose.yaml:*

```console
docker-compose up
```

A primeira vez que o Docker executa o comando **docker-compose up** usando essa configuração, ele puxa as imagens configuradas o nó **de serviços** e, em seguida, baixa e monta:

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-read:)...
latest: Pulling from microsoft/cognitive-services-read
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Depois que as imagens são baixadas, os serviços de imagem são iniciados:

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>Verifique a disponibilidade do serviço

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Veja a seguir um exemplo de saída:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-read              latest
```

### <a name="test-containers"></a>Recipientes de teste

Abra um navegador na máquina host e vá para **localhost** usando a porta especificada a http://localhost:5021/swagger/index.htmlpartir do arquivo *docker-compose.yaml,* como . Por exemplo, você pode usar o recurso **Try It** na API para testar o ponto final do Reconhecimento de Formulário. Ambas as páginas de swagger de contêineres devem estar disponíveis e testá-los.

![Recipiente de reconhecimento de formulário](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Contêineres de Serviços Cognitivos](../cognitive-services-container-support.md)
