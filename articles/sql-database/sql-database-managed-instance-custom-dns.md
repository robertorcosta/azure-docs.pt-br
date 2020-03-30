---
title: DNS personalizado de instância gerenciada
description: Este tópico descreve opções de configuração para um DNS personalizado com uma Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 9c98176413f858d236902c332553d47f749aa3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247071"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurar DNS personalizado para Instância Gerenciada do Banco de Dados SQL do Azure

Uma Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantada em uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) do Azure. Há alguns cenários (por exemplo, db mail, servidores vinculados a outras instâncias do SQL em seu ambiente de nuvem ou híbrido) que exigem nomes de hosts privados para serem resolvidos da Instância Gerenciada. Nesse caso, você precisa configurar um DNS personalizado dentro do Azure. 

Como a Instância Gerenciada usa o mesmo DNS para seu funcionamento interno, configure o servidor DNS personalizado para que ele possa resolver nomes de domínio público.

> [!IMPORTANT]
> Use sempre um nome de domínio totalmente qualificado (FQDN) para o servidor de e-mail, a instância do SQL Server e para outros serviços, mesmo que estejam dentro da sua zona de DNS privada. Por exemplo, `smtp.contoso.com` use para `smtp` o servidor de e-mail porque não resolverá corretamente. Criar um servidor ou replicação vinculado que faz referência a VMs SQL dentro da mesma rede virtual também requer um FQDN e um sufixo DNS padrão. Por exemplo, `SQLVM.internal.cloudapp.net`. Para obter mais informações, consulte [a resolução Nome que usa seu próprio servidor DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> A atualização de servidores DeSns de rede virtual não afetará a Instância Gerenciada imediatamente. A configuração de DNS de instância gerenciada é atualizada após o término da locação do DHCP ou após a atualização da plataforma, o que ocorrer primeiro. **Os usuários são aconselhados a definir sua configuração de DNS de rede virtual antes de criar sua primeira instância gerenciada.**

## <a name="next-steps"></a>Próximas etapas

- Para uma visão geral, consulte [o que é uma instância gerenciada](sql-database-managed-instance.md)
- Para obter um tutorial mostrando como criar uma nova Instância Gerenciada, consulte [Criando uma Instância Gerenciada](sql-database-managed-instance-get-started.md).
- Para obter informações sobre como configurar sua rede virtual à Instância Gerenciada, consulte [Configuração da rede virtual de Instâncias Gerenciadas](sql-database-managed-instance-connectivity-architecture.md)
