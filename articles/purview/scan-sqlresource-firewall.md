---
title: Verificar o BD SQL do Azure atrás de um firewall
description: Saiba como verificar os recursos por trás de um firewall usando o portal de alcance do Azure.
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551423"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>Verificar o BD SQL do Azure atrás de um firewall no Azure alcance

Neste artigo, você aprenderá a usar o Azure alcance para verificar um recurso por trás de um firewall.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Seu próprio [locatário Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

## <a name="set-up-sql-storage-behind-a-firewall"></a>Configurar o armazenamento do SQL por trás de um firewall

A primeira etapa é adicionar o MSI do catálogo a um banco de dados SQL do Azure por meio do [registro e da verificação de um Azure SQL Database](register-scan-azure-sql-database.md).

## <a name="scan-sql-db-from-purview"></a>Verificar o BD SQL de alcance

1. Navegue até o home page alcance.

1. Selecione **centro de gerenciamento** no painel de navegação esquerdo.

1. Selecione **fontes de dados** em **fontes e varredura**.

1. Selecione **+ novo** para adicionar a fonte de dados.

1. Selecione **banco de dados SQL do Azure**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="Captura de tela para selecionar o SQL Server.":::

1. Forneça um nome para a nova fonte de dados, selecione **da assinatura do Azure** e selecione sua assinatura e o nome do servidor na lista suspensa.

   Selecione **concluir** para concluir o registro. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="Captura de tela para concluir a seleção":::

1. Selecione **configurar verificação** para o armazenamento que está atrás do firewall e conexão de teste. A conexão indica que foi bem-sucedida. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="Captura de tela da seleção T0 configurar verificação.":::

1. Configure a frequência de verificação e selecione **continuar**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="Captura de tela da seleção para iniciar a verificação do SQL.":::

1.  A verificação é concluída e o status é atualizado na lista de fontes de dados.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="Captura de tela da verificação de mensagem concluída":::
   
## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode configurar um conjunto de regras de verificação [criar uma regra de verificação definida](create-a-scan-rule-set.md) como agrupar verificações juntas.
