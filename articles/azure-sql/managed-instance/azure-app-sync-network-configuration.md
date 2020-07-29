---
title: Sincronizar a configuração de rede para o serviço de Azure App
titleSuffix: Azure SQL Managed Instance
description: Este artigo discute como sincronizar sua configuração de rede para Azure App plano de Hospedagem de serviço com o Instância Gerenciada do SQL do Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: a0192f204fdb0797d98947a174bf6d669034b666
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695446"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Sincronizar a configuração de rede para Azure App plano de Hospedagem de serviço com o SQL do Azure Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Pode acontecer que, embora você tenha [integrado seu aplicativo a uma rede virtual do Azure](../../app-service/web-sites-integrate-with-vnet.md), não é possível estabelecer uma conexão com o SQL instância gerenciada. Atualização, ou sincronização, a configuração de rede do seu plano de serviço pode resolver esse problema. 

## <a name="sync-network-configuration"></a>Configuração de rede de sincronização 

Para fazer isso, siga estas etapas:  

1. Vá até o seu plano do Serviço de Aplicativo dos aplicativos web.

   ![Captura de tela do plano do serviço de aplicativo](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Selecione **rede** e, em seguida, selecione **clique aqui para gerenciar**.

   ![Captura de tela de gerenciar plano de serviço](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Selecione seu **VNet** e clique em **Sincronizar rede**.

   ![Captura de tela da rede de sincronização](./media/azure-app-sync-network-configuration/sync.png)

4. Aguarde até que a sincronização seja feita.
  
   ![Captura de tela de sincronização concluída](./media/azure-app-sync-network-configuration/sync-done.png)

Agora você está pronto para tentar restabelecer sua conexão com o SQL Instância Gerenciada.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como configurar sua VNet para o SQL Instância Gerenciada, consulte [arquitetura de vnet do sql instância gerenciada](connectivity-architecture-overview.md) e [como configurar a vnet existente](vnet-existing-add-subnet.md).
