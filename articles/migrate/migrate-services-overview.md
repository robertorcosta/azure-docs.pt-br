---
title: Sobre as Migrações para Azure
description: Saiba mais sobre a avaliação e a migração do servidor com o serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a64e7366281f15c94d6551c1f7be27f461737634
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185785"
---
# <a name="about-azure-migrate"></a>Sobre as Migrações para Azure

Este artigo fornece uma visão geral rápida das Migrações para Azure.

As Migrações para Azure ajudam você a migrar sua empresa do ambiente local para o Azure. As Migrações para Azure oferecem um hub centralizado para acompanhar a descoberta, a avaliação e a migração de infraestrutura, aplicativos e dados locais para o Azure.  As Migrações para Azure fornecem:

- **Plataforma de migração unificada**: Um único portal para iniciar, executar e acompanhar sua jornada de migração para o Azure.
- **Variedade de ferramentas**: O Hub fornece avaliação e migração para as ferramentas das Migrações para Azure e se integra a outros serviços do Azure, bem como a outras ferramentas e ofertas de ISV (fornecedor independente de software).
- **Cargas de trabalho**: As Migrações para Azure fornecem avaliação e migração para:
    - **Servidores**: Use a Avaliação de Servidor das Migrações para Azure, a Migração de Servidor das Migrações para Azure e outras ferramentas para avaliação e migração de servidores para as VMs do Azure.
    - **Bancos de dados**: Aproveite as ferramentas Microsoft e de ISV para avaliação e migração de bancos de dados locais para o BD SQL do Azure ou a Instância Gerenciada do SQL do Azure.
    - **Aplicativos Web**: Use o Assistente do Serviço de Aplicativo do Azure para avaliar sites locais para migração para o Serviço de Aplicativo do Azure.
    - **Áreas de trabalho virtual**: Use ferramentas de ISV para avaliar e migrar o Virtual Desktop Infrastructure (VDI) local para a Área de Trabalho Virtual do Windows no Azure.
    - **Data**: Use a família de produtos Azure Data Box para migrar grandes quantidades de dados de maneira rápida e econômica para o Azure.

## <a name="azure-migrate-versions"></a>Versões das Migrações para Azure

Atualmente, há duas versões do serviço de Migrações para Azure:

- **Versão atual**: use esta versão para criar projetos de Migrações para Azure, descobrir computadores locais e orquestrar avaliações e migrações. [Saiba mais](whats-new.md) sobre as novidades desta versão.
- **Versão anterior**: se você usou a versão anterior das Migrações para Azure (havia suporte apenas a avaliação de VMs do VMware locais), agora deve usar a versão atual. Não é mais possível criar projetos das Migrações para Azure usando a versão anterior e recomendamos que você não realize novas descobertas. Para acessar projetos existentes, no portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**. No painel das Migrações para Azure, há uma notificação e um link para acessar projetos antigos das Migrações para Azure.



## <a name="isv-integration"></a>Integração de ISV

Além das ferramentas nativas do Azure, as Migrações para Azure integram-se com várias ofertas de ISV. 

