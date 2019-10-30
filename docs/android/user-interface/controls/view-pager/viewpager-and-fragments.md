---
title: ViewPager com fragmentos
description: ViewPager é um Gerenciador de layout que permite implementar a navegação Gestural. A navegação Gestural permite que o usuário passe o dedo para a esquerda e para a direita para percorrer as páginas de dados. Este guia explica como implementar uma interface do usuário do swipeable com o ViewPager, usando fragmentos como páginas de dados.
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: d0fdfa44ce6caa0c5f0e0aa2eed6406e606eacc4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029030"
---
# <a name="viewpager-with-fragments"></a>ViewPager com fragmentos

_ViewPager é um Gerenciador de layout que permite implementar a navegação Gestural. A navegação Gestural permite que o usuário passe o dedo para a esquerda e para a direita para percorrer as páginas de dados. Este guia explica como implementar uma interface do usuário do swipeable com o ViewPager, usando fragmentos como páginas de dados._

## <a name="overview"></a>Visão Geral

`ViewPager` geralmente é usado em conjunto com fragmentos para que seja mais fácil gerenciar o ciclo de vida de cada página na `ViewPager`. Neste tutorial, `ViewPager` é usado para criar um aplicativo chamado **FlashCardPager** que apresenta uma série de problemas de matemática em cartões flash. Cada cartão flash é implementado como um fragmento. O usuário passa o dedo para a esquerda e para a direita pelos cartões flash e toca em um problema de matemática para revelar sua resposta. Esse aplicativo cria uma instância de `Fragment` para cada placa Flash e implementa um adaptador derivado de `FragmentPagerAdapter`. Em [Viewpager e exibições](~/android/user-interface/controls/view-pager/viewpager-and-views.md), a maior parte do trabalho foi feita em `MainActivity` métodos de ciclo de vida. No **FlashCardPager**, a maior parte do trabalho será feita por uma `Fragment` em um de seus métodos de ciclo de vida. 

Este guia não abrange os conceitos básicos dos fragmentos &ndash; se você ainda não estiver familiarizado com fragmentos no Xamarin. Android, consulte [fragmentos](~/android/platform/fragments/index.md) para ajudá-lo a começar a usar fragmentos. 

## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Crie um novo projeto Android chamado **FlashCardPager**. Em seguida, inicie o Gerenciador de pacotes NuGet (para obter mais informações sobre a instalação de pacotes NuGet, consulte [passo a passos: incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Localize e instale o pacote **Xamarin. Android. support. v4** , conforme explicado em [Viewpager e exibições](~/android/user-interface/controls/view-pager/viewpager-and-views.md). 

## <a name="add-an-example-data-source"></a>Adicionar uma fonte de dados de exemplo

No **FlashCardPager**, a fonte de dados é um baralho de cartões flash representados pela classe `FlashCardDeck`; essa fonte de dados fornece a `ViewPager` com conteúdo de item. `FlashCardDeck` contém uma coleção pronta de problemas e respostas de matemática. O construtor de `FlashCardDeck` não requer argumentos: 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

A coleção de cartões flash no `FlashCardDeck` é organizada de forma que cada cartão flash possa ser acessado por um indexador. Por exemplo, a linha de código a seguir recupera o quarto problema do cartão flash no baralho: 

```csharp
string problem = flashCardDeck[3].Problem;
```

Esta linha de código recupera a resposta correspondente ao problema anterior:

```csharp
string answer = flashCardDeck[3].Answer;
```

Como os detalhes de implementação de `FlashCardDeck` não são relevantes para entender `ViewPager`, o código de `FlashCardDeck` não está listado aqui.
O código-fonte para `FlashCardDeck` está disponível em [FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs).
Baixe esse arquivo de origem (ou copie e cole o código em um novo arquivo **FlashCardDeck.cs** ) e adicione-o ao seu projeto.

## <a name="create-a-viewpager-layout"></a>Criar um layout ViewPager

