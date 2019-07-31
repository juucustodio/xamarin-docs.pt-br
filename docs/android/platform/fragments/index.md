---
title: Fragmentos
description: O Android 3,0 introduziu fragmentos, mostrando como dar suporte a designs mais flexíveis para vários tamanhos de tela diferentes encontrados em telefones e tablets. Este artigo abordará como usar fragmentos para desenvolver aplicativos Xamarin. Android e também como dar suporte a fragmentos em dispositivos anteriores ao Android 3,0 (nível de API 11).
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: cfc4420d95778188368f3e7d3595713d83371d33
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643598"
---
# <a name="fragments"></a>Fragmentos

_O Android 3,0 introduziu fragmentos, mostrando como dar suporte a designs mais flexíveis para vários tamanhos de tela diferentes encontrados em telefones e tablets. Este artigo abordará como usar fragmentos para desenvolver aplicativos Xamarin. Android e também como dar suporte a fragmentos em dispositivos anteriores ao Android 3,0 (nível de API 11)._

## <a name="fragments-overview"></a>Visão geral de fragmentos

Os tamanhos de tela maiores encontrados na maioria dos tablets adicionaram uma camada extra de complexidade ao desenvolvimento do Android – um layout projetado para a tela pequena não necessariamente funciona bem para telas maiores e vice-versa. Para reduzir o número de complicações que isso introduziu, o Android 3,0 adicionou dois novos recursos, *fragmentos* e *pacotes de suporte*.

Os fragmentos podem ser considerados como módulos de interface do usuário. Eles permitem que o desenvolvedor divida a interface do usuário em partes isoladas e reutilizáveis que podem ser executadas em atividades separadas. Em tempo de execução, as próprias atividades decidirão quais fragmentos usar.

Os pacotes de suporte eram originalmente chamados de *bibliotecas de compatibilidade* e fragmentos permitidos a serem usados em dispositivos que executam versões do Android antes do Android 3,0 (API nível 11).

Por exemplo, a imagem abaixo ilustra como um único aplicativo usa fragmentos entre diferentes fatores forma de dispositivo.

[![Diagrama de como os fragmentos são usados em tablets e aparelhos](images/00.png)](images/00.png#lightbox)

*O fragmento a* contém uma lista, enquanto o *fragmento B* contém detalhes de um item selecionado nessa lista. Quando o aplicativo é executado em um Tablet, ele pode exibir ambos os fragmentos na mesma atividade. Quando o mesmo aplicativo é executado em um monofone (com seu tamanho de tela menor), os fragmentos são hospedados em duas atividades separadas. O fragmento A e o fragmento B são os mesmos em ambos os fatores forma, mas as atividades que os hospedam são diferentes.

Para ajudar uma atividade a coordenar e gerenciar todos esses fragmentos, o Android introduziu umanova classe chamada fragmentmanager. Cada atividade tem sua própria instância de a `FragmentManager` para adicionar, excluir e localizar fragmentos hospedados. O diagrama a seguir ilustra a relação entre os fragmentos e as atividades:

[![Diagrama que ilustra as relações entre a atividade, o Gerenciador de fragmentos e os fragmentos](images/01.png)](images/01.png#lightbox)

Em alguns aspectos, os fragmentos podem ser considerados como controles compostos ou como uma Minibarra de atividades. Eles embalam partes da interface do usuário em módulos reutilizáveis que podem ser usados independentemente pelos desenvolvedores em atividades. Um fragmento tem uma hierarquia de exibição — assim como uma atividade — mas, ao contrário de uma atividade, ele pode ser compartilhado entre telas. As exibições diferem dos fragmentos nos fragmentos que têm seu próprio ciclo de vida; os modos de exibição não.

Embora a atividade seja um host para um ou mais fragmentos, ela não reconhece diretamente os fragmentos propriamente ditos. Da mesma forma, os fragmentos não estão cientes diretamente de outros fragmentos na atividade de hospedagem. No entanto, os `FragmentManager` fragmentos e as atividades estão cientes do em sua atividade. Usando o `FragmentManager`, é possível que uma atividade ou um fragmento obtenha uma referência a uma instância específica de um fragmento e, em seguida, chame métodos nessa instância. Dessa forma, a atividade ou os fragmentos podem se comunicar e interagir com outros fragmentos.

Este guia contém uma cobertura abrangente sobre como usar fragmentos, incluindo:

-   **Criando fragmentos** – como criar um fragmento básico e os principais métodos que devem ser implementados.
-   **Gerenciamento e transações de fragmento** – como manipular fragmentos em tempo de execução.
-   **Pacote de suporte do Android** – como usar as bibliotecas que permitem que os fragmentos sejam usados em versões mais antigas do Android.


## <a name="requirements"></a>Requisitos

Os fragmentos estão disponíveis na SDK do Android começando com o nível de API 11 (Android 3,0), conforme mostrado na seguinte captura de tela:

[![Selecionando o nível de API no Gerenciador de SDK do Android](images/02.png)](images/02.png#lightbox)

Os fragmentos estão disponíveis no Xamarin. Android 4,0 e superior. Um aplicativo Xamarin. Android deve ter como destino o nível mínimo de API 11 (Android 3,0) ou superior para usar fragmentos. A estrutura de destino pode ser definida nas propriedades do projeto, conforme mostrado abaixo:

[![Definindo o nível da API do Framework de destino nas opções do projeto](images/03-sml.png)](images/03.png#lightbox)

É possível usar fragmentos em versões mais antigas do Android usando o pacote de suporte do Android e o Xamarin. Android 4,2 ou superior. Como fazer isso é abordado com mais detalhes nos documentos desta seção.


## <a name="related-links"></a>Links relacionados

- [Galeria do Honeycomb (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/honeycombgallery)
- [Fragmentos](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacote de suporte](https://developer.android.com/sdk/compatibility-library.html)
- [Webinar MOTODEV: Apresentando fragmentos](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
