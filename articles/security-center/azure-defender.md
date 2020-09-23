---
title: Visão geral do Azure defender e dos planos disponíveis
description: Saiba mais sobre os planos, as proteções e os alertas do Azure defender. Em seguida, continue para habilitar o Azure defender em suas assinaturas.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bb1c1e6443b5087b48aad7c3171bef557707adb1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977287"
---
# <a name="introduction-to-azure-defender"></a>Introdução ao Azure defender

Os recursos da central de segurança do Azure abrangem os dois pilares mais amplos da segurança em nuvem:

- **Gerenciamento de postura de segurança na nuvem (CSPM)**
- **Proteção de carga de trabalho de nuvem (CWP)**

Os recursos CSPM da central de segurança, como Pontuação segura, detecção de configurações incorretas de segurança em seus computadores Windows e Linux do Azure, fazem parte da experiência gratuita da central de segurança disponível para todos os usuários do Azure. Use esses recursos do CSPM para reforçar sua postura e garantir a conformidade regulatória.

O **Azure defender** é a plataforma de proteção de carga de trabalho de nuvem (CWPP) integrada na central de segurança para proteção avançada, inteligente e de suas cargas de trabalho híbridas e do Azure.

Este é o painel do Azure defender na central de segurança do Azure:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Um exemplo do painel do Azure defender" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Quais tipos de recursos o Azure defender pode proteger?

O Azure defender fornece alertas de segurança e proteção avançada contra ameaças para máquinas virtuais, bancos de dados SQL, contêineres, aplicativos Web, sua rede e muito mais.

Quando você habilita o Azure defender da área de **preços e configurações** da central de segurança do Azure, os seguintes planos do defender são todos habilitados simultaneamente e fornecem defesas abrangentes para a computação, os dados e as camadas de serviço do seu ambiente:

- [Azure Defender para servidores](defender-for-servers-introduction.md)
- [Azure Defender for Serviço de Aplicativo](defender-for-app-service-introduction.md)
- [Azure Defender para Armazenamento](defender-for-storage-introduction.md)
- [Azure Defender para SQL](defender-for-sql-introduction.md)
- [Azure Defender para IoT](defender-for-iot-introduction.md)
- [Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender para registros de contêiner](defender-for-container-registries-introduction.md)
- [Azure Defender para Key Vault](defender-for-key-vault-introduction.md)

Cada um desses planos é explicado separadamente na documentação da central de segurança.


## <a name="hybrid-cloud-protection"></a>Proteção de nuvem híbrida

Além de defender seu ambiente do Azure, você pode adicionar recursos do Azure defender ao seu ambiente de nuvem híbrida:

- Proteger seus servidores não Azure
- Proteja suas máquinas virtuais em outras nuvens (como AWS e GCP)
- Proteger seus dispositivos IoT

Você obterá inteligência de ameaças personalizada e alertas priorizados de acordo com seu ambiente específico para que possa se concentrar no que é mais importante

Implante o [Azure Arc](https://azure.microsoft.com/services/azure-arc/) e habilite o Azure defender para estender a proteção para máquinas virtuais locais e de várias nuvens e bancos de dados SQL. O arco do Azure para servidores é um serviço gratuito, mas os serviços usados em servidores habilitados para Arc, por exemplo, o Azure defender, serão cobrados de acordo com o preço para esse serviço.

[Saiba mais sobre o arco do Azure](https://docs.microsoft.com/azure/azure-arc/overview).


## <a name="azure-defender-alerts"></a>Alertas do Azure Defender 

Quando o Azure defender detecta uma ameaça em qualquer área do seu ambiente, ele gera um alerta. Esses alertas descrevem os detalhes dos recursos afetados, as etapas de correção sugeridas e, em alguns casos, uma opção para disparar um aplicativo lógico em resposta.

Se um alerta for gerado pela central de segurança ou recebido pela central de segurança de um produto de segurança integrado, você poderá exportá-lo. Para exportar seus alertas para o Azure Sentinel, qualquer SIEM de terceiros ou qualquer outra ferramenta externa, siga as instruções em [Exportar alertas para um SIEM](continuous-export.md).

> [!NOTE]
> Alertas de fontes diferentes podem levar diferentes quantidades de tempo para serem exibidos. Por exemplo, alertas que exigem análise de tráfego de rede podem levar mais tempo para aparecer do que alertas relacionados a processos suspeitos em execução em máquinas virtuais.


## <a name="azure-defender-advanced-protection-capabilities"></a>Recursos de proteção avançada do Azure defender

O Azure defender usa análises avançadas para recomendações personalizadas relacionadas aos seus recursos. 

As proteções incluem proteger as portas de gerenciamento de suas VMs com acesso just-in-time e controles de aplicativo adaptáveis para criar listas de permissões para quais aplicativos devem e não deveriam ser executados em seus computadores. 

Use os blocos proteção avançada no painel do Azure defender para monitorar e configurar cada uma dessas proteções. 

## <a name="vulnerability-assessment-and-management"></a>Avaliação e gerenciamento de vulnerabilidades

O Azure defender inclui verificação de vulnerabilidade para suas máquinas virtuais e registros de contêiner sem custo adicional. Os scanners são alimentados por Qualys, mas você não precisa de uma licença do Qualys ou mesmo de uma conta do Qualys-tudo é manipulado diretamente na central de segurança. 

Examine as conclusões desses scanners de vulnerabilidade e responda a eles todos na central de segurança. Isso leva à central de segurança mais próxima de ser o único painel de vidro para todos os seus esforços de segurança na nuvem.

Saiba mais nas seguintes páginas:

- [Solução de avaliação de vulnerabilidade integrada da central de segurança para máquinas virtuais do Azure](deploy-vulnerability-assessment-vm.md)
- [Identificar vulnerabilidades em imagens em registros de contêiner do Azure](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre os benefícios do Azure defender. 

> [!div class="nextstepaction"]
> [Habilitar o Azure Defender](security-center-pricing.md)