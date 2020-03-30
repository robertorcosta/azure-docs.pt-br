---
title: Azure Monitor Workbooks e Modelos de Gerenciador de Recursos do Azure
description: Simplifique relatórios complexos com livros de trabalho do Monitor Azure pré-construídos e personalizados implantados através de modelos de gerenciadores de recursos do Azure
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658397"
---
# <a name="programmatically-manage-workbooks"></a>Gerenciar programáticamente livros de trabalho

Os proprietários de recursos têm a opção de criar e gerenciar suas planilhas de forma programática através de modelos do Gerenciador de Recursos. 

Isso pode ser útil em cenários como:
* Implantação de relatórios de análise seletivas ou específicas de domínio, juntamente com implantações de recursos. Por exemplo, você pode implantar livros de trabalho específicos de desempenho e falha de org para seus novos aplicativos ou máquinas virtuais.
* Implantação de relatórios ou painéis padrão usando livros de trabalho para recursos existentes.

A carteira de trabalho será criada no sub/grupo de recursos desejado e com o conteúdo especificado nos modelos do Gerenciador de recursos.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Modelo do Azure Resource Manager para implantação de livros de trabalho
1. Abra uma carteira de trabalho que você deseja implantar programáticamente.
2. Alterne a carteira de trabalho para editar o modo clicando no item _Editar_ barra de ferramentas.
3. Abra o _Editor_ _</>_ Avançado usando o botão na barra de ferramentas.
4. No editor, alterne _o tipo de modelo_ para o modelo do _Gerenciador de recursos_.
5. O modelo de gerenciador de recursos para criar aparece no editor. Copie o conteúdo e use como está ou mescle-o com um modelo maior que também implante o recurso de destino.

    ![Imagem mostrando como obter o modelo de Gerenciador de recursos dentro da ui da carteira de trabalho](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Modelo de gerenciador de recursos do Azure
Este modelo mostra como implantar uma carteira de trabalho simples que exibe um 'Hello World!'
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Parâmetros de modelo

| Parâmetro | Explicação |
| :------------- |:-------------|
| `workbookDisplayName` | O nome amigável para a carteira de trabalho que é usada na Galeria ou lista salva. Precisa ser único no escopo do grupo de recursos e fonte |
| `workbookType` | A galeria em que a cartilha será mostrada. Os valores suportados `tsg`incluem pasta de trabalho, Monitor Azure, etc. |
| `workbookSourceId` | O ID da instância de recurso à qual a caderneta de trabalho será associada. A nova carteira de trabalho aparecerá relacionada a essa instância de recurso - por exemplo, na tabela de conteúdo do recurso em _Workbook_. Se você quiser que sua pasta de trabalho apareça na galeria da pasta de trabalho no Azure Monitor, use a string _Azure Monitor_ em vez de um ID de recurso. |
| `workbookId` | A orientação única para esta instância da carteira de trabalho. Use _[newGuid()]_ para criar automaticamente uma nova guia. |
| `kind` | Usado para especificar se a carteira de trabalho criada é compartilhada ou privada. Use o valor _compartilhado_ para livros de trabalho compartilhados e _usuário_ para os privados. |
| `location` | A localização do Azure onde a pasta de trabalho será criada. Use _[resourceGroup().location]_ para criá-lo no mesmo local que o grupo de recursos |
| `serializedData` | Contém o conteúdo ou a carga útil a ser usado na caderneta de trabalho. Use o modelo gerenciador de recursos da ui da papelada para obter o valor |

### <a name="workbook-types"></a>Tipos de carteiras de trabalho
Os tipos de livros de trabalho especificam em qual tipo de galeria da carteira de trabalho a nova instância da carteira de trabalho aparecerá. As opções incluem:

| Type | Localização da galeria |
| :------------- |:-------------|
| `workbook` | O padrão usado na maioria dos relatórios, incluindo a galeria Workbooks of Application Insights, Azure Monitor, etc.  |
| `tsg` | A galeria Guias de Solução de Problemas em Insights de Aplicativos |
| `usage` | A _galeria Mais_ em _Uso_ em Insights de Aplicativos |

### <a name="limitations"></a>Limitações
Por uma razão técnica, esse mecanismo não pode ser usado para criar instâncias de carteira de trabalho na galeria _De livros_ de trabalho de Insights de Aplicativos. Estamos trabalhando para resolver essa limitação. Enquanto isso, recomendamos que você use a galeria Guia `tsg`de Solução de Problemas (workbookType:) para implantar livros de trabalho relacionados ao Application Insights.

## <a name="next-steps"></a>Próximas etapas

Explore como as livros de trabalho estão sendo usadas para alimentar a nova [experiência do Azure Monitor for Storage](../insights/storage-insights-overview.md).

