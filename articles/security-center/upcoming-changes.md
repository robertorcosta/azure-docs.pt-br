---
title: Alterações importantes chegando à Central de Segurança do Azure
description: Futuras alterações na Central de Segurança do Azure às quais você talvez precise estar atento e que podem exigir algum planejamento
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2020
ms.author: memildin
ms.openlocfilehash: 052758079d8d413f7b0fead2a5abf3b47b9a691e
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511324"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Futuras alterações importantes na Central de Segurança do Azure

> [!IMPORTANT]
> As informações nesta página estão relacionadas aos produtos ou recursos de pré-lançamento e podem ser substancialmente modificadas antes de serem lançadas comercialmente, caso sejam. A Microsoft não assume nenhum compromisso nem faz garantias, expressas nem implícitas, quanto às informações fornecidas daqui em diante.

Nesta página, você aprenderá sobre as alterações planejadas para a Central de Segurança. Ela descreve as modificações planejadas para o produto que podem afetar coisas como a classificação de segurança ou os fluxos de trabalho.

Se você estiver procurando as notas sobre a versão mais recentes, poderá encontrá-las no artigo [Novidades na Central de Segurança do Azure](release-notes.md).


## <a name="planned-changes"></a>Alterações planejadas

- [Recursos "Não aplicáveis" a serem relatados como "Em conformidade" nas avaliações do Azure Policy](#not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments)
- [35 recomendações de versão prévia adicionadas para aumentar a cobertura do Azure Security Benchmark](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)

### <a name="not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments"></a>Recursos "Não aplicáveis" a serem relatados como "Em conformidade" nas avaliações do Azure Policy

**Data estimada da alteração:** Janeiro de 2021

Atualmente, os recursos avaliados para uma recomendação e que foram considerados **não aplicáveis** são exibidos no Azure Policy como "Fora de conformidade". Nenhuma ação do usuário pode alterar o estado deles para "Em conformidade". Após essa alteração planejada, eles serão relatados como "Em conformidade" para maior clareza.

O único impacto será visto no Azure Policy, em que o número de recursos em conformidade aumentará. Não haverá nenhum impacto na sua classificação de segurança na Central de Segurança do Azure.

### <a name="35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark"></a>35 recomendações de versão prévia adicionadas para aumentar a cobertura do Azure Security Benchmark

**Data estimada da alteração:** Dezembro de 2020

O Azure Security Benchmark é um conjunto específico de diretrizes específicas do Azure criadas pela Microsoft com as melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md).

As 35 recomendações de versão prévia a seguir serão adicionadas à Central de Segurança para aumentar a cobertura desse parâmetro de comparação.

As recomendações de versão prévia não processam um recurso não íntegro e não são incluídas nos cálculos de sua classificação de segurança. Corrija-as sempre que possível, para que, quando o período da versão prévia terminar, elas contribuam para sua classificação. Saiba mais sobre como responder a essas recomendações em [Recomendações de correção na Central de Segurança do Azure](security-center-remediate-recommendations.md).

| Controle de segurança                     | Novas recomendações                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Habilitar a criptografia em repouso            | – As contas do Azure Cosmos DB devem usar chaves gerenciadas pelo cliente para criptografar os dados inativos<br>– Os workspaces do Azure Machine Learning devem ser criptografados com uma CMK (chave gerenciada pelo cliente)<br>– A proteção de dados do Bring Your Own Key será habilitada para servidores MySQL<br>– A proteção de dados do Bring Your Own Key será habilitada para servidores PostgreSQL<br>– As contas dos Serviços Cognitivos devem habilitar a criptografia de dados com uma CMK (chave gerenciada pelo cliente)<br>– Os registros de contêiner devem ser criptografados com uma CMK (chave gerenciada pelo cliente)<br>– As instâncias gerenciadas de SQL devem usar chaves gerenciadas pelo cliente para criptografar dados inativos<br>– Os SQL Servers devem usar chaves gerenciadas pelo cliente para criptografar dados inativos<br>– As contas de armazenamento devem usar uma CMK (chave gerenciada pelo cliente) para criptografia                                                                                                                                                              |
| Implementar melhores práticas de segurança    | – As assinaturas devem ter um endereço de email de contato para problemas de segurança<br> – O provisionamento automático do agente do Log Analytics será habilitado na sua assinatura<br> – A notificação por email para alertas de alta severidade deve ser habilitada<br> – A notificação por email para o proprietário da assinatura para alertas de alta severidade deve ser habilitada<br> – Os cofres de chaves devem ter a proteção contra limpeza habilitada<br> – Os cofres de chaves devem ter a exclusão temporária habilitada |
| Gerenciar acesso e permissões        | – Os aplicativos de funções devem ter a opção 'Certificados do Cliente (Certificados do cliente de entrada)' habilitada |
| Proteger os aplicativos contra DDoS | – O WAF (Firewall de Aplicativo Web) deve ser habilitado para o Gateway de Aplicativo<br> – O WAF (Firewall de Aplicativo Web) deve ser habilitado para o Azure Front Door Service |
| Restringir acesso não autorizado à rede | – O firewall deve ser habilitado no Key Vault<br> – O ponto de extremidade privado deve ser configurado para o Key Vault<br> – A Configuração de Aplicativos deve usar um link privado<br> – O Cache do Azure para Redis deve residir em uma rede virtual<br> – Os domínios da Grade de Eventos do Azure devem usar um link privado<br> – Os tópicos da Grade de Eventos do Azure devem usar um link privado<br> – Os workspaces do Azure Machine Learning devem usar um link privado<br> – O Serviço do Azure SignalR deve usar um link privado<br> – O Azure Spring Cloud deve usar uma injeção de rede<br> – Os registros de contêiner não devem permitir acesso irrestrito à rede<br> – Os registros de contêiner devem usar um link privado<br> – O acesso à rede pública deve ser desabilitado para servidores MariaDB<br> – O acesso à rede pública deve ser desabilitado para servidores MySQL<br> – O acesso à rede pública deve ser desabilitado para servidores PostgreSQL<br> – A conta de armazenamento deve usar uma conexão de link privado<br> – As contas de armazenamento devem restringir o acesso à rede usando regras de rede virtual<br> – Os modelos do Construtor de Imagens de VM devem usar um link privado|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Links relacionados:

- [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Saiba mais sobre o Banco de Dados do Azure para MariaDB](../mariadb/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para MySQL](../mysql/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para PostgreSQL](../postgresql/overview.md)





## <a name="next-steps"></a>Próximas etapas

Para ver todas as alterações recentes feitas no produto, confira as [Novidades na Central de Segurança do Azure](release-notes.md).