---
title: Benchmark de segurança do Azure v2-governança e estratégia
description: Estratégia e governança de benchmark de segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e8a5196bf71712caae1218933ed13345f4cecd99
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059130"
---
# <a name="security-control-governance-and-strategy"></a>Controle de segurança: governança e estratégia

O backup e a recuperação abrangem controles para garantir que os backups de dados e de configuração em diferentes camadas de serviço sejam executados, validados e protegidos.

## <a name="gs-1-define-asset-management-and-protection-strategy"></a>GS-1: definir a estratégia de proteção e gerenciamento de ativos

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Certifique-se de documentar e comunicar uma estratégia clara para o monitoramento contínuo e a proteção de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o monitoramento de sistemas e dados críticos para os negócios. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Padrão de classificação de dados de acordo com os riscos de negócios

-   Visibilidade da organização de segurança em riscos e inventário de ativos 

-   Aprovação da organização de segurança dos serviços do Azure para uso 

-   Segurança de ativos por meio de seu ciclo de vida

-   Estratégia de controle de acesso necessária de acordo com a classificação de dados organizacionais

-   Uso de recursos de proteção de dados nativos e de terceiros do Azure

-   Requisitos de criptografia de dados para casos de uso em trânsito e em repouso

-   Padrões de criptografia apropriados

Observação: o gerenciamento de ativos e a abordagem de proteção para nuvem e locais podem ser diferentes dependendo de vários fatores, como o serviço de aplicativo/modelo de hospedagem, riscos de negócios e requisito de conformidade. 

- [Recomendação da arquitetura de segurança do Azure – armazenamento, dados e criptografia](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Conceitos básicos de segurança do Azure-segurança de dados do Azure, criptografia e armazenamento](../fundamentals/encryption-overview.md)

- [Estrutura de adoção de nuvem – práticas recomendadas de segurança de dados do Azure e criptografia](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Benchmark de segurança do Azure – gerenciamento de ativos](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Benchmark de segurança do Azure-proteção de dados](/azure/security/benchmarks/security-controls-v2-data-protection)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-security-posture-management-strategy"></a>GS-2: definir estratégia de gerenciamento de postura de segurança

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| GS-2 | 20, 3, 5 | RA, CM, SC |

Meça e reduza continuamente os riscos para seus ativos individuais e o ambiente no qual eles estão hospedados. Priorize os ativos de alto valor e as superfícies de ataque altamente expostas, como aplicativos publicados, pontos de entrada e saída de rede, os pontos de extremidade de usuário e administrador, etc.

