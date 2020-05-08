---
title: Configurar regras de firewall de serviço
description: Configure as regras de IP para habilitar o acesso a um registro de contêiner do Azure de endereços IP públicos selecionados ou intervalos de endereços.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: f6459061ca486b4bf229409e6ec1ed1bd808a474
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984610"
---
# <a name="configure-public-ip-network-rules"></a>Configurar regras de rede IP pública

Por padrão, um registro de contêiner do Azure aceita conexões pela Internet de hosts em qualquer rede. Este artigo mostra como configurar seu registro de contêiner para permitir o acesso somente de endereços IP públicos específicos ou intervalos de endereços. São fornecidas etapas equivalentes usando o CLI do Azure e portal do Azure.

As regras de rede IP são configuradas no ponto de extremidade do registro público. As regras de rede IP não se aplicam a pontos de extremidade privados configurados com o [link privado](container-registry-private-link.md)

A configuração de regras de acesso IP está disponível na camada de serviço do registro de contêiner **Premium** . Para obter informações sobre limites e camadas de serviço do registro, consulte [camadas do registro de contêiner do Azure](container-registry-skus.md).

## <a name="access-from-selected-public-network---cli"></a>Acesso da rede pública selecionada-CLI

### <a name="change-default-network-access-to-registry"></a>Alterar o acesso de rede padrão ao registro

Para limitar o acesso a uma rede pública selecionada, primeiro altere a ação padrão para negar acesso. Substitua o nome do registro no seguinte comando [AZ ACR Update][az-acr-update] :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registro

Use o comando [AZ ACR Network-Rule Add][az-acr-network-rule-add] para adicionar uma regra de rede ao registro que permite o acesso de um intervalo ou endereço IP público. Por exemplo, substitua o nome do registro de contêiner e o endereço IP público de uma VM em uma rede virtual.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Depois de adicionar uma regra, levará alguns minutos para que a regra entre em vigor.

## <a name="access-from-selected-public-network---portal"></a>Acesso da rede pública selecionada-Portal

1. No portal, navegue até o registro de contêiner.
1. Em **configurações**, selecione **rede**.
1. Na guia **acesso público** , selecione para permitir acesso público de **redes selecionadas**.
1. Em **Firewall**, insira um endereço IP público, como o endereço IP público de uma VM em uma rede virtual. Ou insira um intervalo de endereços na notação CIDR que contém o endereço IP da VM.
1. Selecione **Salvar**.

![Configurar regra de firewall para registro de contêiner][acr-access-selected-networks]

> [!NOTE]
> Depois de adicionar uma regra, levará alguns minutos para que a regra entre em vigor.

> [!TIP]
> Opcionalmente, habilite o acesso ao registro de um computador cliente local ou intervalo de endereços IP. Para permitir esse acesso, você precisa do endereço IPv4 público do computador. Você pode encontrar esse endereço pesquisando "o que é meu endereço IP" em um navegador da Internet. O endereço IPv4 do cliente atual também aparece automaticamente quando você define as configurações de firewall na página **rede** no Portal.

## <a name="disable-public-network-access"></a>Desabilitar o acesso à rede pública

Para limitar o tráfego para redes virtuais usando o [link privado](container-registry-private-link.md), desabilite o ponto de extremidade público no registro. Desabilitar o ponto de extremidade público substitui todas as configurações de firewall.

### <a name="disable-public-access---portal"></a>Desabilitar acesso público-Portal

1. No portal, navegue até o registro de contêiner e selecione **configurações > rede**.
1. Na guia **acesso público** , em **permitir acesso público**, selecione **desabilitado**. Em seguida, selecione **Salvar**.

![Desabilitar acesso público][acr-access-disabled]

## <a name="restore-default-registry-access"></a>Restaurar o acesso padrão do registro

Para restaurar o registro para permitir o acesso por padrão, atualize a ação padrão. 

### <a name="restore-default-registry-access---portal"></a>Restaurar acesso padrão do registro-Portal

1. No portal, navegue até o registro de contêiner e selecione **configurações > rede**.
1. Em **Firewall**, selecione cada intervalo de endereços e, em seguida, selecione o ícone Excluir.
1. Na guia **acesso público** , em **permitir acesso público**, selecione **todas as redes**. Em seguida, selecione **Salvar**.

![Acesso público de todas as redes][acr-access-all-networks]

## <a name="next-steps"></a>Próximas etapas

* Para restringir o acesso a um registro usando um ponto de extremidade privado em uma rede virtual, consulte [Configurar o link privado do Azure para um registro de contêiner do Azure](container-registry-private-link.md).
* Se você precisar configurar as regras de acesso do registro por trás de um firewall do cliente, consulte [configurar regras para acessar um registro de contêiner do Azure atrás de um firewall](container-registry-firewall-access-rules.md).

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
