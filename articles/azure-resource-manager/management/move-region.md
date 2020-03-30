---
title: Mova os recursos do Azure para outra região
description: Fornece uma visão geral da movimentação de recursos do Azure em todas as regiões do Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485202"
---
# <a name="moving-azure-resources-across-regions"></a>Movimentação de recursos do Azure em todas as regiões

Este artigo fornece informações sobre a movimentação de recursos do Azure em todas as regiões do Azure.

Geografias, regiões e zonas de disponibilidade do Azure formam a base da infra-estrutura global do Azure. As [geografias](https://azure.microsoft.com/global-infrastructure/geographies/) do Azul normalmente contêm duas ou mais [regiões azure.](https://azure.microsoft.com/global-infrastructure/regions/) Uma região é uma área dentro de uma geografia, contendo Zonas de Disponibilidade e vários data centers. 

Depois de implantar recursos em uma região específica do Azure, há uma série de razões pelas quais você pode querer transferir recursos para uma região diferente.

- **Alinhar a um lançamento de região**: Mova seus recursos para uma região Azure recém-introduzida que não estava disponível anteriormente.
- **Alinhar para serviços/recursos**: Mova recursos para aproveitar os serviços ou recursos disponíveis em uma região específica.
- **Responda aos desenvolvimentos dos negócios**: Mova recursos para uma região em resposta a mudanças de negócios, como fusões ou aquisições.
- **Alinhar para a proximidade**: Mova recursos para uma região local para o seu negócio.
- **Atender aos requisitos de dados**: Mover recursos para se alinhar com os requisitos de residência de dados ou necessidades de classificação de dados. [Saiba mais](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Responda aos requisitos de implantação**: Mova recursos que foram implantados por engano ou mova-se em resposta às necessidades de capacidade. 
- **Responda ao descomissionamento**: Mova recursos devido ao descomissionamento de regiões.

## <a name="move-process"></a>Processo de mudança

O processo de mudança depende dos recursos que você está movendo. No entanto, existem alguns passos-chave comuns:

- **Verificar pré-requisitos**: Os pré-requisitos incluem certificar-se de que os recursos necessários estão disponíveis na região de destino, verificar se você tem cota suficiente e verificar se sua assinatura pode acessar a região de destino.
- **Analise dependências**: Seus recursos podem ter dependências de outros recursos. Antes de se mover, descubra as dependências para que os recursos movidos continuem funcionando como esperado após a mudança.
- **Prepare-se para mover**: Estes são os passos que você dá em sua região primária antes da mudança. Por exemplo, talvez seja necessário exportar um modelo do Azure Resource Manager ou começar a replicar recursos da origem para o destino.
- **Mova os recursos**: A forma como você move os recursos depende do que eles são. Você pode precisar implantar um modelo na região de destino ou falhar recursos para o destino.
- **Descarte os recursos de destino**: Depois de mover recursos, você pode querer dar uma olhada nos recursos agora na região alvo e decidir se há algo que você não precisa.
- **Comprometa a movimentação**: Após verificar os recursos na região alvo, alguns recursos podem exigir uma ação de confirmação final. Por exemplo, em uma região-alvo que agora é a região primária, você pode precisar configurar a recuperação de desastres para uma nova região secundária. 
- **Limpe a fonte**: Finalmente, depois de tudo estar funcionando na nova região, você pode limpar e desativar os recursos que você criou para a mudança, e recursos em sua região primária.



## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de quais recursos suportam a movimentação entre regiões, consulte [O suporte à operação Move para recursos](region-move-support.md).
