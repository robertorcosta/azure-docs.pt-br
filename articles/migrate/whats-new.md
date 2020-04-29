---
title: Novidades nas Migrações para Azure
description: Conheça as novidades e as atualizações recentes no serviço Migrações para Azure.
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: ba85b0a4ed30320099388ccb48dd91ad6445efd5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677239"
---
# <a name="whats-new-in-azure-migrate"></a>Novidades nas Migrações para Azure

As [Migrações para Azure](migrate-services-overview.md) ajudam a descobrir, avaliar e migrar servidores, aplicativos e dados para a nuvem do Microsoft Azure. Este artigo resume novas versões e novos recursos nas Migrações para Azure.


## <a name="update-april-2020"></a>Atualização (abril de 2020)

As Migrações para Azure dão suporte a implantações no Azure Government. 

- Você pode descobrir e avaliar VMs do VMware, VMs do Hyper-V e servidores físicos.
- Você pode migrar VMs do VMware, VMs do Hyper-V e servidores físicos para o Azure.
- A migração de VMware pode ser sem agente ou baseada em agente. [Saiba mais](server-migrate-overview.md).
- [Examine](migrate-support-matrix.md#supported-geographies-azure-government) as geografias e as regiões compatíveis com o Azure Government.
- A [análise de dependência baseada em agente](concepts-dependency-visualization.md#agent-based-analysis) não é compatível com o Azure Government.
- Os recursos da versão prévia, especificamente a [análise de dependência sem agente](concepts-dependency-visualization.md#agentless-analysis) e a [descoberta de aplicativos](how-to-discover-applications.md), são compatíveis com o Azure Government.

## <a name="update-march-2020"></a>Atualização (março de 2020)

Uma instalação baseada em script está disponível para configuração no [Dispositivo das Migrações para Azure](migrate-appliance.md):

- A instalação baseada em script é uma alternativa à instalação do .OVA (VMware)/VHD (Hyper-V) do dispositivo.
- Ela fornece um script de instalador do PowerShell que pode ser usado para configurar o dispositivo para o VMware/Hyper-V em um computador existente que executa o Windows Server 2016.

## <a name="update-november-2019"></a>Atualização (novembro de 2019)

Vários novos recursos foram adicionados à migração do Azure:

- **Avaliação de servidor físico**. Agora há suporte para a avaliação de servidores físicos locais, além da migração de servidor físico para a qual já há suporte.
- **Avaliação baseada em importação**. Agora há suporte para a avaliação de computadores que usam metadados e dados de desempenho fornecidos em um arquivo CSV.
- **Descoberta de aplicativo**: As Migrações para Azure agora dão suporte à descoberta em nível de aplicativo de funções, aplicativos e recursos usando o dispositivo de migrações para Azure. Atualmente, isso tem suporte apenas para VMs VMware e é limitado apenas à descoberta (atualmente, não há suporte para avaliação). [Saiba mais](how-to-discover-applications.md)
- **Visualização de dependência sem agente**: Você não precisa mais instalar agentes explicitamente para a visualização de dependência. Agora há suporte para baseados em agente e sem agente.
- **Área de Trabalho Virtual**: Use ferramentas de ISV para avaliar e migrar o Virtual Desktop Infrastructure (VDI) local para a Área de Trabalho Virtual do Windows no Azure.
- **Aplicativo Web**: O Assistente de Migração do Serviço de Aplicativo azure, usado para avaliar e migrar aplicativos Web, agora está integrado às migrações para Azure.

Novas ferramentas de avaliação e migração foram adicionadas às Migrações para Azure:

- **Rackware**: oferta de migração para a nuvem.
- **Movere**: oferta de avaliação.

[Saiba mais](migrate-services-overview.md) sobre como usar ferramentas e ofertas de ISV para avaliação e migração nas Migrações para Azure.

## <a name="azure-migrate-current-version"></a>Versão atual das Migrações para Azure

A versão atual das Migrações para Azure (lançada em julho de 2019) oferece diversos novos recursos:

- **Plataforma de migração unificada**: as Migrações para Azure agora fornecem um único portal para centralizar, gerenciar e acompanhar sua jornada de migração para o Azure, com um fluxo de implantação e uma experiência de portal aprimorados.
- **Ferramentas de avaliação e de migração**: as Migrações para Azure fornecem ferramentas nativas e se integram a outros serviços do Azure, bem como a ferramentas de ISV (fornecedor independente de software). [Saiba mais](migrate-services-overview.md#isv-integration) sobre a integração com ISV.
- **Avaliação das Migrações para Azure**: usando a ferramenta de Avaliação de Servidor das Migrações para Azure, é possível avaliar VMs do VMware e Hyper-V quanto à migração para o Azure. Também é possível avaliar quanto à migração usando outros serviços do Azure e ferramentas ISV.
- **Migração das Migrações para Azure**: usando a ferramenta de Migração de Servidor das Migrações para Azure, é possível migrar VMs do VMware e Hyper-V locais para o Azure, além de servidores físicos, outros servidores virtualizados e VMs de nuvem pública/privada. Além disso, é possível migrar para o Azure usando as ferramentas de ISV.
- **Dispositivo de Migrações para Azure**: as Migrações para Azure implantam um dispositivo leve para descoberta e avaliação de VMs do VMware e Hyper-V locais.
    - Esse dispositivo é usado pela Avaliação de Servidor das Migrações para Azure e pela Migração de Servidor das Migrações para Azure para uma migração sem agente.
    - O dispositivo descobre continuamente os metadados do servidor e os dados de desempenho para fins de avaliação e migração.  
- **Migração da VM do VMware**:  A Migração de Servidor das Migrações para Azure oferecem alguns métodos para migrar VMs do VMware locais para o Azure.  Uma migração sem agente que usa o dispositivo de Migrações para Azure e uma migração baseada em agente que usa um dispositivo de replicação e implanta um agente em cada VM que você deseja migrar. [Saiba mais](server-migrate-overview.md)
 - **Avaliação e migração do banco de dados**: nas Migrações para Azure, é possível avaliar bancos de dados locais quanto à migração para o Azure usando o Assistente de Migração de Dados do Azure. É possível migrar bancos de dados usando o Serviço de Migração de Banco de Dados do Azure.
- **Migração de aplicativos Web**: é possível avaliar os aplicativos Web usando uma URL de ponto de extremidade pública com o Serviço de Aplicativo do Azure. Para a migração de aplicativos .NET internos, é possível baixar e executar o Assistente de Migração do Serviço de Aplicativo.
- **Data Box**: importe grandes quantidades de dados offline para o Azure usando o Azure Data Box nas Migrações para Azure.

## <a name="azure-migrate-previous-version"></a>Versão anterior das Migrações para Azure

se você estava usando a versão anterior das Migrações para Azure (havia suporte apenas a avaliação de VMs do VMware locais), agora deve usar a versão atual. Na versão anterior, você não pode mais criar projetos das Migrações para Azure ou executar novas descobertas. Você ainda pode acessar os projetos existentes. Para fazer isso no portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**. Nas notificações das Migrações para Azure, há um link para acessar projetos antigos das Migrações para Azure.



## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços das Migrações para Azure.
- [Reveja as perguntas frequentes](resources-faq.md) sobre Migrações para Azure.
- Experimente nossos tutoriais para avaliar [VMs VMware](tutorial-assess-vmware.md) e [VMs Hyper-V](tutorial-assess-hyper-v.md).
