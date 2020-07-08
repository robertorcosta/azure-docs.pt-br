---
title: O que é o SQL Azure?
titleSuffix: " "
description: 'Saiba mais sobre as diferentes opções na família de serviços do Azure SQL: banco de dados SQL do Azure, Azure SQL Instância Gerenciada e SQL Server na VM do Azure.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server nuvem, SQL Server na nuvem, banco de dados PaaS, nuvem SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/27/2020
ms.openlocfilehash: 7a4f26b11911caaa7bce40b77570331b492ceb9d
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86080169"
---
# <a name="what-is-azure-sql"></a>O que é o SQL Azure? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

O SQL do Azure é uma família de produtos gerenciados, seguros e inteligentes que usam o mecanismo de banco de dados SQL Server na nuvem do Azure.

- **Banco de dados SQL do Azure**: suporte a aplicativos de nuvem modernos em um serviço de banco de dados inteligente e gerenciado, que inclui computação sem servidor. 
- **Instância gerenciada do SQL do Azure**: modernizar seus aplicativos SQL Server existentes em escala com uma instância inteligente totalmente gerenciada como um serviço, com quase 100% de paridade de recursos com o mecanismo de banco de dados SQL Server. Melhor para a maioria das migrações para a nuvem.
- **SQL Server em VMs do Azure**: Tire e SHIFTe suas cargas de trabalho de SQL Server com facilidade e manutenção de 100% SQL Server de compatibilidade e acesso no nível do sistema operacional. 
 
O Azure SQL é criado sobre o conhecido mecanismo de SQL Server, para que você possa migrar aplicativos com facilidade e continuar a usar as ferramentas, os idiomas e os recursos com os quais você está familiarizado. Suas habilidades e sua experiência são transferidas para a nuvem, para que você possa fazer ainda mais com o que já tem. 

Saiba como cada produto se encaixa na plataforma de dados SQL do Microsoft Azure para corresponder à opção certa para seus requisitos de negócios. Se você priorizar a economia de custos ou a administração mínima, este artigo poderá ajudá-lo a decidir qual abordagem é proporcionada aos requisitos de negócios mais importantes para você.


## <a name="overview"></a>Visão geral

No mundo atual orientado a dados, a condução da transformação digital depende cada vez da nossa capacidade de gerenciar grandes quantidades de dados e de aproveitar seu potencial. Mas os imóveis de dados de hoje são cada vez mais complexos, com dados hospedados localmente, na nuvem ou na borda da rede. Os desenvolvedores que estão criando aplicativos inteligentes e de imersão podem se encontrar restritos por limitações que podem, por fim, afetar sua experiência. Limitações decorrentes de plataformas incompatíveis, segurança de dados inadequada, recursos insuficientes e barreiras de desempenho de preço criam complexidade que podem inibir a modernização e o desenvolvimento do aplicativo. 

Uma das primeiras coisas a compreender em qualquer discussão do Azure versus bancos de dados local do SQL Server é que você pode usá-lo. A plataforma de dados da Microsoft aproveita a tecnologia SQL Server e a disponibiliza em máquinas locais físicas, ambientes de nuvem privada, ambientes de nuvem privada hospedados de terceiros e na nuvem pública. 


### <a name="fully-managed-and-always-up-to-date"></a>Totalmente gerenciado e sempre atualizado 

Gaste mais tempo inovação e menos tempo aplicando patches, atualizando e fazendo backup de seus bancos de dados. O Azure é a única nuvem com SQL ativo que aplica automaticamente as atualizações e patches mais recentes para que seus bancos de dados estejam sempre atualizados — eliminando os problemas de fim de suporte. Até mesmo tarefas complexas como ajuste de desempenho, alta disponibilidade, recuperação de desastre e backups são automatizadas, liberando você para se concentrar em aplicativos.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Proteja seus dados com segurança inteligente interna 

O Azure monitora constantemente seus dados em busca de ameaças. Com o Azure SQL, você pode:

