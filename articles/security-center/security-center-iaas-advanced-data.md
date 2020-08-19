---
title: Segurança de dados avançada da central de segurança do Azure para máquinas SQL (versão prévia)
description: Saiba como habilitar a segurança de dados avançada para máquinas SQL na central de segurança do Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: e0085ef5213853a1577ec039d5e360114aa7c64e
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566260"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a>Segurança de dados avançada para máquinas SQL (versão prévia)

A segurança de dados avançada da central de segurança do Azure para máquinas SQL protege os servidores SQL hospedados no Azure, em outros ambientes de nuvem e até mesmo em computadores locais. Isso estende as proteções para seus SQL Servers nativos do Azure para oferecer suporte total a ambientes híbridos.

Esse recurso de visualização inclui a funcionalidade para identificar e reduzir possíveis vulnerabilidades de banco de dados e detectar atividades anormais que podem indicar ameaças ao seu banco de dados: 

* **Avaliação de vulnerabilidade** -o serviço de verificação para descobrir, acompanhar e ajudá-lo a corrigir possíveis vulnerabilidades de banco de dados. As verificações de avaliação fornecem uma visão geral do estado de segurança de suas máquinas SQL e detalhes de quaisquer descobertas de segurança.

* [Proteção avançada contra ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) – o serviço de detecção que monitora continuamente os SQL Servers em busca de ameaças como injeção de SQL, ataques de força bruta e abuso de privilégio. Esse serviço fornece alertas de segurança orientados a ações na central de segurança do Azure com detalhes da atividade suspeita, orientação sobre como mitigar as ameaças e opções para continuar suas investigações com o Azure Sentinel.

>[!TIP]
> A segurança de dados avançada para máquinas SQL é uma extensão do pacote de [segurança de dados avançado](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)da central de segurança do Azure, disponível para o banco de dados SQL do Azure, o Azure Synapse e o SQL instância gerenciada.


## <a name="set-up-advanced-data-security-for-sql-machines"></a>Configurar a segurança de dados avançada para máquinas SQL 

A configuração da segurança de dados avançada da central de segurança do Azure para máquinas SQL envolve duas etapas:

* Provisione o agente de Log Analytics no host do SQL Server. Isso fornece a conexão com o Azure.

* Habilite o pacote opcional na página de preços e configurações da central de segurança.

Ambos são descritos abaixo.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Etapa 1. Provisione o agente de Log Analytics no host do SQL Server:

