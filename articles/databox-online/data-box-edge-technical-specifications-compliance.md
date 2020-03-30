---
title: Especificações técnicas e conformidade da Microsoft Azure Data Box Edge| Microsoft Docs
description: Conheça as especificações técnicas e a conformidade do seu Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: b646ee9b727d5adf4ec1c8b5c769b3d8f5c0fc1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252042"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Especificações técnicas da Azure Data Box Edge

Os componentes de hardware do seu dispositivo Microsoft Azure Data Box Edge aderem às especificações técnicas e às normas regulamentares descritas neste artigo. As especificações técnicas descrevem as unidades de alimentação (PSUs), capacidade de armazenamento, gabinetes e normas ambientais. 

## <a name="compute-memory-specifications"></a>Computação, especificações de memória

O dispositivo Data Box Edge tem as seguintes especificações para computação e memória:

| Especificação           | Valor                  |
|-------------------------|----------------------------|
| CPU    | CPU de 2 X 10 núcleos                     |
| Memória              | 128 GB de RAM                  |


## <a name="fpga-specifications"></a>Especificações do FPGA

Um Field Programmable Gate Array (FPGA) está incluído em todos os dispositivos Data Box Edge que permitem cenários de Machine Learning (ML). 

| Especificação           | Valor                  |
|-------------------------|----------------------------|
| Fpga   | Intel Arria 10 <br> Os modelos disponíveis de Deep Neural Network (DNN) são os mesmos [suportados por instâncias FPGA em nuvem.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure)| 


## <a name="power-supply-unit-specifications"></a>Especificações da unidade de alimentação

O dispositivo Data Box Edge tem duas unidades de alimentação de 100-240 V (PSUs) com ventiladores de alto desempenho. As duas UPAs fornecem uma configuração de energia redundante. Se uma PSU falhar, o dispositivo continuará a operar normalmente na outra PSU até que o módulo com falha seja substituído. A tabela a seguir lista as especificações técnicas das UPAs.

| Especificação           | 750 W PSU                  |
|-------------------------|----------------------------|
| Alimentação de saída máxima    | 750 W                     |
| Frequência               | 50/60 Hz                   |
| Seleção de faixa de tensão | Variação automática: 100-240 V AC |
| Conectado com a máquina ligada           | Sim                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="network-interface-specifications"></a>Especificações da interface de rede

O dispositivo Data Box Edge tem 6 interfaces de rede, PORT1-PORT6.

| Especificação           | Descrição                 |
|-------------------------|----------------------------|
|  Interfaces de rede    | 2 interfaces de 1 GbE – 1 gerenciamento, não configurável pelo usuário, usado para configuração inicial. A outra interface é configurável pelo usuário e pode ser usada para transferência de dados e é DHCP por padrão. <br>2 interfaces de 25 GbE – Elas também podem ser operadas como interfaces de 10 GbE. Essas interfaces de dados podem ser configuradas pelo usuário como DHCP (padrão) ou estáticas. <br> 2 interfaces de 25 GbE – Essas interfaces de dados podem ser configuradas pelo usuário como DHCP (padrão) ou estáticas.                  |

## <a name="storage-specifications"></a>Especificações do armazenamento

Os dispositivos Data Box Edge possuem SSDs NVMe de 9 X 2,5", cada um com capacidade de 1,6 TB. Desses SSDs, 1 é um disco do sistema operacional, e os outros 8 são discos de dados. A capacidade total utilizável para o dispositivo é de aproximadamente 12,5 TB. A tabela a seguir tem os detalhes para a capacidade de armazenamento do dispositivo.

|     Especificação                          |     Valor             |
|--------------------------------------------|-----------------------|
|    Número de SSDs (unidades de estado sólido)     |    8                  |
|    Capacidade de SSD único                     |    1,6 TB             |
|    Capacidade total                          |    12,8 TB            |
|    Capacidade total utilizável*                  |    ~ 12,5 TB            |

**Algum espaço é reservado para uso interno.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões do compartimento e especificações de peso

