---
title: Sobre Migrações para Azure | Microsoft Docs
description: Fornece uma visão geral do serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 10/22/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: def0832898170e0a278c403349eab1bd89050a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498745"
---
# <a name="about-azure-migrate"></a>Sobre as Migrações para Azure

Este artigo fornece uma visão geral rápida das Migrações para Azure.

As Migrações para Azure ajudam você a migrar de local para o Azure. As Migrações para Azure oferecem um hub centralizado para acompanhar a descoberta, a avaliação e a migração de infraestrutura, aplicativos e dados locais para o Azure. O hub fornece ferramentas e serviços do Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros. As Migrações para Azure fornecem:

- **Plataforma de migração unificada**: Um único portal para iniciar, executar e acompanhar sua jornada de migração para o Azure.
- **Variedade de ferramentas**: Ferramentas nativas e se integração a outros serviços do Azure, bem como a ferramentas de ISV. Selecione as ferramentas de avaliação e migração certas com base em seus requisitos organizacionais.
- **Cargas de trabalho**: As Migrações para Azure fornecem ferramentas de avaliação e migração para:
    - **Servidores**: Use ferramentas da Microsoft ou ferramentas de ISV para avaliação e migração de servidores para VMs do Azure.
    - **Bancos de dados**: Aproveite as ferramentas da Microsoft e ISV para avaliação e migração de bancos de dados locais para a Instância Gerenciada do Banco de Dados SQL do Azure ou o BD de SQL do Azure.
    - **Aplicativos Web**: Use o Assistente do Serviço de Aplicativo do Azure para avaliar sites locais para migração para o Serviço de Aplicativo do Azure.
    - **Áreas de trabalho virtual**: Use ferramentas de ISV para avaliar e migrar o Virtual Desktop Infrastructure (VDI) local para a Área de Trabalho Virtual do Windows no Azure.
    - **Data**: Use a família de produtos Azure Data Box para migrar dados de maneira rápida e econômica para o Azure.

## <a name="azure-migrate-versions"></a>Versões das Migrações para Azure

Atualmente, há duas versões do serviço de Migrações para Azure:

- **Versão atual**: use esta versão para criar projetos de Migrações para Azure, descobrir computadores locais e orquestrar avaliações e migrações. [Saiba mais](whats-new.md) sobre as novidades desta versão.
- **Versão anterior**: se você usou a versão anterior das Migrações para Azure (havia suporte apenas a avaliação de VMs do VMware locais), agora deve usar a versão atual. Não é mais possível criar projetos das Migrações para Azure usando a versão anterior e recomendamos que você não realize novas descobertas. Para acessar projetos existentes, no portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**. No painel das Migrações para Azure, há uma notificação e um link para acessar projetos antigos das Migrações para Azure.

## <a name="isv-integration"></a>Integração de ISV

Além das ferramentas nativas da Microsoft, as Migrações para Azure integram-se com várias ofertas de ISV. 

**ISV** | **Recurso**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Avaliar
[Device 42](https://docs.device42.com/) | Avaliar
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Avaliar
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Avaliar
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Avaliar e migrar
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrar
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar



### <a name="selecting-an-isv-tool"></a>Como selecionar uma ferramenta ISV

Você identifica a ferramenta de que precisa e a adiciona a um projeto de Migrações para Azure.

- Depois de adicionar uma ferramenta ISV, você começa obtendo uma licença ou se inscrevendo para uma avaliação gratuita, de acordo com a política de ISV. O licenciamento para ferramentas ISV está de acordo com o modelo de licenciamento ISV.
- Em cada ferramenta, há uma opção para se conectar às Migrações para Azure. Siga as instruções e a documentação da ferramenta para conectar a ferramenta às Migrações para Azure.L
- Você controla centralmente sua jornada de migração de dentro do projeto de Migrações para Azure, entre ferramentas do Azure e do ISV.


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


## <a name="database-assessment"></a>Avaliação do banco de dados

As Migrações para Azure integram-se com o AMD (Assistente de Migração de Dados) da Microsoft para avaliar bancos de dados do SQL Server locais para migração para BD SQL do Azure, Instância Gerenciada do SQL do Azure ou VMs do Azure em execução no SQL Server. O AMD fornece informações sobre possíveis problemas de bloqueio para a migração. Ele identifica recursos não compatíveis e também novos recursos dos quais você pode se beneficiar após a migração, além de ajudar a identificar o caminho certo para a migração de banco de dados. [Saiba mais](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration"></a>Migração de banco de dados

As Migrações para Azure integram-se ao DMS (Serviço de Migração de Banco de Dados do Azure) para migrar bancos de dados locais para o Azure. Use o DMS para migrar bancos de dados locais para VMs do Azure executando SQL, BD SQL do Azure e Instâncias Gerenciadas do SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-migration"></a>Migração de aplicativos Web

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
