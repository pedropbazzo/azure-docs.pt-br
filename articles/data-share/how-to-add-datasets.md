---
title: Adicionar conjuntos de dados a um compartilhamento existente do Azure data
description: Saiba como adicionar conjuntos de dados a um compartilhamento de dado existente no compartilhamento de dados do Azure e compartilhar com os mesmos destinatários.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 540cdc2948cbcd97bf7c8872f3aace2862280434
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87513482"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Como adicionar conjuntos de dados a um compartilhamento existente no Azure data share

Este artigo explica como adicionar conjuntos de dados a um compartilhamento de dado pré-existente usando o compartilhamento de dados do Azure. Isso permite que você compartilhe mais dados com os mesmos destinatários sem precisar criar um novo compartilhamento.

Para obter informações sobre como adicionar conjuntos de dados à medida que você cria um compartilhamento, consulte o tutorial [compartilhar data](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Navegar até um compartilhamento de dados enviado

No compartilhamento de dados do Azure, navegue até o compartilhamento enviado e selecione a guia **conjuntos** de dados. Clique no botão **+ Adicionar conjuntos** de itens para adicionar mais conjuntos de informações.

![Adicionar conjuntos de dados](./media/how-to/how-to-add-datasets/add-datasets.png)

No painel à direita, selecione o tipo de conjunto de texto que você deseja adicionar e clique em **Avançar**. Selecione a assinatura e o grupo de recursos dos dados que você deseja adicionar. Usando as setas suspensas, localize e marque a caixa ao lado dos dados a serem adicionados.

![Adicionar conjuntos de dados](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Depois de clicar em **Adicionar conjuntos**de os, os conjuntos de valores serão adicionados ao seu compartilhamento. Observação: um instantâneo deve ser disparado por seus consumidores para que eles vejam os novos conjuntos de clientes. Se houver configurações de instantâneo configuradas, os consumidores verão os novos conjuntos de clientes assim que o próximo instantâneo agendado for concluído. Sem configurações de instantâneo configuradas, o consumidor deve disparar manualmente uma cópia completa ou incremental dos dados para receber as atualizações. Para obter mais informações sobre instantâneos, consulte [instantâneos](terminology.md).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [Adicionar destinatários a um compartilhamento de dados existente](how-to-add-recipients.md).