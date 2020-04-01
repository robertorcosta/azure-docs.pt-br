---
title: Preços dos níveis do Azure Security Center
description: O Azure Security Center é oferecido em dois níveis - Free e Standard. Esta página mostra como atualizar de Free para Standard.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2019
ms.author: memildin
ms.openlocfilehash: fd84058c8421d144678c91fac3e5671511d0fd4a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435495"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Atualize para o nível Padrão para maior segurança
A Central de Segurança do Azure fornece gerenciamento de segurança unificado e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, localmente e em outras nuvens. Ela proporciona visibilidade e controle sobre cargas de trabalho de nuvem híbrida, defesas ativas que reduzem a exposição a ameaças e detecção inteligente para ajudá-lo a acompanhar o ritmo veloz da evolução dos ataques cibernéticos.

## <a name="pricing-tiers"></a>Tipos de preço
A Central de Segurança é oferecida em duas camadas:

- O **nível Free** está habilitado em todas as suas assinaturas do Azure uma vez que você visitar o painel do Azure Security Center no portal Azure pela primeira vez, ou se ativado programáticamente via API. O nível gratuito fornece política de segurança, avaliação contínua de segurança e recomendações de segurança acionáveis para ajudá-lo a proteger seus recursos do Azure.
- O **nível Standard** estende os recursos do nível Free para cargas de trabalho em execução em nuvens privadas e outras públicas, fornecendo gerenciamento de segurança unificado e proteção contra ameaças em suas cargas de trabalho na nuvem híbrida. O nível padrão também adiciona recursos de proteção contra ameaças, que usam análises comportamentais incorporadas e aprendizado de máquina para identificar ataques e explorações de dia zero, controles de acesso e aplicativos para reduzir a exposição a ataques de rede e malware, e muito mais. Além disso, o nível padrão adiciona varredura de vulnerabilidade para suas máquinas virtuais. Você pode experimentar o nível padrão gratuitamente. O padrão do Security Center suporta recursos do Azure, incluindo VMs, conjuntos de escala de máquinavirtual, serviço de aplicativo, servidores SQL e contas de armazenamento. Se você tiver o padrão do Azure Security Center, você pode optar por não ter suporte com base no tipo de recurso. 

A maioria das avaliações de segurança de nível livre para VMs, bem como muitos dos alertas de segurança de nível padrão, exigem a instalação do recurso de agente Log Analytics. Você pode habilitar a provisão automática no Security Center para implantar automaticamente o agente para suas VMs do Azure.

## <a name="try-standard-tier-free-for-30-days"></a>Experimente o nível padrão grátis por 30 dias
O nível padrão é gratuito para os primeiros 30 dias. Ao fim dos 30 dias, se você optar por continuar usando o serviço, começaremos a cobrar automaticamente pelo uso.

Você pode atualizar uma assinatura inteira do Azure para o nível padrão, que é herdado por todos os recursos dentro da assinatura.

Para obter o nível padrão:

1. Selecione **as configurações de preços &** no menu principal do Security **Center.**
2. Selecione a assinatura que deseja atualizar para o padrão.
3. Selecione **Tipo de preço**.
4. Selecione **Standard** para atualizar.
5. Clique em **Salvar**.

[![Preços do Centro de Segurança](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Para habilitar todos os recursos do Security Center, você deve aplicar o nível de preço padrão à assinatura que contém as máquinas virtuais aplicáveis. A configuração de preços para um espaço de trabalho não permite acesso de VM just-in-time, controles de aplicativos adaptativos e detecções de rede para recursos do Azure.
>

## <a name="why-upgrade-to-standard"></a>Por que atualizar para o padrão?
A Central de Segurança oferece maior segurança e proteção contra ameaças para suas cargas de trabalho de nuvem híbrida, incluindo:

- **Segurança híbrida** – Obtenha uma exibição unificada sobre a segurança em todas as suas cargas de trabalho locais e na nuvem. Aplique políticas de segurança e avalie continuamente a segurança de suas cargas de trabalho de nuvem híbrida a fim de garantir a conformidade com padrões de segurança. Coletar, pesquisar e analisar dados de segurança de várias fontes, incluindo firewalls e outras soluções de parceiros.
- **Alertas de segurança** - Use análises avançadas e o Microsoft Intelligent Security Graph para obter uma vantagem sobre os ataques cibernéticos em evolução. Aproveite a análise comportamental interna e o aprendizado de máquina para identificar ataques e explorações de dia zero. Monitore redes, computadores e serviços de nuvem contra ataques recebidos e atividade pós-violação. Simplifique a investigação com ferramentas interativas e inteligência contextual contra ameaças.
- **Varredura de vulnerabilidade para máquinas virtuais** - Implante facilmente um scanner em todas as suas máquinas virtuais que fornece a solução mais avançada do setor para o gerenciamento de vulnerabilidades. Visualize, investigue e remediaasse as descobertas diretamente dentro do Security Center. 
- **Controles de acesso e aplicativos** - Bloqueie malware e outros aplicativos indesejados aplicando recomendações de whitelisting alimentadas por aprendizado de máquina adaptadas às suas cargas de trabalho específicas. Reduza a superfície de ataque de rede com acesso controlado e just-in-time às portas de gerenciamento em VMs Azure. Isso reduz drasticamente a exposição à força bruta e outros ataques de rede.
- **Recursos de segurança** de contêineres - Beneficie-se do gerenciamento de vulnerabilidades e da proteção contra ameaças em tempo real em seus ambientes contêineres. Ao habilitar o recurso de registros de contêineres, pode levar até 12hrs até que todos os recursos estejam habilitados.


## <a name="next-steps"></a>Próximas etapas
Neste artigo, foram apresentados os preços da Central de Segurança. Para saber mais sobre a segurança aprimorada do nível Standard e a proteção avançada contra ameaças, consulte:

- [Proteção contra ameaças no Centro de Segurança Do Azure](threat-protection.md)
- [Controle de acesso VM just-in-time](security-center-just-in-time.md)
- [Visão geral de segurança do contêiner](container-security.md)
- [Detalhes de preços em sua moeda de escolha, e de acordo com sua região](https://azure.microsoft.com/pricing/details/security-center/)