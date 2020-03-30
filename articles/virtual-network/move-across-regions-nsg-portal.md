---
title: Mova o Grupo de Segurança da Rede Azure (NSG) para outra região do Azure usando o portal Azure
description: Use o modelo do Azure Resource Manager para mover o grupo de segurança da rede Azure de uma região do Azure para outra usando o portal Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: dce267178c3caf813ccdcac4bba86ccfde3f3421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647179"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Mova o Grupo de Segurança da Rede Azure (NSG) para outra região usando o portal Azure

Existem vários cenários em que você gostaria de mover seus NSGs existentes de uma região para outra. Por exemplo, você pode querer criar um NSG com as mesmas regras de configuração e segurança para testes. Você também pode querer mover um NSG para outra região como parte do planejamento de recuperação de desastres.

Grupos de segurança do Azure não podem ser movidos de uma região para outra. No entanto, você pode usar um modelo do Azure Resource Manager para exportar as regras de configuração e segurança existentes de um NSG.  Em seguida, você pode encenar o recurso em outra região exportando o NSG para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantar o modelo para a nova região.  Para obter mais informações sobre gerenciador de recursos e modelos, consulte [Quickstart: Crie e implante modelos do Azure Resource Manager usando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o grupo de segurança da rede Azure esteja na região do Azure, da qual você deseja se mover.

- Grupos de segurança da rede Azure não podem ser movidos entre regiões.  Você terá que associar o novo NSG aos recursos na região alvo.

- Para exportar uma configuração DE NSG e implantar um modelo para criar um NSG em outra região, você precisará da função Contribuinte de rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Este layout inclui, mas não se limita a balanceadores de carga, IPs públicos e redes virtuais.

- Verifique se sua assinatura do Azure permite criar NSGs na região de destino que é usada. Contate o suporte para habilitar a cota necessária.

- Certifique-se de que sua assinatura tenha recursos suficientes para suportar a adição de NSGs para este processo.  Consulte [Assinatura do Azure e limites de serviço, cotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Prepare-se e mova-se
As etapas a seguir mostram como preparar o grupo de segurança da rede para o movimento de configuração e regra de segurança usando um modelo de Gerenciador de recursos e mover as regras de configuração e segurança do NSG para a região de destino usando o portal.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Exportar o modelo e implantar a partir do portal

1. Faça login no [portal Azure](https://portal.azure.com) > **Grupos de Recursos**.
2. Localize o Grupo de recursos que contém o NSG de origem e clique nele.
3. Selecione >**modelo de exportação** **de configurações** > .
4. Escolha **Implantar** na lâmina **do modelo Exportar.**
5. Clique **EM EDITAR** > **parâmetros** para abrir o arquivo **parameters.json** no editor on-line.
6. Para editar o parâmetro do nome NSG, altere a propriedade **de valor** **parâmetros:**

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Altere o valor de NSG de origem no editor para um nome de sua escolha para o NSG de destino. Certifique-se de envolver o nome entre aspas.

8.  Clique em **Salvar** no editor.

9.  Clique **EM EDITAR** > **modelo** para abrir o arquivo **template.json** no editor on-line.

10. Para editar a região de destino onde as regras de configuração e segurança do NSG serão movidas, altere a propriedade **de localização** em **recursos** no editor on-line:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. Para obter códigos de localização da região, consulte [Azure Localizações](https://azure.microsoft.com/global-infrastructure/locations/).  O código para uma região é o nome da região sem espaços, **central dos EUA.** = **centralus**

12. Você também pode alterar outros parâmetros no modelo se você escolher, e são opcionais dependendo de seus requisitos:

    * **Regras de segurança** - Você pode editar quais regras são implantadas no NSG de destino adicionando ou removendo regras para a seção **securityRules** no arquivo **template.json:**

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      Para concluir a adição ou a remoção das regras no NSG de destino, você também deve editar os tipos de regras personalizadas no final do arquivo **template.json** no formato do exemplo abaixo:

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. Clique em **Salvar** no editor on-line.

14. Clique na**assinatura** **BASICS** > para escolher a assinatura onde o NSG de destino será implantado.

15. Clique no**grupo de recursos** **BASICS** > para escolher o grupo de recursos onde o NSG de destino será implantado.  Você pode clicar em **Criar novo** para criar um novo grupo de recursos para o NSG de destino.  Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do NSG existente.

16. Verifique **o local básico** > **está** definido para o local de destino onde deseja que o NSG seja implantado.

17. Verifique em **CONFIGURAÇÕES** se o nome corresponde ao nome que você inseriu no editor de parâmetros acima.

18. Verifique a caixa em **TERMOS E CONDIÇÕES**.

19. Clique no botão **Comprar** para implantar o grupo de segurança da rede de destino.

## <a name="discard"></a>Descartar

Se desejar descartar o NSG de destino, exclua o grupo de recursos que contém o NSG de destino.  Para isso, selecione o grupo de recursos do painel no portal e **selecione Excluir** na parte superior da página de visão geral.

## <a name="clean-up"></a>Limpar

Para cometer as alterações e concluir a movimentação do NSG, exclua o NSG de origem ou o grupo de recursos. Para isso, selecione o grupo de segurança da rede ou o grupo de recursos do seu painel no portal e **selecione Excluir** no topo de cada página.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um grupo de segurança de rede do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Mover as VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
