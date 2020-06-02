---
title: Tutorial – Implantar o Cluster de vSphere no Azure
description: Saiba como implantar um Cluster de vSphere no Azure usando a AVS (Solução VMWare no Azure)
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: fc753f43563650357cf43c102e94f0057b62a406
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873745"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Tutorial: Implantar uma nuvem privada da AVS no Azure

A AVS (Solução VMware no Azure) permite implantar um cluster de vSphere no Azure. A implantação inicial mínima é de três hosts. Mais hosts podem ser adicionados, um de cada vez, até atingir um máximo de 16 hosts por cluster. 

Como a AVS não permite que você gerencie sua nuvem privada com o vCenter local na inicialização, você precisará fazer a configuração adicional e a conexão com uma instância do vCenter local, a rede virtual, entre outros. Esses procedimentos e os pré-requisitos relacionados serão abordados nesta série de tutoriais.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma nuvem privada da AVS
> * Verificar a nuvem privada implantada

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Direitos administrativos apropriados e permissão para criar uma nuvem privada.
- Verifique se você tem a rede adequada configurada, conforme descrito no [Tutorial: Lista de verificação de rede](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registre o provedor de recursos

Para usar a solução VMware no Azure, primeiro você precisa registrar o provedor de recursos. O exemplo a seguir registra o provedor de recursos com sua assinatura.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Para conhecer outras maneiras de registrar o provedor de recursos, confira [Provedores e tipos de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Criar uma nuvem privada

Você pode criar uma nuvem privada da AVS usando o [portal do Azure](#azure-portal) ou a [CLI do Azure](#azure-cli).

### <a name="azure-portal"></a>Portal do Azure

No Portal do Azure, selecione **+ Criar um recurso**. Na caixa de texto **Pesquisar no Marketplace**, digite `Azure VMware Solution` e selecione **Solução VMware no Azure** na lista. Na janela **Solução VMware no Azure**, selecione **Criar**

Na guia **Básico**, insira valores para os campos. A tabela a seguir mostra uma lista detalhada das propriedades.

| Campo   | Valor  |
| ---| --- |
| **Assinatura** | A assinatura que você planeja usar para a implantação.|
| **Grupo de recursos** | O grupo de recursos com os recursos de sua nuvem privada. |
| **Localidade** | Selecione uma localização, como **Leste dos EUA**.|
| **Nome do recurso** | O nome de sua nuvem privada da AVS. |
| **SKU** | Selecione o seguinte valor do SKU: AV36 |
| **Hosts** | Este é o número de hosts a serem adicionados ao cluster da nuvem privada. O valor padrão é 3. Esse valor pode ser aumentado ou reduzido após a implantação.  |
| **Senha de administrador do vCenter** | Insira uma senha de administrador da nuvem. |
| **Senha do NSX-T Manager** | Insira uma senha de administrador do NSX-T. |
| **Bloco de endereços** | Insira um bloco de endereços IP para a rede CIDR da nuvem privada. Um exemplo é 10.175.0.0/22. |

:::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="criar uma nuvem privada" border="true":::

Quando terminar, selecione **Examinar + Criar**. Na tela a seguir, verifique as informações inseridas. Se todas estiverem corretas, selecione **Criar**.

> [!NOTE]
> Essa etapa leva aproximadamente duas horas. 

### <a name="azure-cli"></a>CLI do Azure

Como alternativa, você pode usar o CLI do Azure para criar uma nuvem privada da AVS no Azure. Para fazer isso, você pode usar o Azure Cloud Shell. As etapas a seguir mostram como fazer isso.

#### <a name="open-azure-cloud-shell"></a>Abrir o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até https://shell.azure.com/bash. Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione **Enter** para executá-lo.

#### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Criar uma nuvem privada

Para criar uma nuvem privada da AVS, você precisa fornecer um nome do grupo de recursos, um nome para a nuvem privada, uma localização e o tamanho do cluster


|Propriedade  |Descrição  |
|---------|---------|
|Nome do Grupo de Recursos     | O nome do grupo de recursos no qual você está implantando a nuvem privada.        |
|Nome da Nuvem Privada     | O nome da nuvem privada.        |
|Location     | A localização usada para a nuvem privada         |
|Tamanho do cluster     | O tamanho do cluster. O valor mínimo é 3.         |
|Bloco de rede     | O intervalo CIDR a ser usado para a nuvem privada. É recomendável que ele seja exclusivo para seu ambiente local, bem como para seu ambiente do Azure.        |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22
```

## <a name="verify-deployment-was-successful"></a>Verificar se a implantação foi bem-sucedida

Navegue até o grupo de recursos que foi criado e selecione sua nuvem privada. Quando a implantação for concluída, você verá a tela a seguir e o status de **Êxito**.

:::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Validar a nuvem privada implantada" border="true":::

## <a name="delete-a-private-cloud"></a>Excluir uma nuvem privada

Se tiver uma nuvem privada da AVS que você verificou que não é mais necessária, você poderá excluí-la. Quando você exclui uma nuvem privada, todos os clusters juntamente com todos os seus componentes são excluídos.

Para fazer isso, navegue até a nuvem privada no portal do Azure e selecione **Excluir**. Na página de confirmação, confirme o nome da nuvem privada e selecione **Sim**.

> [!CAUTION]
> A exclusão da nuvem privada é uma operação irreversível. Após a nuvem privada ser excluída, os dados não poderão ser recuperados, pois todos os componentes e cargas de trabalho em execução serão terminados e todos os dados e configurações da nuvem privada serão destruídos, incluindo os endereços IP públicos. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma nuvem privada da AVS
> * Verificou a nuvem privada implantada

Continue para o próximo tutorial para saber como criar uma rede virtual para uso com sua nuvem privada como parte da configuração do gerenciamento local para seus clusters de nuvem privada.

> [!div class="nextstepaction"]
> [Criar uma rede virtual](tutorial-configure-networking.md)
