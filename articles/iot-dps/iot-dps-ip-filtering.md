---
title: Filtros de conexão IP Azure IoT DPS | Microsoft Docs
description: Como usar a filtragem ip para bloquear conexões de endereços IP específicos para a instância Do DPS do Azure IoT. Você pode bloquear conexões de endereços IP individuais ou de intervalos de endereços IP.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284909"
---
# <a name="use-ip-filters"></a>Usar filtros IP

A segurança é um aspecto importante de qualquer solução de IoT. Às vezes você precisa especificar explicitamente os endereços IP dos quais o dispositivo pode se conectar como parte da sua configuração de segurança. O recurso *de filtro IP* para um DPS (Azure IoT Hub Device Provisioning Service, serviço de provisionamento de dispositivos de hub hub) permite configurar regras para rejeitar ou aceitar tráfego de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando usar

Existem dois casos de uso específicos em que é útil bloquear conexões a um ponto final DPS a partir de certos endereços IP:

* Seu DPS deve receber tráfego apenas a partir de uma gama especificada de endereços IP e rejeitar todo o resto. Por exemplo, você está usando seu DPS com [o Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) para criar conexões privadas entre um DPS e seus dispositivos.

* Você precisa rejeitar o tráfego de endereços IP que foram identificados como suspeitos pelo administrador do DPS.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras do filtro IP são aplicadas no nível de instância DPS. Portanto, as regras de filtro IP se aplicam a todas as conexões de dispositivos e aplicativos de back-end que usam qualquer protocolo com suporte.

Qualquer tentativa de conexão de um endereço IP que corresponda a uma regra IP rejeitada na instância do DPS recebe um código de status e descrição não autorizados do 401. A mensagem de resposta não menciona a regra IP.

## <a name="default-setting"></a>Configuração padrão

Por padrão, a grade **do Filtro IP** no portal para DPS está vazia. Essa configuração padrão significa que seu DPS aceita conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

![Configurações do filtro IP padrão do IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro IP

Para adicionar uma regra de filtro IP, selecione **+ Adicione a regra do filtro IP**.

![Adicione uma regra de filtro IP a um DPS IoT](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Depois de selecionar **Adicionar regra do filtro IP,** preencha os campos.

![Depois de selecionar Adicionar uma regra do filtro IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Forneça um **nome** para a regra do filtro IP. Esta deve ser uma seqüência única, insensível ao caso, alfanumérica de até 128 caracteres. Somente são aceitos caracteres alfanuméricos ASCII de 7 bits mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Forneça um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, uma notação CIDR 192.168.100.0/22 representa os 1024 endereços IPv4 de 192.168.100.0 a 192.168.103.255.

* Selecione **Permitir** ou **Bloquear** como **ação** para a regra do filtro IP.

Depois de preencher os campos, **selecione Salvar** para salvar a regra. Você vê um alerta notificando-o de que a atualização está em andamento.

![Notificação sobre como salvar uma regra de filtro IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

A opção **Adicionar** é desabilitada quando você atinge o máximo de dez regras de filtro IP.

Para editar uma regra existente, selecione os dados que deseja alterar, faça a alteração e selecione **Salvar** para salvar sua edição.

> [!NOTE]
> A rejeição de endereços IP pode impedir que outros Serviços Do Azure interajam com a instância DPS.

## <a name="delete-an-ip-filter-rule"></a>Excluir uma regra de filtro IP

Para excluir uma regra do filtro IP, selecione o ícone da lata de lixo nessa linha e selecione **Salvar**. A regra é removida e a mudança é salva.

![Exclua uma regra do filtro IP IoT DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Atualizar as regras do filtro IP em código

Você pode recuperar e modificar seu filtro IP DPS usando o ponto final REST do provedor de recursos do Azure. Veja `properties.ipFilterRules` no [método createorupdate](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

A atualização das regras do filtro IP do DPS não é suportada atualmente com o Azure CLI ou o Azure PowerShell, mas pode ser realizada com os modelos do Azure Resource Manager. Consulte os [modelos do Azure Resource Manager](../azure-resource-manager/templates/overview.md) para obter orientações sobre o uso de modelos do Gerenciador de Recursos. Os exemplos de modelo a seguir mostram como criar, editar e excluir regras do filtro IP do DPS.

### <a name="add-an-ip-filter-rule"></a>Adicione uma regra de filtro IP

O exemplo do modelo a seguir cria uma nova regra de filtro IP chamada "AllowAll" que aceita todo o tráfego.

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

Atualize os atributos da regra do filtro IP do modelo com base em suas necessidades.

| Atributo                | Descrição |
| ------------------------ | ----------- |
| **Filtername**           | Forneça um nome para a regra do filtro IP. Esta deve ser uma seqüência única, insensível ao caso, alfanumérica de até 128 caracteres. Apenas os caracteres alfanuméricos ASCII de 7 bits mais {'-', '',', '/', '',',\''+', '%', '!', ''', '''' são aceitos. |
| **Ação**               | Os valores aceitos são **Aceitar** ou **Rejeitar** como ação para a regra do filtro IP. |
| **ipMask**               | Forneça um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, uma notação CIDR 192.168.100.0/22 representa os 1024 endereços IPv4 de 192.168.100.0 a 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Atualize uma regra do filtro IP

O exemplo do modelo a seguir atualiza a regra do filtro IP chamada "AllowAll", mostrada anteriormente, para rejeitar todo o tráfego.

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

O exemplo do modelo a seguir exclui todas as regras do filtro IP para a instância DPS.

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

![Alterar a ordem das regras do filtro IP do DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Próximas etapas

Para explorar ainda mais o DPS gerenciador, consulte:

* [Entendendo endereços IP DPS De IoT](iot-dps-understand-ip-address.md)
* [Configure o DPS usando o Azure CLI](how-to-manage-dps-with-cli.md)
* [Controle o acesso ao DPS](how-to-control-access.md)
