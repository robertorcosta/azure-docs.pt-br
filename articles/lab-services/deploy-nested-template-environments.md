---
title: Implantar ambientes de modelo aninhados no Azure DevTest Labs
description: Saiba como implantar modelos aninhados do Azure Resource Manager para fornecer ambientes com o Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e83bc4e77a44f20d55fa3b56bc81aefd1d25bb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168831"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Implantar modelos aninhados do Azure Resource Manager para testar ambientes
Uma implantação aninhada permite que você execute outros modelos do Azure Resource Manager a partir de um modelo principal do Gerenciador de recursos. Ele permite que você decomponha sua implantação em um conjunto de modelos específicos e específicos para propósitos. Ele oferece benefícios em termos de teste, reutilização e legibilidade. O artigo [Usar modelos vinculados ao implantar recursos do Azure](../azure-resource-manager/templates/linked-templates.md) fornece uma boa visão geral desta solução com várias amostras de código. Este artigo fornece um exemplo específico do Azure DevTest Labs. 

## <a name="key-parameters"></a>Parâmetros-chave
Embora você possa criar seu próprio modelo de Gerenciador de recursos do zero, recomendamos que você use o [projeto Azure Resource Group](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) no Visual Studio, o que facilita o desenvolvimento e depuração de modelos. Quando você adiciona um recurso de implantação aninhado ao azuredeploy.json, o Visual Studio adiciona vários itens para tornar o modelo mais flexível. Esses itens incluem a subpasta com o arquivo de modelo si e parâmetros, nomes de variáveis dentro do arquivo de modelo principal e dois parâmetros para o local de armazenamento dos novos arquivos. Os **_artifactsLocation** e **_artifactsLocationSasToken** são os principais parâmetros que os DevTest Labs usam. 

Se você não estiver familiarizado com como o DevTest Labs funciona com ambientes, consulte [Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md). Seus modelos são armazenados no repositório vinculado ao laboratório em DevTest Labs. Quando você cria um novo ambiente com esses modelos, os arquivos são movidos para um contêiner de armazenamento Azure no laboratório. Para poder identificar e copiar os arquivos aninhados, o DevTest Labs identifica os parâmetros _artifactsLocation e _artifactsLocationSasToken e copia as subpastas para o recipiente de armazenamento. Em seguida, ele insere automaticamente o local e o token SaS (SaS) de assinatura de acesso compartilhado em parâmetros. 

## <a name="nested-deployment-example"></a>Exemplo de implantação aninhada
Aqui está um exemplo simples de uma implantação aninhada:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

A pasta no repositório que contém este `nestedtemplates` modelo tem uma subpasta com os arquivos **NestOne.json** e **NestOne.parameters.json**. No **azuredeploy.json**, URI para o modelo é construído usando o local de artefatos, pasta de modelo aninhada, nome de arquivo de modelo aninhado. Da mesma forma, o URI para os parâmetros é construído usando a localização dos artefatos, pasta de modelo aninhada e arquivo de parâmetros para o modelo aninhado. 

Aqui está a imagem da mesma estrutura do projeto no Visual Studio: 

![Estrutura do projeto no Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Você pode adicionar pastas adicionais na pasta principal, mas não mais profunda do que um único nível. 

## <a name="next-steps"></a>Próximas etapas
Veja os seguintes artigos para obter detalhes sobre ambientes: 

- [Criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e usar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Conecte um ambiente à rede virtual do seu laboratório no Azure DevTest Labs](connect-environment-lab-virtual-network.md)