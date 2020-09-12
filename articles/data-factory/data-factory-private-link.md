---
title: Link privado do Azure para Azure Data Factory
description: Saiba mais sobre o link privado do Azure funciona no Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 164a1005d9382711874b644e14b23d2154d613a0
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596015"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Link privado do Azure para Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

O Link Privado permite que você se conecte a vários serviços de PaaS no Azure por meio de um ponto de extremidade privado. Para obter uma lista de serviços de PaaS que dão suporte à funcionalidade de link privado, vá para a [página de documentação do link privado](https://docs.microsoft.com/azure/private-link/). Um ponto de extremidade privado é um endereço IP privado em uma rede virtual e sub-rede específica.

## <a name="secure-communication-between-customer-network-and-azure-data-factory-service"></a>Comunicação segura entre a rede do cliente e o serviço de Azure Data Factory
Para proteger os recursos do Azure contra ataques na rede pública ou deixá-los se comunicarem com segurança entre si, você pode configurar uma rede virtual do Azure como uma representação lógica da sua rede na nuvem. Você também pode conectar uma rede local à sua rede virtual Configurando a VPN IPSec (site a site) ou o ExpressRoute (emparelhamento privado). O Integration Runtime auto-hospedado pode ser instalado em um computador local ou em uma máquina virtual na rede virtual para executar atividades de cópia entre um armazenamento de dados de nuvem e um armazenamento de dados em uma rede privada ou distribuir atividades de transformação em relação aos recursos de computação em uma rede local ou em uma rede virtual do Azure. 

Há vários canais de comunicação necessários entre Data Factory e a rede virtual do cliente.


| **Domínio** | **Porta** | **Descrição** |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Plano de controle. Exigido por Data Factory criação e monitoramento. |
| `*.{region}.datafactory.azure.net` | 443 | Exigido pelo Integration Runtime auto-hospedado para se conectar ao serviço de Data Factory. |
| `*.servicebus.windows.net` | 443 | Exigido pelo Integration Runtime auto-hospedado para criação interativa. |
| `download.microsoft.com` | 443 | Exigido pelo Integration Runtime auto-hospedado para baixar as atualizações. |


Com o suporte do link privado do Azure para Azure Data Factory, você pode criar um ponto de extremidade privado (PE) em sua rede virtual e habilitar a conexão privada para Azure Data Factory específicas. As comunicações para Azure Data Factory serviço passam pelo link privado do Azure que fornece conectividade privada segura. E você não precisa configurar o domínio e a porta acima na rede virtual ou o firewall corporativo que fornece uma maneira mais segura de proteger seus recursos.  

![Arquitetura de link particular Azure Data Factory](./media/data-factory-private-link/private-link-architecture.png)

Aqui estão os benefícios para habilitar o serviço de link privado para cada um dos canais de comunicação descritos acima:
- Porta Você pode fazer a criação e o monitoramento de Azure Data Factory em sua rede virtual, mesmo que bloqueie todas as comunicações de saída.
- Porta As comunicações de comando entre o autohospedado Integration Runtime e o serviço de Azure Data Factory podem ser executadas com segurança em um ambiente de rede privada. O tráfego entre o Integration Runtime autohospedado e o serviço de Azure Data Factory passa por um link privado. 
- (Não tem suporte no momento) A criação interativa usando o autohospedado Integration Runtime passar pelo link privado, como conexão de teste, procurar lista de pastas e lista de tabelas, obter esquema e Visualizar dados.
- (Não tem suporte no momento) A nova versão do Integration Runtime auto-hospedado pode ser baixada automaticamente do centro de download se você habilitar a atualização automática.

> [!NOTE]
> Para funcionalidade que não tem suporte no momento, você ainda precisa configurar o domínio e a porta acima na rede virtual ou no firewall corporativo. 

> [!WARNING]
> Ao criar um serviço vinculado, verifique se a credencial está armazenada no Azure Key Vault. Caso contrário, ele não funcionará quando você habilitar o serviço de vínculo privado no Azure Data Factory.

## <a name="how-to-set-up-private-link-for-azure-data-factory"></a>Como configurar o link privado para Azure Data Factory
Os pontos de extremidade privados podem ser criados usando o portal do Azure, o PowerShell ou a CLI do Azure:

[Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)


Você também pode navegar até sua Azure Data Factory em portal do Azure e criar um ponto de extremidade privado (PE):

![Criar um ponto de extremidade privado](./media/data-factory-private-link/create-private-endpoint.png)


Se você quiser bloquear o acesso público a esse Azure Data Factory e permitir somente o acesso por meio do link privado, poderá desabilitar o acesso à rede de Azure Data Factory no portal do Azure:

![Criar um ponto de extremidade privado](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> A desabilitação do acesso à rede pública só é aplicável a Integration Runtime hospedados internamente, não a Azure Integration Runtime e Integration Runtime SSIS.

> [!NOTE]
> Os usuários ainda podem acessar o portal Azure Data Factory por meio da rede pública depois de desabilitar o acesso à rede pública.

## <a name="next-steps"></a>Próximas etapas

- [Criar um data factory usando a interface do usuário do Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Introdução ao Azure Data Factory](introduction.md)

- [Criação visual no Azure Data Factory](author-visually.md)

