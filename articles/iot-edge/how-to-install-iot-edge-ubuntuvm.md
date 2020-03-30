---
title: Executar o Azure IoT Edge em máquinas virtuais do Ubuntu | Microsoft Docs
description: Instruções de configuração do Azure IoT Edge para máquinas virtuais Ubuntu 18.04 LTS
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: pdecarlo
ms.openlocfilehash: 64e2787aa282e75893fa34e6de1373e6afed09fe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349630"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Executar Azure IoT Edge em Máquinas Virtuais do Ubuntu

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O runtime pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o runtime do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele.

Para saber mais sobre como funciona o runtime do IoT Edge e quais componentes estão incluídos, veja [Entender o runtime do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para implantar uma máquina virtual Ubuntu 18.04 LTS com o tempo de execução do Azure IoT Edge instalado e configurado usando uma seqüência de conexão de dispositivo pré-fornecida. A implantação é realizada usando um modelo de Gerenciador de [Recursos Azure](../azure-resource-manager/templates/overview.md) baseado [em nuvem](../virtual-machines/linux/using-cloud-init.md
) mantido no repositório de projetos [iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy)

Na primeira inicialização, a máquina virtual Ubuntu 18.04 LTS [instala a versão mais recente do tempo de execução do Azure IoT Edge via cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Ele também define uma seqüência de conexão fornecida antes do tempo de execução começar, permitindo que você configure e conecte facilmente o dispositivo IoT Edge sem a necessidade de iniciar uma sessão de SSH ou desktop remota. 

## <a name="deploy-using-deploy-to-azure-button"></a>Implantar usando o botão Implantar no Azure

O [botão Deploy to Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) permite a implantação simplificada de modelos do [Azure Resource Manager mantidos](../azure-resource-manager/templates/overview.md) no GitHub.  Esta seção demonstrará o uso do botão Deploy to Azure contido no repositório de projeto [iotedge-vm deploy.](https://github.com/Azure/iotedge-vm-deploy)  


1. Implantaremos um Azure IoT Edge habilitado para Linux VM usando o modelo iotedge-vm-deploy Azure Resource Manager.  Para começar, clique no botão abaixo:

    [![Implantar no botão Azure para iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Na janela recém-lançada, preencha os campos de formulário disponíveis:

    > [!div class="mx-imgBorder"]
    > [![Captura de tela mostrando o modelo iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Assinatura**: A assinatura ativa do Azure para implantar a máquina virtual em.

    **Grupo de recursos**: Um grupo de recursos existente ou recém-criado para conter a máquina virtual e seus recursos associados.

    **Prefixo de etiqueta DNS**: Um valor necessário de sua escolha que é usado para prefixar o nome de host da máquina virtual.

    **Nome de usuário do admin**: Um nome de usuário, que será fornecido privilégios raiz na implantação.

    **String de conexão do dispositivo**: uma [seqüência de conexão de dispositivo](how-to-register-device.md) para um dispositivo que foi criado dentro do seu Hub [IoT](../iot-hub/about-iot-hub.md)pretendido .

    **Tamanho VM**: O [tamanho](../cloud-services/cloud-services-sizes-specs.md) da máquina virtual a ser implantada

    **Versão do Ubuntu OS**: A versão do Sistema Operacional Ubuntu a ser instalada na máquina virtual base.

    **Localização**: A [região geográfica](https://azure.microsoft.com/global-infrastructure/locations/) para implantar a máquina virtual em, esse valor é padrão para a localização do Grupo de Recursos selecionado.

    **Tipo de autenticação**: Escolha **sshPublicKey** ou **senha,** dependendo da sua preferência.

    Senha ou Chave de **Admin**: O valor da Chave Pública SSH ou o valor da senha, dependendo da escolha do Tipo de Autenticação.

    Quando todos os campos tiverem sido preenchidos, selecione a caixa de seleção na parte inferior da página para aceitar os termos e selecione **Comprar** para iniciar a implantação.

1. Verifique se a implantação foi concluída com sucesso.  Um recurso de máquina virtual deveria ter sido implantado no grupo de recursos selecionado.  Anote o nome da máquina, isso `vm-0000000000000`deve estar no formato . Além disso, tome nota do **Nome DNS**associado, que deve estar no formato `<dnsLabelPrefix>`. `<location>`cloudapp.azure.com.

    O **nome DNS** pode ser obtido na seção **Visão geral** da máquina virtual recém-implantada dentro do portal Azure.

    > [!div class="mx-imgBorder"]
    > [![Captura de tela mostrando o nome dns do iotedge vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Se você quiser SSH nesta VM após a configuração, use o **Nome DNS** associado com o comando:`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Implantar da CLI do Azure

1. Certifique-se de que você instalou a extensão iot azure CLI com:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Em seguida, se você estiver usando o Azure CLI na sua área de trabalho, comece fazendo login:

   ```azurecli-interactive
   az login
   ```

1. Se você tiver várias assinaturas, selecione a assinatura que deseja usar:
   1. Liste suas assinaturas:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Copie o campo SubscriptionID para a assinatura que você gostaria de usar.

   1. Defina sua assinatura de trabalho com o ID que você copiou:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Crie um novo grupo de recursos (ou especifique um existente nas próximas etapas):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Crie uma nova máquina virtual:

    Para usar uma **autenticaçãoTipo** de `password`, veja o exemplo abaixo:

   ```azurecli-interactive
   az group deployment create \
   --name edgeVm \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Para autenticar com uma chave SSH, você pode fazê-lo `sshPublicKey`especificando um tipo de **autenticação** tipo de , em seguida, fornecer o valor da chave SSH no parâmetro **adminPasswordOrKey.**  Um exemplo é mostrado abaixo.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az group deployment create \
    --name edgeVm \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
     
    ```

1. Verifique se a implantação foi concluída com sucesso.  Um recurso de máquina virtual deveria ter sido implantado no grupo de recursos selecionado.  Anote o nome da máquina, isso `vm-0000000000000`deve estar no formato . Além disso, tome nota do **Nome DNS**associado, que deve estar no formato `<dnsLabelPrefix>`. `<location>`cloudapp.azure.com.

    O **nome DNS** pode ser obtido a partir da saída formatado pelo JSON da etapa anterior, dentro da seção **de saídas** como parte da entrada **pública do SSH.**  O valor desta entrada pode ser usado para SSH na máquina recém-implantada.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    O **nome DNS** também pode ser obtido na seção **Visão Geral** da máquina virtual recém-implantada dentro do portal Azure.

    > [!div class="mx-imgBorder"]
    > [![Captura de tela mostrando o nome dns do iotedge vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Se você quiser SSH nesta VM após a configuração, use o **Nome DNS** associado com o comando:`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge provisionado com o runtime instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com a instalação de tempo de execução do IoT Edge corretamente, confira a página [de solução de problemas.](troubleshoot.md)

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o runtime do IoT Edge](how-to-update-iot-edge.md).

Se você quiser abrir portas para acessar a VM através de SSH ou outras conexões de entrada, consulte a documentação do Azure Virtual Machines sobre [a abertura de portas e pontos finais para uma VM Linux](../virtual-machines/linux/nsg-quickstart.md)
