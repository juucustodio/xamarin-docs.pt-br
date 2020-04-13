---
title: Criando um fragmento
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 0e8d3748c7ddd337cf2f27f5b272b208e79d503a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027512"
---
# <a name="creating-a-fragment"></a>Criando um fragmento

Para criar um Fragmento, uma `Android.App.Fragment` classe deve `OnCreateView` herdar e, em seguida, substituir o método. `OnCreateView`será chamado pela atividade de hospedagem quando for a hora de `View`colocar o Fragmento na tela, e retornará um . Um `OnCreateView` típico criará isso `View` inflando um arquivo de layout e, em seguida, anexando-o a um recipiente pai. As características do contêiner são importantes, pois o Android aplicará os parâmetros de layout do pai à UI do Fragmento. O exemplo a seguir ilustra isso:

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

O código acima irá `Resource.Layout.Example_Fragment`inflar a visualização e `ViewGroup` adicioná-la como uma visão infantil ao recipiente.

> [!NOTE]
> As subclasses de fragmentos devem ter um padrão público, sem construtor de argumentos.

## <a name="adding-a-fragment-to-an-activity"></a>Adicionando um fragmento a uma atividade

Há duas maneiras de um Fragmento ser hospedado dentro de uma atividade:

- **Declarativamente** &ndash; Fragmentos podem ser usados declarativamente dentro de `.axml` arquivos de layout usando a `<Fragment>` tag.

- Os Fragmentos **Programáticos** &ndash; também podem ser instanciados dinamicamente usando a `FragmentManager` API da classe.

O uso programático através da `FragmentManager` aula será discutido posteriormente neste guia.

### <a name="using-a-fragment-declaratively"></a>Usando um Fragmento Declarativamente

Adicionar um Fragmento dentro do `<fragment>` layout requer o uso da `class` tag e, em seguida, identificar o Fragmento fornecendo o atributo ou o atributo. `android:name` O trecho a seguir mostra `class` como usar `fragment`o atributo para declarar:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Este próximo trecho mostra como `fragment` declarar a `android:name` usando o atributo para identificar a classe Fragmento :

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Quando a Atividade estiver sendo criada, o Android instanciará cada Fragmento especificado `OnCreateView` no arquivo `Fragment` de layout e inserirá a exibição criada a partir do lugar do elemento.
Os fragmentos que são declarativamente adicionados a uma Atividade são estáticos e permanecerão na Atividade até que sejam destruídas; não é possível substituir ou remover dinamicamente tal Fragmento durante a vida útil da Atividade à qual está anexado.

Cada Fragmento deve ser atribuído um identificador único:

- **android:id** &ndash; Como com outros elementos de IA em um arquivo de layout, este é um ID exclusivo.

- **android:tag** &ndash; Este atributo é uma seqüência única.

Se nenhum dos dois métodos anteriores for usado, então o Fragmento assumirá o ID da exibição do recipiente. No exemplo a `android:id` seguir, onde nem nem `android:tag` é fornecido, o Android atribuirá o ID `fragment_container` ao Fragmento:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:id="+@id/fragment_container"
                android:orientation="horizontal"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

        <fragment class="com.example.android.apis.app.TitlesFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
