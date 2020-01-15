---
title: Solucionar problemas de conectividade do Azure para a recuperação de desastre do Azure com o Azure Site Recovery
description: Solucionar problemas de conectividade na recuperação de desastre de VM do Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 08/05/2019
ms.openlocfilehash: d55f06669a538c2f26f3a1d2da0d96a73529f76e
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941462"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Solucionar problemas de conectividade de rede de VM do Azure para Azure

Este artigo descreve os problemas comuns relacionados à conectividade de rede quando você replica e recupera máquinas virtuais do Azure de uma região para outra região. Para obter mais informações sobre os requisitos de rede, consulte os [requisitos de conectividade para replicar VMs do Azure](azure-to-azure-about-networking.md).

Para replicação de recuperação de Site para o trabalho, conectividade de saída para intervalos específicos de IP ou URLs é necessária da VM. Se a VM estiver atrás de um firewall ou usa regras de grupo de segurança de rede (NSG) para controlar a conectividade de saída, você poderá enfrentar um desses problemas.

**URL** | **Detalhes**  
--- | ---
*.blob.core.windows.net | Necessário para que os dados possam ser gravados para a conta de armazenamento de cache da região de origem da VM. Se você souber todas as contas de armazenamento em cache para suas VMs, poderá permitir-listar as URLs de conta de armazenamento específicas (por exemplo, cache1.blob.core.windows.net e cache2.blob.core.windows.net) em vez de *. blob.core.windows.net
login.microsoftonline.com | Necessário para autorização e autenticação para as URLs do serviço de recuperação de Site.
*.hypervrecoverymanager.windowsazure.com | Necessário para que a comunicação de serviço de recuperação de Site possa ocorrer da VM. Você pode usar o ' Site Recovery IP ' correspondente se o proxy de firewall oferecer suporte a IPs.
*.servicebus.windows.net | Necessário para que os dados de monitoramento e diagnóstico de recuperação de Site possam ser gravados da VM. Você pode usar o ' Site Recovery Monitoring IP ' correspondente se o proxy de firewall oferecer suporte a IPs.

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para intervalos de IP ou URLs de recuperação de Site (código de erro 151037 ou 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problema 1: falha ao registrar a máquina virtual do Azure com o Site Recovery (151195) </br>
- **Possível causa** </br>
  - A conexão não pode ser estabelecida com Site Recovery pontos de extremidade devido à falha na resolução do DNS.
  - Isso é visto com mais frequência durante a proteção novamente quando você fizer failover da máquina virtual, mas o servidor DNS não está acessível da região de recuperação de Desastre.

- **Resolução**
   - Se você estiver usando o DNS personalizado, verifique se o servidor DNS está acessível na região de recuperação de desastre. Para verificar se você tem um DNS personalizado vá para a VM > rede de Recuperação de Desastres > servidores DNS. Tente acessar o servidor DNS da máquina virtual. Se não estiver acessível, torne-o acessível ao fazer o failover do servidor DNS ou criar a linha de site entre a rede de DR e o DNS.

    ![com-erro](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: falha na configuração do Azure Site Recovery (151196)

> [!NOTE]
> Se as máquinas virtuais estiverem atrás do Load balancer interno **padrão** , ele não teria acesso aos IPS do O365 (ou seja, login.microsoftonline.com) por padrão. Altere-o para o tipo **básico** de balanceador de carga interno ou crie acesso de saída conforme mencionado no [artigo](https://aka.ms/lboutboundrulescli).

- **Possível causa** </br>
  - Não é possível estabelecer a Conexão para pontos de extremidade de IP4 de identidade e autenticação do Office 365.

- **Resolução**
  - O Azure Site Recovery precisa de acesso aos intervalos de IPs do Office 365 para autenticação.
    Se você estiver usando o proxy de regras de firewall NSG (grupo) de segurança de rede do Azure para controlar a conectividade de rede de saída na VM, verifique se você permitiu a comunicação com intervalos de IP do O365. Criar Azure Active Directory uma regra de NSG baseada na [marca de serviço (AD do Azure)](../virtual-network/security-overview.md#service-tags) para permitir acesso a todos os endereços IP correspondentes ao Azure AD
      - Se novos endereços forem adicionados ao Azure AD no futuro, você precisará criar novas regras de NSG.

### <a name="example-nsg-configuration"></a>Exemplo de Configuração do NSG

Este exemplo mostra como configurar regras de NSG para uma VM a ser replicada.

- Se você está usando regras de NSG para controlar a conectividade de saída, use regras de "Permitir HTTPS de saída" para a port:443 para todos os intervalos de endereços IP necessários.
- O exemplo presume que o local de origem da VM é "Leste dos EUA" e que o local de destino é "EUA Central".

### <a name="nsg-rules---east-us"></a>Regras de NSG – Leste dos EUA

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage.EastUS" no NSG conforme mostrado na captura de tela abaixo.

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG conforme mostrado na captura de tela abaixo.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Crie regras HTTPS (443) de saída para IPs do Site Recovery correspondentes ao local de destino:

   **Localidade** | **Endereço IP do Site Recovery** |  **Endereço IP de monitoramento do Site Recovery**
    --- | --- | ---
   EUA Central | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Regras de NSG – EUA Central

Essas regras são necessárias para que a replicação possa ser ativada da região de destino para a região de origem após o failover:

1. Crie uma regra de segurança HTTPS (443) de saída para "Storage.CentralUS" no NSG.

2. Crie uma regra de segurança HTTPS (443) de saída para "AzureActiveDirectory" no NSG.

3. Crie regras HTTPS (443) de saída para IPs do Site Recovery correspondentes ao local de origem:

   **Localidade** | **Endereço IP do Site Recovery** |  **Endereço IP de monitoramento do Site Recovery**
    --- | --- | ---
   EUA Central | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: falha na configuração do Site Recovery (151197)
- **Possível causa** </br>
  - Não é possível estabelecer conexão com pontos de extremidade de serviço de Recuperação de Site.

- **Resolução**
  - O Azure Site Recovery requer acesso para a [intervalos de IP de recuperação de Site](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) dependendo da região. Certifique-se de que necessários intervalos os de IP são acessíveis a partir da máquina virtual.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problema 4: falha na replicação do A2A quando o tráfego de rede passa pelo servidor proxy local (151072)
- **Possível causa** </br>
  - As configurações de proxy personalizadas são inválidas e Azure Site Recovery agente do serviço de mobilidade não detectou automaticamente as configurações de proxy do IE


- **Resolução**
  1. O agente do Serviço de Mobilidade detecta as configurações de proxy do IE no Windows e o ambiente /etc/ no Linux.
  2. Se preferir definir proxy somente para Azure Site Recovery serviço de mobilidade, você poderá fornecer os detalhes do proxy em ProxyInfo. conf localizado em:</br>
     - ``/usr/local/InMage/config/`` no ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` no ***Windows***
  3. O ProxyInfo.conf deve ter as configurações de proxy no seguinte formato INI.</br>
                *[proxy]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Azure Site Recovery agente do serviço de mobilidade dá suporte apenas a ***proxies não autenticados***.

### <a name="fix-the-problem"></a>Corrija o problema
Para permitir [as URLs necessárias](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou os [intervalos de IP necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), siga as etapas no [documento diretrizes de rede](site-recovery-azure-to-azure-networking-guidance.md).


## <a name="next-steps"></a>Próximos passos
[Replicar as máquinas virtuais do Azure](site-recovery-replicate-azure-to-azure.md)
