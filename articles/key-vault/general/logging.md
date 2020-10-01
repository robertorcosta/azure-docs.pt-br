---
title: Registro em Log do Azure Key Vault | Microsoft Docs
description: Saiba como monitorar o acesso aos seus cofres de chaves habilitando o log no Azure Key Vault, que salva as informações em uma conta de armazenamento do Azure fornecida por você.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 0364495d751465f644686824758992d47f0b8bdf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91290630"
---
# <a name="azure-key-vault-logging"></a>Log do Azure Key Vault

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Depois de criar um ou mais cofres de chaves, provavelmente você desejará monitorar como e quando os cofres de chaves serão acessados e por quem. Você pode fazer isso habilitando o registro em log do Azure Key Vault, que salva as informações em uma conta de armazenamento do Azure fornecida por você. Um novo contêiner chamado **insights-logs-auditevent** é criado automaticamente para a conta de armazenamento especificada. Você pode usar essa mesma conta de armazenamento para coletar logs de vários cofres de chaves.

Você pode acessar suas informações de log 10 minutos (no máximo) após a operação do cofre de chaves. Na maioria dos casos, será mais rápido do que isso.  Cabe a você gerenciar os logs em sua conta de armazenamento:

* Use os métodos de controle de acesso padrão do Azure para proteger seus logs ao restringir quem pode acessá-los.
* Exclua os logs que você não deseja manter em sua conta de armazenamento.

Use este tutorial para ajudá-lo a começar a usar os logs do Cofre da Chave do Azure. Você criará uma conta de armazenamento, habilitará o registro em log e interpretará as informações de log coletadas.  

> [!NOTE]
> Este tutorial não inclui instruções sobre como criar cofres da chave, chaves ou segredos. Para obter essa informação, confira [O que é o Azure Key Vault?](overview.md). Ou, para obter instruções de uso da CLI do Azure multiplataforma, confira [este tutorial equivalente](manage-with-cli2.md).
>
> Este artigo fornece instruções do Azure PowerShell para atualizar o log de diagnósticos. Você também pode atualizar o log de diagnósticos usando o Azure Monitor na seção **Logs de Diagnóstico** do portal do Azure. 
>

Para obter informações de visão geral sobre o Key Vault, confira [O que é o Azure Key Vault?](overview.md). Para obter informações sobre onde o Key Vault está disponível, consulte a [página de preços](https://azure.microsoft.com/pricing/details/key-vault/). Para obter informações sobre como usar o [Azure Monitor para Key Vault](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário ter o seguinte:

* Um cofre da chave existente que você esteja usando.  
* Azure PowerShell, versão mínima 1.0.0. Para instalar o Azure PowerShell e associá-lo à sua assinatura do Azure, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/). Se você já tiver instalado o Azure PowerShell e não souber a versão, no console do Azure PowerShell, insira `$PSVersionTable.PSVersion`.  
* Armazenamento suficiente no Azure para seus logs do Cofre da Chave.

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>Conectar-se com sua assinatura do cofre de chaves

A primeira etapa para configurar o registro em log das chaves é apontar o Azure PowerShell para o cofre de chaves que você deseja registrar em log.

Inicie uma sessão do Azure PowerShell e entre em sua conta do Azure usando o seguinte comando:  

```powershell
Connect-AzAccount
```

Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. O Azure PowerShell obtém todas as assinaturas associadas a essa conta. Por padrão, o PowerShell usa a primeira.

Talvez seja necessário especificar a assinatura que você usou para criar o cofre de chaves. Insira o seguinte comando para ver as assinaturas da sua conta:

```powershell
Get-AzSubscription
```

Em seguida, para especificar a assinatura associada ao cofre de chaves do qual os logs serão registrados, digite:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Apontar o PowerShell para a assinatura correta é uma etapa importante, especialmente se você tiver várias assinaturas associadas à sua conta. Para saber mais sobre a configuração PowerShell do Azure, consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/).

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>Criar uma conta de armazenamento para seus logs

Embora você possa usar uma conta de armazenamento existente para seus logs, criaremos uma conta de armazenamento que será dedicada aos logs do Key Vault. Para conveniência quando tivermos de especificar isso posteriormente, armazenaremos os detalhes em uma variável chamada **sa**.

Para facilidade de gerenciamento, também usaremos o mesmo grupo de recursos que contém o cofre de chaves. No [tutorial de introdução](../secrets/quick-create-cli.md), esse grupo de recursos é denominado **ContosoResourceGroup** e continuaremos a usar o local Leste da Ásia. Substitua esses valores pelos seus, conforme aplicável:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Se você decidir usar uma conta de armazenamento existente, ela deverá usar a mesma assinatura que seu cofre de chaves. E ela deve usar o modelo de implantação do Azure Resource Manager em vez do modelo de implantação clássico.
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>Identificar o cofre da chave para seus logs