</LinearLayout>
```

### <a name="package-name-case"></a>Caso do nome do pacote

O Android não permite caracteres maiúsculos em nomes de pacotes; ele lançará uma exceção ao tentar inflar a exibição se um nome de pacote contiver um caractere maiúsculo. No entanto, Xamarin.Android é mais indulgente, e vai tolerar caracteres maiúsculos no namespace.

Por exemplo, ambos os trechos a seguir funcionarão com Xamarin.Android. No entanto, o segundo `android.view.InflateException` trecho fará com que um seja jogado por um aplicativo Android baseado em Java puro.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OU

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

## <a name="fragment-lifecycle"></a>Ciclo de vida do fragmento

Os fragmentos têm seu próprio ciclo de vida que é um pouco independente, mas ainda afetado pelo [ciclo de vida da atividade de hospedagem](~/android/app-fundamentals/activity-lifecycle/index.md).
Por exemplo, quando uma atividade faz uma pausa, todos os fragmentos associados são pausados. O diagrama a seguir descreve o ciclo de vida do Fragmento.

[![Diagrama de fluxo ilustrando o ciclo de vida do Fragmento](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)

### <a name="fragment-creation-lifecycle-methods"></a>Métodos do ciclo de vida da criação de fragmentos

A lista abaixo mostra o fluxo dos vários retornos de chamada no ciclo de vida de um Fragmento à medida que ele está sendo criado:

- **`OnInflate()`**&ndash; Chamado quando o Fragmento está sendo criado como parte de um layout de exibição. Isso pode ser chamado imediatamente após o Fragmento ser criado declarativamente a partir de um arquivo de layout XML. O Fragmento ainda não está associado à sua atividade, mas o **Activity**, **Bundle**e **AttributeSet** da hierarquia de exibição são passados como parâmetros. Este método é melhor usado para analisar o **AttributeSet** e para salvar os atributos que podem ser usados posteriormente pelo Fragmento.

- **`OnAttach()`**&ndash; Chamado após o Fragmento está associado com a Atividade. Este é o primeiro método a ser executado quando o Fragmento está pronto para ser usado. Em geral, os Fragmentos não devem implementar um construtor ou substituir o construtor padrão. Todos os componentes necessários para o Fragmento devem ser inicializados neste método.

- **`OnCreate()`**&ndash; Chamado pela Atividade para criar o Fragmento. Quando esse método é chamado, a hierarquia de exibição da Atividade de hospedagem pode não ser completamente instanciada, portanto, o Fragmento não deve confiar em nenhuma parte da hierarquia de exibição da Atividade até mais tarde no ciclo de vida do Fragmento. Por exemplo, não use este método para realizar quaisquer ajustes ou ajustes na interface do usuário do aplicativo. Esta é a primeira vez em que o Fragmento pode começar a coletar os dados de que precisa. O Fragmento está sendo executado no segmento de ia de ui neste momento, então evite qualquer processamento demorado ou execute esse processamento em um segmento de fundo. Esse método pode ser ignorado se **SetRetainInstance(true)** for chamado.
    Esta alternativa será descrita com mais detalhes abaixo.

- **`OnCreateView()`**&ndash; Cria a visualização do Fragmento.
    Este método é chamado assim que o método **OnCreate()** da atividade estiver concluído. Neste ponto, é seguro interagir com a hierarquia de visualização da Atividade. Este método deve retornar a visão que será usada pelo Fragmento.

- **`OnActivityCreated()`**&ndash; Chamado após **Activity.OnCreate** foi concluído pela atividade de hospedagem.
    Ajustes finais na interface do usuário devem ser realizados neste momento.

- **`OnStart()`**&ndash; Chamado após a atividade de contenção foi retomada. Isso torna o Fragmento visível para o usuário. Em muitos casos, o Fragmento conterá código que de outra forma estaria no método **OnStart()** de uma Atividade.

- **`OnResume()`**&ndash; Este é o último método chamado antes que o usuário possa interagir com o Fragmento. Um exemplo do tipo de código que deve ser executado neste método seria ativar recursos de um dispositivo com o qual o usuário pode interagir, como a câmera que o local serviços. Serviços como esses podem causar um dreno excessivo da bateria, e um aplicativo deve minimizar seu uso para preservar a vida útil da bateria.

### <a name="fragment-destruction-lifecycle-methods"></a>Métodos do ciclo de vida da destruição de fragmentos

A lista seguinte explica que os métodos do ciclo de vida que são chamados de Fragmento estão sendo destruídos:

- **`OnPause()`**&ndash; O usuário não é mais capaz de interagir com o Fragmento. Essa situação existe porque alguma outra operação Fragmento está modificando este Fragmento ou a atividade de hospedagem é pausada. É possível que a Atividade que hospeda este Fragmento ainda possa estar visível, ou seja, a Atividade em foco é parcialmente transparente ou não ocupa a tela cheia. Quando este método se torna ativo, é a primeira indicação de que o usuário está deixando o Fragmento. O Fragmento deve salvar quaisquer alterações.

- **`OnStop()`**&ndash; O Fragmento não é mais visível. A atividade do host pode ser interrompida ou uma operação Fragmento está modificando-a na Atividade. Este retorno de chamada serve ao mesmo propósito que **Activity.OnStop**.

- **`OnDestroyView()`**&ndash; Este método é chamado para limpar os recursos associados à vista. Isso é chamado quando a visão associada ao Fragmento foi destruída.

- **`OnDestroy()`**&ndash; Este método é chamado quando o Fragmento não está mais em uso. Ainda está associado à Atividade, mas o Fragmento não está mais funcional. Este método deve liberar todos os recursos que estão em uso pelo Fragmento, como um [**SurfaceView**](xref:Android.Views.SurfaceView) que pode ser usado para uma câmera. Esse método pode ser ignorado se **SetRetainInstance(true)** for chamado. Esta alternativa será descrita com mais detalhes abaixo.

- **`OnDetach()`**&ndash; Este método é chamado pouco antes do Fragmento não estar mais associado à Atividade. A hierarquia de visão do Fragmento não existe mais, e todos os recursos que são usados pelo Fragmento devem ser liberados neste momento.

### <a name="using-setretaininstance"></a>Usando setRetainInstance

É possível que um Fragmento especifique que ele não deve ser completamente destruído se a Atividade estiver sendo recriada. A `Fragment` classe fornece `SetRetainInstance` o método para este fim. Se `true` for passado para este método, então quando a Atividade for reiniciada, a mesma instância do Fragmento será usada. Se isso acontecer, todos os métodos de `OnCreate` retorno `OnDestroy` de chamada serão invocados, exceto os retornos de chamada do ciclo de vida. Este processo é ilustrado no diagrama do ciclo de vida mostrado acima (pelas linhas pontilhadas verdes).

## <a name="fragment-state-management"></a>Gestão do Estado do Fragmento

Os fragmentos podem salvar e restaurar seu estado durante `Bundle`o ciclo de vida fragmentar usando uma instância de um . O Pacote permite que um Fragmento salve dados como pares de chave/valor e é útil para dados simples que não requerem muita memória. Um Fragmento pode salvar seu `OnSaveInstanceState`estado com uma chamada para:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Quando uma nova instância de um Fragmento é `Bundle` criada, o estado salvo `OnCreate`no `OnCreateView`será `OnActivityCreated` disponibilizado para a nova instância através da , e métodos da nova instância.
A amostra a seguir demonstra `current_choice` como `Bundle`recuperar o valor do :

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    if (savedInstanceState != null)
    {
        _currentCheckPosition = savedInstanceState.GetInt("current_choice", 0);
    }
}
```

