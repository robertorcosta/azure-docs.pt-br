---
title: Armazenamento de crescimento automático - Portal Azure - Banco de dados Azure para MariaDB
description: Este artigo descreve como você pode habilitar o armazenamento de crescimento automático para o Banco de Dados Azure para MariaDB usando o portal Azure
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7ec4c7a61725fa02f00bf38048182d640666ae8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528890"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Armazenamento de crescimento automático no Banco de Dados Azure para MariaDB usando o portal Azure
Este artigo descreve como você pode configurar um banco de dados Azure para que o armazenamento do servidor MariaDB cresça sem afetar a carga de trabalho.

Quando um servidor atinge o limite de armazenamento alocado, o servidor é marcado como somente leitura. No entanto, se você habilitar o crescimento automático do armazenamento, o armazenamento do servidor aumentará para acomodar os dados crescentes. Para servidores com menos de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado é aumentado em 5 GB assim que o armazenamento gratuito estiver abaixo do maior de 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento livre está abaixo de 5% do tamanho de armazenamento provisionado. Os limites máximos de armazenamento conforme especificado [aqui](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) se aplicam.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa:
- Um [banco de dados Azure para servidor MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Permitir o crescimento automático do armazenamento 

Siga estas etapas para definir o crescimento automático do armazenamento do servidor MariaDB:

1. No [portal Azure,](https://portal.azure.com/)selecione o banco de dados Azure existente para o servidor MariaDB.

2. Na página do servidor MariaDB, em **Configurações,** clique em **Nível de preços** para abrir a página de nível de preços.

3. Na seção De crescimento automático, selecione **Sim** para permitir o crescimento automático do armazenamento.

    ![Banco de dados Azure para MariaDB - Settings_Pricing_tier - Auto-growth](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Clique em **OK** para salvar as alterações.

5. Uma notificação confirmará que o crescimento automático foi habilitado com sucesso.

    ![Banco de dados Azure para MariaDB - sucesso de crescimento automático](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Próximas etapas

Saiba [como criar alertas em métricas.](howto-alert-metric.md)
