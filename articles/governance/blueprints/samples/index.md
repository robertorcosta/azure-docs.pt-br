---
title: Índice de exemplos de blueprint
description: Índice de exemplos padrão e de conformidade para implantar ambientes, políticas e fundamentos do Cloud Adoptions Framework com o Azure Blueprints.
ms.date: 02/26/2020
ms.topic: sample
ms.openlocfilehash: 703c383f41cd833fb47ca489682258158f9fe179
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196817"
---
# <a name="azure-blueprints-samples"></a>Exemplos do Azure Blueprints

A tabela a seguir contém links para exemplos do Azure Blueprints. Cada exemplo é de qualidade de produção e está pronto para ser implantado hoje para ajudá-lo a atender às várias necessidades de conformidade.

## <a name="standards-based-blueprint-samples"></a>Exemplos de blueprint baseados em padrões

|  |  |
|---------|---------|
| [PBMM Federal do Canadá](./canada-federal-pbmm/index.md) | Fornece proteções para conformidade com o PBMM (B Protegido, Integridade Média, Disponibilidade Média) Federal do Canadá. |
| [CIS Microsoft Azure Foundations Benchmark](./cis-azure-1.1.0/index.md)| Fornece um conjunto de políticas para ajudar a cumprir as recomendações do CIS Microsoft Azure Foundations Benchmark. |
| [Nível de Impacto 4 do DoD](./dod-impact-level-4/index.md) | Fornece um conjunto de políticas para ajudar a manter a conformidade com o Nível de Impacto 4 do DoD. |
| [FedRAMP Moderado](./fedramp-m/index.md) | Fornece um conjunto de políticas para ajudar a manter a conformidade com o FedRAMP moderado. |
| [FedRAMP High](./fedramp-h/index.md) | Fornece um conjunto de políticas para ajudar a manter a conformidade com o FedRAMP High. |
| [HIPAA/HITRUST](./HIPAA-HITRUST/index.md) | Fornece um conjunto de políticas para ajudar a manter a conformidade com HIPAA/HITRUST. |
| [IRS 1075](./irs-1075/index.md) | Fornece as proteções para a conformidade com o IRS 1075.|
| [ISO 27001](./iso27001/index.md) | Fornece as diretrizes para a conformidade com a ISO 27001. |
| [Serviços Compartilhados ISO 27001](./iso27001-shared/index.md) | Oferece um conjunto de padrões de infraestrutura e proteções de política em conformidade que ajudam na certificação ISO 27001. |
| [Carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001](./iso27001-ase-sql-workload/index.md) | Oferece infraestrutura adicional para o exemplo de blueprint dos [Serviços Compartilhados ISO 27001](./iso27001-shared/index.md). |
| [Mídia](./media/index.md) | Fornece um conjunto de políticas para ajudar a manter a conformidade com a MPAA de Mídia. |
| [NIST SP 800-53 R4](./nist-sp-800-53-rev4/index.md) | Fornece as diretrizes para a conformidade com o NIST SP 800-53 R4. |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | Fornece um conjunto de políticas para auxiliar em conformidade com PCI-DSS v3.2.1. |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | Auxilia na conformidade com o SWIFT CSP-CSCF v2020. |
| [Governança de NHS do Reino Unido e OFICIAL do Reino Unido](./ukofficial/index.md) | Oferece um conjunto de padrões de infraestrutura e proteções de política em conformidade que ajudam na certificação OFICIAL do Reuno Unido e da NHS do Reino Unido. |
| [Base do CAF](./caf-foundation/index.md) | Fornece um conjunto de controles para ajudá-lo a gerenciar seu estado de nuvem em alinhamento com o [CAF (Cloud Adoption Framework da Microsoft para o Azure)](/azure/architecture/cloud-adoption/governance/journeys/index). |
| [Zona de aterrissagem da Migração do CAF](./caf-migrate-landing-zone/index.md) | Fornece um conjunto de controles para ajudá-lo na configuração para migrar sua primeira carga de trabalho e gerenciar o estado de nuvem em alinhamento com o [CAF (Cloud Adoption Framework da Microsoft para o Azure)](/azure/architecture/cloud-adoption/migrate/index). |

## <a name="samples-strategy"></a>Estratégia de exemplos

![Estratégia de exemplos de blueprint](../media/blueprint-samples-strategy.png)

Os blueprints da Base do CAF e da zona de destino da Migração do CAF pressupõem que o cliente está preparando uma assinatura única limpa existente para migrar ativos e cargas de trabalho locais para o Azure.
(Regiões A e B da figura).  

Há a oportunidade de iterar nos exemplos de blueprints e procurar padrões de personalização que um cliente esteja aplicando. Também há a oportunidade de abordar proativamente os blueprints que são específicos do setor, como serviços financeiros e comércio eletrônico (extremidade superior da região B). Da mesma forma, prevemos a criação de blueprints para considerações complexas de arquitetura, como várias assinaturas, alta disponibilidade, recursos entre regiões e clientes que estão implementando controles sobre assinaturas e recursos existentes (regiões C e D).

Há exemplos de blueprints que abordam cenários de cliente em que os requisitos de conformidade são altos e as complexidades arquitetônicas são altas (região E da figura). A região F na figura será abordada por clientes e parceiros, aproveitando os exemplos de blueprints e personalizando-os para as suas necessidades exclusivas.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).
