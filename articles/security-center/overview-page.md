---
title: Painel principal da central de segurança do Azure ou página de "visão geral"
description: Saiba mais sobre os recursos da página Visão geral da central de segurança
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 637f7dde63d562f71aa31561a960ed3502a80229
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90932857"
---
# <a name="azure-security-centers-overview-page"></a>Página de visão geral da central de segurança do Azure

Quando você abre a central de segurança do Azure, a primeira página a ser exibida é a página Visão geral. 

:::image type="content" source="media/overview-page/overview.png" alt-text="Página de visão geral da Central de Segurança":::

Descubra e avalie a segurança de suas cargas de trabalho e identifique e reduza riscos, com a página Visão geral da central de segurança.

A visão geral fornece uma exibição unificada sobre a postura de segurança de suas cargas de trabalho de nuvem híbrida. Além disso, ele mostra alertas de segurança, informações de cobertura e muito mais.


## <a name="features-of-the-overview-page"></a>Recursos da página Visão geral

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Barra superior da página de visão geral da central de segurança":::

A **barra de menus superior** oferece:
- **Assinaturas** – você pode exibir e filtrar a lista de assinaturas selecionando esse botão. A Central de Segurança ajustará a exibição para refletir a postura de segurança das assinaturas selecionadas.
- **O que há de novo** – abre as [notas de versão](release-notes.md) para que você possa se manter atualizado com os novos recursos, correções de bugs e funcionalidade preterida.
- **Números de alto nível** para as contas de nuvem conectadas, para mostrar o contexto das informações nos blocos principais abaixo. Bem como o número de recomendações e alertas ativos.
    Saiba mais sobre como conectar suas [contas do AWS](quickstart-onboard-aws.md) e seus projetos do [GCP](quickstart-onboard-gcp.md).


No centro da página há **quatro blocos centrais**, cada um vinculando a um painel dedicado para obter mais detalhes:
- **Pontuação segura** -a central de segurança avalia continuamente seus recursos, suas assinaturas e a organização em busca de problemas de segurança. Em seguida, ele agrega todas as conclusões em uma única pontuação para que você possa ver, rapidamente, sua situação de segurança atual: quanto maior a pontuação, menor o nível de risco identificado. [Saiba mais](secure-score-security-controls.md).
- **Conformidade** – a central de segurança fornece informações sobre sua postura de conformidade com base em avaliações contínuas do seu ambiente do Azure. A Central de Segurança analisa fatores de risco em seu ambiente de nuvem híbrida de acordo com as melhores práticas de segurança. Essas avaliações são mapeadas para controles de conformidade de um conjunto de padrões com suporte. [Saiba mais](security-center-compliance-dashboard.md).
- **Azure defender** – esta é a CWPP (plataforma de proteção de carga de trabalho de nuvem) integrada na central de segurança para proteção avançada e inteligente de suas cargas de trabalho híbridas e do Azure. O bloco mostra a cobertura de seus recursos conectados (para as assinaturas atualmente selecionadas) e os alertas recentes, codificados por cor por severidade. [Saiba mais](azure-defender.md).
- **Inventário** -o bloco mostra o número de VMs não monitoradas e uma indicador simples de seus recursos monitorados pelo Security CenterBen. [Saiba mais](asset-inventory.md).


O painel de **informações** oferece itens personalizados para seu ambiente, incluindo:
- Seus recursos mais atacados
- Seus controles de segurança que têm o maior potencial para aumentar sua pontuação segura
- As recomendações ativas com a maioria dos recursos afetados
- Postagens recentes no blog dos especialistas da central de segurança do Azure

## <a name="next-steps"></a>Próximas etapas

Esta página introduziu a página Visão geral da central de segurança. Para obter informações relacionadas. consulte:

- [Explore e gerencie seus recursos com o inventário de ativos e as ferramentas de gerenciamento](asset-inventory.md)
- [Pontuação segura na Central de Segurança do Azure](secure-score-security-controls.md)