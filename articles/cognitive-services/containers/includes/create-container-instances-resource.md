---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
description: Aprenda a criar um recurso de instância de contêiner do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876385"
---
## <a name="create-an-azure-container-instance-resource"></a>Criar um recurso Azure Container Instance

1. Vá para a página [Criar](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) para Instâncias de Contêiner.

2. Na guia **Noções Básicas,** digite os seguintes detalhes:

    |Configuração|Valor|
    |--|--|
    |Subscription|Selecione sua assinatura.|
    |Resource group|Selecione o grupo de recursos disponível ou crie um novo, como `cognitive-services`.|
    |Nome do contêiner|Digite um `cognitive-container-instance`nome como . O nome deve estar nas tampas inferiores.|
    |Location|Selecione uma região para implantação.|
    |Tipo de Imagem|Se a imagem do contêiner estiver armazenada em um `Public`registro de contêiner que não exija credenciais, escolha . Se acessar a imagem do `Private`contêiner exigir credenciais, escolha . Consulte os [repositórios e imagens do contêiner](../../cognitive-services-container-support.md#container-repositories-and-images) `Public` para `Private` obter detalhes sobre se a imagem do contêiner é ou ("Visualização Pública"). |
    |Nome da imagem|Digite a localização do contêiner dos Serviços Cognitivos. O local é o que é `docker pull` usado como argumento para o comando. Consulte os [repositórios e imagens](../../cognitive-services-container-support.md#container-repositories-and-images) do contêiner para obter os nomes de imagem disponíveis e seu repositório correspondente.<br><br>O nome da imagem deve ser totalmente qualificado especificando três partes. Primeiro, o registro do contêiner, depois o repositório, finalmente o nome da imagem: `<container-registry>/<repository>/<image-name>`.<br><br>Aqui está um `mcr.microsoft.com/azure-cognitive-services/keyphrase` exemplo, representaria a imagem de extração de frases-chave no Registro de Contêineres da Microsoft sob o repositório de Serviços Cognitivos do Azure. Outro exemplo `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` é o que representaria a imagem 'Fala para Texto' no repositório microsoft do registro de contêiner De pré-visualização de contêiner. |
    |Tipo do SO|`Linux`|
    |Tamanho|Alterar o tamanho para as recomendações sugeridas para o seu contêiner de Serviço Cognitivo específico:<br>2 núcleos de CPU<br>4 GB

3. Na guia **Rede, digite** os seguintes detalhes:

    |Configuração|Valor|
    |--|--|
    |Portas|Defina a porta `5000`TCP para . Expõe o contêiner no porto 5000.|

4. Na guia **Avançado,** digite as **variáveis de ambiente necessárias** para as configurações de cobrança de contêiner do recurso Azure Container Instance:

    | Chave | Valor |
    |--|--|
    |`apikey`|Copiado da página **Chaves** do recurso. É uma seqüência de caracteres alfanuméricos de `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`32 sem espaços ou traços, .|
    |`billing`|Copiado da página **Visão Geral** do recurso.|
    |`eula`|`accept`|

5. Clique **em Revisar e Criar**
6. Após a validação passar, clique **em Criar** para concluir o processo de criação
7. Quando o recurso é implantado com sucesso, ele está pronto
