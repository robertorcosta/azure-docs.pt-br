---
title: Impedir exclusão acidental – Compartilhamentos de arquivo do Azure
description: Saiba mais sobre a exclusão temporária nos compartilhamentos de arquivo do Azure e como você pode usá-la para a recuperação de dados e para impedir a exclusão acidental.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 96e3d5001d11455337ae092776a1a4c5c3738012
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83882929"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Impedir exclusão acidental de compartilhamentos de arquivo do Azure

O Armazenamento do Azure agora oferece exclusão temporária para compartilhamentos de arquivo. A exclusão temporária permite recuperar seus dados quando eles são excluídos de forma incorreta por um aplicativo ou outro usuário da conta de armazenamento.

## <a name="how-soft-delete-works"></a>Como funciona a exclusão temporária

Quando habilitada, a exclusão temporária permite salvar e recuperar os compartilhamentos de arquivo quando eles são excluídos. Quando dados são excluídos, é feita a transição deles para um estado com exclusão reversível em vez de serem apagados permanentemente. Você pode configurar o tempo durante o qual os dados com exclusão temporária podem ser recuperados antes de serem excluídos permanentemente.

A exclusão temporária pode ser habilitada em compartilhamentos de arquivos novos ou existentes. A exclusão temporária também é compatível com versões anteriores. Você não precisa alterar nada nos aplicativos para aproveitar as proteções da exclusão temporária. 

Para compartilhamentos de arquivo premium excluídos de maneira temporária, a cota de compartilhamento de arquivo (o tamanho provisionado de um compartilhamento de arquivos) é usada no cálculo da cota de conta de armazenamento total até a data de expiração do compartilhamento com exclusão temporária, quando o compartilhamento é totalmente excluído.

### <a name="availability"></a>Disponibilidade

A exclusão temporária para compartilhamentos de arquivo do Azure está disponível em todas as camadas de armazenamento, em todos os tipos de conta de armazenamento e em todas as regiões em que os Arquivos do Azure estão disponíveis.

## <a name="configuration-settings"></a>Definições de configuração

A exclusão temporária para compartilhamentos de arquivo está habilitada no nível da conta de armazenamento, as configurações de exclusão temporária se aplicam a todos os compartilhamentos de arquivo em uma conta. Quando você cria uma conta de armazenamento, a exclusão temporária fica desativada por padrão. A exclusão reversível também está desativada por padrão para as contas de armazenamento existentes. Você pode ativar e desativar a exclusão temporária a qualquer momento.

Se você habilitar a exclusão temporária para compartilhamentos de arquivo, excluir alguns compartilhamentos de arquivo e desabilitar a exclusão temporária, você ainda poderá acessar e recuperar esses compartilhamentos se eles tiverem sido salvos nesse período. Quando habilitar a exclusão temporária, você precisará também configurar o período de retenção.

O período de retenção indica por quanto tempo os compartilhamentos de arquivos de exclusão temporária ficam armazenados e disponíveis para recuperação. Para compartilhamentos de arquivo que são excluídos explicitamente, a contagem do período de retenção se inicia quando os dados são excluídos. No momento, você pode manter os compartilhamentos com exclusão temporária entre 1 e 365 dias.

Você pode alterar o período de retenção de exclusão reversível a qualquer momento. Um período de retenção atualizado será aplicado somente a compartilhamentos excluídos depois que o período de retenção tiver sido atualizado. Os compartilhamentos excluídos antes da atualização do período de retenção expirarão com base no período de retenção que foi configurado quando esses dados foram excluídos.

Para excluir permanentemente um compartilhamento de arquivos em um estado de exclusão temporária antes do tempo de expiração, você deve restaurar o compartilhamento, desabilitar a exclusão temporária e, em seguida, excluir o compartilhamento novamente. Em seguida, você deve reabilitar a exclusão temporária, pois quaisquer outros compartilhamentos de arquivos nessa conta de armazenamento ficarão vulneráveis à exclusão acidental enquanto a exclusão temporária estiver desativada.

## <a name="pricing-and-billing"></a>Preços e cobrança

Os compartilhamentos de arquivos Standard e Premium são cobrados na capacidade usada quando são excluídos de maneira temporária, em vez da capacidade provisionada. Além disso, os compartilhamentos de arquivos premium são cobrados na taxa de instantâneos durante o estado de exclusão temporária. Os compartilhamentos de arquivos Standard são cobrados na taxa regular durante o estado de exclusão temporária. Você não será cobrado pelos dados que são excluídos permanentemente após o período de retenção configurado.

Para obter mais informações sobre os preços do armazenamento de arquivos do Azure em geral, confira a [Página de Preços do Armazenamento de Arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/).

Quando você habilita inicialmente a exclusão temporária, recomendamos usar um período de retenção pequeno para entender melhor como o recurso afetará sua fatura.

## <a name="next-steps"></a>Próximas etapas

Para saber como habilitar e usar a exclusão temporária, continue em [Habilitar exclusão temporária](storage-files-enable-soft-delete.md)