No [tutorial de introdução](../secrets/quick-create-cli.md), o nome do cofre de chaves era **ContosoKeyVault**. Continuaremos usando esse nome e armazenaremos os detalhes em uma variável chamada **kv**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging-using-azure-powershell"></a><a id="enable"></a>Habilitar o registro em log usando o Azure PowerShell

Para habilitar o registro em log para o Key Vault, usaremos o cmdlet **Set-AzDiagnosticSetting**, juntamente com as variáveis que criamos para a nova conta de armazenamento e o cofre de chaves. Também definiremos o sinalizador **-Enabled** como **$true** e a categoria como `AuditEvent` (a única categoria para o registro em log do Key Vault):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

A saída se parece com esta:

```output
StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccountContosoKeyVaultLogs
ServiceBusRuleId   :
StorageAccountName :
    Logs
    Enabled           : True
    Category          : AuditEvent
    RetentionPolicy
    Enabled : False
    Days    : 0
```

Essa saída confirma que o registro em log está habilitado para o cofre de chaves e salva as informações em sua conta de armazenamento.

Opcionalmente, você pode definir uma política de retenção para os logs, de modo que os logs mais antigos sejam automaticamente excluídos. Por exemplo, defina a política de retenção definindo o sinalizador **-RetentionEnabled** como **$true** e defina o parâmetro **-RetentionInDays** como **90** para que os logs de mais de 90 dias sejam automaticamente excluídos.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

O que é registrado em log:

* Todas as solicitações à API REST autenticadas, incluindo solicitações que falharam devido a permissões de acesso, erros do sistema ou solicitações inválidas.
* As operações do próprio cofre de chaves, incluindo a criação, a exclusão, a configuração de políticas de acesso ao cofre de chaves e a atualização dos atributos do cofre de chaves, como as marcas.
* Operações em chaves e segredos no cofre de chaves, incluindo:
  * A criação, modificação ou exclusão dessas chaves ou segredos.
  * A assinatura, verificação, criptografia, descriptografia, encapsulamento e desencapsulamento de chaves, obtenção de segredos e listagem de chaves e segredos (e suas versões).
