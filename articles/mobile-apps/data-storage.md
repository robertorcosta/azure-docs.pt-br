---
title: Armazenar, gerenciar e persistir dados de aplicativos na nuvem com os serviços do Visual Studio App Center e do Azure
description: Conheça serviços como o Visual Studio App Center que permitem armazenar, gerenciar e persistir dados de aplicativos móveis na nuvem.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 166847325fa9094136f1c2a143f1751420f05f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240904"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Armazenar, sincronizar e consultar dados de aplicativos móveis da nuvem
Não importa que tipo de aplicação você constrói, você provavelmente irá gerar e processar dados. Os usuários do seu aplicativo têm altas expectativas. Eles querem que a aplicação funcione rápido e perfeitamente, em todas as circunstâncias. A maioria dos aplicativos também funciona em vários dispositivos. Você pode acessar seu aplicativo a partir de um desktop ou dispositivo móvel. Vários usuários podem usar o aplicativo ao mesmo tempo e compartilhar dados com a expectativa de obter acesso instantâneo e em tempo real aos dados.

Os usuários do aplicativo nem sempre terão conectividade com a internet. Os aplicativos são projetados e espera-se que funcionem com ou sem conexão com a internet. Os desenvolvedores devem escolher a solução certa para armazenar e sincronizar seus dados na nuvem para fornecer uma ótima experiência ao cliente para sua aplicação.

A Microsoft fornece uma variedade de serviços que eliminam a necessidade de girar servidores, escolher seu banco de dados ou se preocupar com escala ou segurança para fornecer a experiência mais rica possível. Esses serviços fornecem uma ótima experiência de desenvolvedor que permite armazenar dados de aplicativos na nuvem usando APIs SQL ou NoSQL. Você também pode sincronizar dados em todos os dispositivos e permitir que o aplicativo funcione com ou sem uma conexão de rede para ajudar a construir aplicativos escaláveis e robustos.

Use os seguintes serviços para gerenciar e armazenar dados de aplicativos móveis na nuvem.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Data](/appcenter/data/) é um serviço de gerenciamento de dados. Ele permite que os aplicativos gerenciem, persistam e sincronizem dados de aplicativos na nuvem em diferentes dispositivos e plataformas em cenários on-line e off-line. Construído em cima do Azure Cosmos DB, o App Center Data escala à medida que sua base de usuários e número de aplicativos crescem. Ele garante baixa latência, alta disponibilidade e alta escalabilidade para todos os seus dados.

