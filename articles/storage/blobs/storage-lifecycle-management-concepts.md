---
title: Gerenciando o ciclo de vida do armazenamento do Azure
description: Aprenda a criar regras de política de ciclo de vida para fazer a transição dos dados antigos para os níveis Hot to Cool e Archive.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/15/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: be5d86fe690d60f687622243a2f1d7771b8af7d0
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603979"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure

Conjuntos de dados têm ciclos de vida exclusivos. No início do ciclo de vida, as pessoas geralmente acessam alguns dados. Mas a necessidade de acesso cai drasticamente à medida que os dados envelhecem. Alguns dados permanecem ociosos na nuvem e raramente são acessados depois de armazenados. Alguns dados expiram dias ou meses após a criação, enquanto outros conjuntos de dados são lidos e modificados ativamente durante seu tempo de vida. O gerenciamento do ciclo de vida do armazenamento de BLOBs do Azure oferece uma política avançada baseada em regras para contas de armazenamento de BLOBs e GPv2. Use a política para fazer a transição de seus dados para as camadas de acesso apropriadas ou expirá-los ao final do ciclo de vida dos dados.

A política de gerenciamento do ciclo de vida permite:

- Fazer a transição dos blobs para uma camada de armazenamento mais esporádico (frequente para esporádico, frequente para arquivos ou esporádico para arquivos), a fim de otimizar o desempenho e os custos
- Excluir os blobs no final dos respectivos ciclos de vida
- Definir regras a serem executadas uma vez por dia no nível da conta de armazenamento
- Aplicar regras a contêineres ou a um subconjunto de BLOBs (usando prefixos de nome ou [marcas de índice de blob](storage-manage-find-blobs.md) como filtros)

Considere um cenário em que os dados recebem acesso frequente durante os estágios iniciais do ciclo de vida, mas apenas ocasionalmente após duas semanas. Após o primeiro mês, o conjunto de dados raramente é acessado. Nesse cenário, o armazenamento frequente é melhor durante os estágios iniciais. O armazenamento frio é mais apropriado para acesso ocasional. O armazenamento de arquivos é a melhor opção de camada depois que os dados ficam em um mês. Ajustando as camadas de armazenamento em relação à idade dos dados, você pode criar as opções de armazenamento menos dispendiosas para suas necessidades. Para conseguir essa transição, as regras de política de gerenciamento do ciclo de vida estão disponíveis para mover os dados antigos para camadas mais frias.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="availability-and-pricing"></a>Disponibilidade e preços

O recurso gerenciamento de ciclo de vida está disponível em todas as regiões do Azure para contas Uso Geral v2 (GPv2), contas de armazenamento de BLOBs e contas de armazenamento de blob de blocos Premium. No portal do Azure, você pode atualizar uma conta de Uso Geral (GPv1) existente para uma conta do GPv2. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).

