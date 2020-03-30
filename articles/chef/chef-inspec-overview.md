---
title: Use InSpec para a automação de conformidade de sua infraestrutura do Azure
description: Saiba como usar o InSpec para detectar problemas em implantações do Microsoft Azure
keywords: azure, chef, devops, virtual machines, overview, automate, inspec
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158233"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Use InSpec para a automação de conformidade de sua infraestrutura do Azure

[InSpec](https://www.chef.io/inspec/) é a linguagem de software livre do Chef para descrever as regras de segurança e conformidade que podem ser compartilhadas entre os engenheiros de software, as operações e engenheiros de segurança. O InSpec funciona comparando o estado real do sua infraestrutura com o estado desejado que você expressa no código fácil de ler e fácil de gravar do InSpec. InSpec detecta violações e exibe os resultados na forma de um relatório, mas coloca você no controle de correção.

Você pode usar o InSpec para validar o estado dos recursos e grupos de recursos em uma assinatura, incluindo máquinas virtuais, configurações de rede, configurações do Azure Active Directory e muito mais.

Este artigo descreve os benefícios de usar o InSpec para facilitar a segurança e conformidade no Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Tornar a conformidade fácil de entender e avaliar

A documentação de conformidade escrita em planilhas ou documentos do Word deixa os requisitos abertos à interpretação. Com o InSpec, é possível transformar seus requisitos em código com controle de versão, executável, legível. O código substitui conversas sobre o que deve ser avaliado em favor de testes tangíveis com uma intenção clara.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Detectar problemas de todo o fleet e priorizar a sua correção

O InSpec sem agente detecta o modo que permite a você avaliar, rapidamente e em escala, o seu nível de exposição. Metadados internas para classificação de severidade/impacto ajudam a determinar quais áreas de foco para correção. Você também pode escrever regras rapidamente em resposta a novas vulnerabilidades ou normas governamentais e implantá-las imediatamente.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Auditar máquinas virtuais do Azure com configuração de hóspedes de política

O Azure suporta diretamente o uso de definições do Chef InSpec para auditar máquinas virtuais do Azure através [da Configuração de Hóspedes da Política do Azure](/azure/governance/policy/concepts/guest-configuration). A Configuração do Convidado avalia uma máquina virtual Linux para uma definição do Chef InSpec fornecida e relata a conformidade através da Política Do Azure. Os resultados dessas auditorias também são relatados através de registros do Azure Monitor; ativando alertas e outros cenários de automação.

## <a name="satisfy-audits"></a>Satisfazer auditorias

Com InSpec, você pode responder às perguntas de auditoria a qualquer momento - não apenas em intervalos predeterminados como trimestral ou anual. Executando testes do InSpec continuamente, você entra em um ciclo de auditoria sabendo seu histórico e sua postura de conformidade exatos, em vez de ser surpreendido pelas descobertas de um auditor.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Experimente o InSpec no Azure Cloud Shell](https://shell.azure.com)