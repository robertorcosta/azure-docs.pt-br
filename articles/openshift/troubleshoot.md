---
title: Solução de problemas Azure Red Hat OpenShift
description: Solucionar problemas e resolver problemas comuns com o Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274922"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Solução de problemas para o Azure Red Hat OpenShift

Este artigo detalha alguns problemas comuns encontrados ao criar ou gerenciar os clusters Microsoft Azure Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Rejulgando a criação de um cluster fracassado

Se a criação de um cluster Azure Red Hat OpenShift usando o `az` comando CLI falhar, a tentativa de recriação continuará a falhar.
Use `az openshift delete` para excluir o cluster com falha e crie um cluster totalmente novo.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Grupo de recursos do cluster Hidden Azure Red Hat OpenShift

Atualmente, `Microsoft.ContainerService/openShiftManagedClusters` o recurso que é criado automaticamente pelo Azure CLI (`az openshift create` comando) está escondido no portal Azure. Na **exibição de grupo Recurso,** verifique **Mostrar tipos ocultos** para visualizar o grupo de recursos.

![Captura de tela da caixa de seleção do tipo oculto no portal](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Criar um cluster resulta em erro que nenhum provedor de recursos registrado encontrou

Se a criação de um `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`cluster resulta em um erro que, então você fez parte da pré-visualização e agora precisa [comprar instâncias reservadas de máquina virtual do Azure](https://aka.ms/openshift/buy) para usar o produto geralmente disponível. Uma reserva reduz seus gastos pagando antecipadamente por serviços Azure totalmente gerenciados. Consulte [*o que são reservas do Azure*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) para saber mais sobre reservas e como elas economizam dinheiro.

## <a name="next-steps"></a>Próximas etapas

- Experimente o [Red Hat OpenShift Help Center](https://help.openshift.com/) para obter mais informações sobre a solução de problemas do OpenShift.

- Encontre respostas para [perguntas frequentes sobre o Azure Red Hat OpenShift](openshift-faq.md).
