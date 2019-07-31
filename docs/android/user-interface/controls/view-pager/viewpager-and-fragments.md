---
title: ViewPager com fragmentos
description: ViewPager é um Gerenciador de layout que permite implementar a navegação Gestural. A navegação Gestural permite que o usuário passe o dedo para a esquerda e para a direita para percorrer as páginas de dados. Este guia explica como implementar uma interface do usuário do swipeable com o ViewPager, usando fragmentos como páginas de dados.
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 90bffc2360654f571728f76810f144e702a81e57
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646098"
---
# <a name="viewpager-with-fragments"></a>ViewPager com fragmentos

_ViewPager é um Gerenciador de layout que permite implementar a navegação Gestural. A navegação Gestural permite que o usuário passe o dedo para a esquerda e para a direita para percorrer as páginas de dados. Este guia explica como implementar uma interface do usuário do swipeable com o ViewPager, usando fragmentos como páginas de dados._

 
## <a name="overview"></a>Visão geral

`ViewPager`geralmente é usado em conjunto com fragmentos para que seja mais fácil gerenciar o ciclo de vida de cada página no `ViewPager`. Neste tutorial, `ViewPager` é usado para criar um aplicativo chamado **FlashCardPager** que apresenta uma série de problemas de matemática em cartões flash. Cada cartão flash é implementado como um fragmento. O usuário passa o dedo para a esquerda e para a direita pelos cartões flash e toca em um problema de matemática para revelar sua resposta. Esse aplicativo cria uma `Fragment` instância para cada cartão flash e implementa um adaptador derivado de `FragmentPagerAdapter`. Em [Viewpager e exibições](~/android/user-interface/controls/view-pager/viewpager-and-views.md), a maior parte do trabalho foi `MainActivity` feita em métodos de ciclo de vida. No **FlashCardPager**, a maior parte do trabalho será feita por um `Fragment` em um dos seus métodos de ciclo de vida. 

Este guia não abrange os conceitos básicos de fragmentos &ndash; se você ainda não estiver familiarizado com fragmentos no Xamarin. Android, consulte [fragmentos](~/android/platform/fragments/index.md) para ajudá-lo a começar com fragmentos. 



## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Crie um novo projeto Android chamado **FlashCardPager**. Em seguida, inicie o Gerenciador de pacotes NuGet (para obter mais informações sobre a instalação [de pacotes NuGet, consulte Passo a passos: Incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Localize e instale o pacote **Xamarin. Android. support. v4** , conforme explicado em [Viewpager e exibições](~/android/user-interface/controls/view-pager/viewpager-and-views.md). 



## <a name="add-an-example-data-source"></a>Adicionar uma fonte de dados de exemplo

No **FlashCardPager**, a fonte de dados é um baralho de cartões flash representados `FlashCardDeck` pela classe; essa fonte de dados `ViewPager` fornece o conteúdo do item. `FlashCardDeck`contém uma coleção pronta de problemas e respostas de matemática. O `FlashCardDeck` Construtor não requer argumentos: 

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

Como os detalhes de implementação `FlashCardDeck` do não são relevantes para `ViewPager`a compreensão `FlashCardDeck` , o código não é listado aqui.
O código-fonte `FlashCardDeck` para está disponível em [FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs).
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

Esse XML define um `ViewPager` que ocupa a tela inteira. Observe que você deve usar o nome totalmente qualificado **Android. support. v4. View. ViewPager** porque `ViewPager` o é empacotado em uma biblioteca de suporte. `ViewPager`está disponível somente na [biblioteca de suporte do Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); Ele não está disponível na SDK do Android.


## <a name="set-up-viewpager"></a>Configurar o ViewPager

Edite **MainActivity.cs** e adicione as `using` seguintes instruções:

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

