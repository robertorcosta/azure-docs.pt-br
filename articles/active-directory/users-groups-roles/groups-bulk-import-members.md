---
title: Upload de importação em massa para adicionar membros a um grupo - Azure Active Directory | Microsoft Docs
description: Adicione membros do grupo em massa no centro de administrador do Azure Active Directory.
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
ms.openlocfilehash: 8ff4234d9fd28e655e868108e37b09c5afe2767c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517133"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Membros do grupo de importação em massa (pré-visualização) no Azure Active Directory

Usando o portal Azure Active Directory (Azure AD), você pode adicionar um grande número de membros a um grupo usando um arquivo CSV (Comma-Separated Values, valores separados por comuma) para membros do grupo de importação em massa.

## <a name="to-bulk-import-group-members"></a>Para membros do grupo de importação em massa

1. Faça login [no portal Do Zure](https://portal.azure.com) com uma conta de administrador de usuário na organização. Os proprietários do grupo também podem importar em massa membros de grupos que possuem.
1. No Azure AD, selecione **Grupos Todos** > **os grupos**.
1. Abra o grupo ao qual você está adicionando membros e, em seguida, selecione **Membros**.
1. Na página **Membros,** selecione **Importar membros**.
1. Na página Membros do **grupo de importação em massa (Preview),** selecione **Baixar** para obter o modelo de arquivo CSV com as propriedades de membro do grupo necessárias.

    ![O comando Importar Membros está na página de perfil do grupo](./media/groups-bulk-import-members/import-panel.png)

1. Abra o arquivo CSV e adicione uma linha para cada membro do grupo que deseja importar para o grupo (os valores necessários são **ID de objeto membro** ou **nome principal do usuário**). Em seguida, salve o arquivo.

   ![O arquivo CSV contém nomes e IDs para os membros importarem](./media/groups-bulk-import-members/csv-file.png)

1. Na página Membros do **grupo de importação em massa (Preview),** em Upload seu arquivo **csv**, navegue até o arquivo. Quando você seleciona o arquivo, a validação do arquivo CSV é iniciada.
1. Quando o conteúdo do arquivo é validado, a página de importação em massa exibe **o arquivo carregado com sucesso**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
1. Quando o arquivo passar pela validação, **selecione Enviar** para iniciar a operação em massa do Azure que importa os membros do grupo para o grupo.
1. Quando a operação de importação for concluída, você verá uma notificação de que a operação em massa foi bem sucedida.

## <a name="check-import-status"></a>Verifique o status de importação

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação Bulk (visualização).**

   ![A página de resultados de operações em massa mostra o status da solicitação em massa](./media/groups-bulk-import-members/bulk-center.png)

Para obter detalhes sobre cada item de linha dentro da operação em massa, selecione os valores nas colunas **#Sucesso**, **# Falha**ou Total **de Solicitações.** Se ocorrerem falhas, os motivos da falha serão listados.

## <a name="bulk-import-service-limits"></a>Limites de serviço de importação em massa

Cada atividade em massa para importar uma lista de membros do grupo pode ser executada por até uma hora. Isso permite a importação de uma lista de pelo menos 40.000 membros.

## <a name="next-steps"></a>Próximas etapas

- [Membros do grupo de remoção em massa](groups-bulk-remove-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
- [Baixe uma lista de todos os grupos](groups-bulk-download.md)
