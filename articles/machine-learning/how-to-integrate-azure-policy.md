---
title: Auditar e gerenciar a conformidade com o Azure Policy
titleSuffix: Azure Machine Learning
description: Saiba como usar Azure Policy para usar políticas internas para Azure Machine Learning.
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: e7eebb22efe32b290e078348337049c6c3e762db
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994082"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Auditar e gerenciar Azure Machine Learning usando Azure Policy

[Azure Policy](/azure/governance/policy) é uma ferramenta de governança que permite garantir que os recursos do Azure estejam em conformidade com suas políticas. Com Azure Machine Learning, você pode atribuir as seguintes políticas:

* **Chave gerenciada pelo cliente**: auditar ou impor se os espaços de trabalho devem usar uma chave gerenciada pelo cliente.
* **Link privado**: auditar se os espaços de trabalho usam um ponto de extremidade privado para se comunicar com uma rede virtual.

As políticas podem ser definidas em escopos diferentes, como no nível de assinatura ou grupo de recursos. Para obter mais informações, consulte a [documentação do Azure Policy](/azure/governance/policy/overview).

## <a name="built-in-policies"></a>Políticas internas

Azure Machine Learning fornece um conjunto de políticas que você pode usar para cenários comuns com Azure Machine Learning. Você pode atribuir essas definições de política à sua assinatura existente ou usá-las como base para criar suas próprias definições personalizadas. Para obter uma lista completa das políticas internas para Azure Machine Learning, consulte [políticas internas para Azure Machine Learning](/azure/governance/policy/samples/built-in-policies#machine-learning).

Para exibir as definições de política internas relacionadas a Azure Machine Learning, use as seguintes etapas:

1. Vá para __Azure Policy__ na [portal do Azure](https://portal.azure.com).
1. Selecione __definições__.
1. Para __tipo__, selecione _interno_e para __categoria__, selecione __Machine Learning__.

A partir daqui, você pode selecionar as definições de política para exibi-las. Ao exibir uma definição, você pode usar o link __atribuir__ para atribuir a política a um escopo específico e configurar os parâmetros para a política. Para obter mais informações, consulte [atribuir uma política-portal](/azure/governance/policy/assign-policy-portal).

Você também pode atribuir políticas usando [Azure PowerShell](/azure/governance/policy/assign-policy-powershell), [CLI do Azure](https://docs.microsoft.com/azure/governance/policy/assign-policy-azurecli)e [modelos](/azure/governance/policy/assign-policy-template).

## <a name="workspaces-encryption-with-customer-managed-key"></a>Criptografia de espaços de trabalho com chave gerenciada pelo cliente

Controla se os espaços de trabalho devem ser criptografados com uma chave gerenciada pelo cliente (CMK) ou usando uma chave gerenciada pela Microsoft para criptografar métricas e metadados. Para obter mais informações sobre como usar o CMK, consulte a seção [Azure Cosmos DB](concept-enterprise-security.md#azure-cosmos-db) do artigo de segurança da empresa.

Para configurar essa política, defina o parâmetro de efeito como __auditoria__ ou __negar__. Se definido como __auditoria__, você poderá criar espaços de trabalho sem um CMK e um evento de aviso será criado no log de atividades.

Se a política for definida como __Deny__, você não poderá criar um espaço de trabalho a menos que ele ESPECIFIQUE um CMK. A tentativa de criar um espaço de trabalho sem um CMK resulta em um erro semelhante a `Resource 'clustername' was disallowed by policy` e cria um erro no log de atividades. O identificador de política também é retornado como parte desse erro.

## <a name="workspaces-should-use-private-link"></a>Os espaços de trabalho devem usar o link privado

Controla se os espaços de trabalho devem usar o link privado do Azure para se comunicar com a rede virtual do Azure. Para obter mais informações sobre como usar o link privado, consulte [Configurar link privado para um espaço de trabalho](how-to-configure-private-link.md).

Para configurar essa política, defina o parâmetro de efeito como __auditoria__. Se você criar um espaço de trabalho sem usar o link privado, um evento de aviso será criado no log de atividades.

## <a name="next-steps"></a>Próximas etapas

* [Documentação do Azure Policy](/azure/governance/policy/overview)
* [Políticas internas para Azure Machine Learning](policy-reference.md)
* [Trabalhando com políticas de segurança com a central de segurança do Azure](/azure/security-center/tutorial-security-policy)