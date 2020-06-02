---
title: Visão geral sobre a computação confidencial do Azure
description: Visão geral sobre a ACC (computação confidencial do Azure)
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 44006bdfd9ffe6e78380adefe9271f42c0a76f84
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773272"
---
# <a name="confidential-computing-on-azure"></a>Computação confidencial no Azure

A computação confidencial do Azure permite isolar seus dados confidenciais enquanto eles estão sendo processados na nuvem. Muitos setores usam a computação confidencial para proteger os dados. Essas cargas de trabalho incluem:

- Proteção de dados financeiros
- Proteção de informações de pacientes
- Execução de processos de aprendizado de máquina em informações confidenciais
- Execução de algoritmos em conjuntos de dados criptografados provenientes de várias fontes


## <a name="overview"></a>Visão geral
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Sabemos que a proteção de seus dados na nuvem é importante. Ouvimos suas preocupações. Veja algumas perguntas que nossos clientes podem ter ao mover cargas de trabalho confidenciais para a nuvem: 

- Como tenho a certeza de que a Microsoft não acessará dados que não estão criptografados?
- Como faço para impedir ameaças de segurança de administradores privilegiados dentro da minha empresa?
- Quais são outras maneiras de evitar que terceiros acessem dados confidenciais de clientes?

O Microsoft Azure ajuda a minimizar a superfície de ataque para obter uma proteção de dados mais forte. O Azure já oferece muitas ferramentas para proteger [**dados em repouso**](../security/fundamentals/encryption-atrest.md) por meio de modelos como criptografia do lado do cliente e criptografia do lado do servidor. Além disso, o Azure oferece mecanismos para criptografar [**dados em trânsito**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) por meio de protocolos seguros, como TLS e HTTPS. Esta página apresenta um terceiro segmento de criptografia de dados: a criptografia dos **dados em uso**.


## <a name="introduction-to-confidential-computing"></a>Apresentando a computação confidencial <a id="intro to acc"></a>

