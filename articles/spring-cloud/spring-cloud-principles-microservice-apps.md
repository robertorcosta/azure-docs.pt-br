---
title: Java e sistema operacional base para aplicativos de microserviço do Azure Spring Cloud
description: Princípios para manter o sistema operacional de Java e base íntegro para aplicativos de microserviço do Azure Spring Cloud
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.custom: devx-track-java
ms.openlocfilehash: a8e1d43138e0b7481ebb89d747fa26df9470a09f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037128"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>Java e sistema operacional base para Aplicativos de microsserviços do Spring
Veja a seguir os princípios para manter o sistema operacional de Java e base íntegro para aplicativos de microatendimento Spring.
## <a name="principles-for-healthy-java-and-base-os"></a>Princípios para Java íntegro e so base
* Deve ser o mesmo sistema operacional base em camadas-básico | Padrão | Especiais.
    * Atualmente, os aplicativos no Azure Spring Cloud usam uma combinação de Debian 10 e Ubuntu 18, 4.
    * O serviço de compilação do VMware usa o Ubuntu 18, 4.
* Deve ser o mesmo sistema operacional base, independentemente dos pontos de início da implantação-origem | VIDRO
    * Atualmente, os aplicativos no Azure Spring Cloud usam uma combinação de Debian 10 e Ubuntu 18, 4.
* O sistema operacional base deve estar livre de vulnerabilidades de segurança.
    * O sistema operacional base Debian 10 tem 147 abrir CVEs.
    * O sistema operacional de base Ubuntu 18, 4 tem 132 abra CVEs.
* Deve usar o JRE-sem periféricos.
    * Atualmente, os aplicativos no Azure Spring Cloud usam o JDK. JRE-sem periféricos é uma imagem menor.
* Deve usar as compilações mais recentes do Java.
    * Atualmente, os aplicativos no Azure Spring Cloud usam o Build 242 do Java 8. Essa é uma compilação desatualizada.
 
Os sistemas azul examinarão continuamente as alterações nos sistemas operacionais de base e manterão as últimas imagens criadas atualizadas. O Azure Spring Cloud procura alterações em imagens e as atualiza continuamente entre implantações.
 
## <a name="faq-for-azure-spring-cloud"></a>Perguntas frequentes do Azure Spring Cloud

* Quais versões do Java têm suporte? Versão principal e número de Build.
    * Suporte a versões LTS-Java 8 e 11.
    * Usa a compilação mais recente-por exemplo, no momento, o Java 8 Build 252 e o Java 11 Build 7.
* Quem construiu esses tempos de execução Java?
    * Sistemas azul.
* Qual é o sistema operacional base para as imagens?
    * Ubuntu 20, 4 LTS (focal fossa). Os aplicativos continuarão a permanecer na versão mais recente do LTS do Ubuntu.
    * Consulte [Ubuntu 20, 4 LTS (focal fossa)](http://releases.ubuntu.com/focal/)
* Como posso baixar um tempo de execução Java com suporte para dev local? 
    * Consulte [instalar o JDK para Azure e Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)
* Como posso obter suporte para problemas no nível do tempo de execução Java?
    * Abra um tíquete de suporte com o suporte do Azure.
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Implantação padrão no Azure Spring Cloud

> ![Implantação padrão](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>Próximas etapas
* [Início Rápido: Iniciar um aplicativo existente do Azure Spring Cloud usando o portal do Azure](spring-cloud-quickstart-launch-app-portal.md)
* [Suporte a longo prazo Java para Azure e Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)
