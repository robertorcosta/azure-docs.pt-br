---
title: Como excluir e exportar dados pessoais do Azure DevTest Labs
description: Saiba como excluir e exportar dados pessoais do serviço Azure DevLast Labs para dar suporte às obrigações em conformidade com o GDPR (Regulamento Geral sobre a Proteção de Dados).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c87e2fb534480bbf9bbe625d67782e5a11eda18c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169692"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exportar ou excluir dados pessoais do Azure DevTest Labs
Este artigo fornece etapas para excluir e exportar dados pessoais do serviço Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Quais dados pessoais o DevTest Labs coleta?
O DevTest Labs coleta duas partes principais de dados pessoais do usuário. São elas: o endereço de email do usuário e a ID de objeto de usuário. Essas informações são fundamentais para que o serviço forneça recursos em serviço a administradores de laboratório e usuários de laboratório.

### <a name="user-email-address"></a>Endereço de email do usuário
O DevTest Labs usa o endereço de email do usuário para enviar notificações de email de desligamento automático para usuários do laboratório. O email notifica os usuários do computador sendo desligado. Os usuários podem adiar ou ignorar o desligamento, se quiserem fazer isso. Você configura o endereço de email no nível de laboratório ou no nível de VM.

**Configurar email no laboratório:**

![Configurar email no nível de laboratório](./media/personal-data-delete-export/lab-user-email.png)

**Configurar email na VM:**

