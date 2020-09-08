---
title: Visão geral do Microsoft Azure Stack Edge com GPU | Microsoft Docs
description: Descreve o Azure Stack Edge com GPU, uma solução de armazenamento que usa um dispositivo físico para transferência baseada em rede para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: a8e1c83573de53962b3646304389023d91ab6dd3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256235"
---
# <a name="what-is-azure-stack-edge-with-gpu-preview"></a>O que é o Azure Stack Edge com GPU (versão prévia)?

O Azure Stack Edge com GPU é um dispositivo de computação de borda habilitado para IA com funcionalidades de transferência de dados de rede. Este artigo fornece uma visão geral da solução, das vantagens, das principais funcionalidades e dos cenários do Azure Stack Edge em que é possível implantar esse dispositivo.

O Azure Stack Edge com GPU é uma solução de hardware como serviço. A Microsoft envia a você um dispositivo gerenciado em nuvem que funciona como um gateway de armazenamento de rede e tem uma GPU (unidade de processamento gráfico) interna que permite a inferência de IA acelerada. 

> [!IMPORTANT]
> Atualmente, o Azure Stack Edge com GPU está em versão prévia. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="use-cases"></a>Casos de uso

Aqui estão os vários cenários em que o Azure Stack Edge pode ser usado para inferências rápidas de ML (Machine Learning) na borda e pré-processamento de dados, antes do envio para o Microsoft Azure.

- **Inferência com o Azure Machine Learning** – Com o Azure Stack Edge, é possível executar modelos de ML para obter resultados rápidos que podem ser tratados, antes que os dados sejam enviados para a nuvem. O conjunto de dados completo pode ser transferido para continuar a treinar novamente e melhorar os modelos de ML. Para obter mais informações sobre como usar os Modelos de Aceleração de Hardware do Azure Machine Learning no dispositivo Azure Stack Edge, confira [Implantar Modelos de Aceleração de Hardware do Azure Machine Learning no Azure Stack Edge](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Pré-processar dados** – transforme dados antes de enviá-los para o Azure para criar um conjunto de dados mais acionável. O pré-processamento pode ser usado para: 

    - Agregar dados.
    - Modifique os dados, por exemplo, para remover dados pessoais.
    - Subconjunto dados para otimizar o armazenamento e a largura de banda ou para análise posterior.
    - Analisar e reagir a Eventos de IoT. 

- **Transferir dados pela rede para o Microsoft Azure** – Use o Azure Stack Edge para transferir dados de maneira fácil e rápida para o Microsoft Azure para habilitar ainda mais computação e análise ou para fins de arquivamento. 

## <a name="key-capabilities"></a>Principais recursos

O Azure Stack Edge tem as seguintes funcionalidades:

|Recurso |Descrição  |
|---------|---------|
|Inferência de IA acelerada| Habilitada pela GPU interna (uma ou duas, dependendo do modelo).|
|Computação de borda      |Permite a análise, o processamento e a filtragem de dados. Dá suporte a VMs e clusters do Kubernetes.|
|Alto desempenho | Computação de alto desempenho e transferências de dados.|
|Acesso de dados     | Acesso direto a dados dos Blobs de Armazenamento do Microsoft Azure e Arquivos do Azure usando APIs de nuvem para processamento adicional de dados na nuvem. O cache local no dispositivo é usado para acesso rápido dos arquivos usados mais recentemente.|
|Gerenciado pela nuvem     |O dispositivo e o serviço são gerenciados por meio do portal do Azure.  |
|Upload offline     | O modo desconectado é compatível com cenários de upload offline.|
|Protocolos com suporte     | Suporte aos protocolos SMB, NFS e REST padrão para ingestão de dados. <br> Para obter mais informações sobre as versões compatíveis, acesse [Requisitos do sistema do Azure Stack Edge](azure-stack-edge-system-requirements.md).|
|Atualização dedados     | Capacidade de atualizar arquivos locais com a versão mais recente da nuvem.|
|Criptografia    | Suporte ao BitLocker para criptografar dados localmente e proteger a transferência de dados para a nuvem por meio do *https*.|
|Limitação de largura de banda| Limitação para restringir o uso de largura de banda durante horários de pico.|
|ExpressRoute | Adição de segurança por meio do ExpressRoute. Use a configuração de emparelhamento, na qual o tráfego de dispositivos locais para os pontos de extremidade de armazenamento em nuvem viaja pelo ExpressRoute. Para obter mais informações, consulte [Visão geral de ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Componentes

A solução Azure Stack Edge inclui o recurso Azure Stack Edge, o dispositivo físico Azure Stack Edge e uma interface do usuário da Web local.

* **Dispositivo físico Azure Stack Edge** – Um servidor montado em rack de 1U fornecido pela Microsoft que pode ser configurado para enviar dados ao Microsoft Azure.
    
* **Recurso Azure Stack Edge** – Um recurso no portal do Azure com o qual você pode gerenciar um dispositivo Azure Stack Edge em uma interface da Web que pode ser acessada em diferentes localizações geográficas. Use o recurso Azure Stack Edge para criar e gerenciar recursos, exibir e gerenciar dispositivos e alertas, e gerenciar compartilhamentos.  

    Para saber mais, vá para o tópico [Criar uma ordem para o dispositivo Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

* **Interface do usuário da Web local do Azure Stack Edge** – Use a interface do usuário da Web local para executar diagnósticos, desligar e reiniciar o dispositivo Azure Stack Edge, exibir logs de cópia e contatar o Suporte da Microsoft para apresentar uma solicitação de serviço.

    Para saber mais sobre como usar a interface do usuário baseada na Web, veja o tópico [Usar a interface do usuário baseada na Web para administrar o Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Disponibilidade de região

O dispositivo físico Azure Stack Edge, o recurso do Microsoft Azure e a conta de armazenamento de destino para a qual os dados são transferidos não precisam estar na mesma região.

- **Disponibilidade de recursos**: nesta versão prévia, o recurso está disponível nas regiões Leste dos EUA, Oeste da Europa e Sudeste da Ásia.
    
- **Contas de Armazenamento de destino** – as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. Para obter o desempenho ideal, as regiões em que as contas de armazenamento armazenam dados do Azure Stack Edge devem estar próximas do local onde o dispositivo está localizado. Uma conta de armazenamento localizada longe do dispositivo resulta em longas latências de desempenho mais lentos.

## <a name="next-steps"></a>Próximas etapas

- Examinar os [requisitos do sistema do Azure Stack Edge](azure-stack-edge-gpu-system-requirements.md).
- Entender os [limites do Azure Stack Edge](azure-stack-edge-limits.md).
- Implantar o [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) no portal do Azure.
