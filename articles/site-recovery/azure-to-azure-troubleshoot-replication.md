---
title: Solucionar problemas de replicação contínua de VMs Azrue com Azure Site Recovery
description: Solucionando erros e problemas durante a replicação de máquinas virtuais do Azure para recuperação de desastre
services: site-recovery
author: carmonmills
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.author: carmonm
ms.openlocfilehash: b738ffc36334fc540582ba29e803eb2790e2119e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930736"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Solucionar problemas em andamento na replicação de VM do Azure para Azure

Este artigo descreve problemas comuns no Azure Site Recovery quando você está replicando e recuperando máquinas virtuais do Azure de uma região para outra. Ele também explica como solucioná-los. Para obter mais informações sobre configurações com suporte, consulte a [matriz de suporte para replicar máquinas virtuais do Azure](site-recovery-support-matrix-azure-to-azure.md).

O Azure Site Recovery replica consistentemente os dados da região de origem para a região de recuperação de desastre e cria um ponto de recuperação consistente de falha a cada 5 minutos. Se o Site Recovery não puder criar pontos de recuperação durante 60 minutos, ele alertará você com estas informações:

Mensagem de erro: "nenhum ponto de recuperação consistente com falha disponível para a VM nos últimos 60 minutos".</br>
ID do erro: 153007 </br>

As seções a seguir descrevem as causas e soluções.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Alta taxa de alteração de dados na máquina virtual de origem
O Azure Site Recovery aciona um evento se a taxa de alteração de dados na máquina virtual de origem for maior que os limites com suporte. Para verificar se o problema é devido à alta rotatividade, vá para **Itens replicados** > **VM** > **Eventos – Últimas 72 horas**.
Você deve ver o evento “Taxa de alteração de dados além dos limites com suporte”:

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Se você selecionar o evento, deverá ver as informações exatas do disco:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Limites da Azure Site Recovery
A tabela a seguir fornece os limites do Azure Site Recovery. Esses limites baseiam-se nos nossos testes, mas não podem abranger todas as combinações possíveis de E/S de aplicativos. Os resultados reais podem variar dependendo da combinação de E/S do aplicativo. 

Há dois limites a serem considerados, rotatividade dos dados por disco e rotatividade dos dados por máquina virtual. Por exemplo, vamos examinar o disco Premium P20 na tabela a seguir. O Site Recovery pode lidar com 5 MB/s de rotatividade por disco com, no máximo, cinco desses discos por VM, devido ao limite de 25 MB/s de rotatividade total por VM.

**Destino de armazenamento de replicação** | **Tamanho médio da E/S do disco de origem** |**Rotatividade média dos dados para o disco de origem** | **Rotatividade total dos dados por dia para o disco de dados de origem**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior |10 MB/s | 842 GB por disco

### <a name="solution"></a>Solução
O Azure Site Recovery tem limites de taxa de alteração de dados com base no tipo de disco. Para saber se esse problema é recorrente ou momentâneo, localize a taxa de alteração de dados da máquina virtual afetada. Acesse a máquina virtual de origem, localize as métricas em **Monitoramento** e adicione-as conforme mostrado nesta captura de tela:

