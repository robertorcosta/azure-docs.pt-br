---
title: Central de segurança do Azure e registro de contêiner do Azure
description: Saiba como verificar seus registros de contêiner com a central de segurança do Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2020
ms.author: memildin
ms.openlocfilehash: 767ad928779743ec28dbe3da8be6154a1fe0b0ab
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042302"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Verificação de imagem do registro de contêiner do Azure por central de segurança

O ACR (registro de contêiner do Azure) é um serviço de registro gerenciado e privado do Docker que armazena e gerencia suas imagens de contêiner para implantações do Azure em um registro central. Ele é baseado no registro do Docker 2,0 de código aberto.

Se você estiver na camada Standard da central de segurança do Azure, poderá adicionar o pacote de registros de contêiner. Esse recurso opcional traz uma visibilidade mais profunda das vulnerabilidades das imagens em seus registros baseados em Azure Resource Manager. Habilite ou desabilite o pacote no nível de assinatura para abranger todos os registros em uma assinatura. Esse recurso é cobrado por imagem, conforme mostrado na [página de preços](security-center-pricing.md). Habilitando o pacote de registros de contêiner, o garante que a central de segurança esteja pronta para verificar imagens que são enviadas por push ao registro. 

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|Disponibilidade Geral|
|Refere|Camada padrão|
|Registros e imagens com suporte:|![Sim ](./media/icons/yes-icon.png) registros de ACR hospedados pelo Linux que são acessíveis pela Internet pública e fornecem acesso ao shell.<br>![Não há ](./media/icons/yes-icon.png) registros de ACR hospedados pelo Windows.<br>![Nenhum ](./media/icons/yes-icon.png) registro ' particular '-a central de segurança exige que seus registros sejam acessíveis pela Internet pública. A central de segurança não pode se conectar ou verificar registros com acesso limitado com um firewall, um ponto de extremidade de serviço ou pontos de extremidades privados, como o link privado do Azure.<br>![Não há imagens de super nada, como imagens de ](./media/icons/yes-icon.png) [rascunho do Docker](https://hub.docker.com/_/scratch/) ou imagens "Distroless" que contenham apenas um aplicativo e suas dependências de tempo de execução sem um Gerenciador de pacotes, Shell ou sistema operacional.|
|Funções e permissões necessárias:|**Leitor de segurança** e [função de leitor do registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-roles)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) National/soberanas (US Gov, China gov, outros gov)|
|||




## <a name="when-are-images-scanned"></a>Quando as imagens são digitalizadas?

Sempre que uma imagem é enviada para o registro, a central de segurança verifica a imagem automaticamente. Para disparar a verificação de uma imagem, envie-a por push para o repositório.

Quando a verificação for concluída (normalmente após aproximadamente 2 minutos, mas pode ter até 15 minutos), as descobertas estarão disponíveis como recomendações da central de segurança como esta:

[![Exemplo de recomendação da central de segurança do Azure sobre vulnerabilidades descobertas em uma imagem hospedada do registro de contêiner do Azure (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Vantagens da integração

A central de segurança identifica Azure Resource Manager de registros ACR baseados em sua assinatura e fornece diretamente:

* **Azure-verificação de vulnerabilidades nativas** para todas as imagens do Linux enviadas por push. A central de segurança examina a imagem usando um scanner do fornecedor de verificação de vulnerabilidade líder do setor, Qualys. Essa solução nativa é totalmente integrada por padrão.

* **Recomendações de segurança** para imagens do Linux com vulnerabilidades conhecidas. A central de segurança fornece detalhes de cada vulnerabilidade relatada e uma classificação de gravidade. Além disso, ele fornece orientação sobre como corrigir as vulnerabilidades específicas encontradas em cada imagem enviada por push para o registro.

![Visão geral de alto nível da central de segurança do Azure e do ACR (registro de contêiner do Azure)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Perguntas frequentes sobre a verificação de imagem do registro de contêiner do Azure

### <a name="how-does-security-center-scan-an-image"></a>Como a central de segurança examina uma imagem?
A imagem é extraída do registro. Em seguida, ele é executado em uma área restrita isolada com o scanner Qualys, que extrai uma lista de vulnerabilidades conhecidas.

A central de segurança filtra e classifica as descobertas do verificador. Quando uma imagem está íntegra, a central de segurança a marca como tal. A central de segurança gera recomendações de segurança apenas para imagens que têm problemas a serem resolvidos. Notificando apenas quando há problemas, a central de segurança reduz o potencial para alertas informativos indesejados.

### <a name="how-often-does-security-center-scan-my-images"></a>Com que frequência a central de segurança verifica minhas imagens?
As verificações de imagem são disparadas em cada Push.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Posso obter os resultados da verificação por meio da API REST?
Sim. Os resultados estão sob a [API REST de subavaliações](/rest/api/securitycenter/subassessments/list/). Além disso, você pode usar o grafo de recursos do Azure (ARG), a API do tipo Kusto para todos os seus recursos: uma consulta pode buscar uma verificação específica.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Quais tipos de registro são verificados? Quais tipos são cobrados?
A [seção disponibilidade](#availability) lista os tipos de registros de contêiner com suporte no pacote de registros de contêiner. 

Se os registros que não têm suporte estiverem conectados à sua assinatura do Azure, eles não serão verificados e você não será cobrado por eles.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de segurança do contêiner da central de segurança, confira:

* [Central de segurança do Azure e segurança do contêiner](container-security.md)

* [Integração com o Serviço de Kubernetes do Azure](azure-kubernetes-service-integration.md)

* [Proteção da máquina virtual](security-center-virtual-machine-protection.md) – descreve as recomendações da central de segurança
