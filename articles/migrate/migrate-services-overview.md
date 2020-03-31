---
title: Sobre as Migrações para Azure
description: Saiba mais sobre o serviço Migrações para Azure.
ms.topic: overview
ms.date: 03/22/2020
ms.custom: mvc
ms.openlocfilehash: a9723f15d496393d27bdd227ec1121a7878b37e0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80127740"
---
# <a name="about-azure-migrate"></a>Sobre as Migrações para Azure

Este artigo fornece uma visão geral rápida do serviço de Migrações para Azure.

As Migrações para Azure oferecem um hub centralizado para avaliar e migrar os servidores, a infraestrutura, os aplicativos e os dados locais para o Azure. As Migrações para Azure oferecem os seguintes recursos:

- **Plataforma de migração unificada**: Um único portal para iniciar, executar e acompanhar sua jornada de migração para o Azure.
- **Variedade de ferramentas**: Uma variedade de ferramentas para avaliação e migração. As ferramentas incluem as Migrações para Azure: Avaliação de Servidor e as Migrações para Azure: Migração de Servidor. As Migrações para Azure integram-se a outros serviços do Azure e a outras ferramentas e ofertas de ISVs (fornecedores independentes de software).
- **Avaliação e de migração**: no hub de Migrações para Azure, você pode avaliar e migrar:
    - **Servidores**: Avaliar e migrar servidores locais para VMs do Azure.
    - **Bancos de dados**: Avalie e migre bancos de dados locais para o BD SQL do Azure ou para a Instância Gerenciada do Azure SQL.
    - **Aplicativos Web**: Avalie e migre aplicativos Web locais para o Serviço de Aplicativo do Azure usando o Assistente do Serviço de Aplicativo do Azure.
    - **Áreas de trabalho virtual**: avalie e migre o VDI (Virtual Desktop Infrastructure) local para a Área de Trabalho Virtual do Windows no Azure.
    - **Dados**: Migre grandes quantidades de dados para o Azure de forma rápida e econômica usando os produtos Azure Data Box. 


## <a name="integrated-tools"></a>Ferramentas integradas

O hub das Migrações para Azure inclui estas ferramentas.

