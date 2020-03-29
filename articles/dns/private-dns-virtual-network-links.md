---
title: O que é um subrecurso de link de rede virtual de regiões privadas do Azure DNS
description: Visão geral do subrecurso de link de rede virtual a zona privada DoZure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646754"
---
# <a name="what-is-a-virtual-network-link"></a>O que é um link de rede virtual?

Uma vez que você cria uma zona de DNS privada no Azure, ela não é imediatamente acessível a partir de qualquer rede virtual. Você deve vinculá-lo a uma rede virtual antes que uma VM hospedada nessa rede possa acessar a zona de DNS privada.
Para vincular uma zona de DNS privada a uma rede virtual, você deve criar um link de rede virtual a zona dns privada. Cada zona privada de DNS tem uma coleção de recursos infantis de link de rede virtual. Cada um desses recursos representa uma conexão com uma rede virtual.

Você pode vincular uma rede virtual a uma zona de DNS privada como uma rede virtual de registro ou como uma rede virtual de resolução.

## <a name="registration-virtual-network"></a>Cadastro de rede virtual

Quando você [cria um link](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) entre uma zona DNS privada e uma rede virtual, você tem a opção de ativar o registro [automático](./private-dns-autoregistration.md) de registros DNS para máquinas virtuais. Se você escolher essa opção, a rede virtual se tornará uma rede virtual de registro para a região privada de DNS. Um registro DNS é criado automaticamente para as máquinas virtuais que você implanta na rede. Os registros DNS são criados para as máquinas virtuais que você já implantou na rede virtual. Do ponto de vista da rede virtual, a zona de DNS privada torna-se a zona de registro dessa rede virtual.
Uma zona de DNS privada pode ter várias redes virtuais de registro, no entanto, cada rede virtual pode ter exatamente uma zona de registro associada a ela.

## <a name="resolution-virtual-network"></a>Resolução de rede virtual

Quando você cria um link de rede virtual em uma zona DNS privada e opta por não ativar o registro automático de registro de DNS, a rede virtual é tratada como uma rede virtual de resolução. Os registros de DNS para máquinas virtuais implantadas nessas redes não serão criados automaticamente na zona de DNS privada vinculada. No entanto, as máquinas virtuais implantadas em tal rede podem consultar com sucesso os registros DeDN da região dns privada. Esses registros podem ser criados manualmente por você ou podem ser preenchidos a partir de outras redes virtuais que foram vinculadas como redes de registro com a zona dns privada.
Uma zona de DNS privada pode ter várias redes virtuais de resolução e uma rede virtual pode ter várias zonas de resolução associadas a ela.

## <a name="limits"></a>limites

Para entender quantas redes de registro e resolução, você pode vincular a zonas privadas de DNS ver [Limites de DNS do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="other-considerations"></a>Outras considerações

* As redes virtuais implantadas usando o modelo clássico de implantação não são suportadas.

* Você pode criar apenas um link entre uma zona DNS privada e uma rede virtual.

* Cada link de rede virtual uma zona DNS privada deve ter um nome único dentro do contexto da zona DNS privada. Você pode ter links com o mesmo nome em diferentes zonas privadas de DNS.

* Depois de criar um link de rede virtual, verifique o campo "Status de link" do recurso de link de rede virtual. Dependendo do tamanho da rede virtual, pode levar alguns minutos até que o link seja operacionalizado e as alterações do status do link para *Concluído*.

* Quando você exclui uma rede virtual, todos os links de rede virtuais e registros DeDNs registrados automaticamente associados a ela em diferentes zonas de DNS privadas são automaticamente excluídos.

## <a name="next-steps"></a>Próximas etapas

* Saiba como vincular uma rede virtual a uma zona dns privada usando [o portal Azure](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)

* Saiba como criar uma zona privada no DNS do Azure usando o [Azure PowerShell](./private-dns-getstarted-powershell.md) ou a [CLI do Azure](./private-dns-getstarted-cli.md).

* Leia mais sobre alguns [cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas no DNS do Azure.

* Para ver perguntas comuns e obter respostas sobre zonas privadas no DNS do Azure, incluindo o comportamento específico que se pode esperar para determinados tipos de operações, confira as [Perguntas frequentes sobre DNS privado](./dns-faq-private.md).
