---
title: Aumento automático do armazenamento-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como você pode configurar o crescimento automático do armazenamento usando o portal do Azure no banco de dados do Azure para PostgreSQL-servidor único
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: 1853bbb0e44526988b34ed23126664c5a2285a7f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907515"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Crescimento automático do armazenamento usando o portal do Azure no banco de dados do Azure para PostgreSQL-servidor único
Este artigo descreve como você pode configurar um banco de dados do Azure para armazenamento de servidor PostgreSQL para crescer sem afetar a carga de trabalho.

Quando um servidor atinge o limite de armazenamento alocado, o servidor é marcado como somente leitura. No entanto, se você habilitar o crescimento automático do armazenamento, o armazenamento do servidor aumentará para acomodar os dados em crescimento. Para servidores com menos de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado aumenta em 5 GB assim que o armazenamento livre está abaixo do maior que 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado aumenta em 5% quando o espaço livre de armazenamento está abaixo de 5% do tamanho de armazenamento provisionado. Os limites de armazenamento máximos conforme especificado [aqui](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) se aplicam.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa:
- Um [servidor de Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Habilitar crescimento automático de armazenamento 

Siga estas etapas para definir o crescimento automático do armazenamento do servidor PostgreSQL:

1. No [portal do Azure](https://portal.azure.com/), selecione o servidor do banco de dados do Azure para PostgreSQL existente.

2. Na página do servidor PostgreSQL, em **configurações**, clique em **tipo de preço** para abrir a página tipo de preço.

3. Na seção **crescimento automático** , selecione **Sim** para habilitar o crescimento automático do armazenamento.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Banco de dados do Azure para PostgreSQL-Settings_Pricing_tier-aumento automático":::

4. Clique em **OK** para salvar as alterações.

5. Uma notificação confirmará que o crescimento automático foi habilitado com êxito.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Banco de dados do Azure para PostgreSQL-êxito no crescimento automático":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como criar alertas sobre métricas](howto-alert-on-metric.md).