A `OnSaveInstanceState` substituição é um mecanismo apropriado para salvar dados transitórios `current_choice` em um Fragmento através de alterações de orientação, como o valor no exemplo acima. No entanto, `OnSaveInstanceState` a implementação padrão de cuida da economia de dados transitórios na ui para cada visualização que tenha um ID atribuído. Por exemplo, veja um aplicativo `EditText` que tenha um elemento definido no XML da seguinte forma:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Como `EditText` o controle `id` tem um atribuído, o Fragmento salva automaticamente `OnSaveInstanceState` os dados no widget quando é chamado.

### <a name="bundle-limitations"></a>Limitações do pacote

Embora `OnSaveInstanceState` o uso facilite a salvação de dados transitórios, o uso deste método tem algumas limitações:

- Se o Fragmento não for adicionado à pilha traseira, seu estado não será restaurado quando o usuário pressionar o botão **Voltar.**

- Quando o Pacote é usado para salvar dados, esses dados são serializados. Isso pode levar a atrasos no processamento.

## <a name="contributing-to-the-menu"></a>Contribuindo para o Menu

Os fragmentos podem contribuir com itens para o menu de sua atividade de hospedagem.
Uma atividade lida com itens do menu primeiro. Se a Atividade não tiver um manipulador, o evento será repassado para o Fragmento, que então irá manuseá-lo.

Para adicionar itens ao menu da Atividade, um Fragmento deve fazer duas coisas.
Primeiro, o Fragmento deve `OnCreateOptionsMenu` implementar o método e colocar seus itens no menu, conforme mostrado no código a seguir:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

O menu no trecho de código anterior é inflado a partir `menu_fragment_vehicle_list.xml`do Seguinte XML, localizado no arquivo :

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

Em seguida, o `SetHasOptionsMenu(true)`Fragmento deve chamar . A chamada para este método anuncia para o Android que o Fragmento tem itens de menu para contribuir com o menu de opções. A menos que a chamada para este método seja feita, os itens do menu para o Fragmento não serão adicionados ao menu de opção da Atividade. Isso é normalmente feito no `OnCreate()`método do ciclo de vida, como mostrado no próximo trecho de código:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

A tela a seguir mostra como este menu seria:

[![Exemplo de captura de tela do aplicativo Minhas Viagens exibindo itens do menu](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
