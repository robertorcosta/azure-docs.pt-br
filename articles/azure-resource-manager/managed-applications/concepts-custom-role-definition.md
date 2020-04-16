---
title: Visão geral das definições de função personalizadas
description: Descreve o conceito de criar definições de função personalizadas para aplicativos gerenciados.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7b7fff913c177703f959bfa103c8e310d01059e2
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391834"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Artefato de definição de função personalizado em aplicativos gerenciados do Azure

A definição de função personalizada é um artefato opcional em aplicativos gerenciados. Ele é usado para determinar quais permissões o aplicativo gerenciado precisa para executar suas funções.

Este artigo fornece uma visão geral do artefato de definição de função personalizado e suas capacidades.

## <a name="custom-role-definition-artifact"></a>Artefato de definição de função personalizado

Você precisa nomear o artefato de definição de função personalizado costumeRoleDefinition.json. Coloque-o no mesmo nível que createUiDefinition.json e mainTemplate.json no pacote .zip que cria uma definição de aplicativo gerenciada. Para saber como criar o pacote .zip e publicar uma definição de aplicativo gerenciada, consulte [Publicar uma definição de aplicativo gerenciada.](publish-service-catalog-app.md)

## <a name="custom-role-definition-schema"></a>Esquema de definição de função personalizado

O arquivo customRoleDefinition.json tem `roles` uma propriedade de alto nível que é uma matriz de funções. Essas funções são as permissões que o aplicativo gerenciado precisa para funcionar. Atualmente, apenas funções incorporadas são permitidas, mas você pode especificar várias funções. Uma função pode ser referenciada pelo ID da definição da função ou pelo nome da função.

Amostra json para definição de função personalizada:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>Funções

Um papel é composto `$.properties.roleName` de `id`um ou um:

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> Você pode usar `id` `roleName` o campo ou o campo. Apenas um é necessário. Esses campos são usados para procurar a definição de função que deve ser aplicada. Se ambos forem `id` fornecidos, o campo será utilizado.

|Propriedade|Obrigatório?|Descrição|
|---------|---------|---------|
|id|Sim|A id do papel embutido. Você pode usar o ID completo ou apenas o GUID.|
|Rolename|Sim|O nome do papel embutido.|