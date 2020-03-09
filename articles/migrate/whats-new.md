---
title: Novidades nas Migrações para Azure
description: Conheça as novidades e as atualizações recentes no serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 63b6783a2f36d5bc9e84ce8291e7025b27359b6c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361748"
---
# <a name="whats-new-in-azure-migrate"></a>Novidades nas Migrações para Azure

As [Migrações para Azure](migrate-services-overview.md) ajudam a descobrir, avaliar e migrar servidores, aplicativos e dados para a nuvem do Microsoft Azure. Este artigo resume os novos recursos nas Migrações para Azure.



## <a name="update-november-2019"></a>Atualização (novembro de 2019)

Vários novos recursos foram adicionados à migração do Azure:

- **Avaliação de servidor físico**. Agora há suporte para a avaliação de servidores físicos locais, além da migração de servidor físico para a qual já há suporte.
- **Avaliação baseada em importação**. Agora há suporte para a avaliação de computadores que usam metadados e dados de desempenho fornecidos em um arquivo CSV.
- **Descoberta de aplicativos**: o migrações para Azure agora dá suporte à descoberta em nível de aplicativo de aplicativos, funções e recursos usando o dispositivo de migrações para Azure. Atualmente, isso tem suporte apenas para VMs VMware e é limitado apenas à descoberta (atualmente, não há suporte para avaliação). [Saiba mais](how-to-discover-applications.md)
- **Visualização de dependência sem agente**: você não precisa mais instalar agentes explicitamente para visualização de dependência. Agora há suporte para baseados em agente e sem agente.
- **Área de trabalho virtual**: Use ferramentas de ISV para avaliar e migrar a infraestrutura de área de trabalho virtual (VDI) local para a área de trabalho virtual do Windows no Azure.
- **Aplicativo Web**: o serviço de Azure app assistente de migração, usado para avaliar e migrar aplicativos Web, agora está integrado às migrações para Azure.

Novas ferramentas de avaliação e migração foram adicionadas às Migrações para Azure:

- **Rack**: oferta de migração na nuvem.
- **Mover**: oferecer avaliação.

[Saiba mais](migrate-services-overview.md) sobre como usar ferramentas e ofertas de ISV para avaliação e migração nas Migrações para Azure.

## <a name="release-version-july-2019"></a>Versão de lançamento (Julho 2019)

Uma nova versão das Migrações para Azure foi lançada em julho de 2019.

- **Versão atual**: Use esta versão para criar projetos de migrações para Azure, descobrir computadores locais e orquestrar avaliações e migrações.
- **Versão anterior**: para o cliente que usa a versão anterior da migração do Azure (somente a avaliação de VMs do VMware local foi suportada), agora você deve usar a versão atual. Na versão anterior, você não pode mais criar projetos das Migrações para Azure ou executar novas descobertas. Você ainda pode acessar os projetos existentes. Para fazer isso no portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**. Nas notificações das Migrações para Azure, há um link para acessar projetos antigos das Migrações para Azure.


### <a name="azure-migrate-features"></a>Recursos das Migrações para Azure

A versão atual das Migrações para Azure oferece inúmeros recursos novos:


- **Plataforma de migração unificada**: as migrações para Azure agora fornecem um único portal para centralizar, gerenciar e acompanhar sua jornada de migração para o Azure, com uma experiência aprimorada de fluxo de implantação e Portal.
- **Ferramentas de avaliação e migração**: as migrações para Azure fornecem ferramentas nativas e se integram a outros serviços do Azure, bem como a ferramentas ISV (fornecedor independente de software). [Saiba mais](migrate-services-overview.md#isv-integration) sobre a integração com ISV.
- **Avaliação de migrações para Azure**: usando a ferramenta de avaliação de servidor de migrações para Azure, você pode avaliar VMs do VMware e VMs do Hyper-V para migração para o Azure. Também é possível avaliar quanto à migração usando outros serviços do Azure e ferramentas ISV.
- **Migração de migrações para Azure**: usando a ferramenta de migração de servidor de migrações para Azure, você pode migrar VMs VMware locais e VMs do Hyper-V para o Azure, bem como servidores físicos, outros servidores virtualizados e VMs de nuvem privada/pública. Além disso, é possível migrar para o Azure usando as ferramentas de ISV.
- **Dispositivo de migrações para Azure**: as migrações para Azure implantam um dispositivo leve para descoberta e avaliação de VMs VMware locais e VMs do Hyper-V.
    - Esse dispositivo é usado pela Avaliação de Servidor das Migrações para Azure e pela Migração de Servidor das Migrações para Azure para uma migração sem agente.
    - O dispositivo descobre continuamente os metadados do servidor e os dados de desempenho para fins de avaliação e migração.  
- **Migração de VM do VMware**: migração de servidor de migrações para Azure fornece alguns métodos para migrar VMs VMware locais para o Azure.  Uma migração sem agente que usa o dispositivo de Migrações para Azure e uma migração baseada em agente que usa um dispositivo de replicação e implanta um agente em cada VM que você deseja migrar. [Saiba mais](server-migrate-overview.md)
 - **Avaliação e migração de banco de dados**: de migrações para Azure, você pode avaliar bancos de dados locais para migração para o Azure usando o assistente de migração de banco de dados do Azure. É possível migrar bancos de dados usando o Serviço de Migração de Banco de Dados do Azure.
- **Migração de aplicativo Web**: você pode avaliar aplicativos Web usando uma URL de ponto de extremidade pública com o serviço Azure app. Para a migração de aplicativos .NET internos, é possível baixar e executar o Assistente de Migração do Serviço de Aplicativo.
- **Data Box**: importe dados offline de grandes quantidades no azure usando Azure data Box em migrações para Azure.


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços das Migrações para Azure.
- [Reveja as perguntas frequentes](resources-faq.md) sobre Migrações para Azure.
- Experimente nossos tutoriais para avaliar [VMs VMware](tutorial-assess-vmware.md) e [VMs Hyper-V](tutorial-assess-hyper-v.md).
