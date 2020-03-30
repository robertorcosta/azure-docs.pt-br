---
title: Versões do Kubernetes com suporte no Serviço de Kubernetes do Azure
description: Entender a política de suporte de versão do Kubernetes e o ciclo de vida dos clusters no AKS (Serviço de Kubernetes do Azure)
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593437"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versões do Kubernetes com suporte no AKS (Serviço de Kubernetes do Azure)

A comunidade Kubernetes libera versões secundárias aproximadamente a cada três meses. Essas versões incluem novos recursos e melhorias. As versões de patch são mais frequentes (às vezes, semanais) e são destinadas apenas a correções de bugs críticas em uma versão secundária. Essas versões de patch incluem correções de vulnerabilidades de segurança ou de bugs importantes que afetam um grande número de clientes e produtos em execução na produção com base no Kubernetes.

A AKS pretende certificar e lançar novas versões do Kubernetes dentro de 30 dias após o lançamento upstream, sujeito à estabilidade do lançamento.

## <a name="kubernetes-versions"></a>Versões kubernetes

Kubernetes usa o esquema padrão de versão [semântica.](https://semver.org/) Isso significa que cada versão do Kubernetes segue este esquema de numeração:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Cada número na versão indica compatibilidade geral com a versão anterior:

* As principais versões mudam quando alterações de API incompatíveis ou compatibilidade retrovolta podem ser quebradas.
* Versões menores mudam quando são feitas alterações de funcionalidade que são retrocompatíveis com as outras versões menores.
* As versões do patch mudam quando as correções de bugs compatíveis com o retrocesso são feitas.

Os usuários devem procurar executar a versão mais recente da versão menor que estão executando, por exemplo, se o seu cluster de produção estiver em *1.12.14* e *1.12.15* for a versão de patch disponível para a série *1.12,* você deve atualizar para *1.12.15* assim que puder garantir que seu cluster esteja totalmente corrigido e suportado.

## <a name="kubernetes-version-support-policy"></a>Política de suporte de versão do Kubernetes

AKS suporta três versões menores do Kubernetes:

* A versão menor atual que é lançada em AKS (N)
* Duas versões menores anteriores. Cada versão secundária com suporte também dá suporte a dois patches estáveis.

Isto é conhecido como "N-2": (N (Última versão) - 2 (versões menores)).

Por exemplo, se o AKS introduzir *1.15.a* hoje, o suporte será fornecido para as seguintes versões:

Nova versão menor    |    Lista de versões suportadas
-----------------    |    ----------------------
1.15.a               |    1.15.a, 1.15.b, 1.14.c, 1.14.d, 1.13.e, 1.13.f

Onde ".letter" é representativo de versões de patch.

Para obter detalhes sobre as comunicações sobre mudanças e expectativas de versão, consulte "Comunicações" abaixo.

Quando uma nova versão menor é introduzida, a versão menor mais antiga e as versões de patch suportadas são depreciadas e removidas. Por exemplo, se a lista de versões suportadas atual for:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

E a AKS lança 1.16. *, isso significa que o 1.13.* as versões (todas as versões 1.13) serão removidas e estão sem suporte.

> [!NOTE]
> Observe que, se os clientes estiverem executando uma versão kubernetes sem suporte, eles serão solicitados a atualizar ao solicitar suporte para o cluster. Os clusters que executam lançamentos Kubernetes sem suporte não são cobertos pelas políticas de suporte da [AKS](https://docs.microsoft.com/azure/aks/support-policies).

Além do acima em versões menores, a AKS suporta as duas últimas **versões** de patch de uma determinada versão menor. Por exemplo, dadas as seguintes versões suportadas:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Se o Upstream Kubernetes lançou 1.15.3 e 1.14.6 e aKS lança essas versões de patch, as versões de patch mais antigas são depreciadas e removidas, e a lista de versões suportadas se torna:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Comunicações

* Para novas versões **menores** de Kubernetes
  * Todos os usuários são notificados publicamente da nova versão e qual versão será removida.
  * Quando uma nova versão de patch é lançada, a versão mais antiga do patch é removida ao mesmo tempo.
  * Os clientes têm **30 dias** a partir da data de notificação pública para atualizar para uma versão menor suportada.
* Para novas **versões** de patch do Kubernetes
  * Todos os usuários são notificados sobre a nova versão do patch sendo lançada e para atualizar para a versão mais recente do patch.
  * Os usuários têm **30 dias** para atualizar para uma versão de patch mais nova e suportada antes que o mais antigo seja removido.

A AKS define uma "versão lançada" como as versões geralmente disponíveis, habilitadas em todas as medições SLO /Quality of Service e disponíveis em todas as regiões. O AKS também pode suportar versões de pré-visualização que são explicitamente rotuladas e sujeitas a termos e condições de visualização.

#### <a name="notification-channels-for-aks-changes"></a>Canais de notificação para alterações aks

A AKS publica atualizações regulares de serviço que resumem novas versões do Kubernetes, alterações de serviço e atualizações de componentes que foram lançadas no serviço no [GitHub](https://github.com/Azure/AKS/releases).

Essas alterações são distribuídas para todos os clientes como parte da manutenção regular que é oferecida como parte do serviço gerenciado, alguns exigem upgrades explícitos enquanto outros não requerem nenhuma ação.

As notificações também são enviadas via:

* [Notas de versão do AKS](https://aka.ms/aks/releasenotes)
* Notificações do Portal do Azure
* [Canal de atualização do Azure][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Exceções de políticas de versões suportadas

A AKS reserva-se o direito de adicionar ou remover versões novas/existentes que foram identificadas para ter uma ou mais problemas críticos de produção impactando bugs ou problemas de segurança sem aviso prévio.

As liberações específicas de patch podem ser ignoradas ou a implantação acelerada dependendo da gravidade do bug ou do problema de segurança.

### <a name="azure-portal-and-cli-default-versions"></a>Versões padrão do portal azure e CLI

Quando você implanta um cluster AKS no portal ou com o Azure CLI, o cluster é padrão para a versão menor N-1 e o patch mais recente. Por exemplo, se a AKS suporta *1.15.a*, *1.15.b*, *1.14.c,* *1.14.d,* *1.13.e*, e *1.13.f*, a versão padrão selecionada é *1.14.c*.

O AKS escolhe o padrão de N-1 para fornecer aos clientes uma versão conhecida, estável e corrigida por padrão.

## <a name="list-currently-supported-versions"></a>Lista de versões compatíveis no momento

Para descobrir quais versões estão disponíveis atualmente para sua assinatura e região, use o comando [az aks get-versions][az-aks-get-versions]. O exemplo a seguir lista as versões disponíveis do Kubernetes para a região *EastUS*:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>Perguntas frequentes

**O que acontece quando um cliente atualiza um cluster do Kubernetes com uma versão secundária sem suporte?**

Se você estiver na versão *n-3,* você está fora do suporte e será solicitado a atualizar. Se o seu upgrade da versão n-3 para o n-2 for bem sucedido, você agora está dentro de nossas políticas de suporte. Por exemplo: 

- Se a versão AKS mais antiga suportada for *1.13.a* e você estiver em *1.12.b* ou mais velho, você está fora do suporte.
- Se o upgrade de *1.12.b* para *1.13.a* ou superior for bem-sucedido, você voltará às nossas políticas de suporte.

Upgrades para versões mais antigas do que a janela suportada do *N-2* não são suportados. Nesses casos, recomendamos que os clientes criem novos clusters AKS e reimplantem suas cargas de trabalho com versões na janela suportada.

**O que significa "Fora do Suporte"**

'Fora do suporte' significa que a versão que você está executando está fora da lista de versões suportadas, e você será solicitado a atualizar o cluster para uma versão suportada ao solicitar suporte. Além disso, a AKS não faz nenhuma garantia de tempo de execução ou outras garantias para clusters fora da lista de versões suportadas.

**O que acontece quando um cliente dimensiona um cluster do Kubernetes com uma versão secundária sem suporte?**

Para versões menores não suportadas pelo AKS, o dimensionamento para dentro ou para fora deve continuar a funcionar, mas é altamente recomendável atualizar para trazer seu cluster de volta ao suporte.

**Um cliente pode permanecer em uma versão do Kubernetes para sempre?**

Sim. No entanto, se o cluster não estiver em uma das versões suportadas pela AKS, o cluster está fora das políticas de suporte da AKS. O Azure não atualiza nem exclui o cluster automaticamente.

**Que versão o avião de controle suporta se o pool de nós não estiver em uma das versões AKS suportadas?**

O plano de controle deve estar dentro de uma janela de versões de todas as piscinas de nós. Para obter detalhes sobre como atualizar o plano de controle ou piscinas de nó, visite a documentação sobre [a atualização de pools de nó](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

## <a name="next-steps"></a>Próximas etapas

Para obter informações de como atualizar seu cluster, confira [Atualizar um cluster do AKS (Serviço de Kubernetes do Azure)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
