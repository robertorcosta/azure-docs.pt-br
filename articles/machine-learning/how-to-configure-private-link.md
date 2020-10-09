---
title: Configurar um ponto de extremidade privado
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
ms.date: 09/30/2020
ms.openlocfilehash: 4ba7ec73ac70723e21b6acad571d62d14edd250a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828122"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Configurar o link privado do Azure para um espaço de trabalho Azure Machine Learning

Neste documento, você aprenderá a usar o link privado do Azure com seu espaço de trabalho Azure Machine Learning. Para obter informações sobre como criar uma rede virtual para Azure Machine Learning, consulte [visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md)

O link privado do Azure permite que você se conecte ao seu espaço de trabalho usando um ponto de extremidade privado. O ponto de extremidade privado é um conjunto de endereços IP privados na sua rede virtual. Você pode, então, limitar o acesso ao seu espaço de trabalho para ocorrer somente nos endereços IP privados. O link privado ajuda a reduzir o risco de vazamento de dados. Para saber mais sobre pontos de extremidade privados, confira o artigo [Link Privado do Azure](/azure/private-link/private-link-overview).

> [!IMPORTANT]
> O link privado do Azure não afeta o plano de controle do Azure (operações de gerenciamento), como excluir o espaço de trabalho ou gerenciar recursos de computação. Por exemplo, criando, atualizando ou excluindo um destino de computação. Essas operações são executadas pela Internet pública normalmente. Operações de plano de dados, como usar o Azure Machine Learning Studio, APIs (incluindo pipelines publicados) ou o SDK usam o ponto de extremidade privado.
>
> Você pode encontrar problemas ao tentar acessar o ponto de extremidade privado para seu espaço de trabalho se você estiver usando o Mozilla Firefox. Esse problema pode estar relacionado ao DNS sobre HTTPS no Mozilla. É recomendável usar o Microsoft Edge do Google Chrome como uma solução alternativa.

## <a name="prerequisites"></a>Pré-requisitos

Se você planeja usar um espaço de trabalho habilitado para link privado com uma chave gerenciada pelo cliente, você deve solicitar esse recurso usando um tíquete de suporte. Para obter mais informações, consulte [gerenciar e aumentar cotas](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="limitations"></a>Limitações

O uso de um espaço de trabalho Azure Machine Learning com o link privado não está disponível nas regiões do Azure governamental ou na 21Vianet do Azure na China.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Criar um espaço de trabalho que usa um ponto de extremidade privado

Use um dos seguintes métodos para criar um espaço de trabalho com um ponto de extremidade privado:

> [!TIP]
> O modelo de Azure Resource Manager pode criar uma nova rede virtual, se necessário. Todos os outros métodos exigem uma rede virtual existente.

# <a name="resource-manager-template"></a>[Modelo do Resource Manager](#tab/azure-resource-manager)

O modelo de Azure Resource Manager no [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) fornece uma maneira fácil de criar um espaço de trabalho com um ponto de extremidade privado e uma rede virtual.

Para obter informações sobre como usar esse modelo, incluindo pontos de extremidade privados, consulte [usar um modelo de Azure Resource Manager para criar um espaço de trabalho para Azure Machine Learning](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

O SDK do Azure Machine Learning Python fornece a classe [PrivateEndpointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) , que pode ser usada com [Workspace. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) para criar um espaço de trabalho com um ponto de extremidade privado. Essa classe requer uma rede virtual existente.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A [extensão de CLI do Azure para Machine Learning](reference-azure-machine-learning-cli.md) fornece o comando [AZ ml Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) . Os seguintes parâmetros para este comando podem ser usados para criar um espaço de trabalho com uma rede privada, mas requer uma rede virtual existente:

* `--pe-name`: O nome do ponto de extremidade privado que é criado.
* `--pe-auto-approval`: Se as conexões de ponto de extremidade privado com o espaço de trabalho devem ser aprovadas automaticamente.
* `--pe-resource-group`: O grupo de recursos para o qual criar o ponto de extremidade privado. Deve ser o mesmo grupo que contém a rede virtual.
* `--pe-vnet-name`: A rede virtual existente na qual criar o ponto de extremidade privado.
* `--pe-subnet-name`: O nome da sub-rede para a qual criar o ponto de extremidade privado. O valor padrão é `default`.

# <a name="portal"></a>[Portal](#tab/azure-portal)

A guia __rede__ no Azure Machine Learning Studio permite que você configure um ponto de extremidade privado. No entanto, ele requer uma rede virtual existente. Para obter mais informações, consulte [criar espaços de trabalho no portal](how-to-manage-workspace.md).

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Usando um espaço de trabalho em um ponto de extremidade privado

Como a comunicação com o espaço de trabalho só é permitida a partir da rede virtual, todos os ambientes de desenvolvimento que usam o espaço de trabalho devem ser membros da rede virtual. Por exemplo, uma máquina virtual na rede virtual.

> [!IMPORTANT]
> Para evitar a interrupção temporária da conectividade, a Microsoft recomenda liberar o cache DNS em computadores que se conectam ao espaço de trabalho depois de habilitar o link privado. 

Para obter informações sobre máquinas virtuais do Azure, consulte a [documentação sobre máquinas virtuais](/azure/virtual-machines/).


## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como proteger seu espaço de trabalho do Azure Machine Learning, consulte o artigo [visão geral de isolamento e privacidade da rede virtual](how-to-network-security-overview.md) .

* Se você planeja usar uma solução DNS personalizada em sua rede virtual, consulte [como usar um espaço de trabalho com um servidor DNS personalizado](how-to-custom-dns.md).
