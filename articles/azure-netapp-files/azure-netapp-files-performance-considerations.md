---
title: Considerações de desempenho para arquivos do Azure NetApp | Microsoft Docs
description: Descreve considerações de desempenho para arquivos do Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67454133"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Considerações de desempenho para o Azure NetApp Files

O [limite de throughput](azure-netapp-files-service-levels.md) para um volume é determinado por uma combinação da cota atribuída ao volume e ao nível de serviço selecionado. Quando você faz planos de desempenho sobre o Azure NetApp Files, você precisa entender várias considerações. 

## <a name="quota-and-throughput"></a>Cota e throughput  

O limite de rendimento é apenas um determinante do desempenho real que será realizado.  

Considerações típicas de desempenho de armazenamento, incluindo mix de leitura e gravação, o tamanho da transferência, padrões aleatórios ou seqüenciais e muitos outros fatores contribuirão para o desempenho total entregue.  

O throughput empírico máximo observado nos testes é de 4.500 MiB/s.  No nível de armazenamento Premium, uma cota de volume de 70,31 TiB irá provisionar um limite de rendimento que é alto o suficiente para atingir esse nível de desempenho.  

Se você estiver considerando atribuir valores de cota de volume além de 70,31 TiB, a cota adicional pode ser atribuída a um volume para armazenar dados adicionais. No entanto, o contingente adicionado não resultará em um aumento adicional no throughput real.  

Consulte [os benchmarks de desempenho dos arquivos do Azure NetApp](azure-netapp-files-performance-benchmarks.md) para obter informações adicionais.

## <a name="overprovisioning-the-volume-quota"></a>Provisionamento excessivo da cota de volume

Se o desempenho de uma carga de trabalho estiver vinculado ao limite de rendimento, é possível superprovisionar a cota de volume para definir um nível de rendimento mais alto e obter um desempenho mais alto.  

Por exemplo, se um volume no nível de armazenamento Premium tiver apenas 500 GiB de dados, mas exigir 128 MiB/s de throughput, você pode definir a cota para 2 TiB para que o nível de throughput seja definido de acordo (64 MiB/s por TB * 2 TiB = 128 MiB/s).  

Se você tiver um volume de provisionamento consistente para atingir um rendimento mais alto, considere usar um nível de serviço mais alto.  No exemplo acima, você pode alcançar o mesmo limite de throughput com metade da cota de volume usando o nível de armazenamento Ultra (128 MiB/s por TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Aumento dinamicamente ou diminuição da cota de volume

Se seus requisitos de desempenho forem temporários ou se você tiver aumentado as necessidades de desempenho por um período fixo de tempo, você pode aumentar ou diminuir dinamicamente a cota de volume para ajustar instantaneamente o limite de rendimento.  Observe as seguintes considerações: 

* A cota de volume pode ser aumentada ou diminuída sem necessidade de pausar o IO, e o acesso ao volume não é interrompido ou impactado.  

    Você pode ajustar a cota durante uma transação ativa de I/O em relação a um volume.  Observe que a cota de volume nunca pode ser diminuída abaixo da quantidade de dados lógicos armazenados no volume.

* Quando a cota de volume é alterada, a alteração correspondente no limite de throughput é quase instantânea. 

    A alteração não interrompe nem afeta o acesso ao volume ou a I/O.  

* O ajuste da cota de volume requer uma alteração no tamanho do pool de capacidade.  

    O tamanho da piscina de capacidade pode ser ajustado dinamicamente e sem afetar a disponibilidade de volume ou I/O.

## <a name="next-steps"></a>Próximas etapas

- [Níveis de serviço do Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Parâmetros de comparação de desempenho para o Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)