---
title: Preços da central de segurança do Azure
description: A central de segurança do Azure é oferecida em dois modos com e sem o Azure defender.
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
ms.date: 04/28/2020
ms.author: memildin
ms.openlocfilehash: 6d2127af2c2c9e04141551dae72f0177f495b165
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904791"
---
# <a name="pricing-of-azure-security-center"></a>Preços da central de segurança do Azure
A Central de Segurança do Azure fornece gerenciamento de segurança unificado e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, localmente e em outras nuvens. Ela proporciona visibilidade e controle sobre cargas de trabalho de nuvem híbrida, defesas ativas que reduzem a exposição a ameaças e detecção inteligente para ajudá-lo a acompanhar o ritmo veloz da evolução dos ataques cibernéticos.


## <a name="free-option-vs-azure-defender-enabled"></a>Opção gratuita vs do Azure defender habilitada

A central de segurança é oferecida em dois modos:

- **Azure defender off** (gratuito) – a central de segurança sem o Azure defender está habilitada gratuitamente em todas as suas assinaturas do Azure ao visitar o painel da central de segurança do azure na portal do Azure pela primeira vez ou, se habilitada programaticamente via API. O uso desse modo gratuito fornece política de segurança, avaliação de segurança contínua e recomendações de segurança acionáveis para ajudá-lo a proteger seus recursos do Azure.

- O **Azure defender ao** habilitar o Azure defender estende os recursos do modo gratuito para cargas de trabalho em execução em nuvens privadas e públicas, fornecendo gerenciamento de segurança unificado e proteção contra ameaças em suas cargas de trabalho de nuvem híbrida. Alguns dos principais recursos do Azure defender:

    - **Segurança híbrida** – Obtenha uma exibição unificada sobre a segurança em todas as suas cargas de trabalho locais e na nuvem. Aplique políticas de segurança e avalie continuamente a segurança de suas cargas de trabalho de nuvem híbrida a fim de garantir a conformidade com padrões de segurança. Colete, pesquise e analise dados de segurança de várias fontes, incluindo firewalls e outras soluções de parceiros.
    - **Alertas de proteção contra ameaças** -análise comportamental avançada e a gráfico de segurança inteligente da Microsoft fornecem uma borda sobre ataques cibernéticos em evolução. Aproveite a análise comportamental interna e o aprendizado de máquina para identificar ataques e explorações de dia zero. Monitore redes, computadores e serviços de nuvem contra ataques recebidos e atividade pós-violação. Simplifique a investigação com ferramentas interativas e inteligência contextual contra ameaças.
    - **Verificação de vulnerabilidade de máquinas virtuais e registros de contêiner** -implante facilmente um scanner em todas as suas máquinas virtuais que fornece a solução mais avançada do setor para o gerenciamento de vulnerabilidades. Exiba, investigue e corrija as descobertas diretamente na central de segurança. 
    - **Controles de acesso e aplicativo** – bloqueie malware e outros aplicativos indesejados aplicando recomendações de aprendizado de máquina adaptadas para suas cargas de trabalho específicas para criar listas de permissão e negação. Reduza a superfície de ataque de rede com acesso controlado just-in-time a portas de gerenciamento em VMs do Azure. Isso reduz drasticamente a exposição à força bruta e a outros ataques de rede.
    - **Recursos de segurança do contêiner** -beneficie-se do gerenciamento de vulnerabilidades e da proteção contra ameaças em tempo real em seus ambientes em contêineres. Ao habilitar o **Azure defender para registros de contêiner**, pode levar até 12hrs até que todos os recursos estejam habilitados. As cobranças são baseadas no número de imagens de contêiner exclusivas enviadas por push para o registro conectado. Depois que uma imagem for verificada uma vez, você não será cobrada novamente, a menos que ela seja modificada e enviada por push mais uma vez. 

## <a name="try-azure-defender-free-for-30-days"></a>Experimente o Azure Defender gratuitamente por 30 dias

O Azure defender é gratuito nos primeiros 30 dias. No final de 30 dias, se você optar por continuar usando o serviço, começaremos a cobrar automaticamente pelo uso.

## <a name="enable-azure-defender"></a>Habilitar o Azure Defender

Você pode proteger uma assinatura inteira do Azure com o Azure defender e as proteções serão herdadas por todos os recursos na assinatura.

Para habilitar o Azure defender:

1. No menu principal da central de segurança, selecione **preços & configurações**.
1. Selecione a assinatura que você deseja atualizar.
1. Selecione **Azure defender** para atualizar.
1. Clique em **Salvar**.

Veja abaixo a página de preços de uma assinatura de exemplo. Você observará que cada plano no Azure defender é cobrado separadamente e pode ser definido individualmente como ativado ou desativado.

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Página de preços da central de segurança no portal":::

> [!NOTE]
> Para habilitar todos os recursos da central de segurança, incluindo recursos de proteção contra ameaças, você deve habilitar o Azure defender na assinatura que contém as cargas de trabalho aplicáveis. Habilitá-lo no nível do espaço de trabalho não habilita o acesso just-in-time à VM, controles de aplicativo adaptáveis e detecções de rede para recursos do Azure. 
>
> Você pode habilitar o **Azure defender para contas de armazenamento** no nível de assinatura ou de recurso.
> Você pode habilitar o **Azure defender para SQL** no nível de assinatura ou de recurso.
> Você pode habilitar a proteção contra ameaças para o **Banco de Dados do Azure para MariaDB/MySQL/PostgreSQL** apenas no nível do recurso.


## <a name="next-steps"></a>Próximas etapas
Neste artigo, foram apresentados os preços da Central de Segurança. Para material relacionado, consulte:

- [Como otimizar os custos de carga de trabalho do Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Detalhes de preços na sua moeda de escolha e de acordo com sua região](https://azure.microsoft.com/pricing/details/security-center/)
- Talvez você queira gerenciar os custos e limitar a quantidade de dados coletados de uma solução limitando-a a determinado conjunto de agentes. O [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md) permite que você aplique um escopo à solução e direcione a um subconjunto de computadores no workspace. Se você estiver usando o direcionamento de solução, a Central de Segurança listará o workspace como não tendo uma solução.