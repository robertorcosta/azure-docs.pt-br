---
title: Saiba como o gerenciador de segurança protege dispositivos, software – Azure IoT Edge | Microsoft Docs
description: Gerencia a postura de segurança do dispositivo IoT Edge e a integridade dos serviços de segurança.
services: iot-edge
keywords: segurança, o elemento seguro, o enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 90cb7cf0a30ea0ebfe00454288de25ddf6e58d52
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457539"
---
# <a name="azure-iot-edge-security-manager"></a>Gerenciador de segurança do Azure IoT Edge

O gerenciador de segurança do Azure IoT Edge é um núcleo de segurança bem delimitado para proteger o dispositivo IoT Edge e todos os seus componentes, abstraindo o hardware de silício seguro. It is the focal point for security hardening and provides technology integration point to original equipment manufacturers (OEM).

![Gerenciador de segurança do Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

O gerenciador de segurança IoT Edge visa defender a integridade do dispositivo IoT Edge e todas as operações de software inerentes. The security manager transitions trust from underlying hardware root of trust hardware (if available) to bootstrap the IoT Edge runtime and monitor ongoing operations.  O gerenciador de segurança IoT Edge inclui software que trabalha em conjunto com hardware de silício seguro, quando disponível, para ajudar a fornecer as mais altas garantias de segurança possíveis.  

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

The IoT Edge security daemon is responsible for the logical operations of IoT Edge security manager. It represents a significant portion of the trusted computing base of the IoT Edge device. 

### <a name="design-principles"></a>Princípios de design

O daemon de segurança do IoT Edge segue dois princípios básicos: maximizar a integridade operacional e minimizar a sobrecarga e variações.

#### <a name="maximize-operational-integrity"></a>Maximizar a integridade operacional

The IoT Edge security daemon operates with the highest integrity possible within the defense capability of any given root of trust hardware. Com a integração adequada, a raiz do hardware de confiança mede e monitora o daemon de segurança estaticamente e em runtime para resistir à violação.

O acesso físico é sempre uma ameaça aos dispositivos IoT. A raiz de hardware de confiança desempenha um papel importante na defesa da integridade do daemon de segurança do IoT Edge.  Hardware root of trust come in two varieties:

* elementos seguros para a proteção de informações confidenciais, como segredos e chaves criptográficas.
* enclaves seguros para a proteção de segredos, como chaves, e cargas de trabalho confidenciais, como medição e faturamento.

Two kinds of execution environments exist to use hardware root of trust:

* The standard or rich execution environment (REE) that relies on the use of secure elements to protect sensitive information.
* The trusted execution environment (TEE) that relies on the use of secure enclave technology to protect sensitive information and offer protection to software execution.

For devices using secure enclaves as hardware root of trust, sensitive logic within IoT Edge security daemon should be inside the enclave.  Non-sensitive portions of the security daemon can be outside of the TEE.  In any case, original design manufacturers (ODM) and original equipment manufacturers (OEM) should extend trust from their HSM to measure and defend the integrity of the IoT Edge security daemon at boot and runtime.

#### <a name="minimize-bloat-and-churn"></a>Minimizar a sobrecarga e a variação

Outro princípio fundamental para o daemon de segurança IoT Edge é minimizar a rotatividade.  Para obter o mais alto nível de confiança, o daemon de segurança IoT Edge pode se associar à raiz de hardware do dispositivo e operar como código nativo.  It's common for these types of realizations to update the daemon software through the hardware root of trust's secure update paths (as opposed to OS provided update mechanisms), which can be challenging in some scenarios.  While security renewal is recommended for IoT devices, excessive update requirements or large update payloads can expand the threat surface in many ways.  Os exemplos incluem a omissão de atualizações para maximizar a disponibilidade operacional ou a raiz do hardware de confiança, muito restrita para processar grandes cargas úteis de atualização.  As such, the design of IoT Edge security daemon is concise to keep the footprint and trusted computing base small and to minimize update requirements.

### <a name="architecture-of-iot-edge-security-daemon"></a>Arquitetura do daemon de segurança do IoT Edge

![Daemon de segurança do Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

The IoT Edge security daemon takes advantage of any available hardware root of trust technology for security hardening.  It also allows for split-world operation between a standard/rich execution environment (REE) and a trusted execution environment (TEE) when hardware technologies offer trusted execution environments. Role-specific interfaces enable the major components of IoT Edge to assure the integrity of the IoT Edge device and its operations.

#### <a name="cloud-interface"></a>Interface de nuvem

The cloud interface allows the IoT Edge security daemon to access cloud services such as cloud compliments to device security like security renewal.  For example, the IoT Edge security daemon currently uses this interface to access the Azure IoT Hub [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/) for device identity lifecycle management.  

#### <a name="management-api"></a>API de gerenciamento

IoT Edge security daemon offers a management API, which is called by the IoT Edge agent when creating/starting/stopping/removing an IoT Edge module. The security daemon stores “registrations” for all active modules. Esses registros mapeiam a identidade de um módulo para algumas propriedades do módulo. Alguns exemplos dessas propriedades são o identificador do processo (pid) do processo em execução no contêiner ou o hash do conteúdo do contêiner do estivador.

These properties are used by the workload API (described below) to verify that the caller is authorized to perform an action.

The management API is a privileged API, callable only from the IoT Edge agent.  Como o daemon de segurança IoT Edge inicializa e inicia o agente IoT Edge, ele pode criar um registro implícito para o agente IoT Edge, após ter atestado que o agente IoT Edge não foi adulterado. The same attestation process that the workload API uses also restricts access to the management API to only the IoT Edge agent.

#### <a name="container-api"></a>Contêiner de API

The container API interacts with the container system in use for module management, like Moby or Docker.

#### <a name="workload-api"></a>API de carga de trabalho

The workload API is accessible to all modules. It provides proof of identity, either as an HSM rooted signed token or an X509 certificate, and the corresponding trust bundle to a module. O pacote confiável contém certificados de Autoridade de Certificação para todos os outros servidores nos quais os módulos devem confiar.

The IoT Edge security daemon uses an attestation process to guard this API. When a module calls this API, the security daemon attempts to find a registration for the identity. Se bem sucedido, usa as propriedades do registro para medir o módulo. If the result of the measurement process matches the registration, a new proof of identity is generated. Os certificados de Autoridade de Certificação correspondentes (pacote confiável) serão retornados ao módulo.  O módulo usa esse certificado para se conectar ao Hub IoT, outros módulos ou iniciar um servidor. When the signed token or certificate nears expiration, it's the responsibility of the module to request a new certificate. 

### <a name="integration-and-maintenance"></a>Integração e manutenção

A Microsoft mantém a principal base de código para o daemon de segurança [IoT Edge no GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalação e as atualizações

A instalação e as atualizações do daemon de segurança IoT Edge são gerenciadas por meio do sistema de gerenciamento de pacotes do sistema operacional. Os dispositivos IoT Edge com raiz de hardware de confiança devem fornecer proteção adicional à integridade do daemon, gerenciando seu ciclo de vida por meio dos sistemas de gerenciamento seguro de inicialização e de atualizações. Device makers should explore these avenues based on their respective device capabilities.

#### <a name="versioning"></a>Controle de versão

O runtime do IoT Edge rastreia e relata a versão do daemon de segurança IoT Edge. A versão é relatada como o atributo *runtime.platform.version* da propriedade relatada do módulo do agente IoT Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Camada de abstração de plataforma hardware security module (HSM PAL)

O HSM PAL abstrai toda a raiz do hardware de confiança para isolar o desenvolvedor ou usuário do IoT Edge de suas complexidades.  It includes a combination of application programming interface (API) and trans-domain communication procedures, for example communication between a standard execution environment and a secure enclave.  A implementação real do HSM PAL depende do hardware seguro específico em uso. Sua existência permite o uso de praticamente qualquer hardware de silício seguro.

## <a name="secure-silicon-root-of-trust-hardware"></a>Raiz de silício seguro do hardware de confiança

O silício seguro é necessário para ancorar a confiança dentro do hardware do dispositivo IoT Edge.  O silício seguro é variado para incluir o Trusted Platform Module (TPM), o Secure Element (eSE) incorporado, o ARM TrustZone, o Intel SGX e as tecnologias de silício seguras personalizadas.  The use of secure silicon root of trust in devices is recommended given the threats associated with physical accessibility of IoT devices.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integração e manutenção do gerenciador de segurança IoT Edge

O objetivo do gerenciador de segurança do IoT Edge é identificar e isolar os componentes que defendem a segurança e a integridade da plataforma Azure IoT Edge para proteção personalizada. Terceiros, como fabricantes de dispositivos, devem fazer uso de recursos de segurança personalizados disponíveis com o hardware do dispositivo.  Confira a próxima seção de etapas para links que demonstram como proteger o gerenciador de segurança da IoT do Azure com o TPM (Trusted Platform Module) nas plataformas Linux e Windows. Esses exemplos usam software ou TPMs virtuais, mas se aplicam diretamente ao uso de dispositivos TPM discretos.  

## <a name="next-steps"></a>Próximos passos

Leia o blog em [ Protegendo a borda inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Crie e provisione um [dispositivo do IoT Edge com uma TPM virtual em uma máquina virtual do Linux](how-to-auto-provision-simulated-device-linux.md).

Crie e provisione um [dispositivo IoT Edge com um TPM simulado no Windows](how-to-auto-provision-simulated-device-windows.md).
