---
title: Perfil do usuário e ID para uso com Azure Notebooks versão prévia
description: Como criar e gerenciar seu perfil de usuário e a ID de usuário com Azure Notebooks, que se torna parte da URL de blocos de anotações compartilhados.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 9a1ff7f92faec21f537f068f0a33473700ddfed8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831345"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Seu perfil e ID de usuário para Azure Notebooks versão prévia

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

No espaço de colaboração avançado do Azure Notebooks, o perfil do usuário apresenta a imagem pública para outras pessoas:

[![Uma página de perfil de Azure Notebooks](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

A ID de usuário faz parte das URLs que você usa para compartilhar projetos e notebooks. A lista a seguir descreve os diferentes padrões de URL:

- `https://notebooks.azure.com/<user_id>`: Sua página de perfil.
- `https://notebooks.azure.com/<user_id>/projects`: Seus projetos. Você vê todos os projetos, mas os outros usuários veem apenas seus projetos públicos.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Arquivos de projeto.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Clones de projetos específicos.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: A visualização em HTML de um arquivo ou bloco de anotações específico.

## <a name="your-user-id"></a>Sua ID de usuário

Ao entrar no Azure Notebooks pela primeira vez, sua conta receberá automaticamente uma ID de usuário temporária como "anon-idr3ca". Enquanto você tiver uma ID de usuário que comece com "anon-", sempre que você entrar no Azure Notebooks a alteração da senha será solicitada:

![Solicitar a criação de uma ID de usuário ao fazer logon no Azure Notebooks](media/accounts/create-user-id.png)

Um comando **Configurar ID de usuário** também aparece ao lado do nome de usuário temporário:

![Comando Configurar ID de Usuário que é exibido quando você está usando uma ID temporária](media/accounts/configure-user-id-command.png)

Você também pode alterar a ID de usuário a qualquer momento na sua página de perfil.

Uma ID de usuário deve ser composta entre quatro e dezesseis letras, números e hifens. Nenhum outro caractere é permitido, e a ID de usuário não poderá começar ou terminar com um hífen ou usar vários hífens em uma linha. Como as IDs de usuário são exclusivas em todas as contas de Azure Notebooks, você pode ver a mensagem "a ID de usuário já está em uso". (A mensagem também será exibida se você tentar usar uma marca registrada da Microsoft como uma ID de usuário.) Nesses casos, escolha uma ID de usuário diferente.

> [!Important]
> Alterar sua ID invalidará todas as URLs que você possa ter compartilhado usando a ID anterior. É possível alterar sua ID de volta para a ID anterior para revalidar os links. No entanto, é possível que outro usuário reivindique a ID não utilizada nesse meio tempo.

## <a name="your-profile"></a>Seu perfil

Seu perfil é composto de informações visíveis publicamente na URL, `https://notebooks.azure.com/<user_id>`. Sua página de perfil também mostra seus projetos usados recentemente e quaisquer projetos estrelados.

Para editar seu perfil, use o comando **Editar Informações de Perfil** na sua página de perfil. As seções do seu perfil são as seguintes:

| Seção | Sumário |
| --- | --- |
| Foto de perfil | Uma imagem que é mostrada na sua página de perfil. |
| Informações da conta | Seu nome de exibição, ID de usuário e conta de email pública. Aqui, a conta de email fornece aos outros usuários uma maneira de como contatá-lo e essa conta pode ser diferente da [conta](azure-notebooks-user-account.md) que você usa para entrar no Azure Notebooks. |
| Informações do perfil | Sua localização, empresa, cargo, site e uma breve descrição sobre você. |
| Perfis sociais | As IDs do Facebook, Twitter e GItHub, se você quiser compartilhá-las. |
| Configurações de Privacidade | Fornece dois comandos:<ul><li>**Exportar Meu Perfil**: cria e baixa um arquivo *.zip* contendo todas as informações que o Azure Notebooks salva no seu perfil, incluindo fotos, informações de perfil e logs de segurança.</li><li>**Excluir minha conta**: exclui permanentemente todas as suas informações pessoais armazenadas no Azure notebooks.</li></ul> |
| Ativar Recursos do Site | Permite controlar aspectos do comportamento do Azure Notebooks:<ul><li>**Front-end unificado para notebooks**: permite inicialização mais rápida do notebook e melhor persistência.</li><li>**Executar no JupyterLab por padrão**: por padrão, o Azure notebooks fornece uma interface de usuário simples que é adequada para a maioria dos usuários. O JupyterLab fornece uma interface mais avançada, porém mais complexa para usuários experientes.</li><li>**Site VNext**: permite o layout da Web modernizado mostrado nesta documentação.</li></ul> |

## <a name="next-steps"></a>Próximas etapas  

> [!div class="nextstepaction"]
> [Tutorial: criar e executar um bloco de anotações Jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
