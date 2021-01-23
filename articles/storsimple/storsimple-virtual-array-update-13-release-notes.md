---
title: Notas de versão da atualização 1,3 do Microsoft Azure StorSimple virtual array | Microsoft Docs
description: Descreve problemas e resoluções críticos em aberto para a matriz virtual do Azure StorSimple que executa a atualização 1,3.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 02611bdf9689d2f62f661f558fd547ea46bd4d36
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744199"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>Notas de versão da atualização 1,3 do StorSimple virtual array

As notas de versão a seguir identificam as questões críticas em aberto e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de versão são atualizadas continuamente. Problemas críticos que exijam soluções alternativas serão adicionados à medida que forem descobertos. Antes de implantar o StorSimple Virtual Array, examine cuidadosamente as informações contidas nas notas de versão.

A atualização 1,3 corresponde à versão de software 10.0.10319.0.

> [!IMPORTANT]
> - A atualização 1,3 é uma atualização crítica. É altamente recomendável que você o instale assim que possível.
> - Você pode instalar a atualização 1,3 somente em dispositivos que executam a atualização 1,2.
> - As atualizações causam interrupção e reiniciam seu dispositivo. Se a e/s estiver em andamento, o dispositivo incorrerá em tempo de inatividade. Para obter instruções detalhadas sobre os pacotes usados para aplicar essa atualização, acesse [baixar a atualização 1,3](#download-update-13).

## <a name="whats-new-in-update-13"></a>O que há de novo na atualização 1,3

Essa atualização contém os seguintes aprimoramentos:

- O protocolo TLS 1,2 é uma atualização obrigatória e deve ser instalada. A partir desta versão em diante, o TLS 1,2 se torna o protocolo padrão para todas as portal do Azure comunicação.
- As correções de bug de coleta de lixo melhoram o desempenho do ciclo de coleta de lixo quando a conta de armazenamento e o dispositivo estão em duas regiões distantes.
- Correção de falhas de backup devido a tempos limite de BLOB.
- Patches de segurança do sistema operacional/.NET Framework atualizados:
  - [KB4540725](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Windows6.3\RTM\KB4540725\V1.001\free\NEU\X64): março de 2020 Ssu (atualização da pilha de manutenção)
  - [KB4565541](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Windows6.3\RTM\KB4565541\V1.014\free\NEU\X64): acúmulo de julho de 2020
  - [KB4565622](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Partner\DOTNET47x\KB4565622\V1.000\free\NEU\x64): atualização de julho de 2020 .NET Framework

## <a name="download-update-13"></a>Baixar a atualização 1,3

Para baixar essa atualização, vá para o servidor de [Catálogo de Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) e baixe o pacote KB4575898. Este pacote contém os seguintes pacotes:

- **KB4540725**, que contém atualizações cumulativas do Windows para 2012 R2 até março de 2020. Para obter mais informações sobre o que está incluído neste ROLLUP, acesse [ROLLUP de segurança mensal de março](https://support.microsoft.com/help/4540725).
- **KB4565541**, que contém atualizações cumulativas do Windows para 2012 R2 até julho de 2020. Para obter mais informações sobre o que está incluído neste pacote cumulativo, vá para [ROLLUP de segurança mensal de fevereiro](https://support.microsoft.com/help/4565541).
- **KB4565622**, que contém atualizações do Cumulative.NET Framework de até julho de 2020. Para obter mais informações sobre o que está incluído neste pacote cumulativo, vá para [ROLLUP de segurança mensal de fevereiro](https://support.microsoft.com/help/4565622).
- **KB3011067**, que contém as atualizações de software do dispositivo.

Baixe o KB4575898 e siga estas instruções para [aplicar a atualização por meio da interface do usuário da Web local](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="known-issues-in-update-13"></a>Problemas conhecidos na atualização 1,3
Nenhum novo problema foi lançado na atualização 1,3. Todos os problemas indicados pela versão são transferidos de versões anteriores. Para ver o resumo dos problemas conhecidos incluídos nas versões anteriores, vá para [problemas conhecidos na atualização 1,2](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12).

## <a name="next-steps"></a>Próximas etapas
Baixe o KB4575898 e [aplique a atualização por meio da interface do usuário da Web local](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui).

## <a name="references"></a>Referências
Procurando uma nota de versão mais antiga? Acesse:

- [Notas de versão da atualização 1,2 do StorSimple virtual array](./storsimple-virtual-array-update-12-release-notes.md)
- [Notas de versão do Update 1.0 do Array Virtual StorSimple](./storsimple-virtual-array-update-1-release-notes.md)
- [Notas de versão da atualização 0,6 do StorSimple virtual array](./storsimple-virtual-array-update-06-release-notes.md)
- [Notas de versão da Atualização 0.5 do StorSimple Virtual Array](./storsimple-virtual-array-update-05-release-notes.md)
- [Notas de versão da atualização 0,4 do StorSimple virtual array](./storsimple-virtual-array-update-04-release-notes.md)
- [Notas de versão da atualização 0,3 do StorSimple virtual array](./storsimple-ova-update-03-release-notes.md)
- [Notas de versão as Atualizações 0.1 e 0.2 do StorSimple Virtual Array](./storsimple-ova-update-01-release-notes.md)
- [Notas de versão de disponibilidade geral do StorSimple Virtual Array](https://review.docs.microsoft.com/en-us/azure/storsimple/storsimple-ova-pp-release-notes)