- Corrija ameaças em potencial em tempo real com [detecção avançada de ameaças](https://docs.microsoft.com/azure/security/fundamentals/threat-detection#advanced-threat-detection-features-other-azure-services) inteligentes e alertas proativos de avaliação de vulnerabilidades. 
- Obtenha proteção de várias camadas e líder do setor com [controles de segurança internos](https://azure.microsoft.com/overview/security/) , incluindo T-SQL, autenticação, rede e gerenciamento de chaves. 
- Aproveite a cobertura de [conformidade](https://azure.microsoft.com/overview/trusted-cloud/compliance/) mais abrangente de qualquer serviço de banco de dados de nuvem. 


### <a name="business-motivations"></a>Motivações de negócios

Há vários fatores que podem influenciar sua decisão de escolher entre as diferentes ofertas de dados:

- [Custo](#cost): a opção PaaS e IaaS incluem o preço base que cobre a infraestrutura subjacente e o licenciamento. No entanto, com a opção IaaS, você precisa investir tempo e recursos adicionais para gerenciar seu banco de dados, enquanto, em PaaS, você obtém esses recursos de administração incluídos no preço. O IaaS permite que você desligue os recursos enquanto não os estiver usando para diminuir o custo, enquanto a PaaS sempre está em execução, a menos que você remova e recrie seus recursos quando eles forem necessários.
- [Administração](#administration): as opções de PaaS reduzem a quantidade de tempo que você precisa investir para administrar o banco de dados. No entanto, ele também limita o intervalo de tarefas e scripts de administração personalizados que você pode executar ou executar. Por exemplo, o CLR não tem suporte com o banco de dados SQL, mas tem suporte para uma instância do SQL Instância Gerenciada. Além disso, nenhuma opção de implantação no PaaS dá suporte ao uso de sinalizadores de rastreamento.
- [Contrato de nível de serviço](#service-level-agreement-sla): tanto IaaS quanto PaaS fornecem SLA de alto, padrão do setor. A opção PaaS garante SLA de 99,99%, enquanto IaaS, garante SLA de 99,95% para a infraestrutura, o que significa que você precisa implementar mecanismos adicionais para garantir a disponibilidade de seus bancos de dados. Você pode obter o SLA de 99,99% criando uma máquina virtual do SQL adicional e implementando a solução de alta disponibilidade do grupo de disponibilidade SQL Server Always On. 
- [Tempo para mover para o Azure](#market): SQL Server na VM do Azure é a correspondência exata do seu ambiente, portanto, a migração do local para a VM do Azure não é diferente de mover os bancos de dados de um servidor local para outro. O SQL Instância Gerenciada também permite a fácil migração; no entanto, pode haver algumas alterações que você precisa aplicar antes da migração. 


## <a name="service-comparison"></a>Comparação de serviço

   ![Opções de SQL Server de nuvem: SQL Server no banco de dados SQL de IaaS ou SaaS na nuvem.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Como visto no diagrama, cada oferta de serviço pode ser caracterizada pelo nível de administração que você tem sobre a infraestrutura e pelo grau de eficiência de custo.

No Azure, você pode ter suas cargas de trabalho de SQL Server em execução como um serviço hospedado ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) ou uma infraestrutura hospedada ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)). No PaaS, você tem várias opções de produto e camadas de serviço em cada opção. A principal pergunta que você precisa fazer ao decidir entre PaaS ou IaaS é desejar gerenciar seu banco de dados, aplicar patches e fazer backups ou deseja delegar essas operações ao Azure?

### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

O [banco de dados SQL do Azure](database/sql-database-paas-overview.md) é um DBaaS (banco de dados como serviço) relacional hospedado no Azure que se enquadra na categoria do setor de *PaaS (plataforma como serviço)*. 
- Melhor para aplicativos de nuvem modernos que desejam usar os recursos de SQL Server estável mais recentes e ter restrições de tempo no desenvolvimento e no marketing. 
- Um mecanismo de banco de dados SQL Server totalmente gerenciado, com base na mais recente edição de empresa estável do SQL Server. O banco de dados SQL tem duas opções de implantação baseadas em hardware padronizado e software que é propriedade, hospedada e mantida pela Microsoft. 

Com o SQL Server, você pode usar recursos internos e funcionalidade que requer uma configuração extensiva (local ou em uma máquina virtual do Azure). Quando você usa o Banco de Dados SQL, ele é pré-pago, com opções para escalar verticalmente ou horizontalmente, de modo a ter maior capacidade, sem interrupção. O banco de dados SQL tem alguns recursos adicionais que não estão disponíveis no SQL Server, como alta disponibilidade, inteligência e gerenciamento internos.


O Banco de Dados SQL do Azure oferece as seguintes opções de implantação:
  - Como um [*banco de dados individual*](database/single-database-overview.md) com seu próprio conjunto de recursos gerenciados por meio de um [SQL Server lógico](database/logical-servers.md). Um banco de dados individual é semelhante a um [banco de dados independente](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) no SQL Server. Essa opção é otimizada para o desenvolvimento moderno de aplicativo de novos aplicativos de nuvem. As opções de [hiperescala](database/service-tier-hyperscale.md) e sem [servidor](database/serverless-tier-overview.md) estão disponíveis.
  - Um [*pool elástico*](database/elastic-pool-overview.md), que é uma coleção de bancos de dados com um conjunto compartilhado de recursos gerenciados por meio de um [SQL Server lógico](database/logical-servers.md). Bancos de dados individuais podem ser movidos para dentro e para fora de um pool elástico. Essa opção é otimizada para o desenvolvimento de aplicativos modernos de novos aplicativos gerados pela nuvem usando o padrão de aplicativo SaaS multilocatário. Os pools elásticos fornecem uma solução econômica para gerenciar o desempenho de vários bancos de dados que têm padrões de uso variáveis.

### <a name="azure-sql-managed-instance"></a>Instância Gerenciada do Azure SQL

O [Azure SQL instância gerenciada](managed-instance/sql-managed-instance-paas-overview.md) se enquadra na categoria do setor de *PaaS (plataforma como serviço)* e é melhor para a maioria das migrações para a nuvem. O SQL Instância Gerenciada é uma coleção de bancos de dados do sistema e do usuário com um conjunto compartilhado de recursos que estão prontos para serem retirados e deslocados.  
- Melhor para novos aplicativos ou aplicativos locais existentes que desejam usar os mais recentes recursos de SQL Server estável e que são migrados para a nuvem com alterações mínimas. Uma instância do SQL Instância Gerenciada é semelhante a uma instância do [mecanismo de banco de dados do Microsoft SQL Server](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview) que oferece recursos compartilhados para bancos e recursos de escopo de instância adicionais. 
- O SQL Instância Gerenciada dá suporte à migração de banco de dados local com mínimo de sem alteração de banco de dados. Essa opção fornece todos os benefícios de PaaS do banco de dados SQL do Azure, mas adiciona recursos que anteriormente só estavam disponíveis em VMs SQL Server. Isso inclui uma rede virtual nativa e quase 100% de compatibilidade com a SQL Server local. As instâncias do SQL Instância Gerenciada fornecem acesso completo SQL Server e compatibilidade de recursos para migrar SQL Servers para o Azure.

### <a name="sql-server-on-azure-vm"></a>SQL Server na VM do Azure

[SQL Server na VM do Azure](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) se enquadra na categoria de *Infraestrutura como serviço (IaaS)* do setor e permite que você execute SQL Server dentro de uma VM (máquina virtual) totalmente gerenciada no Azure. 
- Melhor para migrações e aplicativos que exigem acesso no nível do sistema operacional. As máquinas virtuais do SQL no Azure são prontas para serem comparadas com o recurso de mudança e deslocamento para aplicativos existentes que exigem migração rápida para a nuvem com alterações mínimas ou nenhuma alteração. As máquinas virtuais do SQL oferecem controle administrativo total sobre a instância de SQL Server e o sistema operacional subjacente para migração para o Azure. 
- Cenários de rápido desenvolvimento e teste quando você não deseja comprar hardware do SQL Server local de não produção. As máquinas virtuais do SQL também são executadas em hardware padronizado que é propriedade, hospedada e mantida pela Microsoft. Ao usar máquinas virtuais do SQL, você pode fazer o pré-pago para uma licença de SQL Server já incluída em uma imagem de SQL Server ou usar facilmente uma licença existente. Você também pode interromper ou retomar a VM conforme o necessário. 
- SQL Server instalado e hospedado na nuvem é executado em máquinas virtuais do Windows Server ou Linux em execução no Azure, também conhecido como IaaS (infraestrutura como serviço). As máquinas virtuais do SQL são uma boa opção para migrar bancos de dados de SQL Server locais e aplicativos sem qualquer alteração no banco. Todas as versões e edições recentes do SQL Server estão disponíveis para instalação em uma máquina virtual de IaaS. 

    A diferença mais significativa do banco de dados SQL e do SQL Instância Gerenciada é que SQL Server em máquinas virtuais do Azure permite o controle total sobre o mecanismo de banco de dados. Você pode escolher quando iniciar a manutenção/aplicação de patches, alterar o modelo de recuperação para simples ou bulk-logged, pausar ou iniciar o serviço quando necessário, e você pode personalizar totalmente o mecanismo de banco de dados SQL Server. Com esse controle adicional, a responsabilidade adicional é o gerenciamento da máquina virtual.
- Otimizado para migrar aplicativos existentes para o Azure ou estendendo aplicativos locais existentes para a nuvem em implantações híbridas. Além disso, você pode usar o SQL Server em uma máquina virtual para desenvolver e testar os aplicativos tradicionais do SQL Server. Com as máquinas virtuais do SQL, você tem os direitos administrativos completos em uma instância de SQL Server dedicada e em uma VM baseada em nuvem. É uma opção perfeita quando uma organização já tem recursos de TI disponíveis para manter as máquinas virtuais. Esses recursos permitem que você crie um sistema altamente personalizado para endereçar os requisitos específicos de desempenho e disponibilidade de seu aplicativo.

Diferenças adicionais são listadas na tabela a seguir, mas o *banco de dados SQL e o sql instância gerenciada são otimizados para reduzir os custos gerais de gerenciamento para o mínimo para provisionamento e gerenciamento de muitos bancos de dados.* Os custos de administração contínuas são reduzidos, pois você não precisa gerenciar máquinas virtuais, sistemas operacionais ou software de banco de dados. Você não precisa gerenciar as atualizações, a alta disponibilidade ou os [backups](database/automated-backups-overview.md). 

Em geral, o banco de dados SQL e o SQL Instância Gerenciada podem aumentar drasticamente o número de bancos de dados gerenciados por um único recurso de ti ou de desenvolvimento. Os [Pools elásticos](database/elastic-pool-overview.md) também dão suporte a arquiteturas de aplicativo multilocatário de SaaS com recursos, como isolamento de locatários e a capacidade de dimensionar para reduzir os custos por meio do compartilhamento de recursos entre os bancos de dados. O [SQL instância gerenciada](managed-instance/sql-managed-instance-paas-overview.md) fornece suporte para recursos com escopo de instância, permitindo a fácil migração de aplicativos existentes, bem como o compartilhamento de recursos entre bancos de dados.

### <a name="comparison-table"></a>Tabela de comparação

| Banco de Dados SQL do Azure | Instância Gerenciada do Azure SQL | SQL Server na VM do Azure |
| :--- | :--- | :--- |
|É compatível com a maioria dos recursos locais no nível do banco de dados. Recursos do SQL Server mais comumente usados estão disponíveis.<br/>Disponibilidade garantida de 99,995%.<br/>Backups internos, aplicação de patch, recuperação.<br/>Última versão estável do Mecanismo de Banco de Dados do Microsoft SQL Server.<br/>Capacidade de atribuir recursos necessários (CPU/armazenamento) para bancos de dados individuais.<br/>Inteligência avançada interna e segurança.<br/>Alteração online de recursos (CPU/armazenamento).| É compatível com quase todos os recursos locais no nível da instância e no nível do banco de dados. Alta compatibilidade com o SQL Server.<br/>99,99% de disponibilidade garantida.<br/>Backups internos, aplicação de patch, recuperação.<br/>Última versão estável do Mecanismo de Banco de Dados do Microsoft SQL Server.<br/>Fácil migração do SQL Server local.<br/>Endereço IP privado na rede virtual do Azure.<br/>Inteligência avançada interna e segurança.<br/>Alteração online de recursos (CPU/armazenamento).| Você tem controle total sobre o mecanismo do SQL Server. É compatível com todos os recursos locais.<br/>Até 99,99% de disponibilidade.<br/>Paridade completa com a versão correspondente do SQL Server no local.<br/>Versão de Mecanismo de Banco de Dados fixa e conhecida.<br/>Fácil migração do SQL Server local.<br/>Endereço IP privado na rede virtual do Azure.<br/>Você tem a capacidade de implantar aplicativos ou serviços no host onde SQL Server é colocado.|
|A migração de SQL Server pode ser desafiadora.<br/>Alguns recursos do SQL Server não estão disponíveis.<br/>Não há tempo de manutenção exato garantido (mas quase transparente).<br/>A compatibilidade com a versão do SQL Server pode ser obtida usando apenas os níveis de compatibilidade do banco de dados.<br/>Não é possível atribuir o endereço IP privado (você pode limitar o acesso usando regras de firewall).|Ainda há um número mínimo de recursos do SQL Server que não estão disponíveis.<br/>Não há tempo de manutenção exato garantido (mas quase transparente).<br/>A compatibilidade com a versão do SQL Server pode ser obtida usando apenas os níveis de compatibilidade do banco de dados.|Você precisa gerenciar seus backups e patches.<br>Você precisa implementar sua própria solução de alta disponibilidade.<br/>Há um tempo de inatividade durante a alteração de recursos (CPU/armazenamento)|
| Bancos de dados de até 100 TB. | Até 8 TB. | SQL Server instâncias com até 256 TB de armazenamento. A instância pode suportar quantos bancos de dados forem necessários. |
| O aplicativo local pode acessar os dados no Azure SQL Database. | [Implementação de rede virtual nativa](managed-instance/vnet-existing-add-subnet.md) e conectividade com seu ambiente local usando o gateway de VPN ou a rota expressa do Azure. | Com as máquinas virtuais do SQL, você pode ter aplicativos que são executados parcialmente na nuvem e parcialmente locais. Por exemplo, você pode estender sua rede local e o Domínio do Active Directory para a nuvem por meio da [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md). Para obter mais informações sobre soluções de nuvem híbrida, veja [Estendendo soluções de dados locais para a nuvem](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |


## <a name="cost"></a>Custo

Quer você seja uma inicialização que seja strapped por dinheiro ou uma equipe em uma empresa estabelecida que opere sob restrições de orçamento rígidas, o financiamento limitado geralmente é o principal driver ao decidir como hospedar seus bancos de dados. Nesta seção, você aprenderá sobre as noções básicas de cobrança e licenciamento no Azure associadas à família de serviços do Azure SQL.  Você também aprenderá sobre como calcular o custo total do aplicativo.

### <a name="billing-and-licensing-basics"></a>Noções básicas de licenciamento e cobrança

Atualmente, o **banco de dados SQL** e o **SQL instância gerenciada** são vendidos como um serviço e estão disponíveis com várias opções e em várias camadas de serviço com preços diferentes para recursos, e todos são cobrados por hora a uma taxa fixa com base na camada de serviço e no tamanho de computação que você escolher. Para obter as informações mais recentes sobre as atuais camadas de serviço com suporte, tamanhos de computação e valores de armazenamento, consulte [modelo de compra baseado em DTU para banco de dados SQL](database/service-tiers-dtu.md) e [modelo de compra baseado em VCORE para o banco de dados SQL e o SQL instância gerenciada](database/service-tiers-vcore.md).

- Com o banco de dados SQL, você pode escolher uma camada de serviço que atenda às suas necessidades de uma ampla gama de preços a partir de 5 $/mês para a camada básica, e você pode criar [pools elásticos](database/elastic-pool-overview.md) para compartilhar recursos entre bancos de dados para reduzir custos e acomodar picos de uso.
- Com o SQL Instância Gerenciada, você também pode trazer sua própria licença. Para obter mais informações sobre o seu próprio licenciamento, consulte [mobilidade de licenças via Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/) ou use a [calculadora de benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) para ver como **economizar até 40%**.

Além disso, você será cobrado pelo tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/)regulares. Você ajustar as camadas de serviço e os tamanhos da computação dinamicamente para atender à variação das necessidades de produtividade do aplicativo.

Com o **banco de dados SQL** e o **SQL instância gerenciada**, o software de banco de dados é automaticamente configurado, corrigido e atualizado pelo Azure, o que reduz os custos de administração. Além disso, seus recursos de [backup interno](database/automated-backups-overview.md) o ajudam a obter economia significativa, principalmente quando você tem um grande número de bancos de dados.

Com o **SQL em VMs do Azure**, você pode usar qualquer uma das imagens de SQL Server fornecidas pela plataforma (que inclui uma licença) ou trazer sua licença de SQL Server. Todas as versões de SQL Server com suporte (2008R2, 2012, 2014, 2016, 2017, 2019) e edições (Developer, Express, Web, Standard, Enterprise) estão disponíveis. Além disso, as versões BYOL (Traga sua Própria Licença) das imagens estão disponíveis. Ao usar as imagens fornecidas pelo Azure, os custos operacionais dependem do tamanho da VM e da edição do SQL Server escolhida. Independentemente do tamanho da VM ou da edição do SQL Server, você paga o custo de licenciamento por minuto do SQL Server e do Linux ou do Windows Server, juntamente com o custo de Armazenamento do Azure para os discos de VM. A opção de cobrança por minuto permite que você use o SQL Server pelo tempo necessário, sem adquirir licenças adicionais do SQL Server. Se você usar sua própria licença do SQL Server no Azure, serão cobrados apenas os custos de armazenamento e de servidor. Para obter mais informações sobre como utilizar seu próprio licenciamento, consulte [Mobilidade de Licenças por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Além disso, você será cobrado pelo tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/)regulares.

#### <a name="calculating-the-total-application-cost"></a>Calculando o custo total do aplicativo

Quando você começar a usar uma plataforma de nuvem, o custo de execução do aplicativo incluirá os custos do novo desenvolvimento e da administração contínua, mais os custos de serviço da plataforma de nuvem pública.

Para obter mais informações sobre preços, consulte os recursos a seguir:

- [Preço do banco de dados SQL & SQL Instância Gerenciada](https://azure.microsoft.com/pricing/details/sql-database/)
- [Preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) para [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Administração

Para muitas empresas, a decisão de fazer a transição para um serviço de nuvem se refere à redução da complexidade de administração, além de seu custo. Com IaaS e PaaS, o Azure administra a infraestrutura subjacente e replica automaticamente todos os dados para fornecer recuperação de desastre, configura e atualiza o software de banco de dado, gerencia o balanceamento de carga e faz failover transparente se houver uma falha de servidor dentro de um data center.

- Com o **banco de dados SQL** e o **SQL instância gerenciada**, você pode continuar administrando seu banco de dados, mas não precisa mais gerenciar o mecanismo de banco de dados, o sistema operacional ou o hardware. Alguns exemplos de itens que você pode continuar a administrar são bancos de dados e logons, ajuste de índice e consulta e auditoria e segurança. Além disso, a configuração da alta disponibilidade para outro data center requer o mínimo de administração e configuração.
- Com o **SQL na VM do Azure**, você tem controle total sobre o sistema operacional e a configuração de SQL Server instância. Com uma VM, cabe a você decidir quando atualizar/atualizar o sistema operacional e o software de banco de dados e quando instalar qualquer software adicional, como antivírus. Alguns recursos automatizados são fornecidos para simplificar muito a aplicação de patches, backup e alta disponibilidade. Além disso, você pode controlar o tamanho da VM, o número de discos e as configurações de armazenamento desses discos. O Azure permite mudar o tamanho de uma VM quando necessário. Para obter informações, consulte [Tamanhos de Máquinas Virtuais e Serviço de Nuvem do Azure](../virtual-machines/windows/sizes.md).

## <a name="service-level-agreement-sla"></a>SLA (contrato de nível de serviço)

Para muitos departamentos de ti, atender às obrigações de tempo de manutenção de um SLA (contrato de nível de serviço) é uma prioridade principal. Nesta seção, vamos examinar qual SLA se aplica a cada opção de hospedagem de banco de dados.

Para o **banco de dados SQL do Azure** e o **Azure SQL instância gerenciada**, a Microsoft fornece um SLA de disponibilidade de 99,99%. Para obter as informações mais recentes, consulte [contrato de nível de serviço](https://azure.microsoft.com/support/legal/sla/sql-database/).

Para o **SQL na VM do Azure**, a Microsoft fornece um SLA de disponibilidade de 99,95% que abrange apenas a máquina virtual. Esse SLA não abrange os processos (como o SQL Server) em execução na VM e exige que você hospede pelo menos duas instâncias de VM em um conjunto de disponibilidade. Para obter as últimas informações, consulte o [SLA de VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Para HA (alta disponibilidade) de banco de dados em VMs, você deve configurar uma das opções de alta disponibilidade com suporte no SQL Server, como [grupos de disponibilidade Always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Usar uma opção de alta disponibilidade com suporte não fornece um SLA adicional, mas permite que você atinja > 99,99% de disponibilidade do banco de dados.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Tempo para mover para o Azure

O **banco de dados SQL do Azure** é a solução certa para aplicativos projetados para a nuvem quando a produtividade do desenvolvedor e o tempo de comercialização rápido para novas soluções são essenciais. Com a funcionalidade de programação como DBA, ele é perfeito para desenvolvedores e arquitetos de nuvem, pois reduz a necessidade de gerenciamento do sistema operacional e do banco de dados subjacentes.

O **Azure SQL instância gerenciada** simplifica muito a migração de aplicativos existentes para o Azure, permitindo que você traga rapidamente os aplicativos de banco de dados migrados para o mercado no Azure.

O **SQL na VM do Azure** será perfeito se seus aplicativos novos ou existentes exigirem grandes bancos de dados ou acesso a todos os recursos no SQL Server ou Windows/Linux, e você quiser evitar o tempo e as despesas de adquirir um novo hardware local. Também é uma boa opção quando você deseja migrar aplicativos locais e bancos de dados existentes para o Azure no estado em que se encontram, em casos em que o banco de dados SQL ou o SQL Instância Gerenciada não é uma boa opção. Como você não precisa alterar as camadas de apresentação, aplicativo e dados, economiza tempo e orçamento na nova arquitetura da solução existente. Em vez disso, você pode se concentrar em migrar todas as soluções para o Azure e fazer algumas otimizações de desempenho que possam ser necessárias para a plataforma Microsoft Azure. Para obter mais informações, veja [Práticas Recomendadas de Desempenho para o SQL Server em Máquinas Virtuais do Azure](virtual-machines/windows/performance-guidelines-best-practices.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Próximas etapas

- Consulte [Seu primeiro Banco de Dados SQL do Azure](database/single-database-create-quickstart.md) para uma introdução ao Banco de Dados SQL.
- Consulte [seu primeiro instância gerenciada de SQL do Azure](managed-instance/instance-create-quickstart.md) para começar a usar o SQL instância gerenciada. 
- Confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Confira [Provisionar uma máquina virtual do SQL Server no Azure](virtual-machines/windows/create-sql-vm-portal.md) para obter uma introdução ao SQL Server em VMs do Azure.
- [Identifique o banco de dados SQL correto ou o SKU do sql instância gerenciada para seu banco de dados local](/sql/dma/dma-sku-recommend-sql-db/).
