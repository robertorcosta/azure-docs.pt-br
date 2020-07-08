---
title: Exportar ou excluir configurações de portal do Azure
description: Saiba como você pode exportar ou excluir suas configurações de usuário, painéis particulares e configurações personalizadas no portal do Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: how-to
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: 248c643c5f1865451abbe201e7033efedb8a7e39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763934"
---
# <a name="export-or-delete-user-settings"></a>Exportar ou excluir configurações do usuário

Você pode usar configurações e recursos no portal do Azure para criar uma experiência personalizada. As informações sobre suas configurações personalizadas são armazenadas no Azure. Você pode exportar ou excluir os seguintes dados de usuário:

* Painéis privados no portal do Azure
* Configurações do usuário, como assinaturas ou diretórios favoritos e o último diretório conectado
* Temas e outras configurações personalizadas do portal

É uma boa ideia exportar e revisar suas configurações antes de excluí-las. Recriar painéis ou refazer configurações personalizadas pode ser demorado.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportar ou excluir suas configurações do portal

1. Entre no [portal do Azure](https://portal.azure.com).

1. No cabeçalho do portal, selecione configurações ![ ícone ](media/azure-portal-export-delete-settings/settings-icon.png) **configurações**.

1. Selecione **Exportar todas as configurações** ou **Excluir todas as configurações e os dashboards particulares**.

    ![Opções de configurações de portal do Azure e configurações](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      A tabela a seguir descreve essas ações.

      | Ação | Descrição |
      | --- | --- |
      | **Exportar todas as configurações** | Cria um arquivo *. JSON* que contém suas configurações de usuário, como seu tema de cores, favoritos e painéis privados.|
      | **Excluir todas as configurações e painéis privados** | Exclui todos os links para painéis privados e outras configurações personalizadas que você fez no Portal. |

> [!NOTE]
> Devido à natureza dinâmica das configurações do usuário e ao risco de dados corrompidos, você não pode importar as configurações do arquivo *. JSON* .
>
>

## <a name="next-steps"></a>Próximas etapas

* [Compartilhar painéis do Azure usando o Controle de Acesso Baseado em Função](azure-portal-dashboard-share-access.md)
* [Adicionar, remover e reorganizar favoritos](azure-portal-add-remove-sort-favorites.md)