Abra **Resources/layout/Main. axml** e substitua seu conteúdo pelo seguinte XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    </android.support.v4.view.ViewPager>
```

Esse XML define um `ViewPager` que ocupa a tela inteira. Observe que você deve usar o nome totalmente qualificado **Android. support. v4. View. ViewPager** porque `ViewPager` é empacotado em uma biblioteca de suporte. `ViewPager` está disponível somente na [biblioteca de suporte do Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); Ele não está disponível na SDK do Android.

## <a name="set-up-viewpager"></a>Configurar o ViewPager

Edite **MainActivity.cs** e adicione as seguintes instruções de `using`:

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

Altere a declaração de classe `MainActivity` para que ela seja derivada de `FragmentActivity`:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` é derivado de`FragmentActivity` (em vez de `Activity`) porque `FragmentActivity` sabe como gerenciar o suporte de fragmentos. Substitua o método `OnCreate` pelo seguinte código: 

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    FlashCardDeck flashCards = new FlashCardDeck();
}
```

Esse código faz o seguinte:

1. Define a exibição do recurso de layout **Main. axml** .

2. Recupera uma referência para o `ViewPager` do layout.

3. Cria uma instância de um novo `FlashCardDeck` como a fonte de dados.

Quando você cria e executa esse código, você deve ver uma exibição semelhante à captura de tela a seguir: 

[![captura de tela do aplicativo FlashCardPager com ViewPager vazio](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

Neste ponto, a `ViewPager` está vazia porque não há fragmentos que são usados para popular a `ViewPager`e não há um adaptador para criar esses fragmentos a partir dos dados no **FlashCardDeck**. 

Nas seções a seguir, uma `FlashCardFragment` é criada para implementar a funcionalidade de cada cartão flash e um `FragmentPagerAdapter` é criado para conectar os `ViewPager` aos fragmentos criados a partir de dados no `FlashCardDeck`. 

## <a name="create-the-fragment"></a>Criar o fragmento

Cada cartão flash será gerenciado por um fragmento de interface do usuário chamado `FlashCardFragment`. a exibição de `FlashCardFragment`exibirá as informações contidas em um único cartão flash. Cada instância do `FlashCardFragment` será hospedada pelo `ViewPager`. 
a exibição de `FlashCardFragment`consistirá em um `TextView` que exibe o texto do problema do cartão flash. Essa exibição implementará um manipulador de eventos que usa um `Toast` para exibir a resposta quando o usuário toca na pergunta do cartão flash. 

### <a name="create-the-flashcardfragment-layout"></a>Criar o layout FlashCardFragment

Antes que `FlashCardFragment` possa ser implementado, seu layout deve ser definido. Esse layout é um layout de contêiner de fragmento para um único fragmento. Adicione um novo layout do Android ao **recurso/layout** chamado **flashcard_layout. axml**. Abra **Resources/layout/flashcard_layout. axml** e substitua seu conteúdo pelo código a seguir: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/flash_card_question"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:textAppearance="@android:style/TextAppearance.Large"
            android:textSize="100sp"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true"
            android:text="Question goes here" />
    </RelativeLayout>
```

Este layout define um único fragmento de cartão flash; cada fragmento é composto de um `TextView` que exibe um problema matemático usando uma fonte grande (100sp). Esse texto é centralizado vertical e horizontalmente no cartão flash. 

### <a name="create-the-initial-flashcardfragment-class"></a>Criar a classe FlashCardFragment inicial

Adicione um novo arquivo chamado **FlashCardFragment.cs** e substitua seu conteúdo pelo código a seguir:

```csharp
using System;
using Android.OS;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    public class FlashCardFragment : Android.Support.V4.App.Fragment
    {
        public FlashCardFragment() { }

        public static FlashCardFragment newInstance(String question, String answer)
        {
            FlashCardFragment fragment = new FlashCardFragment();
            return fragment;
        }
        public override View OnCreateView (
            LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
        {
            View view = inflater.Inflate (Resource.Layout.flashcard_layout, container, false);
            TextView questionBox = (TextView)view.FindViewById (Resource.Id.flash_card_question);
            return view;
        }
    }
}
```

