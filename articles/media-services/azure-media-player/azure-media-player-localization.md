---
title: Localização do Azure Media Player
description: Suporte a vários idiomas para usuários de locais não-ingleses.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727171"
---
# <a name="localization"></a>Localização #

O suporte a vários idiomas permite que usuários de locais não-ingleses interajam nativamente com o jogador. O Azure Media Player irá instanciar com um dicionário global de idiomas, que localizará as mensagens de erro com base no idioma da página. Um desenvolvedor também pode criar uma instância de jogador com uma linguagem de conjunto forçado. O idioma padrão é inglês (en).

> [!NOTE]
> Esse recurso ainda está passando por alguns testes e, como tal, está sujeito a bugs.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Atualmente, o Azure Media Player suporta os seguintes idiomas com seus códigos de idioma correspondentes:

| Linguagem            | Código | Linguagem                | Código   | Linguagem                | Código         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Inglês {default}   | en   | Croata                | hr     | Romeno                | ro           |
| Árabe              | ar   | Húngaro               | hu     | Eslovaco                  | sk           |
| Búlgaro           | bg   | Indonésio              | id     | Esloveno                 | sl           |
| Catalão             | Ca   | Islandês               | is     | Sérvio - Cirílico      | sr-cyrl-cs   |
| Tcheco               | cs   | Italiano                 | it     | Sérvio - latino         | sr-latn-rs   |
| Dinamarquês              | da   | Japonês                | ja     | Russo                 | ru           |
| Alemão              | de   | Cazaque                  | Kk     | Sueco                 | sv           |
| Grego               | el   | Coreano                  | ko     | Tailandês                    | th           |
| Espanhol             | es   | Lituano              | lt     | Tagalo                 | Tl           |
| Estoniano            | et   | Letão                 | lv     | Turco                 | tr           |
| Basco              | Ue   | Malásia               | ms     | Ucraniano               | uk           |
| Farsi               | Fa   | Norueguês - BokmÃ'l     | nb     | Urdu                    | Seu           |
| Finlandês             | fi   | Holandês                   | nl     | Vietnamita              | vi           |
| Francês              | fr   | Norueguês - Nynorsk     | nn     | Chinês - simplificado    | zh-hans      |
| Galego            | gl   | Polonês                  | pl     | Chinês - tradicional   | zh-hant      |
| Hebraico              | he   | Português - Brasil     | pt-br  |                         |              |
| Híndi               | hu   | Português - Portugal   | pt-pt  |                         |              |


> [!NOTE]
> Se você não quiser que nenhuma localização ocorra, você deve forçar o idioma para o inglês

## <a name="next-steps"></a>Próximas etapas ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)