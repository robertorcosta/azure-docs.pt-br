---
title: Criar políticas de segurança personalizadas na central de segurança do Azure | Microsoft Docs
description: Definições de política personalizada do Azure monitoradas pela central de segurança do Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: memildin
ms.openlocfilehash: d703ea38c39ed556102271ac0cf9a609ce449bc3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195911"
---
# <a name="using-custom-security-policies"></a>Usando políticas de segurança personalizadas

Para ajudar a proteger seus sistemas e ambientes, a central de segurança do Azure gera recomendações de segurança. Essas recomendações são baseadas em práticas recomendadas do setor, que são incorporadas à política de segurança padrão genérica fornecida para todos os clientes. Eles também podem vir do conhecimento da central de segurança de padrões do setor e regulamentadores.

Com esse recurso, você pode adicionar suas próprias iniciativas *personalizadas* . Em seguida, você receberá recomendações se o seu ambiente não seguir as políticas que você criar. Todas as iniciativas personalizadas que você criar aparecerão junto com as iniciativas internas do painel de conformidade regulatória, conforme descrito no tutorial [melhorar a conformidade regulatória](security-center-compliance-dashboard.md).

Conforme discutido na [documentação do Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location), quando você especifica um local para sua iniciativa personalizada, ele deve ser um grupo de gerenciamento ou uma assinatura. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Para adicionar uma iniciativa personalizada à sua assinatura 

1. Na barra lateral da central de segurança, abra a página **política de segurança** .

1. Selecione uma assinatura ou grupo de gerenciamento ao qual você gostaria de adicionar uma iniciativa personalizada.

    [![Selecionando uma assinatura para a qual você criará sua política personalizada](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Você deve adicionar padrões personalizados no nível da assinatura (ou superior) para que eles sejam avaliados e exibidos na central de segurança. 
    >
    > Quando você adiciona um padrão personalizado, ele atribui uma *iniciativa* a esse escopo. Portanto, recomendamos que você selecione o escopo mais largo necessário para essa atribuição.

1. Na página política de segurança, em suas iniciativas personalizadas, clique em **Adicionar uma iniciativa personalizada**.

    [![Clique em * * adicionar uma iniciativa personalizada * *](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    A seguinte página é exibida:

    ![Criar ou adicionar uma política](media/custom-security-policies/create-or-add-custom-policy.png)

1. Na página Adicionar iniciativas personalizadas, examine a lista de políticas personalizadas já criadas em sua organização. Se você vir um que deseja atribuir à sua assinatura, clique em **Adicionar**. Se não houver uma iniciativa na lista que atenda às suas necessidades, pule esta etapa.

1. Para criar uma nova iniciativa personalizada:

    1. Clique em **criar novo**.
    1. Insira o local e o nome da definição.
    1. Selecione as políticas a serem incluídas e clique em **Adicionar**.
    1. Insira os parâmetros desejados.
    1. Clique em **Salvar**.
    1. Na página Adicionar iniciativas personalizadas, clique em atualizar. Sua nova iniciativa será mostrada como disponível.
    1. Clique em **Adicionar** e atribua-o à sua assinatura.

    > [!NOTE]
    > A criação de novas iniciativas requer credenciais de proprietário da assinatura. Para obter mais informações sobre as funções do Azure, consulte [permissões na central de segurança do Azure](security-center-permissions.md).

    Sua nova iniciativa entra em vigor e você pode ver o impacto das duas maneiras a seguir:

    * Na barra lateral da central de segurança, em política & conformidade, selecione **conformidade regulatória**. O painel de conformidade é aberto para mostrar sua nova iniciativa personalizada junto com as iniciativas internas.
    
    * Você começará a receber recomendações se o seu ambiente não seguir as políticas que você definiu.

1. Para ver as recomendações resultantes para sua política, clique em **recomendações** na barra lateral para abrir a página recomendações. As recomendações serão exibidas com um rótulo "personalizado" e estarão disponíveis em aproximadamente uma hora.

    [![Recomendações personalizadas](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

## <a name="enhancing-your-custom-recommendations-with-detailed-information"></a>Aprimorando suas recomendações personalizadas com informações detalhadas

As recomendações internas fornecidas com a central de segurança do Azure incluem detalhes como níveis de severidade e instruções de correção. Se você quiser adicionar esse tipo de informação às suas recomendações personalizadas para que ela apareça no portal do Azure ou onde quer que você acesse suas recomendações, você precisará usar a API REST. 

Os dois tipos de informações que você pode adicionar são:

- **RemediationDescription** – cadeia de caracteres
- **Severidade** – Enumeração [baixa, média, alta]

Os metadados devem ser adicionados à definição de política para uma política que faz parte da iniciativa personalizada. Ele deve estar na propriedade ' securityCenter ', conforme mostrado:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High",
    },
```

Abaixo está um exemplo de uma política personalizada, incluindo a propriedade Metadata/securityCenter:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "remediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "severity": "High",
        },
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

Para obter outro exemplo de como usar a propriedade do securityCenter, consulte [esta seção da documentação da API REST](https://docs.microsoft.com/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples).


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar políticas de segurança personalizadas. 

Para obter outros materiais relacionados, consulte os seguintes artigos: 

- [A visão geral das políticas de segurança](tutorial-security-policy.md)
- [Uma lista das políticas de segurança internas](security-center-policy-definitions.md)