- **SQL Server na VM do Azure** – se o seu computador SQL estiver hospedado em uma VM do Azure, você poderá [provisionar automaticamente o agente de log Analytics](security-center-enable-data-collection.md#workspace-configuration). Como alternativa, você pode seguir o procedimento manual para [Adicionar uma VM do Azure](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines).

- **SQL Server no arco do Azure** – se o SQL Server estiver hospedado em um computador de [arco do Azure](https://docs.microsoft.com/azure/azure-arc/) , você poderá implantar o agente de log Analytics usando a recomendação da central de segurança "log Analytics Agent deve ser instalado em suas máquinas de arco do Azure baseadas no Windows (versão prévia)". Como alternativa, você pode seguir o procedimento manual na [documentação do Arc do Azure](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).

- **SQL Server local** – se o SQL Server estiver hospedado em um computador Windows local sem o Arc do Azure, você terá duas opções para conectá-lo ao Azure:
    
    - **Implantar o arco do Azure** -você pode conectar qualquer computador Windows à central de segurança. No entanto, o Arc do Azure fornece uma integração mais profunda em *todo* o seu ambiente do Azure. Se você configurar o arco do Azure, verá a página **SQL Server – arco do Azure** no portal e seus alertas de segurança aparecerão em uma guia **segurança** dedicada nessa página. Portanto, a primeira e recomendada opção é [Configurar o arco do Azure no host](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) e seguir as instruções para **SQL Server no arco do Azure**, acima.
        
    - **Conectar o computador Windows sem o arco do Azure** – se você optar por conectar um SQL Server em execução em um computador Windows sem usar o Arc do Azure, siga as instruções em [conectar computadores Windows ao Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>Etapa 2. Habilite o pacote opcional na página de preços e configurações da central de segurança:

1. Na barra lateral da central de segurança, abra a página **configurações de preço &** .

    - Se você estiver usando o **espaço de trabalho padrão da central de segurança do Azure** (chamado "defaultworkspace-[sua ID de assinatura]-[Região]"), selecione a **assinatura**relevante.

    - Se você estiver usando **um espaço de trabalho não padrão**, selecione o **espaço** de trabalho relevante (Insira o nome do espaço de trabalho no filtro, se necessário):

        ![título](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. Alterne a opção para **servidores SQL em computadores (visualização)** para habilitado. 

    [![Página de preços da central de segurança com pacotes opcionais](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    A segurança de dados avançada para servidores SQL em computadores será habilitada em todos os SQL Servers conectados ao espaço de trabalho selecionado. A proteção estará totalmente ativa após a primeira reinicialização da instância de SQL Server.

    >[!TIP] 
    > Para criar um novo espaço de trabalho, siga as instruções em [criar um log Analytics espaço de trabalho](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).


1. Opcionalmente, configure a notificação por email para alertas de segurança. 
    Você pode definir uma lista de destinatários para receber uma notificação por email quando os alertas da central de segurança são gerados. O email contém um link direto para o alerta na central de segurança do Azure com todos os detalhes relevantes. Para obter mais informações, consulte [configurar notificações por email para alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).



## <a name="explore-vulnerability-assessment-reports"></a>Explorar relatórios de avaliação de vulnerabilidade

O serviço de avaliação de vulnerabilidade examina seus bancos de dados uma vez por semana. As verificações são executadas no mesmo dia da semana em que você habilitou o serviço.

O painel de avaliação de vulnerabilidade fornece uma visão geral dos resultados da sua avaliação em todos os seus bancos de dados, juntamente com um resumo de bancos de dados íntegros e não íntegros, além de um resumo geral das verificações com falha de acordo com a distribuição de risco.

Você pode exibir os resultados da avaliação de vulnerabilidade diretamente da central de segurança.

1. Na barra lateral da central de segurança, abra a página **recomendações** e selecione as **vulnerabilidades de recomendação em seus servidores SQL em computadores devem ser corrigidos (visualização)**. Para obter mais informações, consulte [recomendações da central de segurança](security-center-recommendations.md). 


    [![* * Vulnerabilidades em seus SQL Servers em computadores devem ser corrigidas (visualização) * * recomendação](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    A exibição detalhada para essa recomendação é exibida.

    [![A exibição detalhada para as * * vulnerabilidades em seus SQL Servers em computadores deve ser corrigida (visualização) * * recomendação](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Para obter mais detalhes, faça uma busca detalhada:

    * Para obter uma visão geral dos recursos verificados (bancos de dados) e da lista de verificações de segurança que foram testadas, selecione o servidor de interesse.

    * Para obter uma visão geral das vulnerabilidades agrupadas por um banco de dados SQL específico, selecione o banco de dados de interesse.

    Em cada exibição, as verificações de segurança são classificadas por **severidade**. Clique em uma verificação de segurança específica para ver um painel de detalhes com uma **Descrição**, como **corrigi** -lo e outras informações relacionadas, como **impacto** ou **benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>Alertas de proteção avançada contra ameaças para servidores SQL em computadores
Os alertas são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar máquinas do SQL. Esses eventos podem disparar alertas mostrados na [seção alertas para o banco de dados SQL e SQL data warehouse da página de referência de alertas](alerts-reference.md#alerts-sql-db-and-warehouse).



## <a name="explore-and-investigate-security-alerts"></a>Explorar e investigar alertas de segurança

Os alertas de segurança estão disponíveis na página de alertas da central de segurança, na guia Segurança do recurso ou por meio do link direto nos emails de alerta.

1. Para exibir alertas, selecione **alertas de segurança** na barra lateral da central de segurança e selecione um alerta.

1. Os alertas são projetados para serem independentes, com etapas de correção detalhadas e informações de investigação em cada um. Você pode investigar ainda mais usando outros recursos da central de segurança do Azure e do Azure Sentinel para uma visão mais ampla:

    * Habilite o recurso de auditoria do SQL Server para investigações adicionais. Se você for um usuário do Azure Sentinel, poderá carregar os logs de auditoria do SQL dos eventos do log de segurança do Windows para sentinela e aproveitar uma experiência de investigação rica. [Saiba mais sobre a auditoria de SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15).
    * Para melhorar sua postura de segurança, use as recomendações da central de segurança para o computador host indicado em cada alerta. Isso reduzirá os riscos de futuros ataques. 

    [Saiba mais sobre como gerenciar e responder a alertas](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).


## <a name="next-steps"></a>Próximas etapas

Para obter material relacionado, consulte o seguinte artigo:

- [Alertas de segurança para o banco de dados SQL e SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Configurar notificações por email para alertas de segurança](security-center-provide-security-contact-details.md)
- [Saiba mais sobre o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Pacote de segurança de dados avançado da central de segurança do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)