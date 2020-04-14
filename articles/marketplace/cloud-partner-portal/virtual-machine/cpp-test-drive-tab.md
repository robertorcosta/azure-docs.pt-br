---
title: Guia de test drive de máquina virtual no Portal de Parceiros na Nuvem para o Mercado Azure
description: Descreve a guia Test drive usada na criação de uma oferta de VM do Marketplace do Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 22538047adc17a40438359e11bff7fd20e43bcc6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273079"
---
# <a name="virtual-machine-test-drive-tab"></a>Guia de Test Drive da máquina virtual

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover o gerenciamento das ofertas da Sua Máquina Virtual Do Azure para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [Criar uma oferta de Máquina Virtual do Azure](https://aka.ms/CreateAzureVMoffer) para gerenciar suas ofertas migradas.

A guia **Test Drive** da página **Nova oferta** permite que você forneça a seus clientes potenciais com uma demonstração prática e autogerenciada dos seus recursos principais do produto e benefícios, demonstrados de um estado padronizado.  O Test Drive é um recurso opcional para os tipos de oferta que dão suporte ao Test Drive.  Test Drive requer ativos de suporte a serem implementados corretamente.  Para obter mais informações, consulte o artigo [
Test Drive do Microsoft Azure Marketplace](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Para habilitar esse recurso, a guia **Test Drive**, clique na opção **Sim** em **Habilitar um Test Drive**.  A guia **Test Drive** exibe os campos disponíveis para edição.  Um asterisco anexado (*) no nome do campo indica que isso é necessário.

![Guia Test Drive no formulário Nova Oferta para máquinas virtuais](./media/publishvm_007.png)


## <a name="field-values"></a>Valores de campo

A tabela a seguir descreve o objetivo e o conteúdo desses campos.  Os campos obrigatórios são indicados por um asterisco (*).


|    Campo                  |       Descrição                                                            |
|  ---------                |     ---------------                                                          |
|  *Detalhes*   |  |
| **Descrição\***           | Forneça uma visão geral do seu cenário Test Drive. Esse texto será mostrado ao usuário enquanto o test drive está sendo provisionado. Esse campo dá suporte a HTML básico, caso você queira fornecer conteúdo formatado.  |
| **Manual do Usuário\***           | Faça upload de um manual do usuário detalhado (.pdf) que ajuda os usuários do test drive a entender como usar sua solução.  |
| **Vídeo de demonstração do Test Drive** | Carrega um vídeo que demonstra sua solução.  Se você escolher essa opção, forneça um nome, a URL para o vídeo (hospedado no YouTube ou Vimeo) e uma miniatura (533, 324 pixel) para o vídeo. |
| *Configurações Técnicas* |  |
| **Instâncias\***             | Especificar a disponibilidade de região e relativamente a disponibilidade da instância da vm (clique no ícone de informações para obter mais detalhes).  <br/>As sessões de Test Drive simultâneas potencias não devem exceder o limite de cota para a sua assinatura.  A primeira opção é calculada como: [número de regiões selecionados] x [Instâncias Hot] + [Número de regiões selecionados] x [Instâncias] + [Número de regiões selecionadas] x [Instâncias cold] |
| **Duração da unidade de teste\***   | Duração da ativação máxima em horas. O Test Drive é encerrado automaticamente após o término desse período de tempo.  |
|**Modelo do braço do drive de teste\***| Carregue o modelo do Azure Resource Manager associado a essa unidade de teste. Para obter mais informações, consulte [Transformando o modelo de implantação de máquina Virtual para Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Informações de acesso\***    | Acesso do Azure Resource Manager e informações de logon de avaliação, gravado como texto sem formatação ou HTML simples. |
| *Detalhes da assinatura para implantação do Test Drive* |  |
| **Id de assinatura do Azure\*** | Pode ser obtido entrando na [portal do Microsoft Azure](https://ms.portal.azure.com) e clicando em **Assinaturas** na barra de menus à esquerda. (Exemplo: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Este identificador deve ser um `a83645ac-1234-5ab6-6789-1h234g764ghty`GUID do formulário .|
| **Id do inquilino azure AD\***    | ID do locatário do Microsoft Azure Active Directory.  Pode ser obtido entrando oa [portal do Microsoft Azure](https://ms.portal.azure.com) e clicando em **Azure Active Directory** na barra de menus à esquerda, em seguida, clicando em **Propriedades** na barra de menus intermediária, em seguida, copiando a **ID do Diretório** do formulário.  Esse identificador também deve ser um GUID.  Se deixado em branco, você deve criar uma ID de locatário para sua organização. |
| **ID do aplicativo Azure AD\***       | Identificador para sua solução de VM do Azure registrada  |
| **Chave do aplicativo Azure AD\***      | Chave de autenticação para sua solução registrada |
|   |   |


## <a name="next-steps"></a>Próximas etapas

Na próxima guia [Marketplace](./cpp-marketplace-tab.md), você irá fornecer o marketing e informações legais sobre sua solução.
