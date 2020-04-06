---
title: Solucionar problemas de replicação de VMs do Azure com recuperação do site do Azure
description: Replicação de solução de problemas na recuperação de desastres da Azure VM com a recuperação do site do Azure
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: 8cba02d3c7d1e649853570b199b646b1c4dcce2d
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667410"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Replicação de solução de problemas na recuperação de desastres da Azure VM

Este artigo descreve problemas comuns no Azure Site Recovery quando você está replicando e recuperando máquinas virtuais (VM) do Azure de uma região para outra região. Também explica como solucionar os problemas comuns. Para obter mais informações sobre configurações com suporte, consulte a [matriz de suporte para replicar máquinas virtuais do Azure](site-recovery-support-matrix-azure-to-azure.md).

O Azure Site Recovery replica consistentemente dados da região de origem para a região de recuperação de desastres. Ele também cria um ponto de recuperação consistente com falhas a cada 5 minutos. Se o Site Recovery não puder criar pontos de recuperação durante 60 minutos, ele alertará você com estas informações:

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

As seções a seguir descrevem as causas e soluções.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>Alta taxa de alteração de dados na máquina virtual de origem

O Azure Site Recovery cria um evento se a taxa de alteração de dados na máquina virtual de origem for maior do que os limites suportados. Para ver se o problema é por causa da alta rotatividade, vá para **os itens** > Replicados**VM** > **Events - duração de 72 horas**.
Você deve ver a taxa de alteração de dados do evento **além dos limites suportados:**

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="A página de recuperação do site do Azure que mostra uma alta taxa de alteração de dados que é muito alta.":::

Se você selecionar o evento, deverá ver as informações exatas do disco:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="Página que mostra os detalhes do evento da taxa de alteração de dados.":::

### <a name="azure-site-recovery-limits"></a>Limites da Azure Site Recovery

A tabela a seguir fornece os limites do Azure Site Recovery. Esses limites são baseados em nossos testes, mas eles não podem cobrir todas as combinações possíveis de entrada e saída de aplicativo (I/O). Os resultados reais podem variar dependendo da combinação de E/S do aplicativo.

Há dois limites a considerar: churn de dados por disco e churn de dados por máquina virtual. Vejaos o disco Premium P20 na tabela a seguir, por exemplo. Para uma única VM, a Recuperação do Site pode lidar com 5 MB/s de churn por disco com um máximo de cinco discos. A Recuperação do Site tem um limite de 25 MB/s de churn total por VM.

**Alvo de armazenamento de replicação** | **Tamanho médio da E/S do disco de origem** |**Rotatividade média dos dados para o disco de origem** | **Rotatividade total dos dados por dia para o disco de dados de origem**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior |20 MB/s | 1684 GB por disco

### <a name="solution"></a>Solução

O Azure Site Recovery tem limites para as taxas de alteração de dados, dependendo do tipo de disco. Para ver se esse problema é recorrente ou temporário, encontre a taxa de alteração de dados da máquina virtual afetada. Acesse a máquina virtual de origem, localize as métricas em **Monitoramento** e adicione-as conforme mostrado nesta captura de tela:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="Página que mostra o processo de três etapas para encontrar a taxa de alteração de dados.":::

1. Selecione **Adicionar métrica** e adicione **Bytes de gravação de disco do sistema operacional/s** e **Bytes de gravação de disco de dados/s**.
1. Monitore o pico, conforme mostrado na captura de tela.
1. Exiba as operações de gravação totais que estão acontecendo nos discos do sistema operacional e em todos os discos de dados combinados. Essas métricas talvez não forneçam informações por disco, mas elas indicam o padrão total da rotatividade de dados.

Um aumento na taxa de mudança de dados pode vir de uma explosão de dados ocasional. Se a taxa de alteração de dados for superior a 10 MB/s (para Premium) ou 2 MB/s (para Padrão) e descer, a replicação será catch up. Se o churn estiver consistentemente bem além do limite suportado, considere uma dessas opções:

- Exclua o disco que está causando uma alta taxa de alteração de dados: primeiro, desative a replicação. Em seguida, você pode excluir o disco usando [PowerShell](azure-to-azure-exclude-disks.md).
- Alterar o nível do disco de armazenamento de recuperação de desastres: Essa opção só é possível se o churn de dados de disco for inferior a 20 MB/s. Por exemplo, uma VM com um disco P10 tem um churn de dados superior a 8 MB/s, mas menos de 10 MB/s. Se o cliente puder usar um disco P30 para o armazenamento de destino durante a proteção, o problema poderá ser resolvido. Esta solução só é possível para máquinas que estão usando Discos Gerenciados Premium. Siga estas etapas:

  1. Vá para **Discos** da máquina replicada afetada e copie o nome do disco da réplica.
  1. Vá para esta réplica do disco gerenciado.
  1. Você pode ver um banner em **Visão Geral** que diz que uma URL SAS foi gerada. Selecione este banner e cancele a exportação. Ignore este passo se você não ver o banner.
  1. Assim que a URL do SAS for revogada, vá para **Configuração** para o disco gerenciado. Aumente o tamanho para que a recuperação do site suporte a taxa de churn observada no disco de origem.

## <a name="network-connectivity-problems"></a>Problemas de conectividade de rede

### <a name="network-latency-to-a-cache-storage-account"></a>Latência de rede para uma conta de armazenamento em cache

