---
title: Selecionando o tipo de implantação certo - Banco de dados Azure para MySQL
description: Este artigo descreve quais fatores considerar antes de implantar o Azure Database para MySQL como infra-estrutura como serviço (IaaS) ou plataforma como serviço (PaaS).
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 50bcd42189b1bcc945d726277975892f07f1baa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255544"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Escolha a opção MySQL Server certa no Azure

Com o Azure, suas cargas de trabalho do servidor MySQL podem ser executadas em uma infra-estrutura de máquina virtual hospedada como um serviço (IaaS) ou como uma plataforma hospedada como um serviço (PaaS). O PaaS tem várias opções de implantação e há níveis de serviço dentro de cada opção de implantação. Quando você escolhe entre IaaS e PaaS, você deve decidir se deseja gerenciar seu banco de dados, aplicar patches e fazer backups ou se deseja delegar essas operações ao Azure.

Ao tomar sua decisão, considere as duas opções a seguir:

- **Banco de dados Azure para MySQL**. Esta opção é um mecanismo de banco de dados MySQL totalmente gerenciado com base na versão estável da edição comunitária MySQL. Esse banco de dados relacional como serviço (DBaaS), hospedado na plataforma de nuvem Dozure, se enquadra na categoria do setor de PaaS.

  Com uma instância gerenciada do MySQL no Azure, você pode usar recursos incorporados que, de outra forma, requerem uma configuração extensiva quando o MySQL Server está no local ou em um VM Do Azure.

  Ao usar o MySQL como um serviço, você paga à medida que vai com opções para escalar ou escalar para maior controle sem interrupção. E ao contrário do MySQL Server autônomo, o Azure Database for MySQL tem recursos adicionais, como alta disponibilidade, inteligência e gerenciamento incorporados.

- **MySQL em VMs Azure**. Esta opção se enquadra na categoria industrial de IaaS. Com este serviço, você pode executar o MySQL Server dentro de uma máquina virtual totalmente gerenciada na plataforma de nuvem do Azure. Todas as versões e edições recentes do MySQL podem ser instaladas em uma máquina virtual IaaS.

  Na diferença mais significativa do Banco de Dados Azure para MySQL, o MySQL em VMs Azure oferece controle sobre o mecanismo de banco de dados. No entanto, esse controle vem ao custo da responsabilidade de gerenciar as VMs e muitas tarefas de administração de banco de dados (DBA). Essas tarefas incluem manutenção e patches de servidores de banco de dados, recuperação de banco de dados e design de alta disponibilidade.

As diferenças principais entre essas opções estão listadas na tabela a seguir:

