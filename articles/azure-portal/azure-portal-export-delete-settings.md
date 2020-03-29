---
title: Exportar ou excluir as configurações do portal Azure
description: Saiba como exportar ou excluir as configurações do usuário, painéis privados e configurações personalizadas no portal Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900778"
---
# <a name="export-or-delete-user-settings"></a>Exportar ou excluir configurações do usuário

Você pode usar configurações e recursos no portal Azure para criar uma experiência personalizada. As informações sobre suas configurações personalizadas são armazenadas no Azure. Você pode exportar ou excluir os seguintes dados do usuário:

* Painéis privados no portal Azure
* Configurações do usuário, como assinaturas ou diretórios favoritos e último diretório conectado
* Temas e outras configurações de portal personalizado

É uma boa ideia exportar e revisar suas configurações antes de excluí-las. Reconstruir painéis ou refazer configurações personalizadas pode ser demorado.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportar ou excluir suas configurações do portal

1. Faça login no [portal Azure](https://portal.azure.com).

1. No cabeçalho do ![portal,](media/azure-portal-export-delete-settings/settings-icon.png) selecione configurações de ícone **Configurações**.

1. Selecione **Exportar todas as configurações** ou **Excluir todas as configurações e os dashboards particulares**.

    ![Opções de configurações e configurações do portal Azure](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      A tabela a seguir descreve essas ações.

      | Ação | Descrição |
      | --- | --- |
      | **Exportar todas as configurações** | Cria um arquivo *.json* que contém as configurações do usuário, como seu tema de cor, favoritos e painéis privados.|
      | **Excluir todas as configurações e painéis privados** | Exclui todos os links para painéis privados e outras configurações personalizadas que você fez para o portal. |

> [!NOTE]
> Devido à natureza dinâmica das configurações do usuário e ao risco de corrupção de dados, você não pode importar configurações do arquivo *.json.*
>
>

## <a name="next-steps"></a>Próximas etapas

* [Compartilhar painéis do Azure usando o Controle de Acesso Baseado em Função](azure-portal-dashboard-share-access.md)
* [Adicionar, remover e reorganizar favoritos](azure-portal-add-remove-sort-favorites.md)
