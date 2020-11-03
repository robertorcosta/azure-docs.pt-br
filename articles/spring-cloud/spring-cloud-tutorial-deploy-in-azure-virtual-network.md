---
title: Tutorial – Implantar o Azure Spring Cloud na rede virtual
description: Implantar o Azure Spring Cloud na rede virtual (injeção de v-net).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: e0fc50647e926ea919f70b888f3efc303713fe1e
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631182"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>Tutorial: Implantar o Azure Spring Cloud na rede virtual do Azure (injeção de VNet)

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

Este tutorial explica como implantar uma instância de serviço de nuvem do Azure Spring Cloud em sua rede virtual. Isso às vezes é chamado de injeção de VNet.  

A implantação permite:

* Isolamento de aplicativos e runtime de serviço do Azure Spring Cloud da Internet em sua rede corporativa
* Interação do Azure Spring Cloud com sistemas em data centers locais e/ou serviços do Azure em outras redes virtuais
* Autorização de clientes para controlar comunicações de rede de entrada e saída para o Azure Spring Cloud

## <a name="prerequisites"></a>Prerequisites
Você deve registrar o provedor de recursos `Microsoft.AppPlatform` do Azure Spring Cloud de acordo com as instruções em [Registrar Provedor de Recursos no portal do Azure](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ou executando o seguinte comando az da CLI:

```azurecli
az provider register --namespace Microsoft.AppPlatform
```
## <a name="virtual-network-requirements"></a>Requisitos de rede virtual
A rede virtual na qual você implanta sua instância de serviço do Azure Spring Cloud deve atender aos seguintes requisitos:

* **Localização** : a rede virtual deve residir na mesma localização que a instância do serviço do Azure Spring Cloud.
* **Assinatura** : a rede virtual deve estar na mesma assinatura que a instância de serviço do Azure Spring Cloud.
* **sub-redes** : a rede virtual deve incluir duas sub-redes dedicadas a uma instância de serviço do Azure Spring Cloud: 
    * Uma do Runtime de Serviço
    * Um para seus Aplicativos de Microsserviço do Spring Boot. 
    * Há uma relação um-para-um entre essas sub-redes e uma instância de serviço do Azure Spring Cloud. Você deve usar uma nova sub-rede para cada instância de serviço implantada e cada sub-rede pode incluir apenas uma única instância de serviço.
* **Espaço de endereço** : Um bloco CIDR de até /28 para a sub-rede do Runtime de Serviço e outro bloco CIDR de até /24 para a sub-rede de Aplicativos de Microsserviço do Spring Boot.
* **Tabela de rotas** : as sub-redes não devem ter uma tabela de rotas existente associada.

Os procedimentos a seguir descrevem a configuração da rede virtual para conter a instância do Azure Spring Cloud.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Se você já tiver uma rede virtual para hospedar a instância de serviço do Azure Spring Cloud, pule as etapas 1, 2 e 3. Você pode começar da etapa 4 para preparar sub-redes para a rede virtual.

1. No menu do portal do Azure, selecione **Criar um recurso**. No Azure Marketplace, selecione **rede** > **rede virtual**.

1. Na caixa de diálogo **Criar rede virtual** , insira ou selecione as seguintes informações:

    |Configuração          |Valor                                             |
    |-----------------|--------------------------------------------------|
    |Subscription     |Selecione sua assinatura.                         |
    |Resource group   |Selecione seu grupo de recursos ou crie um.  |
    |Name             |Insira *azure-spring-cloud-vnet*                   |
    |Location         |Selecione **Leste dos EUA**                                |

1. Clique em **Avançar: Endereços IP >** . 
 
1. Para espaço de endereço IPv4, insira 10.1.0.0/16.

1. Selecione **Adicionar sub-rede** e insira *service-runtime-subnet* para **Nome da sub-rede** e 10.1.0.0/24 para **Intervalo de endereços de sub-rede**. Clique em **Adicionar**.

1. Selecione **Adicionar sub-rede** novamente e insira **Nome da sub-rede** para **Intervalo de endereços da sub-rede** , por exemplo, *apps-subnet* e 10.1.1.0/24.  Clique em **Adicionar**.

1. Clique em **Revisar + Criar**. Mantenha o restante com os padrões e clique em **Criar**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Conceder permissão de serviço para a rede virtual

Selecione a rede virtual *azure-spring-cloud-vnet* que você criou anteriormente.

1. Selecione **Controle de acesso (IAM)** e escolha **Adicionar > Adicionar atribuição de função**.

    ![Controle de acesso para v-net](./media/spring-cloud-v-net-injection/access-control.png)

2. Na caixa de diálogo **Adicionar atribuição de função** , insira ou selecione estas informações:

    |Configuração  |Valor                                             |
    |---------|--------------------------------------------------|
    |Função     |Selecione **Proprietário**                                  |
    |Selecionar   |Insira *Provedor de Recursos do Azure Spring Cloud*      |

    Em seguida, selecione *Provedor de Recursos do Azure Spring Cloud* e clique em **Salvar**.

    ![Conceder o provedor de recursos do Azure Spring Cloud para a v-net](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Você também pode obter isso executando o seguinte comando AZ da CLI

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>Implantar a instância de serviço do Azure Spring Cloud na rede virtual

1. Abra o portal do Azure em https://ms.portal.azure.com.

1. Na caixa de pesquisa superior, pesquise **Azure Spring Cloud** e selecione **Azure Spring Cloud** no resultado.

1. Na página do **Azure Spring Cloud** , clique em **+ Adicionar**.

1. Preencha o formulário na página **Criar** do Azure Spring Cloud. 

1. Selecione o mesmo grupo de recursos e região que a rede virtual.

1. Para **Nome** em **Detalhes do Serviço** , selecione *azure-spring-cloud-vnet*.

1. Selecione **Rede** e selecione o seguinte:

    |Configuração                                |Valor                                             |
    |---------------------------------------|--------------------------------------------------|
    |Implantar em sua rede virtual     |Selecione **Sim**                                    |
    |Rede virtual                        |Selecione *azure-spring-cloud-vnet*                  |
    |Sub-rede do runtime de serviço                 |Selecione *service-runtime-subnet*                   |
    |Sub-rede de aplicativos de microsserviço do Spring Boot   |Selecione *apps-subnet*                              |

    ![Guia Criação de rede](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Clique em **Examinar e criar**.

1. Verifique suas especificações e clique em **Criar**.

Após a implantação, dois grupos de recursos adicionais serão criados em sua assinatura para hospedar os recursos de rede para a instância de serviço do Azure Spring Cloud.  Navegue até **Página Inicial** e selecione **Grupos de recursos** nos itens de menu superiores para localizar os novos grupos de recursos a seguir.

O grupo de recursos nomeado como *ap-svc-rt_{nome da instância de serviço}_{região da instância de serviço}* contém recursos de rede para o Runtime de Serviço da instância de serviço.

  ![Runtime de serviço](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

O grupo de recursos nomeado como *ap-app_{nome da instância de serviço}_{região da instância de serviço}* contém recursos de rede para os Aplicativos de Microsserviço do Spring Boot da instância de serviço.

  ![Grupo de recursos dos aplicativos](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Esses recursos de rede estão conectados à sua rede virtual criada acima.

  ![V-net com dispositivo conectado](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Os grupos de recursos são totalmente gerenciados pelo serviço Azure Spring Cloud. NÃO exclua nem modifique manualmente nenhum recurso.

## <a name="next-steps"></a>Próximas etapas

[Implantar o aplicativo no Azure Spring Cloud em sua VNet](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Veja também

- [Solução de problemas do Azure Spring Cloud na VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Responsabilidades do cliente para executar o Azure Spring Cloud na VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)