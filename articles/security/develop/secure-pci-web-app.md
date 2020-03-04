---
title: Proteger o aplicativo Web para PCI DSS | Microsoft Docs
description: Este aplicativo de exemplo implementa práticas recomendadas de segurança que melhoram seu aplicativo e a postura de segurança de sua organização quando você desenvolve no Azure.
keywords: na
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4fe612db65d985be2f1f1c81d03c3ee735c03889
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992608"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Desenvolver uma infraestrutura segura para um aplicativo PCI

## <a name="overview"></a>Visão geral

Essa infraestrutura segura para um aplicativo de PCI (setor de cartão de pagamento) fornece orientação para a implantação de um ambiente de PaaS (plataforma como serviço) do setor de cartão de pagamento adequado para a coleta, o armazenamento e a recuperação de dados de titulares de cartões. Essa solução automatiza a implantação e a configuração de recursos do Azure para uma arquitetura de referência comum, demonstrando maneiras pelas quais os clientes podem atender aos requisitos específicos de segurança e conformidade, e serve como base para os clientes criarem e Configurarem suas próprias soluções no Azure. A solução implementa um subconjunto de requisitos semelhantes a padrões de segurança de dados do setor de cartão de pagamento (PCI DSS 3,2).

Este exemplo implanta automaticamente uma arquitetura de referência de aplicativo Web de PaaS com controles de segurança pré-configurados para ajudar os clientes a obter conformidade semelhante aos requisitos de PCI DSS 3,2. A solução consiste em modelos do Azure Resource Manager e scripts do PowerShell que guiam a implantação e a configuração dos recursos.

Você deve seguir as etapas descritas neste artigo sequencialmente para garantir que os componentes do aplicativo estejam configurados corretamente. O banco de dados, Azure App serviço, Azure Key Vault instância e Aplicativo Azure instância de gateway dependem uns dos outros.

Os scripts de implantação configuram a infraestrutura. Depois de executar os scripts de implantação, você precisará fazer alguma configuração manual no portal do Azure para vincular os componentes e serviços juntos.

Este exemplo destina-se a desenvolvedores experientes no Azure que trabalham no setor de varejo e desejam criar um aplicativo de varejo com a infraestrutura do Azure segura.

> [!NOTE]
> Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.

A implantação de um aplicativo nesse ambiente sem modificações não é suficiente para atender completamente aos requisitos do PCI DSS 3.2. Observe o seguinte:

- Essa arquitetura fornece uma linha de base para ajudar os clientes a usar o Azure de forma compatível com o PCI DSS 3.2.
- Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, já que os requisitos podem variar com base nas particularidades da implementação de cada cliente.

Ao desenvolver e implantar este aplicativo, você aprende a:

