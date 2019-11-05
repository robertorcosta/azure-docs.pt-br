---
title: Iniciar um failover de conta de armazenamento (versão prévia) – Armazenamento do Azure
description: Saiba como iniciar um failover de conta caso o ponto de extremidade primário para sua conta de armazenamento não esteja disponível. O failover atualiza a região secundária para torná-la a região primária de sua conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2bac51a86c8acdba0f6c2f03e5a24ab2b133aa8e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521000"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Iniciar um failover de conta de armazenamento (versão prévia)

Se o ponto de extremidade primário para sua conta de armazenamento com redundância geográfica não estiver disponível por algum motivo, você poderá iniciar um failover de conta (versão prévia). Um failover de conta atualiza o ponto de extremidade secundário para torná-lo um ponto de extremidade primário de sua conta de armazenamento. Quando o failover estiver concluído, os clientes poderão começar a gravar na nova região primária. O failover forçado permite manter uma alta disponibilidade para seus aplicativos.

Este artigo mostra como iniciar um failover de conta para sua conta de armazenamento usando o portal do Azure, o PowerShell ou a CLI do Azure. Para saber mais sobre o failover da conta, consulte [Recuperação de desastre e failover de conta (versão prévia) no Armazenamento do Azure](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Um failover de conta normalmente resulta em alguma perda de dados. Para entender as implicações de um failover de conta e para preparar a perda de dados, examine [Entendendo o processo de failover da conta](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar um failover de conta em sua conta de armazenamento, verifique se você executou as seguintes etapas:

- Registre-se para a versão prévia do failover da conta. Para obter informações sobre como se registrar, confira [Sobre a versão prévia](storage-disaster-recovery-guidance.md#about-the-preview).
- Verifique se a conta de armazenamento está configurada para usar GRS (armazenamento com redundância geográfica) ou RA-GRS (armazenamento com redundância geográfica com acesso de leitura). Para obter mais informações sobre o armazenamento com redundância geográfica, consulte [armazenamento com redundância geográfica (GRS): replicação entre regiões para o armazenamento do Azure](storage-redundancy-grs.md). 

## <a name="important-implications-of-account-failover"></a>Implicações importantes de failover da conta

Quando você inicia um failover de conta para sua conta de armazenamento, os registros DNS para o ponto de extremidade secundário são atualizados para que o ponto de extremidade secundário se torne o ponto de extremidade primário. Verifique se você compreende o impacto potencial para sua conta de armazenamento antes de iniciar um failover.

Para estimar a extensão da provável perda de dados antes de iniciar um failover, verifique a propriedade **Hora da Última Sincronização** usando o cmdlet `Get-AzStorageAccount` do PowerShell e inclua o parâmetro `-IncludeGeoReplicationStats`. Em seguida, verifique a propriedade `GeoReplicationStats` para sua conta. 

Após o failover, o tipo de conta de armazenamento é automaticamente convertido em LRS (Armazenamento com Redundância Local) na nova região primária. Você pode reabilitar o GRS (armazenamento com redundância geográfica) ou o RA-GRS (armazenamento com redundância geográfica com acesso de leitura) para a conta. Observe que a conversão de LRS em GRS ou RA-GRS acarreta um custo adicional. Para obter informações adicionais, veja [Detalhes de preço de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

Depois de habilitar novamente o GRS para sua conta de armazenamento, a Microsoft começa a replicar os dados em sua conta para a nova região secundária. O tempo de replicação depende da quantidade de dados sendo replicados.  

## <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Para iniciar um failover da conta do portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento.
2. Em **Configurações**, selecione **Replicação geográfica**. A imagem a seguir mostra o status de replicação geográfica e de failover de uma conta de armazenamento.

    ![Captura de tela mostrando o status de failover e de replicação geográfica](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Verifique se a conta de armazenamento está configurada para GRS (armazenamento com redundância geográfica) ou RA-GRS (armazenamento com redundância geográfica com acesso de leitura). Se não estiver, selecione **Configuração** em **Configurações** para atualizar sua conta, acrescentando redundância geográfica a ela. 
4. A propriedade **Hora da Última Sincronização** indica o atraso do secundário em relação ao primário. A **Hora da Última Sincronização** fornece uma estimativa da extensão da perda de dados que você experimentará após a conclusão do failover.
5. Selecione **Preparar para failover (versão prévia)** . 
6. Revise a caixa de diálogo de confirmação. Quando você estiver pronto, insira **Sim** para confirmar e iniciar o failover.

    ![Caixa de diálogo de confirmação de que mostra a captura de tela de um failover de conta](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para usar o PowerShell para iniciar um failover de conta, você deve primeiro instalar o módulo de versão prévia 6.0.1. Para instalar o módulo, siga estas etapas:

1. Desinstale as instalações anteriores do Azure PowerShell:

    - Remova as instalações anteriores do Azure PowerShell do Windows usando a configuração **Aplicativos e recursos** em **Configurações**.
    - Remova todos os módulos do **Azure** de `%Program Files%\WindowsPowerShell\Modules`.

1. Verifique se tem a versão mais recente do PowerShellGet instalado. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Feche e reabra a janela do PowerShell depois de instalar o PowerShellGet. 

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instalar um módulo de visualização do armazenamento do Azure que dá suporte ao failover de conta:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

1. Feche e reabra a janela do PowerShell.
 
Para iniciar um failover de conta do PowerShell, execute o seguinte comando:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para usar a CLI do Azure para iniciar um failover de conta, execute os seguintes comandos:

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>Próximas etapas

- [Recuperação de desastre e failover de conta (versão prévia) no Armazenamento do Azure](storage-disaster-recovery-guidance.md)
- [Criando aplicativos altamente disponíveis usando RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Tutorial: criar um aplicativo altamente disponível com o armazenamento de BLOBs](../blobs/storage-create-geo-redundant-storage.md) 
