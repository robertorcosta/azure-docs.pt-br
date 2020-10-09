---
title: Dimensionamento do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Dimensionamento do SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 404f8318816edcc2cfd1c50ca42304ff6ec93039
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77616901"
---
# <a name="sizing"></a>Dimensionamento

O dimensionamento do HANA em Instâncias Grandes não é diferente do dimensionamento do HANA em geral. Para sistemas existentes e implantados que você deseja migrar de outro RDBMS para o HANA, o SAP fornece vários relatórios que são executados nos sistemas SAP existentes. Se o banco de dados é movido para o HANA, esses relatórios verificam os dados e calculam os requisitos de memória para a instância do HANA. Para obter mais informações sobre como executar esses relatórios e obter os patches ou versões mais recentes, leia as seguintes Notas SAP:

- [SAP Note nº 1793345 - dimensionamento do SAP Suite no HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note nº 1872170 - relatório de dimensionamento do Suite no HANA e S/4 HANA](https://launchpad.support.sap.com/#/notes/1872170)
- [Observação SAP #2121330-perguntas frequentes: SAP BW no relatório de dimensionamento do HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 relatório de dimensionamento para BW no HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290-novo relatório de dimensionamento para BW no HANA](https://launchpad.support.sap.com/#/notes/2296290)

Para implementações de campo verde Sizer rápida SAP está disponível para calcular os requisitos de memória da implementação de software SAP sobre HANA.

Os requisitos de memória para o HANA aumentam conforme o volume de dados aumenta. Esteja ciente do seu consumo atual de memória para ajudar a prever o que será no futuro. Com base nos requisitos de memória, é possível e mapear a demanda em uma das SKUs do SAP HANA em Instâncias Grandes.

**Próximas etapas**
- Veja [Requisitos de integração](hana-onboarding-requirements.md)