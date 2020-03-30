---
title: Gerenciamento do ciclo de vida do Armazenamento Azure
description: Aprenda a criar regras de política de ciclo de vida para fazer a transição dos dados antigos para os níveis Hot to Cool e Archive.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 238c12baf55b525a24107a727d09588ef06a6bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598299"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure

Conjuntos de dados têm ciclos de vida exclusivos. No início do ciclo de vida, as pessoas geralmente acessam alguns dados. Mas a necessidade de acesso cai drasticamente à medida que os dados envelhecem. Alguns dados permanecem ociosos na nuvem e raramente são acessados depois de armazenados. Alguns dados expiram dias ou meses após a criação, enquanto outros conjuntos de dados são lidos e modificados ativamente durante seu tempo de vida. O gerenciamento do ciclo de vida do armazenamento Azure Blob oferece uma política rica e baseada em regras para contas de armazenamento GPv2 e Blob. Use a política para fazer a transição de seus dados para as camadas de acesso apropriadas ou expirá-los ao final do ciclo de vida dos dados.

A política de gerenciamento do ciclo de vida permite:

- Fazer a transição dos blobs para uma camada de armazenamento mais esporádico (frequente para esporádico, frequente para arquivos ou esporádico para arquivos), a fim de otimizar o desempenho e os custos
- Excluir os blobs no final dos respectivos ciclos de vida
- Definir regras a serem executadas uma vez por dia no nível da conta de armazenamento
- Aplicar regras a contêineres ou a um subconjunto de blobs (usando prefixos como filtros)

Considere um cenário onde os dados tenham acesso frequente durante os estágios iniciais do ciclo de vida, mas apenas ocasionalmente após duas semanas. Após o primeiro mês, o conjunto de dados raramente é acessado. Nesse cenário, o armazenamento frequente é melhor durante os estágios iniciais. O armazenamento legal é mais apropriado para acessos ocasionais. O armazenamento de arquivos é a melhor opção de nível após o tempo de dados acima de um mês. Ajustando as camadas de armazenamento em relação à idade dos dados, você pode criar as opções de armazenamento menos dispendiosas para suas necessidades. Para conseguir essa transição, as regras de política de gerenciamento do ciclo de vida estão disponíveis para mover os dados antigos para camadas mais frias.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>Suporte da conta de armazenamento

A política de gerenciamento do ciclo de vida está disponível com contas De Propósito Geral v2 (GPv2), contas de armazenamento Blob e contas de armazenamento Premium Block Blob. No portal Azure, você pode atualizar uma conta de Propósito Geral (GPv1) existente para uma conta GPv2. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Preços