**Principais características**
- Fácil provisionamento para um novo banco de dados Azure Cosmos DB ou um banco de dados Azure Cosmos DB existente do portal Visual Studio App Center.
- Suporte ao banco de dados NoSQL para armazenar, sincronizar e consultar facilmente dados de aplicativos.
- Construído em cima do Azure Cosmos DB, este serviço herda a maioria dos principais recursos oferecidos pelo Azure Cosmos DB e pode ser dimensionado globalmente para atender às suas necessidades de negócios.
- Recursos de sincronização on-line e off-line para sincronizar dados entre dispositivos.
- SDKs de cliente sdks móveis que você pode usar para gerenciar facilmente dados de aplicativos privados.
- Suporte à plataforma para iOS, Android, Xamarin e React Native.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Comece com os dados do App Center](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[O Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é um serviço de banco de dados multimodelo distribuído globalmente. Você pode usá-lo para construir aplicações em escala de planeta. Com o Azure Cosmos DB, você pode dimensionar de forma elelástica e independente o throughput e o armazenamento em qualquer número de regiões do Azure em todo o mundo. Você pode aproveitar o acesso rápido de dados de um dígito de um dígito usando suas superfícies de API favoritas. Essas superfícies incluem SQL, MongoDB, Cassandra, Tables ou Gremlin. O Azure Cosmos DB fornece exclusivamente contratos abrangentes de nível de serviço (SLAs) para rendimento, latência, disponibilidade e consistência.

**Principais características**
- Suporta uma ampla gama de APIs, que inclui a API SQL (Core), API Cassandra, API MongoDB, API Gremlin e API de tabela.
- A distribuição global turnkey replica seus dados onde quer que seus usuários estejam. Seus usuários podem interagir com uma réplica dos dados mais próximos a eles.
- Sem esquema ou gerenciamento de índices porque o mecanismo do banco de dados é totalmente agnóstico.
- Presença regional onipresente porque o Azure Cosmos DB está disponível em todas as regiões do Azure em todo o mundo, o que inclui mais de 54 regiões na nuvem pública.
- Precisamente definido, múltiplas opções de consistência porque o protocolo de replicação multi-master do Azure Cosmos DB é cuidadosamente projetado para oferecer cinco opções de consistência bem definidas. Essas cinco opções são fortes, detestádas, sessão, prefixo consistente e eventual.
- 99,999% de disponibilidade para leituras e gravações.
- Programáticamente (ou através do portal Azure) invoque o failover regional de sua conta Azure Cosmos DB para garantir que sua aplicação seja projetada para resistir a um desastre regional.
- Baixa latência garantida no percentil 99 em todo o mundo.

**Referências**
- [Portal Azure](https://portal.azure.com) 
- [Documentação do BD Cosmos do Azure](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Banco de Dados SQL do Azure
 [O Azure SQL Database](https://azure.microsoft.com/services/sql-database/) é um serviço gerenciado por banco de dados de uso geral. Você pode usá-lo para criar uma camada de armazenamento de dados altamente disponível e de alto desempenho para aplicativos e soluções na nuvem Azure.

**Principais características**
- **Modelos e ferramentas de banco de dados elásticos:** Com um banco de dados elástico, os desenvolvedores podem agrupar recursos entre um grupo de bancos de dados para dimensionamento. Para gerenciar administrativamente esses recursos, você envia um script como um trabalho. Em seguida, o banco de dados SQL executa o script nos bancos de dados.
- **Alto desempenho:** Aplicativos de alto desempenho podem aproveitar a versão mais recente. Ele fornece 25% mais poder de banco de dados premium.
- **Backups, replicação e alta disponibilidade:** A replicação incorporada e um SLA apoiado pela Microsoft no nível do banco de dados fornecem continuidade de aplicativos e proteção contra eventos catastróficos. A replicação geográfica ativa permite configurar failover e restauração de autoatendimento, que fornecem controle total sobre "recuperação oops". A restauração de dados está disponível a partir de backups de dados de até 35 dias.
- **Manutenção quase zero:** O software automático faz parte do serviço. As réplicas incorporadas do sistema ajudam a fornecer proteção de dados inerentes, tempo de atividade do banco de dados e estabilidade do sistema. As réplicas do sistema são automaticamente movidas para novos computadores. Eles estão provisionados na mosca como os antigos falham.
- **Segurança:** O Azure SQL Database oferece um portfólio de recursos de segurança para atender às políticas de conformidade organizacionais ou obrigatórias pelo setor:
    - A auditoria fornece aos desenvolvedores a capacidade de executar tarefas relacionadas à conformidade e obter conhecimento sobre as atividades.
    - Desenvolvedores e TI podem implementar políticas no nível do banco de dados para ajudar a limitar o acesso a dados confidenciais com segurança em nível de linha, mascaramento dinâmico de dados e criptografia de dados transparente para o Banco de Dados SQL do Azure.
    - O Banco de Dados Azure SQL é verificado pelos principais auditores de nuvem como parte do escopo das principais certificações e aprovações de conformidade do Azure, como HIPAA BAA, ISO/IEC 27001:2005, FedRAMP e Cláusulas de Modelo da UE.

**Referências**
- [Portal Azure](https://portal.azure.com) 
- [Documentação do Banco de Dados SQL do Azure](/azure/sql-database/) 
