---
title: Preços das camadas da central de segurança do Azure
description: A central de segurança do Azure é oferecida em duas camadas – gratuita e Standard. Esta página mostra como atualizar de gratuito para Standard.
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
ms.openlocfilehash: 882f8ab7e4f55809759f3a94fd04fb2c410fe188
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089712"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Atualizar para a camada Standard para segurança aprimorada

A Central de Segurança do Azure fornece gerenciamento de segurança unificado e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, localmente e em outras nuvens. Ela proporciona visibilidade e controle sobre cargas de trabalho de nuvem híbrida, defesas ativas que reduzem a exposição a ameaças e detecção inteligente para ajudá-lo a acompanhar o ritmo veloz da evolução dos ataques cibernéticos.

## <a name="pricing-tiers"></a>Tipos de preço
A Central de Segurança é oferecida em duas camadas:

- A camada **gratuita** é habilitada em todas as suas assinaturas do Azure quando você visita o painel da central de segurança do azure na portal do Azure pela primeira vez ou, se habilitada programaticamente via API. A camada gratuita fornece política de segurança, avaliação de segurança contínua e recomendações de segurança acionáveis para ajudá-lo a proteger seus recursos do Azure.

- A camada **Standard** estende os recursos da camada gratuita para cargas de trabalho em execução em outras nuvens públicas e privadas, fornecendo gerenciamento de segurança unificado e proteção contra ameaças em suas cargas de trabalho de nuvem híbrida. A camada Standard também adiciona recursos de proteção contra ameaças, que usam análise comportamental interna e aprendizado de máquina para identificar ataques e explorações de dia zero, controles de acesso e de aplicativos para reduzir a exposição a ataques de rede e malware e muito mais. Além disso, a camada Standard adiciona verificação de vulnerabilidade para suas máquinas virtuais. Você pode experimentar a camada Standard gratuitamente. A central de segurança Standard dá suporte a recursos do Azure, incluindo VMs, conjuntos de dimensionamento de máquinas virtuais, serviço de aplicativo, servidores SQL e contas de armazenamento. Se você tiver a central de segurança do Azure Standard, poderá recusar o suporte com base no tipo de recurso. 

A maioria das avaliações de segurança de camada gratuita para VMs, bem como muitos dos alertas de segurança de camada Standard, requer a instalação do agente de Log Analytics. Você pode habilitar o provisionamento automático na central de segurança para implantar automaticamente o agente em suas VMs do Azure.

## <a name="try-standard-tier-free-for-30-days"></a>Experimente a camada Standard gratuitamente por 30 dias
A camada Standard é gratuita para os primeiros 30 dias. Ao fim dos 30 dias, se você optar por continuar usando o serviço, começaremos a cobrar automaticamente pelo uso.

Você pode atualizar uma assinatura inteira do Azure para a camada Standard, que é herdada por todos os recursos na assinatura.

Para obter a camada Standard:

1. Selecione **configurações de & de preços** no menu principal da **central de segurança** .
2. Selecione a assinatura que você deseja atualizar para o padrão.
3. Selecione **Tipo de preço**.
4. Selecione **Standard** para atualizar.
5. Clique em **Save** (Salvar).

[![Preços da central de segurança](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Para habilitar todos os recursos da central de segurança, incluindo recursos de proteção contra ameaças, você deve aplicar o tipo de preço padrão à assinatura que contém as cargas de trabalho aplicáveis. A configuração de preços para um espaço de trabalho não permite o acesso just-in-time à VM, controles de aplicativos adaptáveis e detecções de rede para recursos do Azure. 
>
> Você pode habilitar a proteção contra ameaças para **contas de Armazenamento do Azure** no nível da assinatura ou no nível do recurso.
> Você pode habilitar a proteção contra ameaças para **servidores SQL do Banco de Dados SQL do Azure** no nível da assinatura ou no nível do recurso.
> Você pode habilitar a proteção contra ameaças para o **Banco de Dados do Azure para MariaDB/MySQL/PostgreSQL** apenas no nível do recurso.


## <a name="why-upgrade-to-standard"></a>Por que atualizar para o padrão?
A Central de Segurança oferece maior segurança e proteção contra ameaças para suas cargas de trabalho de nuvem híbrida, incluindo:

- **Segurança híbrida** – Obtenha uma exibição unificada sobre a segurança em todas as suas cargas de trabalho locais e na nuvem. Aplique políticas de segurança e avalie continuamente a segurança de suas cargas de trabalho de nuvem híbrida a fim de garantir a conformidade com padrões de segurança. Colete, pesquise e analise dados de segurança de várias fontes, incluindo firewalls e outras soluções de parceiros.
- **Alertas de segurança** -use a análise avançada e a gráfico de segurança inteligente da Microsoft para obter uma borda sobre ataques cibernéticos em evolução. Aproveite a análise comportamental interna e o aprendizado de máquina para identificar ataques e explorações de dia zero. Monitore redes, computadores e serviços de nuvem contra ataques recebidos e atividade pós-violação. Simplifique a investigação com ferramentas interativas e inteligência contextual contra ameaças.
- **Verificação de vulnerabilidades de máquinas virtuais** – implante facilmente um scanner em todas as suas máquinas virtuais que forneçam a solução mais avançada do setor para o gerenciamento de vulnerabilidades. Exiba, investigue e corrija as descobertas diretamente na central de segurança. 
- **Controles de acesso e de aplicativo** – bloquear malware e outros aplicativos indesejados aplicando recomendações de lista de permissões de aprendizado de máquina adaptadas para suas cargas de trabalho específicas. Reduza a superfície de ataque de rede com acesso controlado just-in-time a portas de gerenciamento em VMs do Azure. Isso reduz drasticamente a exposição à força bruta e a outros ataques de rede.
- **Recursos de segurança do contêiner** -beneficie-se do gerenciamento de vulnerabilidades e da proteção contra ameaças em tempo real em seus ambientes em contêineres. Ao habilitar o recurso de registros de contêiner, pode levar até 12hrs até que todos os recursos estejam habilitados. As cobranças são baseadas no número de imagens de contêiner exclusivas enviadas por push para o registro conectado. Depois que uma imagem for verificada uma vez, ela não será cobrada novamente, a menos que seja modificada e enviada por push mais uma vez. 




## <a name="next-steps"></a>Próximas etapas
Neste artigo, foram apresentados os preços da Central de Segurança. Para saber mais sobre a segurança avançada da camada Standard e a proteção avançada contra ameaças, consulte:

- [Proteção contra ameaças na Central de Segurança do Azure](threat-protection.md)
- [Controle de acesso da VM just-in-time](security-center-just-in-time.md)
- [Visão geral de segurança do contêiner](container-security.md)
- [Detalhes de preços na sua moeda de escolha e de acordo com sua região](https://azure.microsoft.com/pricing/details/security-center/)