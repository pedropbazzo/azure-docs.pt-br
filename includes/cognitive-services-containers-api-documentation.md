---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 8b3d856d255968b4a6736db908ce3999cbd56193
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108882"
---
## <a name="validate-that-a-container-is-running"></a>Validar se um contêiner está em execução 

Há várias maneiras de validar se um contêiner está em execução. Localize o endereço *IP externo* e a porta exposta do contêiner em questão e abra seu navegador da Web favorito. Use as várias URLs de solicitação abaixo para validar se o contêiner está em execução. As URLs de solicitação de exemplo listadas abaixo são `http://localhost:5000` , mas o seu contêiner específico pode variar. Tenha em mente que você depende do endereço *IP externo* do seu contêiner e da porta exposta.

| URL de Solicitação | Finalidade |
|--|--|
| `http://localhost:5000/` | O contêiner fornece uma home page. |
| `http://localhost:5000/ready` | Solicitado com GET, isso fornece uma verificação de que o contêiner está pronto para aceitar uma consulta no modelo.  Essa solicitação pode ser usada para [testes de preparação e de execução](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) do Kubernetes. |
| `http://localhost:5000/status` | Também solicitado com GET, isso verifica se a chave de API usada para iniciar o contêiner é válida sem causar uma consulta de ponto de extremidade. Essa solicitação pode ser usada para [testes de preparação e de execução](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) do Kubernetes. |
| `http://localhost:5000/swagger` | O contêiner fornece um conjunto completo de documentação para os pontos de extremidade e um recurso **Experimentar**. Com esse recurso, é possível inserir suas configurações em um formulário HTML baseado na Web e realizar a consulta sem precisar escrever nenhum código. Após a consulta ser retornada, um exemplo de comando CURL será fornecido para demonstrar o formato do corpo e dos cabeçalhos HTTP exigidos. |

![Home page do contêiner](./media/cognitive-services-containers-api-documentation/container-webpage.png)
