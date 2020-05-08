---
title: Localização de Player de Mídia do Azure
description: Suporte a vários idiomas para usuários de localidades não inglesas.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ca4dc888af414ede270118eff72652f098d3306c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779035"
---
# <a name="localization"></a>Localização #

O suporte a vários idiomas permite que os usuários de localidades não inglesas interajam nativamente com o jogador. Player de Mídia do Azure criará uma instância com um dicionário global de idiomas, que localizará as mensagens de erro com base no idioma da página. Um desenvolvedor também pode criar uma instância de Player com uma linguagem de conjunto forçada. O idioma padrão é inglês (EN).

> [!NOTE]
> Esse recurso ainda está passando por alguns testes e, como tal, está sujeito a bugs.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

O Player de Mídia do Azure atualmente dá suporte aos seguintes idiomas com seus códigos de idioma correspondentes:

| Linguagem            | Código | Linguagem                | Código   | Linguagem                | Código         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Inglês {padrão}   | en   | Croata                | hr     | Romeno                | ro           |
| Árabe              | ar   | Húngaro               | hu     | Eslovaco                  | sk           |
| Búlgaro           | bg   | Indonésio              | id     | Slovene                 | sl           |
| Catalão             | AC   | Islandês               | é     | Sérvio - Cirílico      | Sr-Cyrl-CS   |
| Tcheco               | cs   | Italiano                 | it     | Sérvio - latino         | sr-latn-rs   |
| Dinamarquês              | da   | Japonês                | ja     | Russo                 | ru           |
| Alemão              | de   | Cazaque                  | kk     | Sueco                 | sv           |
| Grego               | el   | Coreano                  | ko     | Tailandês                    | th           |
| Espanhol             | es   | Lituano              | lt     | Tagalo                 | TL           |
| Estoniano            | et   | Letão                 | lv     | Turco                 | tr           |
| Basco              | eu   | Ringgit               | ms     | Ucraniano               | uk           |
| Farsi               | fa   | Norueguês-BokmÃ ¥ l     | nb     | Urdu                    | ur           |
| Finlandês             | fi   | Holandês                   | nl     | Vietnamita              | vi           |
| Francês              | fr   | Norueguês - Nynorsk     | nn     | Chinês-simplificado    | zh-hans      |
| Galego            | gl   | Polonês                  | pl     | Chinês-tradicional   | zh-hant      |
| Hebraico              | he   | Português - Brasil     | pt-br  |                         |              |
| Híndi               | hi   | Português - Portugal   | pt-pt  |                         |              |


> [!NOTE]
> Se você não quiser que nenhuma localização ocorra, você deve forçar o idioma para inglês

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)