Esse código stub da definição de `Fragment` essencial que será usada para exibir um cartão flash. Observe que `FlashCardFragment` é derivada da versão da biblioteca de suporte do `Fragment` definida em `Android.Support.V4.App.Fragment`. O Construtor está vazio para que o método de fábrica de `newInstance` seja usado para criar um novo `FlashCardFragment` em vez de um construtor. 

O método de ciclo de vida `OnCreateView` cria e configura o `TextView`. Ele inplana o layout do `TextView` do fragmento e retorna a `TextView` inplanada ao chamador. `LayoutInflater` e `ViewGroup` são passados para `OnCreateView` para que ele possa inflar o layout. O pacote de `savedInstanceState` contém dados que o `OnCreateView` usa para recriar o `TextView` de um estado salvo. 

A exibição do fragmento é explicitamente inplanada pela chamada para `inflater.Inflate`. O argumento de `container` é o pai da exibição e o sinalizador de `false` instrui o inflater a evitar a adição da exibição inplana ao pai da exibição (ela será adicionada quando `ViewPager` chamar o método `GetItem` do adaptador posteriormente neste passo a passos). 

### <a name="add-state-code-to-flashcardfragment"></a>Adicionar código de estado a FlashCardFragment

Como uma atividade, um fragmento tem um `Bundle` que ele usa para salvar e recuperar seu estado. No **FlashCardPager**, esse `Bundle` é usado para salvar a pergunta e o texto de resposta do cartão flash associado. No **FlashCardFragment.cs**, adicione as seguintes chaves de `Bundle` à parte superior da definição de classe `FlashCardFragment`: 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

Modifique o método de fábrica `newInstance` de forma que ele crie um objeto de `Bundle` e use as chaves acima para armazenar a pergunta e o texto de resposta aprovados no fragmento depois que ele for instanciado: 

```csharp
public static FlashCardFragment newInstance(String question, String answer)
{
    FlashCardFragment fragment = new FlashCardFragment();

    Bundle args = new Bundle();
    args.PutString(FLASH_CARD_QUESTION, question);
    args.PutString(FLASH_CARD_ANSWER, answer);
    fragment.Arguments = args;

    return fragment;
}
```

Modifique o método de ciclo de vida do fragmento `OnCreateView` para recuperar essas informações do pacote passado e carregue o texto da pergunta na `TextBox`: 

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    string question = Arguments.GetString(FLASH_CARD_QUESTION, "");
    string answer = Arguments.GetString(FLASH_CARD_ANSWER, "");

    View view = inflater.Inflate(Resource.Layout.flashcard_layout, container, false);
    TextView questionBox = (TextView)view.FindViewById(Resource.Id.flash_card_question);
    questionBox.Text = question;

    return view;
}
```

A variável `answer` não é usada aqui, mas será usada posteriormente quando o código do manipulador de eventos for adicionado a esse arquivo. 

## <a name="create-the-adapter"></a>Criar o adaptador

`ViewPager` usa um objeto de controlador de adaptador que fica entre o `ViewPager` e a fonte de dados (consulte a ilustração no artigo do [adaptador](~/android/user-interface/controls/view-pager/index.md#adapter) ViewPager). Para acessar esses dados, `ViewPager` requer que você forneça um adaptador personalizado derivado do `PagerAdapter`. Como este exemplo usa fragmentos, ele usa um `FragmentPagerAdapter` &ndash; `FragmentPagerAdapter` é derivado de `PagerAdapter`. 
`FragmentPagerAdapter` representa cada página como uma `Fragment` que é mantida persistentemente no Gerenciador de fragmentos, desde que o usuário possa retornar à página. À medida que o usuário passa por páginas do `ViewPager`, o `FragmentPagerAdapter` extrai informações da fonte de dados e as usa para criar `Fragment`s para o `ViewPager` exibir. 

Ao implementar uma `FragmentPagerAdapter`, você deve substituir o seguinte:

- **Contagem** &ndash; propriedade somente leitura que retorna o número de exibições (páginas) disponíveis.

- **GetItem** &ndash; retorna o fragmento a ser exibido para a página especificada.

Adicione um novo arquivo chamado **FlashCardDeckAdapter.cs** e substitua seu conteúdo pelo código a seguir:

```csharp
using System;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    class FlashCardDeckAdapter : FragmentPagerAdapter
    {
        public FlashCardDeckAdapter (Android.Support.V4.App.FragmentManager fm, FlashCardDeck flashCards)
            : base(fm)
        {
        }

        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override Android.Support.V4.App.Fragment GetItem(int position)
        {
            throw new NotImplementedException();
        }
    }
}
```

Esse código stub da implementação de `FragmentPagerAdapter` essencial. Nas seções a seguir, cada um desses métodos é substituído pelo código de trabalho. A finalidade do construtor é passar o Gerenciador de fragmentos para o construtor da classe base do `FlashCardDeckAdapter`. 

### <a name="implement-the-adapter-constructor"></a>Implementar o construtor do adaptador

Quando o aplicativo instancia a `FlashCardDeckAdapter`, ele fornece uma referência ao Gerenciador de fragmentos e uma `FlashCardDeck`instanciada. Adicione a seguinte variável de membro à parte superior da classe `FlashCardDeckAdapter` em **FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

Adicione a seguinte linha de código ao construtor de `FlashCardDeckAdapter`: 

```csharp
this.flashCardDeck = flashCards;
```

Essa linha de código armazena a instância `FlashCardDeck` que o `FlashCardDeckAdapter` usará. 

### <a name="implement-count"></a>Implementar contagem

A implementação de `Count` é relativamente simples: ela retorna o número de cartões flash no baralho da placa Flash. Substitua `Count` pelo seguinte código: 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```

