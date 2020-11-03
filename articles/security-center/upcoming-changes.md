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
ms.date: 10/26/2020
ms.author: memildin
ms.openlocfilehash: ba0c68589f7ea4b32970e3ad81fea5175b998dc7
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629146"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Futuras alterações importantes na Central de Segurança do Azure

> [!IMPORTANT]
> As informações nesta página estão relacionadas aos produtos ou recursos de pré-lançamento e podem ser substancialmente modificadas antes de serem lançadas comercialmente, caso sejam. A Microsoft não assume nenhum compromisso nem faz garantias, expressas nem implícitas, quanto às informações fornecidas daqui em diante.

Nesta página, você aprenderá sobre as alterações planejadas para a Central de Segurança. Ela descreve as modificações planejadas para o produto que podem afetar coisas como a classificação de segurança ou os fluxos de trabalho.

Se você estiver procurando as notas sobre a versão mais recentes, poderá encontrá-las no artigo [Novidades na Central de Segurança do Azure](release-notes.md).


## <a name="planned-changes"></a>Alterações planejadas

### <a name="recommendations-related-to-azure-security-benchmark-to-be-added-preview"></a>Recomendações relacionadas ao Azure Security Benchmark a serem adicionados (versão prévia)

| Aspecto | Detalhes |
|---------|---------|
|Data do comunicado | 26 de outubro de 2020  |
|Data desta alteração  |  Novembro de 2020 |
|Impacto     | Potencial de mais recomendações para analisar.<br>Nenhum impacto imediato nas classificação de segurança – As recomendações de versão prévia não afetam a classificação de segurança.<br>Sem impacto imediato no status de integridade de recursos – As recomendações de versão prévia não processam um recurso "Não íntegro".|
|  |  |

O Azure Security Benchmark é um conjunto específico de diretrizes específicas do Azure criadas pela Microsoft com as melhores práticas de segurança e conformidade baseadas em estruturas de conformidade comuns. [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md).

As 29 novas recomendações a seguir serão adicionadas à Central de Segurança para aumentar a cobertura do parâmetro de comparação.

As recomendações de versão prévia não processam um recurso não íntegro e não são incluídas nos cálculos de sua classificação de segurança. Corrija-as sempre que possível, para que, quando o período da versão prévia terminar, elas contribuam para sua classificação. Saiba mais sobre como responder a essas recomendações em [Recomendações de correção na Central de Segurança do Azure](security-center-remediate-recommendations.md).

- O Backup do Azure deve ser habilitado para máquinas virtuais
- A retenção de auditoria para servidores SQL deve ser definida em pelo menos 90 dias
- Os logs de diagnóstico devem ser habilitados no Serviço de Aplicativo 
- 'Impor conexão SSL' deve ser habilitada para servidores de banco de dados MySQL
- 'Impor conexão SSL' deve ser habilitada para servidores de banco de dados PostgreSQL
- O FTPS deve ser exigido no aplicativo de API
- O FTPS deve ser exigido no aplicativo de funções
- O FTPS deve ser exigido em seu aplicativo Web
- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB
- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MySQL
- O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para PostgreSQL
- Java deve estar atualizado com a versão mais recente para o seu aplicativo de API
- Java deve estar atualizado com a versão mais recente para o seu aplicativo de funções
- Java deve estar atualizado com a versão mais recente para o seu aplicativo Web
- A identidade gerenciada deve ser usada no aplicativo de API
- A identidade gerenciada deve ser usada no aplicativo de funções
- A identidade gerenciada deve ser usada no aplicativo Web
- PHP deve estar atualizado com a versão mais recente para o seu aplicativo de API
- PHP deve estar atualizado com a versão mais recente para o seu aplicativo Web
- O ponto de extremidade privado deve ser habilitado para servidores MariaDB
- O ponto de extremidade privado deve ser habilitado para servidores MySQL
- O ponto de extremidade privado deve ser habilitado para servidores PostgreSQL
- Python deve estar atualizado com a versão mais recente para o seu aplicativo de API
- Python deve estar atualizado com a versão mais recente para o seu aplicativo de funções
- Python deve estar atualizado com a versão mais recente para o seu aplicativo Web
- TLS deve estar atualizado com a versão mais recente para o seu aplicativo de API
- Python deve estar atualizado com a versão mais recente para o seu aplicativo de funções
- TLS deve estar atualizado com a versão mais recente para o seu aplicativo Web
- Os aplicativos Web devem solicitar um certificado SSL para todas as solicitações de entrada

Links relacionados:

- [Saiba mais sobre o Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Saiba mais sobre aplicativos de API do Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [Saiba mais sobre aplicativos de funções do Azure](../azure-functions/functions-overview.md)
- [Saiba mais sobre aplicativos Web do Azure](../app-service/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para MariaDB](../mariadb/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para MySQL](../mysql/overview.md)
- [Saiba mais sobre o Banco de Dados do Azure para PostgreSQL](../postgresql/overview.md)

## <a name="next-steps"></a>Próximas etapas

Para ver todas as alterações recentes feitas no produto, confira as [Novidades na Central de Segurança do Azure](release-notes.md).