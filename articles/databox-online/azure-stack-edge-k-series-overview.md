---
title: Visão geral do Azure Stack Edge Mini R | Microsoft Docs
description: Descreve o Azure Stack Edge Mini R, uma solução de armazenamento para aplicativos militares que usa um dispositivo físico portátil com uma bateria para transferência por Wi-Fi para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 20efae411ae4d2fae9bf3b5e69dbfdd98da1603a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985632"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>O que é o Azure Stack Edge Mini R?

O Azure Stack Edge Mini R é uma solução de hardware como serviço. A Microsoft enviará a você um dispositivo robusto gerenciado em nuvem e ultra portátil com uma VPU (Unidade de Processamento Visual) interna que habilita a inferência de IA acelerada e tem todas as funcionalidades de um gateway de armazenamento de rede. Esses dispositivos podem ser usados nos ambientes mais adversos e são otimizados para IA, análise e computação sem servidor.

Este artigo fornece uma visão geral da solução Azure Stack Edge Mini R, das principais funcionalidades dela e dos cenários em que é possível implantar esse dispositivo.


## <a name="key-capabilities"></a>Principais recursos

O Azure Stack Edge Mini R tem as seguintes funcionalidades:

|Funcionalidade |Descrição  |
|---------|---------|
|Hardware robusto| Hardware robusto projetado para os ambientes mais adversos.|
|Ultra portátil| Fator forma ultra portátil e operado por bateria.|
|Gerenciado pela nuvem|O dispositivo e o serviço são gerenciados por meio do portal do Azure.|
|Cargas de trabalho de computação de borda|Permite a análise, o processamento e a filtragem de dados.<br>Dá suporte a VMs e cargas de trabalho em contêineres. |
|Inferência de IA acelerada| Habilitado pela VPU do Intel Movidius Myriad X.|
|Com e sem fio | Permite transferências de dados com e sem fio.|
|Acesso de dados     | Acesso direto a dados dos Blobs de Armazenamento do Microsoft Azure e Arquivos do Azure usando APIs de nuvem para processamento adicional de dados na nuvem. O cache local no dispositivo é usado para acesso rápido dos arquivos usados mais recentemente.|
|Modo desconectado|  Opcionalmente, o dispositivo e o serviço podem ser gerenciados por meio do Azure Stack Hub. Implante, execute e gerencie aplicativos no modo offline. <br> O modo desconectado é compatível com cenários de upload offline.|
|Protocolos com suporte     |Dá suporte aos protocolos SMB, NFS e REST padrão para ingestão de dados. <br> Para obter mais informações sobre as versões com suporte, acesse [Requisitos do sistema do Azure Stack Edge Mini R](azure-stack-edge-gpu-system-requirements.md).|
|Atualização dedados     | Capacidade de atualizar arquivos locais com a versão mais recente da nuvem.|
|Criptografia dupla    | O uso da unidade de criptografia automática fornece a primeira camada de criptografia. A VPN fornece a segunda camada de criptografia. Suporte ao BitLocker para criptografar dados localmente e proteger a transferência de dados para a nuvem por meio do *https*.|
|Limitação de largura de banda| Limitação para restringir o uso de largura de banda durante horários de pico.|

## <a name="use-cases"></a>Casos de uso

Veja a seguir os vários cenários em que o Azure Stack Edge Mini R pode ser usado para inferências rápidas de ML (Machine Learning) na borda e pré-processamento de dados, antes do envio para o Azure.

- **Inferência com o Azure Machine Learning** – Com o Azure Stack Edge Mini R, é possível executar modelos de ML para obter resultados rápidos que podem ser tratados antes que os dados sejam enviados para a nuvem. O conjunto de dados completo pode ser transferido para continuar a treinar novamente e melhorar os modelos de ML. Para obter mais informações sobre como usar os modelos de aceleração de hardware do Azure ML no dispositivo Azure Stack Edge Mini R, confira [Implantar modelos de aceleração de hardware do Azure ML no Azure Stack Edge Mini R](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Pré-processar dados** – Transformar dados por meio de opções de computação, como contêineres ou máquinas virtuais, antes de enviá-los ao Azure para criar um conjunto de dados mais acionável. O pré-processamento pode ser usado para:

    - Agregar dados.
    - Modifique os dados, por exemplo, para remover dados pessoais.
    - Subconjunto dados para otimizar o armazenamento e a largura de banda ou para análise posterior.
    - Analisar e reagir a Eventos de IoT.

- **Transferir dados pela rede para o Azure** – Use o Azure Stack Edge Mini R para transferir dados de maneira fácil e rápida para o Azure para habilitar ainda mais a computação e a análise ou para fins de arquivamento.

## <a name="components"></a>Componentes

A solução Azure Stack Edge Mini R inclui o recurso Azure Stack Edge, o dispositivo físico robusto e ultra portátil Azure Stack Edge Mini R e uma IU da Web local.

* **Dispositivo físico Azure Stack Edge Mini R** – Um dispositivo ultra portátil com uma bateria integrada e uma embalagem robusta fornecida pela Microsoft que pode ser configurada para enviar dados para o Azure. O dispositivo com a bateria pesa menos de 3,20 kg.

    ![Dispositivo Azure Stack Edge Mini R](media/azure-stack-edge-k-series-overview/perspective-view-1.png)

* **Recurso Azure Stack Edge** – Um recurso no portal do Azure com o qual você pode gerenciar um dispositivo robusto Azure Stack Edge Mini R em uma interface da Web que pode ser acessada em diferentes localizações geográficas. Use o recurso Azure Stack Edge para criar e gerenciar recursos, exibir e gerenciar dispositivos e alertas, e gerenciar compartilhamentos.  

* **IU da Web local do Azure Stack Edge Mini R** – Use a IU da Web local para realizar a configuração inicial do dispositivo, para executar diagnósticos, desligar e reiniciar o dispositivo Azure Stack Edge Mini R, exibir logs de cópia e entrar em contato com o Suporte da Microsoft para apresentar uma solicitação de serviço.


## <a name="region-availability"></a>Disponibilidade de região

O dispositivo físico Azure Stack Edge Mini R, o recurso do Azure e a conta de armazenamento de destino para a qual os dados são transferidos não precisam estar na mesma região.

- **Disponibilidade de recursos** – Para obter uma lista de todas as regiões em que o recurso Azure Stack Edge está disponível, vá para [Produtos do Microsoft Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Contas de Armazenamento de destino** – as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. Para obter o desempenho ideal, as regiões nas quais as contas de armazenamento guardam dados do Azure Stack Edge Mini R devem estar próximas do local em que o dispositivo está localizado. Uma conta de armazenamento localizada longe do dispositivo resulta em longas latências de desempenho mais lentos.


## <a name="next-steps"></a>Próximas etapas

- Examinar os [requisitos do sistema do Azure Stack Edge Mini R](azure-stack-edge-gpu-system-requirements.md).