- Crie uma instância do Azure Key Vault e armazene e recupere os segredos dela.
- Implantar o banco de dados do Azure para SQL do Azure, configurar o acesso a eles e autorizar a ti.
- Implante o aplicativo Web do Azure com o ambiente do serviço de aplicativo, que é um dedicado isolado com aEcess de firewall de front-end.
- Crie e configure uma instância de gateway Aplicativo Azure com um firewall que usa o [OWASP 10 principais RuleSet](https://coreruleset.org/).
- Habilite a criptografia de dados em trânsito e em repouso usando os serviços do Azure.
- Configurar a política do Azure e as impressões azuis para avaliar as conformidades

Depois de desenvolver e implantar esse aplicativo, você terá configurado o exemplo de aplicativo Web a seguir junto com as medidas de configuração e segurança descritas.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
O aplicativo é um aplicativo típico de n camadas com três camadas. O front-end, back-end e a camada de banco de dados com componentes de monitoramento e gerenciamento de segredo integrados são mostrados aqui:

![Arquitetura do aplicativo](./media/secure-pci-web-app/architecture.png)

A arquitetura consiste nesses componentes:

- [Ambiente do serviço de aplicativo v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Fornece o Ambiente do Serviço de Aplicativo e o balanceador de carga para nossa arquitetura de aplicativo.
- [Gateway de Aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/). Fornece o gateway e o firewall para nossa arquitetura de aplicativo.
- [Application insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Fornece um serviço de gerenciamento de desempenho de aplicativos (APM) extensível em várias plataformas.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Armazena e criptografa os segredos de nosso aplicativo e gerencia a criação de políticas de acesso em relação a eles.
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Fornece identidade baseada em nuvem e serviço de gerenciamento de acesso, entrada e recursos de acesso.
- [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview). Fornece o serviço para hospedar o domínio.
- [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Dimensiona seus aplicativos e cria alta disponibilidade para seus serviços
- [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Fornece solução para cenários de armazenamento de dados modernos.
- [Aplicativo Web do Azure](https://docs.microsoft.com/azure/app-service/overview/).  Fornece um serviço baseado em HTTP para hospedar aplicativos Web.
- [Banco de dados do Azure para AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Armazena com segurança os dados do aplicativo.
- [Plantas do Azure](https://docs.microsoft.com/azure/governance/blueprints/overview/). Fornece especificações e conformidade com determinados padrões e requisitos.
- [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/)
- [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview). Avalia seus recursos para não conformidade com políticas atribuídas.

## <a name="threat-model"></a>Modelo de ameaça
A modelagem de ameaças é o processo de identificar possíveis ameaças à segurança para seus negócios e aplicativos e, em seguida, garantir que um plano de mitigação adequado esteja em vigor.

Este exemplo usou o [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) para implementar a modelagem de ameaças para o aplicativo de exemplo seguro. Ao diagramar os componentes e os fluxos de dados, você pode identificar problemas e ameaças no início do processo de desenvolvimento. Isso poupa tempo e dinheiro mais tarde.

Este é o modelo de ameaça para o aplicativo de exemplo:

![Modelo de ameaça](./media/secure-pci-web-app/threat-model.png)

Algumas ameaças de exemplo e possíveis vulnerabilidades que a ferramenta de modelagem de ameaças gera são mostradas na captura de tela a seguir. O modelo de ameaça fornece uma visão geral da superfície de ataque exposta e solicita que os desenvolvedores pensem sobre como mitigar os problemas.

![Saída do modelo de ameaça](./media/secure-web-app/threat-model-output.png)

Por exemplo, a injeção de SQL na saída do modelo de ameaça anterior é atenuada com a limpeza de entradas do usuário e o uso de funções armazenadas no banco de dados do Azure para PostgreSQL. Essa mitigação impede a execução arbitrária de consultas durante leituras e gravações de dados.

Os desenvolvedores melhoram a segurança geral do sistema, reduzindo cada uma das ameaças na saída do modelo de ameaça.

## <a name="deployment"></a>Implantação
### <a name="prerequisites"></a>Prerequisites
Para colocar o aplicativo em funcionamento, você precisa instalar essas ferramentas:

- Um editor de código para modificar e exibir o código do aplicativo. [Visual Studio Code](https://code.visualstudio.com/) é uma opção de código-fonte aberto.
- [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) em seu computador de desenvolvimento.
- [Git](https://git-scm.com/) em seu sistema. O Git é usado para clonar o código-fonte localmente.
- [JQ](https://stedolan.github.io/jq/), uma ferramenta UNIX para consultar o JSON de maneira amigável.

Este exemplo é composto por arquivos de configuração JSON e scripts do PowerShell que são manipulados pelo serviço de API do Azure Resource Manager para implantar recursos no Azure. As instruções detalhadas de implantação estão disponíveis [aqui](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM).

#### <a name="quickstart"></a>Início rápido

1.  Clone ou baixe [este](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) repositório GitHub na sua estação de trabalho local.
2.  Examine 0-Setup-AdministrativeAccountAndPermission.md e execute os comandos fornecidos.
3.  Implante uma solução de teste com dados de exemplo da Contoso ou crie o piloto de um ambiente de produção inicial.

    Esse script implanta recursos do Azure para uma demonstração de uma loja da Web usando dados de exemplo da contoso.

Neste exemplo, você executa o script de implantação que implanta o aplicativo Web no serviço de aplicativo e cria os recursos.

Há várias maneiras de implantar aplicativos no Azure, incluindo:

- Modelos do Azure Resource Manager
- PowerShell
- CLI do Azure
- Portal do Azure
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="network"></a>Rede
A arquitetura define uma rede virtual privada com o espaço de endereço 10.200.0.0/16.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Grupos de segurança de rede
Grupos de segurança de https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) rede (que contêm ACLs (listas de controle de acesso) que permitem ou negam o tráfego em uma rede virtual. Os grupos de segurança de rede podem ser usados para proteger o tráfego no nível da sub-rede ou da VM individual. Existem os seguintes grupos de segurança de rede:

- 1 Grupo de segurança de rede do Gateway de Aplicativo
- 1 Grupo de segurança de rede do Ambiente do Serviço de Aplicativo
- 1 Grupo de segurança de rede do Banco de Dados SQL do Azure
- 1 grupo de segurança de rede para host bastião

Cada um dos grupos de segurança de rede tem portas e protocolos específicos abertos para que a solução possa funcionar de forma segura e correta. Além disso, as configurações a seguir são habilitadas para cada grupo de segurança de rede:

- Logs de diagnóstico e eventos https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) (estão habilitados e armazenados em uma conta de armazenamento
- Os logs de Azure Monitor estão conectados ao diagnóstico do grupo de segurança de rede (https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>Configuração do NSG
A configuração do NSG para Ambiente do Serviço de Aplicativo deve ser configurada conforme mostrado na imagem abaixo.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Cada sub-rede está associada ao seu grupo de segurança de rede correspondente.

### <a name="config"></a>Config
As sub-redes são configuradas conforme mostrado na imagem abaixo.
 ![Configuração](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>DNS do Azure
O DNS (sistema de nomes de domínio) é responsável por converter (ou resolver) um site ou nome de serviço para seu endereço IP. O [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de hospedagem para domínios DNS que fornece a resolução de nomes usando a infraestrutura do Azure. Ao hospedar domínios no Azure, os usuários podem gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. O DNS do Azure também dá suporte a domínios DNS privados.

### <a name="protect-data"></a>Proteger dados
Para ajudar a proteger os dados na nuvem, você precisa levar em consideração os possíveis estados em que seus dados podem ocorrer e quais controles estão disponíveis para esse estado. Práticas recomendadas para criptografia e segurança de dados do Azure se relacionam aos seguintes estados de dados:

- Em repouso: Isso inclui todos os objetos de armazenamento, contêineres e tipos de informações que existem estaticamente em mídia física, seja ela magnética ou disco óptico.
- Em trânsito: Quando os dados estão sendo transferidos entre componentes, locais ou programas, estará em trânsito. Os exemplos são a transferência pela rede, através de um barramento de serviço (do local para a nuvem e vice-versa, incluindo conexões híbridas, como o ExpressRoute), ou durante um processo de entrada/saída.

### <a name="azure-storage"></a>Armazenamento do Azure
Para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa Azure Key Vault para manter o controle das chaves que acessam e criptografam os dados. Isso ajuda a proteger os dados do titular do cartão em apoio aos compromissos de segurança organizacional e aos requisitos de conformidade definidos pelo PCI DSS 3.2.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução é integrada ao Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure
A instância do Banco de Dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:

- A [autenticação e a autorização do Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidade dos usuários de banco de dados e de outros serviços da Microsoft em uma única localização central.
- A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) controla os eventos de banco de dados e grava-os em um log de auditoria em uma conta de armazenamento do Azure.
- O Banco de Dados SQL do Azure está configurado para usar a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real do banco de dados, dos backups associados e dos arquivos de log de transações para proteger as informações em repouso. A Transparent Data Encryption oferece a garantia de que os dados armazenados não fiquem sujeitos a acesso não autorizado.
- [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
- A [Detecção de Ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar e responder a possíveis ameaças conforme elas ocorrem, fornecendo alertas de segurança sobre atividades suspeitas no banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões anormais de acesso ao banco de dados.
- As [Colunas Criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca sejam exibidos como texto não criptografado no sistema do banco de dados. Após a habilitação da criptografia de dados, somente aplicativos cliente ou servidores de aplicativo com acesso às chaves poderão acessar dados de texto não criptografado.
- A [Máscara de Dados Dinâmicos do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos não privilegiados. A Máscara de Dados Dinâmicos pode descobrir automaticamente dados potencialmente confidenciais e sugerir as máscaras apropriadas a serem aplicadas. Isso ajuda a identificar e reduzir o acesso aos dados, para que eles não saiam do banco de dados por meio de acesso não autorizado. Os clientes são responsáveis por ajustar as configurações da máscara de dados dinâmicos para seguir seu esquema de banco de dados.

### <a name="identity-management"></a>Gerenciamento de identidades
As tecnologias a seguir oferecem funcionalidades para gerenciar o acesso a dados de titular do cartão no ambiente do Azure:

- Azure Active Directory é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários dessa solução são criados no Azure Active Directory, incluindo os usuários que acessam o Banco de Dados SQL do Azure.
- A autenticação no aplicativo é executada usando o Azure Active Directory. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration). Além disso, a criptografia da coluna do banco de dados utiliza o Azure Active Directory para autenticar o aplicativo no Banco de Dados SQL do Azure. Para obter mais informações, confira [como proteger dados confidenciais no Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- O controle de acesso baseado em função do Azure permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos. Em vez de conceder permissão irrestrita aos recursos do Azure a todos os usuários, os administradores podem permitir que apenas determinadas ações acessem os dados do titular do cartão. O acesso à assinatura é limitado ao administrador da assinatura.
- Azure Active Directory Privileged Identity Management permite que os clientes minimizem o número de usuários que têm acesso a determinadas informações, como dados de titulares de cartão. Os administradores podem usar o Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades privilegiadas e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor o acesso administrativo sob demanda Just-In-Time quando necessário.
- Azure Active Directory Identity Protection detecta possíveis vulnerabilidades que afetam as identidades de uma organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas às identidades de uma organização e investiga suspeitas incidentes para executar a ação apropriada para resolvê-los.

### <a name="secrets-management"></a>Gerenciamento de segredos
A solução usa Azure Key Vault para o gerenciamento de chaves e segredos. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. As seguintes funcionalidades do Azure Key Vault ajudam os clientes a proteger os dados e o acesso a eles:

- Políticas de acesso avançadas são configuradas com base na necessidade.
- As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault têm datas de validade.
- Todas as chaves no Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma Chave RSA de 2048 bits protegida por HSM.
- Com o Key Vault, você pode criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados,. Arquivos PFX e senhas) usando chaves que são protegidas por HSMs (módulos de segurança de hardware)
- Use o RBAC para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo.
- Use Key Vault para gerenciar seus certificados TLS com renovação automática.
- Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
- As operações criptográficas permitidas para chaves são restritas às necessárias.

### <a name="azure-security-center"></a>Central de Segurança do Azure
Com a central de segurança do Azure, os clientes podem aplicar e gerenciar centralmente políticas de segurança entre cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques. Além disso, a Central de Segurança do Azure acessa as configurações existentes de serviços do Azure para fornecer recomendações de serviço e configuração, a fim de ajudar a melhorar a postura de segurança e proteger os dados.

A Central de Segurança do Azure usa uma variedade de funcionalidades de detecção para alertar os clientes de ataques potenciais direcionados a seus ambientes. Esses alertas contêm informações valiosas sobre o que disparou o alerta, os recursos de destino e a origem do ataque. A central de segurança do Azure tem um conjunto de alertas de segurança predefinidos, que são disparados quando ocorre uma ameaça ou atividade suspeita. As regras de alerta personalizadas na central de segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados que já foram coletados de seu ambiente.

A Central de Segurança do Azure fornece alertas de segurança e incidentes priorizados, simplificando a descoberta e a resolução por parte dos clientes de possíveis problemas de segurança. Um relatório de inteligência contra ameaças é gerado para cada ameaça detectada para ajudar as equipes de resposta a incidentes na investigação e na correção de ameaças.

### <a name="azure-application-gateway"></a>Gateway de Aplicativo do Azure
A arquitetura reduz o risco de vulnerabilidades de segurança usando um Gateway de Aplicativo do Azure com um firewall de aplicativo Web configurado e o conjunto de regras OWASP habilitado. Dentre outros recursos estão:

- SSL de ponta a ponta
- Desabilitar TLS v 1.0 e v 1.1
- Habilitar o TLSv 1.2
- Firewall do aplicativo Web (modo de prevenção)
- Modo de prevenção com conjunto de regras OWASP 3,0
- Habilitar o registro em log de diagnóstico
- Investigações de integridade personalizadas
- A central de segurança do Azure e o Azure Advisor fornecem proteção e notificações adicionais. A Central de Segurança do Azure também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registro em log e auditoria
Os serviços do Azure registram em log de forma extensiva as atividades do sistema e do usuário, bem como a integridade do sistema:

- Os [logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem insights sobre as operações executadas em recursos em uma assinatura. Os logs de atividade podem ajudar a determinar o iniciador, o horário da ocorrência e o status de uma operação.
- Os [logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os logs emitidos por cada um dos recursos. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento do Azure, Key Vault logs de auditoria e acesso do gateway de aplicativo e logs de firewall. Todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, de até 730 dias, para atender aos requisitos de retenção específicos da organização.

### <a name="azure-monitor-logs"></a>Logs do Azure Monitor
Esses logs são consolidados em [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de Dashboard. Depois de coletados, os dados são organizados em tabelas separadas para cada tipo de dados nos espaços de trabalho do Log Analytics, o que permite que todos os dados sejam analisados juntos, independentemente de sua origem original. Além disso, a central de segurança do Azure integra-se aos logs de Azure Monitor, permitindo que os clientes usem consultas Kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

As seguintes [soluções de monitoramento](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) do Azure estão incluídas como parte dessa arquitetura:

- [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes do servidor em intervalos regulares e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução Integridade do Agente relata Quantos agentes são implantados e sua distribuição geográfica, bem como Quantos agentes não respondem e o número de agentes que estão enviando dados operacionais.
- [Análise do Log de Atividades](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de Análise do Log de Atividades auxilia na análise dos logs de atividades do Azure em todas as assinaturas do Azure para um cliente.

### <a name="azure-monitor"></a>Azure Monitor
O [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações façam auditoria, criem alertas e arquivem dados, incluindo o rastreamento de chamadas à API nos recursos do Azure.

### <a name="application-insights"></a>Application Insights
O [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de Gerenciamento de Desempenho de Aplicativos extensível para desenvolvedores da Web em várias plataformas. O Application Insights detecta anomalias de desempenho e os clientes podem usá-lo para monitorar o aplicativo Web online. Ele inclui ferramentas de análise avançadas para ajudar os clientes a diagnosticarem problemas e entenderem o que os usuários realmente fazem com seu aplicativo. Ele foi projetado para ajudar os clientes a aprimorar continuamente o desempenho e a usabilidade.

### <a name="azure-key-vault"></a>Azure Key Vault
Crie um cofre para a organização no qual armazenar chaves e mantenha a responsabilidade de tarefas operacionais, como a seguir:

- Os dados armazenados no Key Vault incluem:

   - Chave do Application Insights
   - Chave de acesso de armazenamento de dados
   - Cadeia de conexão
   - Nome da tabela de dados
   - Credenciais do Usuário

- Políticas de acesso avançadas são configuradas em uma base de necessidade
- As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos
- Todas as chaves e segredos no Key Vault têm datas de validade
- Todas as chaves no Key Vault são protegidas pelo HSM [tipo de chave = chave RSA de 2048 bits protegida por HSM]
- Todos os usuários/identidades recebem as permissões mínimas necessárias usando o RBAC (controle de acesso baseado em função)
- Aplicativos não compartilham um Key Vault, exceto se confiam uns nos outros e precisam ter acesso aos mesmos segredos em runtime
- Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
- Operações criptográficas permitidas para chaves são restritas às necessárias

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel de VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer uma conexão segura com os recursos implantados como parte dessa arquitetura de referência de aplicativo Web de PaaS. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Implementando um túnel de VPN seguro com o Azure, é possível criar uma conexão privada virtual entre uma rede local e uma Rede Virtual do Azure. Essa conexão ocorre pela Internet e permite aos clientes "encapsular" informações com segurança dentro de um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. O modo de encapsulamento IPsec é usado nessa opção como um mecanismo de criptografia.

Como o tráfego do túnel de VPN passa pela Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link de WAN dedicado entre o Azure e a instalação local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não ocorrem pela Internet, elas oferecem mais confiabilidade, mais velocidade, latências mais baixas e maior segurança que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicações do cliente, os dados não passam pela Internet e, portanto, não estão expostos a ela.

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure.

## <a name="cost-considerations"></a>Considerações de custo
Se você ainda não tiver uma conta do Azure, poderá criar uma gratuita. Acesse a [página conta gratuita](https://azure.microsoft.com/free/) para começar, veja o que você pode fazer com uma conta gratuita do Azure e saiba quais produtos são gratuitos por 12 meses.

Para implantar os recursos no aplicativo de exemplo com os recursos de segurança, você precisa pagar por alguns recursos premium. Conforme o aplicativo é dimensionado e as camadas e avaliações gratuitas oferecidas pelo Azure precisam ser atualizadas para atender aos requisitos do aplicativo, seus custos podem aumentar. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) do Azure para estimar seus custos.

## <a name="next-steps"></a>Próximas etapas
Os artigos a seguir podem ajudá-lo a projetar, desenvolver e implantar aplicativos seguros.

- [Design](secure-design.md)
- [Desenvolver](secure-develop.md)
- [Implantar](secure-deploy.md)
