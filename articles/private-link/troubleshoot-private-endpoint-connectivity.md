---
title: Solucionar problemas de conectividade de ponto de extremidade privado do Azure
description: Diretrizes passo a passo para diagnosticar a conectividade de ponto de extremidade particular
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 1c3fc4ebaffe2347a9c46b626b94042d1c9cfe11
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031849"
---
# <a name="troubleshoot-private-endpoint-connectivity-problems"></a>Solucionar problemas de conectividade de ponto de extremidade privado

Este guia fornece orientações passo a passo para validar e diagnosticar sua configuração de conectividade de ponto de extremidade particular. 

O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura a um serviço de link privado. Essa solução ajuda você a proteger suas cargas de trabalho no Azure fornecendo conectividade privada aos recursos de serviço do Azure de sua rede virtual. Isso efetivamente coloca esses serviços em sua rede virtual. 

Aqui estão os cenários de conectividade que estão disponíveis com pontos de extremidade privados 
- rede virtual da mesma região 
- redes virtuais emparelhadas de região
- redes virtuais emparelhadas globalmente
- cliente local por meio de circuitos de VPN ou de rota expressa

## <a name="diagnosing-connectivity-problems"></a>Diagnosticando problemas de conectividade 
Siga as etapas listadas abaixo para verificar se todas as configurações usuais estão conforme o esperado para resolver problemas de conectividade com a configuração do ponto de extremidade privado.

1. Examinar a configuração do ponto de extremidade privado navegando no recurso 

    a) ir para o **centro de links privado**

      ![Centro de links privado](./media/private-endpoint-tsg/private-link-center.png)

    b) selecionar pontos de extremidade privados no painel de navegação à esquerda
    
      ![Pontos de extremidade privados](./media/private-endpoint-tsg/private-endpoints.png)

    c) filtrar e selecionar o ponto de extremidade privado que você deseja diagnosticar

    d) examinar as informações de rede virtual e DNS
    
     - A validação do estado de conexão foi **aprovada**
     - Verifique se a VM tem conectividade com a VNet que hospeda os pontos de extremidade privados
     - Informações de FQDN (cópia) e endereço IP privado atribuído
    
       ![Configuração de VNet e DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)    
    
2. Usar [**Azure monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) para examinar os dados estão fluindo

    a) no recurso de ponto de extremidade privado, selecione **Monitor**
     - Selecione dados ou saída e reveja se os dados estão fluindo ao tentar se conectar ao ponto de extremidade privado. Espere um atraso de aproximadamente 10 minutos.
    
       ![Verificar telemetria de ponto de extremidade privado](./media/private-endpoint-tsg/private-endpoint-monitor.png)

3. Usar a solução de problemas de conexão de VM do **observador de rede**

    a) Selecione a VM do cliente

    b) Selecione a seção **solução de problemas de conexão** , guia conexão de **saída**
    
      ![Observador de rede-testar conexões de saída](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c) selecione **usar observador de rede para rastreamento de conexão de detalhes**
    
      ![Observador de rede-solução de problemas de conexão](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d) selecione **testar por FQDN**
     - Cole o FQDN do recurso de ponto de extremidade privado
     - Forneça uma porta (*geralmente 443 para o armazenamento do Azure ou cosmos, 1336 para SQL...* )

    e) clique em **testar** e valide os resultados do teste
    
      ![Observador de rede – resultados do teste](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
4. A resolução de DNS dos resultados de teste deve ter o mesmo endereço IP privado atribuído ao ponto de extremidade privado

    a) se as configurações de DNS estiverem incorretas, faça o seguinte
     - Usando a zona privada: 
       - Verifique se a VNet da VM do cliente está associada à zona privada
       - Examinar DNS privado registro de zona existe, criar se não existente
    
     - Usando o DNS personalizado:
       - Examine as configurações de DNS do cliente e valide se a configuração do DNS está correta.
       Consulte [visão geral do ponto de extremidade privado-configuração de DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration) para obter diretrizes.

    b) se a conectividade estiver falhando por causa de NSG/UDRs
     - Examine as regras de saída do NSG e crie as regras de saída apropriadas para permitir o tráfego
    
       ![Regras de saída do NSG](./media/private-endpoint-tsg/nsg-outbound-rules.png)

5. Se a conexão tiver resultados validados, o problema de conectividade poderá estar relacionado a outros aspectos, como segredos, tokens, senhas na camada de aplicativo.
   - Nesse caso, examine a configuração do recurso de link privado associado ao ponto de extremidade privado. Consulte o [Guia de solução de problemas de link privado](https://docs.microsoft.com/azure/private-link/private-link-connectivity-troubleshooting). 

6. Contate a equipe de [suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se o problema ainda não estiver resolvido e se o problema de conectividade ainda existir. 

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

 * [Criar um ponto de extremidade privado na sub-rede atualizada (portal do Azure)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)

 * [Guia de solução de problemas de link privado](https://docs.microsoft.com/azure/private-link/private-link-connectivity-troubleshooting)
