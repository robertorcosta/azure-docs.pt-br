---
title: Vida útil do objeto e dos recursos
description: Explica o gerenciamento de vida para diferentes tipos
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681864"
---
# <a name="object-and-resource-lifetime"></a>Vida útil do objeto e dos recursos

A renderização remota do Azure distingue entre dois tipos: **objetos** e **recursos.**

## <a name="object-lifetime"></a>Vida útil do objeto

*Os objetos* são considerados coisas que o usuário pode criar, modificar e destruir a seu próprio critério. Os objetos podem ser duplicados livremente e cada instância pode sofrer mutações ao longo do tempo. [Consequentemente, entidades](entities.md) e [componentes](components.md) são objetos.

A vida útil dos objetos está totalmente sob controle do usuário. No entanto, não está relacionado com a vida da representação do lado do cliente. As `Entity` classes `Component` gostam `Destroy` e têm uma função que deve ser chamada para desalocar o objeto no host de renderização remota. Além disso, `Entity.Destroy()` destruirá a entidade, seus filhos e todos os componentes dessa hierarquia.

## <a name="resource-lifetime"></a>Vida útil dos recursos

*Recursos* são coisas cuja vida é inteiramente gerenciada pelo host de renderização remota. Os recursos são referências contadas internamente. Eles são desalocados quando ninguém os faz referências por mais tempo.

A maioria dos recursos só pode ser criada indiretamente, normalmente carregando-os a partir de um arquivo. Quando o mesmo arquivo for carregado várias vezes, a renderização remota do Azure retornará a mesma referência e não carregará os dados novamente.

Muitos recursos são imutáveis, por exemplo, [malhas](meshes.md) e [texturas.](textures.md) Alguns recursos são mutáveis, porém, por exemplo, [materiais.](materials.md) Como os recursos são frequentemente compartilhados, modificar um recurso pode afetar vários objetos. Por exemplo, mudar a cor de um material mudará a cor de todos os objetos que usam valinhas, que por sua vez fazem referência a esse material.

### <a name="built-in-resources"></a>Recursos internos

A renderização remota do Azure contém alguns recursos incorporados, que `builtin://` podem ser `AzureSession.Actions.LoadXYZAsync()`carregados antecipando seu respectivo identificador durante a chamada para . Os recursos incorporados disponíveis estão listados na documentação de cada recurso respectivo. Por exemplo, o [capítulo do céu](../overview/features/sky.md) lista as texturas do céu incorporada.

## <a name="general-lifetime"></a>Vida útil geral

A vida útil de todos os objetos e recursos está ligada à conexão. Na desconexão tudo é descartado. Ao se reconectar à mesma sessão, o gráfico de cena estará vazio e todos os recursos serão eliminados.

Na prática, carregar o mesmo recurso em uma sessão, após uma desconexão, geralmente é mais rápido do que na primeira vez. Esse é o caso porque a maioria dos recursos deve ser baixada do Azure Storage na primeira vez, o que não é necessário na segunda vez, economizando um tempo considerável.

## <a name="next-steps"></a>Próximas etapas

* [Entities](entities.md)
* [Componentes](components.md)
* [Modelos](models.md)
