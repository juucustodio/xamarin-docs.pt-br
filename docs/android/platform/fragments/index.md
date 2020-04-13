---
title: Fragmentos
description: O Android 3.0 introduziu fragmentos, mostrando como suportar designs mais flexíveis para os diversos tamanhos de tela encontrados em telefones e tablets. Este artigo abordará como usar fragmentos para desenvolver aplicativos Xamarin.Android, e também como suportar Fragmentos em dispositivos pré-Android 3.0 (API Nível 11).
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 5d243429fe4f61768568a634b205055c1ad94297
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020257"
---
# <a name="fragments"></a>Fragmentos

_O Android 3.0 introduziu fragmentos, mostrando como suportar designs mais flexíveis para os diversos tamanhos de tela encontrados em telefones e tablets. Este artigo abordará como usar fragmentos para desenvolver aplicativos Xamarin.Android, e também como suportar Fragmentos em dispositivos pré-Android 3.0 (API Nível 11)._

## <a name="fragments-overview"></a>Visão geral dos fragmentos

Os tamanhos maiores de tela encontrados na maioria dos tablets adicionaram uma camada extra de complexidade ao desenvolvimento do Android — um layout projetado para a tela pequena não necessariamente funciona tão bem para telas maiores, e vice-versa. Para reduzir o número de complicações que isso introduziu, o Android 3.0 adicionou dois novos recursos, *Fragmentos* e *Pacotes de Suporte.*

Fragmentos podem ser pensados como módulos de interface do usuário. Eles permitem que o desenvolvedor divida a interface do usuário em partes isoladas e reutilizáveis que podem ser executadas em atividades separadas. No tempo de execução, as próprias Atividades decidirão quais Fragmentos usar.

Os pacotes de suporte eram originalmente chamados *bibliotecas de compatibilidade* e permitiam que fragmentos fossem usados em dispositivos que executam versões do Android antes do Android 3.0 (API Nível 11).

Por exemplo, a imagem abaixo ilustra como um único aplicativo usa Fragmentos em diferentes fatores de forma de dispositivo.

[![Diagrama de como os fragmentos são usados em Tablets e Aparelhos](images/00.png)](images/00.png#lightbox)

*O fragmento A* contém uma lista, enquanto *o Fragmento B* contém detalhes para um item selecionado nessa lista. Quando o aplicativo é executado em um tablet, ele pode exibir ambos os Fragmentos na mesma Atividade. Quando o mesmo aplicativo é executado em um aparelho (com seu tamanho de tela menor), os Fragmentos são hospedados em duas atividades separadas. Fragmento A e Fragmento B são os mesmos em ambos os fatores de forma, mas as atividades que os hospedam são diferentes.

Para ajudar uma atividade a coordenar e gerenciar todos esses fragmentos, o Android introduziu uma nova classe chamada *FragmentManager*. Cada atividade tem sua `FragmentManager` própria instância de a para adicionar, excluir e encontrar Fragmentos hospedados. O diagrama a seguir ilustra a relação entre Fragmentos e Atividades:

[![Diagrama ilustrando relações entre Atividade, Gerenciador de Fragmentos e Fragmentos](images/01.png)](images/01.png#lightbox)

Em alguns aspectos, fragmentos podem ser considerados como controles compostos ou como mini-Atividades. Eles empacotam peças de IU em módulos reutilizáveis que podem ser usados independentemente pelos desenvolvedores em Atividades. Um Fragmento tem uma hierarquia de exibição — assim como uma atividade — mas, ao contrário de uma atividade, ele pode ser compartilhado entre as telas. As visualizações diferem dos Fragmentos em que os fragmentos têm seu próprio ciclo de vida; pontos de vista não.

Embora a Atividade seja um hospedeiro de um ou mais Fragmentos, ela não está diretamente ciente dos fragmentos em si. Da mesma forma, os fragmentos não estão diretamente cientes de outros fragmentos na atividade de hospedagem. No entanto, fragmentos e `FragmentManager` atividades estão cientes do em suas Atividades. Usando o `FragmentManager`, é possível para uma Atividade ou um Fragmento obter uma referência a uma instância específica de um Fragmento e, em seguida, chamar métodos nessa instância. Dessa forma, a Atividade ou Fragmentos podem se comunicar e interagir com outros Fragmentos.

Este guia contém uma cobertura abrangente sobre como usar fragmentos, incluindo:

- **Criação de Fragmentos** – Como criar um Fragmento básico e métodos-chave que devem ser implementados.
- **Gerenciamento de Fragmentos e Transações** – Como manipular fragmentos em tempo de execução.
- **Pacote de suporte** para Android – Como usar as bibliotecas que permitem que fragmentos sejam usados em versões mais antigas do Android.

## <a name="requirements"></a>Requisitos

Os fragmentos estão disponíveis no Android SDK a partir do Nível 11 da API (Android 3.0), como mostrado na captura de tela a seguir:

[![Selecionando o nível de API no Android SDK Manager](images/02.png)](images/02.png#lightbox)

Fragmentos estão disponíveis em Xamarin.Android 4.0 ou superior. Um aplicativo Xamarin.Android deve ter como alvo pelo menos a API nível 11 (Android 3.0) ou superior para usar Fragmentos. O Framework de destino pode ser definido nas propriedades do projeto, conforme mostrado abaixo:

[![Definindo o nível de API do quadro de destino nas opções de projeto](images/03-sml.png)](images/03.png#lightbox)

É possível usar Fragmentos em versões mais antigas do Android usando o Pacote de Suporte para Android e Xamarin.Android 4.2 ou superior. Como fazer isso é abordado com mais detalhes nos documentos desta seção.

## <a name="related-links"></a>Links relacionados

- [Galeria honeycomb (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/honeycombgallery)
- [Fragmentos](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacote de suporte](https://developer.android.com/sdk/compatibility-library.html)
