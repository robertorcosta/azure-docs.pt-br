---
title: Usando contas de armazenamento gerenciadas pelo cliente no Azure Monitor Log Analytics
description: Use sua própria conta de armazenamento para cenários de Log Analytics
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: 0a2439f0ed18cf93691a1d0389e049b1b7993d93
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97732046"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Usando contas de armazenamento gerenciadas pelo cliente no Azure Monitor Log Analytics

Log Analytics se baseia no armazenamento do Azure em uma variedade de cenários. Esse uso normalmente é gerenciado automaticamente. No entanto, alguns casos exigem que você forneça e gerencie sua própria conta de armazenamento, também conhecida como conta de armazenamento gerenciada pelo cliente. Este documento detalha o uso do armazenamento gerenciado pelo cliente para a ingestão de logs do WAD/LAD, cenários específicos de link privado e criptografia CMK (chave gerenciada pelo cliente). 

> [!NOTE]
> É recomendável que você não faça uma dependência do conteúdo Log Analytics carregar no armazenamento gerenciado pelo cliente, Considerando que a formatação e o conteúdo podem ser alterados.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Ingerir Diagnóstico do Azure logs de extensão (WAD/LAD)
Os agentes de extensão de Diagnóstico do Azure (também chamados de WAD e LAD para agentes do Windows e Linux, respectivamente) coletam vários logs do sistema operacional e os armazenam em uma conta de armazenamento gerenciada pelo cliente. Em seguida, você pode ingerir esses logs em Log Analytics para revisá-los e analisá-los.
Como coletar Diagnóstico do Azure logs de extensão de sua conta de armazenamento Conecte a conta de armazenamento ao seu espaço de trabalho do Log Analytics como uma fonte de dados de armazenamento usando [o portal do Azure](./diagnostics-extension-logs.md#collect-logs-from-azure-storage) ou chamando a [API do Storage insights](/rest/api/loganalytics/storage%20insights/createorupdate).

Tipos de dados com suporte:
* syslog
* Eventos do Windows
* Service Fabric
* Eventos ETW
* Logs IIS

## <a name="using-private-links"></a>Usando links privados
Contas de armazenamento gerenciadas pelo cliente são necessárias em alguns casos de uso, quando links privados são usados para se conectar a recursos de Azure Monitor. Um desses casos é a ingestão de logs personalizados ou logs do IIS. Esses tipos de dados são carregados primeiro como BLOBs para uma conta intermediária de armazenamento do Azure e, em seguida, ingeridos em um espaço de trabalho. Da mesma forma, algumas soluções Azure Monitor podem usar contas de armazenamento para armazenar arquivos grandes, como a central de segurança do Azure (ASC), que pode precisar carregar arquivos. 

##### <a name="private-link-scenarios-that-require-a-customer-managed-storage"></a>Cenários de link privado que exigem um armazenamento gerenciado pelo cliente
* Ingestão de logs personalizados e logs do IIS
* Permitindo que a solução ASC carregue arquivos

### <a name="how-to-use-a-customer-managed-storage-account-over-a-private-link"></a>Como usar uma conta de armazenamento gerenciada pelo cliente em um link privado
##### <a name="workspace-requirements"></a>Requisitos do espaço de trabalho
Ao se conectar a Azure Monitor por meio de um link privado, os agentes de Log Analytics só podem enviar logs para espaços de trabalho vinculados à sua rede por meio de um link privado. Essa regra requer que você configure corretamente um Azure Monitor objeto de escopo de link privado (AMPLS), conecte-o aos seus espaços de trabalho e, em seguida, conecte o AMPLS à sua rede por meio de um link privado. Para obter mais informações sobre o procedimento de configuração AMPLS, consulte [usar o link privado do Azure para conectar redes com segurança a Azure monitor](./private-link-security.md). 
##### <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento
Para que a conta de armazenamento se conecte com êxito ao seu link privado, ela deve:
* Estar localizado em sua VNet ou em uma rede emparelhada e conectado à sua VNet por meio de um link privado. Isso permite que os agentes em sua VNet enviem logs para a conta de armazenamento.
* Estar localizado na mesma região que o espaço de trabalho ao qual ele está vinculado.
* Permitir que Azure Monitor acesse a conta de armazenamento. Se você optar por permitir apenas selecionar redes para acessar sua conta de armazenamento, deverá selecionar a exceção: "permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento".
![Imagem de serviços de confiança da conta de armazenamento MS](./media/private-storage/storage-trust.png)
* Se o seu espaço de trabalho tratar o tráfego de outras redes também, você deverá configurar a conta de armazenamento para permitir o tráfego de entrada proveniente das redes/Internet relevantes.

##### <a name="link-your-storage-account-to-a-log-analytics-workspace"></a>Vincular sua conta de armazenamento a um espaço de trabalho Log Analytics
Você pode vincular sua conta de armazenamento ao espaço de trabalho por meio do [CLI do Azure](/cli/azure/monitor/log-analytics/workspace/linked-storage) ou da [API REST](/rest/api/loganalytics/linkedstorageaccounts). Valores de DataSourceType aplicáveis:
* CustomLogs – para usar o armazenamento para logs personalizados e logs do IIS durante a ingestão.
* AzureWatson – use o armazenamento para arquivos carregados pela solução de ASC (central de segurança do Azure). Para obter mais informações sobre como gerenciar a retenção, substituir uma conta de armazenamento vinculada e monitorar a atividade da conta de armazenamento, consulte [Gerenciando contas de armazenamento vinculadas](#managing-linked-storage-accounts). 

## <a name="encrypting-data-with-cmk"></a>Criptografando dados com CMK
O armazenamento do Azure criptografa todos os dados em repouso em uma conta de armazenamento. Por padrão, ele criptografa dados com chaves gerenciadas pela Microsoft (MMK). No entanto, o armazenamento do Azure permitirá que você use uma chave gerenciada pelo cliente (CMK) do Azure Key Vault para criptografar seus dados de armazenamento. Você pode importar suas próprias chaves para Azure Key Vault ou pode usar as APIs de Azure Key Vault para gerar chaves.
##### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>Cenários CMK que exigem uma conta de armazenamento gerenciada pelo cliente
* Criptografando consultas de alerta de log com CMK
* Criptografando consultas salvas com CMK

### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Como aplicar CMK a contas de armazenamento gerenciadas pelo cliente
##### <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento
A conta de armazenamento e o cofre de chaves devem estar na mesma região, mas podem estar em assinaturas diferentes. Para obter mais informações sobre criptografia de armazenamento do Azure e gerenciamento de chaves, consulte [criptografia de armazenamento do Azure para dados em repouso](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Aplicar CMK às suas contas de armazenamento
Para configurar sua conta de armazenamento do Azure para usar chaves gerenciadas pelo cliente com Azure Key Vault, use o [portal do Azure](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), o [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou a [CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="managing-linked-storage-accounts"></a>Gerenciando contas de armazenamento vinculadas

Para vincular ou desvincular contas de armazenamento ao seu espaço de trabalho, use o [CLI do Azure](/cli/azure/monitor/log-analytics/workspace/linked-storage) ou a [API REST](/rest/api/loganalytics/linkedstorageaccounts).

##### <a name="create-or-modify-a-link"></a>Criar ou modificar um link
Quando você vincula uma conta de armazenamento a um espaço de trabalho, Log Analytics começará a usá-la em vez da conta de armazenamento de Propriedade do serviço. É possível 
* Registrar várias contas de armazenamento para distribuir a carga de logs entre elas
* Reutilizar a mesma conta de armazenamento para vários espaços de trabalho

##### <a name="unlink-a-storage-account"></a>Desvincular uma conta de armazenamento
Para parar de usar uma conta de armazenamento, desvincule o armazenamento do espaço de trabalho. Desvincular todas as contas de armazenamento de um espaço de trabalho significa Log Analytics tentará confiar em contas de armazenamento gerenciadas pelo serviço. Se sua rede tiver acesso limitado à Internet, esses armazenamentos poderão não estar disponíveis e qualquer cenário que dependa do armazenamento falhará.

##### <a name="replace-a-storage-account"></a>Substituir uma conta de armazenamento
Para substituir uma conta de armazenamento usada para ingestão,
1.  **Crie um link para uma nova conta de armazenamento.** Os agentes de registro em log receberão a configuração atualizada e começarão a enviar dados para o novo armazenamento também. O processo pode levar alguns minutos.
2.  **Desvincule a conta de armazenamento antiga para que os agentes parem de gravar na conta removida.** O processo de ingestão continua lendo os dados dessa conta até que eles sejam todos ingeridos. Não exclua a conta de armazenamento até ver que todos os logs foram ingeridos.

### <a name="maintaining-storage-accounts"></a>Mantendo contas de armazenamento
##### <a name="manage-log-retention"></a>Gerenciar retenção de log
Ao usar sua própria conta de armazenamento, a retenção cabe a você. Em outras palavras, Log Analytics não exclui os logs armazenados no seu armazenamento privado. Em vez disso, você deve configurar uma política para lidar com a carga de acordo com suas preferências.

##### <a name="consider-load"></a>Considerar a carga
As contas de armazenamento podem lidar com uma determinada carga de solicitações de leitura e gravação antes de iniciarem solicitações de limitação (consulte [escalabilidade e metas de desempenho para o armazenamento de BLOBs](../../storage/common/scalability-targets-standard-account.md) para obter mais detalhes). A limitação afeta o tempo necessário para a ingestão de logs. Se sua conta de armazenamento estiver sobrecarregada, registre uma conta de armazenamento adicional para distribuir a carga entre elas. Para monitorar a capacidade e o desempenho da sua conta de armazenamento, veja suas [informações no portal do Azure]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Cobranças relacionadas
As contas de armazenamento são cobradas pelo volume de dados armazenados, o tipo de armazenamento e o tipo de redundância. Para obter detalhes, confira [Preços de blobs de blocos](https://azure.microsoft.com/pricing/details/storage/blobs) e [Preços do Armazenamento de Tabelas](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o link privado do Azure para conectar redes com segurança a Azure monitor](private-link-security.md)
- Saiba mais sobre [Azure monitor chaves gerenciadas pelo cliente](customer-managed-keys.md)
