---
title: Baixe uma lista de grupos no portal do Diretório Ativo do Azure | Microsoft Docs
description: Baixe as propriedades do grupo em massa no centro de administradores do Azure no Azure Active Directory.
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
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517144"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Baixe em massa uma lista de grupos (pré-visualização) no Azure Active Directory

Usando o portal Azure Active Directory (Azure AD), você pode baixar em massa a lista de todos os grupos da sua organização para um arquivo CSV (Comma-Separated Values).

## <a name="to-download-a-list-of-groups"></a>Para baixar uma lista de grupos

1. Faça login [no portal Azure](https://portal.azure.com) com uma conta de administrador na organização.
1. No Azure AD, selecione **Grupos** > **de download de grupos**.
1. Na página **de download de Grupos,** **selecione Iniciar** para receber um arquivo CSV listando seus grupos.

   ![O comando grupos de download está na página Todos os grupos](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Verifique o status do download

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação Bulk (visualização).**

   ![A página de resultados de operações em massa mostra o status da solicitação em massa](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limites de serviço de download em massa

Cada atividade em massa para baixar uma lista de grupos pode ser executada por até uma hora. Isso permite que você baixe uma lista de pelo menos 300.000 grupos.

## <a name="next-steps"></a>Próximas etapas

- [Membros do grupo de remoção em massa](groups-bulk-remove-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
