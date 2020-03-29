---
title: Cargas de trabalho da IBM no Azure | Microsoft Docs
description: Use um emulador de mainframe e outros serviços de parceiros microsoft para rehospedar suas cargas de trabalho ibm z/OS usando o Microsoft Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 13c83c53cdad719d6a4bed4cc1852b85d62082e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68834591"
---
# <a name="ibm-workloads-on-azure"></a>Cargas de trabalho da IBM no Azure

Muitas cargas de trabalho de mainframe da IBM baseadas em z/OS podem ser replicadas no Azure sem perda de funcionalidade e sem que os usuários sequer notem mudanças em seus sistemas subjacentes. Rehospedar aplicativos no Azure oferece os recursos semelhantes a mainframe que você precisa, além da elasticidade, disponibilidade e potencial economia de custos da nuvem.

O Azure oferece suporte à integração com os ambientes de mainframe existentes da IBM, permitindo migrar os aplicativos que fazem sentido, executar soluções híbridas quando necessário e migrar ao longo do tempo. Embora você possa reescrever completamente os programas baseados em mainframe existentes para o Azure, é mais comum rehospedá-los. Reescrever adiciona custo, complexidade e tempo para projetos de migração. Com a rehospedagem, você pode:

- Mova aplicativos para um emulador baseado em nuvem.

- Migre o banco de dados para um banco de dados baseado em nuvem.

- Substitua módulos e códigos usando mecanismos de transformação de código.

Além disso, o software da IBM, incluindo WebSphere e MQ, está agora no Azure Marketplace. Com uma licença para software IBM, você pode aproveitar o dimensionamento de infra-estrutura demanda fornecido pelo Azure para iniciar rapidamente uma máquina virtual.

Um amplo ecossistema de parceiros está disponível para ajudá-lo a migrar os sistemas de mainframe da IBM para o Azure. A maioria segue uma abordagem pragmática de reutilização sempre que possível antes de embarcar em uma implantação em fases de reescrita ou substituição de aplicativos. Obtenha mais orientação e ajuda de parceiros no [centro de migração do Mainframe Azure](https://azure.microsoft.com/migration/mainframe/).

**Próximas etapas**

- [Migração de mainframe: mitos e fatos](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Instale o ambiente de dev/teste IBM zD&T no Azure](./install-ibm-z-environment.md)
- [Configure uma ADCD (Application Developers Controlled Distribution, distribuição controlada de desenvolvedores de aplicativos) no IBM zD&T v1](./demo.md)
- [IBM DB2 pureScale no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
