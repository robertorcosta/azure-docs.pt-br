---
title: Exemplos – Blueprint da Base do CAF – Visão geral
description: Visão geral e arquitetura do exemplo de blueprint da Base do CAF.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
ms.custom: fasttrack-new
ms.openlocfilehash: 404fe62b85d3c273a3aedb495b9fe01d2447360a
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978408"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Visão geral do exemplo de blueprint do Cloud Adoption Framework da Microsoft para o Azure Foundation

O blueprint do CAF (Cloud Adoption Framework da Microsoft para o Azure) Foundation implanta um conjunto de recursos de infraestrutura e controles de política essenciais necessários para seu primeiro aplicativo do Azure de nível de produção. Esse blueprint de base é baseado no padrão recomendado encontrado no CAF.

## <a name="architecture"></a>Arquitetura

O exemplo de blueprint da Base do CAF implanta recursos de infraestrutura recomendados no Azure que podem ser usados pelas organizações para implementar os controles de base necessários para gerenciar a propriedade de nuvem delas. Este exemplo implantará e aplicará recursos, políticas e modelos que permitirão às organizações começar a usar o Azure com confiança.

![Base do CAF – imagem descreve o que será instalado como parte das diretrizes do CAF para criar uma base para começar a usar o Azure](../../media/caf-blueprints/caf-foundation-architecture.png)

Essa implementação incorpora vários serviços do Azure usados para oferecer uma base segura, totalmente monitorada e pronta para empresas. Esse ambiente é composto de:

- Uma instância do [Azure Key Vault](../../../../key-vault/key-vault-overview.md) usada para hospedar segredos usados para as VMs implantadas no ambiente de serviços compartilhados
- Implantar o [Log Analytics](../../../../azure-monitor/overview.md) garante que todas as ações e os serviços serão registrados em um local central desde o momento em que você iniciar sua implantação segura nas [Contas de Armazenamento](../../../../storage/common/storage-introduction.md) para log de diagnósticos
- Implantar a [Central de Segurança do Azure](../../../../security-center/security-center-intro.md) (versão padrão) fornece proteção contra ameaças para suas cargas de trabalho migradas
- O blueprint também define e implanta as [Políticas do Azure](../../../policy/overview.md), para 
  - Marcação (CostCenter) aplicada a grupos de recursos
  - Acrescentar recursos no grupo de recursos com a marca CostCenter
  - Região do Azure permitida para recursos e grupos de recursos
  - SKUs de conta de armazenamento permitidas (escolha durante a implantação)
  - SKUs de VM do Azure permitidas (escolha durante a implantação)
  - Exigir que a inspeção de rede seja implantada 
  - Exigir criptografia de transferência segura na Conta de Armazenamento do Microsoft Azure
  - Negar tipos de recursos (escolher durante a implantação)  
- Iniciativas
  - Habilitar o monitoramento na Central de Segurança do Azure (89 políticas)

Todos esses elementos cumprem as práticas comprovadas publicadas no [Centro de Arquitetura do Azure – Arquiteturas de Referência](/azure/architecture/reference-architectures/).

> [!NOTE]
> A Base do CAF apresenta uma arquitetura de base para as cargas de trabalho.
> Ainda é necessário implantar cargas de trabalho por trás dessa arquitetura de base.

Para obter mais informações, confira o [Cloud Adoption Framework da Microsoft para o Azure – Pronta](/azure/architecture/cloud-adoption/ready/azure-readiness-guide/govern-org-compliance?tabs=AzurePolicy).

## <a name="next-steps"></a>Próximas etapas

Você revisou a visão geral e a arquitetura do exemplo de blueprint da Base do CAF.

> [!div class="nextstepaction"]
>  [Blueprint da Base do CAF – Etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).