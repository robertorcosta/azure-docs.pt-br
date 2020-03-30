---
title: Solucionar problemas de conectividade do ponto de extremidade privado do Azure
description: Orientação passo a passo para diagnosticar conectividade de ponto final privado
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
ms.openlocfilehash: fcc482e6231bbd925fd500a37989052765dede58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538527"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Solucionar problemas de conectividade do ponto de extremidade privado do Azure

Este artigo fornece orientações passo a passo para validar e diagnosticar sua configuração de conectividade Azure Private Endpoint.

O Azure Private Endpoint é uma interface de rede que o conecta de forma privada e segura a um serviço de link privado. Essa solução ajuda você a proteger suas cargas de trabalho no Azure, fornecendo conectividade privada aos recursos de serviço do Azure a partir de sua rede virtual. Esta solução efetivamente traz esses serviços para sua rede virtual.

Aqui estão os cenários de conectividade disponíveis com o Private Endpoint:

- Rede virtual da mesma região
- Redes virtuais regionalmente peered
- Redes virtuais de sionizadas globalmente
- Cliente no local sobre circuitos VPN ou Azure ExpressRoute

## <a name="diagnose-connectivity-problems"></a>Diagnosticar problemas de conectividade 

Revise essas etapas para garantir que todas as configurações usuais sejam esperadas para resolver problemas de conectividade com a configuração do ponto final privado.

1. Revise a configuração do Private Endpoint navegando pelo recurso.

    a. Vá para **Private Link Center**.

      ![Centro de Link Privado](./media/private-endpoint-tsg/private-link-center.png)

    b. No painel esquerdo, selecione **Pontos finais privados**.
    
      ![Pontos finais privados](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filtre e selecione o ponto final privado que deseja diagnosticar.

    d. Revise as informações da rede virtual e do DNS.
     - Valide se o estado de conexão é **aprovado**.
     - Certifique-se de que a VM tenha conectividade com a rede virtual que hospeda os pontos finais privados.
     - Verifique se as informações FQDN (cópia) e endereço IP privado são atribuídos.
    
       ![Configuração de rede virtual e DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Use [o Monitor Do Azure](https://docs.microsoft.com/azure/azure-monitor/overview) para ver se os dados estão fluindo.

    a. No recurso de ponto final privado, selecione **Monitor**.
     - Selecione **Dados In** ou **Data Out**. 
     - Veja se os dados estão fluindo quando você tenta se conectar ao ponto final privado. Espere um atraso de aproximadamente 10 minutos.
    
       ![Verifique a telemetria de ponto final privado](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Use a solução de problemas da **Conexão VM** do Azure Network Watcher.

    a. Selecione o VM do cliente.

    b. Selecione **'Solucionar problemas'** e selecione a guia **Conexões Desaída.**
    
      ![Observador de rede - Testar conexões de saída](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Selecione **Usar o Observador de Rede para rastrear a conexão detalhada**.
    
      ![Observador de rede - Solução de problemas de conexão](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Selecione **Teste por FQDN**.
     - Cole o FQDN do recurso de ponto final privado.
     - Providencie um porto. Normalmente, use 443 para armazenamento Azure ou Azure Cosmos DB e 1336 para SQL.

    e. Selecione **Teste**e valide os resultados do teste.
    
      ![Observador de Rede - Resultados do teste](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. A resolução DNS dos resultados do teste deve ter o mesmo endereço IP privado atribuído ao ponto final privado.

    a. Se as configurações de DNS estiverem incorretas, siga estas etapas:
     - Se você usar uma zona privada: 
       - Certifique-se de que a rede virtual VM do cliente esteja associada à zona privada.
       - Verifique se o registro de zona DNS privado existe. Se não existe, crie-o.
     - Se você usar DNS personalizado:
       - Revise as configurações de DNS personalizadas e valide se a configuração do DNS está correta.
       Para obter orientação, consulte [Visão geral do ponto final privado: configuração dNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).

    b. Se a conectividade estiver falhando devido a grupos de segurança de rede (NSGs) ou rotas definidas pelo usuário:
     - Revise as regras de saída do NSG e crie as regras de saída apropriadas para permitir o tráfego.
    
       ![Regras de saída do NSG](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Se a conexão tiver resultados validados, o problema de conectividade pode estar relacionado a outros aspectos, como segredos, tokens e senhas na camada do aplicativo.
   - Neste caso, revise a configuração do recurso de link privado associado ao ponto final privado. Para obter mais informações, consulte o [guia de solução de problemas do Azure Private Link](troubleshoot-private-link-connectivity.md).

1. Entre em contato com a equipe [de suporte do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se seu problema ainda não foi resolvido e um problema de conectividade ainda existe.

## <a name="next-steps"></a>Próximas etapas

 * [Criar um ponto final privado na sub-rede atualizada (portal Azure)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Guia de solução de problemas do Azure Private Link](troubleshoot-private-link-connectivity.md)