O Site Recovery envia dados replicados à conta de armazenamento em cache. Você pode experimentar latência de rede se o upload dos dados de uma máquina virtual para a conta de armazenamento de cache for mais lento do que 4 MB em 3 segundos.

Para verificar se há um problema relacionado à latência, use [a AzCopy](/azure/storage/common/storage-use-azcopy). Você pode usar este utilitário de linha de comando para carregar dados da máquina virtual para a conta de armazenamento de cache. Se a latência estiver alta, verifique se você está usando um aparelho virtual de rede (NVA) para controlar o tráfego de rede de saída das VMs. O appliance pode ser acelerado se todo o tráfego de replicação passar pelo NVA.

Recomendamos criar um ponto de extremidade de serviço de rede em sua rede virtual para "Armazenamento", para que o tráfego da replicação não acesse a NVA. Para saber mais, confira [Network virtual appliance configuration](azure-to-azure-about-networking.md#network-virtual-appliance-configuration) (Configuração da solução de virtualização de rede).

### <a name="network-connectivity"></a>Conectividade de rede

Para que a replicação de recuperação de site funcione, ela precisa que a VM forneça conectividade de saída a URLs ou faixas IP específicas. Você pode ter sua VM por trás de um firewall ou usar regras do Grupo de Segurança de Rede (NSG) para controlar a conectividade de saída. Se assim for, você pode ter problemas. Para garantir que todas as URLs estejam conectadas, consulte [conectividade outbound para URLs](azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>Erro ID 153006 - Nenhum ponto de recuperação consistente com o aplicativo disponível para a VM nos últimos minutos "X"

A seguir estão alguns dos problemas mais comuns.

### <a name="known-issue-in-sql-server-20082008-r2"></a>Problema conhecido no servidor SQL 2008/2008 R2

**Como corrigir:** Há um problema conhecido com o servidor SQL 2008/2008 R2. Consulte o artigo [Azure Site Recovery Agent ou outrafalha de backup VSS não componente para um servidor que hospeda o SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Os trabalhos de recuperação do Site do Azure falham em servidores que hospedam qualquer versão de instâncias do SQL Server com AUTO_CLOSE DBs

**Como corrigir:** Consulte o artigo [Backups VSS não componentes, como o Azure Site Recovery, falham em servidores que hospedam instâncias do SQL Server com AUTO_CLOSE DBs](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).

### <a name="known-issue-in-sql-server-2016-and-2017"></a>Problema conhecido em SQL Server 2016 e 2017

**Como corrigir**: Consulte o artigo [Atualização Cumulativa 16 para SQL Server 2017](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017).

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Você está usando a configuração direta dos espaços de armazenamento do Azure

**Como corrigir**: A recuperação do site do Azure não pode criar um ponto de recuperação consistente do aplicativo para a configuração direta de espaços de armazenamento. [Configure a diretiva de replicação](azure-to-azure-how-to-enable-replication-s2d-vms.md).

### <a name="more-causes-because-of-vss-related-issues"></a>Mais causas por causa de problemas relacionados ao VSS:

Para solucionar ainda mais os problemas, verifique os arquivos na máquina de origem para obter o código exato de erro para falha:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

Para localizar os erros, abra o arquivo _vacp.log_ em uma pesquisa do editor de texto para a seqüência **vacpError**.

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

No exemplo anterior, **2147754994** é o código de erro que informa sobre a falha após esta frase.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>O escritor VSS não está instalado - Erro 2147221164

**Como corrigir**: Para gerar a tag de consistência do aplicativo, o Azure Site Recovery usa o Volume Shadow Copy Service (VSS). A Recuperação do Site instala um provedor VSS para sua operação para tirar instantâneos de consistência do aplicativo. O Azure Site Recovery instala este Provedor VSS como um serviço. Se o provedor VSS não estiver instalado, a criação do snapshot de consistência do aplicativo falhará. Ele mostra o **erro ID 0x80040154 Classe não registrada**. Consulte o artigo para [solução de problemas de instalação de redação VSS](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>O escritor VSS está desativado - Erro 2147943458

**Como corrigir**: Para gerar a tag de consistência do aplicativo, o Azure Site Recovery usa VSS. A Recuperação do Site instala um provedor VSS para sua operação para tirar instantâneos de consistência do aplicativo. Este provedor VSS está instalado como um serviço. Se você não tiver o serviço do Provedor VSS ativado, a criação do snapshot de consistência do aplicativo falhará. Mostra o erro: **O serviço especificado está desativado e não pode ser iniciado (0x80070422)**.

Se o VSS estiver desativado:

- Verifique se o tipo de inicialização do serviço do provedor VSS está definido como **Automático**.
- Reinicie os seguintes serviços:
  - Serviço VSS.
  - Provedor de recuperação de site do Azure VSS.
  - Serviço VDS.

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - Erro 2147754756

**Como corrigir**: Para gerar a tag de consistência do aplicativo, o Azure Site Recovery usa VSS. Verifique se o serviço de provedor VSS de recuperação do site do Azure está instalado.

Use os seguintes comandos para reinstalar o provedor VSS:

1. Desinstalar o provedor existente:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. Reinstalar o provedor VSS:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Verifique se o tipo de inicialização do serviço do provedor VSS está definido como **Automático**.

Reinicie os seguintes serviços:

- Serviço VSS.
- Provedor de recuperação de site do Azure VSS.
- Serviço VDS.