|            | Banco de Dados do Azure para MySQL | MySQL em VMs Azure    |
|:-------------------|:-----------------------------|:--------------------|
| Contrato de nível de serviço (SLA)                | Oferece SLA de 99,99% de disponibilidade| Até 99,95% de disponibilidade com duas ou mais instâncias no mesmo conjunto de disponibilidade.<br/><br/>99,9% de disponibilidade com uma Única VM de exemplo usando armazenamento premium.<br/><br/>99,99% usando Zonas de disponibilidade com várias instâncias em vários conjuntos de disponibilidade.<br/><br/>Consulte o [SLA das Máquinas Virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Patching do sistema operacional        | Automático  | Gerenciado por clientes |
| Patches MySQL     | Automático  | Gerenciado por clientes |
| Alta disponibilidade | O modelo de alta disponibilidade (HA) baseia-se em mecanismos de failover incorporados para quando ocorre uma interrupção no nível do nó. Nesses casos, o serviço cria automaticamente uma nova instância e anexa o armazenamento a essa instância. | Os clientes arquiteto, implementam, testam e mantêm alta disponibilidade. Os recursos podem incluir clustering, replicação etc.|
| Redundância de zona | Não há suporte no momento | As VMs do Azure podem ser configuradas para serem executadas em diferentes zonas de disponibilidade. Para uma solução local, os clientes devem criar, gerenciar e manter seu próprio data center secundário.|
| Cenários híbridos | Com [a replicação de dados,](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)você pode sincronizar dados de um servidor MySQL externo no banco de dados Azure para o serviço MySQL. O servidor externo pode ser local, em máquinas virtuais ou um serviço de banco de dados hospedado por outros provedores de nuvem.<br/><br/> Com o recurso [de réplica de leitura,](https://docs.microsoft.com/azure/mysql/concepts-read-replicas) você pode replicar dados de um banco de dados Azure para servidor mestre MySQL para até cinco servidores de réplica somente leitura. As réplicas estão dentro da mesma região do Azure ou em todas as regiões. As réplicas somente de leitura são atualizadas de forma assíncrona usando a tecnologia de replicação binlog.| Gerenciado por clientes
| Backup e restauração | Cria automaticamente [backups de servidor](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) e os armazena em armazenamento configurado pelo usuário que é localmente redundante ou geo-redundante. O serviço leva backups completos, diferenciais e de registro de transações | Gerenciado por clientes |
| Monitoramento de operações de banco de dados | Oferece aos clientes a capacidade de [definir alertas](https://docs.microsoft.com/azure/mysql/concepts-monitoring) sobre a operação do banco de dados e agir ao atingir limites. | Gerenciado por clientes |
| Proteção Avançada contra Ameaças | Fornece [proteção avançada contra ameaças.](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal) Essa proteção detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. | Os clientes devem construir essa proteção para si mesmos.
| Recuperação de desastre | Armazena backups automatizados em [armazenamento localmente redundante ou geo-redundante](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal)configurado pelo usuário. Os backups também podem restaurar um servidor a um ponto no tempo. O período de retenção é de 7 a 35 dias. A restauração é realizada usando o portal Azure. | Totalmente gerenciado pelos clientes. As responsabilidades incluem, mas não se limitam a agendamento, teste, arquivamento, armazenamento e retenção. Uma opção adicional é usar um cofre do Azure Recovery Services para fazer backup de VMs e bancos de dados do Azure em VMs. Esta opção está em pré-visualização. |
| Recomendações do desempenho | Fornece aos [clientes recomendações de desempenho baseadas](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) em arquivos de registro de uso gerados pelo sistema. As recomendações ajudam a otimizar as cargas de trabalho. | Gerenciado por clientes |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivações empresariais para a escolha de PaaS ou IaaS

Existem vários fatores que podem influenciar sua decisão de escolher PaaS ou IaaS para hospedar seus bancos de dados MySQL.

### <a name="cost"></a>Custo

O financiamento limitado é muitas vezes a principal consideração que determina a melhor solução para hospedar seus bancos de dados. Isso é verdade se você é uma startup com pouco dinheiro ou uma equipe em uma empresa estabelecida que opera restrições orçamentárias apertadas. Esta seção descreve o básico de faturamento e licenciamento no Azure à medida que eles se aplicam ao Banco de Dados Azure para MySQL e MySQL em VMs Azure.

#### <a name="billing"></a>Cobrança

O Banco de Dados Azure para MySQL está atualmente disponível como um serviço em vários níveis com diferentes preços para recursos. Todos os recursos são cobrados por hora a uma taxa fixa. Para obter as informações mais recentes sobre os níveis de serviço suportados atualmente, tamanhos de computação e valores de armazenamento, consulte o [modelo de compra baseado em vCore](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Você pode ajustar dinamicamente os níveis de serviço e os tamanhos de computação para atender às necessidades variadas de throughput do seu aplicativo. Você é cobrado pelo tráfego de saída da Internet a [taxas regulares de transferência de dados.](https://azure.microsoft.com/pricing/details/data-transfers/)

Com o Azure Database for MySQL, a Microsoft configura, patches e atualiza automaticamente o software do banco de dados. Essas ações automatizadas reduzem seus custos de administração. Além disso, o Banco de Dados Azure para MySQL possui recursos [de backup incorporados.](https://docs.microsoft.com/azure/mysql/concepts-backup) Esses recursos ajudam você a obter uma economia significativa de custos, especialmente quando você tem um grande número de bancos de dados. Em contraste, com MySQL em VMs Azure você pode escolher e executar qualquer versão MySQL. Não importa qual versão MySQL você usa, você paga pela VM provisionada e pelos custos do tipo de licença MySQL específico usado.

O Banco de Dados Azure para MySQL fornece alta disponibilidade incorporada para qualquer tipo de interrupção no nível de nó, mantendo a garantia de 99,99% de SLA para o serviço. No entanto, para a alta disponibilidade do banco de dados dentro das VMs, os clientes devem usar as opções de alta disponibilidade, como [a replicação MySQL,](https://dev.mysql.com/doc/refman/8.0/en/replication.html) que estão disponíveis em um banco de dados MySQL. O uso de uma opção de alta disponibilidade suportada não fornece um SLA adicional. Mas permite que você alcance mais de 99,99% de disponibilidade de banco de dados a custo adicional e despesas administrativas.

Para obter mais informações sobre preços, consulte os seguintes artigos:
* [Banco de dados Azure para preços MySQL](https://azure.microsoft.com/pricing/details/mysql/)
* [Preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Calculadora de preços azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administração

Para muitas empresas, a decisão de fazer a transição para um serviço em nuvem é tanto sobre descarregar a complexidade da administração quanto sobre o custo. Com IaaS e PaaS, Microsoft:

- Administra a infra-estrutura subjacente.
- Replica automaticamente todos os dados para fornecer recuperação de desastres.
- Configura e atualiza o software do banco de dados.
- Gerencia o balanceamento de carga.
- Falha transparente se houver falha no servidor.

A lista a seguir descreve considerações administrativas para cada opção:

* Com o Banco de Dados Azure para MySQL, você pode continuar a administrar seu banco de dados. Mas você não precisa mais gerenciar o mecanismo do banco de dados, o sistema operacional ou o hardware. Exemplos de itens que você pode continuar a administrar incluem:

  - Bancos de dados
  - Conexão
  - Ajuste de índice
  - Ajuste de consulta
  - Auditoria
  - Segurança

  Além disso, configurar alta disponibilidade para outro data center requer mínimo ou nenhuma configuração ou administração.

* Com mySQL em VMs Azure, você tem controle total sobre o sistema operacional e a configuração de instância do servidor MySQL. Com uma VM, você decide quando atualizar ou atualizar o sistema operacional e o software de banco de dados. Você também decide quando instalar qualquer software adicional, como um aplicativo antivírus. Alguns recursos automatizados são fornecidos para simplificar muito o patching, o backup e a alta disponibilidade. Você pode controlar o tamanho da VM, o número de discos e suas configurações de armazenamento. Para obter mais informações, consulte [os tamanhos de serviço virtual e de nuvem para o Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="time-to-move-to-azure"></a>Tempo para mover para o Azure

* O Azure Database for MySQL é a solução certa para aplicativos projetados em nuvem quando a produtividade do desenvolvedor e o rápido tempo de comercialização de novas soluções são críticos. Com funcionalidade programática que é como o DBA, o serviço é adequado para arquitetos e desenvolvedores em nuvem, pois reduz a necessidade de gerenciar o sistema operacional e o banco de dados subjacentes.

* Quando você quer evitar o tempo e a despesa de adquirir novos hardwares no local, o MySQL no Azure VMs é a solução certa para aplicativos que requerem um banco de dados MySQL ou acesso a recursos MySQL no Windows ou Linux. Esta solução também é adequada para migrar aplicativos e bancos de dados existentes no local para o Azure intactos, para casos em que o Azure Database for MySQL é um ajuste ruim.

  Como não há necessidade de alterar as camadas de apresentação, aplicativo e dados, você economiza tempo e orçamento para reprojetar sua solução existente. Em vez disso, você pode se concentrar em migrar todas as suas soluções para o Azure e abordar algumas otimizações de desempenho que a plataforma Azure pode exigir.

## <a name="next-steps"></a>Próximas etapas

* Consulte [o Banco de Dados Do Azure para preços mySQL](https://azure.microsoft.com/pricing/details/MySQL/).
* Comece com a [criação do seu primeiro servidor](https://docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal).
