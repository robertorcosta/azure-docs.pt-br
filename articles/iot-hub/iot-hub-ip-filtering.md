---
title: Filtros IP de conexão do Hub IoT do Azure | Microsoft Docs
description: Como usar a filtragem de IP para bloquear conexões de endereços IP específicos para seu Hub IoT do Azure. Você pode bloquear conexões de endereços IP individuais ou de intervalos de endereços IP.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: robinsh
ms.openlocfilehash: 1ba3c89ea4f964f9e6fd5f902aab29a83a058f25
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074725"
---
# <a name="use-ip-filters"></a>Usar filtros IP

A segurança é um aspecto importante de qualquer solução de IoT com base no Hub IoT do Azure. Às vezes você precisa especificar explicitamente os endereços IP dos quais o dispositivo pode se conectar como parte da sua configuração de segurança. O recurso *filtro IP* permite que você configure regras para rejeitar ou aceitar tráfego de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando usar

Há dois casos de uso específicos em que é útil bloquear os pontos de extremidade do Hub IoT para determinados endereços IP:

* O Hub IoT deve receber tráfego somente de um intervalo específico de endereços IP e rejeitar todo o resto. Por exemplo, caso você esteja usando o Hub IoT com o [Azure ExpressRoute](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) para criar conexões privadas entre um Hub IoT e sua infraestrutura local.

* Você precisa rejeitar tráfego de endereços IP que foram identificados como suspeitos pelo administrador do Hub IoT.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas no nível do serviço Hub IoT. Portanto, as regras de filtro de IP se aplicam a todas as conexões de dispositivos e aplicativos de back-end que usam qualquer protocolo com suporte. No entanto, os clientes que leem diretamente do [ponto de extremidade compatível com o Hub de Eventos interno](iot-hub-devguide-messages-read-builtin.md) (não por meio da cadeia de conexão do Hub IoT) não estão associados às regras de filtro de IP. 

Todas as tentativas de conexão de um endereço IP que corresponde a uma regra IP de rejeição em seu Hub IoT recebem um código de status 401 não autorizado e uma descrição. A mensagem de resposta não menciona a regra IP. Rejeitar endereços IP pode impedir que outros serviços do Azure como Stream Analytics do Azure, Máquinas Virtuais do Azure ou o Gerenciador de Dispositivos no portal do Azure interajam com o hub IoT.

> [!NOTE]
> Se você precisa usar o Azure Stream Analytics (ASA) para ler mensagens de um hub IoT com o filtro de IP habilitado, use o nome compatível com o Hub de Eventos e o ponto de extremidade do hub IoT para adicionar manualmente uma [entrada de fluxo dos Hubs de Eventos](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) no ASA.

## <a name="default-setting"></a>Configuração padrão

Por padrão, a grade **Filtro IP** no portal de Hub IoT fica vazia. Essa configuração padrão significa que seu hub aceita conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

Para acessar a página Configurações de filtro de IP, selecione **Rede**, **Acesso público**, em seguida escolha **Intervalos de IP selecionados**:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="Configurações de filtro de IP padrão do Hub IoT":::

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro IP

Para adicionar uma regra de filtro de IP, selecione **+ Adicionar regra de filtro de IP**.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Adicionar uma regra de filtro de IP a um hub IoT":::

Após selecionar **Adicionar regra de filtro de IP**, preencha os campos.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Após selecionar Adicionar uma regra de filtro de IP":::

* Forneça um **nome** para a regra de filtro de IP. O nome deve ser uma cadeia exclusiva de caracteres alfanuméricos, que não diferencia maiúsculas de minúsculas, de até 128 caracteres. Somente são aceitos caracteres alfanuméricos ASCII de 7 bits mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Forneça um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, uma notação CIDR 192.168.100.0/22 representa os 1024 endereços IPv4 de 192.168.100.0 a 192.168.103.255.

* Selecione **Permitir** ou **Bloquear** como a **ação** para a regra de filtro de IP.

Depois de preencher os campos, selecione **Salvar** para salvar a regra. Você verá um alerta informando que a atualização está em andamento.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Notificação de salvamento uma regra de filtro de IP":::

A opção **Adicionar** é desabilitada quando você atinge o máximo de dez regras de filtro IP.

Para editar uma regra existente, selecione os dados que você deseja alterar, faça a alteração e, em seguida, selecione **Salvar** para salvar a alteração.

## <a name="delete-an-ip-filter-rule"></a>Excluir uma regra de filtro IP

Para excluir uma regra de filtro de IP, selecione o ícone de lixeira na respectiva linha e, em seguida, selecione **Salvar**. Isso removerá a regra e salvará a alteração.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Excluir uma regra de filtro de IP do Hub IoT":::

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Recuperar e atualizar os filtros IP usando a CLI do Azure

Os filtros de IP do seu Hub IoT Podem ser recuperados e atualizados por meio da [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Para recuperar os filtros IP atuais do Hub IoT, execute:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Isso retornará um objeto JSON onde seus filtros IP existentes são listados na chave `properties.ipFilterRules`:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Para adicionar um novo filtro IP para o Hub IoT, execute:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Para remover um filtro IP existente em seu Hub IoT, execute:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Observe que `<ipFilterIndexToRemove>` deve corresponder à ordenação de filtros IP no `properties.ipFilterRules` do seu Hub IoT.

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Recuperar e atualizar os filtros IP usando o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os filtros de IP do seu Hub IoT podem ser recuperados e configurados através do [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Atualizar as regras do filtro de IP usando REST

Você também pode recuperar e modificar um filtro de IP do seu Hub IoT usando o ponto de extremidade REST do provedor de recursos do Azure. Veja `properties.ipFilterRules` no [método createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Por exemplo, se você quiser aceitar endereços no intervalo 192.168.100.0/22 e rejeitar todo o resto, a primeira regra na grade deverá aceitar o intervalo de endereços 192.168.100.0/22. A próxima regra deve rejeitar todos os endereços usando o intervalo 0.0.0.0/0.

Você pode alterar a ordem de suas regras de filtro IP na grade clicando nos três pontos verticais no início de uma linha e usando arrastar e soltar.

Para salvar a nova ordem das regras de filtro IP, clique em **Salvar**.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-rule-order.png" alt-text="Alterar a ordem de suas regras de filtro de IP do Hub IoT":::

## <a name="next-steps"></a>Próximas etapas

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Métricas do IoT Hub](iot-hub-metrics.md)
