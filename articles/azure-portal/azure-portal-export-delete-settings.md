---
title: Exportar ou excluir configurações do portal do Azure | Microsoft Docs
description: Saiba como você pode exportar ou excluir suas configurações de usuário, painéis particulares e configurações personalizadas no portal do Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: f033af37985077f4d8df9d541b55764df0c75eda
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640169"
---
# <a name="export-or-delete-user-settings"></a>Exportar ou excluir configurações do usuário

Você pode usar configurações e recursos no portal do Azure para criar uma experiência personalizada. As informações sobre suas configurações personalizadas são armazenadas no Azure. Você pode exportar ou excluir os seguintes dados de usuário:

* Painéis privados no portal do Azure
* Configurações do usuário, como assinaturas ou diretórios favoritos e o último diretório conectado
* Temas e outras configurações personalizadas do portal

É uma boa ideia exportar e revisar suas configurações antes de excluí-las. Recriar painéis ou refazer configurações personalizadas pode ser demorado.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportar ou excluir as configurações do portal

1. Entre no [portal do Azure](https://portal.azure.com).
2. No cabeçalho do portal, selecione **configurações**.

    ![Captura de tela que mostra o ícone de engrenagem das configurações do portal](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. Selecione **Exportar todas as configurações** ou **Excluir todas as configurações e os dashboards particulares**.

    ![Captura de tela que mostra as configurações de exportação e exclusão do portal](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      A tabela a seguir descreve essas ações.

      | Ação | Description |
      | --- | --- |
      | **Exportar todas as configurações** | Cria um arquivo. JSON que contém suas configurações de usuário, como seu tema de cores, favoritos e painéis privados.|
      | **Excluir todas as configurações e painéis privados** | Exclui todos os links para painéis privados e outras configurações personalizadas que você fez no Portal. |

> [!NOTE]
> Devido à natureza dinâmica das configurações do usuário e ao risco de dados corrompidos, você não pode importar as configurações do arquivo. JSON.
>
>


## <a name="next-steps"></a>Próximos passos

* [Criar e compartilhar painéis do Azure](azure-portal-dashboard-share-access.md)
* [Adicionar, remover e classificar favoritos](azure-portal-add-remove-sort-favorites.md)
