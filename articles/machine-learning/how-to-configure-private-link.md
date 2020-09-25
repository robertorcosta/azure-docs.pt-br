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
ms.date: 09/21/2020
ms.openlocfilehash: 619960238125191e7bd4e702a49016c8fd58c847
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296647"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Configurar o link privado do Azure para um espaço de trabalho Azure Machine Learning (versão prévia)

Neste documento, você aprenderá a usar o link privado do Azure com seu espaço de trabalho Azure Machine Learning. Para obter informações sobre como configurar uma rede virtual para Azure Machine Learning, consulte [visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md)

> [!IMPORTANT]
> O uso do link privado do Azure com o Azure Machine Learning espaço de trabalho está atualmente em visualização pública. Essa funcionalidade só está disponível nas seguintes regiões:
>
> * **Leste dos EUA**
> * **Centro-Sul dos Estados Unidos**
> * **Oeste dos EUA**
> * **Oeste dos EUA 2**
> * **Centro do Canadá**
> * **Sudeste Asiático**
> * **Leste do Japão**
> * **Norte da Europa**
> * **Austrália oriental**
> * **Sul do Reino Unido**
>
> Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O link privado do Azure permite que você se conecte ao seu espaço de trabalho usando um ponto de extremidade privado. O ponto de extremidade privado é um conjunto de endereços IP privados na sua rede virtual. Você pode, então, limitar o acesso ao seu espaço de trabalho para ocorrer somente nos endereços IP privados. O link privado ajuda a reduzir o risco de vazamento de dados. Para saber mais sobre pontos de extremidade privados, confira o artigo [Link Privado do Azure](/azure/private-link/private-link-overview).

> [!IMPORTANT]
> O link privado do Azure não afeta o plano de controle do Azure (operações de gerenciamento), como excluir o espaço de trabalho ou gerenciar recursos de computação. Por exemplo, criando, atualizando ou excluindo um destino de computação. Essas operações são executadas pela Internet pública normalmente. Operações de plano de dados, como usar o Azure Machine Learning Studio, APIs (incluindo pipelines publicados) ou o SDK usam o ponto de extremidade privado.
>
> Você pode encontrar problemas ao tentar acessar o ponto de extremidade privado para seu espaço de trabalho se você estiver usando o Mozilla Firefox. Esse problema pode estar relacionado ao DNS sobre HTTPS no Mozilla. É recomendável usar o Microsoft Edge do Google Chrome como uma solução alternativa.

> [!TIP]
> Azure Machine Learning instância de computação pode ser usada com um espaço de trabalho e um ponto de extremidade privado. Essa funcionalidade está atualmente em visualização pública no **leste dos EUA**, **Centro-Sul dos** EUA e regiões **oeste dos EUA 2** .

## <a name="prerequisites"></a>Pré-requisitos

Se você planeja usar um espaço de trabalho habilitado para link privado com uma chave gerenciada pelo cliente, você deve solicitar esse recurso usando um tíquete de suporte. Para obter mais informações, consulte [gerenciar e aumentar cotas](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

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


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como proteger seu espaço de trabalho do Azure Machine Learning, consulte o artigo [visão geral de isolamento e privacidade da rede virtual](how-to-network-security-overview.md) .
