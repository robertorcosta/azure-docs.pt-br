---
title: Inicialização do sistema operacional – o computador foi reiniciado inesperadamente ou encontrou um erro inesperado
description: Este artigo fornece etapas para resolver problemas em que a VM passa por uma reinicialização inesperada ou erro durante a instalação do Windows.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: 186b1c46303be59e191a1754361e07a2003b997a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87036175"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Inicialização do sistema operacional – o computador foi reiniciado inesperadamente ou encontrou um erro inesperado

Este artigo fornece etapas para resolver problemas em que a VM (máquina virtual) passa por uma reinicialização inesperada ou erro durante a instalação do Windows.

## <a name="symptom"></a>Sintoma

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, você verá que a captura de tela exibe falha na instalação do Windows com o seguinte erro:

**O computador foi reiniciado inesperadamente ou encontrou um erro inesperado. A instalação do Windows não pode continuar. Para instalar o Windows, clique em "OK" para reiniciar o computador e reinicie a instalação.**

![Erro enquanto a instalação do Windows está em andamento: o computador foi reiniciado inesperadamente ou encontrou um erro inesperado. A instalação do Windows não pode continuar. Para instalar o Windows, clique em "OK" para reiniciar o computador e reinicie a instalação.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Erro quando a instalação da instalação do Windows está iniciando serviços: o computador foi reiniciado inesperadamente ou encontrou um erro inesperado. A instalação do Windows não pode continuar. Para instalar o Windows, clique em "OK" para reiniciar o computador e reinicie a instalação.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Causa

O computador está tentando fazer uma inicialização inicial de uma [imagem generalizada](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation), mas encontra problemas devido a um arquivo de resposta personalizado (unattend.xml) sendo processado. Não há suporte para arquivos de resposta personalizados no Azure. 

O arquivo de resposta é um arquivo XML especial que contém definições de configuração e valores para as definições de configuração que você deseja automatizar durante a instalação de uma instalação do sistema operacional Windows Server. As opções de configuração incluem instruções sobre como particionar discos, onde encontrar a imagem do Windows a ser instalada, chaves do produto a serem aplicadas e outros comandos que você gostaria de executar.

No Azure, não há suporte para arquivos de resposta personalizados. Se você especificou um arquivo de **Unattend.xml** personalizado usando a `sysprep /unattend:<your file’s name>` opção, esse erro poderá ocorrer.

No Azure, use a opção **OOBE (experiência inicial do sistema) de entrada** no **Sysprep.exe**ou use `sysprep /oobe` em vez do arquivo Unattend.xml.

Esse problema é criado com mais frequência enquanto você está usando **Sysprep.exe** com uma VM local para carregar uma VM generalizada no Azure. Nessa situação, você também pode estar interessado em como carregar corretamente uma VM generalizada.

## <a name="solution"></a>Solução

### <a name="replace-unattended-answer-file-option"></a>Opção Substituir arquivo de resposta autônomo

Essa situação ocorre quando uma imagem foi preparada para uso no Azure, mas ela usou um arquivo de resposta personalizado, que não tem suporte no Azure, e você usou o **Sysprep** com um sinalizador semelhante ao seguinte comando:

`sysprep /oobe /generalize /unattend:<NameOfYourAnswerFile.XML> /shutdown`

- No comando anterior, substitua `<NameOfYourAnswerFile.XML>` pelo nome do arquivo.

Para corrigir esse problema, siga [as diretrizes do Azure sobre como preparar/capturar uma imagem](../windows/upload-generalized-managed.md) e preparar uma nova imagem generalizada. Durante o Sysprep, não use o `/unattend:<answerfile>` sinalizador. Em vez disso, use apenas os sinalizadores abaixo:

`sysprep /oobe /generalize /shutdown`

- O OOBE ( **check-of-Box-Experience** ) é a configuração com suporte para VMs do Azure.

Você também pode usar a **GUI da ferramenta de preparação do sistema** para realizar a mesma tarefa que o comando acima, selecionando as opções mostradas abaixo:

- Entrar na experiência de entrada
- Generalizar
- Shutdown
 
![Janela da ferramenta de preparação do sistema com opções de OOBE, generalize e Shutdown selecionadas.](./media/unexpected-restart-error-during-vm-boot/3.png)