O recurso de gerenciamento do ciclo de vida é gratuito. O custo de operação normal para as chamadas à API [Listar Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) e [Definir Camada de Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) é cobrado dos clientes. A operação delete é gratuita. Para obter mais informações sobre preços, confira [Preços do Blob de Blocos](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Disponibilidade regional

O recurso de gerenciamento do ciclo de vida está disponível em todas as regiões do Azure.

## <a name="add-or-remove-a-policy"></a>Adicionar ou remover uma política

Você pode adicionar, editar ou remover uma diretiva usando qualquer um dos seguintes métodos:

* [Portal Azure](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [APIs REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Uma política pode ser lida ou escrita na íntegra. Atualizações parciais não são suportadas. 

> [!NOTE]
> Se você habilitar as regras de firewall para sua conta de armazenamento, as solicitações de gerenciamento do ciclo de vida poderão ser bloqueadas. Você pode desbloquear essas solicitações fornecendo exceções para serviços confiáveis da Microsoft. Para obter mais informações, confira a seção Exceções em [Configurar firewalls e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

Este artigo mostra como gerenciar a política usando os métodos portal e PowerShell.  

# <a name="portal"></a>[Portal](#tab/azure-portal)

Existem duas maneiras de adicionar uma política através do portal Azure. 

* [Exibição da lista do portal Azure](#azure-portal-list-view)
* [Exibição do código do portal Azure](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Exibição da lista do portal Azure

1. Faça login no [portal Azure](https://portal.azure.com).

2. No portal Azure, procure e selecione sua conta de armazenamento. 

3. Em **Blob Service,** selecione **o gerenciamento do ciclo de vida** para visualizar ou alterar suas regras.

4. Selecione a guia **''''''''''''''''**

5. Selecione **Adicionar regra** e, em seguida, preencha os campos de formulário definidos em **Ação.** No exemplo a seguir, as bolhas são movidas para o armazenamento frio se não tiverem sido modificadas por 30 dias.

   ![Página de conjunto de ações de gerenciamento de ciclo de vida no portal Azure](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Selecione **O conjunto Filtro** para adicionar um filtro opcional. Em seguida, **selecione Procurar** para especificar um contêiner e uma pasta pelos quais filtrar.

   ![Página do conjunto de filtros de gerenciamento do ciclo de vida no portal Azure](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Selecione **'Revisar + adicionar para** revisar as configurações da diretiva'.

9. Selecione **Adicionar** para adicionar a nova diretiva.

#### <a name="azure-portal-code-view"></a>Exibição do código do portal Azure
1. Faça login no [portal Azure](https://portal.azure.com).

2. No portal Azure, procure e selecione sua conta de armazenamento.

3. Em **Blob Service,** selecione **o gerenciamento do ciclo de vida** para visualizar ou alterar sua política.

4. O JSON a seguir é um exemplo de uma política que pode ser colada na guia **De exibição Código.**

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

5. Selecione **Salvar**.

6. Para obter mais informações sobre este exemplo json, consulte as seções [Política](#policy) e [Regras.](#rules)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

O script PowerShell a seguir pode ser usado para adicionar uma diretiva à sua conta de armazenamento. A `$rgname` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento.

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

Você pode definir o gerenciamento do ciclo de vida usando modelos do Azure Resource Manager. Aqui está um modelo de exemplo para implantar uma conta de armazenamento RA-GRS GPv2 com uma política de gerenciamento do ciclo de vida.

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

Cada regra dentro da política tem vários parâmetros:

| Nome do parâmetro | Tipo de parâmetro | Observações | Obrigatório |
|----------------|----------------|-------|----------|
| `name`         | String |Um nome de regra pode incluir até 256 caracteres alfanuméricos. A regra de nome diferencia maiúsculas de minúsculas.  Ela deve ser exclusiva em uma política. | True |
| `enabled`      | Boolean | Um booleano opcional para permitir que uma regra seja ativada temporariamente. O valor padrão é verdadeiro se não for definido. | Falso | 
| `type`         | Um valor de enumeração | O tipo válido `Lifecycle`atual é . | True |
| `definition`   | Um objeto que define a regra de ciclo de vida | Cada definição é composta por um conjunto de filtros e um conjunto de ações. | True |

## <a name="rules"></a>Regras

Cada definição de regra inclui um conjunto de filtros e um conjunto de ações. O [conjunto de filtros](#rule-filters) limita as ações de regras a determinado conjunto de objetos em um contêiner ou a nomes de objetos. O [conjunto de ações](#rule-actions) aplica-se a camada ou excluir ações para o conjunto filtrado de objetos.

### <a name="sample-rule"></a>Regra de exemplo

A regra de amostra a seguir filtra a conta `container1` para `foo`executar as ações em objetos que existem dentro e começar com .  

>[!NOTE]
>O gerenciamento do ciclo de vida só suporta o tipo de bolha de bloco.  

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
| blobTypes   | Uma matriz de valores de enumeração predefinidos. | A versão atual `blockBlob`suporta . | Sim |
| prefixMatch | Uma matriz de cadeias de caracteres para prefixos a serem correspondidos. Cada regra pode definir até 10 prefixos. Uma cadeia de caracteres de prefixo deve começar com um nome de contêiner. Por exemplo, se você quiser combinar `https://myaccount.blob.core.windows.net/container1/foo/...` todas as bolhas abaixo `container1/foo`de uma regra, o prefixMatch é . | Se você não definir prefixMatch, a regra se aplica a todos os blobs dentro da conta de armazenamento.  | Não |

### <a name="rule-actions"></a>Ações de regra

As ações são aplicadas às bolhas filtradas quando a condição de execução é atendida.

O gerenciamento do ciclo de vida suporta hierarquiagem e exclusão de blobs e exclusão de instantâneos blob. Defina, pelo menos, uma ação para cada regra em blobs ou instantâneos de blob.

| Ação        | Blob base                                   | Instantâneo      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Dá suporte aos blobs atualmente presentes na camada frequente         | Sem suporte |
| tierToArchive | Dá suporte aos blobs atualmente presentes na camada frequente ou esporádica | Sem suporte |
| excluir        | Com suporte                                   | Com suporte     |

>[!NOTE]
>Se você definir mais de uma ação no mesmo blob, o gerenciamento do ciclo de vida aplicará a ação mais barata ao blob. Por exemplo, a ação `delete` é mais barata do que a ação `tierToArchive`. A ação `tierToArchive` é mais barata do que a ação `tierToCool`.

As condições de execução são baseadas na idade. Os blobs base usam a hora da última modificação para acompanhar a idade, enquanto os instantâneos de blob usam a hora de criação do instantâneo para executar a mesma tarefa.

| Condição de execução de ação             | Valor de condição                          | Descrição                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Valor inteiro que indica a idade em dias | A condição para ações de bolha base     |
| daysAfterCreationGreaterThan     | Valor inteiro que indica a idade em dias | A condição para ações de instantâneo slob |

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

### <a name="archive-data-after-ingest"></a>Arquivar dados após a ingestão

Alguns dados permanecem ociosos na nuvem e raramente ou nunca são acessados depois de armazenados. A seguinte política do ciclo de vida é configurada para arquivar dados logo após sua ingestão. Este exemplo transita o bloco blobs `archivecontainer` na conta de armazenamento dentro do contêiner para um nível de arquivo. A transição é realizada agindo em blobs 0 dias após o último tempo modificado:

> [!NOTE] 
> Recomenda-se carregar suas bolhas diretamente no nível de arquivo para ser mais eficiente. Você pode usar o cabeçalho x-ms-acess-tier para [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) ou [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) com a versão REST 2018-11-09 e mais recente ou nossas bibliotecas de clientes de armazenamento blob mais recentes. 

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

Espera-se que alguns dados expirem dias ou meses após a criação. Configure uma política de gerenciamento do ciclo de vida para expirar os dados por exclusão com base na idade deles. O exemplo a seguir mostra uma diretiva que exclui todas as bolhas de bloco com mais de 365 dias.

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
A plataforma executa a política de ciclo de vida uma vez por dia. Uma vez configurada uma diretiva, pode levar até 24 horas para que algumas ações sejam executadas pela primeira vez.  

**Se eu atualizar uma política existente, quanto tempo leva para as ações serem executadas?**  
A política atualizada leva até 24 horas para entrar em vigor. Uma vez que a apólice esteja em vigor, pode levar até 24 horas para as ações serem executadas. Portanto, as ações políticas podem levar até 48 horas para serem concluídas.   

**Reidratei manualmente uma bolha arquivada, como faço para evitar que ela seja transferida temporariamente para o arquivo?**  
Quando uma bolha é movida de um nível de acesso para outro, seu tempo de última modificação não muda. Se você reidratar manualmente uma bolha arquivada para o nível quente, ela será movida de volta para o nível de arquivamento pelo mecanismo de gerenciamento do ciclo de vida. Desabilite a regra que afeta essa bolha temporariamente para evitar que ela seja arquivada novamente. Reative a regra quando a bolha puder ser movida de volta com segurança para o nível de arquivamento. Você também pode copiar a bolha para outro local se ele precisar ficar em nível quente ou frio permanentemente.

## <a name="next-steps"></a>Próximas etapas

Saiba como recuperar dados após uma exclusão acidental:

- [Exclusão reversível para blobs do Armazenamento do Azure ](../blobs/storage-blob-soft-delete.md)
