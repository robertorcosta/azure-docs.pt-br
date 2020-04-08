---
title: Solucionando problemas de conectividade para recuperação de desastres do Azure para o Azure com a recuperação do site do Azure
description: Solucionaproblemas de conectividade na recuperação de desastres da Azure VM
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 49d2d3d3e8829198a57aaf2feb40e89f105667bd
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804853"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Solucionar problemas de conectividade de rede Azure-to-Azure VM

Este artigo descreve os problemas comuns relacionados à conectividade de rede quando você replica e recupera máquinas virtuais (VM) do Azure de uma região para outra. Para obter mais informações sobre os requisitos de rede, consulte os [requisitos de conectividade para replicar VMs Do Zure](azure-to-azure-about-networking.md).

Para replicação de recuperação de Site para o trabalho, conectividade de saída para intervalos específicos de IP ou URLs é necessária da VM. Se a VM estiver atrás de um firewall ou usa regras de grupo de segurança de rede (NSG) para controlar a conectividade de saída, você poderá enfrentar um desses problemas.

| **URL** | **Detalhes** |
| --- | --- |
| `*.blob.core.windows.net` | Necessário para que os dados possam ser gravados para a conta de armazenamento de cache da região de origem da VM. Se você souber todas as contas de armazenamento de cache para suas VMs, você pode usar uma lista de per-requisitos para urls de conta de armazenamento específicas. Por `cache1.blob.core.windows.net` exemplo, `cache2.blob.core.windows.net` e `*.blob.core.windows.net`em vez de . |
| `login.microsoftonline.com` | Necessário para autorização e autenticação para as URLs do serviço de recuperação de Site. |
| `*.hypervrecoverymanager.windowsazure.com` | Necessário para que a comunicação de serviço de recuperação de Site possa ocorrer da VM. Você pode usar o _IP de recuperação de site_ correspondente se o proxy de firewall suportar IPs. |
| `*.servicebus.windows.net` | Necessário para que os dados de monitoramento e diagnóstico de recuperação de Site possam ser gravados da VM. Você pode usar o IP correspondente _de monitoramento de recuperação de site_ se o proxy de firewall suportar IPs. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para intervalos de IP ou URLs de recuperação de Site (código de erro 151037 ou 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Problema 1: falha ao registrar a máquina virtual do Azure com o Site Recovery (151195)

#### <a name="possible-cause"></a>Possível causa

Uma conexão não pode ser estabelecida nos pontos finais de recuperação do site devido a uma falha de resolução do Sistema de Nomes de Domínio (DNS). Esse problema é mais comum durante a reproteção quando você falhou sobre a VM, mas o servidor DNS não pode ser alcançado a partir da região de recuperação de desastres (DR).

#### <a name="resolution"></a>Resolução

Se você estiver usando DNS personalizado, certifique-se de que o servidor DNS esteja acessível a partir da região de recuperação de desastres.

Para verificar se a VM usa uma configuração de DNS personalizada:

1. Abra **máquinas virtuais** e selecione a VM.
1. Navegue até as **Configurações** das VMs e selecione **Rede**.
1. Em **Rede/sub-rede Virtual,** selecione o link para abrir a página de recursos da rede virtual.
1. Vá para **Configurações** e selecione **servidores DNS**.

Tente acessar o servidor DNS da máquina virtual. Se o servidor DNS não estiver acessível, torne-o acessível falhando no servidor DNS ou criando a linha de site entre a rede DR e o DNS.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-erro":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: falha na configuração do Azure Site Recovery (151196)

> [!NOTE]
> Se as VMs estiverem por trás de um balanceador de carga interna **Padrão,** por `login.microsoftonline.com`padrão, ele não teria acesso aos IPs do Office 365, tais como . Altere-o para o tipo de balanceador de carga interna **básico** ou crie acesso de saída como mencionado no artigo [Configure regras de balanceamento de carga e saída no Standard Load Balancer usando o Azure CLI](/azure/load-balancer/configure-load-balancer-outbound-cli).

#### <a name="possible-cause"></a>Possível causa

Uma conexão não pode ser estabelecida para os pontos finais de autenticação e identidade IP4 do Office 365.

#### <a name="resolution"></a>Resolução

- A Recuperação do Site do Azure requer acesso às faixas IP do Office 365 para autenticação.
- Se você estiver usando as regras do Grupo de Segurança da Rede Azure (NSG) /proxy de firewall para controlar a conectividade de rede de saída na VM, certifique-se de permitir a comunicação com as faixas IP do Office 365. Crie uma regra NSG baseada em tag de [serviço Azure Active Directory (Azure AD)](/azure/virtual-network/security-overview#service-tags) que permite o acesso a todos os endereços IP correspondentes ao Azure AD.
- Se novos endereços forem adicionados ao Azure AD no futuro, você precisará criar novas regras do NSG.

### <a name="example-nsg-configuration"></a>Exemplo de Configuração do NSG

Este exemplo mostra como configurar regras de NSG para uma VM a ser replicada.

- Se você estiver usando as regras do NSG para controlar a conectividade de saída, use Permitir regras **de saída HTTPS** para a porta 443 para todas as faixas de endereço IP necessárias.
- O exemplo pressupõe que a localização de origem da VM é **o Leste dos EUA** e o local de destino é o Central **US**.

#### <a name="nsg-rules---east-us"></a>Regras de NSG – Leste dos EUA

1. Crie uma regra de segurança de saída HTTPS para o NSG, conforme mostrado na captura de tela a seguir. Este exemplo usa a **tag de serviço Destino**: _Storage.EastUS_ e **Destination port ranges**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="storage-tag":::

1. Crie uma regra de segurança de saída HTTPS para o NSG, conforme mostrado na captura de tela a seguir. Este exemplo usa a **tag de serviço Destino**: _AzureActiveDirectory_ e **Destination port ranges**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tag":::

1. Crie as regras de saída da porta HTTPS 443 para os IPs de recuperação de site que correspondem ao local de destino:

   | **Localidade** | **Endereço IP do Site Recovery** |  **Endereço IP de monitoramento do Site Recovery** |
   | --- | --- | --- |
   | Centro dos EUA | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>Regras de NSG – EUA Central

Para este exemplo, essas regras do NSG são necessárias para que a replicação possa ser ativada da região de destino para a região de origem após o failover:

1. Crie uma regra de segurança de saída HTTPS para _storage.CentralUS_:

   - **Tag de serviço de destino**: _Armazenamento.CentralUS_
   - **Faixas portuárias de**destino : _443_

1. Crie uma regra de segurança de saída HTTPS para _O AzureActiveDirectory_.

   - **Tag de serviço de destino**: _AzureActiveDirectory_
   - **Faixas portuárias de**destino : _443_

1. Crie as regras de saída da porta HTTPS 443 para os IPs de recuperação de site que correspondem ao local de origem:

   |**Localidade** | **Endereço IP do Site Recovery** |  **Endereço IP de monitoramento do Site Recovery** |
   | --- | --- | --- |
   | Leste dos EUA | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: falha na configuração do Site Recovery (151197)

#### <a name="possible-cause"></a>Possível causa

Uma conexão não pode ser estabelecida aos pontos finais do serviço de recuperação do site do Azure.

#### <a name="resolution"></a>Resolução

O Azure Site Recovery requer acesso para a [intervalos de IP de recuperação de Site](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) dependendo da região. Certifique-se de que as faixas IP necessárias estão acessíveis a partir da VM.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problema 4: Falha na replicação do Azure-to-Azure quando o tráfego de rede passa pelo servidor proxy no local (151072)

#### <a name="possible-cause"></a>Possível causa

As configurações de proxy personalizadas são inválidas e o agente de serviço de mobilidade de recuperação do site do Azure não detectou automaticamente as configurações de proxy do Internet Explorer (IE).

#### <a name="resolution"></a>Resolução

1. O agente de serviço mobility detecta as configurações de proxy do IE no Windows e `/etc/environment` no Linux.
1. Se você preferir definir proxy apenas para o serviço azure Site Recovery Mobility, você pode fornecer os detalhes do proxy no _ProxyInfo.conf_ localizado em:

   - **Linux**:`/usr/local/InMage/config/`
   - **Janelas:**`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. O _ProxyInfo.conf_ deve ter as configurações de proxy no seguinte formato _INI:_

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

1. O agente de serviço de mobilidade de recuperação do site do Azure suporta apenas **proxies não autenticados**.

### <a name="fix-the-problem"></a>Corrija o problema

Para permitir [os URLs necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou as [faixas de IP necessárias,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)siga as etapas do [documento de orientação de rede](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="next-steps"></a>Próximas etapas

[Replicar as máquinas virtuais do Azure](site-recovery-replicate-azure-to-azure.md)
