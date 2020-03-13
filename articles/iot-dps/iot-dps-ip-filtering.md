---
title: Filtros de conexão IP do DPS do Azure IoT | Microsoft Docs
description: Como usar a filtragem de IP para bloquear conexões de endereços IP específicos para sua instância do DPS do Azure IoT. Você pode bloquear conexões de endereços IP individuais ou de intervalos de endereços IP.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284909"
---
# <a name="use-ip-filters"></a>Usar filtros IP

A segurança é um aspecto importante de qualquer solução de IoT. Às vezes você precisa especificar explicitamente os endereços IP dos quais o dispositivo pode se conectar como parte da sua configuração de segurança. O recurso de *filtro IP* para um serviço de provisionamento de dispositivos no Hub IOT do Azure (DPS) permite que você configure regras para rejeitar ou aceitar tráfego de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando usar

Há dois casos de uso específicos em que é útil bloquear conexões a um ponto de extremidade de DPS de determinados endereços IP:

* Seu DPS deve receber tráfego somente de um intervalo especificado de endereços IP e rejeitar todo o resto. Por exemplo, você está usando seu DPS com o [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) para criar conexões privadas entre um DPS e seus dispositivos.

* Você precisa rejeitar o tráfego de endereços IP que foram identificados como suspeitos pelo administrador de DPS.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas no nível de instância do DPS. Portanto, as regras de filtro IP se aplicam a todas as conexões de dispositivos e aplicativos de back-end que usam qualquer protocolo com suporte.

Qualquer tentativa de conexão de um endereço IP que corresponda a uma regra de IP rejeitada em sua instância do DPS recebe um código de status 401 não autorizado e uma descrição. A mensagem de resposta não menciona a regra IP.

## <a name="default-setting"></a>Configuração padrão

Por padrão, a grade de **filtro IP** no portal para DPS está vazia. Essa configuração padrão significa que seu DPS aceita conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

![Configurações de filtro IP padrão de DPS de IoT](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro IP

Para adicionar uma regra de filtro IP, selecione **+ Adicionar regra de filtro IP**.

![Adicionar uma regra de filtro IP a um DPS de IoT](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Depois de selecionar **Adicionar regra de filtro IP**, preencha os campos.

![Depois de selecionar Adicionar uma regra de filtro IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Forneça um **nome** para a regra de filtro IP. Deve ser uma cadeia de caracteres alfanumérica, não diferencia maiúsculas de minúsculas, de até 128 caracteres de comprimento. Somente são aceitos caracteres alfanuméricos ASCII de 7 bits mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Forneça um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, uma notação CIDR 192.168.100.0/22 representa os 1024 endereços IPv4 de 192.168.100.0 a 192.168.103.255.

* Selecione **permitir** ou **Bloquear** como a **ação** para a regra de filtro IP.

Depois de preencher os campos, selecione **salvar** para salvar a regra. Você verá um alerta notificando que a atualização está em andamento.

![Notificação sobre como salvar uma regra de filtro IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

A opção **Adicionar** é desabilitada quando você atinge o máximo de dez regras de filtro IP.

Para editar uma regra existente, selecione os dados que você deseja alterar, faça a alteração e, em seguida, selecione **salvar** para salvar a edição.

> [!NOTE]
> Rejeitar endereços IP pode impedir que outros serviços do Azure interajam com a instância do DPS.

## <a name="delete-an-ip-filter-rule"></a>Excluir uma regra de filtro IP

Para excluir uma regra de filtro IP, selecione o ícone de lixeira na linha e, em seguida, selecione **salvar**. A regra é removida e a alteração é salva.

![Excluir uma regra de filtro IP de DPS de IoT](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Atualizar regras de filtro IP no código

Você pode recuperar e modificar seu filtro de IP de DPS usando o ponto de extremidade REST do provedor de recursos do Azure. Veja `properties.ipFilterRules` no [método createorupdate](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

A atualização de regras de filtro IP de DPS não tem suporte no momento com CLI do Azure ou Azure PowerShell, mas pode ser realizada com modelos de Azure Resource Manager. Consulte [Azure Resource Manager modelos](../azure-resource-manager/templates/overview.md) para obter diretrizes sobre como usar modelos do Resource Manager. Os exemplos de modelo a seguir mostram como criar, editar e excluir regras de filtro IP de DPS.

### <a name="add-an-ip-filter-rule"></a>Adicionar uma regra de filtro IP

O exemplo de modelo a seguir cria uma nova regra de filtro IP denominada "AllowAll" que aceita todo o tráfego.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Atualize os atributos de regra de filtro IP do modelo com base em seus requisitos.

| Atributo                | DESCRIÇÃO |
| ------------------------ | ----------- |
| **Filter**           | Forneça um nome para a regra de filtro IP. Deve ser uma cadeia de caracteres alfanumérica, não diferencia maiúsculas de minúsculas, de até 128 caracteres de comprimento. Somente os caracteres alfanuméricos ASCII de 7 bits mais {'-', ': ', '/', '\', '. ', ' + ', '% ', ' _ ', ' # ', ' * ', '? ', '! ', ' (', ') ', ', ', ' = ', ' @ ', '; ', ' ' '} são aceitos. |
| **Ação**               | Os valores aceitos são **aceitar** ou **rejeitar** como a ação para a regra de filtro IP. |
| **ipMask**               | Forneça um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, uma notação CIDR 192.168.100.0/22 representa os 1024 endereços IPv4 de 192.168.100.0 a 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Atualizar uma regra de filtro IP

O exemplo de modelo a seguir atualiza a regra de filtro IP denominada "AllowAll", mostrada anteriormente, para rejeitar todo o tráfego.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Excluir uma regra de filtro IP

O exemplo de modelo a seguir exclui todas as regras de filtro IP da instância do DPS.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Por exemplo, se você quiser aceitar endereços no intervalo 192.168.100.0/22 e rejeitar todo o resto, a primeira regra na grade deverá aceitar o intervalo de endereços 192.168.100.0/22. A próxima regra deve rejeitar todos os endereços usando o intervalo 0.0.0.0/0.

Você pode alterar a ordem de suas regras de filtro IP na grade clicando nos três pontos verticais no início de uma linha e usando arrastar e soltar.

Para salvar a nova ordem das regras de filtro IP, clique em **Salvar**.

![Alterar a ordem de suas regras de filtro IP do DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Próximas etapas

Para explorar melhor o DPS de gerenciamento, consulte:

* [Noções básicas sobre endereços IP de DPS do IoT](iot-dps-understand-ip-address.md)
* [Configurar o DPS usando o CLI do Azure](how-to-manage-dps-with-cli.md)
* [Controlar o acesso ao DPS](how-to-control-access.md)
