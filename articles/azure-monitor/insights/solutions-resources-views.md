---
title: Modos de exibição em soluções de gerenciamento | Microsoft Docs
description: 'Normalmente, as soluções de gerenciamento incluirão uma ou mais exibições para visualizar os dados.  Este artigo descreve como exportar uma exibição criada pelo Designer de Exibição e incluí-la em uma solução de gerenciamento. '
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 473d10bbec6ca056554f7223800a32e9ca93578e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553909"
---
# <a name="views-in-management-solutions-preview"></a>Exibições em soluções de gerenciamento (versão prévia)
> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento que estão atualmente em versão prévia. Qualquer esquema descrito abaixo está sujeito a alterações.    


Normalmente, as [soluções de gerenciamento](solutions.md) incluirão uma ou mais exibições para visualizar os dados.  Este artigo descreve como exportar uma exibição criada pelo [Designer de Exibição](../../azure-monitor/platform/view-designer.md) e incluí-la em uma solução de gerenciamento.  

> [!NOTE]
> Os exemplos neste artigo usam parâmetros e variáveis que são necessários ou comuns para soluções de gerenciamento e são descritos em [projetar e criar uma solução de gerenciamento no Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já esteja familiarizado com o modo para [criar uma solução de gerenciamento](solutions-creating.md) e com a estrutura de um arquivo de solução.

## <a name="overview"></a>Visão Geral
Para incluir uma exibição em uma solução de gerenciamento, crie um **recurso** no [arquivo de solução](solutions-creating.md).  O JSON que descreve a configuração detalhada da exibição geralmente é complexo e não algo que um autor típico de solução seria capaz de criar manualmente.  O método mais comum é criar a exibição usando o [Designer de Exibição](../../azure-monitor/platform/view-designer.md), exportá-la e, em seguida, adicionar sua configuração detalhada na solução.

As etapas básicas para adicionar uma exibição a uma solução são da seguinte maneira.  Cada etapa é descrita mais detalhadamente nas seções a seguir.

1. Exportar a exibição para um arquivo.
2. Criar o recurso de exibição na solução.
3. Adicionar os detalhes da exibição.

## <a name="export-the-view-to-a-file"></a>Exportar a exibição para um arquivo
Siga as instruções em [Log Analytics View Designer](../../azure-monitor/platform/view-designer.md) (Designer de exibição do Log Analytics) para exportar uma exibição para um arquivo.  O arquivo exportado será no formato JSON com os mesmos [elementos do arquivo de solução](solutions-solution-file.md).  

O elemento **Resources** do arquivo de exibição terá um recurso com um tipo de **Microsoft. OperationalInsights/Workspaces** que representa o espaço de trabalho log Analytics.  Esse elemento terá um subelemento com um tipo de **views**, que representa a exibição e contém a configuração detalhada.  Você copiará os detalhes desse elemento e, em seguida, vai copiá-lo em sua solução.

## <a name="create-the-view-resource-in-the-solution"></a>Criar o recurso de exibição na solução
Adicione o seguinte recurso de exibição ao elemento **resources** do seu arquivo de solução.  Ele usa as variáveis descritas abaixo, que você também deverá adicionar.  Observe que as propriedades **Dashboard** e **OverviewTile** são espaços reservados que serão substituídos pelas propriedades correspondentes do arquivo de exibição exportado.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Adicione as variáveis a seguir ao elemento de variáveis do arquivo de solução e substitua os valores pelos da solução.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Observe que você poderá copiar todo o recurso de exibição do arquivo de exibição exportado, mas precisará fazer as seguintes alterações para que ele funcione em sua solução.  

* O **type** para o recurso de exibição precisa ser alterado de **views** para **Microsoft.OperationalInsights/workspaces**.
* A propriedade **name** para o recurso de exibição precisa ser alterada para incluir o nome do workspace.
* A dependência no espaço de trabalho precisa ser removida, uma vez que o recurso workspace não foi definido na solução.
* A propriedade **DisplayName** precisa ser adicionada à exibição.  **Id**, **Name** e **DisplayName** devem ser compatíveis.
* Os nomes dos parâmetros devem ser alterados para coincidirem com o conjunto de parâmetros exigido.
* As variáveis devem ser definidas na solução e usadas nas propriedades adequadas.

### <a name="log-analytics-api-version"></a>Versão da API Log Analytics
Todos os Log Analytics recursos definidos em um modelo do Resource Manager têm uma propriedade **apiVersion** que define a versão da API que o recurso deve usar.  Esta versão é diferente para exibições com consultas que usam a [linguagem de consulta herdada e atualizada](../../azure-monitor/log-query/log-query-overview.md).  

 A tabela a seguir especifica as versões de API Log Analytics para exibições em espaços de trabalho herdados e atualizados: 

| Versão do espaço de trabalho | Versão da API | Consulta |
|:---|:---|:---|
| V1 (Herdado)   | 2015-11-01-visualização | Formato herdado.<br> Exemplo: tipo = evento EventLevelName = erro  |
| v2 (atualizado) | 2015-11-01-visualização | Formato herdado.  Convertido em formato atualizado na instalação.<br> Exemplo: tipo = evento EventLevelName = erro<br>Convertido em: evento &#124; em que EventLevelName = = "Error"  |
| v2 (atualizado) | 2017-03-03-visualização | Formato de atualização. <br>Exemplo: evento &#124; em que EventLevelName = = "Error"  |


## <a name="add-the-view-details"></a>Adicionar os detalhes de exibição
O recurso de exibição no arquivo de exibição exportado terá dois elementos no elemento **properties** chamados **Dashboard** e **OverviewTile**, que terão a configuração detalhada da exibição.  Copie esses dois elementos e seus conteúdos no elemento **properties** do recurso de exibição em seu arquivo de solução.

## <a name="example"></a>Exemplo
Por exemplo, a amostra a seguir apresenta um arquivo de solução simples com uma exibição.  As reticências (...) são mostradas para os conteúdos de **Dashboard** e **OverviewTile** por motivos de espaço.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Próximos passos
* Conheça os detalhes completos de criação das [soluções de gerenciamento](solutions-creating.md).
* Incluir os [runbooks de automação na solução de gerenciamento](solutions-resources-automation.md).
