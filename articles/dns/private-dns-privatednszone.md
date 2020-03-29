---
title: O que é uma zona privada Do Zure DNS
description: Visão geral de uma zona de DNS privada
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: a951bc07c4a8ed42b1c116332d13674656bbaafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646788"
---
# <a name="what-is-a-private-azure-dns-zone"></a>O que é uma zona privada de DNS do Azure

O DNS privado do Azure fornece um serviço DNS confiável e seguro para gerenciar e resolver nomes de domínio em uma rede virtual sem a necessidade de adicionar uma solução DNS personalizada. Ao usar zonas DNS privadas você poderá usar seus próprios nomes de domínio personalizados, em vez dos nomes fornecidos pelo Azure atualmente disponíveis. 

Os registros contidos em uma zona dns privada não são solucionáveis da Internet. A resolução de DNS contra uma zona DNS privada funciona apenas a partir de redes virtuais vinculadas a ela.

Você pode vincular uma região de DNS privada a uma ou mais redes virtuais criando [links de rede virtuais](./private-dns-virtual-network-links.md).
Você também pode habilitar o recurso [de registro automático](./private-dns-autoregistration.md) para gerenciar automaticamente o ciclo de vida dos registros DeDPara as máquinas virtuais implantadas em uma rede virtual.

## <a name="limits"></a>limites

Para entender quantas zonas de DNS privadas você pode criar em uma assinatura e quantos conjuntos de registros são suportados em uma zona DNS privada, consulte [os limites do DNS do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>Restrições

* As zonas DNS privadas com rótulo único não são suportadas. Sua zona de DNS privada deve ter dois ou mais rótulos. Por exemplo, contoso.com tem dois rótulos separados por um dot. Uma zona de DNS privada pode ter no máximo 34 rótulos.
* Você não pode criar delegações de zona (registros NS) em uma zona de DNS privada. Se você pretende usar um domínio filho, você pode criar diretamente o domínio como uma zona DNS privada e vinculá-lo à rede virtual sem configurar uma delegação de servidor de nomes da região pai.

## <a name="next-steps"></a>Próximas etapas

* Saiba como criar uma zona privada no DNS do Azure usando o [Azure PowerShell](./private-dns-getstarted-powershell.md) ou a [CLI do Azure](./private-dns-getstarted-cli.md).

* Leia mais sobre alguns [cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas no DNS do Azure.

* Para ver perguntas comuns e obter respostas sobre zonas privadas no DNS do Azure, incluindo o comportamento específico que se pode esperar para determinados tipos de operações, confira as [Perguntas frequentes sobre DNS privado](./dns-faq-private.md).
