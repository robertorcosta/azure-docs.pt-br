---
title: Recomendações de segurança para as imagens do Azure Marketplace | Microsoft Docs
description: Este artigo fornece recomendações para as imagens incluídas no Marketplace
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 3925e39824d1702ff43a6b981ac997ddab658b96
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548665"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Recomendações de segurança para as imagens do Azure Marketplace

Sua imagem deve atender a essas recomendações de configuração de segurança. Isso ajuda a manter um alto nível de segurança para as imagens de solução de parceiro no Azure Marketplace.

Execute sempre uma detecção de vulnerabilidade de segurança em sua imagem antes de enviar. Se você detectar uma vulnerabilidade de segurança em sua própria imagem publicada, você deve informar seus clientes em tempo hábil tanto da vulnerabilidade quanto de como corrigi-la.

## <a name="open-source-based-images"></a>Abrir imagens com base na origem

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Categoria**                                                 | **Verificar**                                                                                                                                                                                                                                                                              |
| Segurança                                                     | Instale todos os patches de segurança mais recentes para a distribuição Linux.                                                                                                                                                                                                              |
| Segurança                                                     | Siga as diretrizes do setor para proteger a imagem vm para a distribuição Linux específica.                                                                                                                                                                                     |
| Segurança                                                     | Limite a superfície de ataque mantendo somente o volume mínimo necessário das funções, recursos, serviços e portas de rede do Windows Server.                                                                                                                                               |
| Segurança                                                     | Faça a verificação do código-fonte e da imagem da VM em relação à presença de malware.                                                                                                                                                                                                                                   |
| Segurança                                                     | A imagem VHD só inclui contas bloqueadas necessárias que não possuem senhas padrão que permitiriam login interativo; sem portas traseiras.                                                                                                                                           |
| Segurança                                                     | Desabilite as regras de firewall, a menos que o aplicativo dependa funcionalmente delas, como um aparelho de firewall.                                                                                                                                                                             |
| Segurança                                                     | Remova todas as informações confidenciais da imagem VHD, como testar chaves SSH, arquivo hosts conhecido, arquivos de log e certificados desnecessários.                                                                                                                                       |
| Segurança                                                     | Evite usar LVM.                                                                                                                                                                                                                                            |
| Segurança                                                     | Inclua as versões mais recentes das bibliotecas requeridas: </br> - OpenSSL v1.0 ou superior </br> - Python 2.5 ou superior (Python 2.6+ é altamente recomendável) </br> - Pacote pyasn1 do Python, se já não estiver instalado </br> - d.OpenSSL v 1.0 ou superior                                                                |
| Segurança                                                     | Entradas do histórico Clear Bash/Shell.                                                                                                                                                                                                                                             |
| Rede                                                   | Inclua o servidor SSH por padrão. Defina sSH mantenha-se vivo para sshd config com a seguinte opção: ClientAliveInterval 180.                                                                                                                                                        |
| Rede                                                   | Remova qualquer configuração de rede personalizada da imagem. Exclua o resolv.conf: `rm /etc/resolv.conf`.                                                                                                                                                                                |
| Implantação                                                   | Instale o mais recente Agente Azure Linux.</br> - Instale usando o pacote RPM ou Deb.  </br> - Você também pode usar o processo de instalação manual, mas os pacotes do instalador são recomendados e preferenciais. </br> - Se estiver instalando o agente manualmente por meio do repositório GitHub, primeiro copie o arquivo `waagent` para `/usr/sbin` e execute (como raiz): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>O arquivo de configuração do agente é colocado em `/etc/waagent.conf`. |
| Implantação                                                   | Certifique-se de que o Azure Support pode fornecer aos nossos parceiros a saída de console serial quando necessário e fornecer tempo suficiente para a montagem do disco do SISTEMA OPERACIONAL a partir do armazenamento em nuvem. Adicione os seguintes parâmetros à `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`linha de inicialização do kernel da imagem: . |
| Implantação                                                   | Não troque a partição no disco do sistema operacional. A troca pode ser solicitada para a criação no disco de recurso local pelo Agente do Linux.         |
| Implantação                                                   | Crie uma única partição raiz para o disco do SISTEMA OPERACIONAL.      |
| Implantação                                                   | Somente sistema operacional de 64 bits.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Imagens baseadas no Windows Server

|||
|-------------| -------------------------|
| **Categoria**                                                     | **Verificar**                                                                                                                                                                |
| Segurança                                                         | Use uma imagem de base segura do sistema operacional. O VHD usado para a origem de qualquer imagem baseada no Windows Server deve estar entre as imagens do sistema operacional do Windows Server fornecidas pelo Microsoft Azure. |
| Segurança                                                         | Instale todas as últimas atualizações de segurança.                                                                                                                                     |
| Segurança                                                         | Os aplicativos não devem depender de nomes de usuários restritos, como administrador, raiz ou administrador.                                                                |
| Segurança                                                         | Habilite a criptografia da unidade BitLocker para discos rígidos do Sistema Operacional e discos rígidos de dados.                                                             |
| Segurança                                                         | Limite a superfície de ataque mantendo somente o volume mínimo necessário das funções, recursos, serviços e portas de rede do Windows Server habilitado.                         |
| Segurança                                                         | Faça a verificação do código-fonte e da imagem da VM em relação à presença de malware.                                                                                                                     |
| Segurança                                                         | Configure a atualização de segurança de imagens do Windows Server para atualização automática.                                                                                                                |
| Segurança                                                         | A imagem VHD só inclui contas bloqueadas necessárias que não possuem senhas padrão que permitiriam login interativo; sem portas traseiras.                             |
| Segurança                                                         | Desabilite as regras de firewall, a menos que o aplicativo dependa funcionalmente delas, como um aparelho de firewall.                                                               |
| Segurança                                                         | Remova todas as informações confidenciais da imagem VHD, incluindo arquivos HOSTS, arquivos de log e certificados desnecessários.                                              |
| Implantação                                                       | Somente sistema operacional de 64 bits.                            |

Mesmo que sua organização não tenha imagens no mercado Azure, considere verificar suas configurações de imagem Windows e Linux contra essas recomendações.

## <a name="contacting-customers"></a>Contato com clientes

Para identificar clientes e seus e-mails de contato:

1.  No Portal de Parceiros na Nuvem, no trilho esquerdo, selecione **Insights**.
2.  Na guia **Pedidos e Uso,** use os campos **Data de início** e data de **término** para consultar o uso dentro do intervalo de datas necessário. Isso mostra quais assinaturas do Azure foram usadas para a oferta diariamente. Exporte esses dados. 
3.  Da mesma forma, na guia **Cliente,** consulta e exporta sua base de clientes.
4.  Combine o ID de assinatura da etapa 2 para o ID de assinatura da etapa 3 para encontrar as informações necessárias do cliente.
