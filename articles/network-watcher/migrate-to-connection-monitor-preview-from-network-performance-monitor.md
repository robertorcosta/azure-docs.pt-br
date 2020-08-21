---
title: Migrar para o monitor de conexão (versão prévia) do Monitor de Desempenho de Rede
titleSuffix: Azure Network Watcher
description: Saiba como migrar para o monitor de conexão (versão prévia) do Monitor de Desempenho de Rede.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: 69dbb1dd4017c5acf9c195f5104741caee38c2b7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701409"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Migrar para o monitor de conexão (versão prévia) do Monitor de Desempenho de Rede

Você pode migrar testes de Monitor de Desempenho de Rede para o novo e aprimorado monitor de conexão (versão prévia) em um clique e sem tempo de inatividade. Para saber mais sobre os benefícios, você pode ler o [Monitor de conexão (versão prévia)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

>[!NOTE]
> Somente testes do monitor de conectividade de serviço podem ser migrados para o monitor de conexão (versão prévia).
>

## <a name="key-points-to-note"></a>Pontos-chave a serem observados

* Os agentes locais e as configurações de firewall funcionarão como estão. Nenhuma alteração é necessária. Log Analytics agentes instalados em máquinas virtuais do Azure precisam ser substituídos pela extensão do observador de rede
* Os testes existentes serão mapeados para o monitor de conexão (visualização) – > grupo de teste – > formato de teste. Os usuários podem clicar em *Editar* para exibir e modificar as propriedades do novo monitor de conexão e baixar o modelo para fazer alterações no monitor de conexão e enviá-lo por meio de Azure Resource Manager.
* Os agentes enviam dados para o espaço de trabalho e a métricas do Log Analytics.
* Dados de monitoramento
    * Dados em Log Analytics – toda a migração de dados continua a estar no espaço de trabalho no qual o NPM está configurado na tabela NetworkMonitoring. Após a migração, os dados vão para a tabela NetworkMonitoring e ConnectionMonitor_CL tabela no mesmo espaço de trabalho. Depois que os testes forem desabilitados de NPM, os dados só serão armazenados na tabela ConnectionMonitor_CL
    * Alertas, painéis e integrações baseados em log – você precisará editar manualmente as consultas com base na nova tabela ConnectionMonitor_CL. Você também pode recriar os alertas em métricas usando este link. A capacidade de migrar alertas baseados em logs na tabela NetworkMonitoring para alertas baseados em métricas automaticamente como parte da migração estará disponível em breve
    
## <a name="prerequisites"></a>Pré-requisitos

*   Verifique se o observador de rede está habilitado na assinatura e na região do espaço de trabalho Log Analytics
*   As máquinas virtuais do Azure com agentes do log Analytics instalados precisarão ser habilitadas com a extensão do observador de rede

## <a name="steps-to-migrate-tests-from-network-performance-monitor-to-connection-monitor-preview"></a>Etapas para migrar testes do Monitor de Desempenho de Rede para o monitor de conexão (versão prévia)

1.  Clique em "Monitor de conexão", navegue até "migrar testes de NPM" para migrar testes para o monitor de conexão (versão prévia)

    ![Captura de tela mostrando testes de migração da visualização do monitor de NPM para conexão](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1.  Selecione assinatura, espaço de trabalho e o recurso NPM que você deseja migrar. No momento, você pode migrar apenas testes do monitor de conectividade de serviço.  
1.  Clique em "importar" para migrar testes
1.  Após o início da migração, ocorrem as seguintes alterações: 
    1. Um novo recurso de monitor de conexão é criado
        1. Um monitor de conexão por região e assinatura é criado. Para testes com agentes locais, o nome do novo monitor de conexão é do formato <workspaceName> _"local". Para testes com agentes do Azure, o nome do novo monitor de conexão é <workspaceName> do formato_<Azure_region_name>
        1. Os dados de monitoramento agora são armazenados no mesmo espaço de trabalho Log Analytics no qual o NPM está habilitado, em uma nova tabela chamada Connectionmonitor_CL tabela. 
        1. O nome do teste é postergado para o nome do grupo de teste. A descrição do teste não será migrada.
        1. Os pontos de extremidade de origem e destino são criados e usados no grupo de teste criado. Para agentes locais, os pontos de extremidade são nomeados no formato <workspaceName> _"Endpoint"_ <FQDN of on-premises machine> . Para o Azure, se os testes de migração contiverem agentes não estiverem em execução, será necessário habilitar os agentes e migrar novamente.
        1. A porta de destino e o intervalo de investigação são movidos para a configuração de teste, ou seja, "TC"_ <testname> "e" TC "_ <testname> _" AppThresholds ". Com base nos valores de porta, o protocolo é definido. Os limites de êxito e outras propriedades opcionais são deixados em branco.
    1. NPM não está desabilitado. Portanto, os testes migrados continuam a enviar dados para a tabela NetworkMonitoring, bem como ConnectionMonitor_CL tabela. Essa etapa garante que os alertas baseados em log existentes e as integrações não sejam afetados. A migração de alertas baseados em log na tabela NetworkMonitoring para alertas baseados em métricas automaticamente como parte da migração estará disponível em breve.
    1. O monitor de conexão criado recentemente ficará visível no monitor de conexão (versão prévia)
1.  Após a migração, você precisará desabilitar manualmente os testes em NPM. Até fazer isso, você continuaria a ser cobrado pelo mesmo. Ao desabilitar o NPM, certifique-se de recriar seus alertas na tabela ConnectionMonitor_CL ou usar métricas. Além disso, certifique-se de que todas as integrações externas, como painéis em Power BI, Grafana, integrações com sistemas SIEM, precisem ser migradas para ConnectionMonitor_CL tabela


## <a name="next-steps"></a>Próximas etapas

* Saiba [como migrar do monitor de conexão para o monitor de conexão (versão prévia)](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* Saiba [como criar um monitor de conexão (versão prévia) usando portal do Azure](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
