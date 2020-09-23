---
title: Azure defender para registros de contêiner-os benefícios e recursos
description: Saiba mais sobre os benefícios e recursos do Azure defender para registros de contêiner.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d312e3bd2933d1dce2cdaebf858fedf47105e45d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933785"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Introdução ao Azure defender para registros de contêiner

O ACR (registro de contêiner do Azure) é um serviço de registro gerenciado e privado do Docker que armazena e gerencia suas imagens de contêiner para implantações do Azure em um registro central. Ele é baseado no registro do Docker 2,0 de código aberto.

Para proteger todos os registros baseados em Azure Resource Manager em sua assinatura, habilite o **Azure defender para registros de contêiner** no nível da assinatura. A central de segurança examinará as imagens enviadas por push para o registro, importadas no registro ou quaisquer imagens extraídas nos últimos 30 dias. Esse recurso é cobrado por imagem.

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Quais são os benefícios do Azure defender para registros de contêiner?

A central de segurança identifica os registros ACR baseados em Azure Resource Manager em sua assinatura e fornece diretamente a avaliação e o gerenciamento de vulnerabilidades do Azure-nativo para as imagens do registro.

O **Azure defender para registros de contêiner** inclui um verificador de vulnerabilidade para verificar as imagens em seus registros de registro de contêiner do Azure baseados em Azure Resource Manager e fornecer uma visibilidade mais profunda das vulnerabilidades de suas imagens. O scanner integrado é fornecido pela Qualys, o fornecedor líder do setor de verificação de vulnerabilidades.

Quando forem encontrados problemas – por Qualys ou pela central de segurança – você será notificado no painel da central de segurança. Para cada vulnerabilidade, a central de segurança fornece recomendações acionáveis, juntamente com uma classificação de gravidade, e orientações sobre como corrigir o problema. Para obter detalhes das recomendações da central de segurança para contêineres, consulte a [lista de recomendações de referência](recommendations-reference.md#recs-containers).

A central de segurança filtra e classifica as descobertas do verificador. Quando uma imagem está íntegra, a central de segurança a marca como tal. A central de segurança gera recomendações de segurança apenas para imagens que têm problemas a serem resolvidos. A central de segurança fornece detalhes de cada vulnerabilidade relatada e uma classificação de gravidade. Além disso, ele fornece orientação sobre como corrigir as vulnerabilidades específicas encontradas em cada imagem.

Notificando apenas quando há problemas, a central de segurança reduz o potencial para alertas informativos indesejados.


## <a name="when-are-images-scanned"></a>Quando as imagens são digitalizadas?

Há três gatilhos para uma verificação de imagem:

- **Por push** – sempre que uma imagem for enviada por push ao registro, a central de segurança verificará automaticamente essa imagem. Para disparar a verificação de uma imagem, envie-a por push para o repositório.

- **Recentemente recebidos** -como novas vulnerabilidades são descobertas todos os dias, o **Azure defender para registros de contêiner** também verifica qualquer imagem que tenha sido puxada nos últimos 30 dias. Não há nenhum encargo adicional para um novo exame; conforme mencionado acima, você será cobrado uma vez por imagem.

- **Em importar** – o registro de contêiner do Azure tem ferramentas de importação para trazer imagens para o registro do Hub do Docker, do registro de contêiner da Microsoft ou de outro registro de contêiner do Azure. O **Azure defender para registros de contêiner** verifica as imagens com suporte que você importar. Saiba mais em [importar imagens de contêiner para um registro de contêiner](../container-registry/container-registry-import-images.md).
 
A verificação é concluída normalmente em 2 minutos, mas pode levar até 15 minutos. As descobertas são disponibilizadas como recomendações da central de segurança, como esta:

[![Exemplo de recomendação da central de segurança do Azure sobre vulnerabilidades descobertas em uma imagem hospedada do registro de contêiner do Azure (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Como a central de segurança funciona com o registro de contêiner do Azure

Abaixo está um diagrama de alto nível dos componentes e dos benefícios de proteger seus registros com a central de segurança.

![Visão geral de alto nível da central de segurança do Azure e do ACR (registro de contêiner do Azure)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Perguntas frequentes sobre a verificação de imagem do registro de contêiner do Azure

### <a name="how-does-security-center-scan-an-image"></a>Como a central de segurança examina uma imagem?
A imagem é extraída do registro. Em seguida, ele é executado em uma área restrita isolada com o scanner Qualys, que extrai uma lista de vulnerabilidades conhecidas.

A central de segurança filtra e classifica as descobertas do verificador. Quando uma imagem está íntegra, a central de segurança a marca como tal. A central de segurança gera recomendações de segurança apenas para imagens que têm problemas a serem resolvidos. Notificando apenas quando há problemas, a central de segurança reduz o potencial para alertas informativos indesejados.

### <a name="how-often-does-security-center-scan-my-images"></a>Com que frequência a central de segurança verifica minhas imagens?
As verificações de imagem são disparadas em cada Push ou importação e, se a imagem tiver sido retirada nos últimos 30 dias. 

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Posso obter os resultados da verificação por meio da API REST?
Sim. Os resultados estão sob a [API REST de subavaliações](/rest/api/securitycenter/subassessments/list/). Além disso, você pode usar o grafo de recursos do Azure (ARG), a API do tipo Kusto para todos os seus recursos: uma consulta pode buscar uma verificação específica.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Quais tipos de registro são verificados? Quais tipos são cobrados?
A seção disponibilidade lista os tipos de registros de contêiner com suporte pelo Azure defender para registros de contêiner. 

Se você conectar registros sem suporte à sua assinatura do Azure, eles não serão verificados e você não será cobrado por eles.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de segurança do contêiner da central de segurança, confira:

- [Central de segurança do Azure e segurança do contêiner](container-security.md)

- [Introdução ao Azure defender para kubernetes](defender-for-kubernetes-introduction.md)