Computação confidencial é um termo específico desse setor, definido pelo [CCC](https://confidentialcomputing.io/) (Confidential Computing Consortium), uma fundação dedicada à definição e aceleração da adoção da computação confidencial. O CCC define Computação confidencial como a proteção dos dados em uso executando a computação em um TEE (Ambiente de Execução Confiável) baseado em hardware.

Um TEE é um ambiente que impõe a execução somente de código autorizado. Os dados no TEE não podem ser lidos nem adulterados por qualquer código de fora desse ambiente.

### <a name="enclaves"></a>Enclaves

Enclaves são partes protegidas do processador e da memória de um hardware. Não há como ver dados ou código dentro do enclave, mesmo com um depurador. Se as tentativas de código não confiável modificarem o conteúdo na memória enclave, o ambiente será desabilitado e as operações serão negadas.

Ao desenvolver aplicativos, você pode usar [ferramentas de software](#oe-sdk) para blindar partes de do código e dos dados dentro do enclave. Essas ferramentas garantirão que seu código e seus dados não possam ser exibidos nem modificados por ninguém de fora do ambiente confiável. 

Fundamentalmente, imagine um enclave como uma caixa preta. Você coloca o código criptografado e os dados na caixa. De fora da caixa você não consegue ver nada. Você dá ao enclave uma chave para descriptografar os dados; então, os dados são processados e criptografados novamente, antes de serem enviados para fora do enclave.

### <a name="attestation"></a>Atestado

Você desejará obter a verificação e a validação de que o ambiente confiável é seguro. Essa verificação é o processo de atestado. 

O atestado permite que uma terceira parte confiável tenha maior confiança de que seu software está (1) em execução em um enclave e (2) que o enclave está atualizado e seguro. Por exemplo, um enclave solicita que o hardware subjacente gere uma credencial que inclua uma prova de que o enclave existe na plataforma. O relatório pode ser dado a um segundo enclave que verifica se o relatório foi gerado na mesma plataforma.

O atestado deve ser implementado usando um serviço de atestado seguro que seja compatível com o software do sistema e com o chip. [Os serviços de atestado e provisionamento da Intel](https://software.intel.com/sgx/attestation-services) são compatíveis com as máquinas virtuais de computação confidencial do Azure.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Usando o Azure para computação confidencial baseada em nuvem <a id="cc-on-azure"></a>

A computação confidencial do Azure permite que você aproveite os recursos de computação confidencial em um ambiente virtualizado. Agora você pode usar ferramentas, software e infraestrutura de nuvem para criar com base em hardware seguro. 

### <a name="virtual-machines"></a>Máquinas Virtuais

O Azure é o primeiro provedor de nuvem a oferecer computação confidencial em um ambiente virtualizado. Desenvolvemos máquinas virtuais que atuam como uma camada de abstração entre o hardware e seu aplicativo. Você pode executar cargas de trabalho em escala e com opções de redundância e disponibilidade.  

#### <a name="intel-sgx-enabled-virtual-machines"></a>Máquinas Virtuais habilitadas para Intel SGX

Nas máquinas virtuais de computação confidencial do Azure, uma parte do hardware da CPU é reservada para uma porção do código e dos dados de seu aplicativo. Essa parte restrita é a enclave. 

![Modelo de VM](media/overview/hardware-backed-enclave.png)

Atualmente, a infraestrutura de computação confidencial do Azure é composta de um SKU especializado de VMs (máquinas virtuais). Essas VMs são executadas em processadores Intel com Software Guard Extension (Intel SGX). [O Intel SGX](https://intel.com/sgx) é o componente que proporciona a maior proteção que nós revelamos com a computação confidencial. 

Atualmente, o Azure oferece a [Série DCsv2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) baseada na tecnologia Intel SGX para a criação de enclave baseada em hardware. Você pode criar aplicativos seguros baseados em enclave para serem executados na série DCsv2 de VMs para proteger os dados do seu aplicativo e o código em uso. 

[Leia mais](virtual-machine-solutions.md) sobre a implantação de máquinas virtuais de computação confidencial do Azure com enclaves confiáveis baseados em hardware.

## <a name="application-development"></a>Desenvolvimento de aplicativos <a id="application-development"></a>

Para aproveitar o potencial dos enclaves e dos ambientes isolados, você precisará usar ferramentas compatíveis com a computação confidencial. Há várias ferramentas que compatíveis com o desenvolvimento de aplicativos de enclave. Por exemplo, você pode usar essas estruturas de software livre: 

- [O Open Enclave SDK (Software Development Kit)](https://github.com/openenclave/openenclave)
- [O CCF (Confidential Consortium Framework)](https://github.com/Microsoft/CCF)

### <a name="overview"></a>Visão geral

Um aplicativo criado com enclaves é particionado de duas maneiras:
1. Um componente "não confiável" (o host)
1. Um componente "confiável" (o enclave)

**O host** é onde o aplicativo enclave está em execução e é um ambiente não confiável. O código de enclave implantado no host não pode ser acessado pelo host. 

**O enclave** é onde o código do aplicativo e seus dados/memória em cache são executados. As computações protegidas devem ocorrer no enclave para garantir que os segredos e os dados confidenciais permaneçam protegidos. 

Durante o design do aplicativo, é importante identificar e determinar qual parte do aplicativo precisa ser executada nos enclaves. O código que você escolhe colocar no componente confiável é isolado do restante do aplicativo. Depois que o enclave é inicializado e o código é carregado na memória, esse código não pode ser lido nem alterado usando os componentes não confiáveis. 

### <a name="open-enclave-software-development-kit-oe-sdk"></a>OE SDK (Open Enclave Software Development Kit) <a id="oe-sdk"></a>

Use uma biblioteca ou uma estrutura compatível com seu provedor se quiser escrever um código que seja executado em um enclave. O [OE SDK](https://github.com/openenclave/openenclave) (Open Enclave SDK) é um SDK de software livre que permite a abstração em um hardware habilitado para computação confidencial diferente. 

O OE SDK é criado para ser uma camada de abstração única em qualquer hardware em qualquer CSP. O OE SDK pode ser usado sobre máquinas virtuais de computação confidencial do Azure para criar e executar aplicativos sobre enclaves.

## <a name="next-steps"></a>Próximas etapas

Implante uma máquina virtual da Série DCsv2 e instale o OE SDK nela.

> [!div class="nextstepaction"]
> [Implantar uma VM de computação confidencial no Azure Marketplace](quick-create-marketplace.md)