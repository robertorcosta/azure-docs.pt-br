---
title: Configurar ferramentas do Azure Stream Analytics para Visual Studio
description: Este artigo descreve os requisitos de instalação e como configurar as ferramentas do Azure Stream Analytics para o Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: fa3990d2239da71fb27f4c9a06699f758fcfe7a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354359"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Instalar o Azure Stream Analytics Tools para Visual Studio

Visual Studio 2019 e Visual Studio 2017 suportam Azure Data Lake e Stream Analytics Tools. Este artigo descreve como instalar e desinstalar as ferramentas.

Para obter mais informações sobre o uso das ferramentas, consulte [Quickstart: Crie um trabalho no Azure Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Instalar o

As edições Visual Studio Enterprise (Ultimate/Premium), Professional e Community suportam as ferramentas. Express edition e Visual Studio para Mac não os suportam.

Recomendamos o Visual Studio 2019.

### <a name="install-for-visual-studio-2019-and-2017"></a>Instalação para Visual Studio 2019 e 2017<a name="recommended-visual-studio-2019-and-2017"></a>

As ferramentas Azure Data Lake e Stream Analytics fazem parte do desenvolvimento do **Azure** e do **armazenamento de dados e do processamento de** cargas de trabalho. Habilite uma dessas duas cargas de trabalho durante a instalação. Se o Visual Studio já estiver instalado, selecione **Ferramentas** > **Obter Ferramentas e Recursos** para adicionar cargas de trabalho.

Baixe [Visual Studio 2019 (Pré-visualização 2 ou superior) ou Visual Studio 2017 (15,3 ou mais)](https://www.visualstudio.com/) e siga as instruções para instalar.

Selecione a carga de trabalho **de armazenamento e processamento de dados** como mostrado:

![A carga de trabalho de armazenamento e processamento de dados é selecionada](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Selecione a carga **de trabalho de desenvolvimento do Azure** como mostrado:

![A carga de trabalho de desenvolvimento do Azure é selecionada](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

Depois de adicionar a carga de trabalho, atualize as ferramentas. Este procedimento refere-se ao Visual Studio 2019:

1. Selecione **Extensões** > **Gerenciar extensões**.

1. Em **Gerenciar extensões,** selecione **Atualizações** e escolha **Azure Data Lake e Stream Analytics Tools**.

1. Selecione **Atualizar** para instalar a extensão mais recente.

![Extensões e atualizações do Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Instalação para Visual Studio 2015 e 2013<a name="visual-studio-2015-2013"></a>

As edições Visual Studio Enterprise (Ultimate/Premium), Professional e Community suportam as ferramentas. A edição expressa não os suporta.

* Instale o Visual Studio 2015 ou o Visual Studio 2013 Atualização 4.
* Instale o SDK do Microsoft Azure para .NET versão 2.7.1 ou posterior usando o [Web platform installer](https://www.microsoft.com/web/downloads/platform.aspx).
* Instale [as ferramentas Microsoft Azure Data Lake e Stream Analytics para o Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Atualização<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Para o Visual Studio 2019 e o Visual Studio 2017, um lembrete de nova versão aparece como uma notificação do Visual Studio.

Para o Visual Studio 2015 e o Visual Studio 2013, as ferramentas verificam as novas versões automaticamente. Siga as instruções para instalar a versão mais recente.

## <a name="uninstall"></a>Desinstalar

Você pode desinstalar o Azure Data Lake e o Stream Analytics Tools. Para o Visual Studio 2019 ou Visual Studio 2017, selecione **Ferramentas** > **Obter Ferramentas e Recursos**. Na **modificação,** não selecione **as ferramentas Azure Data Lake e Stream Analytics .** Ele aparece sob a carga de trabalho de **armazenamento e processamento de dados** ou a carga de trabalho de desenvolvimento do **Azure.**

Para desinstalar do Visual Studio 2015 ou visual Studio 2013, acesse **Control Panel** > **Programs and Features**. Desinstale as **ferramentas Microsoft Azure Data Lake e Stream Analytics para o Visual Studio**.