As tabelas a seguir listam as diversas especificações de compartimento para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do compartimento

A tabela a seguir lista as dimensões do compartimento em milímetros e em polegadas.

|     Compartimento     |     Milímetros     |     Polegadas     |
|-------------------|---------------------|----------------|
|    Altura         |    44.45            |    1.75"          |
|    Largura          |    434.1           |    17.09"          |
|    Comprimento          |    740.4           |    29.15"          |

A tabela a seguir lista as dimensões do pacote de transporte em milímetros e polegadas.

|     Pacote     |     Milímetros     |     Polegadas     |
|-------------------|---------------------|----------------|
|    Altura         |    311,2            |    12,25          |
|    Largura          |    642,8          |    25,31          |
|    Comprimento          |   1.051,1          |    41,38          |

### <a name="enclosure-weight"></a>Peso do compartimento

O pacote do dispositivo pesa 66 lbs. e requer duas pessoas para lidar com isso. O peso do dispositivo depende da configuração do gabinete.

|     Compartimento                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso total, incluindo a embalagem       |    61 libras.          |
|    Peso do dispositivo                       |    35 libras.          |

## <a name="enclosure-environment-specifications"></a>Especificações do ambiente de compartimento

Esta seção lista as especificações relacionadas ao ambiente do gabinete, como temperatura, umidade e altitude.

### <a name="temperature-and-humidity"></a>Temperatura e umidade

|     Compartimento         |     Faixa de temperatura ambiente     |     Umidade relativa do ambiente     |     Ponto de condensação máximo     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operacional        |    10°C - 35°C (50°F - 86°F)         |    10% a 80% não condensação.         |    29° C (84° F)            |
|    Não operacional    |    -40°C a 65°C (-40°F - 149°F)     |    5% a 95% não condensação.          |    33 °C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Fluxo de ar, altitude, choque, vibração, orientação, segurança e EMC

|     Compartimento                           |     Especificações operacionais                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Fluxo de ar                              |    O ar do sistema flui da frente para a traseira. O sistema deve ser operado com uma instalação de exaustão traseira de baixa pressão. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Altitude máxima, operacional        |    3048 metros (10.000 pés) com temperatura máxima de operação desclassificada determinada pelas [especificações de desclassificação da temperatura operacional](#operating-temperature-de-rating-specifications).                                                                                |
|    Altitude máxima, não operacional    |    12.000 metros (39.370 pés)                                                                                                                                                                                         |
|    Choque, operacional                   |    6 G por 11 milissegundos em 6 orientações                                                                                                                                                                         |
|    Choque, não operacional               |    71 G por 2 milissegundos em 6 orientações                                                                                                                                                                           |
|    Vibração, operacional               |    0,26 G<sub>RMS</sub> 5 Hz a 350 Hz aleatório                                                                                                                                                                                     |
|    Vibração, não operacional           |    1,88 G<sub>RMS</sub> 10 Hz a 500 Hz por 15 minutos (todos os seis lados testados.)                                                                                                                                                  |
|    Orientação e montagem             |    Montagem de rack de 19"                                                                                                                                                                                        |
|    Segurança e aprovações                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    Regulamento da Comissão (UE) n.º 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Especificações de desclassificação da temperatura de operação

|     Desclassificação da temperatura de operação     |     Faixa de temperatura ambiente                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Até 35°C (95°F)                       |    A temperatura máxima é reduzida em 1°C/300 m (1°F/547 pés) acima de 950 m (3.117 pés).    |
|    35°C a 40°C (95°F a 104°F)            |    A temperatura máxima é reduzida em 1°C/175 m (1°F/319 pés) acima de 950 m (3.117 pés).    |
|    40°C a 45°C (104°F a 113°F)           |    A temperatura máxima é reduzida em 1°C/125 m (1°F/228 pés) acima de 950 m (3.117 pés).    |


## <a name="next-steps"></a>Próximas etapas

- [Implante seu Azure Data Box Edge](data-box-edge-deploy-prep.md)
