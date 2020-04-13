---
title: 'Multitela do Hello, Android: aprofundamento'
description: Neste guia de duas partes, o aplicativo Phoneword básico (criado no guia Hello, Android) é expandido para processar uma segunda tela. Ao longo do caminho, são apresentados os Blocos de Construção de Aplicativo Android básico. Um aprofundamento na arquitetura Android está incluído para ajudá-lo a desenvolver uma melhor compreensão da estrutura de aplicativo e da funcionalidade do Android.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E4150036-7760-4023-BD33-B7BDE7B7AF5B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 4acbfe810abefd9a25721ddf59c9f4f197afdf28
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020972"
---
# <a name="hello-android-multiscreen-deep-dive"></a>Multitela do Hello, Android: aprofundamento

_Neste guia de duas partes, o aplicativo básico Phoneword (criado no guia Hello, Android) é expandido para lidar com uma segunda tela. Ao longo do caminho, os blocos básicos de construção de aplicativos android são introduzidos. Um mergulho mais profundo na arquitetura Android está incluído para ajudá-lo a desenvolver uma melhor compreensão da estrutura e funcionalidade do aplicativo Android._

No [Início rápido de multitela do Hello, Android](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-quickstart.md), você compilou e executou seu primeiro aplicativo Xamarin.Android multitela.

Neste guia, você explorará a arquitetura Android mais avançada. Navegação em Android com *Intenções* é explicada e as opções de navegação de hardware do Android são exploradas. Novas adições ao aplicativo Phoneword são examinadas ao desenvolver uma visão mais holística da relação do aplicativo com o sistema operacional e outros aplicativos.

## <a name="android-architecture-basics"></a>Noções básicas sobre a arquitetura do Android

Em [Aprofundamento no Hello, Android](~/android/get-started/hello-android/hello-android-deepdive.md), você aprendeu que aplicativos Android são programas exclusivos porque carecem de um ponto de entrada único. Em vez disso, o sistema operacional (ou outro aplicativo) inicia qualquer uma das Atividades registradas do aplicativo, que, por sua vez, inicia o processo para o aplicativo. Este aprofundamento na arquitetura do Android expande seu entendimento de como os aplicativos Android são construídos, apresentando os Blocos de Construção do Aplicativo Android e suas funções.

### <a name="android-application-building-blocks"></a>Aplicativo Android construindo blocos

Um aplicativo Android consiste em uma coleção de classes Android especiais chamadas de *Blocos de Aplicativo*, empacotadas com qualquer número de recursos de aplicativo – imagens, temas, classes auxiliares, etc. &ndash; que são coordenadas por um arquivo XML chamado *Manifesto do Android*.

Blocos de Aplicativos formam o backbone de aplicativos Android porque permitem executar ações que normalmente não seriam possíveis com uma classe regular. Os dois mais importantes são _Atividades_ e _Serviços_:

- **Atividade** &ndash; Uma atividade corresponde a uma tela com uma interface do usuário e é conceitualmente similar a uma página da Web em um aplicativo Web. Por exemplo, em um aplicativo de feed de notícias, a tela de logon seria a primeira atividade, a lista rolável de itens de notícias seria outra atividade e a página de detalhes para cada item seria uma terceira. Saiba mais sobre Atividades no guia [Ciclo de Vida da Atividade](~/android/app-fundamentals/activity-lifecycle/index.md).

- **Serviço** &ndash; Serviços do Android dão suporte a atividades assumindo tarefas demoradas e executando-as no segundo plano. Serviços não têm uma interface do usuário e são usados para lidar com tarefas que não estão vinculadas a telas &ndash; por exemplo, tocar uma música em segundo plano ou carregar fotos para um servidor. Para saber mais sobre Serviços, consulte os guias [Como criar serviços](~/android/app-fundamentals/services/index.md) e [Serviços do Android](~/android/app-fundamentals/services/index.md).

Um aplicativo Android pode não usar todos os tipos de Blocos e geralmente tem vários Blocos de um tipo. Por exemplo, o aplicativo Phoneword do [Início rápido do Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) era composto por apenas uma atividade (tela) e alguns arquivos de recurso. Um aplicativo de player de música simples pode ter várias Atividades e um Serviço para tocar músicas quando o aplicativo está em segundo plano.

### <a name="intents"></a>Intenções

Outro conceito fundamental em aplicativos Android é a *Intenção*.
O Android é desenvolvido em torno do *princípio do privilégio mínimo* &ndash; os aplicativos têm acesso somente aos Blocos de que precisam para funcionar e têm acesso limitado aos Blocos que compõem o sistema operacional ou outros aplicativos. Da mesma forma, os Blocos são *ligeiramente acoplados* &ndash; eles são desenvolvidos para ter pouco conhecimento e acesso limitado a outros Blocos (até mesmo aos blocos que fazem parte do mesmo aplicativo).

