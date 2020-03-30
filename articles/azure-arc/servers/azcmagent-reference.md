---
title: Interface da CLI do Agente do Computador Conectado do Azure
description: Documentação de referência para o agente de máquina conectada azure CLI
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513192"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Interface da CLI do Agente do Computador Conectado do Azure

A `Azcmagent` ferramenta (Azure Connected Machine Agent) é usada para configurar e solucionar problemas uma conexão de máquinas não-azure ao Azure.

O agente em si é `himdsd` um processo de daemon chamado no Linux, e um Windows Service chamado `himds` no Windows.

No uso `azcmagent connect` normal, é usado para estabelecer uma conexão `azcmagent disconnect` entre esta máquina e o Azure, e se você decidir que não quer mais essa conexão. Os outros comandos são para solução de problemas ou outros casos especiais.

## <a name="options"></a>Opções

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>CONSULTE TAMBÉM

* [conexão azcmagent](#azcmagent-connect) - Conecta esta máquina ao Azure
* [desconexão azcmagent](#azcmagent-disconnect) - Desconecta esta máquina do Azure
* [azcmagent reconectar](#azcmagent-reconnect) - Reconecta esta máquina ao Azure
* [azcmagent show](#azcmagent-show) - Obtém metadados da máquina e status do agente. Isso é principalmente útil para a solução de problemas.
* [versão azcmagent](#azcmagent-version) - Exibir a versão hybrid management agent

## <a name="azcmagent-connect"></a>azcmagent conectar

Conecta esta máquina ao Azure

### <a name="synopsis"></a>Sinopse

Cria um recurso no Azure representando esta máquina.

Isso usa as opções de autenticação fornecidas para criar um recurso no Azure Resource Manager representando esta máquina. O recurso está no grupo de assinatura e recursos solicitados, e os dados sobre a máquina são armazenados na região do Azure especificada pelo parâmetro de localização.
O nome de recurso padrão é o nome de host desta máquina se não for substituído.

Um certificado correspondente à Identidade Atribuída ao Sistema desta máquina é então baixado e armazenado localmente. Uma vez que essa etapa complete o **Serviço de Metadados da Máquina Conectada do Azure** e o Agente de Configuração do Hóspede comece a sincronizar com a nuvem do Azure.

Opções de autenticação:

* Token de acesso`azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* ID principal de serviço e segredo`azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Login do dispositivo (Interativo)`azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>Sintaxe

```none
azcmagent connect [flags]
```

### <a name="options"></a>Opções

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>desconexão azcmagent

Desconecta esta máquina do Azure

### <a name="synopsis"></a>Sinopse

Exclui o recurso no Azure que representa este servidor.

Este comando usa as opções de autenticação fornecidas para remover o recurso do Azure Resource Manager representando esta máquina. Após este ponto, o **Serviço de Metadados da Máquina Conectada do Azure** e o Agente de Configuração de Hóspedes serão desconectados. Este comando não para nem remove os serviços: remova o pacote para fazer isso.

Este comando requer privilégios maiores do que a função "Azure Connected Machine Onboarding".

Uma vez que uma `azcmagent connect`máquina `azcmagent reconnect` é desconectada, use , não se você quiser criar um novo recurso para ela no Azure.

Opções de autenticação:

* Token de acesso`azcmagent disconnect --access-token <>`
* ID principal de serviço e segredo`azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Login do Dispositivo Interativo`azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>Sintaxe

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>Opções

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>azcmagent reconectar

Reconecta esta máquina ao Azure

### <a name="synopsis"></a>Sinopse

Reconecte a máquina com credenciais inválidas ao Azure.

Se uma máquina já tem um recurso no Azure, mas não é capaz de autenticar a ele, ela pode ser reconectada usando este comando. Isso é possível se uma máquina foi desligada por tempo suficiente para que seu certificado expire (pelo menos 45 dias).

Se uma máquina foi `azcmagent disconnect`desconectada com , use `azcmagent connect` em vez disso.

Este comando usa as opções de autenticação fornecidas para recuperar novas credenciais correspondentes ao recurso Do Azure Resource Manager representando esta máquina.

Este comando requer privilégios maiores do que a função **Onboarding da máquina conectada do Azure.**

Opções de Autenticação

* Token de acesso`azcmagent reconnect --access-token <>`
* ID principal de serviço e segredo`azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Login do Dispositivo Interativo`azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>Sintaxe

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>Opções

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>azcmagent show

Obtém metadados da máquina e status do agente. Isso é principalmente útil para a solução de problemas.

### <a name="synopsis"></a>Sinopse

Obtém metadados da máquina e status do agente. Isso é principalmente útil para a solução de problemas.


### <a name="syntax"></a>Sintaxe

```
azcmagent show [flags]
```

### <a name="options"></a>Opções

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>versão azcmagent

Exibir a versão do Hybrid Management Agent

### <a name="synopsis"></a>Sinopse

Exibir a versão do Hybrid Management Agent

### <a name="syntax"></a>Sintaxe

```none
azcmagent version [flags]
```

### <a name="options"></a>Opções

```none
  -h, --help   help for version
```
