---
title: Configurar um ponto de extremidade privado (versão prévia)
titleSuffix: Azure Machine Learning
description: Use o link privado do Azure para acessar com segurança seu espaço de trabalho do Azure Machine Learning de uma rede virtual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/28/2020
ms.openlocfilehash: bdb7ba30d9fa2d0bd1eff9368d6e30e516b53895
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192722"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Configurar o link privado do Azure para um espaço de trabalho Azure Machine Learning (versão prévia)

Neste documento, você aprenderá a usar o link privado do Azure com seu espaço de trabalho Azure Machine Learning. 

> [!IMPORTANT]
> O uso do link privado do Azure com o Azure Machine Learning espaço de trabalho está atualmente em visualização pública. Essa funcionalidade só está disponível nas regiões **leste dos EUA** e **oeste dos EUA 2** . Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O link privado do Azure permite que você se conecte ao seu espaço de trabalho usando um ponto de extremidade privado. O ponto de extremidade privado é um conjunto de endereços IP privados na sua rede virtual. Você pode, então, limitar o acesso ao seu espaço de trabalho para ocorrer somente nos endereços IP privados. O link privado ajuda a reduzir o risco de vazamento de dados. Para saber mais sobre pontos de extremidade privados, confira o artigo [Link Privado do Azure](/azure/private-link/private-link-overview).

> [!IMPORTANT]
> O link privado do Azure não afeta o plano de controle do Azure (operações de gerenciamento), como excluir o espaço de trabalho ou gerenciar recursos de computação. Por exemplo, criando, atualizando ou excluindo um destino de computação. Essas operações são executadas pela Internet pública normalmente.
>
> Não há suporte para a versão prévia de instâncias de computação do Azure Machine Learning em um workspace em que o Link Privado está habilitado.
>
> Você pode encontrar problemas ao tentar acessar o ponto de extremidade privado para seu espaço de trabalho se você estiver usando o Mozilla Firefox. Esse problema pode estar relacionado ao DNS sobre HTTPS no Mozilla. É recomendável usar o Microsoft Edge do Google Chrome como uma solução alternativa.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Criar um espaço de trabalho que usa um ponto de extremidade privado

> [!IMPORTANT]
> Atualmente, só há suporte para habilitar um ponto de extremidade privado ao criar um novo espaço de trabalho Azure Machine Learning.

O modelo em [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) pode ser usado para criar um espaço de trabalho com um ponto de extremidade privado.

Para obter informações sobre como usar esse modelo, incluindo pontos de extremidade privados, consulte [usar um modelo de Azure Resource Manager para criar um espaço de trabalho para Azure Machine Learning](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Usando um espaço de trabalho em um ponto de extremidade privado

Como a comunicação com o espaço de trabalho só é permitida a partir da rede virtual, todos os ambientes de desenvolvimento que usam o espaço de trabalho devem ser membros da rede virtual. Por exemplo, uma máquina virtual na rede virtual.

> [!IMPORTANT]
> Para evitar a interrupção temporária da conectividade, a Microsoft recomenda liberar o cache DNS em computadores que se conectam ao espaço de trabalho depois de habilitar o link privado. 

Para obter informações sobre máquinas virtuais do Azure, consulte a [documentação sobre máquinas virtuais](/azure/virtual-machines/).


## <a name="using-azure-storage"></a>Usando o Armazenamento do Azure

Para proteger a conta de armazenamento do Azure usada pelo seu espaço de trabalho, coloque-a dentro da rede virtual.

Para obter informações sobre como colocar a conta de armazenamento na rede virtual, consulte [usar uma conta de armazenamento para seu espaço de trabalho](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace).

> [!WARNING]
> Azure Machine Learning não dá suporte ao uso de uma conta de armazenamento do Azure que tenha o link privado habilitado.

## <a name="using-azure-key-vault"></a>Como usar o Azure Key Vault

Para proteger o Azure Key Vault usado pelo seu espaço de trabalho, você pode colocá-lo na rede virtual ou habilitar o link privado para ele.

Para obter informações sobre como colocar o cofre de chaves na rede virtual, consulte [usar uma instância do Key Vault com seu espaço de trabalho](how-to-enable-virtual-network.md#key-vault-instance).

Para obter informações sobre como habilitar o link privado para o cofre de chaves, consulte [integrar Key Vault com o link privado do Azure](/azure/key-vault/private-link-service).

## <a name="using-azure-kubernetes-services"></a>Usando os serviços Kubernetess do Azure

Para proteger os serviços Kubernetess do Azure usados pelo seu espaço de trabalho, coloque-os dentro de uma rede virtual. Para obter mais informações, consulte [usar os serviços Kubernetess do Azure com seu espaço de trabalho](how-to-enable-virtual-network.md#aksvnet).

Azure Machine Learning agora dá suporte ao uso de um serviço kubernetes do Azure que tem o link privado habilitado.
Para criar um cluster AKS privado, siga os documentos [aqui](https://docs.microsoft.com/azure/aks/private-clusters)

## <a name="azure-container-registry"></a>Registro de Contêiner do Azure

Para obter informações sobre como proteger o registro de contêiner do Azure dentro da rede virtual, consulte [usar o registro de contêiner do Azure](how-to-enable-virtual-network.md#azure-container-registry).

> [!IMPORTANT]
> Se você estiver usando o link privado para seu espaço de trabalho Azure Machine Learning e colocar o registro de contêiner do Azure para seu espaço de trabalho em uma rede virtual, também deverá aplicar o seguinte modelo de Azure Resource Manager. Este modelo permite que seu espaço de trabalho se comunique com o ACR por meio do link privado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como proteger seu espaço de trabalho do Azure Machine Learning, consulte o artigo de [segurança da empresa](concept-enterprise-security.md) .