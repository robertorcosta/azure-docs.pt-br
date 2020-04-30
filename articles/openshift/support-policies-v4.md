---
title: Política de suporte do cluster do Azure Red Hat OpenShift 4
description: Compreenda os requisitos da política de suporte para o Red Hat OpenShift 4.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 7bdcccee3270f9d2b611682a9a59505158a494d2
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205203"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Política de suporte do Azure Red Hat OpenShift

Determinadas configurações para clusters do Azure Red Hat OpenShift 4 podem afetar a compatibilidade do cluster. O Azure Red Hat OpenShift 4 permite que os administradores de cluster façam alterações em componentes de cluster internos, mas nem todas as alterações têm suporte. A política de suporte abaixo compartilha as modificações que violam a política e o suporte a void da Microsoft e do Red Hat.

> [!NOTE]
> Os recursos marcados como visualização tecnológica na plataforma de contêiner OpenShift não têm suporte no Azure Red Hat OpenShift.

## <a name="cluster-configuration-requirements"></a>Requisitos de configuração de cluster

* Todos os operadores de cluster OpenShift devem permanecer em um estado gerenciado. A lista de operadores de cluster pode ser retornada executando `oc get clusteroperators`.
* Não remova ou modifique os serviços Prometheus e Alertmanager do cluster.
* Não remova as regras do Alertmanager do serviço.
* Não modifique a versão do cluster OpenShift.
* Não remova ou modifique o registro em log do serviço OpenShift do Azure Red Hat (MDSD Pods).
* Não remova ou modifique o segredo de extração de cluster ' arosvc.azurecr.io '.
* Todas as máquinas virtuais de cluster devem ter acesso de saída à Internet, pelo menos aos pontos de extremidade Azure Resource Manager (ARM) e log de serviço (Geneva).
* O serviço do Azure Red Hat OpenShift acessa seu cluster por meio do serviço de vínculo privado.  Não remova ou modifique o acesso ao serviço.
* Não há suporte para nós de computação não RHCOS. Por exemplo, você não pode usar um nó de computação RHEL.

## <a name="supported-virtual-machine-sizes"></a>Tamanhos de máquina virtual com suporte

O Azure Red Hat OpenShift 4 dá suporte a instâncias de nó de trabalho nos seguintes tamanhos de máquina virtual:

### <a name="general-purpose"></a>Propósito geral

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>Otimizado para memória

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>Otimizado para computação

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|
