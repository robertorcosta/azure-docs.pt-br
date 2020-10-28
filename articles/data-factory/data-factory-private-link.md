---
title: Link privado do Azure para Azure Data Factory
description: Saiba mais sobre como funciona o link privado do Azure no Azure Data Factory.
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
ms.openlocfilehash: c21b4d746d763f41f4360cf93f67939bcd6dc49f
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632678"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Link privado do Azure para Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Usando o link privado do Azure, você pode se conectar a várias implantações de PaaS (plataforma como serviço) no Azure por meio de um ponto de extremidade privado. Um ponto de extremidade privado é um endereço IP privado em uma rede virtual e sub-rede específica. Para obter uma lista de implantações de PaaS que dão suporte à funcionalidade de link privado, consulte a [documentação do link privado](../private-link/index.yml). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Comunicação segura entre redes do cliente e Azure Data Factory 
Você pode configurar uma rede virtual do Azure como uma representação lógica da sua rede na nuvem. Isso oferece os seguintes benefícios:
* Você ajuda a proteger seus recursos do Azure contra ataques em redes públicas.
* Você permite que as redes e Data Factory se comuniquem com segurança entre si. 

Você também pode conectar uma rede local à sua rede virtual Configurando uma conexão VPN (IPsec de protocolo de Internet) (site a site), ou uma conexão do Azure ExpressRoute (emparelhamento privado). 

Você também pode instalar um tempo de execução de integração auto-hospedado em um computador local ou em uma máquina virtual na rede virtual. Isso permite que você:
* Execute atividades de cópia entre um armazenamento de dados de nuvem e um armazenamento de dados em uma rede privada.
* Despache as atividades de transformação em relação aos recursos de computação em uma rede local ou em uma rede virtual do Azure. 

Vários canais de comunicação são necessários entre Azure Data Factory e a rede virtual do cliente, conforme mostrado na tabela a seguir:

| Domínio | Porta | Descrição |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Um plano de controle, exigido pelo Data Factory criação e monitoramento. |
| `*.{region}.datafactory.azure.net` | 443 | Necessárias para que o runtime de integração auto-hospedada se conecte ao serviço do Data Factory. |
| `*.servicebus.windows.net` | 443 | Exigido pelo tempo de execução de integração auto-hospedado para criação interativa. |
| `download.microsoft.com` | 443 | Exigido pelo runtime de integração auto-hospedada para fazer o download das atualizações. |

Com o suporte ao link privado para Azure Data Factory, você pode:
* Crie um ponto de extremidade privado em sua rede virtual.
* Habilite a conexão privada para uma instância específica de data factory. 

As comunicações para Azure Data Factory serviço passam pelo link privado e ajudam a fornecer conectividade privada segura. Você não precisa configurar o domínio e a porta anteriores em uma rede virtual ou seu firewall corporativo para fornecer uma maneira mais segura de proteger seus recursos.  

![Diagrama de link privado para a arquitetura de Azure Data Factory.](./media/data-factory-private-link/private-link-architecture.png)

Habilitar o serviço de vínculo privado para cada um dos canais de comunicação anteriores oferece a seguinte funcionalidade:
- **Com suporte** :
   - Você pode criar e monitorar o data factory em sua rede virtual, mesmo se você bloquear todas as comunicações de saída.
   - As comunicações de comando entre o tempo de execução de integração auto-hospedado e o serviço de Azure Data Factory podem ser executadas com segurança em um ambiente de rede privada. O tráfego entre o tempo de execução de integração auto-hospedado e o serviço de Azure Data Factory passa por um link privado. 
- **Não tem suporte no momento** :
   - A criação interativa que usa um tempo de execução de integração auto-hospedado, como conexão de teste, lista de pastas de pesquisa e lista de tabelas, obter esquema e Visualizar dados, passa por um link privado.
   - A nova versão do tempo de execução de integração auto-hospedado pode ser baixada automaticamente do centro de download da Microsoft se você habilitar a atualização automática.

   > [!NOTE]
   > Para a funcionalidade que não tem suporte no momento, você ainda precisa configurar o domínio e a porta mencionados anteriormente na rede virtual ou no firewall corporativo. 

> [!WARNING]
> Ao criar um serviço vinculado, verifique se suas credenciais estão armazenadas em um cofre de chaves do Azure. Caso contrário, as credenciais não funcionarão quando você habilitar o link privado no Azure Data Factory.

## <a name="set-up-private-link-for-azure-data-factory"></a>Configurar link privado para Azure Data Factory
Você pode criar pontos de extremidade privados usando [o portal do Azure](../private-link/create-private-endpoint-portal.md).

Você também pode ir para sua data factory do Azure no portal do Azure e criar um ponto de extremidade privado, como mostrado aqui:

![Captura de tela do painel "conexões de ponto de extremidade privado" para criar um ponto de extremidade privado.](./media/data-factory-private-link/create-private-endpoint.png)


Se você quiser bloquear o acesso público ao data factory do Azure e permitir acesso somente por meio do link privado, desabilite o acesso à rede para Azure Data Factory no portal do Azure, conforme mostrado aqui:

![Captura de tela do painel "acesso à rede" para criar um ponto de extremidade privado.](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> A desabilitação do acesso à rede pública é aplicável somente ao tempo de execução de integração auto-hospedado, não a Azure Integration Runtime e SQL Server Integration Services (SSIS) Integration Runtime.

> [!NOTE]
> Você ainda pode acessar o portal de Azure Data Factory por meio de uma rede pública depois de desabilitar o acesso à rede pública.

## <a name="next-steps"></a>Próximas etapas

- [Criar um data factory usando a interface do usuário do Azure Data Factory](quickstart-create-data-factory-portal.md)
- [Introdução ao Azure Data Factory](introduction.md)
- [Criação visual no Azure Data Factory](author-visually.md)