![Processo de três etapas para localizar a taxa de alteração de dados](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Selecione **Adicionar métrica** e adicione **Bytes de gravação de disco do sistema operacional/s** e **Bytes de gravação de disco de dados/s**.
2. Monitore o pico, conforme mostrado na captura de tela.
3. Exiba as operações de gravação totais que estão acontecendo nos discos do sistema operacional e em todos os discos de dados combinados. Essas métricas talvez não forneçam informações por disco, mas elas indicam o padrão total da rotatividade de dados.

Se um pico for decorrente de uma intermitência de dados ocasional e a taxa de alteração de dados for superior a 10 MB/s (para Premium) e 2 MB/s (para Standard) por algum tempo e cair, a replicação será alcançada. No entanto, se a rotatividade estiver além do limite com suporte na maioria das vezes, considere uma das opções abaixo se possível:

* **Exclua o disco que está causando uma alta taxa de alteração de dados**: você pode excluir o disco usando o [PowerShell](./azure-to-azure-exclude-disks.md). Para excluir o disco, você precisa desabilitar a replicação primeiro. 
* **Alterar a camada do disco de armazenamento de recuperação de desastre**: essa opção só será possível se a variação de dados do disco for menor que 20 MB/s. Digamos que uma VM com um disco P10 está tendo uma rotatividade de dados superior a 8 MB/s, mas inferior a 10 MB/s. Se o cliente puder usar um disco P30 para o armazenamento de destino durante a proteção, o problema poderá ser resolvido. Observe que essa solução só é possível para computadores que usam Managed Disks Premium. Siga as etapas abaixo:
    - Navegue até a folha discos da máquina replicada afetada e copie o nome do disco de réplica
    - Navegar até este disco gerenciado de réplica
    - Você pode ver uma faixa na folha de visão geral dizendo que uma URL SAS foi gerada. Clique nessa faixa e cancele a exportação. Ignore esta etapa se você não vir a faixa.
    - Assim que a URL SAS for revogada, vá para a folha de configuração do disco gerenciado e aumente o tamanho para que a ASR dê suporte à taxa de rotatividade observada no disco de origem

## <a name="Network-connectivity-problem"></a>Problemas de conectividade de rede

### <a name="network-latency-to-a-cache-storage-account"></a>Latência de rede para uma conta de armazenamento em cache
O Site Recovery envia dados replicados à conta de armazenamento em cache. Talvez você veja a latência de rede se o carregamento dos dados de uma máquina virtual para a conta de armazenamento em cache for mais lento do que 4 MB em 3 segundos. 

Para verificar se há um problema relacionado à latência, use [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) para carregar os dados da máquina virtual para a conta de armazenamento em cache. Se a latência for alta, verifique se você está usando um NVA (dispositivo virtual de rede) para controlar o tráfego de rede de saída das VMs. O appliance pode ser acelerado se todo o tráfego de replicação passar pelo NVA. 

Recomendamos criar um ponto de extremidade de serviço de rede em sua rede virtual para "Armazenamento", para que o tráfego da replicação não acesse a NVA. Para saber mais, confira [Network virtual appliance configuration](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration) (Configuração da solução de virtualização de rede).

### <a name="network-connectivity"></a>Conectividade de rede
Para replicação de recuperação de Site para o trabalho, conectividade de saída para intervalos específicos de IP ou URLs é necessária da VM. Se a VM estiver atrás de um firewall ou usar regras NSG (grupo de segurança de rede) para controlar a conectividade de saída, você poderá enfrentar um desses problemas. Para garantir que todas as URLs estão conectadas, confira [Conectividade de saída para URLs do Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>ID do erro 153006-nenhum ponto de recuperação consistente com o aplicativo disponível para a VM nos últimos ' XXX ' minutos

Alguns dos problemas mais comuns estão listados abaixo

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Causa 1: problema conhecido no SQL Server 2008/2008 R2 
**Como corrigir** : há um problema conhecido com o SQL Server 2008/2008 R2. Consulte este artigo da base [de conhecimento Azure site Recovery agente ou outro backup de VSS que não seja de componente falha para um servidor que hospeda SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Causa 2: os trabalhos de Azure Site Recovery falham em servidores que hospedam qualquer versão de instâncias de SQL Server com bancos de AUTO_CLOSE 
**Como corrigir** : consulte o [artigo](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) da base de conhecimento 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Causa 3: problema conhecido em SQL Server 2016 e 2017
**Como corrigir** : consulte o [artigo](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) da base de conhecimento 

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>Causa 4: você está usando a configuração de espaços de armazenamento diretos
**Como corrigir** : Azure site Recovery não é possível criar um ponto de recuperação consistente com o aplicativo para a configuração de espaços de armazenamento diretos. Consulte o artigo para [configurar corretamente a política de replicação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms)

### <a name="more-causes-due-to-vss-related-issues"></a>Mais causas devido a problemas relacionados ao VSS:

Para solucionar os problemas, verifique os arquivos no computador de origem para obter o código de erro exato para a falha:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Como localizar os erros no arquivo?
Procure a cadeia de caracteres "vacpError" abrindo o arquivo vacp. log em um editor
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

No exemplo acima, **2147754994** é o código de erro que informa sobre a falha, conforme mostrado abaixo

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>O gravador VSS não está instalado-erro 2147221164 

*Como corrigir*: para gerar a marca de consistência do aplicativo, Azure site Recovery usa o VSS (serviço de cópias de sombra de volume) da Microsoft. Ele instala um provedor VSS para sua operação para obter instantâneos de consistência do aplicativo. Este provedor VSS é instalado como um serviço. Caso o serviço do provedor do VSS não esteja instalado, a criação do instantâneo de consistência do aplicativo falha com a ID do erro 0x80040154 "classe não registrada". </br>
Consulte o [artigo para solução de problemas de instalação do gravador VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>O gravador VSS está desabilitado-erro 2147943458

**Como corrigir**: para gerar a marca de consistência do aplicativo, Azure site Recovery usa o VSS (serviço de cópias de sombra de volume) da Microsoft. Ele instala um provedor VSS para sua operação para obter instantâneos de consistência do aplicativo. Este provedor VSS é instalado como um serviço. Caso o serviço do provedor do VSS esteja desabilitado, a criação do instantâneo de consistência do aplicativo falha com a ID do erro "o serviço especificado está desabilitado e não pode ser iniciado (0x80070422)". </br>

- Se o VSS estiver desabilitado,
    - Verifique se o tipo de inicialização do serviço do provedor do VSS está definido como **automático**.
    - Reinicie os seguintes serviços:
        - Serviço VSS
        - Provedor de VSS do Azure Site Recovery
        - Serviço VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>PROVEDOR VSS NOT_REGISTERED-erro 2147754756

**Como corrigir**: para gerar a marca de consistência do aplicativo, Azure site Recovery usa o VSS (serviço de cópias de sombra de volume) da Microsoft. Verifique se o serviço do provedor VSS Azure Site Recovery está instalado ou não. </br>

- Repita a instalação do provedor usando os seguintes comandos:
- Desinstalar o provedor existente: C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Uninstall. cmd
- Reinstalar: C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Install. cmd
 
Verifique se o tipo de inicialização do serviço do provedor do VSS está definido como **automático**.
    - Reinicie os seguintes serviços:
        - Serviço VSS
        - Provedor de VSS do Azure Site Recovery
        - Serviço VDS
