---
title: Guia de Publicação da Oferta de Contêineres para o Azure Marketplace
description: Este artigo descreve os requisitos para publicar Contêineres no Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 80bcf0d5d37a44cee2aab38161abac619542204a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684319"
---
# <a name="containers-offer-publishing-guide"></a>Guia de Publicação da Oferta de Contêineres

Ofertas de contêiner ajudam você a publicar sua imagem de contêiner no Azure Marketplace. Use este guia para compreender os requisitos dessa oferta. 

Essas são ofertas de transação que são implantadas e cobradas pelo Marketplace. A chamada à ação que um usuário vê é "Obtenha agora".

Use o tipo de oferta de Contêiner quando a solução é uma imagem de contêiner do Docker provisionada como um serviço de contêiner do Azure baseado em Kubernetes.

>[!NOTE]
>Por exemplo, um serviço de contêiner do Azure baseado em Kubernetes, como o Serviço de Kubernetes do Azure ou as Instâncias de Contêiner do Azure, a escolha de clientes do Azure para um runtime de contêiner baseado em Kubernetes.  

A Microsoft atualmente dá suporte a modelos de licenciamento BYOL (traga sua própria licença) e gratuito.

## <a name="containers-offer"></a>Oferta de Contêineres

| Requisito | Detalhes |  
|:--- |:--- |  
| Cobrança e medição | Suporte a qualquer modelo de cobrança gratuito ou BYOL. |  
| Imagem criada a partir do Dockerfile | As imagens de contêiner devem ser baseadas na especificação da imagem do Docker e devem ser criadas a partir de um Dockerfile.<ul> <li>Para saber mais sobre como criar imagens do docker, visite a seção Uso localizada em [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hospedagem no ACR | As imagens de contêiner devem ser hospedadas em um repositório ACR (Registro de Contêiner do Azure).<ul> <li>Para saber mais sobre como trabalhar com ACR, visite o Início Rápido: criar um registro de contêiner usando a página do portal do Azure localizada em [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Marcação de imagem | As imagens de contêiner devem conter pelo menos 1 marcação (máximo de marcações: 16).<ul> <li>Para saber mais sobre como marcar uma imagem, visite a página de marca do docker localizada em [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Próximas etapas

Caso ainda não tenha feito isso, 

- [Conheça](https://azuremarketplace.microsoft.com/sell) o mercado.

Para se registrar no Partner Center, comece a criar uma nova oferta ou trabalhar em uma já existente:

- [Faça login no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou concluir sua oferta.
- Consulte [criar uma oferta de contêiner Azure](./partner-center-portal/create-azure-container-offer.md) para obter mais informações.
