---
title: Solucionar problemas de diagnóstico de conectividade no Integration Runtime do SSIS
description: Este artigo fornece diretrizes de solução de problemas para diagnosticar a conectividade no Integration Runtime do SSIS
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 8e520048a6067f134e847953f4f4aa0598d9926e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172509"
---
# <a name="troubleshoot-diagnose-connectivity-in-the-ssis-integration-runtime"></a>Solucionar problemas de diagnóstico de conectividade no Integration Runtime do SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Se você encontrar problemas de conectividade ao executar pacotes do SSIS no tempo de execução de integração do SSIS, especialmente se o tempo de execução de integração do SSIS tiver ingressado na rede virtual do Azure. Você pode tentar diagnosticar problemas automaticamente usando esse recurso de diagnóstico de conectividade na página monitoramento do tempo de execução de integração do SSIS do portal de Azure Data Factory. 

 ![Página de monitor-diagnosticar a ](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png) ![ conexão do monitor de conectividade](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)
 
Este artigo inclui os erros mais comuns que você pode encontrar quando estiver testando a conexão no Integration Runtime do SSIS. Ele descreve as possíveis causas e ações para resolver os erros. 

## <a name="common-errors-potential-causes-and-recommendation-solutions"></a>Erros comuns, possíveis causas e soluções de recomendação

### <a name="error-code-invalidinput"></a>Código de erro: InvalidInput.
* **Mensagem de erro**: Verifique se a entrada está correta.
* **Possíveis causas:** Sua entrada está incorreta.
* **Recomendação:** Verifique sua entrada.

### <a name="error-code-firewallornetworkissue"></a>Código de erro: FirewallOrNetworkIssue.
* **Mensagem de erro**: Verifique se essa porta está aberta no seu firewall/servidor/NSG e a rede está estável.
* **Possíveis causas:** 
  * O servidor não abre essa porta.
  * Seu grupo de segurança de rede tem o tráfego de saída negado nesta porta
  * O firewall local/Firewall do NVA/Azure não abre essa porta.
* **Recomendação:** 
  * Abra esta porta no servidor.
  * Atualize o grupo de segurança de rede para permitir o tráfego de saída nesta porta.
  * Abra essa porta no firewall do NVA/Firewall do Azure/local.

### <a name="error-code-misconfigureddnssettings"></a>Código de erro: MisconfiguredDnsSettings.
* **Mensagem de erro**: se você estiver usando seu próprio servidor DNS na VNet ingressada pelo seu Azure-SSIS ir, verifique se ele pode resolver o nome do host.
* **Possíveis causas:** 
  *  O problema do DNS personalizado
  *  Você não está usando um FQDN (nome de domínio totalmente qualificado) para seu nome de host privado
* **Recomendação:** 
  *  Corrija seu problema de DNS personalizado para certificar-se de que ele pode resolver o nome do host.
  *  Use o FQDN (nome de domínio totalmente qualificado), por exemplo, use <your_private_server>. contoso.com em vez de <your_private_server>, pois Azure-SSIS IR não acrescentará automaticamente seu próprio sufixo DNS.

### <a name="error-code-servernotallowremoteconenction"></a>Código de erro: ServerNotAllowRemoteConenction.
* **Mensagem de erro**: Verifique se o servidor permite conexões TCP remotas por meio desta porta.
* **Possíveis causas:** 
  *  O Firewall do servidor não permite conexões TCP remotas.
  *  O servidor não está online.
* **Recomendação:** 
  *  Permitir conexões TCP remotas no firewall do servidor.
  *  Inicie o servidor.
   
### <a name="error-code-misconfigurednsgsettings"></a>Código de erro: MisconfiguredNsgSettings.
* **Mensagem de erro**: Verifique se o NSG de sua VNet permite o tráfego de saída por essa porta. Se você estiver usando o Azure ExpressRoute e o ou um UDR, verifique se essa porta está aberta no seu firewall/servidor.
* **Possíveis causas:** 
  *  Seu grupo de segurança de rede tem o tráfego de saída negado nesta porta.
  *  O firewall local/Firewall do NVA/Azure não abre essa porta.
* **Recomendação:** 
  *  Atualize o grupo de segurança de rede para permitir o tráfego de saída nesta porta.
  *  Abra essa porta no firewall do NVA/Firewall do Azure/local.

### <a name="error-code-genericissues"></a>Código de erro: GenericIssues.
* **Mensagem de erro**: falha no teste de conexão devido a problemas genéricos.
* **Possíveis causas:** A conexão de teste encontrou um problema temporário geral.
* **Recomendação:** Repita a conexão de teste mais tarde. Se tentar novamente não ajudar, entre em contato com a equipe de suporte do Azure Data Factory.


### <a name="error-code-pspingexecutiontimeout"></a>Código de erro: PSPingExecutionTimeout.
* **Mensagem de erro**: tempo limite de conexão de teste, tente novamente mais tarde.
* **Possíveis causas:** A conectividade de teste atingiu o tempo limite.
* **Recomendação:** Repita a conexão de teste mais tarde. Se tentar novamente não ajudar, entre em contato com a equipe de suporte do Azure Data Factory.

### <a name="error-code-networkinstable"></a>Código de erro: NetworkInstable.
* **Mensagem de erro**: conexão de teste bem-sucedida devido à instabilidade da rede.
* **Possíveis causas:** Problema de rede transitório.
* **Recomendação:** Verifique se a rede do servidor ou do firewall está estável.

## <a name="next-steps"></a>Próximas etapas

- Implante seus pacotes. Confira mais informações em [Implantar um projeto do SSIS no Azure com o SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Execute os pacotes. Confira mais informações em [Executar pacotes SSIS no Azure com o SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Agende seus pacotes. Para obter mais informações, consulte [agendar pacotes do SSIS no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).

