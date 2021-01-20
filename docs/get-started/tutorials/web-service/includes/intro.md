---
ms.openlocfilehash: e46c86def8a22450cf8087cf36a6bb05dd24ef70
ms.sourcegitcommit: 4d260b655cb52b990dda79c239a9721f2e964625
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570859"
---
Antes de tentar realizar este tutorial, você deve ter concluído com sucesso:

- Início rápido [Compilar seu primeiro aplicativo Xamarin.Forms](~/get-started/first-app/index.md).
- Tutorial de [StackLayout](~/get-started/tutorials/stacklayout/index.yml).
- Tutorial de [Rótulo](~/get-started/tutorials/label/index.yml).
- Tutorial de [Botão](~/get-started/tutorials/button/index.yml).
- Tutorial de [CollectionView](~/get-started/tutorials/collectionview/index.yml).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Use o Gerenciador de Pacotes do NuGet para adicionar o Newtonsoft.Json para um projeto Xamarin.Forms.
> - Crie classes de serviço Web.
> - Consuma as classes de serviço Web.

Você usará o Visual Studio 2019, ou o Visual Studio para Mac, para criar um aplicativo simples que demonstra como recuperar os dados do repositório .NET da API Web do GitHub. Os dados recuperados serão exibidos em um [`CollectionView`](xref:Xamarin.Forms.CollectionView). Estas capturas de tela mostram o aplicativo final:

[![Captura de tela de repositórios .NET do GitHub, no iOS e no Android](../images/consume-web-service.png)](../images/consume-web-service-large.png#lightbox)
