---
title: Lista de membros do grupo de download em massa - Portal do Diretório Ativo do Azure | Microsoft Docs
description: Adicione usuários em massa no centro de administrador esporão do Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e29aacb1357509e2b000a9d05c5ced8f9a30dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517168"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Membros de download em massa de um grupo (pré-visualização) no Azure Active Directory

Usando o portal Azure Active Directory (Azure AD), você pode baixar em massa os membros de um grupo em sua organização para um arquivo CSV (Comma-Separated Values).

## <a name="to-bulk-download-group-membership"></a>Para baixar em massa a adesão ao grupo

1. Faça login [no portal Do Zure](https://portal.azure.com) com uma conta de administrador de usuário na organização. Os proprietários de grupos também podem baixar em massa membros de grupos que possuem.
1. No Azure AD, selecione **Grupos Todos** > **os grupos**.
1. Abra o grupo cuja associação você deseja baixar e, em seguida, selecione **Membros**.
1. Na página **Membros,** selecione **Baixar membros** para baixar um arquivo CSV listando os membros do grupo.

   ![O comando Download Members está na página de perfil do grupo](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Verifique o status do download

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação Bulk (visualização).**

   ![A página de resultados de operações em massa mostra o status da solicitação em massa](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limites de serviço de download em massa

Cada atividade em massa para baixar uma lista de membros do grupo pode ser executada por até uma hora. Isso permite que você baixe uma lista de pelo menos 500.000 membros.

## <a name="next-steps"></a>Próximas etapas

- [Membros do grupo de importação a granel](groups-bulk-import-members.md)
- [Membros do grupo de remoção em massa](groups-bulk-download-members.md)