O recurso de gerenciamento do ciclo de vida é gratuito. Os clientes são cobrados pelo custo de operação regular para as chamadas Set API da [camada de blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) . A operação de exclusão é gratuita. Para obter mais informações sobre preços, confira [Preços do Blob de Blocos](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="add-or-remove-a-policy"></a>Adicionar ou remover uma política

Você pode adicionar, editar ou remover uma política usando qualquer um dos seguintes métodos:

* [Azure portal](https://portal.azure.com)
* [PowerShell do Azure](https://github.com/Azure/azure-powershell/releases)
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [APIs REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Uma política pode ser lida ou gravada por completo. Não há suporte para atualizações parciais. 

> [!NOTE]
> Se você habilitar as regras de firewall para sua conta de armazenamento, as solicitações de gerenciamento do ciclo de vida poderão ser bloqueadas. Você pode desbloquear essas solicitações fornecendo exceções para serviços confiáveis da Microsoft. Para obter mais informações, confira a seção Exceções em [Configurar firewalls e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

Este artigo mostra como gerenciar a política usando os métodos do portal e do PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Há duas maneiras de adicionar uma política por meio do portal do Azure. 

* [Exibição de lista de portal do Azure](#azure-portal-list-view)
* [Exibição de código portal do Azure](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Exibição de lista de portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na portal do Azure, procure e selecione sua conta de armazenamento. 

1. Em **serviço blob**, selecione **Gerenciamento de ciclo de vida** para exibir ou alterar suas regras.

1. Selecione a guia **exibição de lista** .

1. Selecione **Adicionar uma regra** e nomeie sua regra no formulário de **detalhes** . Você também pode definir o **escopo da regra**, o **tipo de blob**e os valores de **subtipo de blob** . O exemplo a seguir define o escopo para filtrar BLOBs. Isso faz com que a guia **conjunto de filtros** seja adicionada.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-details.png" alt-text="Gerenciamento do ciclo de vida adicionar uma página de detalhes de regra no portal do Azure":::

1. Selecione **blobs de base** para definir as condições para a regra. No exemplo a seguir, os BLOBs são movidos para o armazenamento frio, caso não tenham sido modificados por 30 dias.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-base-blobs.png" alt-text="Página blobs de base de gerenciamento do ciclo de vida no portal do Azure":::

   A opção **último acessado** está disponível na visualização nas seguintes regiões:

    - França Central
    - Leste do Canadá
    - Canadá Central

   > [!IMPORTANT]
   > A visualização do último controle de tempo de acesso é para uso de não produção apenas. SLAs (Contratos de Nível de Serviço) não estão disponíveis atualmente.
   
   Para obter mais informações sobre a opção **último acesso** , consulte [mover dados com base na data do último acesso (versão prévia)](#move-data-based-on-last-accessed-date-preview).

1. Se você selecionou **limitar BLOBs com filtros** na página de **detalhes** , selecione **conjunto de filtros** para adicionar um filtro opcional. O exemplo a seguir filtra os BLOBs no contêiner *mylifecyclecontainer* que começam com "log".

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-filter-set.png" alt-text="Página conjunto de filtros de gerenciamento do ciclo de vida no portal do Azure":::

1. Selecione **Adicionar** para adicionar a nova política.

#### <a name="azure-portal-code-view"></a>Exibição de código portal do Azure
1. Entre no [portal do Azure](https://portal.azure.com).

1. Na portal do Azure, procure e selecione sua conta de armazenamento.

1. Em **serviço blob**, selecione **Gerenciamento de ciclo de vida** para exibir ou alterar sua política.

1. O JSON a seguir é um exemplo de uma política que pode ser colada na guia **exibição de código** .

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "mylifecyclecontainer/log"
             ]
           }
         }
       }
     ]
   }
   ```

1. Clique em **Salvar**.

1. Para obter mais informações sobre este exemplo de JSON, consulte as seções [política](#policy) e [regras](#rules) .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O script do PowerShell a seguir pode ser usado para adicionar uma política à sua conta de armazenamento. A variável `$rgname` deve ser inicializada com o nome do grupo de recursos. A variável `$accountName` deve ser inicializada com o nome da conta de armazenamento.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[Modelo](#tab/template)

Você pode definir o gerenciamento do ciclo de vida usando modelos de Azure Resource Manager. Aqui está um modelo de exemplo para implantar uma conta de armazenamento RA-GRS GPv2 com uma política de gerenciamento do ciclo de vida.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

## <a name="policy"></a>Política

Uma política de gerenciamento do ciclo de vida é uma coleção de regras em um documento JSON:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

Uma política é uma coleção de regras:

| Nome do parâmetro | Tipo de parâmetro | Observações |
|----------------|----------------|-------|
| `rules`        | Uma matriz de objetos de regra | Pelo menos uma regra é necessária em uma política. Você pode definir até 100 regras em uma política.|

Cada regra na política tem vários parâmetros:

| Nome do parâmetro | Tipo de parâmetro | Observações | Obrigatório |
|----------------|----------------|-------|----------|
| `name`         | String |Um nome de regra pode incluir até 256 caracteres alfanuméricos. A regra de nome diferencia maiúsculas de minúsculas. Ela deve ser exclusiva em uma política. | Verdadeiro |
| `enabled`      | Booliano | Um booliano opcional para permitir que uma regra seja temporariamente desabilitada. O valor padrão será true se não estiver definido. | Falso | 
| `type`         | Um valor de enumeração | O tipo válido atual é `Lifecycle` . | Verdadeiro |
| `definition`   | Um objeto que define a regra de ciclo de vida | Cada definição é composta por um conjunto de filtros e um conjunto de ações. | Verdadeiro |

## <a name="rules"></a>Regras

Cada definição de regra inclui um conjunto de filtros e um conjunto de ações. O [conjunto de filtros](#rule-filters) limita as ações de regras a determinado conjunto de objetos em um contêiner ou a nomes de objetos. O [conjunto de ações](#rule-actions) aplica-se a camada ou excluir ações para o conjunto filtrado de objetos.

### <a name="sample-rule"></a>Regra de exemplo

A regra de exemplo a seguir filtra a conta para executar as ações em objetos que existem dentro `container1` e começa com `foo` .

>[!NOTE]
>- O gerenciamento do ciclo de vida dá suporte a tipos de blob de bloco e de acréscimo.<br>
>- O gerenciamento do ciclo de vida não afeta os contêineres do sistema como $logs e $web.

- Colocar o blob na camada esporádica 30 dias após a última modificação
- Colocar o blob na camada de arquivos 90 dias após a última modificação
- Excluir o blob 2.555 dias (sete anos) após a última modificação
- Excluir instantâneos de blob 90 dias após a criação do instantâneo

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Filtros de regra

Os filtros limitam as ações de regra a um subconjunto de blobs na conta de armazenamento. Se mais de um filtro for definido, um `AND` lógico será executado em todos os filtros.

Filtros incluem:

| Nome do filtro | Tipo do filtro | Observações | Obrigatório |
|-------------|-------------|-------|-------------|
| blobTypes   | Uma matriz de valores de enumeração predefinidos. | A versão atual dá suporte a `blockBlob` e `appendBlob` . Somente a exclusão tem suporte para o `appendBlob` , não há suporte para a camada de conjunto. | Yes |
| prefixMatch | Uma matriz de cadeias de caracteres para correspondência de prefixos. Cada regra pode definir até 10 prefixos. Uma cadeia de caracteres de prefixo deve começar com um nome de contêiner. Por exemplo, se você quiser corresponder a todos os BLOBs em `https://myaccount.blob.core.windows.net/container1/foo/...` para uma regra, o prefixMatch será `container1/foo` . | Se você não definir prefixMatch, a regra se aplicará a todos os BLOBs na conta de armazenamento. | No |
| blobIndexMatch | Uma matriz de valores de dicionário que consiste na chave de marca de índice de BLOB e condições de valor a serem correspondidas. Cada regra pode definir até 10 condições de marca de índice de BLOB. Por exemplo, se você quiser corresponder a todos os BLOBs com `Project = Contoso` em `https://myaccount.blob.core.windows.net/` para uma regra, o blobIndexMatch será `{"name": "Project","op": "==","value": "Contoso"}` . | Se você não definir blobIndexMatch, a regra se aplicará a todos os BLOBs na conta de armazenamento. | No |

> [!NOTE]
> O índice de blob está em visualização pública e está disponível nas regiões do **Canadá central**, **leste do Canadá**, **França central**e **França** . Para saber mais sobre esse recurso juntamente com limitações e problemas conhecidos, confira [Gerenciar e localizar dados no Armazenamento de Blobs do Azure com o Índice de Blob (versão prévia)](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Ações de regra

As ações são aplicadas aos BLOBs filtrados quando a condição de execução é atendida.

O gerenciamento do ciclo de vida dá suporte a camadas e exclusão de BLOBs e exclusão de instantâneos de BLOB. Defina, pelo menos, uma ação para cada regra em blobs ou instantâneos de blob.

| Ação                      | Blob base                                   | Instantâneo      |
|-----------------------------|---------------------------------------------|---------------|
| tierToCool                  | Dá suporte aos blobs atualmente presentes na camada frequente         | Sem suporte |
| enableAutoTierToHotFromCool | Suporte a BLOBs atualmente na camada fria        | Sem suporte |
| tierToArchive               | Dá suporte aos blobs atualmente presentes na camada frequente ou esporádica | Sem suporte |
| excluir                      | Com suporte para `blockBlob` e `appendBlob`  | Com suporte     |

>[!NOTE]
>Se você definir mais de uma ação no mesmo blob, o gerenciamento do ciclo de vida aplicará a ação mais barata ao blob. Por exemplo, a ação `delete` é mais barata do que a ação `tierToArchive`. A ação `tierToArchive` é mais barata do que a ação `tierToCool`.

As condições de execução se baseiam na idade. Os blobs base usam a hora da última modificação para acompanhar a idade, enquanto os instantâneos de blob usam a hora de criação do instantâneo para executar a mesma tarefa.

| Condição de execução de ação               | Valor de condição                          | Descrição                                                                      |
|------------------------------------|------------------------------------------|----------------------------------------------------------------------------------|
| daysAfterModificationGreaterThan   | Valor inteiro que indica a idade em dias | A condição para ações de blob de base                                              |
| daysAfterCreationGreaterThan       | Valor inteiro que indica a idade em dias | A condição para ações de instantâneo de BLOB                                          |
| daysAfterLastAccessTimeGreaterThan | Valor inteiro que indica a idade em dias | apresentação A condição para ações de blob de base quando a hora do último acesso é habilitada |

## <a name="examples"></a>Exemplos

Os exemplos a seguir demonstram como tratar cenários comuns com as regras de política de ciclo de vida.

### <a name="move-aging-data-to-a-cooler-tier"></a>Mover dados antigos para uma camada mais fria

Este exemplo mostra como fazer a transição de blobs de blocos prefixados com `container1/foo` ou `container2/bar`. A política faz a transição de blobs que não foram modificados há mais de 30 dias para o armazenamento esporádico e de blobs não modificados há mais de 90 dias para a camada de arquivos:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="move-data-based-on-last-accessed-date-preview"></a>Mover dados com base na data do último acesso (versão prévia)

Você pode habilitar o controle de tempo do último acesso para manter um registro de quando seu blob é lido ou gravado pela última vez. Você pode usar a hora do último acesso como um filtro para gerenciar camadas e retenção de seus dados de BLOB.

A opção **último acessado** está disponível na visualização nas seguintes regiões:

 - França Central
 - Leste do Canadá
 - Canadá Central

> [!IMPORTANT]
> A visualização do último controle de tempo de acesso é para uso de não produção apenas. SLAs (Contratos de Nível de Serviço) não estão disponíveis atualmente.

#### <a name="how-last-access-time-tracking-works"></a>Como o controle de tempo do último acesso funciona

Quando o controle de tempo do último acesso é habilitado, a propriedade de blob chamada `LastAccessTime` é atualizada quando um blob é lido ou gravado. Uma operação [obter blob](/rest/api/storageservices/get-blob) é considerada uma operação de acesso. [Obter propriedades de blob](/rest/api/storageservices/get-blob-properties), [obter metadados de blob](/rest/api/storageservices/get-blob-metadata)e [obter marcas de blob](/rest/api/storageservices/get-blob-tags) não são operações de acesso e, portanto, não atualizar a hora do último acesso.

Para minimizar o impacto na latência de acesso de leitura, somente a primeira leitura das últimas 24 horas atualiza o último tempo de acesso. As leituras subsequentes no mesmo período de 24 horas não atualizam o último tempo de acesso. Se um blob for modificado entre leituras, o último tempo de acesso será o mais recente dos dois valores.

No exemplo a seguir, os BLOBs são movidos para o armazenamento frio, caso eles não tenham sido acessados por 30 dias. A `enableAutoTierToHotFromCool` propriedade é um valor booliano que indica se um blob deve ser automaticamente em camadas de frio para quente se ele for acessado novamente depois de ser colocado em camadas para o frio.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

#### <a name="storage-account-support"></a>Suporte da conta de armazenamento

O controle de horário do último acesso está disponível para os seguintes tipos de contas de armazenamento:

 - Contas de armazenamento de uso geral v2
 - Bloquear contas de armazenamento de BLOBs
 - Contas de armazenamento de Blobs

Se sua conta de armazenamento for uma conta v1 de uso geral, use o portal do Azure para atualizar para uma conta v2 de uso geral.

As contas de armazenamento com um namespace hierárquico habilitado para uso com Azure Data Lake Storage Gen2 ainda não têm suporte.

#### <a name="pricing-and-billing"></a>Preços e cobrança

Cada última atualização de hora de acesso é considerada uma [outra operação](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="archive-data-after-ingest"></a>Arquivar dados após a ingestão

Alguns dados permanecem ociosos na nuvem e raramente ou nunca são acessados depois de armazenados. A política de ciclo de vida a seguir é configurada para arquivar dados logo após sua ingestão. Este exemplo faz a transição de blobs de blocos na conta de armazenamento dentro do contêiner `archivecontainer` para uma camada de arquivo morto. A transição é realizada agindo em BLOBs 0 dias após a hora da última modificação:

> [!NOTE] 
> É recomendável carregar seus BLOBs diretamente a camada de arquivo para ser mais eficiente. Você pode usar o cabeçalho x-MS-Access-Tier para [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) ou [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) com a versão REST 2018-11-09 e mais recente ou nossas bibliotecas de cliente de armazenamento de blob mais recentes. 

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Expirar os dados com base na idade

Espera-se que alguns dados expirem dias ou meses após a criação. Configure uma política de gerenciamento do ciclo de vida para expirar os dados por exclusão com base na idade deles. O exemplo a seguir mostra uma política que exclui todos os blobs de bloco com mais de 365 dias.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>Excluir dados com marcas de índice de BLOB
Alguns dados só devem ser expirados se explicitamente marcados para exclusão. Você pode configurar uma política de gerenciamento de ciclo de vida para expirar dados marcados com atributos de chave/valor de índice de BLOB. O exemplo a seguir mostra uma política que exclui todos os blobs de bloco marcados com `Project = Contoso` . Para saber mais sobre o Índice de Blob, confira [Gerenciar e localizar dados no Armazenamento de Blobs do Azure com o Índice de Blob (versão prévia)](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="delete-old-snapshots"></a>Excluir instantâneos antigos

Para dados que são modificados e acessados regularmente durante seu ciclo de vida, instantâneos geralmente são usados para controlar versões mais antigas dos dados. Você pode criar uma política que exclui os instantâneos antigos com base na idade do instantâneo. A idade de instantâneo é determinada avaliando-se a hora de criação do instantâneo. Esta regra de política exclui instantâneos blobs de blocos no contêiner `activedata` com 90 dias ou mais após a criação do instantâneo.

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="faq"></a>Perguntas frequentes

**Eu criei uma nova política, por que as ações não são executadas imediatamente?**

A plataforma executa a política de ciclo de vida uma vez por dia. Depois de configurar uma política, pode levar até 24 horas para que algumas ações sejam executadas pela primeira vez.

**Se eu atualizar uma política existente, quanto tempo levará para que as ações sejam executadas?**

A política atualizada leva até 24 horas para entrar em vigor. Depois que a política estiver em vigor, poderá levar até 24 horas para que as ações sejam executadas. Portanto, as ações de política podem levar até 48 horas para serem concluídas.

**Eu resalimentava manualmente um blob arquivado, como impedir que ele fosse movido de volta para a camada de arquivamento temporariamente?**

Quando um blob é movido de uma camada de acesso para outra, sua hora da última modificação não é alterada. Se você reidratar manualmente um blob arquivado na camada quente, ele seria movido de volta para a camada de arquivo pelo mecanismo de gerenciamento do ciclo de vida. Desabilite a regra que afeta esse blob temporariamente para impedir que ele seja arquivado novamente. Habilite novamente a regra quando o blob puder ser movido com segurança de volta para a camada de arquivo morto. Você também poderá copiar o blob para outro local se ele precisar permanecer na camada quente ou fria permanentemente.

## <a name="next-steps"></a>Próximas etapas

Saiba como recuperar dados após uma exclusão acidental:

- [Exclusão reversível para blobs do Armazenamento do Azure ](../blobs/storage-blob-soft-delete.md)

Saiba como gerenciar e localizar dados com o índice de blob:

- [Gerenciar e localizar dados no armazenamento de BLOBs do Azure com índice de BLOB](storage-manage-find-blobs.md)