* Solicitações não autenticadas que resultam em uma resposta 401. Por exemplo, solicitações que não têm um token de portador estão malformadas ou expiradas ou têm um token inválido.  
* Os eventos de notificação da Grade de Eventos sobre vencimento próximo, item vencido e política de acesso ao cofre foram alterados (o evento da nova versão não é registrado). Os eventos são registrados independentemente da presença de uma assinatura de evento criada no cofre de chaves. Para obter mais informações, confira [Esquema de evento da Grade de Eventos do Key Vault](https://docs.microsoft.com/azure/event-grid/event-schema-key-vault)

## <a name="enable-logging-using-azure-cli"></a>Habilitar o registro em log usando a CLI do Azure

```azurecli
az login

az account set --subscription {AZURE SUBSCRIPTION ID}

az provider register -n Microsoft.KeyVault

az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="access-your-logs"></a><a id="access"></a>Acessar seus logs

Os logs do Key Vault são armazenados no contêiner **insights-logs-auditevent** na conta de armazenamento que você forneceu. Para exibir os logs, você precisa baixar blobs.

Primeiro, crie uma variável para o nome do contêiner. Você usará essa variável em todo o restante do guia.

```powershell
$container = 'insights-logs-auditevent'
```

Para listar todos os blobs nesse contêiner, insira:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

A saída deve ser semelhante a esta:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Como você pode ver neste resultado, os blobs seguem uma convenção de nomenclatura: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Os valores de data e hora usam UTC.

Como a mesma conta de armazenamento pode ser usada para coletar logs de vários recursos, a ID do recurso completa no nome do blob será muito útil para acessar ou baixar apenas os blobs que você precisa. Mas, antes de fazer isso, primeiro vamos mostrar como baixar todos os blobs.

Crie uma pasta para baixar os blobs. Por exemplo:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Em seguida, obtenha uma lista de todos os blobs:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Redirecione essa lista por **Get-AzStorageBlobContent** para baixar os blobs para nossa pasta de destino:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Quando você executa esse segundo comando, o delimitador **/** nos nomes de blob cria uma estrutura de pastas completa na pasta de destino. Você usará essa estrutura para baixar e armazenar os blobs como arquivos.

Use caracteres curinga para baixar seletivamente os blobs. Por exemplo:

* Se você tiver vários cofres da chave e quiser baixar logs de apenas um cofre da chave, chamado CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Se você tiver vários grupos de recursos e quiser baixar os logs para apenas um grupo de recursos, use `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Se você quiser baixar todos os logs do mês de janeiro de 2019, use `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Agora você está pronto para começar a examinar o conteúdo dos logs. Mas, antes de passarmos para isso, você precisa conhecer mais dois comandos:

* Para consultar o status das configurações de diagnóstico do recurso cofre de chaves: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Para desabilitar o log do recurso cofre de chaves: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`


## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>Interpretar os logs do Cofre de Chave

Os blobs individuais são armazenados como texto, formatados como um blob JSON. Vamos examinar um exemplo de entrada de log. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

A tabela a seguir lista os nomes e as descrições de campo:

| Nome do campo | Descrição |
| --- | --- |
| **time** |Data e hora em UTC. |
| **resourceId** |ID do Recurso do Azure Resource Manager. Para os logs do Cofre da Chave, isso sempre será a ID do recurso do Cofre da Chave. |
| **operationName** |Nome da operação, como documentado na tabela a seguir. |
| **operationVersion** |Versão da API REST solicitada pelo cliente. |
| **category** |Tipo de resultado. Para logs do Key Vault, `AuditEvent` é o único valor disponível. |
| **resultType** |Resultado da solicitação à API REST. |
| **resultSignature** |Código de status HTTP. |
| **resultDescription** |Descrição adicional sobre o resultado, quando disponível. |
| **durationMs** |Tempo necessário para atender à solicitação da API REST, em milissegundos. Isso não inclui a latência de rede e, portanto, o tempo medido no lado cliente pode não corresponder a esse tempo. |
| **callerIpAddress** |Endereço IP do cliente que fez o a solicitação. |
| **correlationId** |Um GUID opcional que o cliente pode passar para correlacionar os logs do lado do cliente aos logs do lado do serviço (Chave do Cofre). |
| **identidade** |Identidade do token que foi apresentado na solicitação à API REST. Geralmente, é um "usuário", uma "entidade de serviço" ou uma combinação "usuário+appId" como no caso de uma solicitação resultante de um cmdlet do Azure PowerShell. |
| **properties** |Informações que variam de acordo com a operação (**operationName**). Na maioria dos casos, este campo contém informações de cliente (a cadeia de caracteres do agente do usuário passada pelo cliente), o URI exato de solicitação da API REST e o código de status HTTP. Além disso, quando um objeto é retornado como resultado de uma solicitação (por exemplo, **KeyCreate** ou **VaultGet**), também conterá o URI da chave (como `id`), o URI do cofre ou o URI do segredo. |

Os valores do campo **operationName** estão no formato *ObjectVerb*. Por exemplo:

* Todas as operações do cofre de chaves têm o formato `Vault<action>`, como `VaultGet` e `VaultCreate`.
* Todas as operações de chave têm o formato `Key<action>`, como `KeySign` e `KeyList`.
* Todas as operações de segredo têm o formato `Secret<action>`, como `SecretGet` e `SecretListVersions`.

A tabela a seguir lista os valores de **operationName** e os comandos da API REST correspondentes:

### <a name="operation-names-table"></a>Tabela de nomes de operação

| operationName | Comando da API REST |
| --- | --- |
| **Autenticação** |Autenticação via ponto de extremidade do Azure Active Directory |
| **VaultGet** |[Obter informações sobre um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Criar ou atualizar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Excluir um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Atualizar um cofre da chave](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Listar todos os cofres de chaves em um grupo de recursos](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Criar uma chave](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Obter informações sobre uma chave](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importar uma chave para um cofre](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Fazer backup de uma chave](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Excluir uma chave](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Restaurar uma chave](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Assinar com uma chave](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Verificar com uma chave](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Encapsular uma chave](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Desencapsular uma chave](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Criptografar com uma chave](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Descriptografar com uma chave](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Atualizar uma chave](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Listar as chaves em um cofre](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Listar as versões de uma chave](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Criar um segredo](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Obter um segredo](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Atualizar um segredo](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Excluir um segredo](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Listar segredos em um cofre](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Listar versões de um segredo](https://msdn.microsoft.com/library/azure/dn986824.aspx) |
| **VaultAccessPolicyChangedEventGridNotification** | Evento de alteração da política de acesso ao cofre publicado |
| **SecretNearExpiryEventGridNotification** |Evento de segredo próximo do vencimento publicado |
| **SecretExpiredEventGridNotification** |Evento de segredo vencido publicado |
| **KeyNearExpiryEventGridNotification** |Evento de chave próxima do vencimento publicado |
| **KeyExpiredEventGridNotification** |Evento de chave vencida publicado |
| **CertificateNearExpiryEventGridNotification** |Evento de certificado próximo do vencimento publicado |
| **CertificateExpiredEventGridNotification** |Evento de certificado vencido publicado |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Usar logs do Azure Monitor

É possível usar a solução do Key Vault nos logs do Azure Monitor para examinar os logs `AuditEvent` do Key Vault. Nos logs do Azure Monitor, você usa consultas de log para analisar dados e obter as informações necessárias. 

Para obter mais informações, incluindo como configurar isso, confira [Azure Key Vault no Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a><a id="next"></a>Próximas etapas

Para obter um tutorial que usa o Azure Key Vault em um aplicativo Web .NET, confira [Usar o Azure Key Vault em um aplicativo Web](tutorial-net-create-vault-azure-web-app.md).

Para referências de programação, consulte [Guia do desenvolvedor do Cofre da Chave do Azure](developers-guide.md).

Para obter uma lista dos cmdlets do Azure PowerShell 1.0 para o Azure Key Vault, confira [Cmdlets do Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).
