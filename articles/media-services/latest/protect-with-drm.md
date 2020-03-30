---
title: Serviço sinuoso de criptografia e entrega de licenças do Azure Media Services
titleSuffix: Azure Media Services
description: Aprenda a usar o serviço de criptografia dinâmica drm e entrega de licenças para fornecer fluxos criptografados com licenças Microsoft PlayReady, Google Widevine ou Apple FairPlay.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 14ba5f270138db22a76fd697b264046e22577427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086739"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Tutorial: Use o serviço de criptografia dinâmica do DRM e entrega de licenças

> [!NOTE]
> Embora este tutorial use os exemplos do [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet), as etapas gerais são as mesmas para [API REST](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest) ou outros [SDKs](media-services-apis-overview.md#sdks) com suporte.

É possível usar os Serviços de Mídia do Azure para entregar seus fluxos criptografados com licenças do Microsoft PlayReady, Google Widevine ou Apple FairPlay. Para obter uma explicação detalhada, consulte [proteção de conteúdo com criptografia dinâmica](content-protection-overview.md).

Os Serviços de Mídia também fornecem um serviço para fornecer licenças DRM PlayReady, Widevine e FairPlay. Quando um usuário solicita conteúdo protegido por DRM, o aplicativo do jogador solicita uma licença do serviço de licença do Media Services. Se o aplicativo do jogador for autorizado, o serviço de licença do Media Services emitirá uma licença para o jogador. Uma licença contém a chave de descriptografia que pode ser usada pelo player cliente para descriptografar e transmitir o conteúdo.

Este artigo se baseia no exemplo [Criptografando com DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

O exemplo descrito neste artigo produz o seguinte resultado:

![AMS com vídeo protegido por DRM no Azure Media Player](./media/protect-with-drm/ams_player.png)

Este tutorial mostra como:

> [!div class="checklist"]
> * Crie uma codificação Transform.
> * Defina a chave de assinatura usada para a verificação de seu token.
> * Defina os requisitos na política de chave de conteúdo.
> * Crie um StreamingLocator com a política de streaming especificada.
> * Crie uma URL usada para reproduzir seu arquivo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes itens são necessários para concluir o tutorial:

* Examine o artigo [Visão geral da proteção de conteúdo](content-protection-overview.md).
* Revise o [sistema de proteção de conteúdo multi-DRM Design com controle de acesso.](design-multi-drm-system-with-access-control.md)
* Instale o Visual Studio Code ou o Visual Studio.
* Crie uma nova conta dos Serviços de Mídia do Azure, conforme descrito [neste início rápido](create-account-cli-quickstart.md).
* Obtenha as credenciais necessárias para usar as APIs dos Serviços de Mídia seguindo as [APIs de acesso](access-api-cli-how-to.md)
* Defina os valores apropriados no arquivo de configuração do aplicativo (appsettings.json).

## <a name="download-code"></a>Código de download

Clone um repositório do GitHub que contenha o exemplo de .NET completo discutido neste artigo em sua máquina usando o comando a seguir:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
O exemplo de “Criptografar com DRM” está localizado na pasta [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> A amostra cria recursos únicos cada vez que você executa o aplicativo. Normalmente, você reutilizará recursos existentes, como transformações e políticas (se os recursos existentes tiverem configurações necessárias).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar APIs de serviços de mídia com .NET, crie um objeto **AzureMediaServicesClient.** Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código que você clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Criar um ativo de saída  

O [Ativo](assets-concept.md) de saída armazena o resultado do trabalho de codificação.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Obter ou criar uma transformação de codificação

Ao criar um novo exemplo de [Transformação](transforms-jobs-concept.md), você precisa especificar o que deseja produzir como uma saída. O parâmetro necessário `transformOutput` é um objeto, como mostrado no código abaixo. Cada TransformOutput contém um **Predefinição**. Predefinição descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que serão usadas para gerar o TransformOutput desejado. O exemplo descrito neste artigo usa uma predefinição chamada **AdaptiveStreaming**. O Preset codifica o vídeo de entrada em uma escada de bitrate autogerada (pares de resolução bitrate) com base na resolução de entrada e bitrate, e produz arquivos ISO MP4 com vídeo H.264 e áudio AAC correspondente si0 000 de resolução bitrate. 

Antes de criar uma nova **Transformação**, você deve verificar se já existe uma usando o método **Get**, conforme mostrado no código a seguir.  Em Serviços de Mídia v3, os métodos **Get** em entidades retornam **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Enviar Trabalho

Conforme mecionado acima, o objeto de **Transformação** é a receita e um [Trabalho](transforms-jobs-concept.md) é a solicitação real para os Serviços de Mídia para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações como a localização do vídeo de entrada e a localização da saída.

Neste tutorial, criamos a entrada do trabalho com base em um arquivo que é ingerido diretamente de uma [URL de origem hTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

O trabalho leva algum tempo para ser concluído. Quando isso ocorrer, você desejará ser notificado. O exemplo de código a seguir mostra como sondar o serviço para o status do **Trabalho**. Sondagem não é uma prática recomendada para aplicativos de produção devido à latência potencial. A sondagem pode ser acelerada, se houver uso excessivo em uma conta. Os desenvolvedores devem usar a Grade de Eventos. Consulte [Rotear eventos para um ponto de extremidade personalizado de web](job-state-events-cli-how-to.md).

O **Trabalho** normalmente passa pelos seguintes estados: **Agendado**, **Em fila**, **Processamento**, **Concluído** (o estado final). Se o trabalho tiver encontrado um erro, você receberá o estado do **Erro**. Se o trabalho estiver para ser cancelado, você obterá **Cancelando** e **Cancelado** quando estiver pronto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Crie uma política de chave de conteúdo

Uma chave de conteúdo fornece acesso seguro aos seus ativos. Você precisa criar uma [diretiva de chave de conteúdo](content-key-policy-concept.md) ao criptografar seu conteúdo com um DRM. A diretiva configura a forma como a chave de conteúdo é entregue aos clientes finais. A chave de conteúdo está associada a um localizador de streaming. Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia e licenças para usuários autorizados.

Você precisa definir os requisitos (restrições) na **Política de Chave de Conteúdo** que deve ser atendida para fornecer chaves com a configuração especificada. Neste exemplo, definimos as configurações e os requisitos a seguir:

* Configuração

    As licenças do [PlayReady](playready-license-template-overview.md) e do [Widevine](widevine-license-template-overview.md) são configuradas de modo que possam ser entregues pelo serviço de entrega de licença dos Serviços de Mídia. Mesmo que este aplicativo de exemplo não configure a licença [fairplay,](fairplay-license-overview.md) ele contém um método que você pode usar para configurar o FairPlay. Você pode adicionar a configuração fairplay como outra opção.

* Restrição

    O aplicativo define uma restrição do tipo de token JSON Web Token (JWT) na diretiva.

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo. Para descriptografar o fluxo, o player solicita a chave do serviço de distribuição de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que você especificou para a chave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Criar um Localizador de Streaming

Depois que a codificação for concluída e a política de chave de conteúdo for definida, a próxima etapa é disponibilizar o vídeo no ativo de saída para os clientes o reproduzirem. Você disponibiliza o vídeo em duas etapas:

1. Crie um [localizador de streaming](streaming-locators-concept.md).
2. Compile as URLs de streaming que os clientes podem usar.

O processo de criação do **Localizador de Streaming** é chamado de publicação. Por padrão, o **Localizador de Streaming** é válido imediatamente após fazer as chamadas da API. Ele dura até ser excluído, a menos que você configure os horários opcionais de início e término.

Ao criar um **localizador de streaming,** `StreamingPolicyName`você precisa especificar o desejado . Neste tutorial, estamos usando uma das políticas de streaming predefinidas, que diz ao Azure Media Services como publicar o conteúdo para streaming. Neste exemplo, definimos a StreamingLocator.StreamingPolicyName à política do “Predefined_MultiDrmCencStreaming”. As criptografias PlayReady e Widevine são aplicadas e a chave é entregue ao cliente de reprodução com base nas licenças DRM configuradas. Se você também quiser criptografar seu fluxo com CBCS (FairPlay), use “Predefined_MultiDrmStreaming”.

> [!IMPORTANT]
> Ao usar uma [Política de Streaming](streaming-policy-concept.md) personalizada, você deve criar um conjunto limitado de políticas para a conta de Serviço de Mídia e reutilizá-los para os Localizadores de Streaming sempre que as mesmas opções e protocolos de criptografia forem necessários. Sua conta de Serviço de Mídia tem uma cota para o número de entradas de StreamingPolicy. Você não deveria estar criando uma nova Política de Streaming para cada StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obter um token de teste

Neste tutorial, especificamos que a política de chave de conteúdo tenha uma restrição de token. A política restrita do token deve ser acompanhada por um token emitido por um Serviço de Token de Segurança (STS). Os Serviços de Mídia suportam tokens nos formatos [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) e é isso que configuramos na amostra.

O ContentKeyIdentifierClaim é usado em ContentKeyPolicy, o que significa que o token apresentado para o serviço de entrega de chave deve ter o identificador do ContentKey nele. Na amostra, não especificamos uma chave de conteúdo ao criar o Localizador de Streaming, o sistema cria uma aleatória para nós. Para gerar o token de teste, devemos obter o ContentKeyId para colocar na reivindicação ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Construa uma URL de streaming

Agora que o [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, é possível obter as URLs de streaming. Para construir uma URL, você precisa concatenar o nome do host [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e o caminho **do Localizador de Streaming.** Neste exemplo, o *padrão do * **Ponto de Extremidade de Streaming** é usado. Ao criar uma conta de Serviço de Mídia, este *padrão* **Ponto de Extremidade de Streaming** estará em um estado parado, portanto você precisa chamar **Iniciar**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Quando você executa o aplicativo, você vê a seguinte tela:

![Proteger com DRM](./media/protect-with-drm/playready_encrypted_url.png)

Você pode abrir um navegador e colar a URL resultante para inicializar a página de demonstração do Player de Mídia do Azure com a URL e o token já preenchidos para você.

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos em sua conta de Serviços de Mídia

Geralmente, você deve limpar tudo, exceto objetos que você está planejando reutilizar (normalmente, você vai reutilizar Transforms, StreamingLocators, e assim por diante). Se desejar que sua conta seja limpa após fazer experimentos, exclua os recursos que não planeja reutilizar. Por exemplo, o código a seguir exclui trabalhos:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos criados anteriormente.

Execute este comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Observações adicionais

* O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

Fazer Check-out

> [!div class="nextstepaction"]
> [Proteger com AES-128](protect-with-aes128.md)
