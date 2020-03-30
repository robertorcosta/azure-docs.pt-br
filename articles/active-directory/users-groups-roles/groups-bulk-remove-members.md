---
title: Membros do grupo de remoção em massa carregando um arquivo csv - Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 9d384ea4749e2d0bc7edf8df7ac0508566f2f76b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517097"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Membros do grupo de remoção em massa (pré-visualização) no Azure Active Directory

Usando o portal Azure Active Directory (Azure AD), você pode remover um grande número de membros de um grupo usando um arquivo CSV (Comma-Separated Values, valores separados por comuma) para remover em massa os membros do grupo.

## <a name="to-bulk-remove-group-members"></a>Para remover em massa os membros do grupo

1. Faça login [no portal Do Zure](https://portal.azure.com) com uma conta de administrador de usuário na organização. Os proprietários de grupos também podem remover em massa membros de grupos que possuem.
1. No Azure AD, selecione **Grupos Todos** > **os grupos**.
1. Abra o grupo do qual você está removendo membros e, em seguida, selecione **Membros**.
1. Na página **Membros,** selecione **Remover membros**.
1. Na página **"Preview" membros do grupo de remoção em massa (Preview),** selecione **Baixar** para obter o modelo de arquivo CSV com as propriedades necessárias dos membros do grupo.

   ![O comando Remover membros está na página de perfil do grupo](./media/groups-bulk-remove-members/remove-panel.png)

1. Abra o arquivo CSV e adicione uma linha para cada membro do grupo que deseja remover do grupo (os valores necessários são ID de objeto membro ou nome principal do usuário). Em seguida, salve o arquivo.

   ![O arquivo CSV contém nomes e IDs para os membros removerem](./media/groups-bulk-remove-members/csv-file.png)

1. Na página **Bulk remove group members (Preview),** em Upload seu arquivo **csv**, navegue até o arquivo. Quando você selecionar o arquivo, a validação do arquivo .csv será iniciada.
1. Quando o conteúdo do arquivo é validado, a página de importação em massa exibe **o arquivo carregado com sucesso**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
1. Quando o arquivo passar pela validação, **selecione Enviar** para iniciar a operação em massa do Azure que remove os membros do grupo.
1. Quando a operação de remoção for concluída, você verá uma notificação de que a operação em massa foi bem sucedida.

## <a name="check-removal-status"></a>Verifique o status de remoção

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação Bulk (visualização).**

   ![A página de resultados de operações em massa mostra o status da solicitação em massa](./media/groups-bulk-remove-members/bulk-center.png)

Para obter detalhes sobre cada item de linha dentro da operação em massa, selecione os valores nas colunas **#Sucesso**, **# Falha**ou Total **de Solicitações.** Se ocorrerem falhas, os motivos da falha serão listados.

## <a name="bulk-removal-service-limits"></a>Limites de serviço de remoção em massa

Cada atividade em massa para remover uma lista de membros do grupo pode ser executada por até uma hora. Isso permite a remoção de uma lista de pelo menos 40.000 membros.

## <a name="next-steps"></a>Próximas etapas

- [Membros do grupo de importação a granel](groups-bulk-import-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
- [Baixe uma lista de todos os grupos](groups-bulk-download.md)
