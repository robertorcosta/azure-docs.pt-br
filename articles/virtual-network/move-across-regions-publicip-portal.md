---
title: Mova o Azure Public IP para outra região do Azure usando o portal Azure
description: Use o modelo do Azure Resource Manager para mover o IP público do Azure de uma região do Azure para outra usando o portal Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 6d10265e8383b68ebe13c95d8b2a9632668e85da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641385"
---
# <a name="move-azure-public-ip-to-another-region-using-the-azure-portal"></a>Mova o IP público do Azure para outra região usando o portal Azure

Existem vários cenários em que você gostaria de mover seus IPs Públicos Azure existentes de uma região para outra. Por exemplo, você pode querer criar um IP público com a mesma configuração e sku para testes. Você também pode querer mover um IP público para outra região como parte do planejamento de recuperação de desastres.

Os IPs Públicos do Azure são específicos da região e não podem ser movidos de uma região para outra. No entanto, você pode usar um modelo do Azure Resource Manager para exportar a configuração existente de um IP público.  Em seguida, você pode encenar o recurso em outra região exportando o IP público para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantar o modelo para a nova região.  Para obter mais informações sobre gerenciador de recursos e modelos, consulte [Quickstart: Crie e implante modelos do Azure Resource Manager usando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o IP público do Azure está na região do Azure de onde você deseja se mudar.

- Os IPs Públicos do Azure não podem ser movidos entre regiões.  Você terá que associar o novo ip público aos recursos na região alvo.

- Para exportar uma configuração de IP pública e implantar um modelo para criar um IP público em outra região, você precisará da função Contribuinte de rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Esse layout inclui, mas não se limita a balanceadores de carga, grupos de segurança de rede (NSGs) e redes virtuais.

- Verifique se sua assinatura do Azure permite que você crie IPs públicos na região de destino que é usada. Contate o suporte para habilitar a cota necessária.

- Certifique-se de que sua assinatura tenha recursos suficientes para suportar a adição de IPs públicos para este processo.  Consulte [Assinatura do Azure e limites de serviço, cotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Prepare-se e mova-se
As etapas a seguir mostram como preparar o IP público para o movimento de configuração usando um modelo de Gerenciador de recursos e mover a configuração pública de IP para a região de destino usando o portal Azure.

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportar o modelo e implantar a partir de um script

1. Faça login no [portal Azure](https://portal.azure.com) > **Grupos de Recursos**.
2. Localize o Grupo de recursos que contém o IP público de origem e clique nele.
3. Selecione >**modelo de exportação** **de configurações** > .
4. Escolha **Implantar** na lâmina **do modelo Exportar.**
5. Clique **EM EDITAR** > **parâmetros** para abrir o arquivo **parameters.json** no editor on-line.
8. Para editar o parâmetro do nome IP público, altere a propriedade o**valor** de **parâmetros** > do nome IP público de origem para o nome do SEU IP público alvo, certifique-se de que o nome está entre aspas:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  Clique em **Salvar** no editor.

9.  Clique **EM EDITAR** > **modelo** para abrir o arquivo **template.json** no editor on-line.

10. Para editar a região de destino para onde o IP público será movido, altere a propriedade de **localização** em **recursos**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. Para obter códigos de localização da região, consulte [Azure Localizações](https://azure.microsoft.com/global-infrastructure/locations/).  O código para uma região é o nome da região sem espaços, **central dos EUA.** = **centralus**

12. Você também pode alterar outros parâmetros no modelo se você escolher, e são opcionais dependendo de seus requisitos:

    * **Sku** - Você pode alterar o sku do IP público na configuração de padrão para básico ou básico para padrão alterando a propriedade **sku** > **name** no arquivo **template.json:**

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Para obter mais informações sobre as diferenças entre ips públicos sku básicos e padrão, consulte [Criar, alterar ou excluir um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address):

    * **Método de alocação pública de IP** e **tempo limite de ociosidade** - Você pode alterar ambas as opções no modelo alterando a propriedade **publicIPAllocationMethod** de **Dinâmica** para **Estática** ou **Estática** para **Dinâmica**. O tempo de espera ocioso pode ser alterado alterando a propriedade **idleTimeoutInMinutes** para a quantidade desejada.  O padrão é **4**:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Para obter mais informações sobre os métodos de alocação e os valores de tempo de tempo inocioso, consulte [Criar, alterar ou excluir um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


13. Clique em **Salvar** no editor on-line.

14. Clique **em Assinatura BÁSICA** > **Para** escolher a assinatura onde o IP público-alvo será implantado.

15. Clique no**grupo de recursos** **BASICS** > para escolher o grupo de recursos onde o IP público-alvo será implantado.  Você pode clicar em **Criar novo** para criar um novo grupo de recursos para o IP público-alvo.  Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do IP público de origem existente.

16. Verifique **o local básico** > **está** definido para o local de destino onde deseja que o IP público seja implantado.

17. Verifique em **CONFIGURAÇÕES** se o nome corresponde ao nome que você inseriu no editor de parâmetros acima.

18. Verifique a caixa em **TERMOS E CONDIÇÕES**.

19. Clique no botão **Comprar** para implantar o IP público alvo.

## <a name="discard"></a>Descartar

Se você deseja descartar o IP público de destino, exclua o grupo de recursos que contém o IP público de destino.  Para isso, selecione o grupo de recursos do painel no portal e **selecione Excluir** na parte superior da página de visão geral.

## <a name="clean-up"></a>Limpar

Para cometer as alterações e concluir a movimentação do IP público, exclua o IP público de origem ou o grupo de recursos. Para isso, selecione o IP público ou o grupo de recursos do seu painel no portal e **selecione Excluir** no topo de cada página.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um IP público do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Mover as VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
