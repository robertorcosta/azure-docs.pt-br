---
title: Perguntas frequentes tamanhos de VM do Azure sem disco temporário local
description: Este artigo fornece respostas para perguntas frequentes sobre Microsoft Azure tamanhos de VM que não têm disco temporário local.
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: 413f53feedc4fee0877694e3f3a3a509c4d38001
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783535"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>Tamanhos de VM do Azure sem disco temporário local 
Este artigo fornece respostas para perguntas frequentes sobre tamanhos de VM do Azure que não têm um disco temporário local (ou seja, não local temp Desk). Para obter mais informações sobre esses tamanhos de VM, consulte [especificações para dv4 e Dsv4 (cargas de trabalho uso geral)](dv4-dsv4-series.md) ou [especificações para as séries Ev4 e Esv4 (cargas de trabalho com otimização de memória)](ev4-esv4-series.md).

> [!IMPORTANT]
> Os tamanhos de VM da série DV4, Dsv4, Ev4 e Esv4 agora estão em visualização pública. Para se inscrever na visualização pública, preencha este [formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

## <a name="what-does-no-local-temp-disk-mean"></a>O que não significa disco temporário local? 
Tradicionalmente, tivemos tamanhos de VM (por exemplo, Standard_D2s_v3, Standard_E48_v3) que incluem um pequeno disco local (ou seja, uma unidade D:). Agora com esses novos tamanhos de VM, esse pequeno disco local não existe mais; no entanto, você ainda pode anexar HDD Standard, SSD Premium ou SSD Ultra.

## <a name="what-if-i-still-want-local-temp-disk"></a>E se eu ainda quiser um disco temporário local?
Se sua carga de trabalho exigir um disco temporário local, também temos novos tamanhos [de VM Ddv4 e Ddsv4](ddv4-ddsv4-series.md) ou [Edv4 e Edsv4](edv4-edsv4-series.md) disponíveis. Esses tamanhos oferecem 50% de disco temporário maior em comparação com os tamanhos v3 anteriores.

> [!NOTE]
> O disco temporário local não é persistente; para garantir que seus dados sejam persistentes, use as opções HDD Standard, SSD Premium ou SSD Ultra. 

## <a name="what-are-the-differences-between-these-new-vm-sizes-and-the-general-purpose-dv3dsv3-or-the-memory-optimized-ev3esv3-vm-sizes-that-i-am-used-to"></a>Quais são as diferenças entre esses novos tamanhos de VM e o Uso Geral Dv3/Dsv3 ou os tamanhos de VM Ev3/Esv3 com otimização de memória com os quais estou acostumado? 
| famílias de VMs v3 com disco temporário local   | Novas famílias V4 com disco temporário local | Novas famílias v4 sem disco temporário local |
|---|---|---|
| Dv3   | Ddv4 | Dv4 |
| Dsv3 | Ddsv4  | Dsv4 |
| Ev3   | Edv4  | Ev4 |
| Esv3 | Edsv4 |    Esv4 | 

## <a name="can-i-resize-a-vm-size-that-has-a-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>Posso redimensionar um tamanho de VM que tem um disco temporário local para um tamanho de VM sem disco temporário local?  
Não. As únicas combinações permitidas para redimensionamento são: 

1. VM (com disco temporário local)-> VM (com disco temporário local); e 
2. VM (sem disco temporário local)-> VM (sem disco temporário local). 

> [!NOTE]
> Se uma imagem depender do disco de recurso ou se existir um arquivo de paginação ou permuta no disco temporário local, as imagens sem disco não funcionarão — em vez disso, use a alternativa ' com disco '. 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>Esses tamanhos de VM dão suporte a sistemas operacionais Linux e Windows (SO)?
Sim.

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>Isso interromperá meus scripts personalizados, imagens personalizadas ou imagens do sistema operacional que têm arquivos ou arquivos de paginação em um disco temporário local?
Se a imagem personalizada do sistema operacional apontar para o disco temporário local, a imagem poderá não funcionar corretamente com esse tamanho sem disco.

## <a name="have-questions-or-feedback"></a>Dúvidas ou comentários?
Preencha o [formulário de comentários]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u). 

## <a name="next-steps"></a>Próximas etapas 
Neste documento, você aprendeu mais sobre as perguntas mais frequentes relacionadas às VMs do Azure com disco temporário local. Para obter mais informações sobre esses tamanhos de VM, consulte os seguintes artigos:

- [Especificações para dv4 e Dsv4 (carga de trabalho de Uso Geral)](dv4-dsv4-series.md)
- [Especificações para Ev4 e Esv4 (carga de trabalho com otimização de memória)](ev4-esv4-series.md)