![Configurar email no nível de VM](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ID de objeto de usuário
O DevTest Labs usa a ID de objeto de usuário para mostrar as tendências de custo mês a mês e o custo das informações do recurso aos administradores de laboratório. Isso permite controlar custos e gerenciar limites do Lab. 

**Tendência de custo estimado para o mês calendário atual:**
![Tendência de custo estimado para o mês do calendário atual](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Custo estimado mês a mês por recurso:**
![Custo estimado mês a mês por recurso](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Por que esses dados pessoais são necessários?
O serviço do DevTest Labs usa os dados pessoais para fins operacionais. Esses dados são fundamentais para que o serviço forneça recursos importantes. Se você definir uma política de retenção no endereço de email do usuário, os usuários do laboratório não receberão notificações por email de desligamento automático após o endereço de email ser excluído do sistema. Da mesma forma, o administrador de laboratório não poderá exibir as tendências de custo mês a mês e o custo por recurso dos computadores nos laboratórios, se as IDs de objeto de usuário forem excluídas com base em uma política de retenção. Portanto, esses dados precisam ser retidos enquanto o recurso do usuário estiver ativo no Lab.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Como é possível excluir os dados pessoais do sistema?
Como usuário de laboratório, se você quiser excluir esses dados pessoais, poderá excluí-los do Lab. O serviço DevTest Labs tornará os dados pessoais excluídos anônimos, 30 dias após terem sido excluídos pelo usuário.

Por exemplo, se você excluir a VM ou remover o endereço de email, o serviço DevTest Labs tornará esses dados anônimos 30 dias após o recurso ser excluído. A política de retenção de 30 dias após a exclusão é garantir que forneça uma projeção de custos de mês a mês precisa ao administrador de laboratório.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Como é possível solicitar uma exportação nos dados pessoais?
Você pode exportar dados pessoais e de uso de laboratório usando o portal Azure ou powerShell. Os dados são exportados como dois arquivos CSV diferentes:

- **disks.csv** - contém informações sobre os discos que estão sendo usados pelas diferentes VMs
- **virtualmachines.csv** - contém informações sobre as VMs no laboratório.

### <a name="azure-portal"></a>Portal do Azure
Como usuário de laboratório, você pode solicitar uma exportação nos dados pessoais armazenados pelo serviço DevTest Labs. Para solicitar uma exportação, navegue até a opção **Dados pessoais** na página de **Visão geral** do laboratório. Selecione o botão **Solicitar exportação** para iniciar a criação de um arquivo do Excel para download na conta de armazenamento do administrador do Lab. Você pode contatar o administrador do laboratório para exibir esses dados.

1. Selecione **Dados pessoais** no menu esquerdo. 

    ![Página de dados pessoais](./media/personal-data-delete-export/personal-data-page.png)
2. Selecione o **grupo de recursos** que contém o laboratório.

    ![Escolha o grupo de recursos](./media/personal-data-delete-export/select-resource-group.png)
3. Selecione a **conta de armazenamento** no grupo de recursos.
4. Na página **Conta de armazenamento**, selecione **Blobs**.

    ![Selecionar o bloco Blobs](./media/personal-data-delete-export/select-blobs-tile.png)
5. Selecione o contêiner nomeado **labresourceusage** na lista de contêineres.

    ![Selecionar o contêiner de blob](./media/personal-data-delete-export/select-blob-container.png)
6. Selecione a **pasta** com o nome do laboratório. Você localizará arquivos **csv** para **discos** e **máquinas virtuais** do laboratório nesta pasta. É possível baixar esses arquivos csv, filtrar o conteúdo para o usuário de laboratório que estiver solicitando acesso e compartilhá-los.

    ![Baixar arquivo CSV](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

Os principais componentes da amostra acima são:

- O comando Invoke-AzureRmResourceAction.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Dois parâmetros de ação
    - **blobStorageAbsoluteSasUri** - A conta de armazenamento URI com o token SAS (SAS) de assinatura de acesso compartilhado. No script PowerShell, esse valor poderia ser passado em vez da chave de armazenamento.
    - **useStartDate** - A data inicial para puxar dados, com a data final sendo a data atual em que a ação é executada. A granularidade está no nível do dia, então mesmo se você adicionar informações de tempo, ela será ignorada.

### <a name="exported-data---a-closer-look"></a>Dados exportados - um olhar mais atento
Agora vamos dar uma olhada mais de perto nos dados exportados. Como mencionado anteriormente, uma vez que os dados são exportados com sucesso, haverá dois arquivos CSV. 

O **virtualmachines.csv** contém as seguintes colunas de dados:

| Nome da coluna | Descrição |
| ----------- | ----------- | 
| SubscriptionId | O identificador de assinatura em que o laboratório existe. |
| LabUId | Identificador GUID exclusivo para o laboratório. |
| Nome do Laboratório | Nome do laboratório. |
| LabResourceId | ID de recursos de laboratório totalmente qualificado. |
| ResourceGroupName | Nome do grupo de recursos que contém o VM | 
| ResourceId | ID de recurso totalmente qualificado para o VM. |
| UID de recursos | GUID para o VM |
| Nome | Nome da máquina virtual. |
| CreatedTime | A data-hora em que a VM foi criada. |
| DeletedDate | A data-hora em que a VM foi excluída. Se está vazio, a exclusão ainda não ocorreu. |
| Proprietário de recursos | Dono da VM. Se o valor estiver vazio, então é uma VM reivindicada ou criada por um diretor de serviço. |
| PricingTier | Nível de preços da VM |
| Status de recursos | Estado de disponibilidade da VM. Ativo, se ainda existir ou Inativo, se a VM tiver sido excluída. |
| ComputaçãoResourceId | Identificador de recursos de computação de máquina virtual totalmente qualificado. |
| Reclamado | Definido como verdadeiro se a VM é uma VM reivindicável | 
| EnvironmentId | O identificador de recursos do ambiente no qual a máquina Virtual foi criada. Está vazio quando a VM não foi criada como parte de um recurso ambiental. |
| ExpirationDate | Data de validade da VM. Está definido para esvaziar, se uma data de validade não foi definida.
| GalleryImageReferenceVersion |  Versão da imagem base vm. |
| GalleryImageReferenceOferta | Oferta da imagem base VM. |
| GalleryImageReferenceEditor | Editor da imagem base vm. |
| GalleryImageReferenceSku | Sku da imagem base vm |
| GalleryImageReferenceOstype | Tipo de sO da imagem base vm |
| CustomImageId | ID totalmente qualificado da imagem personalizada da base VM. |

As colunas de dados contidas em **disks.csv** estão listadas abaixo:

| Nome da coluna | Descrição | 
| ----------- | ----------- | 
| SubscriptionId | ID da assinatura que contém o laboratório |
| LabUId | GUID para o laboratório |
| Nome do Laboratório | Nome do laboratório | 
| LabResourceId | ID de recurso totalmente qualificado para o laboratório | 
| ResourceGroupName | Nome do grupo de recursos que contém o laboratório | 
| ResourceId | ID de recurso totalmente qualificado para o VM. |
| UID de recursos | GUID para o VM |
 |Nome | O nome do disco anexado |
| CreatedTime |A data e a hora em que o disco de dados foi criado. |
| DeletedDate | A data e a hora em que o disco de dados foi excluído. |
| Status de recursos | Status do recurso. Ativo, se o recurso existir. Inativo, quando excluído. |
| DiskBlobName | Blob nome para o disco de dados. |
| DiskSizeGB | O tamanho do disco de dados. |
| DiskType | Tipo do disco de dados. 0 para Standard, 1 para Premium. |
| LeasedByVmId | ID de recursos da VM ao qual o disco de dados foi anexado. |


> [!NOTE]
> Se você está lidando com vários laboratórios e deseja obter informações gerais, as duas colunas-chave são o **LabUID** e o **ResourceUId**, que são os ids únicos entre as assinaturas.

Os dados exportados podem ser manipulados e visualizados usando ferramentas, como SQL Server, Power BI, etc. Esse recurso é especialmente útil quando você deseja relatar o uso do seu laboratório para sua equipe de gerenciamento que pode não estar usando a mesma assinatura do Azure que você.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir: 

- [Definir políticas para um laboratório](devtest-lab-get-started-with-lab-policies.md)
- [Perguntas frequentes](devtest-lab-faq.md)
