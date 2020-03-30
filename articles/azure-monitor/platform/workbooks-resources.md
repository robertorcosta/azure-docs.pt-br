---
title: Parâmetros de recursos de livros de trabalho do Azure Monitor
description: Simplifique a emissão de relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cc2cde7932f783f63ee2783f0589ce4f88f248a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658095"
---
# <a name="workbook-resource-parameters"></a>Parâmetros de recursos da carteira de trabalho

Os parâmetros de recursos permitem a seleção de recursos em livros de trabalho. Isso é útil na definição do escopo a partir do qual obter os dados. Um exemplo é permitir que os usuários selecionem o conjunto de VMs, que os gráficos posteriormente usarão ao apresentar os dados.

Os valores dos seletores de recursos podem vir do contexto da pasta de trabalho, lista estática ou das consultas do Gráfico de Recursos do Azure.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Criação de um parâmetro de recursos (recursos da carteira de trabalho)
1. Comece com uma carteira de trabalho vazia no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro da caderneta de trabalho.
3. Clique no botão adicionar _parâmetro_ azul.
4. No novo painel de parâmetros que aparece digite:
    1. Nome do parâmetro:`Applications`
    2. Tipo de parâmetro:`Resource picker`
    3. Necessário:`checked`
    4. Permitir várias seleções:`checked`
5. Obter dados de:`Workbook Resources`
6. Inclua apenas tipos de recursos:`Application Insights`
7. Escolha 'Salvar' na barra de ferramentas para criar o parâmetro.

![Imagem mostrando a criação de um parâmetro de recursos usando recursos da carteira de trabalho](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Criação de um parâmetro de recursos (Gráfico de recursos do Azure)
1. Comece com uma carteira de trabalho vazia no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro da caderneta de trabalho.
3. Clique no botão adicionar _parâmetro_ azul.
4. No novo painel de parâmetros que aparece digite:
    1. Nome do parâmetro:`Applications`
    2. Tipo de parâmetro:`Resource picker`
    3. Necessário:`checked`
    4. Permitir várias seleções:`checked`
5. Obter dados de:`Query`
    1. Tipo de consulta:`Azure Resource Graph`
    2. Assinaturas:`Use default subscriptions`
    3. No controle de consulta, adicione este trecho
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Escolha 'Salvar' na barra de ferramentas para criar o parâmetro.

![Imagem mostrando a criação de um parâmetro de recurso usando o Gráfico de Recursos do Azure](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> O Gráfico de Recursos do Azure ainda não está disponível em todas as nuvens. Certifique-se de que ele é suportado em sua nuvem de destino se você escolher essa abordagem.

[Documentação do Gráfico de Recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>Criação de um parâmetro de recursos (lista JSON)
1. Comece com uma carteira de trabalho vazia no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro da caderneta de trabalho.
3. Clique no botão adicionar _parâmetro_ azul.
4. No novo painel de parâmetros que aparece digite:
    1. Nome do parâmetro:`Applications`
    2. Tipo de parâmetro:`Resource picker`
    3. Necessário:`checked`
    4. Permitir várias seleções:`checked`
5. Obter dados de:`JSON`
    1. No controle de conteúdo, adicione este trecho de json
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Aperte o botão azul _Atualizar._
6. Definir opcionalmente `Include only resource types` o _'Insights_ de aplicativos'
7. Escolha 'Salvar' na barra de ferramentas para criar o parâmetro.

## <a name="referencing-a-resource-parameter"></a>Fazendo referência a um parâmetro de recursos
1. Adicione um controle de consulta à caderneta de trabalho e selecione um recurso Application Insights.
2. Use o _aplicativo Insights_ para baixar para vincular o parâmetro ao controle. Isso define o escopo da consulta para os recursos retornados pelo parâmetro no tempo de execução.
4. No controle KQL, adicione este trecho
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Executar consulta para ver os resultados. 

![Imagem mostrando um parâmetro de recurso referenciado em um controle de consulta](./media/workbooks-resources/resource-reference.png)

> Essa abordagem pode ser usada para vincular recursos a outros controles, como métricas.

## <a name="resource-parameter-options"></a>Opções de parâmetros de recursos
| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `{Applications}` | O ID de recurso selecionado | _/subscriptions/<sub-id>/resourceGroups/<grupo de recursos>/provedores/<autenticação do tipo de recurso>/acme_ |
| `{Applications:label}` | O rótulo do recurso selecionado | `acmefrontend` |
| `{Applications:value}` | O valor do recurso selecionado | _'/assinaturas/<subid>/resourceGroups/<grupo de recursos>/provedores/<autenticação do tipo de recurso>/acme'._ |
| `{Applications:name}` | O nome do recurso selecionado | `acmefrontend` |
| `{Applications:resourceGroup}` | O grupo de recursos do recurso selecionado | `acmegroup` |
| `{Applications:resourceType}` | O tipo do recurso selecionado | _microsoft.insights/componentes_ |
| `{Applications:subscription}` | A assinatura do recurso selecionado |  |
| `{Applications:grid}` | Uma grade mostrando as propriedades dos recursos. Útil para renderizar em um bloco de texto durante a depuração  |  |

## <a name="next-steps"></a>Próximas etapas

* [Comece a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções ricas de visualizações.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da sua carteira de trabalho.