**Ferramenta** | **Avaliar/Migrar** | **Detalhes**
--- | --- | ---
**Migrações para Azure: Avaliação de Servidor** | Avaliar servidores. | Descubra e avalie as VMs do VMware locais, as VMs do Hyper-V e os servidores físicos para se preparar para a migração para o Azure.
**Migrações para Azure: Migração de Servidor** | Migrar servidores. | Migre VMs do VMware, VMs do Hyper-V, servidores físicos, outras máquinas virtualizadas e VMs da nuvem pública para o Azure. 
**Assistente de migração do banco de dados (DMA)** | Avalie os bancos de dados SQL Server locais para migração para o BD SQL do Azure, a Instância Gerenciada SQL do Azure ou VMs do Azure que executam o SQL Server. | O Assistente de Migração de Dados ajuda a identificar possíveis problemas de bloqueio para a migração. Ele identifica recursos sem suporte e novos recursos dos quais você pode se beneficiar após a migração, além de ajudar a identificar o caminho certo para a migração de banco de dados. [Saiba mais](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**DMS (Serviço de Migração de Banco de Dados)** | Migre bancos de dados locais para VMs do Azure executando SQL, BD SQL do Azure e Instâncias Gerenciadas do SQL do Azure. | [Saiba mais](https://docs.microsoft.com/azure/dms/dms-overview) sobre DMS.
**Movere** | Avaliar servidores. | [Saiba mais](#movere) sobre o Movere.
**Assistente de Migração de Aplicativo Web** | Avalie e migre aplicativos Web locais para o Azure. |  Use o Assistente de Migração do Serviço de Aplicativo do Azure para avaliar sites locais para migração para o Serviço de Aplicativo do Azure.<br/><br/> Use o Assistente para migrar aplicativos Web .NET e PHP para o Azure. [Saiba mais](https://appmigration.microsoft.com/) sobre o Assistente de Migração do Serviço de Aplicativo do Azure.
**Azure Data Box** | Migração de dados offline. | Use os produtos Azure Data Box offline para mover grandes quantidades de dados offline para o Azure. [Saiba mais](https://docs.microsoft.com/azure/databox/).

## <a name="isv-integration"></a>Integração de ISV

O recurso Migrações para Azure integra-se a várias ofertas de ISV. 

**ISV**    | **Recurso**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrar servidores
[Cloudamize](https://www.cloudamize.com/platform) | Avaliar servidores
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Avaliar e migrar servidores
[Device 42](https://docs.device42.com/) | Avaliar servidores
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrar servidores
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Avaliar servidores
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Avaliar servidores e bancos de dados


## <a name="azure-migrate-server-assessment-tool"></a>Avaliação da ferramenta Servidor das Migrações para Azure

A ferramenta Migrações para Azure: Avaliação de Servidor descobre e avalia VMs do VMware locais, VMs do Hyper-V e servidores físicos para migração para o Azure. Veja o que a ferramenta faz:

- **Preparação para o Azure:** avalia se os computadores locais estão prontos para a migração para o Azure.
- **Dimensionamento do Azure:** estima o tamanho das VMs do Azure após a migração.
- **Estimativa de custo do Azure:** Os custos estimados para a execução de computadores locais no Azure.
- **Análise de dependência:** se usar a Avaliação de Servidor com a [visualização de dependência](concepts-dependency-visualization.md), você poderá identificar de maneira eficaz as dependências entre servidores e otimizar estratégias para mover os servidores interdependentes para o Azure.


A Avaliação de Servidor usa um [dispositivo das Migrações para Azure](migrate-appliance.md) leve que você implanta localmente.

- O dispositivo é executado em uma VM ou servidor físico. Você pode instalá-lo facilmente usando um modelo baixado.
- O dispositivo descobre computadores locais e envia continuamente metadados de computadores e dados de desempenho para as Migrações para Azure.
- A descoberta do dispositivo é sem agente. Nada é instalado nos computadores descobertos.
- Após a descoberta do dispositivo, você reúne os computadores descobertos em grupos e executa avaliações para um grupo.

## <a name="azure-migrate-server-migration-tool"></a>Ferramenta de Migração de Servidor das Migrações para Azure

A ferramenta Migrações para Azure: Migração de Servidor ajuda você a migrar VMs do VMware locais, VMs do Hyper-V, servidores físicos, outros computadores virtualizados e VMs da nuvem pública para o Azure. Você pode migrar computadores depois de avaliá-los ou migrá-los sem uma avaliação. 

Para a migração sem agente de VMs do VMware e a migração de VMs do Hyper-V, a Migração de Servidor usa um dispositivo das Migrações para Azure que você implanta no local. O dispositivo também será usado se você configurar a avaliação do servidor, e é descrito na seção anterior.


## <a name="selecting-assessmentmigration-tools"></a>Selecionando ferramentas de avaliação/migração

No hub das Migrações para Azure, selecione a ferramenta que deseja usar para avaliação ou migração e adicione-a a um projeto de Migrações para Azure. Se você adicionar uma ferramenta de ISV ou o Movere:

- Para começar, obtenha uma licença ou se inscreva para uma avaliação gratuita, de acordo com as instruções da ferramenta. O licenciamento de ferramentas é determinado pelo ISV ou ferramenta. 
- Em cada ferramenta, há uma opção para se conectar às Migrações para Azure. Siga as instruções da ferramenta para se conectar.
- Controle seu percurso de migração de dentro do projeto das Migrações para Azure em todas as ferramentas.


## <a name="movere"></a>Movere

O Movere é uma plataforma SaaS que aumenta a business intelligence com a apresentação precisa de ambientes de TI inteiros em um único dia. À medida que as organizações crescem, mudam e se otimizam digitalmente, a solução fornece às empresas a confiança de que elas precisam para ter visibilidade e controle sobre seus ambientes, independentemente da plataforma, do aplicativo ou da geografia. O movere foi [adquirido](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) pela Microsoft e não é mais vendido como uma oferta autônoma.  O Movere está disponível por meio dos programas de Avaliação de Soluções Microsoft e Economia de Nuvem. [Saiba mais](https://www.movere.io) sobre o Movere. 

Incentivamos você a examinar também as Migrações para Azure – nosso serviço de migração interno. As Migrações para Azure fornecem um hub central para simplificar sua migração para a nuvem. O hub apresenta suporte abrangente para diferentes cargas de trabalho, incluindo servidores físicos e virtuais, bancos de dados e aplicativos. A visibilidade de ponta a ponta facilita o acompanhamento do progresso durante a descoberta, a avaliação e a migração. Com as ferramentas internas do Azure e do ISV parceiro, as Migrações para Azure também contam com um amplo intervalo de recursos, que incluem a descoberta de servidores físicos e virtuais, o dimensionamento correto baseado no desempenho, o planejamento de custos, as avaliações baseadas em importação e a análise da dependência de aplicativos sem agente. Se você estiver procurando ajuda especializada para começar, a Microsoft tem um [Provedor de Serviços Gerenciados Especialista no Azure](https://azure.microsoft.com/partners) para orientá-lo ao longo do percurso. Confira o [site das Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Versões das Migrações para Azure

Há duas versões do serviço de Migrações para Azure:

- **Versão atual**: use esta versão para criar projetos de Migrações para Azure, descobrir computadores locais e orquestrar avaliações e migrações. [Saiba mais](whats-new.md) sobre as novidades desta versão.
- **Versão anterior**: se você usou a versão anterior das Migrações para Azure (havia suporte apenas a avaliação de VMs do VMware locais), agora deve usar a versão atual. Não é mais possível criar projetos das Migrações para Azure usando a versão anterior e recomendamos que você não realize novas descobertas. Para acessar projetos existentes, no portal do Azure, pesquise e selecione **Migrações para Azure**. No painel **Migrações para Azure**, há uma notificação e um link para acessar projetos antigos das Migrações para Azure.



## <a name="next-steps"></a>Próximas etapas

- Experimente nossos tutoriais para avaliar [VMs do VMware](tutorial-prepare-vmware.md), [VMs do Hyper-V](tutorial-prepare-hyper-v.md) ou [servidores físicos](tutorial-prepare-physical.md).
- [Reveja as perguntas frequentes](resources-faq.md) sobre Migrações para Azure.
