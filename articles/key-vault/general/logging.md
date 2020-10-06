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
ms.openlocfilehash: a51e9a628f67269357d42bd1d3af10c1d86f301a
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91739775"
---
# <a name="azure-key-vault-logging"></a>Log do Azure Key Vault

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Depois de criar um ou mais cofres de chaves, provavelmente você desejará monitorar como e quando os cofres de chaves serão acessados e por quem. Você pode fazer isso habilitando o registro em log do Azure Key Vault, que salva as informações em uma conta de armazenamento do Azure fornecida por você. Um novo contêiner chamado **insights-logs-auditevent** é criado automaticamente para a conta de armazenamento especificada. Você pode usar essa mesma conta de armazenamento para coletar logs de vários cofres de chaves.

Você pode acessar suas informações de log 10 minutos (no máximo) após a operação do cofre de chaves. Na maioria dos casos, será mais rápido do que isso.  Cabe a você gerenciar os logs em sua conta de armazenamento:

* Use os métodos de controle de acesso padrão do Azure para proteger seus logs ao restringir quem pode acessá-los.
* Exclua os logs que você não deseja manter em sua conta de armazenamento.

Para obter informações de visão geral sobre o Key Vault, confira [O que é o Azure Key Vault?](overview.md). Para obter informações sobre onde o Key Vault está disponível, consulte a [página de preços](https://azure.microsoft.com/pricing/details/key-vault/). Para obter informações sobre como usar o [Azure Monitor para Key Vault](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview).

## <a name="interpret-your-key-vault-logs"></a>Interpretar os logs do Cofre de Chave

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
