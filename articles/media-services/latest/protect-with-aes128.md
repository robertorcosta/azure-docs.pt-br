---
title: Criptografe vídeo com AES-128
titleSuffix: Azure Media Services
description: Saiba como criptografar vídeo com criptografia AES de 128 bits e como usar o serviço de entrega de chaves no Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 126700e6290650221a9cb9711b22472301409fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974165"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Tutorial: Criptografe vídeo com AES-128 e use o serviço de entrega chave

> [!NOTE]
> Embora o tutorial use os exemplos do [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet), as etapas gerais são as mesmas para [API REST](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest) ou outros [SDKs](media-services-apis-overview.md#sdks) suportados.

Você pode usar os Serviços de Mídia para a distribuição HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming criptografado com o AES usando chaves de criptografia de 128 bits. Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia para usuários autorizados. Se você deseja que os Serviços de Mídia criptografem dinamicamente seu vídeo, você associa a chave de criptografia a um localizador de streaming e também configura a política de chave de conteúdo. Quando um fluxo é solicitado por um reprodutor, o Media Services usa a chave especificada para criptografar dinamicamente seu conteúdo com a AES-128. Para descriptografar o fluxo, o player solicita a chave do serviço de distribuição de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que você especificou para a chave.

Você pode criptografar cada ativo com vários tipos de criptografia (AES-128, PlayReady, Widevine, FairPlay). Consulte [Tipos de criptografia e protocolos de streaming](content-protection-overview.md#streaming-protocols-and-encryption-types) para ver o que faz sentido combinar. Além disso, [veja Como proteger com DRM](protect-with-drm.md).

A saída da amostra deste artigo inclui uma URL para o Azure Media Player, url manifesto e o token AES necessário para reproduzir o conteúdo. A amostra define a expiração do token JSON Web Token (JWT) para 1 hora. Você pode abrir um navegador e colar a URL resultante para inicializar a página de demonstração do Player de Mídia do Azure com a URL e o token preenchidos para você já no seguinte formato: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

Este tutorial mostra como:

> [!div class="checklist"]
> * Baixe a amostra [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) descrita no artigo.
> * Comece a usar APIs de Serviços de Mídia com .NET SDK.
> * Crie um ativo de saída.
> * Crie uma codificação Transform.
> * Submeta um trabalho.
> * Espere o trabalho ser concluído.
> * Crie uma política de chave de conteúdo
> * Configure a diretiva para usar a restrição do token JWT.
> * Crie um localizador de streaming.
> * Configure o Localizador de Streaming para criptografar o vídeo com AES (ClearKey).
> * Pegue um sinal de teste.
> * Construa uma URL de streaming.
> * Limpe os recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir o tutorial.

* Examine o artigo [Visão geral da proteção de conteúdo](content-protection-overview.md).
* Instale o Visual Studio Code ou o Visual Studio.
* [Crie uma conta de Serviços de Mídia](create-account-cli-quickstart.md).
* Obtenha credenciais necessárias para usar APIs de Serviços de Mídia seguindo [APIs de acesso](access-api-cli-how-to.md).

## <a name="download-code"></a>Código de download

Clone um repositório do GitHub que contenha o exemplo de .NET completo discutido neste artigo em sua máquina usando o comando a seguir:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

O exemplo de "Criptografar com AES-128" está localizado na pasta [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES).

> [!NOTE]
> A amostra cria recursos únicos cada vez que você executa o aplicativo. Normalmente, você reutilizará recursos existentes, como transformações e políticas (se os recursos existentes tiverem configurações necessárias).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar APIs de serviços de mídia com .NET, crie um objeto **AzureMediaServicesClient.** Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código que você clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Criar um ativo de saída  

O [Ativo](https://docs.microsoft.com/rest/api/media/assets) de saída armazena o resultado do trabalho de codificação.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Obter ou criar uma transformação de codificação

Ao criar um novo exemplo de [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você precisa especificar o que deseja produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, como mostrado no código a seguir. Cada **TransformOutput** contém um **Predefinição**. **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que serão usadas para gerar o **TransformOutput** desejado. O exemplo descrito neste artigo usa uma predefinição chamada **AdaptiveStreaming**. O Preset codifica o vídeo de entrada em uma escada de bitrate autogerada (pares de resolução bitrate) com base na resolução de entrada e bitrate e, em seguida, produz arquivos ISO MP4 com vídeo H.264 e áudio AAC correspondente a cada par de resolução bitrate.

Antes de criar um novo [Transform](https://docs.microsoft.com/rest/api/media/transforms), primeiro verifique se já existe usando o método **Get,** como mostrado no código a seguir. Em Serviços de Mídia v3, os métodos **Get** em entidades retornam **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Enviar Trabalho

Conforme mecionado acima, o objeto de [Transformação](https://docs.microsoft.com/rest/api/media/transforms) é a receita e um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs) é a solicitação real para os Serviços de Mídia para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações como a localização do vídeo de entrada e a localização da saída.

Neste tutorial, criamos a entrada do trabalho com base em um arquivo que é ingerido diretamente de uma [URL de origem hTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

O trabalho leva algum tempo para ser concluído. Quando isso ocorrer, você desejará ser notificado. O exemplo de código a seguir mostra como sondar o serviço para o status do [Trabalho](https://docs.microsoft.com/rest/api/media/jobs). Sondagem não é uma prática recomendada para aplicativos de produção devido à latência potencial. A sondagem pode ser acelerada, se houver uso excessivo em uma conta. Os desenvolvedores devem usar a Grade de Eventos. Para obter mais informações, confira [Encaminhar eventos para um ponto de extremidade da Web personalizado](job-state-events-cli-how-to.md).

O **Trabalho** normalmente passa pelos seguintes estados: **Agendado**, **Em fila**, **Processamento**, **Concluído** (o estado final). Se o trabalho tiver encontrado um erro, você receberá o estado do **Erro**. Se o trabalho estiver para ser cancelado, você obterá **Cancelando** e **Cancelado** quando estiver pronto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Crie uma política de chave de conteúdo

Uma chave de conteúdo fornece acesso seguro aos seus ativos. Você precisa criar uma **política de chave de conteúdo** que configure como a chave de conteúdo é entregue aos clientes finais. A chave de conteúdo está associada ao **Localizador de Streaming**. Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia para usuários autorizados.

Quando um fluxo é solicitado por um reprodutor, o Media Services usa a chave especificada para criptografar dinamicamente seu conteúdo (neste caso, usando criptografia AES.) Para descriptografar o fluxo, o jogador solicita a chave do serviço de entrega chave. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que você especificou para a chave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Criar um Localizador de Streaming

Depois que a codificação for concluída e a política de chave de conteúdo for definida, a próxima etapa é disponibilizar o vídeo no ativo de saída para os clientes o reproduzirem. Você disponibiliza o vídeo em duas etapas:

1. Crie um [localizador de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators).
2. Compile as URLs de streaming que os clientes podem usar.

O processo de criação do **Localizador de Streaming** é chamado de publicação. Por padrão, o **Localizador de Streaming** é válido imediatamente após fazer as chamadas da API. Ele dura até ser excluído, a menos que você configure os horários opcionais de início e término.

Ao criar um [localizador de streaming,](https://docs.microsoft.com/rest/api/media/streaminglocators)você precisará especificar o **StreamingPolicyName**desejado . Neste tutorial, estamos usando uma das PredefinedStreamingPolicies, que informa ao Azure Media Services como publicar o conteúdo para streaming. Neste exemplo, a criptografia AES Envelope é aplicada (essa criptografia também é conhecida como criptografia ClearKey porque a chave é entregue ao cliente de reprodução via HTTPS e não uma licença DRM).

> [!IMPORTANT]
> Ao usar uma [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizada, será necessário estruturar um conjunto limitado dessas políticas para sua conta de Serviço de Mídia e reutilizá-las em seus Localizadores de Streaming, sempre que as mesmas opções e protocolos de criptografia forem necessários. Sua conta de Serviço de Mídia tem uma cota para o número de entradas de StreamingPolicy. Você não deveria estar criando uma nova Política de Streaming para cada Localizador de Streaming.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obter um token de teste

Neste tutorial, especificamos que a política de chave de conteúdo tenha uma restrição de token. A política restrita do token deve ser acompanhada por um token emitido por um Serviço de Token de Segurança (STS). Os Serviços de Mídia suportam tokens no formato [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) e é isso que configuramos na amostra.

O ContentKeyIdentifierClaim é usado na **Diretiva de Chave de Conteúdo,** o que significa que o token apresentado ao serviço Key Delivery deve ter o identificador da chave de conteúdo nele. Na amostra, não especificamos uma chave de conteúdo ao criar o Localizador de Streaming, o sistema criou um aleatório para nós. Para gerar o token de teste, devemos obter o ContentKeyId para colocar na reivindicação ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Criar uma URL de streaming de DASH

Agora que o [Localizador de Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, você pode obter as URLs de streaming. Para construir uma URL, você precisa concatenar o nome do host [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e o caminho **do Localizador de Streaming.** Neste exemplo, o *padrão do * **Ponto de Extremidade de Streaming** é usado. Ao criar uma conta de Serviço de Mídia, este *padrão* **Ponto de Extremidade de Streaming** estará em um estado parado, portanto você precisa chamar **Iniciar**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos em sua conta de Serviços de Mídia

Geralmente, você deve limpar tudo, exceto objetos que você está planejando reutilizar (normalmente, você vai reutilizar Transforms, Streaming Locators, etc.). Se desejar que sua conta seja limpa após fazer experimentos, exclua os recursos que não planeja reutilizar. Por exemplo, o código a seguir exclui trabalhos:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos criados anteriormente.

Execute este comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="additional-notes"></a>Observações adicionais

* O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Proteger com DRM](protect-with-drm.md)
