---
title: Migrar dos serviços de mídia do Azure v2 para v3
description: Este artigo descreve as alterações introduzidas nos Serviços de Mídia do Azure v3 e mostra as diferenças entre as duas versões.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: serviços de mídia do azure, transmissão, difusão, ao vivo, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: fd094e35ceaa718ec1b258d74106b39744cbd16f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79087825"
---
# <a name="media-services-v2-vs-v3"></a>Serviços de mídia v2 versus v3

Este artigo descreve as alterações introduzidas nos Serviços de Mídia do Azure v3 e mostra as diferenças entre as duas versões.

## <a name="general-changes-from-v2"></a>Alterações gerais da V2

* Para ativos criados com v3, os serviços de mídia oferecem suporte apenas à [criptografia de armazenamento do lado do servidor do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * É possível usar APIs v3 com ativos criados com APIs v2 que tinham [criptografia de armazenamento](../previous/media-services-rest-storage-encryption.md) (AES 256) fornecida pelo Serviços de Mídia do Microsoft Azure.
    * Você não pode criar novos Ativos com a criptografia de [armazenamento AES 256 legada](../previous/media-services-rest-storage-encryption.md) usando APIs v3.
* As propriedades do [ativo](assets-concept.md)na v3 diferem de v2, veja [como as propriedades são mapeadas](#map-v3-asset-properties-to-v2).
* Os v3 SDKs agora são desacoplados do Storage SDK, o que lhe dá mais controle sobre a versão do Storage SDK que você deseja usar e evita problemas de versão. 
* Nas APIs da v3, todas as taxas de bits de codificação estão em bits por segundo. Isso é diferente das predefinições do v2 Media Encoder Standard. Por exemplo, o bitrate em v2 seria especificado como 128 (kbps), mas em v3 seria 128000 (bits / segundo). 
* Entidades AssetFiles, AccessPolicies e IngestManifests não existem na v3.
* A propriedade IAsset.ParentAssets não existe na v3.
* ContentKeys não é mais uma entidade; agora é uma propriedade do Localizador de Streaming.
* O suporte para Grade de Eventos substitui o NotificationEndpoints.
* As seguintes entidades foram renomeadas
    * A Saída do Trabalho substitui a Tarefa e, agora faz parte de um Trabalho.
    * O Localizador de Streaming substitui o Localizador.
    * O Evento ao Vivo substitui o Canal.<br/>A cobrança dos Eventos ao Vivo baseia-se em medidores do Canal ao Vivo. Para obter mais informações, confira [cobrança](live-event-states-billing.md) e [preços](https://azure.microsoft.com/pricing/details/media-services/).
    * A Saída Dinâmica substitui o Programa.
* As Saídas ao Vivo começam na criação e terminam quando são excluídas. Os programas funcionaram de maneira diferente nas APIs v2 e tiveram que ser iniciados após a criação.
* Para obter informações sobre um trabalho, você precisa saber o nome da transformação sob a qual o trabalho foi criado. 
* Em v2, os arquivos de metadados de [entrada](../previous/media-services-input-metadata-schema.md) e [saída](../previous/media-services-output-metadata-schema.md) XML são gerados como resultado de um trabalho de codificação. Em v3, o formato de metadados mudou de XML para JSON. 
* Nos serviços de mídia v2, o vetor de inicialização (IV) pode ser especificado. Nos serviços de mídia v3, o FairPlay IV não pode ser especificado. Embora não afete os clientes que usam os serviços de mídia para o empacotamento e a entrega de licença, pode ser um problema ao usar um sistema DRM de terceiros para fornecer as licenças FairPlay (modo híbrido). Nesse caso, é importante saber que o FairPlay IV é derivado da ID de chave CBCs e pode ser recuperado usando esta fórmula:

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    por

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    Para obter mais informações, consulte o [Azure Functions código C# para os serviços de mídia v3 no modo híbrido para operações em tempo real e VOD](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Examine as convenções de nomenclatura que são aplicadas aos [recursos dos serviços de mídia v3](media-services-apis-overview.md#naming-conventions). Examine também os [blobs de nomenclatura](assets-concept.md#naming).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Falhas de recurso em relação a APIs v2

A API de v3 tem as seguintes falhas de recurso em relação a API v2. Fechar as lacunas está em andamento.

* O [Codificador Premium](../previous/media-services-premium-workflow-encoder-formats.md) e os legados [processadores analíticos de mídia](../previous/media-services-analytics-overview.md) (Visualização do Azure Media Indexer 2, API de Detecção Facial, etc.) não podem ser acessados pela v3.<br/>Os clientes que desejam migrar da visualização do Indexador de Mídia 1 ou 2 podem usar imediatamente a predefinição AudioAnalyzer na API v3.  Essa nova predefinição contém mais recursos que o antigo Indexador de Mídia 1 ou 2. 
* Muitos dos [recursos avançados do Media Encoder Standard em APIs v2](../previous/media-services-advanced-encoding-with-mes.md) não estão disponíveis atualmente na v3, como:
  
    * A união de ativos
    * Sobreposições
    * Corte
    * Sprites em miniatura
    * Inserindo uma faixa de áudio silenciosa quando a entrada não tem áudio
    * Inserindo uma faixa de vídeo quando a entrada não tem vídeo
* Atualmente, os Eventos ao Vivo com transcodificação não dão suporte à inserção em curso de imagem fixa e à inserção de marcador de anúncio por meio da chamada à API. 
 
## <a name="asset-specific-changes"></a>Alterações específicas do ativo

### <a name="map-v3-asset-properties-to-v2"></a>Mapear Propriedades de ativos V3 para v2

A tabela a seguir mostra como as propriedades do [ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)em v3 são mapeadas para as propriedades do ativo na v2.

|Propriedades v3|Propriedades de v2|
|---|---|
|`id`-(exclusivo) o caminho de Azure Resource Manager completo, consulte os exemplos no [ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`-(exclusivo) consulte [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`-o valor (exclusivo) começa com `nb:cid:UUID:` o prefixo.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options`(opções de criação)|
|`type`||

### <a name="storage-side-encryption"></a>Criptografia do armazenamento

Para proteger os Ativos em repouso, os ativos devem ser criptografados pela criptografia do armazenamento. A tabela a seguir mostra como a criptografia do armazenamento funciona nos Serviços de Mídia:

|Opção de criptografia|Descrição|Serviços de Mídia v2|Serviços de Mídia v3|
|---|---|---|---|
|Criptografia do Armazenamento dos Serviços de Mídia|Criptografia AES-256, chave gerenciada pelos serviços de mídia.|Com suporte<sup>(1)</sup>|Sem suporte<sup>(2)</sup>|
|[Criptografia do Serviço de Armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Criptografia do lado do servidor oferecida pelo armazenamento do Azure, chave gerenciada pelo Azure ou por cliente.|Com suporte|Com suporte|
|[Criptografia do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Criptografia do lado do cliente oferecida pelo armazenamento do Azure, chave gerenciada por cliente no Key Vault.|Sem suporte|Sem suporte|

<sup>1</sup> enquanto os serviços de mídia dão suporte ao tratamento de conteúdo em claro/sem qualquer forma de criptografia, fazer isso não é recomendado.

<sup>2</sup> Nos Serviços de Mídia v3, a criptografia de armazenamento (criptografia AES-256) somente terá suporte para compatibilidade com versões anteriores quando os Ativos tiverem sido criados com os Serviços de Mídia v2. O que significa v3 funciona com ativos criptografados de armazenamento existentes, mas não permitirá a criação de novos.

## <a name="code-differences"></a>Diferenças de código

A tabela a seguir mostra as diferenças de código entre v2 e v3 para cenários comuns.

|Cenário|API V2|API V3|
|---|---|---|
|Criar um ativo e enviar um arquivo |[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[exemplo de .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Enviar um trabalho|[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[exemplo de .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra como criar primeiro uma transformação e, em seguida, enviar uma tarefa.|
|Publicar um ativo com criptografia AES |1. criar ContentKeyAuthorizationPolicyOption<br/>2. criar ContentKeyAuthorizationPolicy<br/>3. criar AssetDeliveryPolicy<br/>4. criar ativo e carregar conteúdo ou enviar trabalho e usar ativo de saída<br/>5. associar o AssetDeliveryPolicy ao ativo<br/>6. criar ContentKey<br/>7. anexar ContentKey ao ativo<br/>8. criar AccessPolicy<br/>9. criar localizador<br/><br/>[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. criar política de chave de conteúdo<br/>2. criar ativo<br/>3. carregar conteúdo ou usar ativo como JobOutput<br/>4. criar localizador de streaming<br/><br/>[exemplo de .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Obter detalhes do trabalho e gerenciar trabalhos |[Gerenciar trabalhos com v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Gerenciar trabalhos com v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Por favor, marque este artigo e continue verificando atualizações.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

[Diretrizes de migração dos Serviços de Mídia v2 para v3](migrate-from-v2-to-v3.md)
