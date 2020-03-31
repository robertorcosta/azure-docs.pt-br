---
title: Segurança avançada de dados para IaaS no Azure Security Center | Microsoft Docs
description: " Saiba como ativar a segurança avançada de dados para o IaaS no Azure Security Center. "
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
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282725"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Segurança avançada de dados para servidores SQL em Máquinas Virtuais Do Azure (Preview)
A segurança avançada de dados para servidores SQL em Máquinas Virtuais Azure é um pacote unificado para recursos avançados de segurança SQL. Esse recurso de visualização inclui funcionalidade para identificar e mitigar possíveis vulnerabilidades do banco de dados e detectar atividades anômalas que possam indicar ameaças ao seu banco de dados. 

Esta oferta de segurança para servidores SQL Do Azure VMs é baseada na mesma tecnologia fundamental usada no [pacote Azure SQL Database Advanced Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Visão geral

A segurança avançada de dados fornece um conjunto de recursos avançados de segurança SQL, que consistem em avaliação de vulnerabilidades e proteção avançada contra ameaças.

* [A avaliação de vulnerabilidades](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) é um serviço fácil de configurar que pode descobrir, rastrear e ajudá-lo a corrigir possíveis vulnerabilidades no banco de dados. Ele fornece visibilidade ao seu estado de segurança e inclui as etapas para resolver problemas de segurança e melhorar suas fortificações de banco de dados.
* [O Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detecta atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais para acessar ou explorar seu servidor SQL. Ele monitora continuamente seu banco de dados para atividades suspeitas e fornece alertas de segurança orientados para ações em padrões anômalos de acesso ao banco de dados. Esses alertas fornecem os detalhes da atividade suspeita e as ações recomendadas para investigar e mitigar a ameaça.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Comece com a segurança avançada de dados para SQL em VMs Azure

As etapas a seguir começam a ser iniciadas com o Advanced Data Security for SQL no Azure VMs Public Preview.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Configure segurança avançada de dados para SQL em VMs azure

Habilite a segurança avançada de dados para servidores SQL em máquinas virtuais no nível de assinatura/espaço de trabalho:
 
1. Na barra lateral do Security Center, abra a página **de configurações de & de preços.**

1. Selecione a assinatura ou o espaço de trabalho para o qual você deseja ativar o Advanced Data Security para SQL em VMs Azure.

1. Alterne a opção de **servidores SQL em VM (Preview)** para habilitação. 

    (Clique na captura de tela para expandir)

    [![Recomendações e alertas do Security Center como visto no Windows Admin Center](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    O Advanced Data Security for SQL Servers será ativado em todos os servidores SQL conectados ao espaço de trabalho selecionado ou no espaço de trabalho padrão da assinatura selecionada.

    >[!NOTE]
    > A solução estará totalmente ativa após a primeira reinicialização do SQL Server. 

Para criar um novo espaço de trabalho, siga as instruções em [Criar um espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Para conectar o host do SQL Server a um espaço de trabalho, siga as instruções em [Conectar computadores Windows ao Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


## <a name="set-up-email-notification-for-security-alerts"></a>Configure a notificação por e-mail para alertas de segurança 

Você pode definir uma lista de destinatários para receber uma notificação por e-mail quando os alertas da Central de Segurança forem gerados. O e-mail contém um link direto para o alerta no Azure Security Center com todos os detalhes relevantes. 

1. Vá para as**configurações de preços do Security** **Center** > & e clique na assinatura relevante

    ![Configurações de Assinatura](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. No menu Configurações, clique em **Notificações de e-mail**. 
1. Na caixa de texto **do endereço de e-mail,** digite os endereços de e-mail para receber as notificações. Você pode inserir mais de um endereço de e-mail separando os endereços de e-mail com uma comuma (,).  Por admin1@mycompany.comexemplo,admin2@mycompany.comadmin3@mycompany.com

    ![Configurações de email](./media/security-center-advanced-iaas-data/email-settings.png)

1. Nas configurações de **notificação de e-mail,** defina as seguintes opções:
  
    * **Envie notificação por e-mail para alertas de alta gravidade**: Em vez de enviar e-mails para todos os alertas, envie apenas para alertas de alta gravidade.
    * **Envie também notificações por e-mail aos proprietários de assinaturas**: Envie notificações para os proprietários de assinaturas também.

1. Na parte superior da tela notificações de **e-mail,** clique **em Salvar**.

  > [!NOTE]
  > Certifique-se de clicar **em Salvar** antes de fechar a janela, ou as novas configurações de notificação **de e-mail** não serão salvas.

## <a name="explore-vulnerability-assessment-reports"></a>Explorar relatórios de avaliação de vulnerabilidades

O painel de avaliação de vulnerabilidades fornece uma visão geral dos resultados de sua avaliação em todos os seus bancos de dados. Você pode visualizar a distribuição de bancos de dados de acordo com a versão do SQL Server, juntamente com um resumo de falha versus aprovação de bancos de dados e um resumo geral de verificações de falha de acordo com a distribuição de riscos.

Você pode visualizar os resultados da avaliação de vulnerabilidadediretamente do Security Center.

1. Na barra lateral do Security Center, em higiene de segurança de recursos, selecione **Data & Storage**.

1. Selecione as vulnerabilidades de recomendação **em seus bancos de dados SQL em VMs devem ser corrigidas (Visualização)**. Para obter mais informações, consulte [Recomendações do Centro de Segurança](security-center-recommendations.md). 

    [![**As vulnerabilidades em seus bancos de dados SQL em VMs devem ser corrigidas (Preview)** recomendação](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    A visão detalhada desta recomendação aparece.

    [![A visualização detalhada das **Vulnerabilidades em seus bancos de dados SQL em VMs deve ser corrigida (Preview)** recomendação](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Para detalhar para obter mais detalhes:

    * Para obter uma visão geral dos recursos digitalizados (bancos de dados) e da lista de verificações de segurança que foram testadas, clique no servidor de interesse.
    [![Vulnerabilidades agrupadas pelo servidor SQL](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Para obter uma visão geral das vulnerabilidades agrupadas por um banco de dados SQL específico, clique no banco de dados de interesse.
    [![Vulnerabilidades agrupadas pelo servidor SQL](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    Em cada visão, as verificações de segurança são classificadas pela **Gravidade**. Clique em uma verificação de segurança específica para ver um painel de detalhes com uma **Descrição,** como **remediá-lo** e outras informações relacionadas, como **Impacto** ou **Benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Proteção avançada contra ameaças para servidores SQL em alertas de VMs do Azure
Os alertas são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar servidores SQL. Esses eventos podem disparar os alertas a seguir:

### <a name="anomalous-access-pattern-alerts-preview"></a>Alertas de padrão de acesso anômalos (Visualização)

* **Acesso a partir de localização incomum:** Este alerta é acionado quando há uma alteração no padrão de acesso ao servidor SQL, onde alguém fez logon no servidor SQL a partir de uma localização geográfica incomum. Possíveis causas:
    * Um invasor ou ex-empregado mal-intencionado acessou seu SQL Server.
    * Um usuário legítimo acessou seu SQL Server a partir de um novo local.
* **Acesso a partir de um aplicativo potencialmente prejudicial**: Este alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Possíveis causas:
    * Um invasor tentando violar seu SQL usando ferramentas de ataque comuns.
    * Um teste de penetração legítimo em ação.
* **Acesso a partir de uma entidade de segurança não familiar**: Este alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, onde alguém fez logon no SQL Server a partir de uma entidade de segurança incomum (usuário SQL). Possíveis causas:
    * Um invasor ou ex-empregado mal-intencionado acessou seu SQL Server. 
    * Um usuário legítimo acessou seu SQL Server a partir de um novo diretor.
* **Credenciais SQL de força bruta**: Este alerta é disparado quando há um número alto anormal de logons com falha com credenciais diferentes. Possíveis causas:
    * Um atacante tentando violar seu SQL usando força bruta.
    * Um teste de penetração legítimo em ação.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Potenciais ataques de injeção SQL (suportados no SQL Server 2019)

* **Vulnerabilidade à injeção SQL**: Este alerta é acionado quando um aplicativo gera uma declaração SQL defeituosa no banco de dados. Esse alerta pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Possíveis causas:
    * Um defeito no código do aplicativo que cria a instrução SQL com erro
    * O código do aplicativo ou procedimentos armazenados não limpam a entrada do usuário ao construir a instrução SQL com erro, o que pode ser explorado para injeção de SQL
* **Potencial injeção de SQL**: Este alerta é disparado quando acontece uma exploração ativa em uma vulnerabilidade do aplicativo identificada para injeção de SQL. Isso significa que o invasor está tentando inserir instruções SQL maliciosas usando o código de aplicativo ou procedimentos armazenados vulneráveis.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Comando inseguro (suportado no SQL Server 2019)

* **Ação potencialmente insegura**: Este alerta é acionado quando um comando altamente privilegiado e potencialmente inseguro é executado. Possíveis causas:
    * O comando que recomenda ser desativado para uma melhor postura de segurança está ativado.
    * Um invasor tentando explorar o acesso SQL ou escalar privilégios.   


## <a name="explore-and-investigate-security-alerts"></a>Explorar e investigar alertas de segurança

Seus alertas de segurança de dados estão disponíveis na página de alertas do Security Center, na guia de segurança do recurso ou no link direto nos e-mails de alerta.

1. Para exibir alertas:

    * No Security Center - Clique **em alertas** de segurança na barra lateral e selecione um alerta.
    * No escopo de recursos - Abra a página de recursos relevante e, a partir da barra lateral, clique em **Segurança**. 

1. Os alertas são projetados para serem independentes, com etapas detalhadas de remediação e informações de investigação em cada um deles. Você pode investigar mais usando outros recursos do Azure Security Center e do Azure Sentinel para uma visão mais ampla:

    * Habilite o recurso de auditoria do SQL Server para investigações posteriores. Se você é um usuário do Azure Sentinel, você pode carregar os registros de auditoria SQL dos eventos do Windows Security Log para o Sentinel e desfrutar de uma rica experiência de investigação.
    * Para melhorar sua postura de segurança, use as recomendações do Security Center para a máquina host indicada em cada alerta. Isso reduzirá os riscos de ataques futuros. 


## <a name="next-steps"></a>Próximas etapas

Para material relacionado, consulte o seguinte artigo:

- [Como remediar recomendações](security-center-remediate-recommendations.md)