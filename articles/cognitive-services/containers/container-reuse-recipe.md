---
title: Receitas para recipientes Docker
titleSuffix: Azure Cognitive Services
description: Aprenda a construir, testar e armazenar contêineres com algumas ou todas as configurações para implantação e reutilização.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 97342f1dd4f6ce343626ba6c294f09dabe3db5c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717213"
---
# <a name="create-containers-for-reuse"></a>Criar contêineres para reutilização

Use essas receitas de recipientes para criar recipientes de serviços cognitivos que podem ser reutilizados. Os contêineres podem ser construídos com algumas ou todas as configurações de modo que _não_ sejam necessários quando o contêiner é iniciado.

Uma vez que você tenha esta nova camada de recipiente (com configurações), e você testou-o localmente, você pode armazenar o recipiente em um registro de contêiner. Quando o contêiner for iniciado, ele só precisará das configurações que não estão armazenadas no recipiente no momento. O contêiner de registro privado fornece espaço de configuração para você passar essas configurações.

## <a name="docker-run-syntax"></a>Docker executar sintaxe

Qualquer `docker run` exemplo neste documento assume um `^` console Windows com um caractere de continuação de linha. Considere o seguinte para seu próprio uso:

* não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.
* Se você estiver usando um sistema operacional diferente do Windows, ou um console diferente do console Windows, use o console/terminal correto, a sintaxe de pastas para montagens e o caractere de continuação da linha para o seu console e sistema.  Como o contêiner Cognitive Services é um sistema operacional Linux, o suporte de destino usa uma sintaxe de pasta no estilo Linux.
* `docker run`exemplos usam o diretório `c:` fora da unidade para evitar quaisquer conflitos de permissão no Windows. Se você precisar usar um diretório específico como o diretório de entrada, talvez seja necessário conceder ao Docker permissão de serviço.

## <a name="store-no-configuration-settings-in-image"></a>Não armazene configurações na imagem

Os `docker run` comandos de exemplo de cada serviço não armazenam nenhuma configuração no contêiner. Quando você inicia o contêiner a partir de um console ou serviço de registro, essas configurações precisam ser aprovadas. O contêiner de registro privado fornece espaço de configuração para você passar essas configurações.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Receita de reutilização: armazene todas as configurações com o recipiente

Para armazenar todas as configurações, `Dockerfile` crie um com essas configurações.

Problemas com esta abordagem:

* O novo contêiner tem um nome e uma etiqueta separados do recipiente original.
* Para alterar essas configurações, você terá que alterar os valores do Arquivo Docker, reconstruir a imagem e republicar para o seu registro.
* Se alguém tiver acesso ao seu registro de contêineres ou ao seu host local, ele pode executar o contêiner e usar os pontos finais do Serviço Cognitivo.
* Se o seu Serviço Cognitivo não precisar de suportes de entrada, não adicione as `COPY` linhas ao seu arquivo Docker.

Crie o Arquivo Docker, puxando do contêiner de Serviços Cognitivos existentes que você deseja usar e, em seguida, use comandos docker no Arquivo Docker para definir ou puxar as informações que o contêiner precisa.

Neste exemplo:

* Define o ponto final `{BILLING_ENDPOINT}` de cobrança, a `ENV`partir da chave de ambiente do host usando .
* Define a aPI-key `{ENDPOINT_KEY}` de cobrança, a partir da chave de ambiente do host usando 'ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Receita de reutilização: configurações de faturamento da loja com contêiner

Este exemplo mostra como construir o contêiner de sentimento do Text Analytics a partir de um arquivo Docker.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Construa e execute o contêiner [localmente](#how-to-use-container-on-your-local-host) ou a partir do seu [contêiner de registro privado,](#how-to-add-container-to-private-registry) conforme necessário.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Receita de reutilização: faturamento da loja e configurações de montagem com recipiente

Este exemplo mostra como usar a compreensão do idioma, salvando o faturamento e os modelos do Arquivo Docker.

* Copia o arquivo de modelo 'Compreensão de idiomas' (LUIS) do sistema de arquivos do host usando `COPY`.
* O contêiner LUIS suporta mais de um modelo. Se todos os modelos estiverem armazenados `COPY` na mesma pasta, todos vocês precisarão de uma declaração.
* Execute o arquivo docker do pai relativo do diretório de entrada do modelo. Para o exemplo a `docker build` `docker run` seguir, execute os `/input`comandos e comandos do pai relativo de . O `/input` primeiro `COPY` no comando é o diretório do computador host. O `/input` segundo é o diretório do contêiner.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Construa e execute o contêiner [localmente](#how-to-use-container-on-your-local-host) ou a partir do seu [contêiner de registro privado,](#how-to-add-container-to-private-registry) conforme necessário.

## <a name="how-to-use-container-on-your-local-host"></a>Como usar o contêiner em seu host local

Para construir o arquivo `<your-image-name>` Docker, substitua-o pelo novo nome da imagem e use:

```console
docker build -t <your-image-name> .
```

Para executar a imagem e removê-la`--rm`quando o recipiente parar ( ):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Como adicionar contêiner ao registro privado

Siga estas etapas para usar o Arquivo Docker e coloque a nova imagem no registro de contêiner privado.  

1. Crie `Dockerfile` um com o texto da receita de reutilização. A `Dockerfile` não tem uma extensão.

1. Substitua quaisquer valores nos suportes angulares por seus próprios valores.

1. Construa o arquivo em uma imagem na linha de comando ou terminal, usando o seguinte comando. Substitua os valores nos `<>`suportes angulares, com o nome e a marca do seu próprio recipiente.  

    A opção `-t`de tag, é uma maneira de adicionar informações sobre o que você mudou para o contêiner. Por exemplo, um `modified-LUIS` nome de contêiner indica que o recipiente original foi colocado em camadas. Um nome `with-billing-and-model` de identificação indica como o contêiner De compreensão de idiomas (LUIS) foi modificado.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Faça login no Azure CLI a partir de um console. Este comando abre um navegador e requer autenticação. Uma vez autenticado, você pode fechar o navegador e continuar trabalhando no console.

    ```azurecli
    az login
    ```

1. Faça login no seu registro privado com o Azure CLI a partir de um console.

    Substitua os valores nos `<my-registry>`suportes angulares, com seu próprio nome de registro.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Você também pode fazer login com o login do docker se for atribuído um diretor de serviço.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Marque o contêiner com o local de registro privado. Substitua os valores nos `<my-registry>`suportes angulares, com seu próprio nome de registro. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Se você não usar um `latest` nome de marca, está implícito.

1. Empurre a nova imagem para o seu registro de contêiner privado. Quando você visualizar seu registro de contêiner privado, o nome do contêiner usado no seguinte comando CLI será o nome do repositório.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar e usar a instância do contêiner Azure](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->