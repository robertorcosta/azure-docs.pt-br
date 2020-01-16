---
title: Problemas de implantação para perguntas frequentes sobre Serviços de Nuvem do Microsoft Azure | Microsoft Docs
description: Este artigo lista as perguntas frequentes sobre a implantação para Serviços de Nuvem do Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 63a219078927e9001a8eb4085c722e7ec8d2fac9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980640"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de implantação para Serviços de Nuvem do Azure: perguntas frequentes

Este artigo inclui perguntas frequentes sobre problemas de implantação para [Serviços de Nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Você também pode consultar a [página de tamanho de VM de Serviços de Nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Por que implantar um serviço de nuvem ao slot de preparo às vezes falha com um erro de alocação de recurso se já houver uma implantação existente no slot de produção?
Se um serviço de nuvem tiver uma implantação em cada slot, todo o serviço de nuvem será fixado a um cluster específico. Isso significa que, se uma implantação já existir no slot de produção, uma nova implantação de preparo só poderá ser alocada no mesmo cluster que o slot de produção.

Falhas de alocação de ocorrerem quando o cluster em que se encontra o serviço de nuvem não tem recursos físicos de computação suficientes para atender à sua solicitação de implantação.

Para obter ajuda para atenuar essas falhas de alocação, consulte [falha de alocação do serviço de nuvem: soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Por que escalar vertical ou horizontalmente uma implantação do serviço de nuvem às vezes resulta em falha de alocação?
Quando um serviço de nuvem é implantado, ele geralmente é fixado a um cluster específico. Isso significa que escalar vertical/horizontalmente um serviço de nuvem existente deve alocar novas instâncias no mesmo cluster. Se o cluster estiver se aproximando da capacidade ou se o tamanho/tipo de VM desejado não estiver disponível, a solicitação poderá falhar.

Para obter ajuda para atenuar essas falhas de alocação, consulte [falha de alocação do serviço de nuvem: soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Por que implantar um serviço de nuvem em um grupo de afinidades às vezes resulta em falha de alocação?
Uma nova implantação para um serviço de nuvem vazio pode ser alocada pela malha em qualquer cluster nessa região, a menos que o serviço de nuvem esteja fixado em um grupo de afinidades. As implantações no mesmo grupo de afinidade serão tentadas no mesmo cluster. Se o cluster estiver se aproximando da capacidade, a solicitação poderá falhar.

Para obter ajuda para atenuar essas falhas de alocação, consulte [falha de alocação do serviço de nuvem: soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Por que alterar o tamanho da VM ou adicionar uma nova VM a um serviço de nuvem existente às vezes resulta em falha de alocação?
Os clusters em um datacenter podem ter configurações diferentes de tipos de computador (por exemplo, uma série, série Av2, série D, série Dv2, série G, série H, etc.). Porém, nem todos os clusters necessariamente terão todos os tipos de VMs. Por exemplo, se você tentar adicionar uma VM da série D a um serviço de nuvem que já esteja implantado em um cluster somente da série A, ocorrerá uma falha de alocação. Isso também ocorrerá se você tentar alterar os tamanhos de SKU da VM (por exemplo, trocar da série A para a série D).

Para obter ajuda para atenuar essas falhas de alocação, consulte [falha de alocação do serviço de nuvem: soluções](cloud-services-allocation-failures.md#solutions).

Para verificar os tamanhos disponíveis em sua região, consulte [Microsoft Azure: produtos disponíveis por região](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Por que implantar um serviço de nuvem às vezes falha devido a limites/cotas/restrições em minha assinatura ou o serviço?
A implantação de um serviço de nuvem poderá falhar se os recursos que precisam ser alocados excederem a cota padrão ou máxima permitida para o serviço no nível da região/do datacenter. Para obter mais informações, consulte [Limites de Serviços de Nuvem](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits).

Também é possível controlar a cota/uso atual da sua assinatura no portal: portal do Azure => Assinaturas => \<assinatura adequada>=> "Uso + cota".

Informações relacionadas ao uso/consumo de recursos também podem ser recuperadas por meio das APIs de Cobrança do Azure. Confira [API de uso de recursos do Azure (versão prévia)](../cost-management-billing/manage/usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Como posso alterar o tamanho de uma VM de serviço de nuvem implantada sem reimplantá-la?
Você não pode alterar o tamanho de uma VM de serviço de nuvem implantada sem reimplantá-la. O tamanho da VM é criado em CSDEF, que somente pode ser atualizado com uma reimplantação.

Para obter mais informações, consulte [Como atualizar um serviço de nuvem](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Por que eu não consigo implantar os Serviços de Nuvem por meio das APIs de Gerenciamento de Serviços ou PowerShell ao usar a conta de armazenamento do Azure Resource Manager? 

Como o serviço de nuvem é um recurso clássico que não é compatível diretamente com o modelo de Azure Resource Manager, você não pode associá-lo com as contas de armazenamento de Azure Resource Manager. Veja algumas opções: 

- Implantar por meio da API REST.

    Quando você implanta por meio da API REST do Gerenciamento de Serviços, é possível contornar a limitação especificando uma URL SAS para o armazenamento de blobs, que funcionará com a conta de armazenamento clássica e do Azure Resource Manager. Leia mais sobre a propriedade 'PackageUrl' [aqui](/previous-versions/azure/reference/ee460813(v=azure.100)).

- Implantar por meio do [Portal do Azure](https://portal.azure.com).

    Isso funcionará na [portal do Azure](https://portal.azure.com) à medida que a chamada passa por um proxy/Shim que permite a comunicação entre Azure Resource Manager e recursos clássicos. 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Por que o portal do Azure exige que eu forneça uma conta de armazenamento para a implantação?

No portal clássico, o pacote foi carregado diretamente para a camada da API de gerenciamento e, então, a camada da API colocaria temporariamente o pacote em uma conta de armazenamento interno.  Esse processo causa problemas de desempenho e escalabilidade, porque a camada da API não foi projetada para ser um serviço de carregamento de arquivo.  No portal do Azure (modelo de implantação do Gerenciador de Recursos), ignoramos a etapa provisória do primeiro carregamento para a camada da API, resultando em implantações mais rápidas e mais confiáveis.

Quanto ao custo, ele é muito pequeno e você pode reutilizar a mesma conta de armazenamento em todas as implantações. Você pode usar a [calculadora de preços de armazenamento](https://azure.microsoft.com/pricing/calculator/#storage1) para determinar o custo para carregar o service pack (CSPKG), fazer o download do CSPKG, e então excluir o CSPKG.
