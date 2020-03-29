---
title: Microsoft Azure StorSimple Virtual Array Update 1.2 notas de lançamento| Microsoft Docs
description: Descreve problemas e resoluções abertas críticas para o StorSimple Virtual Array executando a Atualização 1.2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66420597"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple Virtual Array Update 1.2 notas de versão

As notas de versão a seguir identificam as questões críticas em aberto e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de versão são continuamente atualizadas. Problemas críticos que exijam soluções alternativas serão adicionados à medida que forem descobertos. Antes de implantar o StorSimple Virtual Array, examine cuidadosamente as informações contidas nas notas de versão.

A atualização 1.2 corresponde à versão do software **10.0.10311.0**.

> [!IMPORTANT]
> - As atualizações causam interrupção e reiniciam seu dispositivo. Se processos de E/S estiverem em andamento, o dispositivo sofrerá tempo de inatividade. Para obter instruções detalhadas sobre os pacotes usados para aplicar esta atualização, acesse [Download Update 1.2](#download-update-12).
> - A atualização 1.2 está disponível para você através do portal Azure somente se o seu dispositivo estiver executando a Atualização 1.0 ou 1.1.

## <a name="whats-new-in-update-12"></a>Novidades na Atualização 1.2

Esta atualização contém as seguintes correções de bugs:

- Maior resiliência ao processar arquivos excluídos.
- Exceções de manuseio aprimoradas no caminho de inicialização de código, levando a falhas reduzidas em backups, restauração, leituras em nuvem e recuperação automatizada de espaço.

## <a name="download-update-12"></a>Baixar Atualização 1.2

Para baixar esta atualização, acesse o servidor [microsoft update catalog](https://www.catalog.update.microsoft.com/Home.aspx) e baixe o pacote KB4502035. Este pacote contém os seguintes pacotes:

 - **KB4493446** que contém atualizações cumulativas do Windows para 2012 R2 até abril de 2019. Para obter mais informações sobre o que está incluído neste rollup, vá para [abril mensal de segurança rollup](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** que é um arquivo de pacote autônomo da Microsoft Update WindowsTH-KB3011067-x64. Esse arquivo é usado para atualizar o software do dispositivo.

Baixe KB4502035 e siga estas instruções para [aplicar a atualização via web UI local](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problemas corrigidos na Atualização 1.2

A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Não. | Recurso | Problema |
| --- | --- | --- |
| 1 |Exclusão| Nas versões anteriores do software, houve um problema quando o uso do dispositivo não mudou mesmo quando os arquivos foram excluídos. Este problema é corrigido nesta versão. O caminho de código de hierarquiagem tornou-se mais resistente ao processar arquivos excluídos.|
| 2 |Manipulação de exceção| Nas versões anteriores do software, houve um problema após a reinicialização do sistema que poderia potencialmente levar a falhas em backups, restauração, leitura da nuvem e recuperação automatizada do espaço. Esta versão contém alterações na forma como as exceções foram tratadas no caminho da inicialização.|

## <a name="known-issues-in-update-12"></a>Problemas conhecidos na Atualização 1.2

Nenhum novo problema foi observado na Atualização 1.2. Todos os problemas de lançamento são realizados em versões anteriores. Para ver o resumo dos problemas conhecidos incluídos nas versões anteriores, acesse [Os problemas conhecidos na Atualização 1.1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Próximas etapas

Baixe KB4502035 e [aplique a atualização via web UI local](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Referências

Procurando uma nota de versão mais antiga? Acesse:
* [StorSimple Virtual Array Update 1.1 Release Notes](storsimple-virtual-array-update-11-release-notes.md)
* [Notas de versão do Update 1.0 do Array Virtual StorSimple](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 Release Notes](storsimple-virtual-array-update-06-release-notes.md)
* [Notas de versão da Atualização 0.5 do StorSimple Virtual Array](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 Release Notes](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Release Notes](storsimple-ova-update-03-release-notes.md)
* [Notas de versão as Atualizações 0.1 e 0.2 do StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)
* [Notas de versão de disponibilidade geral do StorSimple Virtual Array](storsimple-ova-pp-release-notes.md)
