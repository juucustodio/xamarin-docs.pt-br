---
title: ViewPager com fragmentos
description: ViewPager é um Gerenciador de layout que permite que você implemente gestual navegação. Navegação gestual permite que o usuário passe o dedo para esquerda e direita para percorrer as páginas de dados. Este guia explica como implementar uma interface do usuário deslizantes com ViewPager, usando fragmentos como as páginas de dados.
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 1b6e1c8ce91eaad46e779527c5ba12e2187cad24
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038353"
---
# <a name="viewpager-with-fragments"></a>ViewPager com fragmentos

_ViewPager é um Gerenciador de layout que permite que você implemente gestual navegação. Navegação gestual permite que o usuário passe o dedo para esquerda e direita para percorrer as páginas de dados. Este guia explica como implementar uma interface do usuário deslizantes com ViewPager, usando fragmentos como as páginas de dados._

 
## <a name="overview"></a>Visão geral

`ViewPager` geralmente é usado em conjunto com fragmentos, para que ele seja mais fácil de gerenciar o ciclo de vida de cada página no `ViewPager`. Neste passo a passo `ViewPager` é usado para criar um aplicativo chamado **FlashCardPager** que apresenta uma série de problemas de matemática em cartões flash. Cada cartão flash é implementado como um fragmento. O usuário passa o dedo left e right por meio de placas flash e toca em um problema de matemática para revelar sua resposta. Esse aplicativo cria uma `Fragment` instância para cada cartão flash e implementa um adaptador deriva `FragmentPagerAdapter`. Na [Viewpager e modos de exibição](~/android/user-interface/controls/view-pager/viewpager-and-views.md), a maioria do trabalho foi feito no `MainActivity` métodos de ciclo de vida. Na **FlashCardPager**, a maioria do trabalho será feita um `Fragment` em um de seus métodos de ciclo de vida. 

Este guia não abrange os conceitos básicos de fragmentos &ndash; se você ainda não estiver familiarizado com fragmentos no xamarin. Android, consulte [fragmentos](~/android/platform/fragments/index.md) para ajudá-lo a começar com fragmentos. 



## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Criar um novo projeto Android chamado **FlashCardPager**. Em seguida, inicie o Gerenciador de pacotes do NuGet (para obter mais informações sobre como instalar pacotes do NuGet, consulte [passo a passo: Incluir um NuGet no projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Localizar e instalar o **Xamarin.Android.Support.v4** pacote conforme explicado nas [Viewpager e modos de exibição](~/android/user-interface/controls/view-pager/viewpager-and-views.md). 



## <a name="add-an-example-data-source"></a>Adicionar uma fonte de dados de exemplo

Na **FlashCardPager**, a fonte de dados é um baralho de cartas de flash representado pela `FlashCardDeck` classe; essas fontes da fonte de dados a `ViewPager` com o conteúdo do item. `FlashCardDeck` contém uma coleção prontas para uso de problemas de matemática e respostas. O `FlashCardDeck` construtor não requer nenhum argumento: 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

A coleção de cartões flash em `FlashCardDeck` é organizado de modo que cada cartão flash pode ser acessado por um indexador. Por exemplo, a seguinte linha de código recupera o quarto problema do cartão flash do baralho: 

```csharp
string problem = flashCardDeck[3].Problem;
```

Esta linha de código recupera a resposta correspondente para o problema anterior:

```csharp
string answer = flashCardDeck[3].Answer;
```

Porque os detalhes de implementação `FlashCardDeck` não são relevantes para compreensão `ViewPager`, o `FlashCardDeck` código não está listado aqui.
O código-fonte `FlashCardDeck` está disponível em [FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs).
Baixe esse arquivo de origem (ou copie e cole o código em um novo **FlashCardDeck.cs** arquivo) e adicioná-lo ao seu projeto.



## <a name="create-a-viewpager-layout"></a>Criar um Layout de ViewPager

