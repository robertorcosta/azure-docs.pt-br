---
title: Recursos suportados para O Azure Red Hat OpenShift
description: Entenda quais regiões do Azure e tamanhos de máquinas virtuais são suportados pelo Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243660"
---
# <a name="azure-red-hat-openshift-resources"></a>Recursos do Azure Red Hat OpenShift

Este tópico lista as regiões do Azure e os tamanhos das máquinas virtuais suportados pelo serviço Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Regiões do Azure

Consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) para uma lista atual de regiões onde você pode implantar clusters Azure Red Hat OpenShift.

## <a name="virtual-machine-sizes"></a>Tamanhos de máquina virtual

Aqui estão os tamanhos de máquina virtual suportados que você pode especificar para os nós de computação em seu cluster Azure Red Hat OpenShift.

> [!Important]
> Cada VM tem um número diferente de drives que podem ser anexados. Isso pode não ser tão claro imediatamente quanto a memória ou o tamanho da CPU.
> Nem todos os tamanhos de VM estão disponíveis em todas as regiões. Mesmo que a API suporte o tamanho especificado, você pode obter um erro se o tamanho não estiver disponível na região especificada.
> Consulte [A lista atual de tamanhos de VM suportados por região](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para obter mais informações.

## <a name="compute-node-sizes"></a>Tamanhos de nó de computação

Os seguintes tamanhos de nó de computação são suportados pela API AZURE Red Hat OpenShift REST:

|Tamanho|vCPU|RAM|
|-|-|-|
|Padrão D4s v3|4|16 GB|
|Padrão D8s v3|8|32 GB|
|Padrão D16s v3|16|64 GB|
|Padrão D32s v3|32|128 GB|
|-|-|-|
|Padrão E4s v3|4|32 GB|
|Padrão E8s v3|8|64 GB|
|Padrão E16s v3|16|128 GB|
|Padrão E32s v3|32|256 GB|
|-|-|-|
|Padrão F8s v2|8|16 GB|
|F16s padrão v2|16|32 GB|
|F32s padrão v2|32|64 GB|

## <a name="master-node-sizes"></a>Tamanhos de nó mestre

Os seguintes tamanhos de nó mestre/infra-estrutura são suportados pela API Azure Red Hat OpenShift REST:

|Tamanho|vCPU|RAM|
|-|-|-|
|Padrão D4s v3|4|16 GB|
|Padrão D8s v3|8|32 GB|
|Padrão D16s v3|16|64 GB|
|Padrão D32s v3|32|128 GB|

## <a name="next-steps"></a>Próximas etapas

Experimente o tutorial [de cluster Create azure Red Hat OpenShift.](tutorial-create-cluster.md)
