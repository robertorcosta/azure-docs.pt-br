---
title: Visão Geral do exemplo de blueprint da zona de destino de Migração do CAF
description: Visão geral e arquitetura do exemplo de blueprint da zona de destino do CAF (Cloud Adoption Framework) para o Azure Migration.
ms.date: 04/15/2020
ms.topic: sample
ms.openlocfilehash: 6deb671070a742448560309b1b1a3572f5aa082d
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852303"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Visão geral do exemplo de blueprint da zona de aterrissagem de migração do Cloud Adoption Framework da Microsoft para o Azure

O blueprint da zona de aterrissagem de migração do CAF (Cloud Adoption Framework) da Microsoft para o Azure é um conjunto de infraestrutura para ajudar você na configuração para migrar sua primeira carga de trabalho e gerenciar o estado de nuvem em alinhamento com o CAF.

O exemplo de blueprint da [Base do CAF](../caf-foundation/index.md) estende esse exemplo.

## <a name="architecture"></a>Arquitetura

O exemplo de blueprint da zona de aterrissagem de migração do CAF implanta recursos de infraestrutura de base no Azure, que podem ser usados pelas organizações para preparar a assinatura delas para receber a migração de máquinas virtuais. Ela também ajuda a implementar os controles de governança necessários para gerenciar sua propriedade de nuvem. Este exemplo implantará e aplicará recursos, políticas e modelos que permitirão às organizações começar a usar o Azure com confiança.

:::image type="content" source="../../media/caf-blueprints/caf-migration-landing-zone-architecture.png" alt-text="Zona de destino de migração do CAF, a imagem descreve o que é instalado como parte das diretrizes do CAF para a zona de destino inicial" border="false":::

Este ambiente é composto de vários serviços do Azure usados para oferecer uma governança segura, totalmente monitorada e pronta para empresas. Esse ambiente é composto de:

- Uma instância do [Azure Key Vault](../../../../key-vault/general/overview.md) empregada para hospedar segredos usados para certificados, chaves e segredos implantados no ambiente de serviços compartilhados
- Implantar o [Log Analytics](../../../../azure-monitor/overview.md) garante que todas as ações e os serviços serão registrados em um local central desde o momento em que você iniciar sua migração
- Implantar a [Rede Virtual do Azure](../../../../virtual-network/virtual-networks-overview.md) fornece uma rede isolada e sub-redes para sua máquina virtual.
- Implantar o [Projeto Migrações para Azure](../../../../migrate/migrate-services-overview.md) para descoberta e avaliação. Estamos adicionando as ferramentas para avaliação de servidor, migração de servidor, avaliação de banco de dados e migração de banco de dados.  


Todos esses elementos cumprem as práticas comprovadas publicadas no [Centro de Arquitetura do Azure – Arquiteturas de Referência](/azure/architecture/reference-architectures/).

> [!NOTE]
> O blueprint de migração do CAF cria uma zona de aterrissagem para suas cargas de trabalho. Você ainda precisa executar a avaliação e a migração das Máquinas Virtuais/dos Bancos de Dados sobre essa arquitetura de base.

Para obter mais informações, confira o [Cloud Adoption Framework da Microsoft para o Azure – Migrar](/azure/architecture/cloud-adoption/migrate/).

## <a name="next-steps"></a>Próximas etapas

Você revisou a visão geral e arquitetura do exemplo de blueprint da zona de aterrissagem da Migração do CAF.

> [!div class="nextstepaction"]
> [Blueprint da zona de aterrissagem de Migração do CAF – Etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
