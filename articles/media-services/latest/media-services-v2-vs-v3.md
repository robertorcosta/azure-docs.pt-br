---
title: Migrar do Azure Media Services v2 para v3
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087825"
---
# <a name="media-services-v2-vs-v3"></a>Serviços de Mídia v2 vs. v3

Este artigo descreve as alterações introduzidas nos Serviços de Mídia do Azure v3 e mostra as diferenças entre as duas versões.

## <a name="general-changes-from-v2"></a>Mudanças gerais de v2

* Para ativos criados com v3, o Media Services suporta apenas a criptografia de armazenamento do lado do [servidor do Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * É possível usar APIs v3 com ativos criados com APIs v2 que tinham [criptografia de armazenamento](../previous/media-services-rest-storage-encryption.md) (AES 256) fornecida pelo Serviços de Mídia do Microsoft Azure.
    * Você não pode criar novos Ativos com a criptografia de [armazenamento AES 256 legada](../previous/media-services-rest-storage-encryption.md) usando APIs v3.
* As propriedades do [Ativo](assets-concept.md)em v3 diferem de v2, veja [como o mapa de propriedades](#map-v3-asset-properties-to-v2).
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
* Para obter informações sobre um trabalho, você precisa saber o nome Transform no qual o trabalho foi criado. 
* Em v2, os arquivos de metadados de [entrada](../previous/media-services-input-metadata-schema.md) e [saída](../previous/media-services-output-metadata-schema.md) XML são gerados como resultado de um trabalho de codificação. Em v3, o formato de metadados mudou de XML para JSON. 
* Em Serviços de Mídia v2, o vetor de inicialização (IV) pode ser especificado. Em Media Services v3, o FairPlay IV não pode ser especificado. Embora não afete os clientes que usam os Serviços de Mídia para a entrega de embalagens e licenças, pode ser um problema ao usar um sistema DRM de terceiros para fornecer as licenças FairPlay (modo híbrido). Nesse caso, é importante saber que o FairPlay IV é derivado do CBCS Key ID e pode ser recuperado usando esta fórmula:

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

    Para obter mais informações, consulte o [código C# das funções Do Zure para Serviços de Mídia v3 no modo híbrido para operações Live e VOD](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Revise as convenções de nomeação aplicadas aos [recursos v3 do Media Services](media-services-apis-overview.md#naming-conventions). Também [revise as bolhas de nomeação](assets-concept.md#naming).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Falhas de recurso em relação a APIs v2

A API de v3 tem as seguintes falhas de recurso em relação a API v2. Fechar as lacunas está em andamento.

* O [Codificador Premium](../previous/media-services-premium-workflow-encoder-formats.md) e os legados [processadores analíticos de mídia](../previous/media-services-analytics-overview.md) (Visualização do Azure Media Indexer 2, API de Detecção Facial, etc.) não podem ser acessados pela v3.<br/>Os clientes que desejam migrar da visualização do Indexador de Mídia 1 ou 2 podem usar imediatamente a predefinição AudioAnalyzer na API v3.  Essa nova predefinição contém mais recursos que o antigo Indexador de Mídia 1 ou 2. 
* Muitos dos [recursos avançados do Media Encoder Standard em](../previous/media-services-advanced-encoding-with-mes.md) APIs v2 não estão disponíveis atualmente em v3, tais como:
  
    * A união de ativos
    * Sobreposições
    * Corte
    * Sprites em miniatura
    * Inserindo uma faixa de áudio silenciosa quando a entrada não tem áudio
    * Inserir uma faixa de vídeo quando a entrada não tem vídeo
* Atualmente, os Eventos ao Vivo com transcodificação não dão suporte à inserção em curso de imagem fixa e à inserção de marcador de anúncio por meio da chamada à API. 
 
## <a name="asset-specific-changes"></a>Mudanças específicas de ativos

### <a name="map-v3-asset-properties-to-v2"></a>Mapear propriedades de ativos v3 para v2

A tabela a seguir mostra como as propriedades do [Ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)no mapa v3 para as propriedades do Ativo em v2.

|propriedades v3|propriedades v2|
|---|---|
|`id`- (único) o caminho completo do Azure Resource Manager, veja exemplos em [Asset](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`- (único) ver [Convenções de Nomeação](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`- (único) o `nb:cid:UUID:` valor começa com o prefixo.|
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
|Criptografia do Armazenamento dos Serviços de Mídia|Criptografia AES-256, chave gerenciada pelos Serviços de Mídia.|Com suporte<sup>(1)</sup>|Sem suporte<sup>(2)</sup>|
|[Criptografia do Serviço de Armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Criptografia do lado do servidor oferecida pelo Azure Storage, chave gerenciada pelo Azure ou pelo cliente.|Com suporte|Com suporte|
|[Criptografia do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Criptografia do lado do cliente oferecida pelo armazenamento Azure, chave gerenciada pelo cliente no Key Vault.|Sem suporte|Sem suporte|

<sup>1</sup> Embora os Serviços de Mídia apoiem o manuseio de conteúdo na clara/sem qualquer forma de criptografia, fazê-lo não é recomendado.

<sup>2</sup> Nos Serviços de Mídia v3, a criptografia de armazenamento (criptografia AES-256) somente terá suporte para compatibilidade com versões anteriores quando os Ativos tiverem sido criados com os Serviços de Mídia v2. O v3 funciona com ativos criptografados de armazenamento existentes, mas não permitirá a criação de novos.

## <a name="code-differences"></a>Diferenças de código

A tabela a seguir mostra as diferenças de código entre v2 e v3 para cenários comuns.

|Cenário|API V2|API V3|
|---|---|---|
|Criar um ativo e enviar um arquivo |[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[exemplo de .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Enviar um trabalho|[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[exemplo de .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra como criar primeiro uma transformação e, em seguida, enviar uma tarefa.|
|Publicar um ativo com criptografia AES |1. Crie a opção ContentKeyAuthorizationPolicyOption<br/>2. Crie a política de autorização de chaves de conteúdo<br/>3. Criar política de entrega de ativos<br/>4. Criar conteúdo de ativos e carregar ou enviar trabalho e usar o ativo de saída<br/>5. Associar a Política de Entrega de Ativos com O Ativo<br/>6. Crie a chave de conteúdo<br/>7. Anexar a chave de conteúdo ao ativo<br/>8. Criar política de acesso<br/>9. Criar localizador<br/><br/>[exemplo de .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Crie a política-chave de conteúdo<br/>2. Criar ativo<br/>3. Carregue conteúdo ou use o Asset como JobOutput<br/>4. Criar localizador de streaming<br/><br/>[exemplo de .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Obtenha detalhes do trabalho e gerencie trabalhos |[Gerencie empregos com v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Gerencie empregos com v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Por favor, marque este artigo e continue verificando atualizações.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

[Diretrizes de migração dos Serviços de Mídia v2 para v3](migrate-from-v2-to-v3.md)