Altere a `MainActivity` declaração de classe para que ela seja derivada `FragmentActivity`de:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity`é derivado de`FragmentActivity` (em vez `Activity`de) `FragmentActivity` porque sabe como gerenciar o suporte de fragmentos. Substitua o método `OnCreate` pelo seguinte código: 

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

1.  Define a exibição do recurso de layout **Main. axml** .

2.  Recupera uma referência para o `ViewPager` do layout.

3.  Cria uma instância de `FlashCardDeck` uma nova como a fonte de dados.

Quando você cria e executa esse código, você deve ver uma exibição semelhante à captura de tela a seguir: 

[![Captura de tela do aplicativo FlashCardPager com ViewPager vazio](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

Neste ponto, o `ViewPager` está vazio porque não há os fragmentos que são usados para popular o `ViewPager`, e não há um adaptador para criar esses fragmentos a partir dos dados no **FlashCardDeck**. 

Nas seções a seguir, a `FlashCardFragment` é criar para implementar a funcionalidade de cada cartão flash e um `FragmentPagerAdapter` é criado para conectar o `ViewPager` `FlashCardDeck`aos fragmentos criados a partir de dados no. 



## <a name="create-the-fragment"></a>Criar o fragmento

Cada cartão flash será gerenciado por um fragmento de interface do `FlashCardFragment`usuário chamado. `FlashCardFragment`exibirá as informações contidas em um único cartão flash. Cada instância do `FlashCardFragment` será hospedada `ViewPager`pelo. 
`FlashCardFragment`o modo de exibição do será `TextView` composto por um que exibe o texto do problema do cartão flash. Essa exibição implementará um manipulador de eventos que usa `Toast` um para exibir a resposta quando o usuário toca na pergunta do cartão flash. 



### <a name="create-the-flashcardfragment-layout"></a>Criar o layout FlashCardFragment

Antes `FlashCardFragment` que o possa ser implementado, seu layout deve ser definido. Esse layout é um layout de contêiner de fragmento para um único fragmento. Adicione um novo layout do Android ao **recurso/layout** chamado **flashcard_layout. axml**. Abra **Resources/layout/flashcard_layout. axml** e substitua seu conteúdo pelo código a seguir: 

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

Este layout define um único fragmento de cartão flash; cada fragmento é composto por um `TextView` que exibe um problema matemático usando uma fonte grande (100sp). Esse texto é centralizado vertical e horizontalmente no cartão flash. 



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

Esse código stub a definição essencial `Fragment` que será usada para exibir um cartão flash. Observe que `FlashCardFragment` é derivado da versão da biblioteca de suporte `Fragment` do definida `Android.Support.V4.App.Fragment`em. O Construtor está vazio para que o `newInstance` método de fábrica seja usado para criar um `FlashCardFragment` novo em vez de um construtor. 

O `OnCreateView` método de ciclo de vida cria e configura `TextView`o. Ele inplana o layout para o fragmento `TextView` e retorna o `TextView` plano de infixo para o chamador. `LayoutInflater`e `ViewGroup` são passados para `OnCreateView` para que ele possa inflar o layout. O `savedInstanceState` pacote contém dados que `OnCreateView` o usa para recriar `TextView` o de um estado salvo. 

A exibição do fragmento é explicitamente replanada pela chamada para `inflater.Inflate`. O `container` argumento é o pai do modo de exibição e `false` o sinalizador instrui o inflater a evitar a adição da exibição inplana ao pai da exibição (ela será adicionada quando `ViewPager` a chamada for o método do `GetItem` adaptador posteriormente neste Walkthrough). 



### <a name="add-state-code-to-flashcardfragment"></a>Adicionar código de estado a FlashCardFragment

Como uma atividade, um fragmento tem um `Bundle` que ele usa para salvar e recuperar seu estado. No **FlashCardPager**, isso `Bundle` é usado para salvar a pergunta e o texto de resposta do cartão flash associado. No **FlashCardFragment.cs**, adicione as seguintes `Bundle` chaves à parte superior da `FlashCardFragment` definição de classe: 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

Modifique o `newInstance` método de fábrica para que ele crie `Bundle` um objeto e use as chaves acima para armazenar a pergunta e o texto de resposta aprovados no fragmento depois que ele for instanciado: 

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

Modifique o método `OnCreateView` de ciclo de vida do fragmento para recuperar essas informações do pacote passado e carregue o texto da pergunta `TextBox`no: 

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

A `answer` variável não é usada aqui, mas será usada posteriormente quando o código do manipulador de eventos for adicionado a esse arquivo. 


## <a name="create-the-adapter"></a>Criar o adaptador

`ViewPager`usa um objeto de controlador de adaptador que fica `ViewPager` entre o e a fonte de dados (consulte a ilustração no artigo do [adaptador](~/android/user-interface/controls/view-pager/index.md#adapter) ViewPager). Para acessar esses dados, `ViewPager` é necessário fornecer um adaptador personalizado derivado de. `PagerAdapter` Como este exemplo usa fragmentos, ele usa um `FragmentPagerAdapter` &ndash; `FragmentPagerAdapter` é derivado de `PagerAdapter`. 
`FragmentPagerAdapter`representa cada página como uma `Fragment` que é mantida persistentemente no Gerenciador de fragmentos, desde que o usuário possa retornar à página. À medida que o usuário passa por páginas do `ViewPager`, o `FragmentPagerAdapter` extrai informações da fonte de dados e a usa `ViewPager` para criar `Fragment`s para exibição. 

Ao implementar um `FragmentPagerAdapter`, você deve substituir o seguinte:

-   **Contagem** de &ndash; Propriedade somente leitura que retorna o número de exibições (páginas) disponíveis.

-   **GetItem** &ndash; Retorna o fragmento a ser exibido para a página especificada.

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

Esse código stub da implementação essencial `FragmentPagerAdapter` . Nas seções a seguir, cada um desses métodos é substituído pelo código de trabalho. A finalidade do construtor é passar o Gerenciador de fragmentos para o `FlashCardDeckAdapter`Construtor da classe base. 



### <a name="implement-the-adapter-constructor"></a>Implementar o construtor do adaptador

Quando o aplicativo instancia o `FlashCardDeckAdapter`, ele fornece uma referência ao Gerenciador de fragmentos e uma `FlashCardDeck`instanciada. Adicione a seguinte variável de membro à parte superior da `FlashCardDeckAdapter` classe em **FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

Adicione a seguinte linha de código ao `FlashCardDeckAdapter` Construtor: 

```csharp
this.flashCardDeck = flashCards;
```

Essa linha de código armazena a `FlashCardDeck` instância que o `FlashCardDeckAdapter` usará. 



### <a name="implement-count"></a>Implementar contagem

A `Count` implementação é relativamente simples: ela retorna o número de cartões flash no baralho da placa Flash. Substitua `Count` pelo seguinte código: 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


A `NumCards` propriedade de `FlashCardDeck` retorna o número de cartões flash (número de fragmentos) no conjunto de dados. 



### <a name="implement-getitem"></a>Implementar GetItem

O `GetItem` método retorna o fragmento associado à posição especificada. Quando `GetItem` é chamado para uma posição no baralho do Flash Card, ele retorna um `FlashCardFragment` configurado para exibir o problema do cartão flash nessa posição. Substitua o método `GetItem` pelo seguinte código: 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

Esse código faz o seguinte:

1.  Pesquisa a cadeia de caracteres do problema matemático `FlashCardDeck` no baralho para a posição especificada. 

2.  Pesquisa a cadeia de caracteres de resposta `FlashCardDeck` no baralho para a posição especificada. 

3.  Chama o `FlashCardFragment` método `newInstance`de fábrica, passando as cadeias de caracteres de problema e resposta do cartão flash. 

4.  Cria e retorna um novo cartão `Fragment` flash que contém o texto de pergunta e resposta para essa posição. 

Quando o `ViewPager` renderiza o `Fragment` em `position`, ele exibe o `TextBox` que contém a cadeia de caracteres do problema matemático `position` que reside no baralho do Flash Card. 



## <a name="add-the-adapter-to-the-viewpager"></a>Adicionar o adaptador ao ViewPager

Agora que o `FlashCardDeckAdapter` está implementado, é hora de adicioná-lo `ViewPager`ao. No **MainActivity.cs**, adicione a seguinte linha de código ao final do `OnCreate` método:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

Esse código instancia o `FlashCardDeckAdapter`, passando o `SupportFragmentManager` no primeiro argumento. (A `SupportFragmentManager` propriedade de FragmentActivity é usada para obter uma referência &ndash; `FragmentManager` ao para obter mais informações sobre o `FragmentManager`, consulte [Gerenciando fragmentos](~/android/platform/fragments/managing-fragments.md).) 

A implementação principal agora está completa &ndash; e executa o aplicativo.
Você deve ver a primeira imagem do baralho do cartão flash que aparece na tela, conforme mostrado à esquerda na próxima captura. Passe o dedo para a esquerda para ver mais cartões flash e, em seguida, passe o dedo para a direita para voltar pelo baralho da placa Flash:

[![Capturas de tela de exemplo do aplicativo FlashCardPager sem indicadores de pager](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Adicionar um indicador de pager

Essa implementação `ViewPager` mínima exibe cada cartão flash no baralho, mas não fornece nenhuma indicação de onde o usuário está dentro do baralho. A próxima etapa é adicionar um `PagerTabStrip`. O `PagerTabStrip` informa o usuário sobre qual número de problema é exibido e fornece o contexto de navegação, exibindo uma dica das cartas anteriores e do próximo flash. 

Abra **Resources/layout/Main. axml** e `PagerTabStrip` adicione um ao layout:

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

Ao compilar e executar o aplicativo, você verá o vazio `PagerTabStrip` exibido na parte superior de cada cartão flash: 

[![Closeup de PagerTabStrip sem texto](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>Exibir um título

Para adicionar um título a cada guia de página, implemente o `GetPageTitleFormatted` método no adaptador. `ViewPager`chamadas `GetPageTitleFormatted` (se implementadas) para obter a cadeia de título que descreve a página na posição especificada. Adicione o seguinte método à `FlashCardDeckAdapter` classe em **FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

Esse código converte a posição no baralho do cartão flash em um número de problema. A cadeia de caracteres resultante é convertida `String` em um Java que é `ViewPager`retornado para o. Quando você executa o aplicativo com esse novo método, cada página exibe o número do problema no `PagerTabStrip`: 

[![Capturas de tela de FlashCardPager com o número do problema exibido acima de cada página](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

Você pode passar o dedo para ver o número do problema no baralho da placa Flash que é exibido na parte superior de cada cartão flash. 



## <a name="handle-user-input"></a>Tratar entrada do usuário

O **FlashCardPager** apresenta uma série de cartões flash baseados em fragmento em `ViewPager`um, mas ainda não há uma maneira de revelar a resposta para cada problema. Nesta seção, um manipulador de eventos é adicionado ao `FlashCardFragment` para exibir a resposta quando o usuário toca no texto de problema do cartão flash. 

Abra **FlashCardFragment.cs** e adicione o seguinte código ao final do `OnCreateView` método logo antes que a exibição seja retornada ao chamador: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

Esse `Click` manipulador de eventos exibe a resposta em um sistema de notificação que aparece quando o `TextBox`usuário toca no. A `answer` variável foi inicializada anteriormente quando as informações de estado foram lidas do pacote que `OnCreateView`foi passado para. Crie e execute o aplicativo e, em seguida, toque no texto do problema em cada cartão flash para ver a resposta: 

[![Capturas de tela de notificações do aplicativo FlashCardPager quando o problema de matemática é tocado](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

O **FlashCardPager** apresentado neste passo a passos usa `MainActivity` um derivado `FragmentActivity`de, mas você também pode `MainActivity` derivar de `AppCompatActivity` (que também fornece suporte para gerenciamento de fragmentos). Para exibir um `AppCompatActivity` exemplo, consulte [FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager) na Galeria de exemplo.



## <a name="summary"></a>Resumo

Este tutorial forneceu um exemplo passo a passo de como criar um aplicativo baseado em `ViewPager`básico usando `Fragment`s. Ele apresentou uma fonte de dados de exemplo contendo perguntas e respostas de cartão `ViewPager` flash, um layout para exibir os cartões flash `FragmentPagerAdapter` e uma subclasse que conecta `ViewPager` o à fonte de dados. Para ajudar o usuário a navegar pelos cartões flash, foram incluídas instruções que explicam como adicionar um `PagerTabStrip` para exibir o número do problema na parte superior de cada página. Por fim, o código de manipulação de eventos foi adicionado para exibir a resposta quando o usuário toca em um problema de cartão flash. 



## <a name="related-links"></a>Links relacionados

- [FlashCardPager (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
