---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2fe091a4ff0295ecadfd69ba3f2d4ca59e9612e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334606"
---
>[!NOTE]
>Para recursos que não são fixos, abra um boleto de apoio para pedir um aumento nas cotas. Não crie contas adicionais do Azure Media Services na tentativa de obter limites mais altos.

| Recurso | Limite | 
| --- | --- | 
| Contas do Azure Media Services em uma única assinatura | 25 (fixo) |
| Unidades reservadas de mídia por conta do Media Services |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Trabalhos por conta dos Serviços de Mídia | 50.000<sup>2</sup> |
| Tarefas encadeadas por trabalho | 30 (fixo) |
| Ativos por conta de Serviços de Mídia | 1.000.000|
| Ativos por tarefa | 50 |
| Ativos por trabalho | 100 |
| Localizadores exclusivos associados a um ativo simultaneamente | 5<sup>4</sup> |
| Canais ao vivo por conta do Media Services |5|
| Programas no estado interrompido por canal  |50|
| Programa em estado de execução por canal  |3|
| Pontos finais de streaming que estão parados ou em execução por conta do Media Services|2|
| Unidades de streaming por ponto de extremidade de streaming |10 |
| Contas de armazenamento | 1,000<sup>5</sup> (fixo) |
| Políticas | 1,000,000<sup>6</sup> |
| Tamanho de arquivo| Em alguns cenários, há um limite no tamanho máximo do arquivo suportado para processamento em Serviços de Mídia. <sup>7</sup> |

<sup>1</sup> Se você mudar o tipo, por exemplo, de S2 para S1, os limites máximos de unidade reservada serão redefinidos.

<sup>2</sup> Esse número inclui empregos enfileirados, acabados, ativos e cancelados. Não inclui empregos excluídos. Você pode excluir trabalhos antigos usando **iJob.Delete** ou a solicitação **DELETE** HTTP.

A partir de 1º de abril de 2017, qualquer registro de trabalho em sua conta com mais de 90 dias é automaticamente excluído, juntamente com seus registros de tarefas associados. A exclusão automática ocorre mesmo que o número total de registros esteja abaixo da cota máxima. Para arquivar as informações de trabalho e tarefa, use o código descrito no [Gerenciar ativos com o Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup> Quando você faz uma solicitação para listar entidades de trabalho, um máximo de 1.000 empregos é devolvido por solicitação. Para acompanhar todos os trabalhos enviados, use as consultas superiores ou pular conforme descrito nas [opções de consulta do sistema OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup> Os localizadores não são projetados para gerenciar o controle de acesso por usuário. Para dar diferentes direitos de acesso a usuários individuais, utilize soluções de gestão de direitos digitais (DRM). Para obter mais informações, consulte [Proteger seu conteúdo com o Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup> As contas de armazenamento devem ser da mesma assinatura do Azure.

<sup>6</sup> Há um limite de 1.000.000 políticas para diferentes políticas de Serviços de Mídia. Um exemplo é para a diretiva Localizador ou A Política de Autorização de Chave De Conteúdo. 

>[!NOTE]
> Se você sempre usar os mesmos dias e permissões de acesso, use o mesmo ID de política. Para obter informações e um exemplo, consulte [Gerenciar ativos com o Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup> O tamanho máximo suportado para uma única bolha é atualmente de até 5 TB no Azure Blob Storage. Limites adicionais se aplicam nos Serviços de Mídia com base nos tamanhos de VM que são usados pelo serviço. O limite de tamanho se aplica aos arquivos que você carrega e também aos arquivos que são gerados como resultado do processamento do Media Services (codificação ou análise). Se o arquivo de origem for maior que 260 GB, o trabalho provavelmente falhará. 

A tabela a seguir mostra os limites das unidades reservadas de mídia S1, S2 e S3. Se o arquivo de origem for maior do que os limites definidos na tabela, seu trabalho de codificação falhará. Se você codificar fontes de resolução 4K de longa duração, você será obrigado a usar unidades reservadas de mídia S3 para alcançar o desempenho necessário. Se você tiver conteúdo 4K maior que o limite de 260 GB nas unidades reservadas de mídia S3, abra um bilhete de suporte.

|Tipo de unidade reservada de mídia    |Tamanho máximo de entrada (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
