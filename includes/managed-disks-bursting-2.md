---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 801f0f03b49d20c84a4531bd0daad7630a0ed01d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585083"
---
## <a name="common-scenarios"></a>Cenários comuns
Os cenários a seguir podem se beneficiar muito da intermitência:
- **Melhorando os tempos de inicialização**  – com a intermitência, sua instância será inicializada a uma taxa significativamente mais rápida. Por exemplo, o disco do sistema operacional padrão para VMs com habilitação Premium é o disco P4, que é um desempenho provisionado de até 120 IOPS e 25 MB/s. Com a intermitência, o P4 pode ir até 3500 IOPS e 170 MB/s, permitindo um tempo de inicialização para acelerar por 6 vezes.
- **Manipulação de trabalhos em lotes** – algumas cargas de trabalho do aplicativo são cíclicas por natureza e exigem um desempenho de linha de base para a maioria do tempo e exigem um desempenho maior por um curto período de tempo. Um exemplo disso é um programa de contabilidade que processa transações diárias que exigem uma pequena quantidade de tráfego de disco. No final do mês, o reconcilia os relatórios que precisam de uma quantidade muito maior de tráfego de disco.
- **Preparação para picos de tráfego** – os servidores Web e seus aplicativos podem enfrentar sobretensões de tráfego a qualquer momento. Se o seu servidor Web for apoiado por VMs ou discos que usam intermitência, os servidores serão mais bem equipados para lidar com picos de tráfego. 

## <a name="bursting-flow"></a>Fluxo de intermitência
O sistema de crédito de intermitências aplica-se da mesma maneira no nível de disco e no nível de máquina virtual. Seu recurso, uma VM ou um disco, será iniciado com créditos totalmente em estoque. Esses créditos permitirão que você se intermitência por 30 minutos na taxa máxima de intermitência. Os créditos de intermitência acumulam quando o recurso está em execução em seus limites de armazenamento em disco de desempenho. Para todos os IOPS e MB/s que seu recurso está usando abaixo do limite de desempenho, você começa a acumular créditos. Se o recurso tiver Créditos acumulados a serem usados para intermitência e sua carga de trabalho precisar de desempenho extra, seu recurso poderá usar esses créditos para ir acima do seu limite de desempenho para dar a ele o desempenho de e/s de disco necessário para atender à demanda.



![Diagrama de Bucket de intermitência](media/managed-disks-bursting/bucket-diagram.jpg)

Cabe a você saber como você deseja usar os 30 minutos de intermitência. Você pode usá-lo por 30 minutos consecutivamente ou esporadicamente ao longo do dia. Quando o produto é implantado, ele vem pronto com créditos completos e quando esgota os créditos que leva menos de um dia para ser totalmente estocado em todos os créditos. Você pode acumular e gastar seus créditos de intermitência a seu critério e o Bucket de 30 minutos não precisa estar cheio novamente para intermitência. Uma coisa a ser observada sobre a acumulação de intermitência é que ele é diferente para cada recurso, pois ele se baseia em IOPS não utilizados e MB/s abaixo de seus valores de desempenho. Isso significa que produtos de desempenho de linha de base mais altos podem acumular seus valores de intermitência mais rápido que os produtos de execução de linha de base menores. Por exemplo, um deixar de disco P1 sem atividade acumulará 120 IOPS por segundo, enquanto um disco P20 acumula 2.300 IOPS por segundo, enquanto deixar sem atividade.

## <a name="bursting-states"></a>Estados de intermitência
Há três Estados em que o recurso pode estar com a intermitência ativada:
- **Acumulando** – o tráfego de e/s do recurso está usando menos do que o destino de desempenho. Acumular créditos de intermitência para IOPS e MB/s é feito separadamente um do outro. Seu recurso pode estar acumulando créditos de IOPS e créditos de MB/s ou vice-versa.
- **Intermitência** – o tráfego do recurso está usando mais do que o destino de desempenho. O tráfego de intermitência consumirá de forma independente os créditos de IOPS ou largura de banda.
- **Constante** – o tráfego do recurso está exatamente no destino de desempenho.

## <a name="examples-of-bursting"></a>Exemplos de intermitência

Os exemplos a seguir mostram como a intermitência funciona com várias combinações de VM e disco. Para facilitar o acompanhamento dos exemplos, vamos nos concentrar em MB/s, mas a mesma lógica é aplicada de forma independente ao IOPS.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Máquina virtual não expansível com discos com intermitência
**Combinação de VM e disco:** 
- Standard_D8as_v4 
    - MB/s não armazenados em cache: 192
- Disco do sistema operacional P4
    - MB/s provisionados: 25
    - Máximo de MB/s de intermitência: 170 
