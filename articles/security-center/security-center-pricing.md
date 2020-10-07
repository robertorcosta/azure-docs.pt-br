---
title: Preços da Central de Segurança do Azure
description: 'A Central de Segurança do Azure é oferecida em dois modos: com e sem o Azure Defender.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: a01d4137217aa594d4636f3338d3f33dc03cc836
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713739"
---
# <a name="pricing-of-azure-security-center"></a>Preços da Central de Segurança do Azure
A Central de Segurança do Azure fornece gerenciamento de segurança unificado e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, localmente e em outras nuvens. Ela proporciona visibilidade e controle sobre cargas de trabalho de nuvem híbrida, defesas ativas que reduzem a exposição a ameaças e detecção inteligente para ajudá-lo a acompanhar o ritmo veloz da evolução dos ataques cibernéticos.


## <a name="free-option-vs-azure-defender-enabled"></a>Opção gratuita vs Azure Defender habilitado

A Central de Segurança é oferecida em dois modos:

- **Azure Defender DESATIVADO** (Gratuito) – A Central de Segurança sem o Azure Defender está habilitada gratuitamente em todas as suas assinaturas do Azure quando você acessa o painel da Central de Segurança do Azure no portal do Azure pela primeira vez ou, se ela é habilitada de maneira programática por meio da API. O uso desse modo gratuito fornece uma política de segurança, uma avaliação de segurança contínua e recomendações de segurança práticas para ajudar você a proteger seus recursos do Azure.

- **Azure Defender ATIVADO**: a habilitação do Azure Defender estende as funcionalidades do modo gratuito para cargas de trabalho em execução em nuvens privadas e outras nuvens públicas, oferecendo gerenciamento unificado de segurança e proteção contra ameaças nas suas cargas de trabalho de nuvem híbrida. Alguns dos principais recursos do Azure Defender:

    - **Segurança híbrida** – Obtenha uma exibição unificada sobre a segurança em todas as suas cargas de trabalho locais e na nuvem. Aplique políticas de segurança e avalie continuamente a segurança de suas cargas de trabalho de nuvem híbrida a fim de garantir a conformidade com padrões de segurança. Colete, pesquise e analise dados de segurança de várias fontes, incluindo firewalls e outras soluções de parceiros.
    - **Alertas de proteção contra ameaças**: a análise comportamental avançada e o Gráfico de Segurança Inteligente da Microsoft oferecem uma vantagem em relação aos ataques cibernéticos em evolução. Aproveite a análise comportamental interna e o aprendizado de máquina para identificar ataques e explorações de dia zero. Monitore redes, computadores e serviços de nuvem contra ataques recebidos e atividade pós-violação. Simplifique a investigação com ferramentas interativas e inteligência contextual contra ameaças.
    - **Verificação de vulnerabilidades de máquinas virtuais e registros de contêiner**: implante com facilidade um verificador em todas as suas máquinas virtuais que fornece a solução mais avançada do setor para o gerenciamento de vulnerabilidades. Veja, investigue e corrija as descobertas diretamente na Central de Segurança. 
    - **Controles de acesso e de aplicativo**: bloqueie malware e outros aplicativos indesejados aplicando recomendações ativadas por machine learning adaptadas para suas cargas de trabalho específicas a fim de criar listas de permissões e negação. Reduza a superfície de ataque da rede com o acesso just-in-time controlado às portas de gerenciamento nas VMs do Azure. Isso reduz significativamente a exposição a ataques de força bruta e outros ataques de rede.
    - **Recursos de segurança do contêiner**: beneficie-se do gerenciamento de vulnerabilidades e da proteção contra ameaças em tempo real nos seus ambientes em contêineres. Ao habilitar o **Azure Defender para registros de contêiner**, podem ser necessárias até 12 horas até que todos os recursos sejam habilitados. As cobranças são baseadas no número de imagens de contêiner exclusivas enviadas por push para o registro conectado. Depois que uma imagem for verificada uma vez, você não será cobrado novamente, a menos que ela seja modificada e enviada por push mais uma vez. 

## <a name="try-azure-defender-free-for-30-days"></a>Experimente o Azure Defender gratuitamente por 30 dias

O Azure Defender é gratuito nos primeiros 30 dias. Ao final dos 30 dias, se você optar por continuar usando o serviço, a cobrança pelo uso será iniciada automaticamente.

## <a name="enable-azure-defender"></a>Habilitar o Azure Defender

Você pode proteger uma assinatura inteira do Azure com o Azure Defender e as proteções serão herdadas por todos os recursos na assinatura.

Para habilitar o Azure Defender:

1. No menu principal da Central de Segurança, selecione **Preços e configurações**.
1. Selecione a assinatura que deseja atualizar.
1. Escolha **Azure Defender ativado** para fazer a atualização.
1. Selecione **Salvar**.

Veja abaixo a página de preços para obter um exemplo de assinatura. Você observará que cada plano do Azure Defender é cobrado separadamente e pode ser definido de maneira individual como ativado ou desativado.

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Página de preços da Central de Segurança no portal":::

> [!NOTE]
> Para habilitar todos os recursos da Central de Segurança, incluindo as funcionalidades de proteção contra ameaças, habilite o Azure Defender na assinatura que contém as cargas de trabalho aplicáveis. A habilitação dele no nível do workspace não habilita o acesso just-in-time à VM, os controles de aplicativo adaptáveis nem as detecções de rede para os recursos do Azure. Além disso, os únicos planos do Azure Defender disponíveis no nível do workspace são para servidores e para SQL Server em máquinas.
>
> Habilite o **Azure Defender para contas de armazenamento** no nível da assinatura ou do recurso.
> Habilite o **Azure Defender para SQL** no nível da assinatura ou do recurso.
> Você pode habilitar a proteção contra ameaças para o **Banco de Dados do Azure para MariaDB/MySQL/PostgreSQL** apenas no nível do recurso.


## <a name="next-steps"></a>Próximas etapas
Neste artigo, foram apresentados os preços da Central de Segurança. Para obter materiais relacionados, confira:

- [Como otimizar os custos da carga de trabalho do Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Detalhes de preços na sua moeda de escolha e de acordo com a sua região](https://azure.microsoft.com/pricing/details/security-center/)
- Talvez você queira gerenciar os custos e limitar a quantidade de dados coletados de uma solução limitando-a a determinado conjunto de agentes. O [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md) permite que você aplique um escopo à solução e direcione a um subconjunto de computadores no workspace. Se você estiver usando o direcionamento de solução, a Central de Segurança listará o workspace como não tendo uma solução.