A propriedade `NumCards` de `FlashCardDeck` retorna o número de cartões flash (número de fragmentos) no conjunto de dados. 

### <a name="implement-getitem"></a>Implementar GetItem

O método `GetItem` retorna o fragmento associado à posição fornecida. Quando `GetItem` é chamado para uma posição no baralho do Flash Card, ele retorna um `FlashCardFragment` configurado para exibir o problema do cartão flash nessa posição. Substitua o método `GetItem` pelo seguinte código: 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

Esse código faz o seguinte:

1. Pesquisa a cadeia de caracteres do problema matemático no baralho `FlashCardDeck` para a posição especificada. 

2. Pesquisa a cadeia de caracteres de resposta no baralho `FlashCardDeck` para a posição especificada. 

3. Chama o método de fábrica de `FlashCardFragment` `newInstance`, passando as cadeias de caracteres de problema e resposta do cartão flash. 

4. Cria e retorna um novo `Fragment` de cartão flash que contém o texto de pergunta e resposta para essa posição. 

Quando o `ViewPager` renderiza a `Fragment` em `position`, ele exibe o `TextBox` que contém a cadeia de caracteres do problema matemático que reside em `position` no baralho da placa Flash. 

## <a name="add-the-adapter-to-the-viewpager"></a>Adicionar o adaptador ao ViewPager

Agora que a `FlashCardDeckAdapter` está implementada, é hora de adicioná-la ao `ViewPager`. No **MainActivity.cs**, adicione a seguinte linha de código ao final do método de `OnCreate`:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

Esse código instancia o `FlashCardDeckAdapter`, passando o `SupportFragmentManager` no primeiro argumento. (A propriedade `SupportFragmentManager` de FragmentActivity é usada para obter uma referência para o `FragmentManager` &ndash; para obter mais informações sobre o `FragmentManager`, consulte [Gerenciando fragmentos](~/android/platform/fragments/managing-fragments.md).) 

A implementação principal agora está concluída &ndash; compilar e executar o aplicativo.
Você deve ver a primeira imagem do baralho do cartão flash que aparece na tela, conforme mostrado à esquerda na próxima captura. Passe o dedo para a esquerda para ver mais cartões flash e, em seguida, passe o dedo para a direita para voltar pelo baralho da placa Flash:

