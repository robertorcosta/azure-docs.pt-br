---
title: Gerente de segurança do Azure IoT Edge - Azure IoT Edge
description: Gerencia a postura de segurança do dispositivo IoT Edge e a integridade dos serviços de segurança.
services: iot-edge
keywords: segurança, o elemento seguro, o enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d5cfa16196a8815b711fd5277a80f6eb67d3a388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548689"
---
# <a name="azure-iot-edge-security-manager"></a>Gerenciador de segurança do Azure IoT Edge

O gerenciador de segurança do Azure IoT Edge é um núcleo de segurança bem delimitado para proteger o dispositivo IoT Edge e todos os seus componentes, abstraindo o hardware de silício seguro. O gerente de segurança é o ponto focal para o endurecimento da segurança e fornece ponto de integração de tecnologia para os fabricantes de equipamentos originais (OEM).

![Gerenciador de segurança do Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

O gerenciador de segurança IoT Edge visa defender a integridade do dispositivo IoT Edge e todas as operações de software inerentes. O gerenciador de segurança transita a confiança do hardware raiz de hardware subjacente do hardware (se disponível) para inicializar o tempo de execução do IoT Edge e monitorar as operações em andamento.  O gerenciador de segurança IoT Edge inclui software que trabalha em conjunto com hardware de silício seguro, quando disponível, para ajudar a fornecer as mais altas garantias de segurança possíveis.  

As responsabilidades do gerenciador de segurança do IoT Edge incluem (mas sem limitação):

* Inicialização segura e medida do dispositivo Azure IoT Edge.
* Fornecimento de identidade de dispositivo e transição de confiança, quando aplicável.
* Hospede e proteja componentes de dispositivos de serviços em nuvem, como o Serviço de provisionamento de dispositivos.
* Forneça com segurança identidades exclusivas aos módulos do IoT Edge.
* Gatekeeper para dispositivo hardware raiz de confiança através de serviços notariais.
* Monitora a integridade das operações do IoT Edge no runtime.

O gerenciador de segurança IoT Edge inclui três componentes:

* Daemon de segurança do IoT Edge.
* Camada de abstração da plataforma do módulo de segurança de hardware (HSM PAL).
* Raiz de silício de hardware opcional ou altamente recomendada de hardware ou HSM.

## <a name="the-iot-edge-security-daemon"></a>O daemon de segurança do IoT Edge

O daemon de segurança IoT Edge é responsável pelas operações lógicas do gerente de segurança IoT Edge. Ele representa uma parte significativa da base de computação confiável do dispositivo IoT Edge.

### <a name="design-principles"></a>Princípios de design

O daemon de segurança do IoT Edge segue dois princípios básicos: maximizar a integridade operacional e minimizar a sobrecarga e variações.

#### <a name="maximize-operational-integrity"></a>Maximizar a integridade operacional

O daemon de segurança IoT Edge opera com a mais alta integridade possível dentro da capacidade de defesa de qualquer raiz de hardware de confiança. Com a integração adequada, a raiz do hardware de confiança mede e monitora o daemon de segurança estaticamente e em runtime para resistir à violação.

O acesso físico é sempre uma ameaça aos dispositivos IoT. A raiz de hardware de confiança desempenha um papel importante na defesa da integridade do daemon de segurança do IoT Edge.  A raiz de confiança do hardware vem em duas variedades:

* elementos seguros para a proteção de informações confidenciais, como segredos e chaves criptográficas.
* enclaves seguros para a proteção de segredos, como chaves, e cargas de trabalho confidenciais, como medição e faturamento.

Existem dois tipos de ambientes de execução para usar a raiz de hardware de confiança:

* O ambiente de execução padrão ou rico (REE) que depende do uso de elementos seguros para proteger informações confidenciais.
* O ambiente de execução confiável (TEE) que conta com o uso da tecnologia secure enclave para proteger informações confidenciais e oferecer proteção à execução de software.

Para dispositivos que usam enclaves seguros como raiz de hardware de confiança, a lógica sensível dentro do daemon de segurança ioT Edge deve estar dentro do enclave.  Partes não sensíveis do daemon de segurança podem estar fora do TEE.  De qualquer forma, os fabricantes originais de design (ODM) e os fabricantes de equipamentos originais (OEM) devem estender a confiança de seu HSM para medir e defender a integridade do daemon de segurança IoT Edge no boot e tempo de execução.

#### <a name="minimize-bloat-and-churn"></a>Minimizar a sobrecarga e a variação

Outro princípio fundamental para o daemon de segurança IoT Edge é minimizar a rotatividade.  Para obter o mais alto nível de confiança, o daemon de segurança IoT Edge pode se associar à raiz de hardware do dispositivo e operar como código nativo.  É comum que esses tipos de realizações atualizem o software daemon através da raiz de hardware dos caminhos de atualização segura do Trust (em oposição aos mecanismos de atualização fornecidos pelo SO), o que pode ser desafiador em alguns cenários.  Embora a renovação de segurança seja recomendada para dispositivos IoT, requisitos excessivos de atualização ou grandes cargas de atualização podem expandir a superfície de ameaça de muitas maneiras.  Os exemplos incluem a omissão de atualizações para maximizar a disponibilidade operacional ou a raiz do hardware de confiança, muito restrita para processar grandes cargas úteis de atualização.  Como tal, o design do daemon de segurança IoT Edge é conciso para manter a pegada e a base de computação confiável pequena e minimizar os requisitos de atualização.

### <a name="architecture-of-iot-edge-security-daemon"></a>Arquitetura do daemon de segurança do IoT Edge

![Daemon de segurança do Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

O daemon de segurança IoT Edge aproveita qualquer raiz de hardware disponível de tecnologia de confiança para endurecimento de segurança.  Ele também permite uma operação em mundo dividido entre um ambiente de execução padrão/rico (REE) e um ambiente de execução confiável (TEE) quando as tecnologias de hardware oferecem ambientes de execução confiáveis. Interfaces específicas de função permitem que os principais componentes do IoT Edge garantam a integridade do dispositivo IoT Edge e suas operações.

#### <a name="cloud-interface"></a>Interface de nuvem

A interface na nuvem permite que o daemon de segurança do IoT Edge acesse serviços de nuvem, como elogios na nuvem à segurança do dispositivo, como renovação de segurança.  Por exemplo, o daemon de segurança IoT Edge atualmente usa essa interface para acessar o Serviço de [Provisionamento de Dispositivos](https://docs.microsoft.com/azure/iot-dps/) Hub Azure IoT para o gerenciamento do ciclo de vida da identidade do dispositivo.  

#### <a name="management-api"></a>API de gerenciamento

O daemon de segurança do IoT Edge oferece uma API de gerenciamento, que é chamada pelo agente IoT Edge ao criar/iniciar/parar/remover um módulo IoT Edge. O daemon de segurança armazena "registros" para todos os módulos ativos. Esses registros mapeiam a identidade de um módulo para algumas propriedades do módulo. Por exemplo, essas propriedades do módulo incluem o identificador de processo (pid) do processo em execução no contêiner e o hash do conteúdo do contêiner docker.

Essas propriedades são usadas pela API de carga de trabalho (descrita abaixo) para verificar se o chamador está autorizado para uma ação.

A API de gerenciamento é uma API privilegiada, callable somente do agente IoT Edge.  Uma vez que o daemon de segurança IoT Edge bootstraps e inicia o agente IoT Edge, ele verifica que o agente IoT Edge não foi adulterado, então ele pode criar um registro implícito para o agente IoT Edge. O mesmo processo de atestado que a API de carga de trabalho usa também restringe o acesso à API de gerenciamento apenas ao agente IoT Edge.

#### <a name="container-api"></a>Contêiner de API

A API do contêiner interage com o sistema de contêineres em uso para o gerenciamento de módulos, como Moby ou Docker.

#### <a name="workload-api"></a>API de carga de trabalho

A API de carga de trabalho é acessível a todos os módulos. Ele fornece uma prova de identidade, seja como um token assinado com raiz HSM ou um certificado X509, e o pacote de confiança correspondente a um módulo. O pacote confiável contém certificados de Autoridade de Certificação para todos os outros servidores nos quais os módulos devem confiar.

O daemon de segurança IoT Edge usa um processo de atestado para proteger esta API. Quando um módulo chama essa API, o daemon de segurança tenta encontrar um registro para a identidade. Se bem sucedido, usa as propriedades do registro para medir o módulo. Se o resultado do processo de medição corresponder ao registro, uma nova prova de identidade será gerada. Os certificados de Autoridade de Certificação correspondentes (pacote confiável) serão retornados ao módulo.  O módulo usa esse certificado para se conectar ao Hub IoT, outros módulos ou iniciar um servidor. Quando o token ou certificado assinado se aproxima do vencimento, é responsabilidade do módulo solicitar um novo certificado.

### <a name="integration-and-maintenance"></a>Integração e manutenção

A Microsoft mantém a principal base de código para o daemon de segurança [IoT Edge no GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalação e as atualizações

A instalação e as atualizações do daemon de segurança IoT Edge são gerenciadas por meio do sistema de gerenciamento de pacotes do sistema operacional. Os dispositivos IoT Edge com raiz de hardware de confiança devem fornecer proteção adicional à integridade do daemon, gerenciando seu ciclo de vida por meio dos sistemas de gerenciamento seguro de inicialização e de atualizações. Os fabricantes de dispositivos devem explorar esses caminhos com base em suas respectivas capacidades de dispositivo.

#### <a name="versioning"></a>Controle de versão

O runtime do IoT Edge rastreia e relata a versão do daemon de segurança IoT Edge. A versão é relatada como o atributo *runtime.platform.version* da propriedade relatada do módulo do agente IoT Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Camada de abstração de plataforma hardware security module (HSM PAL)

O HSM PAL abstrai toda a raiz do hardware de confiança para isolar o desenvolvedor ou usuário do IoT Edge de suas complexidades.  Ele inclui uma combinação de aPI (Application Programming Interface, interface de programação de aplicativos) e procedimentos de comunicação transdomínio, por exemplo, comunicação entre um ambiente de execução padrão e um enclave seguro.  A implementação real do HSM PAL depende do hardware seguro específico em uso. Sua existência permite o uso de praticamente qualquer hardware de silício seguro.

## <a name="secure-silicon-root-of-trust-hardware"></a>Raiz de silício seguro do hardware de confiança

O silício seguro é necessário para ancorar a confiança dentro do hardware do dispositivo IoT Edge.  O silício seguro é variado para incluir o Trusted Platform Module (TPM), o Secure Element (eSE) incorporado, o ARM TrustZone, o Intel SGX e as tecnologias de silício seguras personalizadas.  Recomenda-se o uso de uma raiz segura de silício de confiança nos dispositivos, dadas as ameaças associadas à acessibilidade física de dispositivos IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integração e manutenção do gerenciador de segurança IoT Edge

O objetivo do gerenciador de segurança do IoT Edge é identificar e isolar os componentes que defendem a segurança e a integridade da plataforma Azure IoT Edge para proteção personalizada. Terceiros, como fabricantes de dispositivos, devem fazer uso de recursos de segurança personalizados disponíveis com o hardware do dispositivo.  Confira a próxima seção de etapas para links que demonstram como proteger o gerenciador de segurança da IoT do Azure com o TPM (Trusted Platform Module) nas plataformas Linux e Windows. Esses exemplos usam software ou TPMs virtuais, mas se aplicam diretamente ao uso de dispositivos TPM discretos.  

## <a name="next-steps"></a>Próximas etapas

Leia o blog em [ Protegendo a borda inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Crie e provisione um [dispositivo do IoT Edge com uma TPM virtual em uma máquina virtual do Linux](how-to-auto-provision-simulated-device-linux.md).

Crie e provisione um [dispositivo IoT Edge com um TPM simulado no Windows](how-to-auto-provision-simulated-device-windows.md).
