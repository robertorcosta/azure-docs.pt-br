---
title: Cotas e limitações dos Serviços de Mídia do Azure v3 | Microsoft Docs
description: Este tópico descreve as cotas e as limitações nos Serviços de Mídia do Azure v3
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.openlocfilehash: 819548d784e5cba9fcec6b2110137d91bf28e03d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296918"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Cotas e limitações dos Serviços de Mídia do Azure v3

Este artigo descreve cotas e limitações nos Serviços de Mídia do Azure v3.

| Resource | Limite padrão | 
| --- | --- | 
| Ativos por conta de Serviços de Mídia do Azure | 1\.000.000|
| Filtros de Manifesto Dinâmico|100|
| JobInputs por trabalho | 50  (fixo)|
| JobOutputs por trabalho | 20 (fixo) |
| TransformOutputs em uma transformação | 20 (fixo) |
| Arquivos por JobInput|10 (fixo)|
| Tamanho do arquivo| Em alguns cenários, há um limite no tamanho máximo de arquivo com suporte para o processamento nos Serviços de Mídia. <sup>(1)</sup> |
| Trabalhos por conta dos Serviços de Mídia | 500.000 <sup>(2)</sup> (fixo)|
| Eventos ao vivo por conta dos Serviços de Mídia |5|
| Contas de Serviços de Mídia em uma única assinatura | 25 (fixo) |
| Saídas ao vivo por evento ao vivo |3 <sup>(3)</sup> |
| Duração máxima de saída ao vivo | 25 horas |
| Contas de armazenamento | 100<sup>(4)</sup> (fixo) |
| Pontos de extremidade de streaming (interrompidos ou em execução) por conta dos Serviços de Mídia|2 (fixo)|
| Políticas de Streaming | 100 <sup>(5)</sup> |
| Transformações por conta dos Serviços de Mídia | 100  (fixo)|
| Localizadores de streaming exclusivos associados a um Ativo ao mesmo tempo | 100<sup>(6)</sup> (fixo) |
| Opções por política de chave de conteúdo |30 | 
| Licenças por mês para cada um dos tipos de DRM no serviço de entrega de chave dos serviços de mídia por conta|1\.000.000|

<sup>1</sup> O tamanho máximo com suporte para um único blob atualmente é de até 5 TB no Azure Blob Storage. Limites adicionais se aplicam aos serviços de mídia com base nos tamanhos de VM que são usados pelo serviço. O limite de tamanho se aplica aos arquivos que você carrega e também aos arquivos que são gerados como resultado do processamento de serviços de mídia (codificação ou análise). Se o arquivo de origem for maior que 260 GB, o trabalho provavelmente falhará. 

A tabela a seguir mostra os limites nas unidades reservadas de mídia S1, S2 e S3. Se o arquivo de origem for maior do que os limites definidos na tabela, o trabalho de codificação falhará. Se você codificar fontes de resolução de 4K de longa duração, será necessário usar unidades reservadas de mídia S3 para atingir o desempenho necessário. Se você tiver um conteúdo de 4K maior do que o limite de 260 GB nas unidades reservadas de mídia S3, entre em contato conosco em amshelp@microsoft.com para obter possíveis reduções para dar suporte ao seu cenário.

|Tipo de unidade reservada de mídia   |Tamanho máximo de entrada (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. 

Qualquer registro de Trabalho em sua conta com mais de 90 dias será excluído automaticamente, mesmo que o número total de registros esteja abaixo da cota máxima. 

<sup>3</sup> saídas ao vivo começam na criação e param quando excluídas.

<sup>4</sup> As contas de armazenamento devem ser da mesma assinatura do Azure.

<sup>5</sup> ao usar uma [política de streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizada, você deve criar um conjunto limitado de políticas para sua conta de serviço de mídia e reutilizá-las para o StreamingLocators sempre que forem necessárias as mesmas opções de criptografia e protocolos. Você não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

<sup>6</sup> os localizadores de streaming não foram projetados para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM).

## <a name="support-ticket"></a>Tíquete de suporte

Para obter recursos que não são fixos, você pode solicitar que as cotas sejam geradas, abrindo um [tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Inclua informações detalhadas na solicitação sobre as alterações de cota desejadas, cenários de casos de uso e regiões necessárias. <br/>**Não** crie contas adicionais dos Serviços de Mídia do Azure em uma tentativa de obter limites mais altos.

## <a name="next-steps"></a>Próximas etapas

[Visão geral](media-services-overview.md)