[![capturas de tela de exemplo do aplicativo FlashCardPager sem indicadores de pager](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>Adicionar um indicador de pager

Essa implementação mínima de `ViewPager` exibe cada cartão flash no baralho, mas não fornece nenhuma indicação de onde o usuário está dentro do baralho. A próxima etapa é adicionar um `PagerTabStrip`. O `PagerTabStrip` informa o usuário sobre qual número de problema é exibido e fornece o contexto de navegação, exibindo uma dica dos cartões flash anteriores e próximos. 

Abra **Resources/layout/Main. axml** e adicione um `PagerTabStrip` ao layout:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

  <android.support.v4.view.PagerTabStrip
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:layout_gravity="top"
      android:paddingBottom="10dp"
      android:paddingTop="10dp"
      android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

Ao compilar e executar o aplicativo, você verá o `PagerTabStrip` vazio exibido na parte superior de cada cartão flash: 

[![closeup de PagerTabStrip sem texto](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)

### <a name="display-a-title"></a>Exibir um título

Para adicionar um título a cada guia de página, implemente o método `GetPageTitleFormatted` no adaptador. `ViewPager` chama `GetPageTitleFormatted` (se implementado) para obter a cadeia de caracteres de título que descreve a página na posição especificada. Adicione o seguinte método à classe `FlashCardDeckAdapter` em **FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

Esse código converte a posição no baralho do cartão flash em um número de problema. A cadeia de caracteres resultante é convertida em um `String` Java que é retornado para o `ViewPager`. Quando você executa o aplicativo com esse novo método, cada página exibe o número do problema no `PagerTabStrip`: 

[![capturas de tela do FlashCardPager com o número do problema exibido acima de cada página](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

Você pode passar o dedo para ver o número do problema no baralho da placa Flash que é exibido na parte superior de cada cartão flash. 

## <a name="handle-user-input"></a>Tratar entrada do usuário

O **FlashCardPager** apresenta uma série de cartões flash baseados em fragmento em um `ViewPager`, mas ainda não há uma maneira de revelar a resposta para cada problema. Nesta seção, um manipulador de eventos é adicionado ao `FlashCardFragment` para exibir a resposta quando o usuário toca no texto do problema do cartão flash. 

Abra **FlashCardFragment.cs** e adicione o seguinte código ao final do método `OnCreateView` logo antes da exibição ser retornada ao chamador: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

Esse `Click` manipulador de eventos exibe a resposta em um sistema de notificação que aparece quando o usuário toca na `TextBox`. A variável `answer` foi inicializada anteriormente quando as informações de estado foram lidas do pacote passado para `OnCreateView`. Crie e execute o aplicativo e, em seguida, toque no texto do problema em cada cartão flash para ver a resposta: 

[![capturas de tela de notificações do aplicativo FlashCardPager quando o problema de matemática é tocado](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

O **FlashCardPager** apresentado neste passo a passos usa um `MainActivity` derivado de `FragmentActivity`, mas você também pode derivar `MainActivity` de `AppCompatActivity` (que também oferece suporte ao gerenciamento de fragmentos). Para exibir um exemplo de `AppCompatActivity`, consulte [FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager) na Galeria de exemplo.

## <a name="summary"></a>Resumo

Este tutorial forneceu um exemplo passo a passo de como criar um aplicativo básico baseado em `ViewPager`usando `Fragment`s. Ele apresentou uma fonte de dados de exemplo contendo perguntas e respostas de cartão flash, um layout `ViewPager` para exibir os cartões flash e uma subclasse `FragmentPagerAdapter` que conecta o `ViewPager` à fonte de dados. Para ajudar o usuário a navegar pelos cartões flash, foram incluídas instruções que explicam como adicionar um `PagerTabStrip` para exibir o número do problema na parte superior de cada página. Por fim, o código de manipulação de eventos foi adicionado para exibir a resposta quando o usuário toca em um problema de cartão flash. 

## <a name="related-links"></a>Links relacionados

- [FlashCardPager (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