Abra **Resources/layout/Main.axml** e substitua seu conteúdo pelo seguinte XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    </android.support.v4.view.ViewPager>
```

Esse XML define um `ViewPager` que ocupa a tela inteira. Observe que você deve usar o nome totalmente qualificado **android.support.v4.view.ViewPager** porque `ViewPager` é empacotado em uma biblioteca de suporte. `ViewPager` só está disponível de [biblioteca de suporte ao Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); ele não está disponível no SDK do Android.


## <a name="set-up-viewpager"></a>Configurar ViewPager

Edite **MainActivity.cs** e adicione o seguinte `using` instruções:

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

Alterar o `MainActivity` declaração de classe, de modo que ela é derivada de `AppCompatActivity`:

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

1.  Define o modo de exibição do **Main. axml** recursos de layout.

2.  Recupera uma referência para o `ViewPager` do layout.

3.  Cria uma nova `FlashCardDeck` como fonte de dados.

Quando você compila e executa esse código, você deverá ver uma exibição semelhante ao seguinte captura de tela: 

[![Aplicativo de captura de tela de FlashCardPager com ViewPager vazio](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

Neste ponto, o `ViewPager` está vazio porque ele está faltando os fragmentos que são usados preencher o `ViewPager`, e tenha um adaptador para criar esses fragmentos dos dados no **FlashCardDeck**. 

Nas seções a seguir, uma `FlashCardFragment` é criar para implementar a funcionalidade de cada cartão flash e uma `FragmentPagerAdapter` é criado para conectar o `ViewPager` para os fragmentos criados a partir de dados no `FlashCardDeck`. 



## <a name="create-the-fragment"></a>Criar o fragmento

Cada cartão flash será gerenciado por um fragmento de interface do usuário chamado `FlashCardFragment`. `FlashCardFragment`do modo de exibição exibirá as informações contidas com um único cartão flash. Cada instância do `FlashCardFragment` será hospedada pela `ViewPager`. 
`FlashCardFragment`do modo de exibição consistirá um `TextView` que exibe o texto de problema do cartão flash. Este modo de exibição será implementar um manipulador de eventos que usa um `Toast` para exibir a resposta quando o usuário toca a pergunta do cartão flash. 



### <a name="create-the-flashcardfragment-layout"></a>Criar o Layout de FlashCardFragment

Antes de `FlashCardFragment` pode ser implementado, seu layout deve ser definido. Este layout é um layout de contêiner do fragmento para um único fragmento. Adicionar um novo layout de Android para **recursos/layout** chamado **flashcard_layout.axml**. Abra **Resources/layout/flashcard_layout.axml** e substitua seu conteúdo pelo seguinte código: 

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

Esse layout define um fragmento único cartão flash; cada fragmento é composto por um `TextView` que exibe um problema de matemática usando uma fonte grande (100sp). Esse texto é centralizado verticalmente e horizontalmente no cartão flash. 



### <a name="create-the-initial-flashcardfragment-class"></a>Criar a classe FlashCardFragment inicial

Adicionar um novo arquivo chamado **FlashCardFragment.cs** e substitua seu conteúdo pelo seguinte código:

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

Esse código criará um stub essencial `Fragment` definição que será usada para exibir um cartão flash. Observe que `FlashCardFragment` é derivado da versão da biblioteca de suporte do `Fragment` definidos no `Android.Support.V4.App.Fragment`. O construtor estiver vazio, de modo que o `newInstance` método de fábrica é usado para criar um novo `FlashCardFragment` em vez de um construtor. 

O `OnCreateView` método de ciclo de vida cria e configura o `TextView`. Ele descompacta o layout do fragmento `TextView` e retorna o inflado `TextView` ao chamador. `LayoutInflater` e `ViewGroup` são passados para `OnCreateView` , de modo que ele pode aumentar o layout. O `savedInstanceState` pacote contém dados que `OnCreateView` usa para recriar o `TextView` de um estado salvo. 

Modo de exibição do fragmento está explicitamente inflado pela chamada para `inflater.Inflate`. O `container` argumento é o pai do modo de exibição e o `false` sinalizador instrui o inflater de não adicionar o modo de exibição inflado ao pai da exibição (ele será adicionado quando `ViewPager` chamada do adaptador de `GetItem` método posteriormente nesta passo a passo). 



### <a name="add-state-code-to-flashcardfragment"></a>Adicione o código de estado para FlashCardFragment

Como uma atividade, um fragmento tem um `Bundle` que ele usa para salvar e recuperar seu estado. Na **FlashCardPager**, esse `Bundle` é usado para salvar a pergunta e texto de resposta para o cartão flash associado. Na **FlashCardFragment.cs**, adicione o seguinte `Bundle` chaves na parte superior do `FlashCardFragment` definição de classe: 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

Modificar a `newInstance` método de fábrica então que ele cria um `Bundle` de objeto e usa as chaves acima para armazenar a pergunta passada e texto no fragmento de resposta após ele ser instanciado: 

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

Modifique o método de ciclo de vida do fragmento `OnCreateView` para recuperar essas informações do pacote no passado e carregar o texto da pergunta o `TextBox`: 

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

O `answer` variável não é usada aqui, mas ele será usado mais tarde quando o código do manipulador de eventos é adicionado a esse arquivo. 


## <a name="create-the-adapter"></a>Criar o adaptador

`ViewPager` usa um objeto de controlador de adaptador que fica entre o `ViewPager` e a fonte de dados (veja a ilustração no ViewPager [adaptador](~/android/user-interface/controls/view-pager/index.md#adapter) artigo). Para acessar esses dados, `ViewPager` exige que você forneça um adaptador personalizado derivado de `PagerAdapter`. Como este exemplo usa os fragmentos, ele usa um `FragmentPagerAdapter` &ndash; `FragmentPagerAdapter` é derivado de `PagerAdapter`. 
`FragmentPagerAdapter` representa cada página como um `Fragment` que persistentemente é mantida no Gerenciador de fragmento para desde que o usuário pode retornar à página. Como o dedo do usuário por meio das páginas do `ViewPager`, o `FragmentPagerAdapter` extrai informações de fonte de dados e o utiliza para criar `Fragment`s para o `ViewPager` para exibir. 

Quando você implementa um `FragmentPagerAdapter`, você deve substituir o seguinte:

-   **Contagem** &ndash; propriedade somente leitura que retorna o número de modos de exibição (páginas) disponíveis.

-   **GetItem** &ndash; retorna o fragmento a ser exibida para a página especificada.

Adicionar um novo arquivo chamado **FlashCardDeckAdapter.cs** e substitua seu conteúdo pelo seguinte código:

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

Esse código criará um stub essencial `FragmentPagerAdapter` implementação. Nas seções a seguir, cada um desses métodos é substituída pelo código de trabalho. A finalidade do construtor é o Gerenciador de fragmento de passar o `FlashCardDeckAdapter`do construtor de classe base. 



### <a name="implement-the-adapter-constructor"></a>Implementar o construtor do adaptador

Quando o aplicativo cria uma instância de `FlashCardDeckAdapter`, ele fornece uma referência para o Gerenciador de fragmento e um instanciadas `FlashCardDeck`. Adicione a seguinte variável de membro na parte superior do `FlashCardDeckAdapter` classe **FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

Adicione a seguinte linha de código para o `FlashCardDeckAdapter` construtor: 

```csharp
this.flashCardDeck = flashCards;
```

Esta linha de código armazena o `FlashCardDeck` da instância que o `FlashCardDeckAdapter` usará. 



### <a name="implement-count"></a>Contagem de implementar

O `Count` implementação é relativamente simple: ele retorna o número de cartões flash no baralho cartão flash. Substitua `Count` pelo seguinte código: 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


O `NumCards` propriedade de `FlashCardDeck` retorna o número de cartões flash (número de fragmentos) no conjunto de dados. 



### <a name="implement-getitem"></a>Implementar GetItem

O `GetItem` método retorna o fragmento associado à posição determinada. Quando `GetItem` é chamado para uma posição em que o baralho de cartas de flash, ele retorna um `FlashCardFragment` configurado para exibir o problema do cartão flash nessa posição. Substitua o método `GetItem` pelo seguinte código: 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

Esse código faz o seguinte:

1.  Procura a cadeia de caracteres de problema de matemática no `FlashCardDeck` baralho para a posição especificada. 

2.  Procura a cadeia de caracteres em resposta a `FlashCardDeck` baralho para a posição especificada. 

3.  Chamadas a `FlashCardFragment` método de fábrica `newInstance`, passando as cadeias de caracteres do cartão flash problema e a resposta. 

4.  Cria e retorna um novo cartão flash `Fragment` que contém o texto da pergunta e resposta para essa posição. 

Quando o `ViewPager` renderiza os `Fragment` no `position`, ele exibe o `TextBox` que contém a cadeia de caracteres de problema de matemática que reside em `position` no baralho cartão flash. 



## <a name="add-the-adapter-to-the-viewpager"></a>Adicionar o adaptador para o ViewPager

Agora que o `FlashCardDeckAdapter` é implementado, é hora de adicioná-lo para o `ViewPager`. Na **MainActivity.cs**, adicione a seguinte linha de código ao final do `OnCreate` método:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

Esse código cria uma instância de `FlashCardDeckAdapter`, passando o `SupportFragmentManager` no primeiro argumento. (O `SupportFragmentManager` propriedade do FragmentActivity é usada para obter uma referência para o `FragmentManager` &ndash; para obter mais informações sobre o `FragmentManager`, consulte [gerenciamento de fragmentos](~/android/platform/fragments/managing-fragments.md).) 

A implementação principal agora está concluída &ndash; compilar e executar o aplicativo.
Você deve ver a primeira imagem do cartão flash baralho aparecem na tela, conforme mostrado à esquerda na próxima captura de tela. Passe o dedo para esquerda para ver mais cartões flash, em seguida, o direito de passar o dedo para percorrer o baralho de cartas de flash:

[![Capturas de tela de exemplo de aplicativo FlashCardPager sem indicadores de pager](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Adicionar um indicador de Pager

Nesse mínimo `ViewPager` implementação exibe cada cartão flash no baralho, mas ela fornece nenhuma indicação de onde o usuário está dentro do baralho. A próxima etapa é adicionar um `PagerTabStrip`. O `PagerTabStrip` informa ao usuário sobre qual problema número é exibido e fornece o contexto de navegação, exibindo uma dica dos cartões flash anteriores e posteriores. 

Abra **Resources/layout/Main.axml** e adicione um `PagerTabStrip` ao layout:

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

Quando você compilar e executa o aplicativo, você deverá ver a esvaziar `PagerTabStrip` exibida na parte superior de cada cartão flash: 

[![Close-up de PagerTabStrip sem texto](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>Exibir um título

Para adicionar um título para cada guia de página, implementar o `GetPageTitleFormatted` método no adaptador. `ViewPager` chamadas `GetPageTitleFormatted` (se implementado) para obter a cadeia de caracteres do título que descreve a página na posição especificada. Adicione o seguinte método para o `FlashCardDeckAdapter` classe **FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

Esse código converte a posição do cartão flash baralho em um número de problema. A cadeia de caracteres resultante é convertida em um Java `String` que é retornado para o `ViewPager`. Quando você executa o aplicativo com esse novo método, cada página exibe o número de problema no `PagerTabStrip`: 

[![Capturas de tela de FlashCardPager com o número de problema exibido acima de cada página](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

Você pode passe o dedo para frente e para trás para ver o número de problema do baralho de cartão flash é exibido na parte superior de cada cartão flash. 



## <a name="handle-user-input"></a>Manipular a entrada do usuário

**FlashCardPager** apresenta uma série de cartões de flash com base no fragmento em um `ViewPager`, mas ele ainda não tem uma maneira para revelar a resposta para cada problema. Nesta seção, um manipulador de eventos é adicionado para o `FlashCardFragment` para exibir a resposta quando o usuário toca no texto de problema do cartão flash. 

Abra **FlashCardFragment.cs** e adicione o seguinte código ao final do `OnCreateView` antes que o modo de exibição é retornado ao chamador de método: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

Isso `Click` manipulador de eventos exibe a resposta em uma notificação do sistema que aparece quando o usuário toca o `TextBox`. O `answer` variável foi inicializada anteriormente quando informações de estado foi lido do pacote que foi passado para `OnCreateView`. Compilar e executar o aplicativo e toque no texto do problema em cada cartão flash para ver a resposta: 

[![Aplicativo de capturas de tela de FlashCardPager notificações do sistema quando o problema de matemática é tocado](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

O **FlashCardPager** apresentadas neste passo a passo usa um `MainActivity` derivado `FragmentActivity`, mas também é possível derivar `MainActivity` de `AppCompatActivity` (que também oferece suporte para o gerenciamento de fragmentos). Para exibir uma `AppCompatActivity` exemplo, consulte [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface%5CFlashCardPager/) na Galeria de exemplo. 



## <a name="summary"></a>Resumo

Este passo a passo fornecido um exemplo passo a passo de como criar um basic `ViewPager`-com base em aplicativo usando `Fragment`s. Ela apresentada uma fonte de dados de exemplo que contém o cartão flash perguntas e respostas, uma `ViewPager` layout para exibir os cartões flash e uma `FragmentPagerAdapter` subclasse que se conecta a `ViewPager` à fonte de dados. Para ajudar o usuário navegue por meio de placas flash, instruções foram incluídas que explicam como adicionar um `PagerTabStrip` para exibir o número de problema na parte superior de cada página. Por fim, o código de manipulação de eventos foi adicionado para exibir a resposta quando o usuário toca em um problema do cartão flash. 



## <a name="related-links"></a>Links relacionados

- [FlashCardPager (amostra)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