Para comunicação, os Blocos de Aplicativos enviam e recebem mensagens assíncronas chamadas *Intenções*. As Intenções contêm informações sobre o Bloco receptor e, às vezes, alguns dados. Uma Intenção enviada de um componente um Aplicativo faz algo acontecer em outro componente de Aplicativo, associando ambos os componentes de Aplicativo e permitindo que eles se comuniquem. Ao enviar e receber Intenções, você pode fazer os Blocos coordenarem ações complexas, como iniciar o aplicativo de câmera para capturar e salvar, coletar informações sobre o local ou navegar de uma tela para a próxima.

### <a name="androidmanifestxml"></a>AndroidManifest.XML

Quando você adiciona um Bloco ao aplicativo, ele é registrado com um arquivo XML especial chamado **Manifesto do Android**. O Manifesto acompanha todos os Blocos de Aplicativos em um aplicativo, bem como requisitos de versão, permissões e bibliotecas vinculadas &ndash; tudo que o sistema operacional precisa saber para seu aplicativo ser executado. O **Manifesto do Android** também funciona com Atividades e Intenções para controlar quais ações são apropriadas a uma determinada Atividade. Esses recursos avançados do manifesto do Android são abordados no guia [Trabalhando com o Manifesto do Android](~/android/platform/android-manifest.md).

Na versão de tela única do aplicativo Phoneword, apenas uma Atividade, uma Intenção e o `AndroidManifest.xml` foram usados, juntamente com recursos adicionais como ícones. Na versão multitela do Phoneword, uma Atividade extra foi adicionada; ela foi iniciada na primeira Atividade usando uma Intenção. A próxima seção explora como Intenções ajudam a criar a navegação em aplicativos Android.

## <a name="android-navigation"></a>Navegação em Android

Foram feitas tentativas para navegar entre as telas. É hora de aprofundar-se nesse código para ver como as Intenções funcionam e entender sua função na navegação em Android.

### <a name="launching-a-second-activity-with-an-intent"></a>Iniciando uma segunda atividade com uma intenção

No aplicativo Phoneword, uma Intenção foi usada para inicializar uma segunda tela (Atividade). Comece criando uma Intenção, *Context* passando`this`no Contexto atual ( , referindo-se ao **contexto**atual`TranslationHistoryActivity`) e o tipo de Bloco de Aplicativo que você está procurando ( ):

```csharp
Intent intent = new Intent(this, typeof(TranslationHistoryActivity));
```

O **Contexto** é uma interface para informações globais sobre o ambiente de aplicativo &ndash; permite que objetos recém-criados saibam o que está acontecendo com o aplicativo. Se você pensar em uma Intenção como uma mensagem, estará fornecendo o nome do destinatário da mensagem (`TranslationHistoryActivity`) e o endereço do destinatário (`Context`).

O Android oferece a opção de anexar dados simples a uma Intenção (dados complexos são tratados de modo diferente). No exemplo do Phoneword, `PutStringArrayExtra` é usado para anexar uma lista de números de telefone à Intenção e `StartActivity` é chamado no destinatário da Intenção. O código completo tem esta aparência:

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", _phoneNumbers);
    StartActivity(intent);
};
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Conceitos adicionais introduzidos no Phoneword

O aplicativo Phoneword introduziu vários conceitos não abordados neste guia. Esses conceitos incluem:

**Recursos de Cadeia de Caracteres** &ndash; No aplicativo Phoneword, o texto de `TranslationHistoryButton` foi definido como `"@string/translationHistory"`. A sintaxe `@string` significa que o valor da cadeia de caracteres é armazenado no _arquivo de recursos de cadeia de caracteres_, **Strings.xml**. O seguinte valor para ao `translationHistory` sequência foi adicionada a **Strings.xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Call History</string>
</resources>
```

Para saber mais sobre recursos de cadeia de caracteres e outros recursos do Android, consulte o [guia de Recursos do Android](~/android/app-fundamentals/resources-in-android/index.md).

**ListView e ArrayAdapter** &ndash; Uma _ListView_ é um componente de interface do usuário que oferece uma maneira simples de apresentar uma lista de rolagem de linhas. A instância `ListView` requer um _Adaptador_ para fornecer a ela dados contidos nas exibições de linha. A seguinte linha de código foi usada para preencher a interface do usuário do `TranslationHistoryActivity`:

```csharp
this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
```

ListViews e Adaptadores estão além do escopo deste documento, mas são discutidos no guia muito abrangente [ListViews e adaptadores](~/android/user-interface/layouts/list-view/index.md).
[Popular um ListView usando dados](~/android/user-interface/layouts/list-view/populating.md) trata especificamente do uso de classes `ListActivity` e `ArrayAdapter` internas para criar e popular um `ListView` sem definir um layout personalizado, como foi feito no exemplo Phoneword.

## <a name="summary"></a>Resumo

Parabéns, você concluiu seu primeiro aplicativo Android multitela! Este guia apresentou *Blocos de Construção de Aplicativo Android* e *Intenções* e os utilizou para compilar um aplicativo Android multitela. Agora você tem a base sólida necessária para começar a desenvolver seus próprios aplicativos Xamarin.Android.

Em seguida, você aprenderá a compilar aplicativos de plataforma cruzada com Xamarin nos [guias Como criar aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md).