- 2 discos de dados P10 
    - MB/s provisionados: 100
    - Máximo de MB/s de intermitência: 170

 Quando a VM é inicializada, ela recupera dados do disco do sistema operacional. Como o disco do sistema operacional faz parte de uma VM que está sendo inicializada, o disco do sistema operacional estará cheio de créditos de intermitência. Esses créditos permitirão que o disco do so estoure sua inicialização em 170 MB/s segundo.

![A VM envia uma solicitação de 192 MB/s de taxa de transferência para o disco do sistema operacional, o disco do sistema operacional responde com dados de 170 MB/s.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-startup.jpg)

Após a conclusão da inicialização, um aplicativo é executado na VM e tem uma carga de trabalho não crítica. Essa carga de trabalho requer 15 MB/S que se espalham uniformemente em todos os discos.

![O aplicativo envia a solicitação de 15 MB/s de taxa de transferência para a VM, a VM recebe solicitação e envia a cada um de seus discos uma solicitação de 5 MB/s, cada disco retorna 5 MB/s, a VM retorna 15 MB/s para o aplicativo.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-idling.jpg)

Em seguida, o aplicativo precisa processar um trabalho em lote que requer 192 MB/s. 2 MB/s são usados pelo disco do sistema operacional e o restante são divididos uniformemente entre os discos de dados.

![O aplicativo envia a solicitação de 192 MB/s de taxa de transferência para a VM, a VM recebe a solicitação e envia a massa de sua solicitação aos discos de dados (95 MB/s cada) e 2 MB/s para o disco do sistema operacional, os discos de dados são rompidos para atender à demanda e todos os discos retornam a taxa de transferência solicitada para a VM,](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Máquina virtual expansível com discos não intermitentes
**Combinação de VM e disco:** 
- Standard_L8s_v2 
    - MB/s não armazenados em cache: 160
    - Máximo de MB/s de intermitência: 1.280
- Disco do sistema operacional P50
    - MB/s provisionados: 250 
- 2 discos de dados P10 
    - MB/s provisionados: 250

 Após a inicialização inicial, um aplicativo é executado na VM e tem uma carga de trabalho não crítica. Essa carga de trabalho requer 30 MB/s que se espalham uniformemente em todos os discos.
![O aplicativo envia a solicitação de 30 MB/s de taxa de transferência para a VM, a VM recebe solicitação e envia a cada um de seus discos uma solicitação de 10 MB/s, cada disco retorna 10 MB/s, a VM retorna 30 MB/s para o aplicativo.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Em seguida, o aplicativo precisa processar um trabalho em lote que requer 600 MB/s. O Standard_L8s_v2 intermitência para atender a essa demanda e, em seguida, as solicitações para os discos são distribuídas uniformemente para os discos P50.

![O aplicativo envia a solicitação de 600 MB/s de taxa de transferência para a VM, e a VM usa picos para tomar a solicitação e envia a cada um de seus discos uma solicitação de 200 MB/s, cada disco retorna 200 MB/s, as intermitências de VM para retornarem de 600 MB/s para o aplicativo.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Máquina virtual expansível com discos expansível
**Combinação de VM e disco:** 
- Standard_L8s_v2 
    - MB/s não armazenados em cache: 160
    - Máximo de MB/s de intermitência: 1.280
- Disco do sistema operacional P4
    - MB/s provisionados: 25
    - Máximo de MB/s de intermitência: 170 
- 2 discos de dados P4 
    - MB/s provisionados: 25
    - Máximo de MB/s de intermitência: 170 

Quando a VM for iniciada, ela será intermitente para solicitar seu limite de intermitência de 1.280 MB/s do disco do sistema operacional e o disco do sistema operacional responderá com seu desempenho de intermitência de 170 MB/s.

![Na inicialização, a VM é intermitente para enviar uma solicitação de 1.280 MB/s para o disco do sistema operacional, o disco do sistema operacional é estouros para retornar os 1.280 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Após a inicialização, você inicia um aplicativo que tem uma carga de trabalho não crítica. Esse aplicativo requer 15 MB/s que é distribuído uniformemente em todos os discos.

![O aplicativo envia a solicitação de 15 MB/s de taxa de transferência para a VM, a VM recebe solicitação e envia a cada um de seus discos uma solicitação de 5 MB/s, cada disco retorna 5 MB/s, a VM retorna 15 MB/s para o aplicativo.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Em seguida, o aplicativo precisa processar um trabalho em lote que requer 360 MB/s. O Standard_L8s_v2 é intermitente para atender a essa demanda e, em seguida, solicitações. Apenas 20 MB/s são necessários para o disco do sistema operacional. Os 340 MB/s restantes são tratados pelos discos de dados de intermitência P4.

![O aplicativo envia a solicitação de 360 MB/s de taxa de transferência para a VM, a VM usa picos para tomar a solicitação e envia a cada um de seus discos de dados uma solicitação de 170 MB/s e 20 MB/s do disco do sistema operacional, cada disco retorna os MB/s necessários, as intermitências de VM para retornar 360 MB/s para o aplicativo.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)