- [Benchmark de segurança do Azure – gerenciamento de postura e vulnerabilidade](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-align-organization-roles-responsibilities-and-accountabilities"></a>GS-3: alinhar funções de organização, responsabilidades e responsabilidades

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| GS-3 | N/D | PL, PM |

Certifique-se de documentar e comunicar uma estratégia clara para funções e responsabilidades em sua organização de segurança. Priorize o fornecimento de responsabilidade clara por decisões de segurança, educação sobre o modelo de responsabilidade compartilhada e treinamento técnico para segurança na nuvem. 

- [Prática recomendada de segurança do Azure 1 – pessoas: treinar equipes na jornada de segurança de nuvem](https://aka.ms/AzSec1)

- [Prática recomendada de segurança do Azure 2-pessoas: educar equipes sobre tecnologia de segurança de nuvem](https://aka.ms/AzSec2)

- [Prática recomendada de segurança do Azure 3-processo: atribuir responsabilidade por decisões de segurança na nuvem](https://aka.ms/AzSec3)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-define-network-security-strategy"></a>GS-4: definir a estratégia de segurança de rede

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| GS-4 | 9 | CA, SC |

Estabeleça uma abordagem de segurança de rede do Azure como parte da estratégia geral de controle de acesso de segurança da sua organização.  

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Gerenciamento de rede centralizado e responsabilidade de segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação corporativa

-   Estratégia de correção em diferentes cenários de ameaça e ataque

-   Borda da Internet e a estratégia de entrada e saída

-   Estratégia de interconectividade local e de nuvem híbrida

-   Artefatos de segurança de rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Observação: sua abordagem de segurança de rede para nuvem e local pode ser diferente dependendo de vários fatores, como modelo de serviço de aplicativo, exposição de ameaça e configuração de rede híbrida.

- [Prática recomendada de segurança do Azure 11-arquitetura. Estratégia de segurança unificada única](https://aka.ms/AzSec11)

- [Benchmark de segurança do Azure-segurança de rede](/azure/security/benchmarks/security-controls-v2-network-security)

- [Visão geral da segurança de rede do Azure](../fundamentals/network-overview.md)

- [Estratégia de arquitetura de rede corporativa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-identity-and-privileged-access-strategy"></a>GS-5: definir a identidade e a estratégia de acesso privilegiado

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| GS-5 | 16, 4 | AC, AU, SC |

Estabeleça uma identidade do Azure e abordagens de acesso privilegiado como parte da estratégia geral de controle de acesso de segurança da sua organização.  

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Um sistema de identidade e autenticação centralizado e sua interconectividade com outros sistemas de identidade internos e externos

-   Métodos de autenticação fortes em diferentes casos de uso e condições

-   Proteção de usuários altamente privilegiados

-   Monitoramento e manipulação de atividades de usuário de anomalias  

-   Identidade do usuário e processo de reavaliação e reconciliação de acesso

Observação: sua identidade e sua abordagem de acesso privilegiado para nuvem e local podem ser diferentes dependendo de vários fatores, como o caminho de acesso a dados/aplicativos, o modelo de serviço e a estratégia de acesso de cliente/parceiro.

- [Benchmark de segurança do Azure-gerenciamento de identidade](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Benchmark de segurança do Azure-acesso privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Prática recomendada de segurança do Azure 11-arquitetura. Estratégia de segurança unificada única](https://aka.ms/AzSec11)

- [Visão geral da segurança de gerenciamento de identidade do Azure](../fundamentals/identity-management-overview.md) 

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-logging-and-threat-response-strategy"></a>GS-6: definir a estratégia de resposta contra ameaças e registro

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| GS-6 | 19 | IR, AU, RA, SC |

Estabeleça uma estratégia de resposta a ameaças e registro para detectar e corrigir ameaças rapidamente, atendendo aos requisitos de conformidade. Priorize o fornecimento de analistas com alertas de alta qualidade e experiências integradas para que eles possam se concentrar em ameaças em vez de integração e etapas manuais. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   A função e as responsabilidades da organização de operações de segurança (SecOps) 

-   Um processo de resposta a incidentes bem definido, alinhando-se ao NIST ou a outra estrutura do setor 

-   Captura de log e retenção para dar suporte à detecção de ameaças, resposta a incidentes e necessidades de conformidade

-   Visibilidade centralizada e informações de correlação sobre ameaças, usando SIEM, recursos nativos do Azure e outras fontes 

-   Plano de comunicação e notificação com seus clientes, fornecedores e partes públicas de seu interesse

-   Uso de plataformas nativas e de terceiros do Azure para tratamento de incidentes, como registro em log e detecção de ameaças, análise forense e remediação de ataque e erradicação

-   Processos para lidar com incidentes e atividades pós-incidente, como lições aprendidas e retenção de evidências

Observação: sua abordagem de registro em log e detecção de ameaças para nuvem e local pode ser diferente dependendo de vários fatores, como requisito de conformidade, panorama de ameaças e capacidade de detecção e correção. 

- [Benchmark de segurança do Azure-registro em log e detecção de ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Benchmark de segurança do Azure-resposta de incidente](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Prática recomendada de segurança do Azure 4-Process. Atualizar processos de resposta a incidentes para a nuvem](https://aka.ms/AzSec11)

- [Guia de decisão da estrutura de adoção, registro em log e relatórios do Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gerenciamento e monitoramento do Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-backup-and-recovery-strategy"></a>GS-7: definir a estratégia de backup e recuperação

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| GS-7 | 10 | CP |

Estabeleça uma estratégia de backup e recuperação do Azure para sua organização. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   As definições de RTO (objetivo de tempo de recuperação) e RPO (objetivo de ponto de recuperação) de acordo com seus objetivos de resiliência de negócios

-   Design de redundância em sua configuração de infraestrutura e aplicativos

-   Proteção de backup usando controle de acesso e criptografia de dados

Observação: sua abordagem de backup e recuperação para nuvem e local pode ser diferente dependendo dos vários fatores, como redundância de infraestrutura, modelo de hospedagem/serviço de aplicativo e requisitos de conformidade.

- [Benchmark de segurança do Azure-backup e recuperação](/azure/security/benchmarks/security-controls-v2-backup-recovery)

- [Estrutura de arquitetura de boa qualidade do Azure-backup e recuperação de desastre para aplicativos do Azure](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Estrutura de adoção do Azure-continuidade dos negócios e recuperação de desastre](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente**:

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