**ISV** | **Recurso**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrar servidores
[Cloudamize](https://www.cloudamize.com/platform) | Avaliar servidores
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Avaliar e migrar servidores
[Device 42](https://docs.device42.com/) | Avaliar servidores
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrar servidores
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Avaliar servidores
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Avaliar servidores e bancos de dados

## <a name="azure-tool-integration"></a>Integração de ferramentas do Azure

A tabela resume outras ferramentas que são integradas às Migrações para Azure.

**Ferramenta** | **Detalhes**
--- | ---
Migrações para Azure: Avaliação de Servidor | Avaliar servidores
Migrações para Azure: Migração de Servidor | Migrar servidores
DMA (Assistente de Migração de Banco de Dados) | Avaliar bancos de dados
DMS (Serviço de Migração de Banco de Dados) | Migrar bancos de dados
Movere | Avaliar servidores
Assistente de Migração de Aplicativo Web | Avaliar e migrar aplicativos Web



### <a name="selecting-a-tool"></a>Seleção de uma ferramenta

Identifique a ferramenta de que precisa e adicione-a em um projeto das Migrações para Azure.

- Se você estiver adicionando uma ferramenta de ISV ou Movere:
    - comece obtendo uma licença ou se inscrevendo para uma avaliação gratuita, de acordo com a política da ferramenta. O licenciamento de ferramentas está de acordo com o modelo de licenciamento de ferramentas ou de ISV.
    - Em cada ferramenta, há uma opção para se conectar às Migrações para Azure. Siga as instruções e a documentação da ferramenta para conectar a ferramenta às Migrações para Azure.
- Você controla de forma centralizada seu percurso de migração de dentro do projeto de Migrações para Azure, em todo o Azure e em outras ferramentas.



## <a name="azure-migrate-server-assessment-tool"></a>Avaliação da ferramenta Servidor das Migrações para Azure

Migrações para Azure: A ferramenta de avaliação de servidor descobre e avalia VMs do VMware, Hyper-V locais e servidores físicos para migração para o Azure. Ela ajuda a identificar o seguinte:

- **Preparação para o Azure:** Avalie se os computadores locais estão prontos para a migração para o Azure.
- **Dimensionamento do Azure:** O tamanho estimado das VMs do Azure após a migração.
- **Estimativa de custo do Azure:** Os custos estimados para a execução de computadores locais no Azure.
- **Visualização de dependências:** Dependências entre servidores (se a visualização de dependência estiver habilitada) e maneiras ideais de mover servidores dependentes para o Azure.

A Avaliação do Servidor usa um dispositivo leve que você implanta localmente e registra na Avaliação do Servidor.

- O dispositivo descobre computadores locais.
- Ele conecta-se à Avaliação do Servidor e envia continuamente metadados de computadores e dados relacionados a desempenho para as Migrações para Azure.
- A descoberta do dispositivo é sem agente. Nada precisa ser instalado nos computadores descobertos.
- Após a descoberta, você coleta computadores descobertos em grupos. Normalmente, você reúne computadores que gostaria de migrar juntos.
- Crie uma avaliação para um grupo. Em seguida, você pode analisar a avaliação para descobrir sua estratégia de migração.

## <a name="azure-migrate-server-migration-tool"></a>Ferramenta de Migração de Servidor das Migrações para Azure

Migrações para Azure: A ferramenta de Migração de Servidor ajuda você a migrar VMs do VMware e Hyper-V locais, servidores físicos, outros computadores virtualizados e VMs de nuvem pública para o Azure. Você pode migrar computadores depois de avaliá-los ou sem uma avaliação.


## <a name="database-migration-assistant"></a>Assistente de Migração de Banco de Dados

As Migrações para Azure integram-se com o AMD (Assistente de Migração de Dados) da Microsoft para avaliar bancos de dados do SQL Server locais para migração para BD SQL do Azure, Instância Gerenciada do SQL do Azure ou VMs do Azure em execução no SQL Server. O AMD fornece informações sobre possíveis problemas de bloqueio para a migração. Ele identifica recursos não compatíveis e também novos recursos dos quais você pode se beneficiar após a migração, além de ajudar a identificar o caminho certo para a migração de banco de dados. [Saiba mais](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration-service"></a>Serviço de Migração de Banco de Dados

As Migrações para Azure integram-se ao DMS (Serviço de Migração de Banco de Dados do Azure) para migrar bancos de dados locais para o Azure. Use o DMS para migrar bancos de dados locais para VMs do Azure executando SQL, BD SQL do Azure e Instâncias Gerenciadas do SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="movere"></a>Movere

 
O Movere é uma plataforma SaaS que aumenta a business intelligence com a apresentação precisa de ambientes de TI inteiros em um único dia. À medida que as organizações crescem, mudam e se otimizam digitalmente, a solução fornece às empresas a confiança de que elas precisam para ter visibilidade e controle sobre seus ambientes, independentemente da plataforma, do aplicativo ou da geografia. O movere foi [adquirido](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) pela Microsoft e não é mais vendido como uma oferta autônoma.  O Movere está disponível por meio dos programas de Avaliação de Soluções Microsoft e Economia de Nuvem. [Saiba mais](https://www.movere.io) sobre o Movere. Se você tiver dúvidas, envie-as para: movereq@microsoft.com ou entre em contato com seu representante Microsoft.

Incentivamos você a examinar também as Migrações para Azure – nosso serviço de migração interno. As Migrações para Azure fornecem um hub central para simplificar sua migração para a nuvem. O hub apresenta suporte abrangente para diferentes cargas de trabalho, incluindo servidores físicos e virtuais, bancos de dados e aplicativos. A visibilidade de ponta a ponta facilita o acompanhamento do progresso durante a descoberta, a avaliação e a migração. Com as ferramentas internas do Azure e do ISV parceiro, as Migrações para Azure também contam com um amplo intervalo de recursos, que incluem a descoberta de servidores físicos e virtuais, o dimensionamento correto baseado no desempenho, o planejamento de custos, as avaliações baseadas em importação e a análise da dependência de aplicativos sem agente. Se você estiver procurando ajuda especializada para começar, a Microsoft tem um [Provedor de Serviços Gerenciados Especialista no Azure](https://azure.microsoft.com/partners) para orientá-lo ao longo do percurso. Confira o [site das Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="web-app-migration-assistant"></a>Assistente de Migração de Aplicativo Web

As Migrações para Azure integram-se com o Assistente de Migração de serviço de aplicativo do Azure. No hub de Migrações para Azure, você pode avaliar e migrar aplicativos Web locais para o Azure, usando o Assistente, conforme a seguir:

- **Avaliar aplicativos Web Online**: Use Assistente de Migração do Serviço de Aplicativo do Azure para avaliar sites locais para migração para o Serviço de Aplicativo do Azure.
- **Migrar aplicativos Web**: Migre aplicativos Web .NET e PHP para o Azure usando o Assistente de Migração do Serviço de Aplicativo do Azure.

[Saiba mais](https://appmigration.microsoft.com/) sobre o Assistente.



## <a name="offline-data-migration"></a>Migração de dados offline

Você pode usar os produtos Azure Data Box offline para mover grandes quantidades de dados offline para o Azure. [Saiba mais](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Próximas etapas

- Experimente nossos tutoriais para avaliar [VMs VMware](tutorial-assess-vmware.md) e [VMs Hyper-V](tutorial-assess-hyper-v.md).
- [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços das Migrações para Azure.
- [Reveja as perguntas frequentes](resources-faq.md) sobre Migrações para Azure.
