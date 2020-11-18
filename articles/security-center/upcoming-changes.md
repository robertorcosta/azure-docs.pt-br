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
ms.date: 11/09/2020
ms.author: memildin
ms.openlocfilehash: df863372cbf7abfb6fee145b7d13bb00d8deb074
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380153"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Futuras alterações importantes na Central de Segurança do Azure

> [!IMPORTANT]
> As informações nesta página estão relacionadas aos produtos ou recursos de pré-lançamento e podem ser substancialmente modificadas antes de serem lançadas comercialmente, caso sejam. A Microsoft não assume nenhum compromisso nem faz garantias, expressas nem implícitas, quanto às informações fornecidas daqui em diante.

Nesta página, você aprenderá sobre as alterações planejadas para a Central de Segurança. Ela descreve as modificações planejadas para o produto que podem afetar coisas como a classificação de segurança ou os fluxos de trabalho.

Se você estiver procurando as notas sobre a versão mais recentes, poderá encontrá-las no artigo [Novidades na Central de Segurança do Azure](release-notes.md).


## <a name="planned-changes"></a>Alterações planejadas

### <a name="system-updates-should-be-installed-on-your-machines-recommendation-getting-sub-recommendations"></a>Recomendação "As atualizações do sistema devem ser instaladas nos computadores" obtendo sub-recomendações

#### <a name="summary"></a>Resumo

| Aspecto | Detalhes |
|---------|---------|
|Data do comunicado | 9 de novembro de 2020  |
|Data desta alteração  |  Meados de novembro de 2020 |
|Impacto     | Durante a transição da versão atual desta recomendação para a versão substituta, a classificação de segurança pode ser alterada. |
|  |  |

Estamos lançando uma versão aprimorada da recomendação **As atualizações do sistema devem ser instaladas nos computadores**. A nova versão *substituirá* a versão atual no controle de segurança para aplicar atualizações do sistema e proporcionará os seguintes aprimoramentos:

- Sub-recomendações para cada atualização ausente
- Uma experiência reformulada nas páginas da Central de Segurança do Azure do portal do Azure
- Dados aprimorados para a recomendação do Azure Resource Graph

#### <a name="transition-period"></a>Período de transição

Haverá um período de transição de 36 h (aproximadamente). Para minimizar possíveis interrupções, agendamos a atualização para ocorrer em um fim de semana. Durante a transição, as pontuações seguras podem ser afetadas.

#### <a name="redesigned-portal-experience"></a>Experiência de portal reprojetada

A página de detalhes da recomendação para **As atualizações do sistema devem ser instaladas nos computadores** inclui a lista de conclusões, conforme mostrado abaixo. Quando você seleciona uma localização individual, o painel de detalhes é aberto com um link para as informações de correção e uma lista de recursos afetados.

:::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Abrindo uma das sub-recomendações na experiência do portal para a recomendação atualizada":::


#### <a name="richer-data-from-azure-resource-graph"></a>Dados mais avançados do Azure Resource Graph

O Azure Resource Graph é um serviço no Azure projetado para fornecer exploração eficiente de recursos. Você pode usar o ARG para consultar em escala um determinado conjunto de assinaturas a fim de controlar seu ambiente de maneira eficaz. 

Para a Central de Segurança do Azure, você pode usar ARG e [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) para consultar uma ampla gama de dados de postura de segurança.

Se você consultar a versão atual da recomendação **As atualizações do sistema devem ser instaladas nos computadores**, a única informação disponível do ARG é que a recomendação precisa ser corrigida em um computador. Quando a versão atualizada for liberada, a consulta a seguir retornará cada atualização de sistema ausente agrupada por computador.

```kusto
securityresources
| where type =~ "microsoft.security/assessments/subassessments"
| where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
| where properties.status.code == "Unhealthy"
```

## <a name="next-steps"></a>Próximas etapas

Para ver todas as alterações recentes feitas no produto, confira as [Novidades na Central de Segurança do Azure](release-notes.md).